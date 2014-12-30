Link - http://uberskill.blogspot.kr/2012/08/cracking-android-gesture-patterns.html

```
$ wget 'http://www.android-forensics.com/tools/AndroidGestureSHA1.rar'
$ unrar AndroidGestureSHA1.rar
$ grep -i `xxd -p gesture.key` AndroidGestureSHA1.rar
56742391;04 05 06 03 01 02 08 00;4895B0FDC65F7802D165140BF1A77B982BD98779
```
