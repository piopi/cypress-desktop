FROM node:15.10.0-buster-slim

ENV DISPLAY=:1 \
    VNC_PORT=5901 \
    NO_VNC_PORT=6901 \
    VNC_COL_DEPTH=32 \
    VNC_RESOLUTION=1920x1080\
    # No interactive frontend during docker build
    DEBIAN_FRONTEND=noninteractive \
    TERM=vt100 \
    # avoid million NPM install messages
    npm_config_loglevel=warn \
    # allow installing when the main user is root
    npm_config_unsafe_perm=true \
    CI=1\
    # disable shared memory X11 affecting Cypress v4 and Chrome
    QT_X11_NO_MITSHM=1 \
    _X11_NO_MITSHM=1 \
    _MITSHM=0 \
    # point Cypress at the /root/cache no matter what user account is used
    CYPRESS_CACHE_FOLDER=/home/docker/.cache/Cypress

RUN apt-get update && \
    apt-get install --no-install-recommends -y \
    #Cypress dependencies
    libgtk2.0-0 libgtk-3-0 libnotify-dev libgconf-2-4 libgbm-dev libnss3 \
    libxss1 libasound2 libxtst6 xauth\
    #install xvfb window environement
    xvfb x11-utils dbus-x11 xfce4 xfce4-terminal tigervnc-standalone-server tigervnc-common\
    #utilities packages
    wget curl gpg git bzip2 vim procps python x11-xserver-utils sudo\
    #FireFox and Chrome dependencies
    ca-certificates fonts-liberation libappindicator3-1 xdg-utils;\
    # Clean up
    apt-get clean; \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN npm install -g npm@latest && npm --version; \
    npm install -g yarn@latest --force && yarn --version

# Install NOVNC.    
RUN     git clone --branch v1.2.0 --single-branch https://github.com/novnc/noVNC.git /opt/noVNC; \
    git clone --branch v0.9.0 --single-branch https://github.com/novnc/websockify.git /opt/noVNC/utils/websockify; \
    ln -s /opt/noVNC/vnc.html /opt/noVNC/index.html     

# Node libraries
RUN node -p process.versions
RUN npm install -g "cypress@9.1.1"; \
    cypress verify; \
    # Cypress cache and installed version
    # should be in the root user's home folder
    cypress cache path; \
    cypress cache list; \
    cypress info; \
    cypress version

# give every user read access to the "/root" folder where the binary is cached
RUN ls -la /home/docker/.cache/Cypress ; \
    chmod 777 /home/docker/.cache/Cypress && mkdir /src

RUN set -eux ;\
    mkdir /home/docker/.config; \
    mkdir -p /home/scripts ; \ 
    chmod -R 777 /home/docker ; \
    chmod -R 777 /home/docker/.config; \
    chmod -R 777 /src

RUN groupadd -g 999 docker && useradd -u 999 -g docker -G sudo -m -s /bin/bash docker ; \
    sed -i /etc/sudoers -re 's/^%sudo.*/%sudo ALL=(ALL:ALL) NOPASSWD: ALL/g' ; \
    sed -i /etc/sudoers -re 's/^root.*/root ALL=(ALL:ALL) NOPASSWD: ALL/g' ; \
    sed -i /etc/sudoers -re 's/^#includedir.*/## **Removed the include directive** ##"/g' ; \
    echo "docker ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers ; \
    echo "Customized the sudoers file for passwordless access to the docker user!" ; \
    echo "docker user:";  su - docker -c id



USER docker
# versions of local tools
RUN echo  " node version:    $(node -v) \n" \
    "npm version:     $(npm -v) \n" \
    "yarn version:    $(yarn -v) \n" \
    "debian version:  $(cat /etc/debian_version) \n" \
    "user:            $(whoami) \n"

COPY scripts/entrypoint.sh /home/scripts
copy assets/background.jpg /src
copy assets/cypressicon.png /src
COPY scripts/cypress_launch.sh /src
COPY assets/xfce4/.config /home/docker/.config


#Expose port 5901 to view display using VNC Viewer
EXPOSE 5901 6901
ENTRYPOINT ["/home/scripts/entrypoint.sh"]
