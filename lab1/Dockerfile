FROM ubuntu:22.04

# Prevent interactive prompts during build
ENV DEBIAN_FRONTEND=noninteractive

# --------------------------------------------------
# Base system + build tooling
# --------------------------------------------------
RUN apt-get update && apt-get install -y \
    build-essential \
    clang \
    llvm \
    llvm-dev \
    gdb \
    git \
    curl \
    wget \
    vim \
    ca-certificates \
    python3 \
    python3-pip \
    cmake \
    make \
    sudo \
    \
    # ---- Xpdf build dependencies ----
    libx11-dev \
    libxext-dev \
    libxpm-dev \
    libfreetype6-dev \
    libfontconfig1-dev \
    zlib1g-dev \
    \
    && rm -rf /var/lib/apt/lists/*


# --------------------------------------------------
# AFL++ installation
# --------------------------------------------------
RUN git clone https://github.com/AFLplusplus/AFLplusplus.git /opt/aflplusplus \
    && cd /opt/aflplusplus \
    && make distrib \
    && make install

ENV PATH="/opt/aflplusplus:/usr/local/bin:${PATH}"

# --------------------------------------------------
# Build AFL++-instrumented Xpdf 3.02
# --------------------------------------------------
WORKDIR /opt

RUN wget https://dl.xpdfreader.com/old/xpdf-3.02.tar.gz \
    && tar -xvzf xpdf-3.02.tar.gz \
    && rm xpdf-3.02.tar.gz \
    && cd xpdf-3.02 \
    && CC=afl-clang-fast \
       CXX=afl-clang-fast++ \
       ./configure --prefix=/opt/xpdf-afl \
    && make -j$(nproc) \
    && make install

# Make Xpdf binaries easy to access
ENV PATH="/opt/xpdf-afl/bin:${PATH}"

# --------------------------------------------------
# Optional Python tooling (analysis scripts)
# --------------------------------------------------
WORKDIR /lab
COPY requirements.txt .
RUN pip3 install --no-cache-dir -r requirements.txt || true

# --------------------------------------------------
# Copy lab files (targets, inputs, scripts)
# --------------------------------------------------
COPY . .

# --------------------------------------------------
# Default shell for students
# --------------------------------------------------
CMD ["bash"]
