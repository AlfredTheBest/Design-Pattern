#策略模式

##应用场景
###一个商场收银软件，营业员根据客户所购买的商品的单价和数量，向客户收费
用两个文本框来输入单价和数量，一个确定按钮来算出每种商品的费用，用个列表框来记录商品的清单，一个标签来记录总计，一个重置按钮来重新开始。
     
     double total = 0.0d;
     private void btn0k_Click(object sender, EventArgs e)
     {
     	double totalPrices = Convert.ToDouble(txtPrice.Text) * Convert.ToDouble(txtNum.Text);
     	total = total + totalPrices;
     	IbxList.Items.Add("单价：" +txtPrice.Text + "数量：" ＋ txtNum.Text + "合计："+ totalPrices.ToString());
     	IblResult.Text = total.ToString();
     }
     
     
###比如遇到节假日 增加打折
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
     
##简单工厂实现