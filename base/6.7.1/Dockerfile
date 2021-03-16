FROM node:15.10.0-buster-slim

ENV DISPLAY=:1 \
    VNC_PORT=5901 \
    NO_VNC_PORT=6901 \
    VNC_COL_DEPTH=32 \
    VNC_RESOLUTION=1920x1080 

# No interactive frontend during docker build
ENV DEBIAN_FRONTEND=noninteractive 

# "fake" dbus address to prevent errors
ENV DBUS_SESSION_BUS_ADDRESS=/dev/null

# a few environment variables to make NPM installs easier
    # good colors for most applications
ENV TERM vt100 \
    # avoid million NPM install messages
    npm_config_loglevel warn \
    # allow installing when the main user is root
    npm_config_unsafe_perm true \
    CI=1

RUN apt-get update && \
  apt-get install --no-install-recommends -y \
  #Cypress dependencies
  libgtk2.0-0 libgtk-3-0 libnotify-dev libgconf-2-4 libgbm-dev libnss3 \
  libxss1 libasound2 libxtst6 xauth\
  #install xvfb window environement
  xvfb x11-utils tint2 dbus-x11 xfce4 xfce4-terminal\
  #utilities packages
  wget curl gpg git bzip2 vim procps python x11-xserver-utils\
  #FireFox and Chrome dependencies
  ca-certificates fonts-liberation libappindicator3-1 xdg-utils;\
  # Clean up
  apt-get clean; \
  rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN npm install -g npm@latest && npm --version; \
    npm install -g yarn@latest --force && yarn --version

# Install NOVNC and TigerVNC.    
RUN     wget -qO- https://dl.bintray.com/tigervnc/stable/tigervnc-1.8.0.x86_64.tar.gz | tar xz --strip 1 -C /; \
        git clone --branch v1.2.0 --single-branch https://github.com/novnc/noVNC.git /opt/noVNC; \
        git clone --branch v0.9.0 --single-branch https://github.com/novnc/websockify.git /opt/noVNC/utils/websockify; \
        ln -s /opt/noVNC/vnc.html /opt/noVNC/index.html     
# disable shared memory X11 affecting Cypress v4 and Chrome
ENV QT_X11_NO_MITSHM=1 \
    _X11_NO_MITSHM=1 \
    _MITSHM=0
# Node libraries
RUN node -p process.versions
# point Cypress at the /root/cache no matter what user account is used
ENV CYPRESS_CACHE_FOLDER=/root/.cache/Cypress
RUN npm install -g "cypress@6.7.1"; \
    cypress verify; \
    # Cypress cache and installed version
    # should be in the root user's home folder
    cypress cache path; \
    cypress cache list; \
    cypress info; \
    cypress version

# give every user read access to the "/root" folder where the binary is cached
RUN ls -la /root
RUN chmod 755 /root && mkdir /src
# versions of local tools
RUN echo  " node version:    $(node -v) \n" \
  "npm version:     $(npm -v) \n" \
  "yarn version:    $(yarn -v) \n" \
  "debian version:  $(cat /etc/debian_version) \n" \
  "user:            $(whoami) \n"
COPY scripts/entrypoint.sh /src
COPY assets/background.jpg /src
COPY assets/cypressicon.png /src
COPY scripts/cypress_launch.sh /src
COPY assets/xfce4/.config /root/.config
#Expose port 5901 to view display using VNC Viewer
EXPOSE 5901 6901
ENTRYPOINT ["/src/entrypoint.sh"]