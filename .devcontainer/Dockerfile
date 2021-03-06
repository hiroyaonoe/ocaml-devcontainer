FROM ocaml/opam2:latest

# Avoid warnings by switching to noninteractive
ENV DEBIAN_FRONTEND=noninteractive

ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

USER root

# [Optional] Update UID/GID if needed
RUN if [ "$USER_GID" != "1000" ] || [ "$USER_UID" != "1000" ]; then \
    groupmod --gid $USER_GID $USERNAME \
    && usermod --uid $USER_UID --gid $USER_GID $USERNAME \
    && chown -R $USER_UID:$USER_GID /home/$USERNAME; \
  fi

# hadolint ignore=DL3008
RUN apt-get update \
  && apt-get -y install --no-install-recommends m4 \

  # [Optional] Add sudo support
  && apt-get install -y sudo \
  && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
  && chmod 0440 /etc/sudoers.d/$USERNAME \
  #
  # Clean up
  && apt-get autoremove -y \
  && apt-get clean -y \
  && rm -rf /var/lib/apt/lists/* 
  

# Install Opam packages
RUN opam update && \
  opam install \
  base core_kernel \
  ounit2 qcheck dune \
  ocamlformat && \
  opam pin add ocaml-lsp-server https://github.com/ocaml/ocaml-lsp.git && \
  opam install ocaml-lsp-server && \
  touch .ocamlformat


# Switch back to dialog for any ad-hoc use of apt-get
ENV DEBIAN_FRONTEND=dialog

# Add opam binaries to PATH.
ENV PATH=/home/opam/.opam/4.10/bin:$PATH

RUN eval $(opam env)
