http://ubuntuforums.org/showthread.php?t=1409720

http://midactstech.blogspot.kr/2013/09/how-to-mount-windows-cifs-share-on_18.html

```
mount -t cifs //10.0.2.2/shared /home/alex/shared -o user,rw,uid=alex,gid=alex,credentials=/root/.cifs
```
