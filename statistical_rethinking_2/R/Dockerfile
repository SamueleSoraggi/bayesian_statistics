FROM dreg.cloud.sdu.dk/ucloud-apps/jupyter-base:3.4.2

MAINTAINER "Samuele Soraggi <samuele@birc.au.dk>"

LABEL software="Genomics Sandbox" \
      author="Samuele Soraggi" \
      version="2022.08.01" \
      license="MIT" \
      description="Courses, datasets and software tools for genomics analysis"

USER 0

############# Intro to NGS (Aarhus summer course) - release 2022.08.01
RUN mkdir -p /usr/rethinking \
 && chown -R "${NB_USER}":"${NB_GID}" /usr/rethinking

USER 11042

WORKDIR /usr/rethinking

COPY --chown="${NB_USER}":"${NB_GID}" environment.yml /usr/rethinking/

RUN mamba env create -p "${CONDA_DIR}/envs/rethinking_R" -f environment.yml \
 && mamba clean --all -f -y \
 && fix-permissions "/home/${NB_USER}" \
 && fix-permissions "${CONDA_DIR}"

RUN ${CONDA_DIR}/envs/rethinking_R/bin/R -e "devtools::install_github('rmcelreath/rethinking')" \
  && ${CONDA_DIR}/envs/rethinking_R/bin/R -e "library(cmdstanr); install_cmdstan(cores = 2)" \
  && ${CONDA_DIR}/envs/rethinking_R/bin/R -e "IRkernel::installspec(user=TRUE, name = 'rethinking_R', displayname = 'rethinking_R')"


WORKDIR /work

## Add JupyterLab Extensions
#RUN printf "Install JupyterLab extensions:" \
# && pip install --no-cache-dir "nteract-on-jupyter" \
 #&& jupyter labextension install "jupyter-threejs" \
 #&& jupyter labextension install "ipyvolume" \
# && jupyter lab clean -y \
 ## add support for LaTeX docs
 #&& pip install --no-cache-dir "jupyterlab-latex" \
 ## open spreadsheets such as Excel and OpenOffice
# && jupyter labextension install "jupyterlab-spreadsheet" \
# && jupyter lab clean -y \
 ## add top bar
# && pip install --no-cache-dir "jupyterlab-topbar" \
# && jupyter labextension install "jupyterlab-topbar-text" \
# && jupyter lab clean -y \
 ## add system monitor
# && pip install --no-cache-dir "jupyterlab-system-monitor" \
 ## add theme toggle bottom
 #&& jupyter labextension install "jupyterlab-theme-toggle" \
# && jupyter lab clean -y \
 ## add code formatter
# && pip install --no-cache-dir "autopep8" "yapf" "isort" "black" \
# && pip install --no-cache-dir "jupyterlab_code_formatter" \
# && jupyter lab build -y \
# && jupyter lab clean -y \
# && fix-permissions "/home/${NB_USER}" \
 ## add variableInspector
# && pip install --no-cache-dir "lckr-jupyterlab-variableinspector" \
 ## add nbdime
# && pip install --no-cache-dir "nbdime" \
 ## add Bokeh extension
 #&& pip install --no-cache-dir "jupyter_bokeh" \
 ## add Plotly extension
# && pip install --no-cache-dir  "plotly" \
# && jupyter labextension install "jupyterlab-plotly" \
# && fix-permissions "/home/${NB_USER}" \
# && fix-permissions "${CONDA_DIR}"


## Set startup script in the PATH
COPY --chown="${NB_USER}":"${NB_GID}" start-jupyter "${CONDA_DIR}"/bin/
RUN chmod +x "${CONDA_DIR}"/bin/start-jupyter

WORKDIR /usr/rethinking/