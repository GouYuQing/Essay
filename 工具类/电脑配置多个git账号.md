# 电脑配置多个git账号

### 1. 生成新的SSH key（如果已有多个SSH key可跳过此步骤）
打开Git Bash或Terminal，输入以下命令：

```tsx
ssh-keygen -t rsa -C "your_email@example.com"
//your_email@example.com为你的新邮箱地址。
```

按照提示操作，生成新的SSH key，并将其保存在指定的位置。

并且生成的公钥私钥的文件会在~/.ssh文件下,可以进行命名的修改，方便分辨和进行ssh config的配置

### 2. 将新的SSh key复制到Key中

登录你的Git账号，打开“Settings” -> “SSH and GPG keys”，点击“New SSH key”按钮，将.pub（账号公钥）文件下的SSH key粘贴到“Key”中，点击“Add SSH key”按钮。并配置本地仓库用户名和邮箱

### 2.创建并配置SSH config文件

在Git Bash或Terminal中，创建config文件，输入以下命令：

```ts
touch ~/.ssh/config
//打开config文件，输入以下内容：

# 默认的账号
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa
# 新账号
Host github-new
  HostName github.com
  User git
  IdentityFile ~/.ssh/new_ssh_key
```

其中，**github.com**为默认的账号，g**ithub-new**为新账号，**/.ssh/id_rsa**为默认SSH key的路径，**/.ssh/new_ssh_key**为新SSH key的路径。

### 3.配置Git新的用户名和邮箱。

在Git Bash或Terminal中，输入以下命令：

```ts
git config user.name "Your Name"
git config user.email "your_email@example.com
//"其中，“Your Name”为你的新用户名，“your_email@example.com”为你的新邮箱地址。
```

### 4.测试账号连接

```ts
ssh -T git@github
ssh -T git@gitlab
//运行命令后如果出现“Hi xxxx! You’ve successfully authenticated, but GitHub does not provide shell access.”，其中“xxxx”为你配置的 User 名字，这时恭喜你，配置成功了~
```

完成以上步骤后，即成功在电脑上配置多个git账号。并且可以在不同电脑上同时操作仓库，在使用Git时，需要在使用新账号的本地仓库中进行操作。例如，使用新账号提交代码时，需要切换到新账号的本地仓库，并使用新账号的用户名和邮箱进行提交。