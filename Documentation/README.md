# Building Android for Google Pixel on Ubuntu 20.04.2 LTS

## Establishing a Build Environment

### Setting up a Linux build environment

#### Installing required packages

```bash
$ sudo apt install git-core gnupg flex bison build-essential zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 libncurses5-dev lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z1-dev libgl1-mesa-dev libxml2-utils xsltproc unzip fontconfig
```

## Source Control Tools

### Installing Repo

#### Ensure that you have a `bin/` directory in your home directory and that it's included in your path:

```bash
$ mkdir -p ~/bin
$ PATH="${HOME}/bin:${PATH}"
```

#### Download the Repo Launcher and ensure that it's executable:

```bash
$ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
$ chmod a+x ~/bin/repo
```

#### Optionally, verify the launcher matches the Google signatures:

```bash
$ gpg --recv-key 8BB9AD793E8E6153AF0F9A4416530D5E920F5C65
$ curl https://storage.googleapis.com/git-repo-downloads/repo.asc | gpg --verify - ~/bin/repo
```

## Downloading the Source

### Initializing a Repo client

#### Create an empty directory to hold your working files:

```bash
$ mkdir platform
$ cd platform
```

#### Configure Git with your real name and email address:

```bash
$ git config --global user.name "Tecatech"
$ git config --global user.email "hexdump1337@gmail.com"
```

#### Run `repo init` to get the latest version of Repo with its most recent bug fixes:

```bash
$ repo init -u https://android.googlesource.com/platform/manifest
```

#### To check out the `sailfish` branch:

```bash
$ repo init -u https://android.googlesource.com/platform/manifest -b android-10.0.0_r17
```

#### If you get a "`/usr/bin/env 'python' no such file or directory`" error message, use the following solution:

```bash
$ sudo ln -s /usr/bin/python3 /usr/bin/python
```

### Downloading the Android source tree

#### To download the Android source tree to your working directory, run:

```bash
$ repo sync
```

#### To speed syncs, pass the `-c` (current branch) and `-jthreadcount` flags:

```bash
$ repo sync -c -j$(nproc)
```

### Obtain proprietary binaries

#### Download proprietary binaries

```bash
$ wget https://dl.google.com/dl/android/aosp/google_devices-sailfish-qp1a.191005.007.a3-a1615a0f.tgz
$ wget https://dl.google.com/dl/android/aosp/qcom-sailfish-qp1a.191005.007.a3-191228fe.tgz
```

#### Extract proprietary binaries

```bash
$ tar xvf google_devices-sailfish-qp1a.191005.007.a3-a1615a0f.tgz
$ tar xvf qcom-sailfish-qp1a.191005.007.a3-191228fe.tgz
```

```bash
$ ./extract-google_devices-sailfish.sh
$ ./extract-qcom-sailfish.sh
```

#### Clean up

```bash
$ make clobber
```

## Building Kernels

### Downloading sources and build tools

#### Download the sources for the `marlin` branch:

```bash
$ mkdir kernel && cd kernel
```

```bash
$ repo init -u https://android.googlesource.com/kernel/manifest -b android-msm-marlin-3.18-pie-qpr2
```

```bash
$ repo sync -c -j$(nproc)
```

### Building the kernel

#### Then build the kernel with this:

```bash
$ build/build.sh
```

#### If you get a "`python: can't open file 'build/buildinfo/buildinfo.py': [Errno 2] No such file or directory`" error message, use the following solution:

```bash
$ vim build.config
```

##### The adjustments to be made can be summarized as follows through a `git diff` result:

```patch
diff --git a/build.config b/build.config
index 31533fb9..81caf05d 100644
--- a/build.config
+++ b/build.config
@@ -1,5 +1,4 @@

 KERNEL_DIR=private/msm-google
 . ${ROOT_DIR}/${KERNEL_DIR}/build.config.common
 POST_DEFCONFIG_CMDS="check_defconfig && compression_tool_and_files lz4"
-EXTRA_CMDS='python build/buildinfo/buildinfo.py'
 STOP_SHIP_TRACEPRINTK=1
```

## Building Android

### Setting up the environment

#### After you have built the kernel image, copy the result over into AOSP's `device/google/marlin-kernel` directory, with:

```bash
cp ${kernel}/out/android-msm-marlin-3.18/dist/Image.lz4-dtb device/google/marlin-kernel
```

#### Initialize the environment with the `envsetup.sh` script:

```bash
$ source build/envsetup.sh
```

### Choosing a target

#### Choose the `sailfish` target to build with `lunch`:

```bash
$ lunch aosp_sailfish-userdebug
```

### Building the code

#### Build everything with `m`:

```bash
$ m
```
