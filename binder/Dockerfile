FROM ghcr.io/eic/eic_xl:nightly

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

# install conda
#RUN wget -O Miniforge3.sh "https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-$(uname)-$(uname -m).sh"
#RUN bash Miniforge3.sh -b -p "${HOME}/conda"

# add our environment
#COPY binder/environment.yml .

#RUN source ${HOME}/conda/bin/activate; conda env create -f environment.yml; 

ENTRYPOINT ["git", "clone", "https://github.com/eic/HSF-India/"]

WORKDIR ${HOME}
