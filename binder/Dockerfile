FROM gitlab-registry.cern.ch/cms-cloud/cmssw-docker/cc7-cms:latest

RUN apt-get update -y
RUN apt-get install -y git pip wget

RUN pip install --no-cache --upgrade pip && \
    pip install --no-cache notebook jupyterlab

RUN pip install --no-cache numba awkward uproot matplotlib h5py hist

# create user with a home directory
ARG NB_USER
ARG NB_UID
ENV USER ${NB_USER}
ENV HOME /home/${NB_USER}
ENV SHELL /bin/bash
RUN adduser --disabled-password \
    --gecos "Default user" \
    --uid ${NB_UID} \
    ${NB_USER}

WORKDIR ${HOME}
USER ${USER}

ENTRYPOINT ["cd $HOME; git clone --recurse-submodules https://github.com/research-software-collaborations/courses-hsf-india-september2025"]
