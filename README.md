Linuxcnc on a modern linux platform without a shit-ton of pain, trying to include _everything_ necessary, but still a work in progress, feel free to email/submit pull reuqests
if you have additions or updates.<br>
<br>
<br>
Based on this wiki, good to hear it works:<br>
<pre>
http://www.cnc-club.ru/wiki/index.php/%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0_LinuxCNC_%D0%BD%D0%B0_Ubuntu_18.0
</pre>
<br>
install, had to install from CD, unetbootin didnt work:<br>
<pre>
http://releases.ubuntu.com/bionic/ubuntu-18.04.2-desktop-amd64.iso
<pre>
<br>

Linux 4.18.16, closer to the ubuntu stock kernel version for this distribution<br>
<pre>
export LINUX_VER=4.18.16
export RT_VER=rt9
cd ~
wget https://mirrors.edge.kernel.org/pub/linux/kernel/projects/rt/4.18/patch-$LINUX_VER-$RT_VER.patch.gz
wget https://mirrors.edge.kernel.org/pub/linux/kernel/v4.x/linux-$LINUX_VER.tar.gz
gzip -cd ~/patch-$LINUX_VER-$RT_VER.patch.gz > patch-$LINUX_VER.patch
</pre>
<br>

dependencies<br>
<pre>
sudo apt-get update
Sudo apt-get upgrade
sudo apt-get install git devscripts build-essential imagemagick tcl8.6-dev tk8.6-dev libxaw7-dev libncurses-dev python-dev python-tk libglu1-mesa-dev libgtk2.0-dev source-highlight libboost-python-dev debhelper libmodbus-dev dvipng libusb-1.0-0-dev inkscape python-numpy python-imaging-tk python-gtkglext1 blt bwidget libtk-img tclx libudev-dev python-vte libqt4-dev gssproxy python-pil libxenomai-dev git python-vte libssl-dev yapps2  intltool libreadline-gplv2-dev bison flex  libelf-dev python-gi screen  kernel-package fakeroot libncurses5-dev ccache
</pre>

<br>
Patch <br>
<pre>
Then unpack the archives in this directory, in the terminal we write:
tar -xzvf ~/linux-$LINUX_VER.tar.gz
cd linux-$LINUX_VER
cp ~/patch-$LINUX_VER.patch .
patch -p1 --verbose < patch-$LINUX_VER.patch
</pre>
<br>

configure<br>
<pre>
cp /boot/config-`uname -r` .config
make oldconfig
make menuconfig

Processor type and features -> Preemption Model (Fully Preemptible Kernel (RT))
PREEMPT_RT_FULL=y

Power management and ACPI option -> [ ] Suspend to RAM 
SUSPEND=n

Power management and ACPI option -> [ ] Hibernation (aka 'suspend to disk')  
HIBERNATION=n

"Disable" CPU frequency scaling .. not sure 
CPU_FREQ_DEFAULT_GOV_PERFORMANCE=y
</pre>

Then click save and exit.<br>
<br>



Compile the kernel for this in the terminal:<br>
<pre>
fakeroot make-kpkg -j `getconf _NPROCESSORS_ONLN` --initrd --append-to-version=-linuxcnc kernel_image kernel_headers
</pre>

install compiled kerenl:
<pre>
cd /usr/src
sudo dpkg -i linux-image-$LINUX_VER-linuxcnc*.deb 
sudo dpkg -i linux-headers-$LINUX_VER-linuxcnc*.deb 
</pre>

<br>
Reboot the computer<br>
<pre>
cd ~
git clone https://github.com/LinuxCNC/linuxcnc.git linuxcnc-dev
cd linuxcnc-dev/src
./autogen.sh
./configure --with-realtime=uspace
make -j`getconf _NPROCESSORS_ONLN`
sudo make setuid
source linuxcnc-dev/scripts/rip-environment
</pre>

configure linux CNC using stepconf and place linuxcnc icon on desktop<br>
you should now have a modern realtime linux suitable for use with your linuxcnc machine which shoulf be maintainable using ubuntu's
current package repos, I dont know why linuxcnc doesnt provide live images for this<br>


<br>
opencv for vision systems, you probably dont need this
<pre>
sudo apt-get install libjpeg8-dev libtiff4-dev libjasper-dev libpng12-dev
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
sudo apt-get install libgtk2.0-dev
sudo apt-get install libatlas-base-dev gfortran
sudo apt-get install python-opencv
</pre>
