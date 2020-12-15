
## Build image（optional）

可以修改 `Dockerfile` 来重新构建镜像，构建时运行 `make build-img` ，之后将构建的镜像推送至自己的镜像仓库，然后将 `template.yaml` 中的 `Image` 字段修改为自己的镜像地址即可。

## Deploy

部署前需要将 `template.yaml` 中的域名进行替换为 "Auto" 选项或者自己使用的自定义域名，然后执行 `make deploy` 将函数部署至函数计算。

## Sync wordpress to NAS

部署完成后需要将 `src/conf/wordpress.conf` 中的域名 `37917579-1986114430573743.test.functioncompute.com` 替换成自己的自定义域名。然后将 wordpress 相关文件上传到 NAS 中：

```bash
$  make sync-nas
```

同步完成后，可以浏览器访问： 37917579-1986114430573743.test.functioncompute.com/readme.html 来验证 nginx 启动成功；访问 37917579-1986114430573743.test.functioncompute.com/info.php 来验证 php-fpm 启动成功。

若修改了 NAS 挂载路径配置，请将 `src/conf/wordpress.conf` 中的 `root` 字段以及 `template.yaml` 中的 `MY_FC_DIR` 环境变量字段进行相应更新。

## Init

首次访问域名时，需要初始化 wordpress 的 sqlite 设置，后续访问即可进行页面浏览。