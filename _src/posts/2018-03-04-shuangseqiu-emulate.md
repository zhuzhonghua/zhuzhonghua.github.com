    Title: 用Python模拟双色球抽奖
    Date: 2018-03-04T00:00:22
    Tags: Python, 双色球, 大乐透, 彩票

1. 抓取历史开奖的双色球号码  
2. 模拟生成N注彩票，并且过滤掉历史上开奖的号  
3. 把模拟出来的彩票写入sqlite数据  

用Python3写  
需要用到几个python库  
1. requests (pip3 install requests) 网络库  
2. bs4 (pip3 install bs4) BeautifulSoup，方便分析抓取内容  
3. sqlite3 (pip3 install sqlite3) 数据库  

#获取历史所有期号  


	def get_all_option():
	  # 彩票获取源
	  url="http://kaijiang.500.com/shtml/ssq/18016.shtml"
	  req = requests.get(url)
		
	  # 找到对应的字段	
	  soup = BeautifulSoup(req.text, 'html.parser')
	  linkids = re.compile("link[0-9]+")
	  all_ids = soup.find(attrs={'class':'iSelectBox'})
			  
	  # 把所有期号写入ssq_all_ids.txt 这个文件，一行一个期号
	  all_links = all_ids.find_all('a')
	  f = open("ssq_all_ids.txt", "w")
	  for link_id in all_links:
	    f.write(link_id.string+'\n')
	  f.close()
	  
#获取特定期号的号码


	def get_ball(url, id):
	  # 抓取id期号的号码
	  req = requests.get(url, timeout=10)
	  soup = BeautifulSoup(req.text, 'html.parser')
	  balls = soup.find(attrs={'class':'ball_box01'})
	
	  # 找到红号字段
	  red_balls = []
	  for red in balls.find_all(attrs={'class':'ball_red'}):
	    red_balls.append(red.string)
	
	  # 找到蓝号字段
	  blue_balls = []
	  for blue in balls.find_all(attrs={'class':'ball_blue'}):
	    blue_balls.append(blue.string)
	
	  # 把期号，红号，蓝号都写入 ssq_hit_ids.txt
	  # 期号|红号1,红号2,红号3,红号4,红号5,红号6|蓝号
	  # 蓝号只有一个，不过可以扩展到大乐透，多个蓝号
	  f = open('ssq_hit_ids.txt', 'a+')
	  f.write(id+"|"+",".join(red_balls)+"|"+",".join(blue_balls)+'\n')
	  f.close()
	  print("get  "+id)
	  
	  
#更新期号  
以后再使用时，只需要抓取缺失的期号  


	def update_ids():
	  # 拿到所有期号
	  url="http://kaijiang.500.com/shtml/ssq/18016.shtml"
	  req = requests.get(url)
	
	  soup = BeautifulSoup(req.text, 'html.parser')
	  linkids = re.compile("link[0-9]+")
	  all_ids = soup.find(attrs={'class':'iSelectBox'})
	  all_links = all_ids.find_all('a')
	
	  new_ids = []
	  for link_id in all_links:
	    new_ids.append(link_id.string)
	
	  # 拿到已经抓取的期号
	  old_ids = []
	  with open("ssq_all_ids.txt", 'r') as f:
	    old_ids = f.readlines()
	  old_ids_new = [one.replace('\n','') for one in old_ids]
	  # 转换成set，方便查找
	  old_ids_set = set(old_ids_new)
	
	  for one in new_ids:
	    # 如果没抓取过，那么抓取
	    if not one in old_ids_set:
	      url="http://kaijiang.500.com/shtml/ssq/"+one.replace('\n','')+".shtml"
	      try:
	        get_ball(url, one)
	        with open("ssq_all_ids.txt", 'a') as f:
	          f.write(one+"\n")
	        print("update  id "+one)
	      except Exception as e:
	        print(e)
	        with open('ssq_err_ids.txt', 'a+') as f:
	          f.write(one+'\n')
			  
#初始抓取  


	def main():
	  # 把所有期号读进来
	  all_ids = []
	  with open('ssq_all_ids.txt', 'r') as f:
	    all_ids = f.readlines()

	  # 把每一期的开奖号码抓下来
	  for one_id in all_ids:
	    one_id = one_id.replace('\n','')
	    url="http://kaijiang.500.com/shtml/ssq/"+one_id+".shtml"
	    try:
	      get_ball(url, one_id)
	      time.sleep(.100)
	    except Exception as e:
	      print(e)
	      # 做异常判断，防止漏抓取内容
	      f = open('ssq_err_ids.txt', 'a+')
	      f.write(one_id+'\n')
	      f.close()
			  
			  
现在就可以运行  


	get_all_option()
	main()
	
抓取所有历史开奖号码了  

接下来可以模拟开奖号码，写数据库了  

#数据库脚本


	# 删除库的脚本
	sql_drop_guess = '''
	drop table guess;
	'''
	# 创建库的脚本
	# id 是主键，自增
	# nxx是对应模拟的号码，随机出2，那么n02列就有值，可以记录1，或者直接记录2
	# 没有模拟出来的号，那对应的列就是默认值0
	# count 是对应模拟出的号的个数，可能同一号码，会模拟出多次
	# 就好像全国2亿注彩票，可能还有人买重了
	# 为了优化，还建立了唯一索引
	# 这里只是模拟红球，因为篮球只有16个号，刨除上一期开的号，那就是只有15个号
	# 如果觉得哪一组红球比较有戏，可以拖15个篮球，所以随机模拟时，不考虑篮球
	sql_create_guess = '''
	CREATE TABLE guess (
	  id INTEGER primary key AUTOINCREMENT,
	  n01 int default 0, n02 int default 0, n03 int default 0,
	  n04 int default 0, n05 int default 0, n06 int default 0,
	  n07 int default 0, n08 int default 0, n09 int default 0,
	  n10 int default 0, n11 int default 0, n12 int default 0,
	  n13 int default 0, n14 int default 0, n15 int default 0,
	  n16 int default 0, n17 int default 0, n18 int default 0,
	  n19 int default 0, n20 int default 0, n21 int default 0,
	  n22 int default 0, n23 int default 0, n24 int default 0,
	  n25 int default 0, n26 int default 0, n27 int default 0,
	  n28 int default 0, n29 int default 0, n30 int default 0,
	  n31 int default 0, n32 int default 0, n33 int default 0,
	  count int,
	  unique (n01,n02,n03,n04,n05,n06,n07,n08,n09,n10,
	          n11,n12,n13,n14,n15,n16,n17,n18,n19,n20,
	          n21,n22,n23,n24,n25,n26,n27,n28,n29,n30,n31,n32,n33)
	);
	'''

#删库和建库代码


	def drop_guess_table():
	  # 连接数据库文件
	  conn = sqlite3.connect(dbname)
	  # 执行脚本
	  c = conn.cursor()
	  c.execute(sql_drop_guess)
	  conn.commit()
	  # 关闭数据库文件
	  conn.close()
	  print('drop guess success')
	
	def create_guess_db():
	  # 连接数据库文件
	  conn = sqlite3.connect(dbname)
	  c = conn.cursor()
	  # 执行脚本
	  c.execute(sql_create_guess)
	  conn.commit()
	  # 关闭数据库文件
	  conn.close()
	  print('create db success')
	  
dbname 会定义成一个全局变量  
双色球每周会开3期，所以每天一个数据库文件，就可以了

#把历史开奖号读进来


	def get_all_history():
	  # 读取历史开奖号码文件
	  lines = []
	  with open('ssq_hit_ids.txt') as f:
	    lines = f.readlines()
	
	  all_red=set()
	  for line in lines:
	    if len(line) <= 0:
	      continue
	    # 摘取红号  
	    red_balls = line.split('|')[1].split(',')
	    # 红号排序，并组织成"红号1,红号2,红号3,红号4,红号5,红号6"的字符串放入set中，方便查找
	    int_red_balls = []
	    for one in red_balls:
	      int_red_balls.append(int(one))
	    int_red_balls.sort()
	    str_red_balls=[str(one) for one in int_red_balls]
	    all_red.add(','.join(str_red_balls))
	
	  return all_red
	  
#生成一组红号  


	def gen_one():
	  # 33个红球
	  # 这里可以优化，比如想杀号，定胆的情况下
	  all_red_balls = list(range(1,33+1))
	  gen_red_balls = []
	  # 把所有可能的号随机打乱
	  # 取一个，从列表里删除一个，防止取到重复的红号
	  index = random.randrange(0, len(all_red_balls))
	  gen_red_balls.append(all_red_balls.pop(index))
	
	  index = random.randrange(0, len(all_red_balls))
	  gen_red_balls.append(all_red_balls.pop(index))
	
	  index = random.randrange(0, len(all_red_balls))
	  gen_red_balls.append(all_red_balls.pop(index))
	
	  index = random.randrange(0, len(all_red_balls))
	  gen_red_balls.append(all_red_balls.pop(index))
	
	  index = random.randrange(0, len(all_red_balls))
	  gen_red_balls.append(all_red_balls.pop(index))
	
	  index = random.randrange(0, len(all_red_balls))
	  gen_red_balls.append(all_red_balls.pop(index))
	
	  gen_red_balls.sort()
	  return gen_red_balls


#生成N注，并过滤历史开奖号  


	def gen():
	  # 拿到历史开奖号
	  red_history = get_all_history()
	  all_red_balls = {}
	  # run_count是指定的模拟多少注	  
	  global run_count
	  while run_count > 0:
	    run_count -= 1
	    in_history = True
	    while in_history:
	      # 生成一组，然后构造成",”连接的字符串，然后过滤历史号
	      red_balls = gen_one()
	      str_red_balls = [str(one) for one in red_balls]
	      one_red = ",".join(str_red_balls)
	      if not one_red in red_history:
	        # 同一号码，累计+1
	        all_red_balls[one_red] = all_red_balls.get(one_red, 0) + 1
	        in_history = False
	  print("gen all balls")
	  return all_red_balls
	  
#准备写入数据库  

数据库列辅助函数  


	def get_ball_col(ball):
	  if int(ball) < 10:
	    return 'n0'+ball
	  else:
	    return 'n'+ball

#动态构造插入和更新数据库SQL  


	def get_sql_insert(ored, count):
	  # ored 红球数组
	  # count 对应红球重复的个数
	  col1 = get_ball_col(ored[0])
	  col2 = get_ball_col(ored[1])
	  col3 = get_ball_col(ored[2])
	  col4 = get_ball_col(ored[3])
	  col5 = get_ball_col(ored[4])
	  col6 = get_ball_col(ored[5])
	  sql = 'insert into guess('+col1+','+col2+','+col3+','+col4+','+col5+','+col6+',count) values('+ored[0]+','+ored[1]+','+ored[2]+','+ored[\
	3]+','+ored[4]+','+ored[5]+','+str(count)+')'
	  return sql
	
	def get_sql_update(ored, count):
	  # ored 红球数组
	  # count 对应红球重复的个数
	  col1 = get_ball_col(ored[0])
	  col2 = get_ball_col(ored[1])
	  col3 = get_ball_col(ored[2])
	  col4 = get_ball_col(ored[3])
	  col5 = get_ball_col(ored[4])
	  col6 = get_ball_col(ored[5])
	  sql = 'update guess set count=count+%s where '+col1+'=%s and '+col2+'=%s and '+col3+'=%s and '+col4+'=%s and '+col5+'=%s and '+col6+'=%s\
	'
	  return sql % (count, ored[0], ored[1], ored[2], ored[3], ored[4], ored[5])
	  
#写入数据库  


	def update_all_red_balls(all_red_balls):
	  # all_red_balls 所有模拟出的红球
	  conn = sqlite3.connect(dbname)
	  c = conn.cursor()
	  for k in all_red_balls:
	    # 单组红号分拆成数组
	    red_balls = k.split(',')
	    count = all_red_balls[k]
	    # 如果插入失败，则尝试更新count
	    try:
	      sql = get_sql_insert(red_balls, count)
	      c.execute(sql)
	    except Exception:
	      sql = get_sql_update(red_balls, count)
	      c.execute(sql)
	  conn.commit()
	  conn.close()
	  
还有一种做法，每生成一组红号就写入数据库，通过try catch来插入或者更新  
这种方式，生成数量小的话，还可以接受，比如10万注，当生成上亿注时，这个效率就不可接受了  
第一版就是每生成一注就写数据库，跑1亿注，结果跑了48小时都没有跑完  
所以优化成现在这样，先把所有期望生成的彩票都生成，然后一次都写入数据库，效率提升了N倍  

#运行  


	dbname = "ssq-2018-3-1.db"
	run_count = 100000000
	
可以在每次运行之前先跑一遍  


	update_ids()


更新历史开奖号  
每次新模拟，需要先创建数据库文件  


	create_guess_db()
	
如果操作失误，可以先删除数据库  


	drop_guess_table()
	
或者修改 dbname，再创建 create_guess_db()  


	begin = datetime.datetime.now()
	update_all_red_balls(gen())
	end = datetime.datetime.now()
	
	print((end-begin).total_seconds())
	
记录开始时间，运行，然后记录结束时间，看看N注彩票运行了多久  

JUST FOR FUN!
