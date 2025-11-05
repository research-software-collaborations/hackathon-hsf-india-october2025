ARG GH_ORG="https://github.com/davidlange6"
ARG GH_REPO="my-first-pixi-project"
ARG GH_BRANCH="main"
ARG ENV_NAME="binder" 
#should get name from environment.yml itself..

FROM ubuntu:24.04

USER root
#can I move all of this to mamba?
RUN apt-get update -y
RUN apt-get install -y emacs nano vim git
#python3 glibc-devel.i686 vim nano emacs xxhash-devel libzstd 
RUN apt-get clean -y all

COPY binder/entrypoint.sh /usr/bin/entrypoint.sh

#RUN which python3
#can all this be removed?
RUN python3 -m ensurepip
RUN pip3 install --no-cache --upgrade pip
RUN pip3 install --no-cache notebook jupyterlab 

RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh \
    -O ~/miniconda.sh && \
    /bin/bash ~/miniconda.sh -b -p /opt/conda && \
    rm ~/miniconda.sh && \
    ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh

RUN . /opt/conda/etc/profile.d/conda.sh; 
    
ARG NB_USER=jovyan
ARG NB_UID=1000
ENV USER ${NB_USER}
ENV HOME /home/${NB_USER}
ENV SHELL /bin/bash
RUN adduser \
    --uid ${NB_UID} \
    ${NB_USER}

COPY . ${HOME}
USER root
RUN chown -R ${NB_UID} ${HOME}

USER ${NB_USER}

WORKDIR ${HOME}

# add our environment
RUN git clone -b ${GH_BRANCH} ${GH_ORG}/${GH_REPO}
#COPY binder/environment.yml .

RUN . /opt/conda/etc/profile.d/conda.sh; mamba env create -f ${GH_REPO}/environment.yml; \
    conda activate ${ENV_NAME}; \
    python -m ipykernel install --user --name myenv --display-name "Python (hsf-india)"

RUN echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate base" >> ~/.bashrc


