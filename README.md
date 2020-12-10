# Unofficial Guide

## A Noobs guide to install ros melodic on singularity

* As of Dec 10th,2020 Singularity is not supported on Windows
* These guidlines are tested for Ubuntu 18.04 and ahead

### Installing GO

* You coud just install go from the official guide but since you are here, you need it from here :smirk:

Install the dependencies using apt
```bash
sudo apt-get update && sudo apt-get install -y build-essential libseccomp-dev pkg-config squashfs-tools cryptsetup
```
* Visit [Go downloads](https://golang.org/dl/)
* Copy the latest stable release link for linux , until todat 15.6 is the latest one
```bash
wget https://golang.org/dl/go1.15.6.linux-amd64.tar.gz
```
* The /usr/local hierarchy is for use by the system administrator when installing software locally.
* Locally installed software must be placed within /usr/local rather than /usr unless it is being installed to replace or upgrade software in /usr. [Ref](https://unix.stackexchange.com/questions/4186/what-is-usr-local-bin)
* Thus extract it in /usr/local using [tar](https://www.cyberciti.biz/faq/linux-unix-extracting-specific-files/)

```bash
sudo tar -C /usr/local -xzf go1.15.6.linux-amd64.tar.gz
```

* Now we need to set the environment variables for go
* I set them directly in .bashrc but you can do it in [alternative](https://unix.stackexchange.com/questions/3052/is-there-a-bashrc-equivalent-file-read-by-all-shells)

```bash
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc
mkdir ~/go_projects
echo 'export GOPATH="$HOME/go_projects"' >> ~/.bashrc
echo 'export GOBIN="$GOPATH/bin"' >> ~/.bashrc
```
* Now resource
```bash
source ./bashrc
```
* Now check the installation
```bash
go version
go env
```
* Output would be ```go version go1.15.6 linux/amd64``` and 
```
GOBIN="/home/satacker/go_projects/bin"
GOCACHE="/home/satacker/.cache/go-build"
GOENV="/home/satacker/.config/go/env"
...{and so on}
```

### Installing Singularity

* It is optional to install golangci-lint as per the [singularity installation](https://github.com/hpcng/singularity/blob/master/INSTALL.md) 

```bash
curl -sfL https://install.goreleaser.com/github.com/golangci/golangci-lint.sh | sh -s -- -b $(go env GOPATH)/bin v1.21.0
```

* Now installing Singularity

```bash
mkdir -p $(go env GOPATH)/src/github.com/sylabs
cd $(go env GOPATH)/src/github.com/sylabs
git clone https://github.com/sylabs/singularity.git
cd singularity
```

* As of today v3.6.4 is a stable one
* Now inside `singularity`
```bash
git checkout v3.6.4
./mconfig
cd ./builddir
make
sudo make install
```
* To verify the installation
```bash
singularity version
```

### [Installing ros melodic from osrf image](https://robotism.me/blog/creating-ROS-melodic-container-with-singularity-3.5/)
```bash
cd ~ && sudo singularity build --sandbox melodic/ docker://osrf/ros:melodic-desktop-full
```
* Now the container image is ready
* To execute scripts in the container
```bash
sudo singularity shell --writable melodic/
```
* This will open a Shell in Singurity image with the current directory mounted by default 
* Try `apt update`

### Running roscore
* To run roscore source the shell functions from ros using `/ros_entrypoint.sh roscore`
* Try running gazebo simply by entering `gazebo` , hopefully a gui will pop up, if not see known issues

## Known issues
* While running the gui apps the windowing system may give `No protocol specified` and `xcb_connection_has_error() returned true`
The possible workaround (also the easiest) for this is to allow the anyone on the localhost to connect to your dislpay using `xhost +local:` - [ref](https://unix.stackexchange.com/questions/85782/error-no-protocol-specified-when-running-from-remote-machine-via-ssh)
