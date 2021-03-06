FROM ubuntu:18.04

ENV APP_DIR /srv/app
ENV PATH ${APP_DIR}/venv/bin:$PATH

ENV TZ=Europe/Berlin
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt-get update && \
    apt-get install --yes \
            python3.6 \
            python3.6-venv \
            python3.6-dev \
            build-essential \
            tar \
            git \
            wget \
            npm \
            nodejs \
            locales \
            vim \
            # for nbconvert
            pandoc \
            texlive-xetex \
            texlive-fonts-recommended \
            texlive-generic-recommended

RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
    locale-gen

ENV LC_ALL en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US.UTF-8
# Set this to be on container storage, rather than under $HOME
ENV IPYTHONDIR ${APP_DIR}/venv/etc/ipython

RUN adduser --disabled-password --gecos "Default Jupyter user" jovyan

RUN install -d -o jovyan -g jovyan ${APP_DIR}

WORKDIR /home/jovyan

RUN apt-get install --yes python3-venv

USER jovyan
RUN python3.6 -m venv ${APP_DIR}/venv

COPY requirements.txt /tmp/requirements.txt
RUN pip install --no-cache-dir -r /tmp/requirements.txt

RUN pip install --no-cache-dir jupyterhub==0.8.1

RUN jupyter nbextension enable --py widgetsnbextension --sys-prefix && \
    jupyter serverextension enable --py jupyterlab --sys-prefix

# nbresuse to show users memory usage
RUN pip install nbresuse==0.3.0

# interact notebook extension
RUN pip install nbgitpuller==0.6.1

# Install nbzip
RUN pip install nbzip==0.1.0 && \
    jupyter serverextension enable  --sys-prefix --py nbzip && \
    jupyter nbextension     install --sys-prefix --py nbzip && \
    jupyter nbextension     enable  --sys-prefix --py nbzip

# Install RISE for presentation
RUN pip install RISE==5.4.1 && \
    jupyter-nbextension install rise --py --sys-prefix

# Install codefolding and google translate
RUN pip install jupyter_contrib_nbextensions==0.5.0 && \
    jupyter contrib nbextension install --sys-prefix && \
    jupyter nbextension enable --sys-prefix codefolding/main && \
    jupyter nbextension enable --sys-prefix nbTranslate/main

RUN pip install jupyterhub-firstuseauthenticator==0.11 

ADD password.dat /opt/password.dat
ADD password.dat ${IPYTHONDIR}/password.dat

# Install drawio
#RUN jupyter labextension install jupyterlab-drawio

# Install jupyterlab-latex
#RUN pip install jupyterlab_latex==0.4.1 && \
#    jupyter labextension install @jupyterlab/latex

ADD ipython_config.py ${IPYTHONDIR}/ipython_config.py

# HACK: We wanna make sure students don't hit the 60/hr/IP limit for github
# So we just put in a Personal Access Token for a dummy here.
# FIXME: Move the token to travis encrypted secrets
#ENV NETRC /srv/app/.netrc
#COPY netrc-gen /tmp/netrc-gen
#RUN /tmp/netrc-gen ${NETRC}

EXPOSE 8888
