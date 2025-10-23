FROM ubuntu:24.04 
#jammy

RUN apt-get update -y
RUN apt-get install -y git pip wget 

RUN apt-get install -y software-properties-common
RUN add-apt-repository -y ppa:apptainer/ppa
RUN apt-get update -y

#emacs nano 
#vim
RUN apt-get install -y apptainer

# install the notebook package
#RUN pip install --no-cache --upgrade pip 
RUN pip install --no-cache notebook jupyterlab

#RUN pip install --no-cache numba awkward uproot matplotlib h5py hist


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

WORKDIR ${HOME}
