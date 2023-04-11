# Git使用(一)：Git、Gerrit多帐户配置

**1. 生成github.com对应的私钥公钥（本文中文件地址C:\Users\yechy\）**

执行命令 ssh-keygen -t rsa -C email地址，创建github对应的sshkey，命名为id_rsa_github，密码可以不填
`
cd C:\Users\xx\.ssh
`
`
ssh-keygen -t rsa -C xxx@xxx.com
`

**2. 同样的方式生产gerrit服务器的私钥公钥（邮箱地址可以相同可以不同）**

执行命令ssh-keygen -t rsa -C email，创建github对应的sshkey，命名为id_rsa_gerrit。
`
ssh-keygen -t rsa -C xxx@xxx.com
`

**3. 把github对应的公钥和gerrit对应的公钥上传到服务器**

略

**4. 在.ssh目录创建config文本文件并完成相关配置**

每个账号单独配置一个**Host**，每个Host要取一个别名，每个Host主要配置HostName和IdentityFile两个属性即可。

Host的名字可以取为自己喜欢的名字，不过这个会影响git相关命令，例如：
Host mygithub 这样定义的话，命令如下，即git@后面紧跟的名字改为mygithub
```bash
git clone git@mygithub:xx/xxx.git
```

HostName 　　　　　　　   这个是真实的域名地址
IdentityFile 　　　　　　　  这里是id_rsa的地址
PreferredAuthentications   配置登录时用什么权限认证--可设为publickey,password
publickey,keyboard-interactive等
User 　　　　　　　　　　　配置使用用户名

config文件配置：
```bash
#配置github.com
Host github.com
HostName github.com
IdentityFile C:\\Users\\xx\\.ssh\\id_rsa_github
User xx

#配置gerrit
Host gerrit
HostName 192.168.0.102
IdentityFile C:\\Users\\xx\\.ssh\\id_rsa
User yy
KexAlgorithms +diffie-hellman-group1-sha1
```

**5. 打开cmder客户端执行测试命令测试是否配置成功（会自动在.ssh目录生成known_hosts文件把私钥配置进去）**
`
ssh -T git@github.com
`