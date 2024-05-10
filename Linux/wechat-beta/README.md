# 微信Linux原生版

![wechat-beta](../../src/images/wechat-beta.png)

- 基于微信测试版1.0.0.145打包
- 修复了非麒麟发行版登录失败的问题
  
> 百度网盘链接：[wechat-beta_1.0.0.145_amd64.deb](https://pan.baidu.com/s/1F4CCjmCB5a5Md7gF-aMSlQ?pwd=am9h)， 提取码：am9h

```shell
# 安装
# 下载提供的deb包后

sudo dpkg -i wechat-beta_1.0.0.145_amd64.deb

# 卸载

sudo dpkg -P wechat-beta
```

## 自定义修改和构建deb包

**1. 下载解包文件、安装依赖**
1. 下载 [源deb解包文件](https://pan.baidu.com/s/1-VLkAeqsbgVkptbPe6rSkQ?pwd=uj3w)， 提取码: uj3w
2. `tar -xzvf wechat-beta.tar.gz && cd wechat-beta`
3. `sudo apt update && sudo apt install shc -y` 

**2. 修改 `extract/opt/wechat-launch.sh` 可以自定义微信数据保存位置**

```shell
FILEPATH="${HOME}/文档/WeChat_Files"
if [ -d "${HOME}/文档" ]; then
	mkdir -p ${HOME}/文档/WeChat_Files
        FILEPATH="${HOME}/文档/WeChat_Files"
else if [ -d "${HOME}/Documents" ]; then
        mkdir -p ${HOME}/Documents/WeChat_Files
        FILEPATH="${HOME}/Documents/WeChat_Files"
else
        mkdir -p ${HOME}/WeChat_Files
        FILEPATH="${HOME}/WeChat_Files"
fi

exec bwrap \
        --dev-bind / / \
        --bind ${HOME}/文档/WeChat_Files ${HOME}/文档/xwechat_files \
        --ro-bind /opt/wechat-beta/license/.kyact /etc/.kyact \
        --ro-bind /opt/wechat-beta/license/LICENSE /etc/LICENSE \
        --ro-bind /opt/wechat-beta//license/lsb-release-ukui /etc/lsb-release \
        --ro-bind-try /usr/lib/snapd-xdg-open/xdg-open /usr/bin/xdg-open \
        /opt/wechat-beta/wechat $@
```

**3. 将shell脚本封装为二进制程序**

```shell
# 进入 extract/opt/wechat-beta 目录
shc -f wechat-launch.sh && \
mv -f wechat-launch.sh.x wechat-launch &&\
rm -f wechat-launch.sh.c 
```

```shell
# 切换到 wechat-beta 根目录下
dpkg-deb -b extract/ build/

# 安装
sudo dpkg -i build/wechat-beta_1.0.0.145_amd64.deb