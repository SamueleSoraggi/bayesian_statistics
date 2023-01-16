FROM dreg.cloud.sdu.dk/ucloud-apps/jupyter-base:3.4.2

MAINTAINER "Samuele Soraggi <samuele@birc.au.dk>"

LABEL software="Genomics Sandbox" \
      author="Samuele Soraggi" \
      version="2022.08.01" \
      license="MIT" \
      description="Courses, datasets and software tools for genomics analysis"

USER 0

############# Intro to NGS (Aarhus summer course) - release 2022.08.01
RUN mkdir -p /usr/Intro_to_NGS \
 && chown -R "${NB_USER}":"${NB_GID}" /usr/Intro_to_NGS

USER 11042

WORKDIR /usr/Intro_to_NGS

RUN git init \
 && git remote add origin https://github.com/hds-sandbox/NGS_summer_course_Aarhus.git \
 && git config core.sparseCheckout true \
 && echo "Environments/" >> .git/info/sparse-checkout \
 && echo "Notebooks/" >> .git/info/sparse-checkout \
 && echo "Scripts/" >> .git/info/sparse-checkout \
 && git pull --depth=1 origin main \
 ## Data download
 && mkdir -p /usr/Intro_to_NGS/Data \
 && curl https://zenodo.org/record/6952995/files/clover.tar.gz?download=1 -o /usr/Intro_to_NGS/Data/Clover_Data.tar.gz \
 && tar -zxvf /usr/Intro_to_NGS/Data/Clover_Data.tar.gz -C /usr/Intro_to_NGS/Data/ \
 && curl https://zenodo.org/record/6952995/files/singlecell.tar.gz?download=1 -o /usr/Intro_to_NGS/Data/scrna_Data.tar.gz \
 && tar -zxvf /usr/Intro_to_NGS/Data/scrna_Data.tar.gz -C /usr/Intro_to_NGS/Data/ \
 && rm -f /usr/Intro_to_NGS/Data/*.tar.gz \
 ## Create environments
 && mamba env create -p "${CONDA_DIR}/envs/NGS_aarhus_py" -f /usr/Intro_to_NGS/Environments/python_environment.yml \
 && mamba env create -p "${CONDA_DIR}/envs/NGS_aarhus_r" -f /usr/Intro_to_NGS/Environments/R_environment.yml \
 && mamba clean --all -f -y \
 && fix-permissions "/home/${NB_USER}" \
 && fix-permissions "${CONDA_DIR}"

WORKDIR /work

## Add JupyterLab Extensions
RUN printf "Install JupyterLab extensions:" \
 && pip install --no-cache-dir "nteract-on-jupyter" \
 #&& jupyter labextension install "jupyter-threejs" \
 #&& jupyter labextension install "ipyvolume" \
 && jupyter lab clean -y \
 ## add support for LaTeX docs
 #&& pip install --no-cache-dir "jupyterlab-latex" \
 ## open spreadsheets such as Excel and OpenOffice
 && jupyter labextension install "jupyterlab-spreadsheet" \
 && jupyter lab clean -y \
 ## add top bar
 && pip install --no-cache-dir "jupyterlab-topbar" \
 && jupyter labextension install "jupyterlab-topbar-text" \
 && jupyter lab clean -y \
 ## add system monitor
 && pip install --no-cache-dir "jupyterlab-system-monitor" \
 ## add theme toggle bottom
 #&& jupyter labextension install "jupyterlab-theme-toggle" \
 && jupyter lab clean -y \
 ## add code formatter
 && pip install --no-cache-dir "autopep8" "yapf" "isort" "black" \
 && pip install --no-cache-dir "jupyterlab_code_formatter" \
 && jupyter lab build -y \
 && jupyter lab clean -y \
 && fix-permissions "/home/${NB_USER}" \
 ## add variableInspector
 && pip install --no-cache-dir "lckr-jupyterlab-variableinspector" \
 ## add nbdime
 && pip install --no-cache-dir "nbdime" \
 ## add Bokeh extension
 #&& pip install --no-cache-dir "jupyter_bokeh" \
 ## add Plotly extension
 && pip install --no-cache-dir  "plotly" \
 && jupyter labextension install "jupyterlab-plotly" \
 && fix-permissions "/home/${NB_USER}" \
 && fix-permissions "${CONDA_DIR}"

USER 0

## setup IGV browser
RUN npm install --global http-server \
 && git clone https://github.com/igvteam/igv-webapp.git /usr/igv-webapp \
 && fix-permissions /usr/igv-webapp

WORKDIR /usr/igv-webapp

RUN npm install \
 && npm run build

USER 11042
WORKDIR /work


## Set startup script in the PATH
COPY --chown="${NB_USER}":"${NB_GID}" start-jupyter "${CONDA_DIR}"/bin/
RUN chmod +x "${CONDA_DIR}"/bin/start-jupyter

