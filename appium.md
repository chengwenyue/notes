##appium

- 1. appium 和 seleium 共同使用时会出现类型转换错误，解决方法是保证appium和seleium的maven依赖一致

----------

- 2. 最新版本的appium(当前版本1.7.2)不支持name定位元素，在appium安装路径下，修改appium-desktop\resources\app\node_modules\appium\node_modules\appium-android-driver\build\lib\driver.js文件79行如下：
```
this.locatorStrategies = ['xpath', 'id', 'class name', 'accessibility id', '-android uiautomator', 'name'];
```

----------

- 3. appium移动端自动化跑起来比web端selenium慢，还不稳定，受网络、环境、终端设备等各种限制，再所难免，这时候**等待**就很重要；因为不稳定，经常会出错，同样为了准确定位问题，打好logs也尤为重要。

---

- 4.findElement当寻找一个不存在的元素是会报错，而不是返回null。

	源码如下：

		public WebElement findElement(SearchContext context) {
			List<WebElement> allElements = findElements(context);
			if (allElements == null || allElements.isEmpty())
				throw new NoSuchElementException("Cannot locate an element using " + toString());
			return allElements.get(0);
		}
  	


- 5.adb启动报错

		adb server version (31) doesn't match this client (39); killing...
		could not read ok from ADB Server
	原因：adb 5037端口被占用（常被360占用！！）

	cmd输入：netstat -aon|findstr 5037，然后在任务管理器中干掉对应的进程，重启adb即可

- 6.appium的UiAutomator2-driver可以捕获到Toast，但UiAutomator2只支持Android5.0+
	
		[UiAutomator2] Error: UIAutomation2 is only supported since Android 5.0 (Lollipop). 
		You could still use other supported backends in order to automate older Android versions.

- 7 appium-chromedriver路径

		.\appium-desktop\resources\app\node_modules\appium\node_modules\appium-chromedriver\chromedriver\win

- 8 多设备并发测试出现的问题：Encountered internal error running command: Error: Android bootstrap socket crashed: Error: This socket has been ended by the other party ，参考[https://github.com/appium/appium/issues/8513](https://github.com/appium/appium/issues/8513)


- 9 node 启动appium  
		
		在路径appium-desktop\resources\app\node_modules\appium\build\lib下执行下面命令

		node main.js --session-override -a 0.0.0.0 -p 4723
- 10 UIAutomator2驱动不支持By.name查找

- 11 部分手机会弹出权限获取弹框，需要在打开应用的时候进行处理

- 12 appium会在C:\Users\31010\AppData\Local\Temp产生非常大的日志文件 文件大小能达到7-8G
	
