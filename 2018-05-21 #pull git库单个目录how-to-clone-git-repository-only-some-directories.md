
> 有时git库里的东西比较多，我们只希望像SVN一样，只拉取git库的一个目录。
例如：基础代码仓库dw_etl有很多基础代码，我们只想拉取仓库里fct_ordr_path_off目录的文件。

```
mkdir fct_ordr_path_off.git
git init fct_ordr_path_off.git && cd  fct_ordr_path_off.git
git remote add -f origin https://gitlab.abcplus.org/bi-source/dw_etl.git

#### 开启sparse checkout功能
git config core.sparsecheckout true

### 将nginx-conf/目录写入到该文件中
echo "fct_ordr_path_off/" >> .git/info/sparse-checkout

### 确认查看该文件内容
cat .git/info/sparse-checkout

### 拉取远程master分支
git pull origin master

```

### 参考
https://askubuntu.com/questions/460885/how-to-clone-git-repository-only-some-directories