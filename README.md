Linuxcnc on a modern linux platform without a shit-ton of pain, trying to include _everything_ necessary, but still a work in progress, feel free to email/submit pull reuqests
if you have additions or updates.


Based on this wiki, good to hear it works:
http://www.cnc-club.ru/wiki/index.php/%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0_LinuxCNC_%D0%BD%D0%B0_Ubuntu_18.0

install, had to install from CD, unetbootin didnt work:
http://releases.ubuntu.com/bionic/ubuntu-18.04.2-desktop-amd64.iso


Linux 4.13.13, reported to work per  original post
<code>
export LINUX_VER=4.13.13
export RT_VER=rt5
wget https://mirrors.edge.kernel.org/pub/linux/kernel/v4.x/linux-$LINUX_VER.tar.gz
wget https://mirrors.edge.kernel.org/pub/linux/kernel/projects/rt/4.13/patch-$LINUX_VER-$RT_VER.patch.gz
gzip -cd ~/patch-$LINUX_VER-$RT_VER.patch.gz > patch-$LINUX_VER.patch
</code>


Linux 4.18.16, closer to the ubuntu stock kernel version for this distribution
<code>
export LINUX_VER=4.18.16
export RT_VER=rt9
wget https://mirrors.edge.kernel.org/pub/linux/kernel/projects/rt/4.18/patch-$LINUX_VER-$RT_VER.patch.gz
wget https://mirrors.edge.kernel.org/pub/linux/kernel/v4.x/linux-$LINUX_VER.tar.gz
gzip -cd ~/patch-$LINUX_VER-$RT_VER.patch.gz > patch-$LINUX_VER.patch
</code>


dependencies
<code>
sudo apt-get update
Sudo apt-get upgrade
sudo apt-get install git devscripts build-essential imagemagick tcl8.6-dev tk8.6-dev libxaw7-dev libncurses-dev python-dev python-tk libglu1-mesa-dev libgtk2.0-dev source-highlight libboost-python-dev debhelper libmodbus-dev dvipng libusb-1.0-0-dev inkscape python-numpy python-imaging-tk python-gtkglext1 blt bwidget libtk-img tclx libudev-dev python-vte libqt4-dev gssproxy python-pil libxenomai-dev git python-vte libssl-dev yapps2  intltool libreadline-gplv2-dev bison flex
</code>


Patch 
<code>
cd /usr/src
Then unpack the archives in this directory, in the terminal we write:
sudo tar -xzvf ~/linux-$LINUX_VER.tar.gz
sudo ln -s linux-$LINUX_VER linux
cd linux-$LINUX_VER
sudo cp ~/patch-$LINUX_VER.patch .
sudo patch -p1 --verbose < patch-$LINUX_VER.patch
</code>

configure
<code>
sudo cp /boot/config-4.18.0-15-generic .config

sudo make oldconfig
make menuconfig

Processor type and features -> Preemption Model (Fully Preemptible Kernel (RT))
PREEMPT_RT_FULL=y

Power management and ACPI option -> [ ] Suspend to RAM 
SUSPEND=n

Power management and ACPI option -> [ ] Hibernation (aka 'suspend to disk')  
HIBERNATION=n

"Disable" CPU frequency scaling .. not sure 
CPU_FREQ_DEFAULT_GOV_PERFORMANCE=y
</code>

Then click save and exit.




Compile the kernel for this in the terminal:
<code>
sudo make -j4 
sudo make modules_install -j4
sudo make install -j4

sudo update-grub
</code>

Reboot the computer

<code>
cd ~
git clone https://github.com/LinuxCNC/linuxcnc.git linuxcnc-dev
cd linuxcnc-dev/src
./autogen.sh
./configure --with-realtime=uspace
make -j4
sudo make setuid
source linuxcnc-dev/scripts/rip-environment
</code>



sudo apt-get install libjpeg8-dev libtiff4-dev libjasper-dev libpng12-dev
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
sudo apt-get install libgtk2.0-dev
sudo apt-get install libatlas-base-dev gfortran

sudo apt-get install libatlas-base-dev gfortran

sudo apt-get install python-opencv
