# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
ARG REGISTRY=quay.io
ARG OWNER=jupyter
ARG BASE_CONTAINER=$REGISTRY/$OWNER/minimal-notebook:python-3.11
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
    'category_encoders' \
    'cloudpickle' \
    'conda-forge::blas=*=openblas' \
    'cython' \
    #'dataprep' \
    'dill' \
    'dtale' \
    'lifelines' \
    'h5py' \
    'holoviews' \
    'hvplot' \
    'hydra-core' \
    'ipympl'\
    'ipywidgets' \
    'jupyterlab-git' \
    'jupyterlab-lsp' \
    'conda-forge::libarrow=*=*_cpu' \
    'matplotlib-base' \
    'networkx' \
    'numba' \
    'numexpr' \
    'openpyxl' \
    'panel' \
    'pandas>=2.0,<3' \
    'papermill' \
    'patsy' \
    'pingouin' \
    'pre-commit' \
    'protobuf' \
    'pyarrow>=10.0.1' \
    'pyreadstat' \
    'pytables' \
    'python-lsp-server' \
    'rope' \
    'ruff' \
    'scikit-learn' \
    'scikit-survival' \
    'scipy' \
    'seaborn' \
    'statsmodels' \
    'theme-darcula' \
    'widgetsnbextension'\
    'ydata-profiling' \
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

RUN "${CONDA_DIR}/bin/pip" install --no-cache --no-input \
    hydra-joblib-launcher

# Import matplotlib the first time to build the font cache.
ENV XDG_CACHE_HOME="/home/${NB_USER}/.cache/"

RUN MPLBACKEND=Agg python -c "import matplotlib.pyplot" && \
    fix-permissions "/home/${NB_USER}"

USER root

# R pre-requisites
RUN apt-get update --yes && \
    apt-get install --yes --no-install-recommends \
    fonts-dejavu \
    unixodbc \
    unixodbc-dev \
    r-cran-rodbc \
    gfortran \
    gcc && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

USER ${NB_UID}

# R packages including IRKernel which gets installed globally.
# r-e1071: dependency of the caret R package
RUN mamba install --quiet --yes \
    'r-arrow>=10.0.0,<11' \
    'r-base' \
    'r-caret' \
    'r-crayon' \
    'r-devtools' \
    'r-e1071' \
    'r-forecast' \
    'r-ggplot2' \
    'r-ggpubr' \
    'r-glmnet' \
    'r-grf' \
    'r-gtsummary' \
    'r-hexbin' \
    'r-htmltools' \
    'r-htmlwidgets' \
    'r-irkernel' \
    'r-languageserver' \
    'r-mlr3verse' \
    'r-patchwork' \
    'r-plotly' \
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
    'radian' \
    'unixodbc' && \
    mamba clean --all -f -y && \
    fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}"

WORKDIR "${HOME}"
