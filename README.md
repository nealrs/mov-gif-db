**Turn Quicktime movies into animated gifs & automagically upload them to Dropbox!**

**Inspired by:** this [gist](https://gist.github.com/dergachev/4627207) from [Alex Dergachev](https://github.com/dergachev/)

**Dependencies:** ffmpeg, X-Quartz, gifsicle (available via homebrew) and [Dropbox-Uploader](https://github.com/andreafabrizi/Dropbox-Uploader)

After you've squared away the dependencies:

1. Add the code below to your `.bash_profile` & alias Dropbox Uploader as `dbu`
2. Run `dbu` & follow the setup procedure to create a new app & API keys.
3. Try converting & upload a movie `$ movgif input.mov [max-width]`
4. Have fun!

```shell
movgif(){

  # check for input file
  if [ -z "$1" ]
    then
      echo "$(tput setaf 1)$(tput setab 7)PROPER USAGE: $ movgif input.mov [max width in pixels]$(tput sgr 0)"
      kill -INT $$
  fi
  
  # check for & set maxsize
  if [ ! -z "$2" ]
    then
      maxsize="-vf scale=$2:-1"
    else
      maxsize=""
  fi    
  
  # set output & run conversion
  out="$1_$(LC_CTYPE=C tr -cd '[:alnum:]' < /dev/urandom | fold -w8 | head -n1).gif"    
  ffmpeg -i $1 $maxsize -pix_fmt rgb24 -r 10 -f gif - | gifsicle --optimize=3 --delay=3 > $out
  echo "$(tput setaf 2)output file: $out$(tput sgr 0)"
  
  # upload to dropbox & get sharing link
  dbu upload "$out" /
  dbu share "$out"
  
}
```
