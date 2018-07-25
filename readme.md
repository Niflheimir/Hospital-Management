这是本科java语言程序设计的第二次实验的内容。
采用桌面应用程序模式，开发一个医院挂号系统，管理包括人员、号种及其挂号费用，挂号退号等信息，完成登录、挂号、查询和统计打印功能。数据库表如下所示，建立索引的目的是加速访问，请自行确定每个索引要涉及哪些字段。

T_KSXX  (科室信息表)
字段名称	字段类型	主键	索引	可空	备注
KSBH	CHAR(6)	是	是	否	科室编号，数字
KSMC	CHAR(10)	否	否	否	科室名称
PYZS	CHAR(8)	否	否	否	科室名称的拼音字首

T_BRXX  (病人信息表)
字段名称	字段类型	主键	索引	可空	备注
BRBH	CHAR(6)	是	是	否	病人编号，数字
BRMC	CHAR(10)	否	否	否	病人名称
DLKL	CHAR(8)	否	否	否	登录口令
YCJE	DECIMAL(10,2)	否	否	否	病人预存金额
DLRQ	DateTime	否	否	是	最后一次登录日期及时间

T_KSYS  (科室医生表)
字段名称	字段类型	主键	索引	可空	备注
YSBH	CHAR(6)	是	是	否	医生编号，数字，第1索引
KSBH	CHAR(6)	否	是	否	所属科室编号，第2索引
YSMC	CHAR(10)	否	否	否	医生名称
PYZS	CHAR(4)	否	否	否	医生名称的拼音字首
DLKL	CHAR(8)	否	否	否	登录口令
SFZJ	BOOL	否	否	否	是否专家
DLRQ	DATETIME	否	否	是	最后一次登录日期及时间

T_HZXX  (号种信息表)
字段名称	字段类型	主键	索引	可空	备注
HZBH	CHAR(6)	是	是	否	号种编号，数字，第1索引
HZMC	CHAR(12)	否	否	否	号种名称	
PYZS	CHAR(4)	否	否	否	号种名称的拼音字首
KSBH	CHAR(6)	否	是	否	号种所属科室，第2索引
SFZJ	BOOL	否	否	否	是否专家号，即号种类别
GHRS	INT	否	否	否	每日限定的挂号人数
GHFY	DECIMAL(8,2)	否	否	否	挂号费

T_GHXX  (挂号信息表)
字段名称	字段类型	主键	索引	可空	备注
GHBH	CHAR(6)	是	是	否	挂号的顺序编号，数字
HZBH	CHAR(6)	否	是	否	号种编号：可找号种SFZJ
YSBH	CHAR(6)	否	是	否	医生编号
BRBH	CHAR(6)	否	是	否	病人编号	
GHRC	INT	否	是	否	该病人某号种的挂号人次
THBZ	BOOL	否	否	否	退号标志=true为已退号码
GHFY	DECIMAL(8,2)	否	否	否	病人的实际挂号费用
RQSJ	DATETIME	否	否	否	挂号日期时间

为了减少编程工作量，T_KSXX、T_BRXX、T_KSYS、T_HZXX的信息手工录入数据库，每个表至少录入6条记录，所有类型为CHAR(6)的字段数据从“000001”开始，连续编码且中间不得空缺。为病人开发的桌面应用程序要实现的主要功能具体如下：
（1）病人登录：输入自己的病人编号和密码，经验证无误后登录。
（2）病人挂号：病人处于登录状态，选择科室、号种和医生（非专家医生不得挂专家号，专家医生可以挂普通号）；输入缴费金额，计算并显示找零金额后完成挂号。所得挂号的编号从系统竞争获得生成，挂号的顺序编号连续编码不得空缺。
功能（2）的界面如下所示，在光标停在“科室名称”输入栏时，可在输入栏下方弹出下拉列表框，显示所有科室的“科室编号”、“科室名称”和“拼音字首”，此时可通过鼠标点击或输入科室名称的拼音字首两种输入方式获得“科室编号”，用于插入T_GHXX表。注意，采用拼音字首输入时可同时完成下拉列表框的科室过滤，使得下拉列表框中符合条件的科室越来越少，例如，初始为“内一科”和“内二课”。其它输入栏，如“医生姓名”、“号种类别”、“号种名称”也可同时支持两种方式混合输入。
每种号种挂号限定当日人次，挂号人数超过规定数量不得挂号。一个数据一致的程序要保证：挂号总人数等于当日各号种的挂号人次之和，病人的账务应保证开支平衡。已退号码不得用于重新挂号，每个号重的GHRC数据应连续不间断，GHRC从1开始。若病人有预存金额则直接扣除挂号费，此时“交款金额”和“找零金额”处于灰色不可操作状态。

 
为医生开发的桌面应用程序要实现的主要功能具体如下：
（1）医生登录：输入自己的医生编号和密码，经验证无误后登录。
（2）病人列表：医生处于登录状态，显示自己的挂号病人列表，按照挂号编号升序排列。显示结果如下表所示。

挂号编号	病人名称	挂号日期时间	号种类别
000001	章紫衣	2018-12-30  11:52:26	专家号
000003	范冰冰	2018-12-30  11:53:26	普通号
000004	刘德华	2018-12-30  11:54:28	普通号

（3）收入列表：医生处于登录状态，显示所有科室不同医生不同号种起止日期内的收入合计，起始日期不输入时默认为当天零时开始，截止日期至当前时间为止。时间输入和显示结果如下表所示。

起始时间：2018-12-30  00:00:00   截止时间：2018-12-30  12:20:00               
科室名称	医生编号	医生名称	号种类别	挂号人次		收入合计
感染科	000001	李时珍	专家号	24	48
感染科	000001	李时珍	普通号	10	10
内一科	000002	扁鹊	普通号	23	23
保健科	000003	华佗	专家号	10	20

病人应用程序和医生应用程序可采用主窗口加菜单的方式实现。例如，医生应用程序有三个菜单项，分别为“病人列表”、“收入列表”和“退出系统”等。
考虑到客户端应用程序要在多台计算机上运行，而这些机器的时间各不相同，客户端程序每次在启动时需要同数据库服务器校准时间，可以建立一个时间服务程序或者直接取数据库时间校准。建议大家使用MS  SQL数据库开发。
挂号时锁定票号可能导致死锁，为了防止死锁或系统响应变慢，建议大家不要锁死数据库表或者字段。程序编写完成后，同时启动两个挂号程序进行单步调试，以便测试两个病人是否会抢到同一个号、或者有号码不连续或丢号的现象。
系统考核目标：（1）挂号后数据库数据包括挂号时间不会出现不一致或时序颠倒现象，以及挂号人次超过该号种当日限定数量的问题；（2）挂号号码和挂号人次不会出现不连续或丢号问题；（3）病人的开支应平衡，并应和医院的收入平衡；（4）系统界面友好、操作简洁，能支持全键盘操作、全鼠标操作或者混合操作；（5）能支持下拉列表框过滤输入；（6）系统响应迅速，不会出现死锁；（7）统计报表应尽可能不采用多重或者多个循环实现； （8）若采用时间服务器程序校准时间，最好能采用心跳检测机制，显示客户端的上线和下线情况。
思考题：当病人晚上11:59:59秒取得某号种的挂号价格10元，当他确定保存时价格在第2天00:00:00已被调整为20元，在编程时如何保证挂号费用与当天价格相符？
保存的时刻查询数据库，以此时记录为准。

