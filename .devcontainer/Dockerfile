# See here for image contents: https://github.com/microsoft/vscode-dev-containers/tree/v0.134.1/containers/alpine/.devcontainer/base.Dockerfile
ARG PYTHON_VERSION="3.8"
ARG OCAML_VERSION="4.10"
FROM python:${PYTHON_VERSION}-buster as base-python

# https://pythonspeed.com/articles/multi-stage-docker-python/
RUN python -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"
RUN python -m pip install --upgrade pip
# For faster instalation
RUN pip install wheel
RUN pip install --no-cache-dir --no-warn-script-location pandas numpy jupyter requests matplotlib dash


# Once this gets fixed: https://discuss.ocaml.org/t/psa-ocaml-opam2-docker-images-updated-to-4-08-0-release/3948
# We can use smaller image
FROM ocaml/opam2:debian-10
# Redeclare arg: https://docs.docker.com/engine/reference/builder/#understand-how-arg-and-from-interact
ARG OCAML_VERSION

# Needed for merlin
USER root
RUN apt-get install -y m4
USER opam
RUN opam update && opam install merlin 
RUN opam user-setup install

# Copy installed python
COPY --from=base-python /opt/venv /opt/venv
COPY --from=base-python /usr/local/bin /usr/local/bin
COPY --from=base-python /usr/local/lib /usr/local/lib
ENV LD_LIBRARY_PATH='/lib:/usr/lib:/usr/local/lib'

# Make sure we use the virtualenv:
ENV PATH="/opt/venv/bin:$PATH"

# Get the correct ocaml
# This is a manual version of: RUN eval $(opam env)
ENV OPAM_SWITCH_PREFIX='/home/opam/.opam/'${OCAML_VERSION}
ENV CAML_LD_LIBRARY_PATH='/home/opam/.opam/'${OCAML_VERSION}'/lib/stublibs:Updated by package ocaml'
ENV OCAML_TOPLEVEL_PATH='/home/opam/.opam/'${OCAML_VERSION}'/lib/toplevel'
ENV MANPATH="$MANPATH"':/home/opam/.opam/'${OCAML_VERSION}'/man'
ENV OCAML_DOCKER_PATH='/home/opam/.opam/'${OCAML_VERSION}'/bin'
ENV PATH="$OCAML_DOCKER_PATH:$PATH"

# This enables us to install additional python libraries
USER root
# Expose jupyter port
EXPOSE 8888
