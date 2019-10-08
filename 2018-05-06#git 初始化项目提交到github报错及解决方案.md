## 正常流程
### Git global setup

```
git config --global user.name "vk"
git config --global user.email "vk@abcplus.com"
```

### Create a new repository
```
git clone git@gitlab.abcplus.org:vk/HiveUDF.git
cd HiveUDF
touch README.md
git add README.md
git commit -m "add README"
git push -u origin master
```

### Existing folder or Git repository
```
cd existing_folder
git init
git remote add origin git@gitlab.abcplus.org:vk/HiveUDF.git
git add .
git commit
git push -u origin master
```
## 异常流程
在github上创建了一个项目，但是pull的时候提示：

> $ git pull origin master
>
>  From github.com:vickyi/xcx_chihuo
> * branch            master     -> FETCH_HEAD
> * [new branch]      master     -> origin/master
>
> fatal: refusing to merge unrelated histories

解决办法：
```
$ git pull origin master --allow-unrelated-histories
From github.com:vickyi/xcx_chihuo
 * branch            master     -> FETCH_HEAD
Merge made by the 'recursive' strategy.
 LICENSE | 674 ++++++++++++++++++++++++++++++++++++++++++++++++++
 1 file changed, 674 insertions(+)
 create mode 100644 LICENSE

```