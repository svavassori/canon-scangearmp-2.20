# Canon ScanGear MP 2.20-1 source code and build instructions

## Original Canon ScanGear MP sources
The original Canon ScanGear MP sources have been downloaded from:
https://gdlp01.c-wss.com/gds/2/0100005532/01/scangearmp-source-2.20-1.tar.gz
and it contains the code for the following Canon PIXMA model series:

* MG2400
* MG2500
* MG3500
* MG5500
* MG6400
* MG6500
* MG7100
* P200

```text
Name: scangearmp-source-2.20-1.tar.gz
Size: 4622763 bytes
Last modified date: 17 lug 2013
Hashes:
   MD5: 8e8b2fed2baefde15fbd5314db0027f0  scangearmp-source-2.20-1.tar.gz
  SHA1: 0da09d4142e6515302c9d57531980bbb55b3aae4  scangearmp-source-2.20-1.tar.gz
SHA256: a5741c9f7219f02b68b53cd295d695a7782e070f7e681634f06cd15eefae2a6c  scangearmp-source-2.20-1.tar.gz
SHA512: 5ce44c83831be99d19f7d9c0323dcb5534e88cbb9855a2361ffd85815389532a052fcc165820610081bbffd78898cc35df700cbdb5a0104af96c5e3009c1782c  scangearmp-source-2.20-1.tar.gz
```

## How to build the binary package

```bash
docker build -t canon-build-env:12.15 .
docker run --rm -v $(pwd):/build -it -u $(id -u):$(id -g) canon-build-env:12.15
cd /build/scangearmp-source-2.20-1
dpkg-buildpackage --build=binary --unsigned-changes --unsigned-source
```

## Run from a docker container

```bash
# to show magic cookie
xauth list
docker run --rm -it --privileged -v $(pwd):/debs -v /dev/bus/usb:/dev/bus/usb -v /tmp/.X11-unix/:/tmp.X11-unix -e DISPLAY=$DISPLAY --net=host debian:12.15 bash
cd /debs
apt-get update
apt-get install --yes xauth ./scangearmp-common_2.20-1_amd64.deb ./scangearmp-mg2500series_2.20-1_amd64.deb
xauth add <magic-cookie>
scangearmp
```

## Variables to set in ~/.quiltrc to work with quilt patches

This .quiltrc is already set in the Dockerfile in `/tmp/.quiltrc`,
it is not needed if you just build the package.

```bash
cat <<EOF >> ~/.quiltrc
QUILT_PATCHES=debian/patches
QUILT_SERIES=debian/patches/series
QUILT_DIFF_ARGS="--no-timestamps --no-index -pab"
QUILT_REFRESH_ARGS="--no-timestamps --no-index -pab"
QUILT_PATCH_OPTS="--reject-format=unified"
EOF
```

