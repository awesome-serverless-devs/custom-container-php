
## 构建镜像（可选）

可以修改 `Dockerfile` 来重新构建镜像，流程如下所示：

```bash
# 构建镜像
$ custom-container-php  docker build -t fc-wordpress-custom-container  -f Dockerfile ./

# 标记镜像
$ custom-container-php  docker tag fc-wordpress-custom-container:latest "$registry_name"/"$namespace"/fc-wordpress-custom-container:"$version"
```

上述 `"$user_name"`、 `"$registry_name"` 、`"$namespace"` 以及 `$version` 均由使用者自行填写，在标记镜像结束后，可以选择将本地镜像推送至远端仓库：

```bash
# 登陆 registry
$ custom-container-php  docker login --username="$user_name" "$registry_name"

# 推送镜像
$ custom-container-php  docker push "$registry_name"/"$namespace"/fc-wordpress-custom-container:"$version"
```

或者选择在部署阶段使用 `--push-registry <pushRegistry>` 参数将镜像推送至远端仓库，这个将在下面进行介绍。

## 部署

部署时镜像的设置分为如下两种场景：镜像已推送以及镜像未推送。

### 镜像已推送

远端仓库已经存在函数依赖的镜像，则此时只需要将 `template.yaml` 中的 `Properties.CustomContainer.Image` 字段更改为对应镜像的地址即可，或者直接使用示例中的镜像地址即可。

### 镜像未推送

首先将 `template.yaml` 中的 `Properties.Function.CustomContainer.Image` 字段更改为远端镜像的理论地址，然后在部署时增加 `--push-registry acr-internet` 参数，这样就可以通过公网将镜像推送至远端仓库。

部署时域名设置也分为两种场景：使用 Auto 设置以及未使用 Auto 设置。

### 使用 Auto 设置

将 `template.yaml` 中的 `Properties.Function.Triggers.domains.domain` 字段设置为 `Auto` ，部署后记录下分配的自定义域名，然后将文件 "conf/wordpress.conf" 中的 `server_name` 字段后的域名替换为该域名即可。

### 未使用 Auto 配置

若要使用自己设置的自定义域名需要首先将该域名的 `CNAME` 解析到函数 `region` 下的函数计算 `endpoint` ，然后将 `template.yaml` 中的 `Properties.Function.Triggers.domains.domain` 字段替换为该域名，同时将文件 "conf/wordpress.conf" 中的 `server_name` 字段后的域名替换为该域名即可。

上述配置完成后，执行 `s deploy` 进行部署。

## 同步 wordpress 至 NAS

部署完成后将 wordpress 相关文件上传到 NAS 中：

```bash
$  s nas sync ./conf
```

同步完成后，可以浏览器访问： 37917579-1986114430573743.test.functioncompute.com/readme.html 来验证 nginx 启动成功；访问 37917579-1986114430573743.test.functioncompute.com/info.php 来验证 php-fpm 启动成功。

若修改了 NAS 挂载路径配置，请将 `src/conf/wordpress.conf` 中的 `root` 字段以及 `template.yaml` 中的 `MY_FC_DIR` 环境变量字段进行相应更新。

## 数据库初始化

首次访问域名时，需要初始化 wordpress 的 sqlite 设置，后续访问即可进行页面浏览。