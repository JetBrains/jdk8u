#How JetBrains Runtime is organised
##Workspaces

[github.com/JetBrains/jdk8u](https://github.com/JetBrains/jdk8u/)  
[github.com/JetBrains/jdk8u_corba](https://github.com/JetBrains/jdk8u_corba/)  
[github.com/JetBrains/jdk8u_hotspot](https://github.com/JetBrains/jdk8u_hotspot/)  
[github.com/JetBrains/jdk8u_jaxp](https://github.com/JetBrains/jdk8u_jaxp/)  
[github.com/JetBrains/jdk8u_jaxws](https://github.com/JetBrains/jdk8u_jaxws/)  
[github.com/JetBrains/jdk8u_jdk](https://github.com/JetBrains/jdk8u_jdk/)  
[github.com/JetBrains/jdk8u_langtools](https://github.com/JetBrains/jdk8u_langtools/)  
[github.com/JetBrains/jdk8u_nashorn](https://github.com/JetBrains/jdk8u_nashorn/)  
    
Usually, we do not modify any of the repositories except [jdk]((https://github.com/JetBrains/jdk8u_jdk/)) module. Other repositories are needed for build purposes.
##Getting sources
To get sources you need:
```
git clone git@github.com:JetBrains/jdk8u.git
cd jdk8u
./getModules.sh
```
or `getModules.bat` on Windows

##Linux-x64
We are using Docker image of CentOS 6.7 to build 64 bit linux images. Current docker image submitted to the docker hub is alexatdocker/centos-ojdkenv:latestv9
Here is Dockerfile  for creating  current build environment. You need to put it into the separate folder along with the following opt.tgz archive unpacked. Then use the following commands to update the image:
```
docker build .
docker images # To see the created image (c6d85cd2d3c5)
docker tag c6d85cd2d3c5 alexatdocker/centos-ojdkenv:latestv5
docker push alexatdocker/centos-ojdkenv
```
##Linux-x32
There is no official Docker image of 32bit CentOS 6.7. So, we're creating it by ourselves:
```
export BASEURL="http://mirror.centos.org/centos-6/6/os/i386/Packages/"
sudo mkdir /tmp/sysroot
sudo wget "${BASEURL}/centos-release-6-7.el6.centos.12.3.i686.rpm"
sudo rpm --root /tmp/sysroot --rebuilddb
sudo rpm --root /tmp/sysroot -i centos-release-6-7.el6.centos.12.3.i686.rpm
sudo yum --nogpgcheck --installroot=/tmp/sysroot groupinstall base
sudo sed -e 's/$releasever/6/g' -i /tmp/sysroot/etc/yum.repos.d/CentOS-Base.repo
sudo tar -czf /tmp/sysroot.tgz -C /tmp/sysroot/ .
cat /tmp/sysroot.tgz | docker import - centos32
docker run -i -t centos32 linux32 /bin/bash
docker images # To see the created image (c9ed933f7021)
docker tag c9ed933f7021 alexatdocker/centos32:base
docker push  alexatdocker/centos32
```
Then we use the same procedure as we had for x64 to create build environment using modified Dockerfile
```
docker build .
docker images # To see the created image (e72fd4701c0d)
docker tag e72fd4701c0d alexatdocker/centos32:jbr_build_env0
docker push alexatdocker/centos32
```

#Configure Local Build Environment
##Linux
```
sudo apt-get install git zip bzip2 unzip tar curl
sudo apt-get install ccache make gcc g++ ca-certificates ca-certificates-java
sudo apt-get install libxext-dev libxrender-dev libxtst-dev libxt-dev
sudo apt-get install libasound2-dev libcups2-dev libfreetype6-dev
git clone git@github.com:JetBrains/jdk8u.git
cd jdk8u
./getModules.sh
download jdk8 from Oracle into /home/user/jdk1.8.0_102
export JAVA_HOME=/home/user/jdk1.8.0_102
sh ./configure
make
```