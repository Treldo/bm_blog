---
layout: default
title: 同一客户端下使用多个 git 账号
---

# 同一客户端下使用多个 git 账号

---

### 清除git的全局设置

``` shell
git config --global --unset user.name
git config --global --unset user.email
```

### 生成新站好的SSH keys

**1. 用 ssh-keygen 命令生成一组新的 id_rsa_new 和 id_rsa_new.pub**

``` shell
ssh-keygen -t rsa -C "new email"
```

在生成第一组 **id_rsa** 和 **id_rsa.pub_** 可以选用默认的文件名，在出现提示输入文件名的时候要输入一个不同的文件名，**比如：**这里填的是 `id_rsa_new`

``` shell
Enter file in which to save the key (~/.ssh/id_rsa): id_rsa_new
```

**注：**windows用户和mac用户的区别就是，mac中 `.ssh` 文件夹在根目录下，所以表示成 `~/.ssh/`，而windwos用户是 `C:\Users\Administrator\.ssh`。

**2. 执行 ssh-agent 让 ssh 识别新的私钥**

因为默认只读取 `id_rsa`，为了让 SSH 识别新的私钥，需将其添加到 SSH agent 中：

``` shell
ssh-add ~/.ssh/id_rsa_new
```

如果出现 `Could not open a connection to your authentication agent` 的错误，就试着用以下命令：

``` shell
ssh-agent bash
ssh-add ~/.ssh/id_rsa_new
```

`ssh-add ~/.ssh/id_rsa_new` 只能将把指定的私钥添加到 `ssh-agent` 所管理的一个 session 当中。而 `ssh-agent` 是一个用于存储私钥的临时性的 session 服务，也就是说当你重启之后，ssh-agent 服务也就重置了，session 会话也就失效了。
解决办法就是在添加 ssh 私钥的时候使用如下命令：`ssh-add -K privateKey`，即可一劳永逸将私钥添加进 Mac 本身的钥匙串中，即 Keychain。

>参考博客：<http://www.icodeyou.com/2016/01/17/ssh-add-mac/>

**3. 配置 ~/.ssh/config 文件**

``` shell
# 该文件用于配置私钥对应的服务器
Host git@github.com    #别名，随便定 后面配置地址有用
    HostName https://github.com
    User git
    IdentityFile ~/.ssh/id_rsa    #密钥文件的地址，注意是私钥

# second user(second@mail.com)
Host git@code.xxxxxxx.com
    HostName https://code.xxxxxxx.com
    User git
    IdentityFile ~/.ssh/id_rsa_new
```

**4. 添加新的 SSH keys 到新账号的 SSH 设置中**

**测试一下**

``` shell
$ ssh -T git@github.com    # 此处是 Host 后面定义的别名
Hi xxx! You’ve successfully authenticated, but GitHub does not provide shell access.

# 上面是 github 的成功返回语句，下面是 gitlab 的成功返回语句。

$ ssh -T git@xxxxxx.com
Welcome to GitLab, xxx!  
```

链接：<https://www.jianshu.com/p/89cb26e5c3e8/>

来源：简书

## 参考资料

<https://blog.csdn.net/onTheRoadToMine/article/details/79029331/>

<https://gist.github.com/suziewong/4378434/>
