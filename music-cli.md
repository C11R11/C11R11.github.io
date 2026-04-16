# Standardize the Files (The FFmpeg Trick)

```sh 
brew install ffmpeg

for f in *.mp3; do ffmpeg -i "$f" -codec:a libmp3lame -b:a 256k -ar 44100 "CD_${f}"; done
```

# Get a playlist 

> regist to it

https://www.chosic.com/spotify-playlist-exporter/