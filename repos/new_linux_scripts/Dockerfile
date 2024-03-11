# General flow from https://github.com/plotly/Kaleido/blob/master/repos/linux_scripts/Dockerfile
# Which credits https://github.com/chromedp/docker-chromium-builder/blob/master/Dockerfile
# Customized to match modern Chromium build processes, following: https://chromium.googlesource.com/chromium/src/+/refs/tags/120.0.6099.199/docs/linux/build_instructions.md

FROM ubuntu:20.04

# Stable chromium from https://chromereleases.googleblog.com/search/label/Desktop%20Update
# Chromium 120.0.6099.199 on 2024-01-03
# Depot tools commit from 2023-08-29, which 120.0.6099.199 uses
ENV \
  CHROMIUM_TAG="120.0.6099.199" \
  DEPOT_TOOLS_COMMIT="9f3b33a275e7a5b19d8ce4aba7960d2a38858681" \
  DEPOT_TOOLS_UPDATE=0 \
  DEBIAN_FRONTEND=noninteractive \
  PATH="/depot_tools:$PATH"

# Installing tzdata here prevents an interactive prompt later on
RUN apt-get update && apt-get install -y curl git lsb-release python3 sudo tzdata python3-pip

RUN python3 -m pip install -U wheel setuptools

RUN \
  cd / \
  && git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git \
  && cd depot_tools \
  && git reset --hard $DEPOT_TOOLS_COMMIT
  
# Set the timezone and write a few debconf configurations to prevent the keyboard prompt
RUN \
  echo Etc/UTC > /etc/timezone \
  && echo tzdata tzdata/Areas select Etc | debconf-set-selections \
  && echo tzdata tzdata/Zones/Etc UTC | debconf-set-selections \
  && echo debconf debconf/frontend select Noninteractive | debconf-set-selections
  
RUN \
  curl -s https://chromium.googlesource.com/chromium/src/+/refs/tags/${CHROMIUM_TAG}/build/install-build-deps.py?format=TEXT \
  | base64 -d > install-build-deps.py && chmod +x ./install-build-deps.py \
  && ./install-build-deps.py --no-syms --no-android --no-arm --no-chromeos-fonts --no-nacl --no-prompt
