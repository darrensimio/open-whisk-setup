# Install Docker & Docker Compose on AWS Linux 2

## Installing Docker CE
Docker CE is the community package of The Docker Engine. Docker is a foundational technology which OpenWhisk is build upon, hence required for OpenWhisk to work.

```
sudo amazon-linux-extras install docker
sudo service docker start
sudo usermod -a -G docker ec2-user
```

If you have an older installation of Docker on the host, it is recommended for it to be uninstalled before performing the above steps.

```
sudo apt-get remove docker docker-engine docker.io containerd runc
```

## Setting up Docker

Configure docker to auto-start:
```
sudo chkconfig docker on
```

Installing the git client:
```
sudo yum install -y git
```

Reboot to verify it all loads fine on its own:
```
sudo reboot
```

## Installing Docker Compose

Install the docker-compose binary via GitHub:
```
sudo curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
```

Fix permissions after download:
```
sudo chmod +x /usr/local/bin/docker-compose
```

Verify installation is successful:
```
docker-compose version
```

## Installing NodeJS & NPM
NodeJS and NPM is required as the installation process of OpenWhisk concludes with some automated tests to verify the successful installation of OpenWhisk. These tests are written in NodeJS.

Add the NodeSource yum repository:
```
curl -sL https://rpm.nodesource.com/setup_10.x | sudo bash -
```

Install NodeJS & YUM:
```
sudo yum install nodejs
```

Verify NodeJS installation is successful:
```
node --version
```

Verify NPM installation is successful:
```
npm version
```

## Installing OpenWhisk

Downloading source file from Github:
```
git clone https://github.com/apache/incubator-openwhisk-devtools.git
```

Installation and setup:
```
cd incubator-openwhisk-devtools/docker-compose
make quick-start
```

This process can take up to 10 minutes, depending on the processing capability of the machine you are installing it on.

Get the `AUTH_KEY` used in the installation:
```
cat ./openwhisk-src/ansible/files/auth.guest`
```

## References

- https://gist.github.com/npearce/6f3c7826c7499587f00957fee62f8ee9
- https://linuxize.com/post/how-to-install-node-js-on-centos-7/
- https://gist.github.com/npearce/13d4d4c72bd4781b96a0a811ffbc454d