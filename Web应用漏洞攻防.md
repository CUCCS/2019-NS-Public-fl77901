# Web应用漏洞攻防
---

# 实验目的
- 了解常见 Web 漏洞训练平台；
- 了解 常见 Web 漏洞的基本原理；
- 掌握 OWASP Top 10 及常见 Web高危漏洞的漏洞检测、漏洞利用和漏洞修复方法；

# 实验环境
- WebGoat
- Juice Shop

# 实验要求
- 每个实验环境完成不少于 5 种不同漏洞类型的漏洞利用练习；
# 实验过程
- 实验环境搭建
    - 先在kali上安装docker和docker compose；
    - 然后按照下图所示搭建即可。 
    ![参考搭建口令][1]
    ![环境搭建中][2]
    - 搭建完成，登录webgoat  
    ![ps查看安全][3]
    ![登录webgoat][4]
- web应用漏洞攻防实验过程：  
一、webgoat环境下的五种攻击：  
1. 未验证的用户输入：绕过前端的限制  
    - 先在firefox中下载插件SwitchyOmega，并配置相关参数，再打开burpsuite，配置相关参数；  
    ![插件参数设置][5]
    ![bursuite相关参数][6]  
    - 在firefox中将proxy换为上述刚设置好的burpsuite，找到网站页面源码把maxlength改为12345，然后在burpsuite软件中选择intercept is on，提交表单，在burpsuite里面修改表单参数，最后forward： 
    ![表单直接提交失败][7]
    ![intercept is on][8] 
    ![修改参数][9]  
    ![表单提交成功][10]  
    - 实验分析：前端的限制类似于一个“摆设”，很容易被突破。
2. 未验证的用户输入:绕过客户端javascript校验  
    - 方法类似于‘绕过前端的限制’，不过在burpsuite里要修改error=0和在网站页面源码里删除关于提交限制的代码：
    ![修改表单内容，提交失败][11]
    ![error=0][12]
    ![删去代码][13]
    ![成功提交][14]
    - 实验分析：前端的校验标准容易被突破。
3. 跨站脚本执行XSS
    - 以Tom的身份登录，点击ViewProfile按钮，在street一栏输入`<script>alert('hacked')</script>`,然后点击UpdateProfile按钮，页面显示‘hacked’：  
    ![tom-开始][15]
    ![tom-非法代码][16]  
    ![tom-成功][17]
    - 以David身份登录，浏览Bruce信息，完成：  
    - ![david-bruce][18]  
    - 以Larry身份登录，点击‘SearchStaff’，在搜索框添加代码`<script>alert('dangerous')</script>`,最后点击‘FindProfile’按钮:  
    ![larry-非法代码][19]  
    ![larry-成功][20]
    - 实验分析：跨站脚本执行，用户可以通过非法输入改变程序运行结果。
4. 脆弱的访问控制  
    - 如下图所示，在‘User Name’输入‘webgoat’提交，然后密码找回提问最喜欢的颜色，尝试几次，red正确，于是很简单地就得到了密码：
    ![初始界面][21]
    ![获得密保red][22]  
    - 实验分析：web会提供密码找回功能，但有些密保问题过于简单会造成威胁。
5. 脆弱认证和会话管理
    - 如下图所示，先在邮件里添加`&SID=WHATEVER`,点击确认提交，页面出现‘Goat Hills Financial’，点击，按提示使用Jane（密码tarzan）登录，显示‘You are ：Hacker Joe’，点击‘Goat Hills Financial’，修改上方网址为`&SID=WHATEVER`,回车成功登录：
    ![改邮件][23]  
    ![jane登录][24]
    ![hacker-joe][25]
    ![成功登录][26]
    - 实验分析：本实验主要运用原理--服务器通过每个用户唯一的Session ID 来确认其合法性。如果用户已登录，并且授权他不必重新验证授权时，当他重新登录应用系统时，他的Session ID 依然是被认为合法的。所以我们直接在网址上修改SID就可以登录。
6. sql注入缺陷
    - 如下图随便选择一个用户，随便输入一个密码，打开burpsuite软件拦截提交，修改password为`'or'1'='1`，forward，成功绕过登录：
    ![随便选一个用户][27]
    ![修改密码][28]
    ![登录成功][29]
    - 实验分析：修改后的密码实现了passward为true。  
二、Juice Shop环境下的五种漏洞攻防  
    - 环境搭建，搭建成功后输入网址`127.0.0.1:3000/`进入juice shop：
    ![搭建1][30]  
    ![查看端口][31]  
    - 查找源码，访问对应网址，成功寻找到计分板：  
    ![找网址][32]  
    ![成功找到][33]  
1. sql注入缺陷  
    - 在登陆界面电子邮箱一栏填写填写`'or' 1'='1`失败，换为`'or 1=1--`,密码随便写，登录，成功绕过了认证实现了登录：  
    ![刚开始失败][34]  
    ![登录成功][35]  
    - 实验分析：同上述webgoat的sql。
2. 脆弱认证
    - 忘记密码，填写Bjoern的邮箱，发现密保问题，问他最喜欢的宠物，在网上查找发现他有个叫Zaya的猫，填入，成功更新他的代码：  
    ![密保][36]  
    ![填入问题][37]  
    ![成功登录][38]  
    - 实验分析：社会工程学问题破解密码。
3. XSS
    - 在搜索框输入代码`<iframe src="javascript:alert('xss')">`,出现弹框，实验成功：
    ![输入代码][39]
    ![xss攻击成功][40]
    - 实验分析：用户通过非法输入实现程序。
4. 敏感数据曝光
    - 浏览‘关于我们’，发现一个链接，鼠标放在链接上左下角显示下载路径，根据左下角路径访问ftp链接，发现文件，文件可以打开，实现了机密文件获取：
    ![发现链接][41]
    ![访问链接][42]
    ![文件可以打开][43]
    ![成功][44]
    - 实验分析：web网站常见漏洞，数据保存不认真，通过简单的操作就可以找到敏感数据文件。
5. 脆弱的访问控制
    - 打开burpsuite软件，设置intercept is on，返回juice shop点击购物车，看到burpsuite界面出现截取到的内容，修改第一行basket后数字为6，forward，成功看到别人购物车：
    ![初始][45]
    ![修改][46]
    ![成功][47]
    - 实验分析：通过简单的修改就可以访问其他用户的购物车，web网站访问控制设置简单。
    
# 实验总结
- 实验中遇到的问题及解决方案：  
1. 下载docker失败——host-only只是和宿主机相连，不能连网——添加NAT网络；
2. `docker-compose up -d`命令输入后一直提示不能连接到Docker daemon——没有把root用户添加到docker组中——把root用户添加到docker组中，重启；  
![bug2][48]  

3. `docker-compose up -d`下载过程中最后显示`no space left on device`——`df -h`查看磁盘空间发现/dev/sdal/全部被占满——上网查询资料，使用了两种方法（磁盘扩容、手动添加虚拟硬盘）扩大磁盘空间均失败——重启后虚拟机无法开机——查资料，在非图形界面删除所有运行日志、下载软件、文件夹，`df -h`查看磁盘空间依旧完全被占满，`ls -lh`查看发现已无可以清理的文件，reboot——重启失败——放弃这个kali，重新安装虚拟机kali，虚拟分配空间设为30GB,重新实验发现/dev/sda1磁盘变大可正常使用；  
![bug3][49]  
![bug3.2][50]  
![重装kali][51]  
![bug3-solved][52]  

4. 做完webgoat实验后，juice shop实验环境一直搭建失败——端口冲突，修改代码更换端口后依然搭建失败——虚拟机恢复快照前的状态，只搭建juice shop环境；
5. burpsuite软件proxy-options中无法勾选proxy listeners——查询资料，发现是端口被占用——换端口，与火狐里面的burpsuite保持一致：
    ![换6060][53]
    ![换6060-2][54]  

# 实验参考资料
- https://wenku.baidu.com/view/0d9de30e905f804d2b160b4e767f5acfa1c783d7.html?pn=1（webgoat中文手册）；
- https://github.com/CUCCS/2019-NS-Public-DXY0411/blob/ns_chap0x07/ns_chap0x07/Web%20%E5%BA%94%E7%94%A8%E6%BC%8F%E6%B4%9E%E6%94%BB%E9%98%B2.md（一位同学的实验报告）；


  [1]: https://s2.ax1x.com/2019/11/22/M75QTe.jpg
  [2]: https://s2.ax1x.com/2019/11/22/M75nOK.jpg
  [3]: https://s2.ax1x.com/2019/11/22/M753Yd.jpg
  [4]: https://s2.ax1x.com/2019/11/22/M75MwD.jpg
  [5]: https://s2.ax1x.com/2019/11/22/M7oGsP.jpg
  [6]: https://s2.ax1x.com/2019/11/22/M7o8Mt.jpg
  [7]: https://s2.ax1x.com/2019/11/23/MbVIH0.jpg
  [8]: https://s2.ax1x.com/2019/11/22/M7oMPH.jpg
  [9]: https://s2.ax1x.com/2019/11/22/M7oJqf.jpg
  [10]: https://s2.ax1x.com/2019/11/22/M7o1xI.jpg
  [11]: https://s2.ax1x.com/2019/11/23/MbVTEV.jpg
  [12]: https://s2.ax1x.com/2019/11/23/MbV5Bq.jpg
  [13]: https://s2.ax1x.com/2019/11/23/Mb5U0K.jpg
  [14]: https://s2.ax1x.com/2019/11/23/MbV4un.jpg
  [15]: https://s2.ax1x.com/2019/11/24/MLUKvd.jpg
  [16]: https://s2.ax1x.com/2019/11/24/MLUugH.jpg
  [17]: https://s2.ax1x.com/2019/11/24/MLUGUf.jpg
  [18]: https://s2.ax1x.com/2019/11/24/MLU8VP.jpg
  [19]: https://s2.ax1x.com/2019/11/24/MLUlDI.jpg
  [20]: https://s2.ax1x.com/2019/11/24/MLUQKA.jpg
  [21]: https://s2.ax1x.com/2019/11/24/MLwU2D.jpg
  [22]: https://s2.ax1x.com/2019/11/24/MLw8V1.jpg
  [23]: https://s2.ax1x.com/2019/11/24/MLwlr9.jpg
  [24]: https://s2.ax1x.com/2019/11/24/MLwQKJ.jpg
  [25]: https://s2.ax1x.com/2019/11/24/MLwJ56.jpg
  [26]: https://s2.ax1x.com/2019/11/24/MLwrVI.jpg
  [27]: https://s2.ax1x.com/2019/11/24/MLImv9.jpg
  [28]: https://s2.ax1x.com/2019/11/24/MLIegJ.jpg
  [29]: https://s2.ax1x.com/2019/11/24/MLIZ34.jpg
  [30]: https://s2.ax1x.com/2019/11/24/MLbsje.jpg
  [31]: https://s2.ax1x.com/2019/11/24/MLbrcD.jpg
  [32]: https://s2.ax1x.com/2019/11/24/MLq0Vs.png
  [33]: https://s2.ax1x.com/2019/11/24/MLqdbj.jpg
  [34]: https://s2.ax1x.com/2019/11/24/MLXTGq.jpg
  [35]: https://s2.ax1x.com/2019/11/24/MLX7R0.jpg
  [36]: https://s2.ax1x.com/2019/11/24/MLjdS0.jpg
  [37]: https://s2.ax1x.com/2019/11/24/MLjUWq.jpg
  [38]: https://s2.ax1x.com/2019/11/24/MLjwlV.jpg
  [39]: https://s2.ax1x.com/2019/11/24/MLvkpq.jpg
  [40]: https://s2.ax1x.com/2019/11/24/MLvA10.jpg
  [41]: https://s2.ax1x.com/2019/11/24/MLzNYd.jpg
  [42]: https://s2.ax1x.com/2019/11/24/MLzJTe.jpg
  [43]: https://s2.ax1x.com/2019/11/24/MLzGwD.jpg
  [44]: https://s2.ax1x.com/2019/11/24/MLztFH.jpg
  [45]: https://s2.ax1x.com/2019/11/24/MOSvCj.jpg
  [46]: https://s2.ax1x.com/2019/11/24/MOSx8s.jpg
  [47]: https://s2.ax1x.com/2019/11/24/MOSX5Q.jpg
  [48]: https://s2.ax1x.com/2019/11/22/M75AY9.jpg
  [49]: https://s2.ax1x.com/2019/11/22/M75io4.jpg
  [50]: https://s2.ax1x.com/2019/11/22/M75kFJ.jpg
  [51]: https://s2.ax1x.com/2019/11/22/M75ZS1.jpg
  [52]: https://s2.ax1x.com/2019/11/22/M75EWR.jpg
  [53]: https://s2.ax1x.com/2019/11/24/MOC33d.jpg
  [54]: https://s2.ax1x.com/2019/11/24/MOC19H.jpg