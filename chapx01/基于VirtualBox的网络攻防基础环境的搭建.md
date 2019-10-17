
# 基于VirtualBox的网络攻防基础环境的搭建
## 一、实验目的
- 掌握VirtualBox虚拟机的安装与使用；
- 掌握VirtualBox的虚拟网络类型和按需配置；
- 掌握VirtualBox的虚拟硬盘多重加载；

## 二、实验环境
- VirtualBox虚拟机；
- 攻击者主机（Attacker）：Kali Rolling 2109.2；
- 网关（Gateway，GW）：Debian Buster
- 靶机（Victim）：xp-sp/Kali；

## 三、实验内容
- 虚拟硬盘多重加载配置：    

创建虚拟机——虚拟机主界面右上角全局工具——虚拟介质管理
——类型下普通改为多重加载——出现弹框释放虚拟硬盘，点击确定——回到虚拟机主界面点击存储手动添加vdi文件——完成。    

![](https://s2.ax1x.com/2019/09/24/uEpDG4.md.jpg)

- 搭建下图所示虚拟机网络拓扑：    

    - 进行此项操作的注意事项：选择NAT网络而不是网络地址转换（NAT）！    
    
![](https://s2.ax1x.com/2019/09/24/uEEw8K.md.png)    

创建六台虚拟机——为每台虚拟机按图示设置网卡——完成。    

![](https://i.niupic.com/images/2019/09/26/_363.jpg)
![](https://s2.ax1x.com/2019/09/24/uEAnSO.md.jpg)
![](https://s2.ax1x.com/2019/09/24/uEAulD.md.jpg)
![](https://i.niupic.com/images/2019/09/26/_364.jpg)
![](https://s2.ax1x.com/2019/09/24/uEAefK.md.jpg)
![](https://s2.ax1x.com/2019/09/24/uEpBiF.md.jpg)

- 网络连通性测试：    

    - 进行此项实验前的注意事项：    
    a.关闭xp系统的防火墙；   
    b.开启所有的虚拟机（因为是一个完整的拓扑结构，不全部打开就够不成拓扑）；    
    c.下述步骤均以靶机Debian2、靶机xp2为例。

      - 1.靶机可以直接访问攻击者主机
    Attacker IP：10.0.2.4/24
    Gateway IP：10.0.2.15/24（NAT网络）
                172.16.111.1/24（intnet2网络）
    victim-xp-2 IP：172.16.111.139 （默认网关 172.16.111.1）
    victim-debian-2 IP：172.16.111.137/24
    靶机debian2-ping-攻击者主机可以访问：
    ![靶机Debian2-ping-主机][1]
    靶机xp-2-ping-攻击者主机可以访问：
    ![靶机xp-2-ping-主机][2]    
    
      - 2.攻击者主机无法直接访问靶机     
      攻击者-ping-靶机Debian2没有反应，无法访问：    
      攻击者 -ping-靶机xp-2没有反应，无法访问：    
      
      ![g][3]    
    
      - 3.网关可以直接访问攻击者主机和靶机    
      网关-ping-攻击者主机/靶机debian2/靶机xp-2可以访问：    
      ![网关][4]    
      
      - 4.靶机的所有对外上下行的流量必须经过网关    
      此处以靶机xp2为例，见下图：    
      ![此处输入图片的描述][5]    
      
      - 5.所有节点均可访问互联网    
      网关-ping-百度可以访问：    
      ![此处输入图片的描述][6]    
      攻击者主机-ping-百度可以访问：    
      ![此处输入图片的描述][7]    
      靶机Debian2-ping-百度可以访问：    
      ![此处输入图片的描述][8]    
      靶机xp2-ping-百度可以访问：    
      ![此处输入图片的描述][9]    
      
##四、实验总结    
- 实验中遇到的问题及其解决方案：    
    - 打开虚拟机无论哪一个都ping不通外网：    
      解决：开启所有的虚拟机（因为是一个完整的拓扑结构，不全部打开就够不成拓扑）；    
    - 进行虚拟机之间的互ping时ping不通：    
      解决：选择NAT网络而不是网络地址转换（NAT）！；    

- VirtualBox网络连接方式及其特点：    
![此处输入图片的描述][10]

##五、实验参考资料
- https://www.jianshu.com/p/280c6a6f2594（如何在Markdown中插入图片）
- https://www.jianshu.com/p/191d1e21f7ed（Markdown基础语法）
- https://github.com/CUCCS/2018-NS-Public-jckling/blob/master/ns-0x01/%E5%9F%BA%E4%BA%8EVirtualBox%E7%9A%84%E7%BD%91%E7%BB%9C%E6%94%BB%E9%98%B2%E5%9F%BA%E7%A1%80%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA.md（2016级师哥/姐的实验报告）
- 此外还要感谢赵紫如和谌雯馨同学对我提出的问题耐心回答！


  [1]: https://s2.ax1x.com/2019/09/26/umqW9J.jpg
  [2]: https://s2.ax1x.com/2019/09/26/umq2h4.jpg
  [3]: https://i.niupic.com/images/2019/09/26/_347.jpg
  [4]: https://i.niupic.com/images/2019/09/26/_348.jpg
  [5]: https://i.niupic.com/images/2019/09/26/_373.jpg
  [6]: https://i.niupic.com/images/2019/09/26/_349.jpg
  [7]: https://i.niupic.com/images/2019/09/26/_350.jpg
  [8]: https://i.niupic.com/images/2019/09/26/_351.jpg
  [9]: https://i.niupic.com/images/2019/09/26/_352.jpg
  [10]: https://i.niupic.com/images/2019/09/26/_1267.png