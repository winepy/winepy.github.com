### 1:安装git工具

### 2.查看git是否已安装

```
git --version
```

### 3.配置git信息

```
git config --glabal user.name "winepy"
git config --global user.email "XXXXXXXX@163.com"

// 查看配置信息
git config --list
```

### 4. 生成公钥

```
ssh-keygen -r rsa -C "XXXXXXXXXX@163.com"

cd ~/.ssh

// 查看并复制公钥内容
vim id_rsa.pub
```

### 5.打开github，设置ssh

访问github、打开Settings中的SSH and GPG keys
新建SSH key；
自定义title后，再将第四步中的公钥粘贴到key中


### 6.验证

```
ssh -T git@github.com
验证是否配置成功
成功时会有下面输出：Hi XXXXX! You've successfully authenticated
否则：失败
```
