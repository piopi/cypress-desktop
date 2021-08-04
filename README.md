# Cypress Docker Desktop [![Docker Image CI](https://github.com/piopi/cypress-desktop/actions/workflows/docker-image.yml/badge.svg)](https://github.com/piopi/cypress-desktop/actions/workflows/docker-image.yml)

### Overview

A Dockerized Cypress Image with an integrated light-weight desktop environment. 

![](/screenshots/Capture.PNG)
*noVNC view of the Container running*

The Image comes with noVNC to allow user to view the desktop environment with their browsers.

Two type of images are provided:

| Image                                    | Default                                        | Description                                                  | Monthly pulls |
| ---------------------------------------- | ---------------------------------------------- | ------------------------------------------------------------ | ------------- |
| [piopirahl/cypress-desktop-base](https://github.com/piopi/cypress-desktop/tree/main/base)     | `piopirahl/cypress-desktop-base:cypress8.2.0`  | Cypress with the desktop environment only.                   |   ![](https://img.shields.io/docker/pulls/piopirahl/cypress-desktop-base.svg?cacheSeconds=3600)            |
| [piopirahl/cypress-desktop-browsers](https://github.com/piopi/cypress-desktop/tree/main/browsers) | `piopirahl/cypress-desktop-browsers:all-1.0.5` | Cypress with the desktop environment and with one or multiple browsers.  The tag `  all-x.x.x` include all 3 browsers (Chrome, Firefox and Edge). | ![](https://img.shields.io/docker/pulls/piopirahl/cypress-desktop-browsers.svg?cacheSeconds=3600)         |

### Usage

**With the base Image:** 

```
docker run -d -p 6901:6901 -p 5901:5901 -v $PWD:/src/cypress piopirahl/cypress-desktop-base:cypress6.7.1
```

**With the Browser Image:** 

```
docker run -d -p 6901:6901 -p 5901:5901 -v $PWD:/src/cypress piopirahl/cypress-desktop-browsers:all-1.0.1
```

You will be able to access the noVNC windows at [http://localhost:6901](http://localhost:6901) or use your VNC viewer with `localhost:5901`

#### Ports

**6901** is exposed by default for the noVNC.

**5901** is exposed by default for VNC.

#### Volumes Binding 
To share your cypress host files with the container, you need to bind the host volume with `/src` on the container. 
*This is the location where Cypress files/configuration reside on the* container.
**E.g:** `-v $PWD:/src`

#### Custom configs

The config files are stored under `/home/docker/.config` on the container. 
In order, to save on your host your configs, you can follow those steps:
1. Run the docker image to generate the configs on the container
```
docker run -d -p 6901:6901 -p 5901:5901 --name cypressDesktop piopirahl/cypress-desktop:1.0.3 
```
2. Copy the content of the container on the host 
```
mkdir config
docker cp desktop:/home/docker/.config  $PWD/config
```
3. Stop the running container and start a new one with a mounted volume 

   ```
   docker rm -f desktop
   docker run -d -p 6901:6901 -p 5901:5901 --name desktop -v $PWD/config/.config:/home/docker/.config piopirahl/docker-desktop:1.0.1 
   ```

4. Now your local configs will be saved on your host machine

### Sudo password
The default username in the container is docker and you can use `sudo` without the need of a password.

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
