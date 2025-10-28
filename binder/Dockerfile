#
ARG GH_REPO="https://github.com/iris-hep/NSBI-workflow-tutorial"
ARG GH_BRANCH="workspace_building_feature"
ARG PIXI_ENV="nsbi-env-gpu"

#
# Taken from https://github.com/renan-r-santos/pixi-kernel-binder
#
###################################################################################################
# Environment builder
###################################################################################################
FROM ghcr.io/prefix-dev/pixi:0.56.0-jammy as pixi-builder
#
ARG GH_REPO
ARG GH_BRANCH
ARG PIXI_ENV

#

WORKDIR /opt/binder

# Install environment
COPY binder/pixi.toml pixi.toml
#COPY binder/pixi.lock pixi.lock
COPY binder/compressed_mapping.json compressed_mapping.json

#RUN pixi lock --check
RUN pixi install && \
    pixi run jupyter kernelspec uninstall python3 -y && \
    pixi run jupyter kernelspec uninstall pixi-kernel-xcpp11 -y && \
    pixi run jupyter kernelspec uninstall pixi-kernel-xcpp14 -y && \
    pixi run jupyter kernelspec uninstall pixi-kernel-xcpp17 -y && \
    pixi run jupyter kernelspec uninstall pixi-kernel-ir -y && \ 
    pixi shell-hook --shell bash > /pixi-activate.sh && \
    chmod +x /pixi-activate.sh

# Clean up
RUN find .pixi \( -name '*.a' -o -name '*.pyc' -o -name '*.pyx' -o -name '*.pyo' \) -delete && \
    find .pixi -name '__pycache__' -type d -exec rm -rf '{}' '+'

RUN sed -i 's/PIXI_ENVIRONMENT_NAME=default/PIXI_ENVIRONMENT_NAME='"${PIXI_ENV}"'/' /pixi-activate.sh
RUN cat /pixi-activate.sh
###################################################################################################
# Final image
###################################################################################################
FROM ghcr.io/prefix-dev/pixi:0.56.0-jammy

ARG GH_REPO
ARG GH_BRANCH
ARG PIXI_ENV

RUN apt-get update -y
RUN apt-get install -y git wget emacs vim nano
RUN apt-get update -y

# https://mybinder.readthedocs.io/en/latest/tutorials/dockerfile.html
ARG NB_USER=jovyan
ARG NB_UID=1000
ENV USER ${NB_USER}
ENV NB_UID ${NB_UID}
ENV HOME /home/${NB_USER}
ENV SHELL=/bin/bash

RUN adduser --disabled-password \
    --gecos "Default user" \
    --uid ${NB_UID} \
    ${NB_USER}

RUN echo '#!/bin/bash\n. /usr/local/share/pixi-activate.sh\n git pull\n exec "$@"' > /usr/local/share/docker-entrypoint.sh && \
    chmod +x /usr/local/share/docker-entrypoint.sh

# Make sure the contents of this repo are in ${HOME} and owned by ${NB_USER}
COPY binder/jupyter_config.py ${HOME}/
RUN echo "c.MappingKernelManager.default_kernel_name='pixi-kernel-python3'" >> ${HOME}/jupyter_config.py
COPY binder/pixi.lock ${HOME}/
COPY binder/pixi.toml ${HOME}/

RUN cd ${HOME}; git clone -b ${GH_BRANCH} ${GH_REPO}

RUN mkdir -p ${HOME}/.jupyter && \
    cp ${HOME}/jupyter_config.py ${HOME}/.jupyter/jupyter_config.py && \
    chown -R ${NB_UID} ${HOME}

USER ${NB_USER}

# Copy the environment
COPY --from=pixi-builder /opt/binder/.pixi /opt/binder/.pixi
COPY --from=pixi-builder /pixi-activate.sh /usr/local/share/pixi-activate.sh

WORKDIR ${HOME}/NSBI-workflow-tutorial 

# Install the example for faster startup
RUN CONDA_OVERRIDE_CUDA=12.5 pixi install -e ${PIXI_ENV} --manifest-path pixi.toml
RUN CONDA_OVERRIDE_CUDA=12.5 pixi run -e ${PIXI_ENV} python -m ipykernel install --user --name sbikernel --display-name "Python (pixi: sbikernel)"

ENTRYPOINT ["/usr/local/share/docker-entrypoint.sh"]
