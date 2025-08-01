

# 新版本配置文件管理

用户可以在左侧导航栏选择用户参数模版，进入配置文件管理页。

配置文件是MySQL实例启动所需的重要组成，涉及相关的参数配置。MySQL实例都需要使用特定的配置文件，UCloud提供默认配置文件，默认配置文件不可以修改。

![image](/images/配置管理界面.png)

## 导入配置文件

除了提供的默认配置文件外，用户也可以导入自定义配置文件，以便让MySQL实例使用该配置。

点击配置文件上方的“导入”按钮，在弹出的输入框中填写“配置名称”、“描述”、“DB类型”，并选择本地的配置文件，点击确定后，即可完成导入。

![image](/images/导入配置文件.png)

## 创建配置文件

除导入配置文件外，也可以直接在配置文件管理页创建配置文件。

点击“创建”按钮，在弹出对话框中输入“配置名称”、“描述”、“DB类型”，并且可以选择从哪一个现有配置中进行复制，点击“确定”后即可完成配置文件的创建操作。

![image](/images/创建配置文件.png)

## 克隆配置文件

如果想快速复制一份配置文件，也可以直接在配置文件管理页克隆配置文件。

点击配置文件后面的“...”按钮，选中“克隆”。在弹出对话框中输入新的配置文件名称，点击“确定”后即可完成配置文件的克隆操作。

![image](/images/配置文件克隆.png)
![image](/images/克隆弹出信息.png)

## 查看配置文件详情

在配置文件列表中选择指定的配置文件，点击“详情”，查看具体参数信息及应用情况。

参数列表：

![image](/images/配置详情页面.png)

新增"默认值"和“参数描述”列

## 编辑配置文件

在配置文件列表中选择自定义配置文件，在“操作”中点击“详情”可以查看其当前的所有参数项并可以进行编辑及保存修改。

![image](/images/配置详情页面.png)

![image](/images/配置文件修改.png)

## 配置文件应用到实例

用户可以把当前配置文件，应用到同可用区同版本的实例中

点击“应用到实例”，在弹出对话框中选选择需要应用的实例。

![image](/images/应用到实例.png)

![image](/images/应用到实例弹出页.png)

注意：

应用配置文件可能需要重启实例。

## 删除配置文件

配置文件支持删除操作。在配置文件列表页选中需要删除的配置文件，选中删除即可完成删除操作。

![image](/images/删除配置文件.png)

注意：

默认配置文件不可删除。

## 实例修改配置信息

在实例的详情中可以对实例修改配置信息。可以点击“编辑”修改参数。也可以点击“应用参数模版”直接使用某个参数模版。

![image](/images/实例参数详情列表.png)

### 修改实例的参数

![image](/images/实例修改参数.png)

注意：

修改需要重启才能生效的参数会重启实例。需要客户确认当前的业务情况，选择合适时间修改。

### 应用参数模版

点击"应用参数模版"，在弹出对话框中选择新的配置文件名称，点击“确定”后即可完成新配置文件的应用。

![image](/images/应用参数模版.png)
