###使用dtm实现分布式事务

如果我们要进行一个类似于银行跨行转账的业务，转出（TransOut）和转入（TransIn）分别在不同的微服务里

对于跨行转账操作，最简单的做法是，在Try阶段调整余额，在Cancel阶段反向调整余额，Confirm阶段则空操作。这么做带来的问题是，如果A扣款成功，金额转入B失败，最后回滚，把A的余额调整为初始值。在这个过程中如果A发现自己的余额被扣减了，但是收款方B迟迟没有收到余额，那么会对A造成困扰。

更好的做法是，Try阶段冻结A转账的金额，Confirm进行实际的扣款，Cancel进行资金解冻，这样用户在任何一个阶段，看到的数据都是清晰明了的。

下面我们进行一个TCC事务的具体开发


我们首先创建两张表，一张是用户余额表，一张是冻结资金表，建表语句如下：
	
	CREATE TABLE dtm_busi.`user_account` (
	  `id` int(11) AUTO_INCREMENT PRIMARY KEY,
	  `user_id` int(11) not NULL UNIQUE ,
	  `balance` decimal(10,2) NOT NULL DEFAULT '0.00',
	  `create_time` datetime DEFAULT now(),
	  `update_time` datetime DEFAULT now()
	);
	
	CREATE TABLE dtm_busi.`user_account_trading` (
	  `id` int(11) AUTO_INCREMENT PRIMARY KEY,
	  `user_id` int(11) not NULL UNIQUE ,
	  `trading_balance` decimal(10,2) NOT NULL DEFAULT '0.00',
	  `create_time` datetime DEFAULT now(),
	  `update_time` datetime DEFAULT now()
	);

trading表中，trading_balance记录正在交易的金额。

我们先编写核心代码，冻结/解冻资金操作，会检查约束balance+trading_balance >= 0，如果约束不成立，执行失败

	func adjustTrading(uid int, amount int) (interface{}, error) {
	 
	  dbr := sdb.Exec("update dtm_busi.user_account_trading t join dtm_busi.user_account a on t.user_id=a.user_id and t.user_id=? set t.trading_balance=t.trading_balance + ? where a.balance + t.trading_balance + ? >= 0", uid, amount, amount)
	  if dbr.Error == nil && dbr.RowsAffected == 0 { // 如果余额不足，返回错误
	    return nil, fmt.Errorf("update error, balance not enough")
	  }
	  其他情况检查及处理
	}

然后是调整余额

	func adjustBalance(uid int, amount int) (ret interface{}, rerr error) {
	  
	  这里略去进行相关的事务处理，包括开启事务，以及在defer中处理提交或回滚
	  // 将原先冻结的资金记录解冻
	  dbr := db.Exec("update dtm_busi.user_account_trading t join dtm_busi.user_account a on t.user_id=a.user_id and t.user_id=? set t.trading_balance=t.trading_balance + ?", uid, -amount)
	  if dbr.Error == nil && dbr.RowsAffected == 1 { // 解冻成功
	    // 调整金额
	    dbr = db.Exec("update dtm_busi.user_account set balance=balance+? where user_id=?", amount, uid)
	  }
	  其他情况检查及处理
	}

下面我们来编写具体的Try/Confirm/Cancel的处理函数

	RegisterPost(app, "/api/TransInTry", func (c *gin.Context) (interface{}, error) {
	  return adjustTrading(1, reqFrom(c).Amount)
	})
	RegisterPost(app, "/api/TransInConfirm", func TransInConfirm(c *gin.Context) (interface{}, error) {
	  return adjustBalance(1, reqFrom(c).Amount)
	})
	RegisterPost(app, "/api/TransInCancel", func TransInCancel(c *gin.Context) (interface{}, error) {
	  return adjustTrading(1, -reqFrom(c).Amount)
	})
	
	RegisterPost(app, "/api/TransOutTry", func TransOutTry(c *gin.Context) (interface{}, error) {
	  return adjustTrading(2, -reqFrom(c).Amount)
	})
	RegisterPost(app, "/api/TransOutConfirm", func TransInConfirm(c *gin.Context) (interface{}, error) {
	  return adjustBalance(2, -reqFrom(c).Amount)
	})
	RegisterPost(app, "/api/TransOutCancel", func TransInCancel(c *gin.Context) (interface{}, error) {
	  return adjustTrading(2, reqFrom(c).Amount)
	})

到此各个子事务的处理函数已经OK了，然后是开启TCC事务，进行分支调用

	
	_, err := dtmcli.TccGlobalTransaction(DtmServer, func(tcc *dtmcli.Tcc) (rerr error) {
	  // CallBranch 会将事务分支的Confirm/Cancel注册到全局事务上，然后直接调用Try
	  res1, rerr := tcc.CallBranch(&TransReq{Amount: 30}, host+"/api/TransOutTry", host+"/api/TransOutConfirm", host+"/api/TransOutRevert"
	  进行错误检查，以及其他逻辑
	  res2, rerr := tcc.CallBranch(&TransReq{Amount: 30}, host+"/api/TransInTry", host+"/api/TransInConfirm", host+"/api/TransInRevert")
	  进行错误检查，有任何错误，返回错误，回滚交易
	  // 如果没有错误，函数正常返回后，全局事务会提交，TM会调用各个事务分支的Confirm，完成整个事务
	})

至此，一个完整的TCC分布式事务编写完成。

如果您想要完整运行一个成功的示例，那么按照dtm项目的说明搭建好环境之后，运行下面命令运行tcc的例子即可

	go run app/main.go tcc_barrier

