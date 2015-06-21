``
sudo apt-get install xclip
wget http://db.tt/JjlLYd5A -O urxvtclip.tgz
tar zxvf urxvtclip.tgz
sudo cp urxvtclip /usr/lib/urxvt/perl/clipboard
``

# modify .Xresource file like below
``
URxvt.keysym.Shift-Control-V: perl:clipboard:paste
URxvt.iso14755: False
URxvt.perl-ext-common: default,clipboard
``
