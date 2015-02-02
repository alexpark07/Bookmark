source list

emdebian (cross-compile)

```
deb http://emdebian.org/debian/ stable main
deb http://ftp.us.debian.org/debian/ stable main contrib non-free

wget http://ftp.kr.debian.org/debian/pool/main/g/gmp/libgmp3c2_4.3.2+dfsg-1_amd64.deb
sudo dpkg -i libgmp3c2_4.3.2+dfsg-1_amd64.deb

apt-get install linux-libc-dev-armel-cross -y
apt-get install libc6-armel-cross libc6-dev-armel-cross -y
apt-get install binutils-arm-linux-gnueabi -y 
apt-get install gcc-4.4-arm-linux-gnueabi -y 
apt-get install g++-4.4-arm-linux-gnueabi -y

```
