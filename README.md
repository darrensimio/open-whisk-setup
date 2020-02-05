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
This key is required to configure the OpenWhisk CLI to communicate with your OpenWhisk installation.

## Setup OpenWhisk CLI

### WSK

The CLI took for OpenWhisk is [wsk](https://github.com/apache/openwhisk/blob/master/docs/cli.md#openwhisk-cli). It supports Linux, Mac, and Windows.

Install `wsk` on MacOS:
```
brew update
brew install wsk
```

For installations on other OS, please visit [https://openwhisk.apache.org/documentation.html#wsk-cli](https://openwhisk.apache.org/documentation.html).

### WSKDeploy

The command line tool for deploying to OpenWhisk is [wskdeploy](https://github.com/apache/openwhisk-wskdeploy/blob/master/README.md).

Install `wskdeploy` on MacOS:
```
brew update
brew install wskdeploy
```

For installations on other OS, please visit [https://openwhisk.apache.org/documentation.html#wsk-cli](https://openwhisk.apache.org/documentation.html).

## Configuring `wsk` to connect to your OpenWhisk Installation
In order to configure WSK, you will need the following information handly:

- IP address of your server
- AUTH_KEY (instructions on the `Installing OpenWhisk` section)

Example usage:
```
wsk -i property set --apihost API_HOST --auth AUTH_KEY
```

## Creating a Function
The TWO most important files when creating a function in OpenWhisk are:

- manifest.yml
- source code of the function which can be on one of the supported languages

## Deploying a Function

The `wskdeploy` cli tool uses the settings configured in the `wsk` CLI tool to deploy to the OpenWhisk installation.

Using the `--strict` flag will override the OpenWhisk runtime mapping, and strictly follow what is specified in the menifest file.

Example:
```
wskdeploy -m manifest.yml --strict
```

After deploying, it is important to test the function. This can be done via the following command:

```
wsk -i action invoke /guest/hello_world_package/hello_world --result --param name Darren --param place "Bedok, Singapore"
```

This will result in the following output:

```
{
    "greeting": "Hello, Darren from Bedok, Singapore"
}
```

To increase the verbosity of the output, the `-v` flag can be used.

Example:
```
wsk -i action invoke /guest/hello_world_package/hello_world --result --param name Darren --param place "Bedok, Singapore" -v
```

Results:
```
REQUEST:
[POST]	https://0.0.0.0/api/v1/namespaces/guest/actions/hello_world_package/hello_world?blocking=true&result=true
Req Headers
{
  "Authorization": [
    "Basic XXX"
  ],
  "Content-Type": [
    "application/json"
  ],
  "User-Agent": [
    "OpenWhisk-CLI/1.0 (not set) darwin amd64"
  ]
}
Req Body
{"name":"Darren","place":"Bedok, Singapore"}

RESPONSE:Got response with code 200
Resp Headers
{
  "Access-Control-Allow-Headers": [
    "Authorization, Origin, X-Requested-With, Content-Type, Accept, User-Agent"
  ],
  "Access-Control-Allow-Methods": [
    "GET, DELETE, POST, PUT, HEAD"
  ],
  "Access-Control-Allow-Origin": [
    "*"
  ],
  "Connection": [
    "keep-alive"
  ],
  "Content-Length": [
    "50"
  ],
  "Content-Type": [
    "application/json"
  ],
  "Date": [
    "Wed, 05 Feb 2020 06:51:09 GMT"
  ],
  "Server": [
    "openresty/1.13.6.2"
  ],
  "X-Openwhisk-Activation-Id": [
    "XXX"
  ],
  "X-Request-Id": [
    "XXX"
  ]
}
Response body size is 50 bytes
Response body received:
{"greeting":"Hello, Darren from Bedok, Singapore"}
{
    "greeting": "Hello, Darren from Bedok, Singapore"
}
```

This is extremely useful during development time.

## References

- https://gist.github.com/npearce/6f3c7826c7499587f00957fee62f8ee9
- https://linuxize.com/post/how-to-install-node-js-on-centos-7/
- https://gist.github.com/npearce/13d4d4c72bd4781b96a0a811ffbc454d