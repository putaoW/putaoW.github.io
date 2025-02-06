V7交换机静态路由、Track与NQA联动配置
目录
[V7交换机静态路由、Track与NQA联动配置](file:///C:/Users/Administrator/AppData/Local/Temp/CyberArticle/fe2c41668234a87feb83940b3bbf0bba.htm#_Toc10138454)
[1 配置需求或说明](file:///C:/Users/Administrator/AppData/Local/Temp/CyberArticle/fe2c41668234a87feb83940b3bbf0bba.htm#_Toc10138455)
	[1.1适用产品系列](file:///C:/Users/Administrator/AppData/Local/Temp/CyberArticle/fe2c41668234a87feb83940b3bbf0bba.htm#_Toc10138456)
	[1.2配置需求及实现的效果](file:///C:/Users/Administrator/AppData/Local/Temp/CyberArticle/fe2c41668234a87feb83940b3bbf0bba.htm#_Toc10138457)
[2 组网图](file:///C:/Users/Administrator/AppData/Local/Temp/CyberArticle/fe2c41668234a87feb83940b3bbf0bba.htm#_Toc10138458)
[3 配置步骤](file:///C:/Users/Administrator/AppData/Local/Temp/CyberArticle/fe2c41668234a87feb83940b3bbf0bba.htm#_Toc10138459)
[4 验证配置](file:///C:/Users/Administrator/AppData/Local/Temp/CyberArticle/fe2c41668234a87feb83940b3bbf0bba.htm#_Toc10138460)
	 
 
1  配置需求或说明
1.1适用产品系列
	本案例适用于如S7000E、S7500E等的V7交换机，V5、V7交换机具体分类及型号可以参考“1.1 Comware V5、V7平台交换机分类说明”。
1.2配置需求及实现的效果
		SW1、SW2、SW3和SW4连接了2.2.2.0/24和3.3.3.0/24两个网段，在交换机上配置静态路由以实现两个网段的互通，并配置路由备份以提高网络的可靠性。
		SW2上配置环回口L0模拟2.2.2.0/24网段内的主机，在SW2上存在两条到达3.3.3.0/24网段的静态路由，下一跳分别为SW1和SW4，这两条静态路由形成备份，其中：
	·     下一跳为SW1的静态路由优先级高，作为主路由。该路由可达时，SW2通过SW1将报文转发到3.3.3.0/24网段。
	·     下一跳为SW4的静态路作为备份路由。
	·     在SW2上通过静态路由、Track与NQA联动，实时判断主路由是否可达。当主路由不可达时，备份路由生效，SW2通过SW4将报文转发到3.3.3.0/24网段。
	 
		同样地，SW3上配置环回接口L0模拟3.3.3.0/24网段内的主机，在SW3D上存在两条到达2.2.2.0/24网段的静态路由，下一跳分别为SW1和SW4。这两条静态路由形成备份，其中：
	·     下一跳为SW1的静态路由优先级高，作为主路由。该路由可达时，SW3通过SW1将报文转发到2.2.2.0/24网段。
	·     下一跳为SW4的静态路作为备份路由。
	·     在SW3上通过静态路由、Track与NQA联动，实时判断主路由是否可达。当主路由不可达时，备份路由生效，SW3通过SW4将报文转发到2.2.2.0/24网段。
2  组网图
	
![Image](https://github.com/user-attachments/assets/4253807d-cf69-48d3-adf5-b375f846e0f1)

	
	
3  配置步骤
一． 交换机vlan和ip地址基本配置
	#在SW1上关闭生成树
	<H3C>system-view
	System View: return to User View with Ctrl+Z.
	[H3C]undo stp global enable
	#创建vlan 4 
	[H3C]vlan 4
	[H3C-vlan4]quit
	#配置vlan 1和vlan 4虚接口IP地址
	[H3C]interface Vlan-interface 1
	[H3C-Vlan-interface1] ip address 13.13.13.1 255.255.255.0
	[H3C-Vlan-interface1]quit
	[H3C]interface Vlan-interface 4
	[H3C-Vlan-interface4] ip address 12.12.12.1 255.255.255.0
	[H3C-Vlan-interface4]quit
	#将端口2换分到vlan 4，端口1默认属于vlan 1
	[H3C]interface GigabitEthernet 1/0/2
	[H3C-GigabitEthernet1/0/2] port access vlan 4
	[H3C-GigabitEthernet1/0/2]quit
 
	#在SW2上关闭生成树
	<H3C>system-view
	System View: return to User View with Ctrl+Z.
	[H3C]undo stp global enable
	#创建环回接口LoopBack 0用来模拟主机，地址是2.2.2.2/24
	[H3C]interface LoopBack 0
	[H3C-LoopBack0]ip address 2.2.2.2 255.255.255.0
	[H3C-LoopBack0]quit
	#创建vlan 3、4，并分别配置IP地址。
	[H3C]vlan 3
	[H3C-vlan3]quit
	[H3C]interface Vlan-interface 3
	[H3C-Vlan-interface3] ip address 24.24.24.2 255.255.255.0
	[H3C-Vlan-interface3]quit
	[H3C]vlan 4
	[H3C-vlan4]quit
	[H3C]interface Vlan-interface 4
	[H3C-Vlan-interface4] ip address 12.12.12.2 255.255.255.0
	[H3C-Vlan-interface4]quit
	#将端口1换分到vlan 4，端口2划分到vlan 3
	[H3C]interface GigabitEthernet 1/0/1
	[H3C-GigabitEthernet1/0/1] port access vlan 4
	[H3C-GigabitEthernet1/0/1]quit
	[H3C]interface GigabitEthernet 1/0/2
	[H3C-GigabitEthernet1/0/2] port access vlan 3
	[H3C-GigabitEthernet1/0/2]quit
 
	#在SW3上关闭生成树
	<H3C>system-view
	System View: return to User View with Ctrl+Z.
	[H3C]undo stp global enable
	#创建环回接口LoopBack 0用来模拟主机，地址是3.3.3.3/24
	[H3C]interface LoopBack 0
	[H3C-LoopBack0]ip address 3.3.3.3 255.255.255.0
	[H3C-LoopBack0]quit
	#创建vlan 2，并分别配置vlan 1和vlan 2的IP地址
	[H3C]vlan 2
	[H3C-vlan2]quit
	[H3C]interface Vlan-interface 1
	[H3C-Vlan-interface1] ip address 13.13.13.3 255.255.255.0
	[H3C-Vlan-interface1]quit
	[H3C]interface Vlan-interface 2
	[H3C-Vlan-interface2] ip address 34.34.34.3 255.255.255.0
	[H3C-Vlan-interface2]quit
	#将端口1换分到vlan 2，端口2默认属于vlan 1
	[H3C]interface GigabitEthernet 1/0/1
	[H3C-GigabitEthernet1/0/1] port access vlan 2
	[H3C-GigabitEthernet1/0/1]quit
 
	#在SW4上关闭生成树
	<H3C>system-view
	System View: return to User View with Ctrl+Z.
	[H3C]undo stp global enable
	#创建vlan 2、3，并分别配置IP地址
	[H3C]vlan 2
	[H3C-vlan2]quit
	[H3C]interface Vlan-interface 2
	[H3C-Vlan-interface2] ip address 34.34.34.4 255.255.255.0
	[H3C-Vlan-interface2]quit
	[H3C]vlan 3
	[H3C-vlan3]quit
	[H3C]interface Vlan-interface 3
	[H3C-Vlan-interface3] ip address 24.24.24.4 255.255.255.0
	[H3C-Vlan-interface3]quit
	#将端口1换分到vlan 3，端口2划分到vlan 2
	[H3C]interface GigabitEthernet 1/0/1
	[H3C-GigabitEthernet1/0/1] port access vlan 3
	[H3C-GigabitEthernet1/0/1]quit
	[H3C]interface GigabitEthernet 1/0/2
	[H3C-GigabitEthernet1/0/2] port access vlan 2
	[H3C-GigabitEthernet1/0/2]quit
	 
 
二． 链路检测配置
	 
	【SW1】
	#SW1配置到2.2.2.0和3.3.3.0网段的静态路由
	[H3C]ip route-static 2.2.2.0 24 12.12.12.2
	[H3C]ip route-static 3.3.3.0 24 13.13.13.3
 
	【SW2】
	# 配置到达3.3.3.0/24网段的静态路由：下一跳地址为12.12.12.1，优先级为缺省值60，该路由与Track项1关联。
	<H3C> system-view
	[H3C] ip route-static 3.3.3.0 24 12.12.12.1 track 1
	# 配置到达3.3.3.0/24网段的静态路由：下一跳地址为24.24.24.4，优先级为80。
	[H3C] ip route-static 3.3.3.0 24 24.24.24.4 preference 80
	# 配置到达13.13.13.0的静态路由：下一跳地址为12.12.12.1。
	[H3C]ip route-static 13.13.13.0 24 12.12.12.1
	# 配置到达34.34.34.0的静态路由：下一跳地址为24.24.24.4。
	[H3C]ip route-static 34.34.34.0 24 24.24.24.4
	# 创建管理员名为admin、操作标签为test的NQA测试组。
	[H3C] nqa entry admin test
	# 配置测试类型为ICMP-echo。
	[H3C-nqa-admin-test] type icmp-echo
	# 配置测试的目的地址为13.13.13.3，下一跳地址为12.12.12.1，以便通过NQA检测Switch 2－Switch 2－Switch 3这条路径的连通性。
	[H3C-nqa-admin-test-icmp-echo] destination ip 13.13.13.3
	[H3C-nqa-admin-test-icmp-echo] next-hop ip 12.12.12.1
	# 配置测试频率为100ms。
	[H3C-nqa-admin-test-icmp-echo] frequency 100
	# 配置联动项1（连续失败5次触发联动）。
	[H3C-nqa-admin-test-icmp-echo] reaction 1 checked-element probe-fail threshold-type consecutive 5 action-type trigger-only
	[H3C-nqa-admin-test-icmp-echo] quit
	# 启动探测。
	[H3C] nqa schedule admin test start-time now lifetime forever
	# 配置Track项1，关联NQA测试组（管理员为admin，操作标签为test）的联动项1。
	[H3C] track 1 nqa entry admin test reaction 1
	【SW3】
	# 配置到达2.2.2.0/24网段的静态路由：下一跳地址为13.13.13.1，优先级为缺省值60，该路由与Track项1关联。
	<H3C> system-view
	[H3C] ip route-static 2.2.2.0 24 13.13.13.1 track 1
	# 配置到达2.2.2.0/24网段的静态路由：下一跳地址为34.34.34.4，优先级为80。
	[H3C] ip route-static 2.2.2.0 24 34.34.34.4 preference 80
	# 配置到达12.12.12.0的静态路由：下一跳地址为13.13.13.1。
	[H3C] ip route-static 12.12.12.0 24 13.13.13.1
	# 配置到达24.24.24.0的静态路由：下一跳地址为34.34.34.4。
	[H3C] ip route-static 24.24.24.0 24 34.34.34.4
	# 创建管理员名为admin、操作标签为test的NQA测试组。
	[H3C] nqa entry admin test
	# 配置测试类型为ICMP-echo。
	[H3C-nqa-admin-test] type icmp-echo
	# 配置测试的目的地址为12.12.12.2，下一跳地址为13.13.13.1，以便通过NQA检测Switch 2－Switch 2－Switch 3这条路径的连通性。
	[H3C-nqa-admin-test-icmp-echo] destination ip 12.12.12.2
	[H3C-nqa-admin-test-icmp-echo] next-hop ip 13.13.13.1
	# 配置测试频率为100ms。
	[H3C-nqa-admin-test-icmp-echo] frequency 100
	# 配置联动项1（连续失败5次触发联动）。
	[H3C-nqa-admin-test-icmp-echo] reaction 1 checked-element probe-fail threshold-type consecutive 5 action-type trigger-only
	[H3C-nqa-admin-test-icmp-echo] quit
	# 启动探测。
	[H3C] nqa schedule admin test start-time now lifetime forever
	# 配置Track项1，关联NQA测试组（管理员为admin，操作标签为test）的联动项1。
	[H3C] track 1 nqa entry admin test reaction 1
	 
	【SW4】
	#SW1配置到2.2.2.0和3.3.3.0网段的静态路由
	[H3C]ip route-static 2.2.2.0 24 24.24.24.2
	[H3C]ip route-static 3.3.3.0 24 34.34.34.3
	 
4  验证配置
 # 显示SW2上Track项的信息：
[H3C]display track all
Track ID: 1
  State: Positive
  Duration: 0 days 0 hours 1 minutes 18 seconds
  Tracked object type: NQA
  Notification delay: Positive 0, Negative 0 (in seconds)
  Tracked object:
    NQA entry: admin test
    Reaction: 1
    Remote IP/URL: 13.13.13.3
    Local IP: --
    Interface: --
#查看SW2上的路由表：
[H3C]display ip routing-table
 
Destinations : 23       Routes : 23
 
Destination/Mask   Proto   Pre Cost        NextHop         Interface
0.0.0.0/32         Direct  0   0           127.0.0.1       InLoop0
2.2.2.0/24         Direct  0   0           2.2.2.2         Loop0
2.2.2.0/32         Direct  0   0           2.2.2.2         Loop0
2.2.2.2/32         Direct  0   0           127.0.0.1       InLoop0
2.2.2.255/32       Direct  0   0           2.2.2.2         Loop0
3.3.3.0/24         Static  60  0           12.12.12.1      Vlan4
12.12.12.0/24      Direct  0   0           12.12.12.2      Vlan4
12.12.12.0/32      Direct  0   0           12.12.12.2      Vlan4
12.12.12.2/32      Direct  0   0           127.0.0.1       InLoop0
12.12.12.255/32    Direct  0   0           12.12.12.2      Vlan4
13.13.13.0/24      Static  60  0           12.12.12.1      Vlan4
24.24.24.0/24      Direct  0   0           24.24.24.2      Vlan3
24.24.24.0/32      Direct  0   0           24.24.24.2      Vlan3
24.24.24.2/32      Direct  0   0           127.0.0.1       InLoop0
24.24.24.255/32    Direct  0   0           24.24.24.2      Vlan3
34.34.34.0/24      Static  60  0           24.24.24.4      Vlan3
127.0.0.0/8        Direct  0   0           127.0.0.1       InLoop0
127.0.0.0/32       Direct  0   0           127.0.0.1       InLoop0
127.0.0.1/32       Direct  0   0           127.0.0.1       InLoop0
127.255.255.255/32 Direct  0   0           127.0.0.1       InLoop0
224.0.0.0/4        Direct  0   0           0.0.0.0         NULL0
224.0.0.0/24       Direct  0   0           0.0.0.0         NULL0
255.255.255.255/32 Direct  0   0           127.0.0.1       InLoop0
#以上NQA测试的结果为主路由可达（Track项状态为Positive），访问3.3.3.0/24网段路径是SW2-SW1-SW3。
 
#将SW1的2号端口shutdown
[H3C]int GigabitEthernet 1/0/2
[H3C-GigabitEthernet1/0/2]shutdown
# 显示SW2上Track项的信息：
[H3C]display track all
Track ID: 1
  State: Negative
  Duration: 0 days 0 hours 0 minutes 10 seconds
  Tracked object type: NQA
  Notification delay: Positive 0, Negative 0 (in seconds)
  Tracked object:
    NQA entry: admin test
    Reaction: 1
    Remote IP/URL: 13.13.13.3
    Local IP: --
Interface: --
#查看SW2上的路由表
[H3C]display ip routing-table
 
Destinations : 18       Routes : 18
 
Destination/Mask   Proto   Pre Cost        NextHop         Interface
0.0.0.0/32         Direct  0   0           127.0.0.1       InLoop0
2.2.2.0/24         Direct  0   0           2.2.2.2         Loop0
2.2.2.0/32         Direct  0   0           2.2.2.2         Loop0
2.2.2.2/32         Direct  0   0           127.0.0.1       InLoop0
2.2.2.255/32       Direct  0   0           2.2.2.2         Loop0
3.3.3.0/24         Static  80  0           24.24.24.4      Vlan3
24.24.24.0/24      Direct  0   0           24.24.24.2      Vlan3
24.24.24.0/32      Direct  0   0           24.24.24.2      Vlan3
24.24.24.2/32      Direct  0   0           127.0.0.1       InLoop0
24.24.24.255/32    Direct  0   0           24.24.24.2      Vlan3
34.34.34.0/24      Static  60  0           24.24.24.4      Vlan3
127.0.0.0/8        Direct  0   0           127.0.0.1       InLoop0
127.0.0.0/32       Direct  0   0           127.0.0.1       InLoop0
127.0.0.1/32       Direct  0   0           127.0.0.1       InLoop0
127.255.255.255/32 Direct  0   0           127.0.0.1       InLoop0
224.0.0.0/4        Direct  0   0           0.0.0.0         NULL0
224.0.0.0/24       Direct  0   0           0.0.0.0         NULL0
255.255.255.255/32 Direct  0   0           127.0.0.1       InLoop0
#NQA测试的结果为主路由不可达（Track项状态为Negative），则备份路由生效，访问3.3.3.0/24网段路径是SW2-SW4-SW3。出现故障后，2.2.2.2到3.3.3.3之间仍然可以通信：
[H3C]ping -a 2.2.2.2 3.3.3.3
Ping 3.3.3.3 (3.3.3.3) from 2.2.2.2: 56 data bytes, press CTRL_C to break
56 bytes from 3.3.3.3: icmp_seq=0 ttl=254 time=4.000 ms
56 bytes from 3.3.3.3: icmp_seq=1 ttl=254 time=3.000 ms
56 bytes from 3.3.3.3: icmp_seq=2 ttl=254 time=1.000 ms
56 bytes from 3.3.3.3: icmp_seq=3 ttl=254 time=1.000 ms
56 bytes from 3.3.3.3: icmp_seq=4 ttl=254 time=1.000 ms
 
--- Ping statistics for 3.3.3.3 ---
5 packet(s) transmitted, 5 packet(s) received, 0.0% packet loss
round-trip min/avg/max/std-dev = 1.000/2.000/4.000/1.265 ms
[H3C]%May 29 10:21:41:325 2019 H3C PING/6/PING_STATISTICS: Ping statistics for 3.3.3.3: 5 packet(s) transmitted, 5 packet(s) received, 0.0% packet loss, round-trip min/avg/max/std-dev = 1.000/2.000/4.000/1.265 ms.