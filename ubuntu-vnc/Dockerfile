FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive
ENV USER=neko
ENV PASSWORD=79967996
ENV HOME=/home/neko
ENV DISPLAY=:1

# Install desktop, VNC, audio, browser
RUN apt update && apt install -y \
    xfce4 xfce4-goodies \
    tigervnc-standalone-server tigervnc-common \
    novnc websockify \
    firefox \
    sudo \
    dbus-x11 \
    pulseaudio \
    alsa-utils \
    xterm \
    wget curl \
    && apt clean

# Create user
RUN useradd -m -s /bin/bash ${USER} \
    && echo "${USER}:${PASSWORD}" | chpasswd \
    && usermod -aG sudo,audio ${USER}

# VNC password
RUN mkdir -p ${HOME}/.vnc \
    && echo "${PASSWORD}" | vncpasswd -f > ${HOME}/.vnc/passwd \
    && chmod 600 ${HOME}/.vnc/passwd \
    && chown -R ${USER}:${USER} ${HOME}

# VNC startup
RUN echo '#!/bin/sh\nunset SESSION_MANAGER\nunset DBUS_SESSION_BUS_ADDRESS\nexec startxfce4 &' \
    > ${HOME}/.vnc/xstartup \
    && chmod +x ${HOME}/.vnc/xstartup \
    && chown ${USER}:${USER} ${HOME}/.vnc/xstartup

# PulseAudio TCP (WiFi/network audio)
RUN sed -i 's/;load-module module-native-protocol-tcp/load-module module-native-protocol-tcp auth-anonymous=1/' /etc/pulse/default.pa

# noVNC
RUN ln -s /usr/share/novnc/vnc.html /usr/share/novnc/index.html

USER ${USER}
WORKDIR ${HOME}

EXPOSE 5901 6080

CMD vncserver :1 -geometry 1280x800 -depth 24 && \
    websockify --web=/usr/share/novnc 6080 localhost:5901
