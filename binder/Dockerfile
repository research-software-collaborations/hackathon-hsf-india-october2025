# A cc7 (yes, wow, cc7) container for DUNE

ARG BASEIMAGE=gitlab-registry.cern.ch/sft/docker/centos7-core:2023-10-26
FROM ${BASEIMAGE}

USER root

# disable no longer functioning repos and add archive ones
RUN sed -i 's/enabled=1/enabled=0/' /etc/yum.repos.d/*.repo
ADD binder/centos.repo /etc/yum.repos.d/centos.repo
RUN yum-config-manager --add-repo /etc/yum.repos.d/centos.repo
RUN yum clean all ; yum makecache

#RUN yum repolist
#RUN yum install -y policycoreutils-python
#ADD binder/etc-yum-cernvm.repo /etc/yum.repos.d/cernvm.repo
#RUN yum-config-manager --add-repo /etc/yum.repos.d/cernvm.repo
#RUN yum install -y https://cvmrepo.web.cern.ch/cvmrepo/yum/cvmfs/EL/7.10/x86_64/cvmfs-config-default-2.0-1.noarch.rpm

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

RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda2-4.5.11-Linux-x86_64.sh -O ~/miniconda.sh && \
    /bin/bash ~/miniconda.sh -b -p /opt/conda && \
    rm ~/miniconda.sh && \
    ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh

RUN . /opt/conda/etc/profile.d/conda.sh; conda upgrade -n base conda
    
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



RUN echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate base" >> ~/.bashrc


WORKDIR ${HOME}

#RUN wget -O Miniforge3.sh "https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-$(uname)-$(uname -m).sh"
#RUN bash Miniforge3.sh -b -p "${HOME}/conda"

# add our environment
COPY binder/environment.yml .
RUN source ~/.bashrc; conda env create -f environment.yml -y
SHELL ["conda", "run", "-n", "hsf-india", "/bin/bash", "-c"]
#
#RUN source ${HOME}/conda/bin/activate; conda env create -f environment.yml && conda clean -afy; conda activate hsf-india-epic
#ENV PATH ${HOME}/conda/envs/hsf-india-epic/bin:${HOME}/conda/bin:$PATH
#RUN which conda
#RUN conda list
#RUN conda env list
##even with all of this it seems we don't get the right environment...
#RUN conda install notebook jupyterlab
##RUN conda activate hsf-india-epic
##RUN conda list
#RUN echo "conda activate hsf-india-epic" >> ~/.bashrc
#RUN cat /usr/bin/entrypoint.sh
#
#ENTRYPOINT ["/usr/bin/entrypoint.sh"]
##CMD     ["/bin/sh"]


