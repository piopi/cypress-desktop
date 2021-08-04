FROM piopirahl/cypress-desktop-base:cypress8.2.0
USER root
# install Chrome browser
ENV CHROME_VERSION 92.0.4515.131
RUN wget -O /usr/src/google-chrome-stable_current_amd64.deb "http://dl.google.com/linux/chrome/deb/pool/main/g/google-chrome-stable/google-chrome-stable_${CHROME_VERSION}-1_amd64.deb" && \
  dpkg -i /usr/src/google-chrome-stable_current_amd64.deb; \
  apt-get install -f -y && \
  rm -f /usr/src/google-chrome-stable_current_amd64.deb; \
  apt-get clean; \
  rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*; \
  google-chrome --version

# install Firefox browser
ARG FIREFOX_VERSION=90.0
RUN wget --no-verbose -O /tmp/firefox.tar.bz2 https://download-installer.cdn.mozilla.net/pub/firefox/releases/$FIREFOX_VERSION/linux-x86_64/en-US/firefox-$FIREFOX_VERSION.tar.bz2 \
  && tar -C /opt -xjf /tmp/firefox.tar.bz2 \
  && rm /tmp/firefox.tar.bz2 \
  && ln -fs /opt/firefox/firefox /usr/bin/firefox

# install Microsoft Edge
RUN set -eux; \
  curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg; \
  install -o root -g root -m 644 microsoft.gpg /etc/apt/trusted.gpg.d/; \
  sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/edge stable main" > /etc/apt/sources.list.d/microsoft-edge-dev.list'; \
  rm microsoft.gpg;\
  apt update && apt install microsoft-edge-dev --no-install-recommends -y; \
  ln -s /usr/bin/microsoft-edge /usr/bin/edge; \
  # Clean up
  apt-get clean; \
  rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

USER docker
# versions of local tools
RUN echo  " node version:    $(node -v) \n" \
  "npm version:     $(npm -v) \n" \
  "yarn version:    $(yarn -v) \n" \
  "debian version:  $(cat /etc/debian_version) \n" \
  "Edge version:    $(edge --version) \n" \
  "Chrome version:  $(google-chrome --version) \n" \
  "Firefox version: $(firefox --version) \n" \
  "user:            $(whoami) \n"
COPY scripts/entrypoint.sh /src
ENTRYPOINT ["/src/entrypoint.sh"]


