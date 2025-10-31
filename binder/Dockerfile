# A cc7 (yes, wow, cc7) container for DUNE

ARG BASEIMAGE=gitlab-registry.cern.ch/sft/docker/centos7-core:2023-10-26
FROM ${BASEIMAGE}

USER root

# disable no longer functioning repos and add archive ones
RUN sed -i 's/enabled=1/enabled=0/' /etc/yum.repos.d/*.repo
ADD binder/centos.repo /etc/yum.repos.d/centos.repo
RUN yum-config-manager --add-repo /etc/yum.repos.d/centos.repo
RUN yum clean all ; yum makecache

RUN yum install -y python3 glibc-devel.i686 vim nano emacs xxhash-devel libzstd  > /etc/yum.out

#RUN yum install -y asciidoc ftgl-devel giflib-devel gl2ps-devel glew-devel glibc-devel.i686 harfbuzz-devel \
#                libAfterImage-devel libffi-devel libgcc.i686 libstdc++-devel.i686 libzstd-devel lz4-devel \
#                openldap-devel pcre2-devel perl-DBD-SQLite readline-devel subversion swig tcl-devel texinfo \
#                tk-devel xmlto xxhash-devel zstd

RUN yum clean -y all

COPY binder/entrypoint.sh /usr/bin/entrypoint.sh

#RUN which python3
RUN python3 -m ensurepip
RUN pip3 install --no-cache --upgrade pip
RUN pip3 install --no-cache notebook jupyterlab 
#RUN pip3 install --no-cache numba awkward uproot matplotlib h5py hist
#https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
#https://repo.anaconda.com/archive/Anaconda3-5.3.1-Linux-x86_64.sh
#works https://repo.anaconda.com/miniconda/Miniconda3-py39_23.5.1-0-Linux-x86_64.sh \
RUN wget --quiet https://github.com/conda-forge/miniforge/releases/download/23.3.1-1/Mambaforge-23.3.1-1-Linux-x86_64.sh \
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
COPY binder/environment.yml .
RUN . /opt/conda/etc/profile.d/conda.sh; mamba env create -f environment.yml; \
    conda activate hsf-india; \
    python -m ipykernel install --user --name myenv --display-name "Python (hsf-india)"

RUN echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate base" >> ~/.bashrc
#SHELL ["conda", "run", "-n", "hsf-india", "/bin/bash", "-c"]


