# Building Android for Google Pixel on Ubuntu 20.04.2 LTS

## Setting up a Linux build environment

### Installing required packages

```bash
$ sudo apt install git-core gnupg flex bison build-essential zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 libncurses5-dev lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z1-dev libgl1-mesa-dev libxml2-utils xsltproc unzip fontconfig
```

## Installing Repo

### Ensure that you have a `bin/` directory in your home directory and that it's included in your path:

```bash
$ mkdir -p ~/bin
$ PATH="${HOME}/.bin:${PATH}"
```

### Download the Repo Launcher and ensure that it's executable:

```bash
$ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
$ chmod a+x ~/bin/repo
```

### Optionally, verify the launcher matches the Google signatures:

```bash
$ gpg --recv-key 8BB9AD793E8E6153AF0F9A4416530D5E920F5C65
$ curl https://storage.googleapis.com/git-repo-downloads/repo.asc | gpg --verify - ~/bin/repo
```

## Initializing a Repo client

### Create an empty directory to hold your working files:

```bash
$ mkdir android
$ cd android
```

### Configure Git with your real name and email address:

```bash
$ git config --global user.name "Tecatech"
$ git config --global user.email "hexdump1337@gmail.com"
```

### Run `repo init` to get the latest version of Repo with its most recent bug fixes:

```bash
$ repo init -u https://android.googlesource.com/platform/manifest
```

### To check out the `Pixel` branch:

```bash
$ repo init -u https://android.googlesource.com/platform/manifest -b android-10.0.0_r17
```

### If you get a "`/usr/bin/env 'python' no such file or directory`" error message, use the following solution:

```bash
$ sudo ln -s /usr/bin/python3 /usr/bin/python
```

## Downloading kernel sources and build tools

### Download the sources for the `sailfish` branch:

```bash
$ repo init -u https://android.googlesource.com/kernel/manifest -b android-msm-marlin-3.18-pie-qpr2
$ repo sync -c -j$(nproc)
```

## Installing the `Pixel` driver binaries

### Download the `Pixel` driver binaries:

```bash
$ wget https://dl.google.com/dl/android/aosp/google_devices-sailfish-qp1a.191005.007.a3-a1615a0f.tgz
$ wget https://dl.google.com/dl/android/aosp/qcom-sailfish-qp1a.191005.007.a3-191228fe.tgz
```

### Extract the `Pixel` driver binaries:

```bash
$ tar xvf google_devices-sailfish-opm4.171019.016.b1-839e6b26.tgz
$ tar xvf qcom-sailfish-opm4.171019.016.b1-3c7f92b3.tgz
```

### Execute the launch scripts:

```bash
$ ./extract-google_devices-sailfish.sh
$ ./extract-qcom-sailfish.sh
```

## Setting up the environment

### Initialize the environment with the `envsetup.sh` script:

```bash
$ source build/envsetup.sh
```

## Choosing a target

### Choose the `sailfish` target to build:

```bash
$ lunch aosp_sailfish-userdebug
```

## Building the code

### Build everything with `m`:

```bash
$ m
```