## js学习日常记录

### 在onclick中指定点击方法


方式1

    onclick="javascript:fun()"

方式2

    onclick="fun()"

方式3

	$("#fun").click(function(){  
	     fun();  
	}); 

### jQuery的extend方法
	
Jquery的扩展方法原型是:　

	extend(dest,src1,src2,src3...);

它的含义是将src1,src2,src3...合并到dest中,返回值为合并后的dest,由此可以看出该方法合并后，是修改了dest的结构的。

### esayui记录

[esayui记录](./easyui使用记录.md)
