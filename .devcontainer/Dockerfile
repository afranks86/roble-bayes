# Use the [Choice] comment to indicate option arguments that should appear in VS Code UX. Use a comma separated list.
#
# [Choice] Debian OS version: bullseye, buster
# ARG VARIANT="bullseye"
# FROM buildpack-deps:${VARIANT}-curl
FROM jupyter/datascience-notebook:r-4.3.1

USER root

RUN apt-get update && \
    apt-get install -y --no-install-recommends curl lmodern && \
    rm -rf /var/lib/apt/lists/*

ENV QUARTO_VERSION 1.3.450
RUN curl --silent -L --fail \
        https://github.com/quarto-dev/quarto-cli/releases/download/v${QUARTO_VERSION}/quarto-${QUARTO_VERSION}-linux-amd64.deb > /tmp/quarto.deb && \
    apt-get update && \
    apt-get install -y --no-install-recommends /tmp/quarto.deb && \
    rm -rf /tmp/quarto.deb /var/lib/apt/lists/* /tmp/library-script

USER ${NB_USER}

### R development
RUN pip install radian && \
    R -q -e 'install.packages(c("markdown", "languageserver", "httpgd"), repos="cloud.r-project.org")' &&\
    R -q -e 'remotes::install_github("ManuelHentschel/vscDebugger")'

### Prints Jupyter server token when terminal is opened
RUN echo "echo \"Jupyter server token: \$(jupyter server list 2>&1 | grep -oP '(?<=token=)[[:alnum:]]*')\"" > ${HOME}/.get-jupyter-url.sh && \
    echo "sh \${HOME}/.get-jupyter-url.sh" >> ${HOME}/.bashrc

RUN pip install jupyterlab-quarto

## Install cmdstanr and INLA-R
RUN R -q -e 'install.packages("cmdstanr", repos = c("https://mc-stan.org/r-packages/", getOption("repos")))' &&\
    R -q -e 'cmdstanr::install_cmdstan()' &&\
    R -q -e 'install.packages("bayesplot")' &&\
    R -q -e 'install.packages("INLA",repos=c(getOption("repos"),INLA="https://inla.r-inla-download.org/R/stable"), dep=TRUE)'


USER root

RUN apt-get update && \
    apt-get install -y --no-install-recommends tree && \
    rm -rf /var/lib/apt/lists/*
