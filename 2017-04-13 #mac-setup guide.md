```
http://sourabhbajaj.com/mac-setup/iTerm/README.html


##### Mac OS X：在标题栏上显示目录完整路径
defaults write com.apple.finder _FXShowPosixPathInTitle -bool YES

##### 显示影藏的文件
defaults write com.apple.finder AppleShowAllFiles -bool true

- [x] killall Finder

我们再打开Finder浏览的时候，会发现完整路径已经出现在标题栏上了
```