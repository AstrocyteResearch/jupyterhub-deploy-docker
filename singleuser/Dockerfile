# this is our first build stage, it will not persist in the final image
ARG DOCKER_NOTEBOOK_IMAGE
FROM $DOCKER_NOTEBOOK_IMAGE as intermediate

# install ssh
USER root
RUN  apt-get -yq update && \
     apt-get -yqq install ssh

USER jovyan
# add credentials on build
RUN mkdir /home/jovyan/.ssh/
COPY --chown=jovyan:users ./deploy_keys/deploy_key_analysis.pem /home/jovyan/.ssh/id_rsa
RUN chmod 600 /home/jovyan/.ssh/id_rsa

# make sure your domain is accepted
RUN touch /home/jovyan/.ssh/known_hosts
RUN ssh-keyscan github.com >> /home/jovyan/.ssh/known_hosts
RUN cd /home/jovyan && git clone git@github.com:AstrocyteResearch/analysis.git


FROM $DOCKER_NOTEBOOK_IMAGE
COPY --from=intermediate /home/jovyan/analysis /home/jovyan/analysis
COPY --from=intermediate /home/jovyan/.ssh/known_hosts /home/jovyan/.ssh/known_hosts

# install ssh
USER root
RUN  apt-get -yq update && \
     apt-get -yqq install ssh

USER jovyan

# Install Jupyterhub
ARG JUPYTERHUB_VERSION
RUN python3 -m pip install --no-cache jupyterhub==$JUPYTERHUB_VERSION

# Install Extensions

# Git https://github.com/jupyterlab/jupyterlab-git
RUN jupyter labextension install @jupyterlab/git && \
    pip install --upgrade jupyterlab-git && \
    jupyter serverextension enable --py jupyterlab_git

# Table of Contents  https://github.com/jupyterlab/jupyterlab-toc
RUN jupyter labextension install @jupyterlab/toc

# Hub integration
RUN jupyter labextension install @jupyterlab/hub-extension

# Commenting integration (may not work with singleuser)
# RUN jupyter labextension install jupyterlab-commenting

# Install Analysis
USER root
RUN chown jovyan:users -R /home/jovyan/analysis
USER jovyan

# RUN cd /home/jovyan/analysis && chmod -R 777 /home/jovyan/analysis
RUN cd /home/jovyan/analysis && python3 -m pip install -e .

