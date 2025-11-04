# UAAA 域名加速产品 DNS 配置指南
## 一、配置背景说明
UAAA 域名加速产品通过独立 DNS 服务器提供优化解析服务，提升域名解析效率与稳定性。使用前需将云主机默认 DNS 指向特定DNS解析服务器，以启用加速功能，以下为各操作系统云主机的具体配置步骤。

## 二、DNS 服务器地址
请将私有网络中云主机的默认 DNS 修改为以下地址（主备顺序建议优先填写前者）：
```Plain
100.90.90.90
100.90.90.100
```

## 三、不同操作系统下的DNS修改方式
### **Rocky Linux / CentOS（含CentOS6/7/8）**

1. 临时修改 DNS（立即生效，重启后失效）​

```Plain
sudo vim /etc/resolv.conf
```

删除原有nameserver行，添加以下内容：
```Plain
nameserver 100.90.90.90
nameserver 100.90.90.100
```

保存退出  

2. 持久化配置（重启后保留）​

```Plain
sudo vim /etc/sysconfig/network-scripts/ifcfg-<网卡名>  # 例如ifcfg-eth0或ifcfg-ens33
```
修改以下字段（若不存在则新增）:
```Plain
DNS1=100.90.90.90  
DNS2=100.90.90.100  
```
将原配置中的DNS1/DNS2替换为指定地址，保存并退出，保存后重启网络服务：
```Plain
sudo systemctl restart network  
```
重启完成即可生效。

### **Ubuntu(含20.04/22.04/24.04)**

1. 临时修改 DNS（立即生效，重启后失效）​

```Plain
sudo vim /etc/resolv.conf
```

删除原有nameserver行，添加以下内容：
```Plain
nameserver 100.90.90.90
nameserver 100.90.90.100
```

保存退出  

2. 持久化配置（重启后保留）​

打开配置文件：
```Plain
sudo vim /etc/netplan/50-cloud-init.yaml
```
找到 nameservers: 区域，修改为：  
```Plain
nameservers:
  addresses: [100.90.90.90, 100.90.90.100]  
```
保存并退出，让配置立即生效：
```Plain
sudo netplan apply 
``` 

重启完成即可生效。

### **Ubuntu(含14.04/16.04/18.04)**

1. 临时修改 DNS（立即生效，重启后失效）​

```Plain
sudo vim /etc/resolv.conf
```

删除原有nameserver行，添加以下内容：
```Plain
nameserver 100.90.90.90
nameserver 100.90.90.100
```

保存退出  

2. 持久化配置（重启后保留）​

**非cloud init启动管理**

```Plain
sudo vim /etc/network/interfaces
```
找到eth0接口的dns-nameservers行，修改为：  
```Plain
dns-nameservers 100.90.90.90 100.90.90.100  
```

保存并退出，保存后重启网络服务： 
```Plain
sudo /etc/init.d/networking restart 
``` 
重启完成即可生效。

**使用cloud init进行启动管理**

```Plain
sudo vim /etc/network/interfaces.d/50-cloud-init.cfg 
```
修改dns-nameservers字段，修改为：
```Plain
dns-nameservers 100.90.90.90 100.90.90.100  
```
保存并退出，保存后重启网络服务： 
```Plain
sudo systemctl restart networking.service 
``` 
重启完成即可生效。


### Debian(含8/9/10)

1. 临时修改 DNS（立即生效，重启后失效）​

```Plain
sudo vi /etc/resolv.conf
```

删除原有nameserver行，添加以下内容：
```Plain
nameserver 100.90.90.90
nameserver 100.90.90.100
```

保存退出  

2. 持久化配置（重启后保留）​

**非cloud init启动管理**

```Plain
sudo vi /etc/network/interfaces
```
找到eth0接口的dns-nameservers行，修改为：  
```Plain
dns-nameservers 100.90.90.90 100.90.90.100  
```
可选保留公共DNS（如114.114.114.114）作为备用，配置如下：
```Plain
dns-nameservers 100.90.90.90 100.90.90.100 114.114.114.114  
``` 

保存并退出，保存后重启网络服务： 
```Plain
sudo systemctl restart networking  
``` 
重启完成即可生效。

**使用cloud init进行启动管理**

```Plain
sudo vi /etc/network/interfaces.d/50-cloud-init
```
修改dns-nameservers字段，修改为：
```Plain
dns-nameservers 100.90.90.90 100.90.90.100  
```
可选保留公共DNS（如114.114.114.114）作为备用，配置如下：
```Plain
dns-nameservers 100.90.90.90 100.90.90.100 114.114.114.114  
``` 

保存并退出，保存后重启网络服务： 
```Plain
sudo systemctl restart networking 
``` 
重启完成即可生效。

**使用 systemd-resolved（Debian 9/10 默认）**
禁用 resolv.conf 的自动管理
```Plain
sudo ln -sf /run/systemd/resolve/resolv.conf /etc/resolv.conf
```
编辑 systemd-resolved 配置
```Plain
sudo vi /etc/systemd/resolved.conf
```
在 [Resolve] 部分添加：
```Plain
DNS=100.90.90.90 100.90.90.100
FallbackDNS=114.114.114.114
```
保存并退出，保存后重启网络服务： 
```Plain
sudo systemctl restart systemd-resolved
```
重启完成即可生效。

### Redhat 6.X

1. 临时修改 DNS（立即生效，重启后失效）​

```Plain
sudo vim /etc/resolv.conf
```

删除原有nameserver行，添加以下内容：
```Plain
nameserver 100.90.90.90
nameserver 100.90.90.100
```

保存退出  

2. 持久化配置（重启后保留）​

```Plain
sudo vim /etc/sysconfig/network-scripts/ifcfg-eth0
```

找到eth0接口的dns-nameservers行，修改为：  

```Plain
DNS1=100.90.90.90
DNS2=100.90.90.100 
```

保存并退出，保存后重启网络服务： 

```Plain
sudo service network restart 
``` 
重启完成即可生效。


### Windows(含2008/2012/2016)

1. 右键点击start,选择 network connections。

![image](/images/1.png)

2. 右键点击 Ethernet，选择Properties。

![image](/images/2.png)

3. 选择Internet Protocol Version4(TCP/IPv4）,并修改Preferred DNS server和Alternnate DNS server。

![image](/images/3.png)

4. 点击OK，即可修改成功。

## 四、验证配置生效
配置完成后，可通过以下命令验证 DNS 是否生效：

- Linux/macOS：nslookup [your-domain.com](http://your-domain.com/)，查看解析服务器是否为 `100.90.90.90` 或 `100.90.90.100`。

- Windows：命令提示符执行nslookup，输入域名后查看 “服务器” 字段。

**注意事项**：

- 部分云厂商可能限制 DNS 修改，需确保网卡配置文件权限正常（如ifcfg-eth0文件可写）。
