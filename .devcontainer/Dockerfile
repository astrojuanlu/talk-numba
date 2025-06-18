# See https://containers.dev/ for more information
# and https://github.com/mamba-org/micromamba-docker for micromamba usage
FROM ubuntu:22.04

# Install dependencies (including sudo)
RUN apt-get update && \
    apt-get install -y curl ca-certificates bzip2 sudo && \
    rm -rf /var/lib/apt/lists/*

# Create vscode user for Dev Container compatibility
RUN useradd -m -s /bin/bash -d /home/vscode vscode && \
    mkdir -p /etc/sudoers.d && \
    echo 'vscode ALL=(ALL) NOPASSWD:ALL' > /etc/sudoers.d/vscode && \
    chmod 0440 /etc/sudoers.d/vscode

# Install micromamba
ENV MAMBA_ROOT_PREFIX=/opt/conda
ENV PATH=$MAMBA_ROOT_PREFIX/bin:$PATH
RUN curl -Ls https://micro.mamba.pm/api/micromamba/linux-64/latest | tar -xvj -C /usr/local/bin --strip-components=1 bin/micromamba

# Ensure /opt/conda exists and set permissions for micromamba root
RUN mkdir -p /opt/conda && chown -R vscode:vscode /opt/conda

# Switch to vscode user and create environment
USER vscode
RUN micromamba create -y -n dev python=3.8 libllvm8 -c conda-forge && \
    micromamba clean -a -y

# Switch to root to modify /etc/bash.bashrc
USER root
RUN echo "source micromamba activate dev" >> /etc/bash.bashrc

# Switch back to vscode
USER vscode

# Expose Jupyter port
EXPOSE 8888

# Set working directory
WORKDIR /workspace

# Default command: start a shell
CMD ["bash"]
