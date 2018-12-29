# xrdp
#FROM ubuntu:latest
#FROM rattydave/ubuntu-ssh:18.10
# ubuntu:18.10 = cosmic
FROM ubuntu:18.10

ENV DEBIAN_FRONTEND noninteractive
ENV DBUS_SESSION_BUS_ADDRESS=/dev/null
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:US
ENV LC_ALL en_US.UTF-8

RUN cd /root && \
    sed -i 's/^#\s*\(deb.*partner\)$/\1/g' /etc/apt/sources.list && \ 
    apt-get update -y && \ 
    apt-get install -yqq locales  && \ 
    echo 'LANG="en_US.UTF-8"' > /etc/default/locale && \ 
    echo 'LANGUAGE="en_US:US"' >> /etc/default/locale && \ 
    echo 'LC_ALL="en_US.UTF-8"' >> /etc/default/locale && \ 
    locale-gen en_US.UTF-8 && \ 
    update-locale LANG=en_US.UTF-8  && \ 
    apt-get install -yqq \
        mate-desktop-environment-core \
        mate-themes \
        mate-accessibility-profiles \
        mate-applet-appmenu \
        mate-applet-brisk-menu \
        mate-applets \
        mate-applets-common \
        mate-calc \
        mate-calc-common \
        mate-dock-applet \
        mate-hud \
        mate-indicator-applet \
        mate-indicator-applet-common \
        mate-menu \
        mate-notification-daemon \
        mate-notification-daemon-common \
        mate-utils \
        mate-utils-common \
        mate-window-applets-common \
        mate-window-buttons-applet \
        mate-window-menu-applet \
        mate-window-title-applet \
        ubuntu-mate-icon-themes \
        ubuntu-mate-themes \
        xrdp \
        xorgxrdp \
        language-pack-ko \
        tightvncserver && \
    apt-get install --no-install-recommends -yqq \
        supervisor \
        sudo \
        tzdata \
        vim \
        mc \
        ca-certificates \
        xterm \
        curl \
        wget \
        build-essential \
        cmake \
        git \
        libjpeg-dev \
        libpng-dev \
        epiphany-browser \
        default-jdk \
        openssh-server && \
    ln -fs /usr/share/zoneinfo/Europe/London /etc/localtime && dpkg-reconfigure -f noninteractive tzdata && \
    apt-get -y autoclean && apt-get -y autoremove && \
    apt-get -y purge $(dpkg --get-selections | grep deinstall | sed s/deinstall//g) && \
    rm -rf /var/lib/apt/lists/*  && \
    echo "mate-session" > /etc/skel/.xsession && \
    sed -i '/TerminalServerUsers/d' /etc/xrdp/sesman.ini  && \
    sed -i '/TerminalServerAdmins/d' /etc/xrdp/sesman.ini  && \
    xrdp-keygen xrdp auto  && \
    mkdir -p /var/run/xrdp && \
    chmod 2775 /var/run/xrdp  && \
    mkdir -p /var/run/xrdp/sockdir && \
    chmod 3777 /var/run/xrdp/sockdir && \
    echo "[program:sshd]" >/etc/supervisor/conf.d/sshd.conf && \
    echo "command=/usr/sbin/sshd -D" >> /etc/supervisor/conf.d/sshd.conf && \
    echo "stdout_logfile=/var/log/supervisor/%(program_name)s.log" >> /etc/supervisor/conf.d/sshd.conf && \
    echo "stderr_logfile=/var/log/supervisor/%(program_name)s.log" >> /etc/supervisor/conf.d/sshd.conf && \
    echo "autorestart=true" >> /etc/supervisor/conf.d/sshd.conf && \
    echo "[program:xrdp-sesman]" > /etc/supervisor/conf.d/xrdp.conf && \
    echo "command=/usr/sbin/xrdp-sesman --nodaemon" >> /etc/supervisor/conf.d/xrdp.conf && \
    echo "process_name = xrdp-sesman" >> /etc/supervisor/conf.d/xrdp.conf && \
    echo "[program:xrdp]" >> /etc/supervisor/conf.d/xrdp.conf && \
    echo "command=/usr/sbin/xrdp -nodaemon" >> /etc/supervisor/conf.d/xrdp.conf && \
    echo "process_name = xrdp" >> /etc/supervisor/conf.d/xrdp.conf
    #echo "[Desktop Entry]" > /etc/xdg/autostart/setxkbmap.desktop && \
    #echo "Type=Application" >> /etc/xdg/autostart/setxkbmap.desktop && \
    #echo "Exec=setxkbmap gb" >> /etc/xdg/autostart/setxkbmap.desktop && \
    #echo "Hidden=false" >> /etc/xdg/autostart/setxkbmap.desktop && \
    #echo "X-MATE-Autostart-enabled=true" >> /etc/xdg/autostart/setxkbmap.desktop && \
    #echo "Name[C]=SetKeyBoard GB" >> /etc/xdg/autostart/setxkbmap.desktop && \
    #echo "Name=SetKeyBoard GB" >> /etc/xdg/autostart/setxkbmap.desktop && \
    #echo "Comment[C]=Sets the keyboard to GB" >> /etc/xdg/autostart/setxkbmap.desktop && \
    #echo "Comment=Sets the keyboard to GB" >> /etc/xdg/autostart/setxkbmap.desktop

# pytorch
ARG PYTHON_VERSION=3.6
RUN curl -o ~/miniconda.sh -O  https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh  && \
     chmod +x ~/miniconda.sh && \
     ~/miniconda.sh -b -p /opt/conda && \
     rm ~/miniconda.sh && \
     /opt/conda/bin/conda install -y python=$PYTHON_VERSION numpy pyyaml scipy ipython mkl mkl-include cython typing && \
     /opt/conda/bin/conda install -y -c pytorch magma-cuda90 tqdm && \
     /opt/conda/bin/conda clean -ya
ENV PATH /opt/conda/bin:$PATH
# This must be done before pip so that requirements.txt is available
WORKDIR /opt/pytorch
# COPY . .

# RUN git submodule update --init
# RUN TORCH_CUDA_ARCH_LIST="3.5 5.2 6.0 6.1 7.0+PTX" TORCH_NVCC_FLAGS="-Xfatbin -compress-all" \
#     CMAKE_PREFIX_PATH="$(dirname $(which conda))/../" \
#     pip install -v .

# RUN git clone https://github.com/pytorch/vision.git && cd vision && pip install -v .
# RUN conda install pytorch-cpu torchvision-cpu -c pytorch
RUN pip install https://download.pytorch.org/whl/cpu/torch-1.0.0-cp36-cp36m-linux_x86_64.whl && \
    pip install torchvision tqdm
WORKDIR /workspace
RUN chmod -R a+w /workspace

# khaiii
RUN git clone https://github.com/kakao/khaiii.git
WORKDIR /workspace/khaiii

RUN pip install cython
RUN pip install --upgrade pip

RUN mkdir build

WORKDIR /workspace/khaiii/build

RUN cmake ..
RUN make all
RUN make resource

# zeppelin
# `Z_VERSION` will be updated by `dev/change_zeppelin_version.sh`
ENV Z_VERSION="0.8.0"
ENV LOG_TAG="[ZEPPELIN_${Z_VERSION}]:" \
Z_HOME="/opt/zeppelin"

RUN echo "$LOG_TAG update and install basic packages" && \
# apt-get install -y software-properties-common && \
# apt-get install -y python-software-properties && \
# dockeapt install apt-transport-https software-properties-common && \
# dockeapt install apt-transport-https python-software-properties && \
apt install apt-transport-https && \
apt -y autoclean && \
apt -y dist-upgrade && \
apt-get install -y build-essential

RUN echo "$LOG_TAG install tini related packages" && \
apt-get install -y grep sed dpkg && \
TINI_VERSION=`curl https://github.com/krallin/tini/releases/latest | grep -o "/v.*\"" | sed 's:^..\(.*\).$:\1:'` && \
curl -L "https://github.com/krallin/tini/releases/download/v${TINI_VERSION}/tini_${TINI_VERSION}.deb" > tini.deb && \
dpkg -i tini.deb && \
rm tini.deb

ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
RUN echo "$LOG_TAG Install java8" && \
apt-get -y update && \
apt-get install -y openjdk-8-jdk && \
rm -rf /var/lib/apt/lists/*

# should install conda first before numpy, matploylib since pip and python will be installed by conda
RUN echo "$LOG_TAG Install miniconda2 related packages" && \
apt-get -y update && \
apt-get install -y bzip2 \
libglib2.0-0 libxext6 libsm6 libxrender1 \
git mercurial subversion && \
echo 'export PATH=/opt/conda/bin:$PATH' > /etc/profile.d/conda.sh

RUN echo "$LOG_TAG Install python related packages" && \
apt-get -y update && \
apt-get install -y python3-dev python3-pip && \
apt-get install -y gfortran && \
# numerical/algebra packages
 apt-get install -y libblas-dev libatlas-base-dev liblapack-dev && \
# font, image for matplotlib
apt-get install -y libpng-dev libfreetype6-dev libxft-dev && \
# for tkinter
apt-get install -y python3-tk libxml2-dev libxslt-dev zlib1g-dev && \
pip install numpy && \
pip install matplotlib

# RUN echo "$LOG_TAG Install R related packages" && \
# echo "deb http://cran.rstudio.com/bin/linux/ubuntu xenial/" | tee -a /etc/apt/sources.list && \
# gpg --keyserver keyserver.ubuntu.com --recv-key E084DAB9 && \
# gpg -a --export E084DAB9 | apt-key add - && \
# apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E298A3A825C0D65DFD57CBB651716619E084DAB9 && \
# # add-apt-repository 'deb https://cloud.r-project.org/bin/linux/ubuntu bionic-cran35/' && \
# add-apt-repository 'deb https://cloud.r-project.org/bin/linux/ubuntu cosmic-cran35/' && \
# apt-get -y update && \
# apt-get -y install r-base r-base-dev && \
# R -e "install.packages('knitr', repos='http://cran.us.r-project.org')" && \
# R -e "install.packages('ggplot2', repos='http://cran.us.r-project.org')" && \
# R -e "install.packages('googleVis', repos='http://cran.us.r-project.org')" && \
# R -e "install.packages('data.table', repos='http://cran.us.r-project.org')" && \
# # for devtools, Rcpp
#  apt-get -y install libcurl4-gnutls-dev libssl-dev && \
# R -e "install.packages('devtools', repos='http://cran.us.r-project.org')" && \
# R -e "install.packages('Rcpp', repos='http://cran.us.r-project.org')" && \
# Rscript -e "library('devtools'); library('Rcpp'); install_github('ramnathv/rCharts')"

RUN echo "$LOG_TAG Download Zeppelin binary" && \
wget -O /tmp/zeppelin-${Z_VERSION}-bin-all.tgz http://archive.apache.org/dist/zeppelin/zeppelin-${Z_VERSION}/zeppelin-${Z_VERSION}-bin-all.tgz && \
tar -zxvf /tmp/zeppelin-${Z_VERSION}-bin-all.tgz
# mv /tmp/zeppelin-${Z_VERSION}-bin-all ${Z_HOME} && \
# rm -rf /tmp/zeppelin-${Z_VERSION}-bin-all.tgz

RUN echo "$LOG_TAG Cleanup" && \
apt-get autoclean && \
apt-get clean

#ENTRYPOINT [ "/usr/bin/tini", "--" ]
#WORKDIR ${Z_HOME}
#CMD ["bin/zeppelin.sh"]

ADD xrdp.ini /etc/xrdp/xrdp.ini
ADD startup.sh /root/startup.sh
CMD ["/bin/bash", "/root/startup.sh"]

EXPOSE 22 8080 8888 3389
