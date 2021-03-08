# Cypress Docker Desktop [![Docker Image CI](https://github.com/piopi/cypress-desktop/actions/workflows/docker-image.yml/badge.svg)](https://github.com/piopi/cypress-desktop/actions/workflows/docker-image.yml)

### Overview

A Dockerized Cypress Image with an integrated light-weight desktop environment. 

![](/screenshots/Capture.PNG)
*noVNC view of the Container running*

The Image comes with noVNC to allow user to view the desktop environment with their browsers.

Two type of images are provided:

| Image                                    | Default                                        | Description                                                  | Monthly pulls |
| ---------------------------------------- | ---------------------------------------------- | ------------------------------------------------------------ | ------------- |
| [piopirahl/cypress-desktop-base](https://github.com/piopi/cypress-desktop/tree/main/base)     | `piopirahl/cypress-desktop-base:cypress6.6.0`  | Cypress with the desktop environment only.                   |   ![](https://img.shields.io/docker/pulls/piopirahl/cypress-desktop-base.svg?maxAge=604800)            |
| [piopirahl/cypress-desktop-browsers](https://github.com/piopi/cypress-desktop/tree/main/browsers) | `piopirahl/cypress-desktop-browsers:all-1.0.0` | Cypress with the desktop environment and with one or multiple browsers.  The tag `  all-x.x.x` include all 3 browsers (Chrome, Firefox and Edge). |      ![](https://img.shields.io/docker/pulls/piopirahl/cypress-desktop-browsers.svg?maxAge=604800)         |

### Usage

**With the base Image:** 

```
docker run -d -p 6901:6901 -p 5901:5901 piopirahl/cypress-desktop-base:cypress6.6.0 -v $PWD:/src
```

**With the Browser Image:** 

```
docker run -d -p 6901:6901 -p 5901:5901 piopirahl/cypress-desktop-browsers:all-1.0.0 -v $PWD:/src
```

You will be able to access the noVNC windows at [http://localhost:6901](http://localhost:6901) or use your VNC viewer with `localhost:5901`

#### Ports

**6901** is exposed by default for the noVNC.

**5901** is exposed by default for VNC.

#### Volumes Binding 
To share your cypress host files with the container, you need to bind the host volume with `/src` on the container. 
*This is the location where Cypress files/configuration reside on the* container.
**E.g:** `-v $PWD:/src`

### DockerHub

DockerHub link of the images:

- https://hub.docker.com/repository/docker/piopirahl/cypress-desktop-base
- https://hub.docker.com/repository/docker/piopirahl/cypress-desktop-browsers

## Image Contents

- [Xvfb](http://www.x.org/releases/X11R7.6/doc/man/man1/Xvfb.1.xhtml) - X11 in a virtual framebuffer
- [TigerVNC](https://github.com/TigerVNC/tigervnc) - A VNC server that scrapes the above X11 server
- [noNVC](https://github.com/novnc/noVNC) - A HTML5 canvas vnc viewer
- [xfce4](https://www.xfce.org/) - a small desktop environment
- [Cypress](https://github.com/cypress-io/cypress)-  Testing tool



## Maintainers

Mostapha El Sabah [Piopi](https://github.com/piopi)
