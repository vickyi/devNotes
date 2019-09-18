# git设置全局和项目级username和email

## 全局

一般在职的员工，公司的git项目必须使用公司邮箱和用户名，可以设置全局的属性。

```shell
git config --global user.name "username"
git config --global user.email "email"
```

## 针对某个项目

比如公司的项目必须使用公司邮箱，但是自己的项目想使用不同的用户名和邮箱，可以采用此种方式。

```shell
git config user.name "dolphin"
git config user.email "email@example.com"
```
