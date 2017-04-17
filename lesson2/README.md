# 策略模式

## 应用场景
### 一个商场收银软件，营业员根据客户所购买的商品的单价和数量，向客户收费
用两个文本框来输入单价和数量，一个确定按钮来算出每种商品的费用，用个列表框来记录商品的清单，一个标签来记录总计，一个重置按钮来重新开始。
     
     double total = 0.0d;
     private void btn0k_Click(object sender, EventArgs e)
     {
     	double totalPrices = Convert.ToDouble(txtPrice.Text) * Convert.ToDouble(txtNum.Text);
     	total = total + totalPrices;
     	IbxList.Items.Add("单价：" +txtPrice.Text + "数量：" ＋ txtNum.Text + "合计："+ totalPrices.ToString());
     	IblResult.Text = total.ToString();
     }
     
     
### 比如遇到节假日 增加打折
     double total = 0.0d;
     
     private void Form_Load(object sender, EventArgs e)
     {
     	cbxType.Items.AddRange(new object[] {"正常收费","打八折","打五折"});
     	cbxType.SlectedIndex = 0;
     }
     
     private void btn0k_Click(object sender, EventArgs e)
     {
     double totalPrices = 0.0d;
     switch(cbxType.Selectedindex)
     {
     	case 0:
     		     totalPrices = Convert.ToDouble(txtPrice.Text) * Convert.ToDouble(txtNum.Text);
     	case 1:
     		     totalPrices = Convert.ToDouble(txtPrice.Text) * Convert.ToDouble(txtNum.Text)*0.8;
     	case 2:
     		     totalPrices = Convert.ToDouble(txtPrice.Text) * Convert.ToDouble(txtNum.Text)*0.5;
     }
		total = total + totalPrices;
     	IbxList.Items.Add("单价：" +txtPrice.Text + "数量：" ＋ txtNum.Text + "合计："+ totalPrices.ToString());
     	IblResult.Text = total.ToString();
     }
     
## 简单工厂实现

面对对象的编程，并不是类越多越好，类的划分是为了封装，但是分装的基础是抽象，具有相同属性和功能的对象的抽象集合才是类。打一折和打九折只是形式的不同，抽象分析出来，所有的打折算法都是一样的，所以打折算法应该是一个类。
    
    //现金收费抽象类
    abstract class CashSuper
    {
    	public abstract double acceptCash(double money);
    }
    
    //正常收费子类
    class CashNormal: CashSuper
    {
    	public override double acceptCash(double money)
    	{
    		return money;
    	}
    }
    
    //打折收费子类
    class CashRebate: CashSuper
    {
    	private double moneyRebate = 1d;
    	public Cash Rebebate(string moneyRebate)
    	{
    		this.moneyRebate = double.Parse(moneyRebate);
    	}
    	publci override double acceptCash(double money)
    	{
    		return money * moneyRebate;
    	}
    }
    
    //返利收费子类
    class cashReturn: CashSuper
    {
    	private double moneyCondition = 0.0d;
    	private double moneyreturn = 0.0d;
    	public CashReturn(string moneyCondition, string moneyReturn)
    	{
    		this.moneyCondition = double.Parse(moneyCondtion);
    		this.moneyReturn = double.Parse(moneyReturn);
    	}
    	
    	public override double acceptCash(double money)
    	{
    		double result = money;
    		if(money >= moneyCondition)
    		{
    			result = money - Math.Floor(money / moneyCondition) * moneyReturn;
    		}
    			return result;
    	}
    	
    }
    
    //现金收费工厂类
    class CashFactory
    {
    	public static CashSuper createCashAccept(string type)
    	{
    		CashSuper cs = null;
    		switch (type)
    		{
    			case:"正常收费":
    				cs = new CashNormal();
    				break;
    			case:"满300返100":
    				CashReturn cr1 = new CashReturn("300","100");
    				cs = cr1;
    				break;
    			case:"打8折":
    				CashRebate cr2 = new CashRebate("0,8");
    				cs = cr2;
    				break;
    		}
    		return cs;
    	}
    }
    
    //客户端程序主要部分
    double total = 0.0d;
    private void btn0k_clik(object sneder, EventArgs e)
    {
    	cashSuper csuper = CashFactory.createCashAccept(cbxType.SelectedItem.ToString());
    	double totalPrices = 0d;
    	totalPrices = csuper.acceptCash(Convert.ToDouble(txtPrice.txt)*Convert.ToDouble(txtNum.Text));
    	total = total + totalPrices;
    	IbxList.Items.Add("单价：" +txtPrice.Text + "数量：" ＋ txtNum.Text + "合计："+ totalPrices.ToString());
     	IblResult.Text = total.ToString();
    	
    }
    
简单工厂模式虽然能解决这个问题，但是这个模式知识解决对象的创建问题，而且由于工厂本身包括了所有的收费方式，商场是可能经常性地更改打折额度和返利额度，每次维护或扩展收费方式都要改动这个工厂，以致代码需重新编译部署，这真的是很糟糕的处理方式，所以用它不是做好的办法，。面对算法的时常变动应该有更好的办法。
    
## 策略模式
策略模式定义了算法家族，分别封装起来，让它们之间可以互相替换，此模式让算法的变化，不会影响到使用算法的客户。
	
    //CashContext类	
    class CashContext
    {
    	private CashSuper cs;
    	public CashContext(CashSuper csuper)
    	{
    		this.cs = csuper;
    	}
    	
    	public double GetResult(double money)
    	{
    		return cs.accptCash(money);
    	}
    	
    }
    
    //客户端主要代码
    double total = 0.0d;
    private void btn0k_Click(object sender, EventArgs e)
    {
    	CashContext cc = null;
    	switch (cbxType.selectedItem.ToString())
    	{
    		case: "正常收费":
    			cc = new CashContext(new CashNormal());
    			break;
    		case: "满300返100":
    			cc = new CashContext(new CashReturn ("300","100"));
    			break;
    		case: "打8折":
    			cc = new CashContext(new CashRebate("0.8"));
    			break;
    	}
    	double totalPrices = 0d;
    	totalPrices = cc.GetResult(convert.ToDouble(txtPrice.Text)*convert.ToDouble(txtNum.Text));
    	total = total + totalPrices;
    	IbxList.Items.Add("单价：" +txtPrice.Text + "数量：" ＋ txtNum.Text + "合计："+ totalPrices.ToString());
     	IblResult.Text = total.ToString();
    	
    }
    
    
虽然策略模式写出来了，但是不应该让客户端去判断用哪一个算法。

## 策略与简单工厂结合

    class CashContext
    {
    	CashSuper cs  = null;
    	
    	public CashContext(String type)
    	{
    		switch(type)
    		{
    			case "正常收费":
    				CashNormal cs0 = new CashNormal();
    				cs = cs0;
    				break;
    			case "满300返100":
    				CashReturn cr1 = new CashReturn("300","100");
    				cs = cr1;
    				break;
    			case "打8折"：
    				CashReturn cr2 = new CashRebate("0.8");
    				cs = cr2;
    				break;
    		}
    		public double GetResult(double money)
    		{
    			return cs.acceptCash(money);
    		}
    	}
    }
    
    //客户端代码
    double total = 0.0d;
    private void btn0k_Click(object sender, EvnetArgs e)
    {
    	CashContext csuper = new CashContext(cbxType.slectedItem.ToString());
    	double totalPrices = 0d;
    	totalPrices = csuper.GetResult(Convert.ToDouble(txtPrice.Text)*Convert.ToDouble(txtNum.Text));
    	total = total + totalPrices;
    	IbxList.Items.Add("单价：" +txtPrice.Text + "数量：" ＋ txtNum.Text + "合计："+ totalPrices.ToString());
     	IblResult.Text = total.ToString();	
    }
    
    //简单工厂模式的用法
    CashSuper csuper = CashFactory.CreateCashAccept(cbxType.SelectedItem.ToString());
    =csuper.GetResult;
    
    //策略模式与简单工厂结合的用法
    CashContext csuper = new (CashContext(cbxType.SelectedItem.ToString()));
    =csuper.GetResult;
简单工厂模式让客户端认识两个类， CashSuper和CashFactory,而策略模式与简单工厂结合的用法，客户端只需要认识一个类CashContext就可以了。耦合更加降低。
    
## 策略模式解析
策略模式是一种定义一系列算法的方法，从概念上来看，所有这些算法完成的都是相通的工作，只是实现不同，它可以以相同的方式调用所有的算法，减少各种算法类和使用算法类之间的耦合。
策略模式就是用来封装算法的，但是实践中，我们发现可以用它来分装几乎任何类型的规则，只要在分析过程中听到需要在不同的时间应用不同的业务规则，就可以考虑使用策略模式处理这种变化的可能性。
    