Sublime Text 提供丰富的插件，但是需要安装Package Control 后才能安装其他插件。官网下载的Sublime 默认是没有安装Package Control的。The simplest method of installation is through the Sublime Text console -- 最便捷的方式是通过Sublime 的控制台来安装。 

## 打开 Sublime Text 3 console
可以通过快捷键 **ctrl+\`** 或者` View > Show Console menu` 打开。如下图：

![这里写图片描述](http://img.blog.csdn.net/20170117155427229?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbXVsYW5ncmVuMTk4OA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170117155536807?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbXVsYW5ncmVuMTk4OA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 执行命令
```
import urllib.request,os,hashlib; h = 'df21e130d211cfc94d9b0905775a7c0f' + '1e3d39e33b79698005270310898eea76'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
```
输入上面的命令，按回车键就会执行，关掉 sublime 重新打开即可生效。

## 如果是Sublime Text 2，执行以下命令
```
import urllib2,os,hashlib; h = 'df21e130d211cfc94d9b0905775a7c0f' + '1e3d39e33b79698005270310898eea76'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); os.makedirs( ipp ) if not os.path.exists(ipp) else None; urllib2.install_opener( urllib2.build_opener( urllib2.ProxyHandler()) ); by = urllib2.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); open( os.path.join( ipp, pf), 'wb' ).write(by) if dh == h else None; print('Error validating download (got %s instead of %s), please try manual install' % (dh, h) if dh != h else 'Please restart Sublime Text to finish installation')
```
输入上面的命令，按回车键就会执行，关掉 sublime 重新打开即可生效。

## 安装其他插件
### 快捷键 ctrl + shift + p，输入install
![这里写图片描述](http://img.blog.csdn.net/20170117160008555?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbXVsYW5ncmVuMTk4OA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 选择Install Package，等待加载插件仓库

![这里写图片描述](http://img.blog.csdn.net/20170117160202763?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbXVsYW5ncmVuMTk4OA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

比如我输入python，就会列出与python相关的插件。
接下来就可以愉快的安装插件了。