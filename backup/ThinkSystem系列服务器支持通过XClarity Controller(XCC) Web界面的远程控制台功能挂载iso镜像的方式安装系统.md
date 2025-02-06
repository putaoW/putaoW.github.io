知识点分析:
ThinkSystem系列服务器支持通过XClarity Controller(XCC) Web界面的远程控制台功能挂载iso镜像的方式安装系统。
 
首先要确认当前服务器是否已激活远程控制台及远程挂载iso镜像的功能。
 
登陆XCC Web界面后，查看"BMC Configuration"中的"License"：
 
"Lenovo XClarity Controller Advanced Upgrade"提供远程控制台功能；
 
"Lenovo XClarity Controller Enterprise Upgrade"提供远程挂载iso镜像功能。
 

 
 
确认以上两项功能同时具备后，点击"Home"或者"Remote Console"，再点击"Remote Console"行下面的显示器缩略图即可打开远程控制台窗口。
 
注意：XCC中的远程控制台使用HTML5技术，需要浏览器支持及启用HTML5功能，并提供所需权限和安全许可。
 

 
 
配备有
 
ThinkSystem RAID930 系列
 
ThinkSystem RAID530 系列
 
ThinkSystem 430 系列
 
SAS RAID和HBA卡的服务器在安装
 
Windows Server 2012 R2
 
Windows Server 2016
 
Red Hat Enterprise Linux 7.3
 
SUSE Linux Enterprise Server 11 SP4
 
SUSE Linux Enterprise Server 12 SP2
 
等操作系统时，需要加载RAID和HBA卡驱动。
 
联想数据中心产品支持网站提供了所需驱动：
 
对于Windows Server需要解压驱动包，并使用iso镜像工具（如UltraISO/软碟通）软件重新打包为iso镜像备用；
 
对于Red Hat Enterprise Linux 7.3系统需要解压出"dd.iso"备用；
 
对于SUSE Linux Enterprise Server系统使用kISO镜像安装，[详细请点击此处了解](http://iknow.lenovo.com/detail/dc_169418.html)。

操作步骤:
一、远程挂载镜像和驱动安装Red Hat Enterprise Linux 7.3操作系统示例
 
点击远程控制台的"Media"图标；
 

 
 
点击"Active"激活镜像挂载功能；
 
点击"Browse..."浏览系统安装镜像iso文件；
 

 
点击挂载镜像窗口右上方的"+"，以挂载另一个镜像；
 

 
点击第二行的"Browse..."浏览dd.iso驱动文件；
 

 
系统安装镜像和驱动都选择完毕后，点击"Mount all local media"以挂载镜像；
 

 
 
提示挂载成功，滚动并点击镜像挂载窗口最下方的"Close"关闭此窗口。
 
如果要取消挂载，则点击所选镜像右侧的"Unmount"或者"Deactive"。
 

 
启动服务器，在出现ThinkSystem logo界面按F12键进入选择启动项界面。选择"XCC Virtual Media"这一项；
 

 
出现Red Hat Enterprise Linux 7.3操作系统安装GRUB界面，选择"Install Red Hat Enterprise Linux 7.3"并按"E"键；
 

 
在第二行"quiet"前面输入"dd"，再按"Ctrl+X"组合键继续；

 
 
找到装载有"dd.iso"的光驱，即"4) sr1"，输入数字"4"。
 
注意：如果没有识别到光驱，需要先输入字母"R"刷新一次。

 
 
选择要加载的驱动程序，即"1)" SAS驱动，输入数字"1"；
 

 
 
所需驱动已经被选中"1) [x]"，输入字母"C"继续；
 

 
 
不需要再加载其他驱动，输入字母"C"继续；
 

 
 
安装程序会继续加载文件，并进入图形安装界面；
 

 
 
进入安装界面后并选择语言文字后，在选择安装位置时即可发现卷和硬盘；
 

 
 
然后按照向导提示进行选择并继续安装即可。
 
二、远程挂载镜像和驱动安装Windows Server 2016操作系统示例
 
注意：光盘（或者虚拟光盘）安装Windows操作系统时会提示"Press any key to boot from CD or DVD"，需要按任意键以从光盘引导启动。
 

 
 
Windows Server操作系统安装镜像及驱动文件挂载方式与上文相同。
 

 
在分区界面（你想将Windows安装在哪里？）会无法找到卷和硬盘。先点击“浏览驱动程序”，加载驱动；
 

 
浏览并选择驱动所在光盘或者文件夹；

 
 
点击对应ThinkSystem RAID/HBA驱动程序，再点击下一步加载；

 
 
然后即可发现卷和硬盘，可新建分区并下一步开始安装操作系统。
 

 
各个版本操作系统对应的驱动下载链接：
[lnvgy_dd_sraidmr_7.702.06.00_rhel7.3_x86-64_dd.zip](http://webdoc.lenovo.com.cn/lenovowsi/new_cskb/att/167754/lnvgy_dd_sraidmr_7.702.06.00_rhel7.3_x86-64_dd.zip)

[lnvgy_dd_sraidmr35_7.702.12.00_win2012r2-64.zip](http://webdoc.lenovo.com.cn/lenovowsi/new_cskb/att/167754/lnvgy_dd_sraidmr35_7.702.12.00_win2012r2-64.zip)

[lnvgy_dd_sraidmr35_7.702.12.00_win2016-64.zip](http://webdoc.lenovo.com.cn/lenovowsi/new_cskb/att/167754/lnvgy_dd_sraidmr35_7.702.12.00_win2016-64.zip)

[lnvgy_dd_sraidmr_7.702.06.00_sles11-sp4_x86-64.zip](http://webdoc.lenovo.com.cn/lenovowsi/new_cskb/att/167754/lnvgy_dd_sraidmr_7.702.06.00_sles11-sp4_x86-64.zip)

[lnvgy_dd_sraidmr_7.702.06.00_sles12-sp2_x86-64.zip](http://webdoc.lenovo.com.cn/lenovowsi/new_cskb/att/167754/lnvgy_dd_sraidmr_7.702.06.00_sles12-sp2_x86-64.zip)