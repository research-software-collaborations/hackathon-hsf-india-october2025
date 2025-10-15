# A container where CernVM-FS is up and running for CMSSW

# Make the base image configurable:
#ARG BASEIMAGE=gitlab-registry.cern.ch/cms-cloud/cmssw-docker/cc7-cms:latest
#gitlab-registry.cern.ch/sft/docker/centos7-core:2023-10-26
ARG BASEIMAGE=gitlab-registry.cern.ch/sft/docker/centos7-core:2023-10-26
# Set up the SLC6 CMSSW base:
FROM ${BASEIMAGE}

LABEL maintainer="DLange"

# Build-time metadata as defined at http://label-schema.org
ARG BUILD_DATE
ARG VCS_REF
ARG VCS_URL
ARG VERSION
LABEL   org.label-schema.build-date=$BUILD_DATE \
        org.label-schema.name="CC7 CMSSW Docker images using CVMFS" \
        org.label-schema.description="Provide lightweight images for running CMSSW." \
        org.label-schema.url="http://cms-sw.github.io/" \
        org.label-schema.vcs-ref=$VCS_REF \
        org.label-schema.vcs-url=$VCS_URL \
        org.label-schema.vendor="CERN" \
        org.label-schema.version=$VERSION \
        org.label-schema.schema-version="1.0"

USER root

# install cvmfs repos
#ADD binder/etc-yum-cernvm.repo /etc/yum.repos.d/cernvm.repo
#RUN yum-config-manager --add-repo /etc/yum.repos.d/cernvm.repo
#RUN cat /etc/yum.repos.d/cernvm.repo
# was cvmfs-config-default - seems not to exist
#RUN yum-config-manager --add-repo https://cvmrepo.web.cern.ch/cvmrepo/yum/cvmfs/EL/7.10/x86_64/



#RUN yum install -y https://linuxsoft.cern.ch/centos-vault/7.9.2009/os/x86_64/Packages/libselinux-2.5-15.el7.x86_64.rpm
RUN yum install -y https://linuxsoft.cern.ch/centos-vault/7.9.2009/os/x86_64/Packages/libmnl-1.0.3-7.el7.x86_64.rpm
RUN yum install -y https://linuxsoft.cern.ch/centos-vault/7.9.2009/os/x86_64/Packages/libnfnetlink-1.0.1-4.el7.x86_64.rpm
RUN yum install -y https://linuxsoft.cern.ch/centos-vault/7.9.2009/os/x86_64/Packages/libnetfilter_conntrack-1.0.6-1.el7_3.x86_64.rpm
RUN yum install -y https://linuxsoft.cern.ch/centos-vault/7.9.2009/os/x86_64/Packages/iptables-1.4.21-35.el7.x86_64.rpm

RUN yum install -y https://linuxsoft.cern.ch/centos-vault/7.9.2009/os/x86_64/Packages/iproute-4.11.0-30.el7.x86_64.rpm
RUN yum install -y https://linuxsoft.cern.ch/centos-vault/7.9.2009/os/x86_64/Packages/libselinux-utils-2.5-15.el7.x86_64.rpm https://linuxsoft.cern.ch/centos-vault/7.9.2009/os/x86_64/Packages/policycoreutils-2.5-34.el7.x86_64.rpm
#RUN yum install policycoreutils
RUN yum install -y https://linuxsoft.cern.ch/centos-vault/7.9.2009/os/x86_64/Packages/initscripts-9.49.53-1.el7.x86_64.rpm
RUN yum install -y https://cvmrepo.web.cern.ch/cvmrepo/yum/cvmfs/EL/7.10/x86_64/cvmfs-config-default-2.0-1.noarch.rpm
#RUN     yum --disablerepo="*" --enablerepo="https://cvmrepo.web.cern.ch/cvmrepo/yum/cvmfs/EL/7.10/x86_64/" install -y cvmfs cvmfs-config-default fuse && \
RUN yum install -y https://cvmrepo.web.cern.ch/cvmrepo/yum/cvmfs/EL/7.10/x86_64/cvmfs-libs-2.12.7-1.el7.x86_64.rpm
#RUN yum install -y https://linuxsoft.cern.ch/centos-vault/7.9.2009/os/x86_64/Packages/audit-libs-2.8.5-4.el7.x86_64.rpm
RUN yum install -y https://linuxsoft.cern.ch/centos-vault/7.9.2009/os/x86_64/Packages/audit-libs-python-2.8.5-4.el7.x86_64.rpm
RUN yum install -y https://linuxsoft.cern.ch/centos-vault/7.9.2009/os/x86_64/Packages/libsemanage-python-2.5-14.el7.x86_64.rpm
RUN yum install -y https://linuxsoft.cern.ch/centos-vault/7.9.2009/os/x86_64/Packages/setools-libs-3.3.8-4.el7.x86_64.rpm
#RUN yum install -y https://linuxsoft.cern.ch/centos-vault/7.9.2009/os/x86_64/Packages/policycoreutils-python-2.5-34.el7.x86_64.rpm
RUN yum install -y https://cvmrepo.web.cern.ch/cvmrepo/yum/cvmfs/EL/7.10/x86_64/cvmfs-2.12.7-1.el7.x86_64.rpm
#RUN yum install -y fuse
RUN yum clean -y all

# afs does not work when building on Mac
# add kerberos and afs configs
# ADD krb5.conf /etc/krb5.conf
# ADD afs/etc /usr/vice/etc
# ADD openafs.ko /lib/modules/2.6.32-696.30.1.el6.x86_64/fs/openafs/openafs.ko
# RUN echo /lib/modules/2.6.32-696.30.1.el6.x86_64/fs/openafs/openafs.ko | openafs-modules --add-modules && \
    # /sbin/chkconfig --add afs && \
    # /sbin/service afs start

# add CVMFS mounting
RUN mkdir -p /cvmfs/cms.cern.ch && \
    echo "cms.cern.ch /cvmfs/cms.cern.ch cvmfs defaults 0 0" >> /etc/fstab && \
    mkdir -p /cvmfs/cms-opendata-conddb.cern.ch && \
    echo "cms-opendata-conddb.cern.ch /cvmfs/cms-opendata-conddb.cern.ch cvmfs defaults 0 0" >> /etc/fstab
ADD binder/cms.cern.ch.local /etc/cvmfs/config.d/cms.cern.ch.local
ADD binder/run-cvmfs.sh /etc/cvmfs/run-cvmfs.sh
RUN chmod uga+rx /etc/cvmfs/run-cvmfs.sh
ADD binder/default.local /etc/cvmfs/default.local

RUN pip3 install --no-cache --upgrade pip
RUN pip3 install --no-cache notebook jupyterlab
RUN pip3 install --no-cache numba awkward uproot matplotlib h5py hist

#WORKDIR /code
#USER cmsusr
#ENV USER cmsusr
#ENV HOME /home/cmsusr
#
#ADD     binder/entrypoint-cvmfs.sh /opt/cms/entrypoint.sh
#RUN     sudo chmod 755 /opt/cms/entrypoint.sh && \
#        sudo chown -R cmsusr /home/cmsusr && \
#        chmod 755 /home/cmsusr


ARG NB_USER
ARG NB_UID
ENV USER ${NB_USER}
ENV HOME /home/${NB_USER}
ENV SHELL /bin/bash
RUN adduser \
    --gecos "Default user" \
    --uid ${NB_UID} \
    ${NB_USER}

#--disabled-password \
RUN passwd -d $NB_USER

#ENTRYPOINT ["/opt/cms/entrypoint.sh"]
#CMD     ["/bin/sh"]


