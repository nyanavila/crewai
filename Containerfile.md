# Use the latest OpenShift AI notebook base image
FROM quay.io/opendatahub/workbench-images:jupyter-datascience-ubi9-python-3.11-20250312-414a9c0

# Switch to root user to install dependencies
USER root

# Upgrade pip and install dependencies with pinned versions
COPY requirements.txt /tmp/requirements.txt
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -r /tmp/requirements.txt && \
    PYTHON_SITE_PACKAGES=$(python3 -c "import site; print(site.getsitepackages()[0])") && \
    chmod -R g+w "$PYTHON_SITE_PACKAGES" && \
    fix-permissions "$PYTHON_SITE_PACKAGES"

# Copy utilities script into the image
COPY utils.py /opt/app-root/src/utils.py

# Switch back to OpenShift AI's default non-root user
USER 1001

# Set working directory (Must match OpenShift AI expectations)
WORKDIR /opt/app-root/src

# Expose Jupyter Notebook port
EXPOSE 8888

# Define the default entrypoint for the notebook
ENTRYPOINT ["start-notebook.sh"]
