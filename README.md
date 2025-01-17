# Docker image for Jenkins agents connected over SSH

[![Join the chat at https://gitter.im/jenkinsci/docker](https://badges.gitter.im/jenkinsci/docker.svg)](https://gitter.im/jenkinsci/docker?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
[![GitHub stars](https://img.shields.io/github/stars/jenkinsci/docker-ssh-agent?label=GitHub%20stars)](https://github.com/jenkinsci/docker-ssh-agent)
[![Docker Pulls](https://img.shields.io/docker/pulls/jenkins/ssh-agent.svg)](https://hub.docker.com/r/jenkins/ssh-agent/)
[![GitHub release](https://img.shields.io/github/release/jenkinsci/docker-ssh-agent.svg?label=changelog)](https://github.com/jenkinsci/docker-ssh-agent/releases)

A [Jenkins](https://jenkins.io) agent image which allows using SSH to establish the connection.
It can be used together with the [SSH Build Agents plugin](https://plugins.jenkins.io/ssh-slaves) or other similar plugins.

See [Jenkins Distributed builds](https://wiki.jenkins-ci.org/display/JENKINS/Distributed+builds) for more info.

## Running

### Running with the SSH Build Agents plugin

To run a Docker container

```bash
docker run -d --rm --name=agent -e "JENKINS_AGENT_SSH_PUBKEY=<public_key>" jenkins/ssh-agent
```

 - `-d`: To start a container in detached mode, use the `-d` option. Containers started in detached mode exit when the root process used to run the container exits, unless you also specify the --rm option.
 - `--rm`: If you use -d with --rm, the container is removed when it exits or when the daemon exits, whichever happens first.
 - `--name`: Assigns a name to the container. If you do not specify a name, Docker generates a random name.

Please note none of these options are mandatory, they are just examples.

You will then be able to connect this agent using the [SSH Build Agents plugin](https://plugins.jenkins.io/ssh-slaves) as "jenkins" with the matching private key.

When using the Linux image, you have to set the value of the `Remote root directory` to `/home/jenkins/agent` in the agent configuration UI.

![Remote root directory with a Linux agent](docs/ssh-plugin-remote-root-directory-linux.png "Remote root directory with a Linux agent")

When using the Windows image, you have to set the value of the `Remote root directory` to `C:/Users/jenkins/Work` in the agent configuration UI.

![Remote root directory with a Windows agent](docs/ssh-plugin-remote-root-directory-windows.png "Remote root directory with a Windows agent")

If you intend to use another directory than `/home/jenkins/agent` under Linux or `C:/Users/jenkins/Work` under Windows, don't forget to add it as a data volume.

```bash
docker run -v docker-volume-for-jenkins-ssh-agent:/home/jenkins/agent:rw jenkins/ssh-agent "<public key>"
```

### How to use this image with Docker Plugin

To use this image with [Docker Plugin](https://plugins.jenkins.io/docker-plugin), you need to pass the public SSH key using environment variable `JENKINS_AGENT_SSH_PUBKEY` and not as a startup argument.

In _Environment_ field of the Docker Template (advanced section), just add:

    JENKINS_AGENT_SSH_PUBKEY=<YOUR PUBLIC SSH KEY HERE>

Don't put quotes around the public key.

Please note that you have to set the value of the `Remote File System Root` to `/home/jenkins/agent` in the Docker Agent Template configuration UI.

![Remote File System Root](docs/docker-plugin-remote-filesystem-root.png "Remote File System Root directory")

If you intend to use another directory than `/home/jenkins/agent`, don't forget to add it as a data volume.

![Docker Volumes mounts](docs/docker-plugin-volumes.png "Docker Volumes mounts")

You should be all set.

## Extending the image

Should you need to extend the image, you could use something along those lines:

```Dockerfile
FROM jenkins/ssh-agent:bullseye-jdk17 as ssh-agent
# [...]
COPY --chown=jenkins mykey "${JENKINS_AGENT_HOME}"/.ssh/mykey
# [...]
``` 

## Configurations

The image has several supported configurations, which can be accessed via the following tags:

`${IMAGE_VERSION}` can be found on the [releases](https://github.com/jenkinsci/docker-ssh-agent/releases) page.

* `latest`, `latest-jdk11`, `jdk11`, `latest-bullseye-jdk11`, `bullseye-jdk11`, `${IMAGE_VERSION}`, `${IMAGE_VERSION}-jdk11`, ([Dockerfile](11/bullseye/Dockerfile))
* `latest-jdk8`, `jdk8`, `latest-bullseye-jdk8`, `bullseye-jdk8`, `${IMAGE_VERSION}-jdk8`, ([Dockerfile](8/bullseye/Dockerfile))
* `latest-jdk17`, `jdk17`, `latest-bullseye-jdk17`, `bullseye-jdk17`, `${IMAGE_VERSION}-jdk17`, ([Dockerfile](17/bullseye/Dockerfile))
* `latest-alpine-jdk8`, `alpine-jdk8`, `${IMAGE_VERSION}-jdk8`, ([Dockerfile](8/alpine/Dockerfile))
* `nanoserver-1809`, `nanoserver-ltsc2019`, `nanoserver-1809-jdk11`, `nanoserver-ltsc2019-jdk11`, `${IMAGE_VERSION}-nanoserver-1809`, `${IMAGE_VERSION}-nanoserver-ltsc2019`, `${IMAGE_VERSION}-nanoserver-1809-jdk11`, `${IMAGE_VERSION}-nanoserver-ltsc2019-jdk11` ([Dockerfile](11/windows/nanoserver-ltsc2019/Dockerfile))
* `nanoserver-1809-jdk8`, `nanoserver-ltsc2019-jdk8`, `${IMAGE_VERSION}-nanoserver-1809-jdk8`, `${IMAGE_VERSION}-nanoserver-ltsc2019-jdk8` ([Dockerfile](8/windows/nanoserver-ltsc2019/Dockerfile))
* `windowsservercore-1809`, `windowsservercore-ltsc2019`, `windowsservercore-1809-jdk11`, `windowsservercore-ltsc2019-jdk11`, `${IMAGE_VERSION}-windowsservercore-1809`, `${IMAGE_VERSION}-windowsservercore-ltsc2019`, `${IMAGE_VERSION}-windowsservercore-1809-jdk11`, `${IMAGE_VERSION}-windowsservercore-ltsc2019-jdk11` ([Dockerfile](11/windows/windowsservercore-ltsc2019/Dockerfile))
* `windowsservercore-1809-jdk8`, `windowsservercore-ltsc2019-jdk8`, `${IMAGE_VERSION}-windowsservercore-1809-jdk8`, `${IMAGE_VERSION}-windowsservercore-ltsc2019-jdk8` ([Dockerfile](8/windows/windowsservercore-ltsc2019/Dockerfile))

## Changelog

See [GitHub Releases](https://github.com/jenkinsci/docker-ssh-agent/releases/latest).
Note that the changelogs and release tags were introduced in Dec 2019, and there are no entries for previous releases.
Please consult with the commit history if needed.
