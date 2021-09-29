# E5-Rclone-Actions-Repo

> 基本原理：在Actions中每天北京时间早上6点自动使用Rclone调用OneDrive使Office E5订阅保持活跃，同时将配置文件反向提交到仓库保持Github活跃，玄学续期，不保证百分百成功。

**由于我自己同时用了多种续订工具，所以并不确定能否只靠本项目续订！**

# 说明

基于原项目[E5-Rclone-Actions](https://github.com/peng4740/E5-Rclone-Actions)进一步改良，原项目在secret填入rclone配置文件后无法更新配置文件，尽管rclone获取的token有效期挺长的，但重新配置一次还是得花一点时间。所以我的改良思路为：预先把rclone配置文件压缩加密后放进仓库，用rclone读取配置文件，过一遍自动上传、删除文件（夹），配置文件就会自动刷新，最后将刷新后的配置文件再次打包加密上传到仓库覆盖，实现自动更新配置文件无限循环。

微软在[Microsoft365开发人员面板](https://developer.microsoft.com/zh-cn/microsoft-365/profile/settings/accounts-linked)中新增**将开发人员帐户与Microsoft365开发人员计划帐户绑定**的选项，通过链接GitHub帐户，授权Microsoft使用GitHub活动数据来度量开发人员活动，GitHub活动将计入Microsoft365开发人员版订阅续订中。本项目每次调用OneDrive结束后自动将配置文件提交回仓库，同时会让Github产生活动记录，一举两得。

`E5-Rclone-Actions-Repo.yml`文件中cron表达式为UTC时间，比北京时间晚8小时，所以填写为22时。


# 其他修改：
- 加入每月自定提交文件的workflow，防止系统休眠。

# 使用步骤
## 1 导入仓库
**注意**：fork后的仓库通过actions提交代码不会计入github个人首页的活跃统计图中，即在此仓库没有**contributions**，当然官方仍没有说明在github的活跃度是如何度量的。所以要在github个人活跃度中有记录，需要手动新建、导入仓库。
## 2 配置rclone
rclone添加OneDrive的教程不再赘述，需注意的是配置名应为**e5**，配置时或配置完成后注意设定配置名，若实在需要改配置名，应同时更改仓库`/.github/workflows/`路径下**E5-Rclone-Gist-Actions.yml**文件中的**e5**相关参数。将名为**rclone.conf**的配置**单文件**加密压缩（即解压后只有这个文件没有目录）为**zip**格式，并将压缩包重命名为**rclone.zip**上传至仓库根目录。**注意：若使用的压缩软件是WinRAR，加密压缩时请在输入密码的小窗口下方勾选“ZIP使用传统加密法”，否则会报错"unsupported compression method 99"，推荐使用7zip压缩本文件。**
## 3 配置secret

点击仓库顶栏**settings**→**Secrets→New repository secret**：

- Name：**PASSWD**  Value：**压缩包密码**


## 4 启动actions
点击仓库顶栏**Actions**→**I understand my workflows, go ahead and enable them**，手动enable两个workflow。

- **Avoid workflow being suspended**：每月自动提交文件到仓库，防止因长期未提交文件造成actions被系统停止。

- **E5-Rclone-Actions-Repo**：每天自动使用rclone上传文件到OneDrive。

顺手star一下自己的仓库手动触发actions。
