FROM debian:latest

MAINTAINER Tyler W. H. Backman "tbackman@lbl.gov"

USER root

ENV LANG C.UTF-8
WORKDIR /root

# Install debian packages
RUN apt-get update \
	&& apt-get install -y \
		screen \
		wget \
		vim \
		man \
		pkg-config \
 		ipython \
		python3-pip \
		python3-all-dev \
		cython \
		python3-tk \
		python3-libxml2 \
		python3-lxml \
		tcsh \
		git \
	&& apt-get autoremove -y \
    && ln -s /usr/bin/python3 /usr/local/bin/python \
    && ln -s /usr/bin/pip3 /usr/local/bin/pip \
	&& rm -rf /var/lib/apt/lists/*

# install pip packages
RUN pip install -U pip setuptools
ADD ./requirements.txt /root/
RUN pip install -r requirements.txt

# install script to download GAMS
ADD ./install_gams.sh /root/
ADD ./gamsbatch /root/
ENV PATH="${PATH}:/opt/gams/gams24.5_linux_x64_64_sfx"

# install jQMM
RUN git clone -b extend https://github.com/somtirtharoy/jqmm.git \
    && mkdir -p /users/hgmartin/libraries/ \ 
    && mkdir -p /scratch/hgmartin_scratch/tests \ 
    && mkdir -p /root/tests \
    && ln -s /root/jqmm /users/hgmartin/libraries/quantmodel

# install svgfig
RUN git clone https://github.com/jpivarski/svgfig.git \
    && cd svgfig/svgfig-1.x \
    && python setup.py install \
    && cd \
    && rm -rf svgfig

# Add Tini
ENV TINI_VERSION v0.14.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /tini
RUN chmod +x /tini
ENTRYPOINT ["/tini", "--"]

# run jupyter server
CMD ["jupyter", "notebook", "--allow-root", "--no-browser", "--ip=0.0.0.0", "--notebook-dir=/root/jqmm"]
