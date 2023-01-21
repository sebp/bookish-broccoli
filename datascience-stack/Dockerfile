# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
ARG OWNER=jupyter
ARG BASE_CONTAINER=$OWNER/minimal-notebook
FROM $BASE_CONTAINER

LABEL maintainer="Jupyter Project <jupyter@googlegroups.com>"

# Fix: https://github.com/hadolint/hadolint/wiki/DL4006
# Fix: https://github.com/koalaman/shellcheck/wiki/SC3014
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

USER root

RUN apt-get update --yes && \
    apt-get install --yes --no-install-recommends \
    # for cython: https://cython.readthedocs.io/en/latest/src/quickstart/install.html
    build-essential \
    # for latex labels
    cm-super \
    dvipng \
    # for matplotlib anim
    ffmpeg && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

USER ${NB_UID}

# Install Python 3 packages
RUN mamba install --quiet --yes \
    'altair' \
    'beautifulsoup4' \
    'black' \
    'bokeh' \
    'bottleneck' \
    'cloudpickle' \
    'conda-forge::blas=*=openblas' \
    'cython' \
    'dataprep' \
    'dill' \
    'dtale' \
    'lifelines' \
    'flake8' \
    'h5py' \
    'ipympl'\
    'ipywidgets' \
    # Temporary fix for: https://github.com/jupyter/docker-stacks/issues/1851
    'jupyter_server>=2.0.0' \
    'jupyterlab-git' \
    'jupyterlab-lsp' \
    'matplotlib-base' \
    'networkx' \
    'numba' \
    'numexpr' \
    'openpyxl' \
    'pandas' \
    'patsy' \
    'protobuf' \
    'pyflakes' \
    'pytables' \
    'python-lsp-server' \
    'rope' \
    'scikit-learn' \
    'scikit-survival' \
    'scipy' \
    'seaborn' \
    'statsmodels' \
    'theme-darcula' \
    'widgetsnbextension'\
    'yapf' \
    'xlrd' && \
    mamba clean --all -f -y && \
    fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}"

# Install facets which does not have a pip or conda package at the moment
WORKDIR /tmp
RUN git clone https://github.com/PAIR-code/facets.git && \
    jupyter nbextension install facets/facets-dist/ --sys-prefix && \
    rm -rf /tmp/facets && \
    fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}"

# Import matplotlib the first time to build the font cache.
ENV XDG_CACHE_HOME="/home/${NB_USER}/.cache/"

RUN MPLBACKEND=Agg python -c "import matplotlib.pyplot" && \
    fix-permissions "/home/${NB_USER}"

RUN mkdir -p "/home/${NB_USER}/.jupyter/lab/user-settings" \
    "/home/${NB_USER}/.ipython/profile_default" && \
    fix-permissions "/home/${NB_USER}/.jupyter/lab/user-settings" && \
    fix-permissions "/home/${NB_USER}/.ipython/profile_default"
COPY --chown="${NB_UID}:${NB_GID}" user-settings "/home/${NB_USER}/.jupyter/lab/user-settings/"
RUN echo 'c = get_config()' > "/home/${NB_USER}/.ipython/profile_default/ipython_config.py" && \
    echo 'c.Completer.use_jedi = False' >> "/home/${NB_USER}/.ipython/profile_default/ipython_config.py"

USER root

# R pre-requisites
RUN apt-get update --yes && \
    apt-get install --yes --no-install-recommends \
    fonts-dejavu \
    gfortran \
    gcc && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

USER ${NB_UID}

# R packages including IRKernel which gets installed globally.
# r-e1071: dependency of the caret R package
RUN mamba install --quiet --yes \
    'r-base' \
    'r-caret' \
    'r-crayon' \
    'r-devtools' \
    'r-e1071' \
    'r-forecast' \
    'r-ggplot2' \
    'r-glmnet' \
    'r-grf' \
    'r-hexbin' \
    'r-htmltools' \
    'r-htmlwidgets' \
    'r-irkernel' \
    'r-languageserver' \
    'r-ranger' \
    'r-reshape2' \
    'r-rcolorbrewer' \
    'r-rcurl' \
    'r-rmarkdown' \
    'r-rodbc' \
    'r-rsqlite' \
    'r-rstanarm' \
    'r-shiny' \
    'r-survival' \
    'r-tidyverse' \
    'unixodbc' && \
    mamba clean --all -f -y && \
    fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}"

WORKDIR "${HOME}"
