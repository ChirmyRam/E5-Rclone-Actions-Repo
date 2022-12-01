# E5-Rclone-Actions-Repo
本仓库地址：https://github.com/ChirmyRam/E5-Rclone-Actions-Repo

> 基本原理：在Actions中每隔一天在北京时间早上6点自动使用Rclone调用OneDrive使Office E5订阅保持活跃，同时将配置文件反向提交到仓库保持Github活跃，玄学续期，不保证百分百成功。

**声明：Github严厉打击在公共仓库使用actions刷自动脚本的行为，轻则封仓库，重则封号，像我一样头铁的可以试试，请勿直接fork本仓库以免连坐，单独复制本仓库链接导入新建仓库！由于我自己同时用了多种刷API的工具，所以并不确定能否只靠本项目续订！**

# 一、说明

- `E5-Rclone-Actions-Repo.yml`文件中cron时间表达式按UTC时间运行，比北京时间晚8小时，若在北京时间早上6点运行则应填写为22时。为降低资源使用率，避免被识别为滥用，所以每隔一天运行一次。

- 基于原项目[E5-Rclone-Actions](https://github.com/peng4740/E5-Rclone-Actions)进一步改良，原项目在secret填入rclone配置文件后无法更新配置文件，尽管rclone获取的token有效期为90天，但重新配置一次还挺费劲。所以我的改良思路为：预先把rclone配置文件压缩加密后放进仓库，用rclone读取配置文件，过一遍自动上传、删除文件（夹），配置文件就会自动刷新，最后将刷新后的配置文件再次打包加密上传到仓库覆盖，实现自动更新配置文件无限循环。

- 微软在[Microsoft365开发人员面板](https://developer.microsoft.com/zh-cn/microsoft-365/profile/settings/accounts-linked)中新增**将开发人员帐户与Microsoft365开发人员计划帐户绑定**的选项，通过链接GitHub帐户，授权Microsoft使用GitHub活动数据来度量开发人员活动，GitHub活动将计入Microsoft365开发人员版订阅续订中。本项目每次调用OneDrive结束后自动将配置文件提交回仓库，同时会让Github产生活动记录，一举两得。

# 二、使用步骤
## 1. 导入仓库
**注意**：fork后的仓库、私有仓库通过actions提交代码不会计入github个人首页的活跃热力图中，即在此仓库没有**contributions**，当然官方仍未说明在github的活跃度是如何度量的。所以要在github个人活跃度中有记录，需要手动新建、导入仓库。
## 2. 配置rclone
rclone添加OneDrive的教程不再赘述，需注意的是配置名应为**e5**，配置时或配置完成后注意设定配置名，若实在需要改配置名，应同时更改仓库`/.github/workflows/`路径下**E5-Rclone-Gist-Actions.yml**文件中的**e5**相关参数。将名为**rclone.conf**的配置**单文件**加密压缩（即解压后只有这个文件没有目录）为**zip**格式，并将压缩包重命名为**rclone.zip**上传至仓库根目录。**注意：若使用的压缩软件是WinRAR，加密压缩时请在输入密码的小窗口下方勾选“ZIP使用传统加密法”，否则会报错"unsupported compression method 99"，推荐使用7zip压缩本文件。**
## 3. 配置secret

点击仓库顶栏**settings**→**Secrets→New repository secret**：

- Name：**PASSWD**  Value：**压缩包密码**


## 4. 启动actions
点击仓库顶栏**Actions**→**I understand my workflows, go ahead and enable them**，手动enable两个workflow。

在**Actions**中选择左侧**E5-Rclone-Actions-Repo**，点击右侧**Run workflow**手动运行测试一下是否正常。
