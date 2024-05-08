FROM debian:latest as setup

ENV IBC_VERSION=3.17.0
ENV INSTALL_FILENAME="ibgateway-10.19.2m-standalone-linux-x64.sh"

RUN apt update && \
    apt install --no-install-recommends -y \
    ca-certificates git libxtst6 libgtk-3-0 openbox procps python3 socat tigervnc-standalone-server unzip wget2 xterm  && \
# Setup noVNC for browser VNC access
    git clone --depth 1 https://github.com/novnc/noVNC.git && \
    chmod +x ./noVNC/utils/novnc_proxy && \
    git clone --depth 1 https://github.com/novnc/websockify.git /noVNC/utils/websockify && \
# Download and setup IBC
    wget2 https://github.com/IbcAlpha/IBC/releases/download/${IBC_VERSION}/IBCLinux-${IBC_VERSION}.zip -O ibc.zip \
    && unzip ibc.zip -d /opt/ibc \
    && rm ibc.zip && \
# Fetch hashes
    wget2 https://github.com/AndrewMarchukov/ibkr-docker/releases/download/10.19.2m-stable/ibgateway-10.19.2m-standalone-linux-x64.sh.sha256 \
    -O hash  && \
# Download IB Gateway (which contains TWS) and check hashes
    wget2 https://github.com/AndrewMarchukov/ibkr-docker/releases/download/10.19.2m-stable/ibgateway-10.19.2m-standalone-linux-x64.sh \
    -O "$INSTALL_FILENAME" \
    && sha256sum -c hash \
    && chmod +x "$INSTALL_FILENAME" \
    && yes '' | "./$INSTALL_FILENAME"  \
    && rm "$INSTALL_FILENAME"  && \
    mkdir -p ~/ibc && mv /opt/ibc/config.ini ~/ibc/config.ini && \
    chmod a+x *.sh /opt/ibc/*.sh /opt/ibc/scripts/*.sh && \
    apt-get remove --purge -y git wget2 unzip && \
    apt clean autoclean && \
    apt autoremove -y && \
    rm -rf /var/lib/apt /var/lib/dpkg /var/cache /var/log/apt /tmp/* && \
# Copy scripts
COPY image-files/start.sh image-files/replace.sh ./
# Override default noVNC file listing
COPY image-files/index.html /noVNC
CMD [ "./start.sh" ]