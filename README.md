# BlueAlsa
/etc/apt/sources.list like this:

deb http://ftp.de.debian.org/debian bullseye main non-free

https://forum.armbian.com/topic/6480-bluealsa-bluetooth-audio-using-alsa-not-pulseaudio/


cd && git clone https://github.com/Arkq/bluez-alsa.git
cd bluez-alsa
# git checkout tags/v1.3.1 -b v1.3.1
sudo apt install bluez bluez-tools libglib2.0-dev libasound2-dev build-essential autoconf libbluetooth-dev libtool libsbc-dev automake ibbsd-dev libfdk-aac-dev libncurses5-dev libreadline-dev automake libbsd-dev libfdk-aac-dev libncurses5-dev libreadline-dev -y

mkdir -p m4
autoreconf --install
mkdir build && cd build
# ../configure --enable-aac --enable-debug
#../configure --enable-debug
../configure CFLAGS="-g -O0" LDFLAGS="-g" --enable-debug
make && sudo make install

#https://forum.armbian.com/topic/6480-bluealsa-bluetooth-audio-using-alsa-not-pulseaudio/

Dependencies:
===================================================
apt-get install libasound2 libasound2-dev dh-autoreconf libortp-dev bluez bluez-tools libbluetooth-dev libusb-dev libglib2.0-dev libudev-dev libical-dev libreadline-dev libsbc1 libsbc-dev bluetooth libfdk-aac-dev libdbus-1-dev


Installation:
===================================================
git clone https://github.com/Arkq/bluez-alsa.git

cd bluez-alsa/

autoreconf --install

mkdir build && cd build

FOR Raspberry PI Zero use -  ../configure CFLAGS="-g -O0" LDFLAGS="-g" --enable-debug

../configure --enable-aac --enable-ofono
(without --enable-debug to get rid of the ebug messages while starting/conencting)
OR
For systems with aarch64-cpu-type (like the Allwinner H5 or A64 = 64bit):
-------------------------------------------------------------------------
../configure --enable-aac --enable-ofono --with-alsaplugindir=/usr/lib/aarch64-linux-gnu/alsa-lib
( /usr/lib/aarch64-linux-gnu/alsa-lib is generated while compiling )

make && make install


Starting BlueALSA 1st time:
===================================================
bluealsa --profile=a2dp-source --a2dp-force-audio-cd &

Pairing and trusting BT-device:
===================================================
bluetoothctl
scan on
[NEW] Device 00:11:67:3F:9B:18 BTLS9001
scan off
pair 00:11:67:3F:9B:18
trust 00:11:67:3F:9B:18
quit


REBOOT: (maybe)
===================================================
reboot


Starting BlueALSA 2nd time:
===================================================
export LIBASOUND_THREAD_SAFE=0
bluealsa --profile=a2dp-source --a2dp-force-audio-cd &


Connect truested speaker manually:
===================================================
echo -e "connect 00:11:67:3F:9B:18\nquit" | bluetoothctl


NEW syntax for aplay and mpg123 (without HCI and DEV):
===================================================
aplay -D bluealsa /home/guido/espeak.wav
mpg123 -v -a bluealsa -@ /home/guido/iradio/MP3_FFH80s

CREATING a BlueALSA /etc/asound.conf:
# =================================================
pcm.!default "bluealsa"
ctl.!default "bluealsa"
defaults.bluealsa.interface "hci0"
defaults.bluealsa.device "00:11:67:3F:9B:18"
defaults.bluealsa.profile "a2dp"
# =================================================

Info about bluealsa:
===================================================
amixer -D bluealsa

Set the volume in alsamixer:
===================================================
alsamixer -D bluealsa


aplay and mpg123 AFTER creating a BlueALSA /etc/asounf.conf:
===================================================
aplay /home/guido/espeak.wav
mpg123 -v -@ /home/guido/iradio/MP3_FFH80s


Disconnecting the trusted/connected BT-device:
===================================================
echo -e "disconnect 00:11:67:3F:9B:18\nquit" | bluetoothctl 

