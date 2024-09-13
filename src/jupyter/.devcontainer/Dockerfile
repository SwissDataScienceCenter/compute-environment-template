FROM docker.io/jupyter/${templateOption:baseVariant}:${templateOption:imageVariant}

USER root
# Generally, Dev Container Features assume that the non-root user (in this case jovyan)
# is in a group with the same name (in this case jovyan). So we must first make that so.
RUN groupadd jovyan \
    && usermod -g jovyan -a -G users jovyan

# install jupyterlab
ENV CONDA_ENVS_DIRS /work/envs
ENV SHELL=/bin/bash
RUN conda install -y jupyterlab && \
    jupyter labextension disable "@jupyterlab/apputils-extension:announcements" && \
    ln -s /opt/conda/bin/jupyter-lab /opt/conda/bin/jupyter-notebook

# Copy environment.yml (if found) to a temp location so we update the environment. Also
# copy "noop.txt" so the COPY instruction does not fail if no environment.yml exists.
COPY environment.yml* .devcontainer/noop.txt /tmp/conda-tmp/

USER jovyan
RUN if [ -f "/tmp/conda-tmp/environment.yml" ]; then umask 0002 && \
    /opt/conda/bin/conda env create -n project -f /tmp/conda-tmp/environment.yml; fi && \
    conda init && \
    conda install -y -n project ipykernel && \
    /opt/conda/envs/project/bin/python -m ipykernel install --name project --display-name "Project environment" --user && \
    echo "conda activate project" >> $HOME/.bashrc
