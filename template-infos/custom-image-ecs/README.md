## 部署说明
模板示例修改说明：
- 该模板适用于计算巢中ISV通过自定义镜像部署软件的场景，您可以基于该示例，在创建ISV服务时配置镜像分发实现多地域创建不同镜像的ECS服务器。

如何使用模板示例创建资源栈：
- 如果您使用RAM用户，使用模板前请确保已开通ECS、VPC服务并已得到授权。
- 点击“创建资源栈”进行部署，根据参数的描述，填写或选择参数。
- 资源栈创建完成后，通过资源栈输出页面查询EcsInstanceId。

## 自动化部署
1. 点击[部署链接](https://ros.console.aliyun.com/region/stacks/create?templateUrl=https://ros-public-templates.oss-cn-hangzhou.aliyuncs.com/ros-templates/examples/isv/custom-image-ecs.yml&hideStepRow=true&hideStackConfig=true&pageTitle=自定义镜像创建单实例ECS云服务器(未添加数据盘)&isSimplified=true&balanceIntercept=true)进入部署页面。
2. 在**配置模板参数**步骤中，根据控制台提示输入参数信息，确认安全信息后点击**下一步：检查并确认**进入参数确认页面。
3. 在资源配置预览页面，确认模板参数以及费用之后，单击**创建**。
4. 当**资源栈信息**页面的**状态**显示为**创建成功**时表示自动化部署完成。

## 部署信息确认
- 单击**资源**页签，可查看创建的资源。
- 单击**参数**页签，可查看创建资源栈时填写的参数信息。
- 单击**输出**页签，可查看输出信息，可用于部署验证。

## 清理资源
1. 在[资源栈列表](https://ros.console.aliyun.com/region/stacks)中选择之前创建的资源栈。
2. 单击其右侧**操作**列的**删除**并确认，可一键删除所有创建的资源。
