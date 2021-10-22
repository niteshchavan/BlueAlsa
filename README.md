# After completing below Bluetooth install. Configure kodi to use bluetooth headphones
In kodi settings
set the audio to ALSA default beforehand in setting if you are not getting results

Then open ./kodi/userdata/guisettings.xml

<audiooutput>

      ...
      
    <audiodevice>ALSA:bluealsa</audiodevice>
      
      ...
      
This change will be overwritten if you open the settings so be careful.

# Bluetooth on Raspberry Pi Zero

source https://forum.armbian.com/topic/6480-bluealsa-bluetooth-audio-using-alsa-not-pulseaudio/

#Pre-Requsites
=============================================
apt-get install libasound2 libasound2-dev dh-autoreconf libortp-dev bluez bluez-tools libbluetooth-dev libusb-dev libglib2.0-dev libudev-dev libical-dev libreadline-dev libsbc1 libsbc-dev bluetooth libdbus-1-dev

wget http://ftp.us.debian.org/debian/pool/non-free/f/fdk-aac/libfdk-aac1_0.1.6-1_armhf.deb

wget http://ftp.us.debian.org/debian/pool/non-free/f/fdk-aac/libfdk-aac-dev_0.1.6-1_armhf.deb

dpkg -i libfdk-aac1_0.1.6-1_armhf.deb

dpkg -i libfdk-aac-dev_0.1.6-1_armhf.deb

#Installation:
=============================================
git clone https://github.com/Arkq/bluez-alsa.git

cd bluez-alsa/

mkdir -p m4

autoreconf --install

mkdir build && cd build

../configure CFLAGS="-g -O0" LDFLAGS="-g"

make && make install

#Starting BlueALSA 1st time:
=============================================
bluealsa --profile=a2dp-source --a2dp-force-audio-cd &

#Pairing and trusting BT-device:
=============================================
bluetoothctl

scan on

[NEW] Device 00:11:67:3F:9B:18 BTLS9001

scan off

pair 00:11:67:3F:9B:18

trust 00:11:67:3F:9B:18

quit


#Try if u get illegal error
=============================================
../configure --enable-aac --enable-ofono
(without --enable-debug to get rid of the ebug messages while starting/conencting)
OR
For systems with aarch64-cpu-type (like the Allwinner H5 or A64 = 64bit):
-------------------------------------------------------------------------
../configure --enable-aac --enable-ofono --with-alsaplugindir=/usr/lib/aarch64-linux-gnu/alsa-lib
( /usr/lib/aarch64-linux-gnu/alsa-lib is generated while compiling )


#Starting BlueALSA 2nd time:
=============================================
export LIBASOUND_THREAD_SAFE=0
bluealsa --profile=a2dp-source --a2dp-force-audio-cd &


#Connect truested speaker manually:
=============================================
echo -e "connect 00:11:67:3F:9B:18\nquit" | bluetoothctl


#NEW syntax for aplay and mpg123 (without HCI and DEV):
============================================
aplay -D bluealsa /home/guido/espeak.wav
mpg123 -v -a bluealsa -@ /home/guido/iradio/MP3_FFH80s

CREATING a BlueALSA /etc/asound.conf:
# ===========================================

pcm.!default "bluealsa"

ctl.!default "bluealsa"

defaults.bluealsa.interface "hci0"

defaults.bluealsa.device "00:11:67:3F:9B:18"

defaults.bluealsa.profile "a2dp"

# ===========================================

Info about bluealsa:
=============================================
amixer -D bluealsa

Set the volume in alsamixer:
=============================================
alsamixer -D bluealsa


aplay and mpg123 AFTER creating a BlueALSA /etc/asounf.conf:
=============================================
aplay /home/guido/espeak.wav
mpg123 -v -@ /home/guido/iradio/MP3_FFH80s


Disconnecting the trusted/connected BT-device:
=============================================
echo -e "disconnect 00:11:67:3F:9B:18\nquit" | bluetoothctl 
