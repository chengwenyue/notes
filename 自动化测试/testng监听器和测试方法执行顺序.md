
---
Test类

	public class Test2 extends TestBase {
	
		@BeforeClass
		public void beforeClass(){
			LogUtil.info(" Test2 : beforeClass");
		}
		
		@AfterClass
		public void afterClass(){
			LogUtil.info(" Test2 : afterClass");
		}
		
		@Test
		public void test2() throws Exception{
			LogUtil.info(" Test2 : test2");
		}
	
		@Override @BeforeSuite
		public void beforeSuite() {
			LogUtil.info(" Test2 : beforeSuite");
		}
	
		@Override @AfterSuite
		public void afterSuite() {
			LogUtil.info(" Test2 : afterSuite");
		}
	}

---
监听器

	public class AllListener implements IInvokedMethodListener,ITestListener,ISuiteListener{
	
		@Override
		public void onStart(ISuite suite) {
			LogUtil.info("ISuite : onStart");
		}
	
		@Override
		public void onFinish(ISuite suite) {
			LogUtil.info("ISuite : onFinish");
		}
	
		@Override
		public void onTestStart(ITestResult result) {
			LogUtil.info("ITestResult : onTestStart");
			
		}
	
		@Override
		public void onTestSuccess(ITestResult result) {
			LogUtil.info("ITestResult : onTestSuccess");
			
		}
	
		@Override
		public void onTestFailure(ITestResult result) {
			LogUtil.info("ITestResult : onTestFailure");
			
		}
	
		@Override
		public void onTestSkipped(ITestResult result) {
			LogUtil.info("ITestResult : onTestSkipped");
			
		}
	
		@Override
		public void onTestFailedButWithinSuccessPercentage(ITestResult result) {
			LogUtil.info("ITestResult : onTestFailedButWithinSuccessPercentage");
			
		}
	
		@Override
		public void onStart(ITestContext context) {
			LogUtil.info("ITestContext : onStart");
			
		}
	
		@Override
		public void onFinish(ITestContext context) {
			LogUtil.info("ITestContext : onFinish");
			
		}
	
		@Override
		public void beforeInvocation(IInvokedMethod method, ITestResult testResult) {
			LogUtil.info("IInvokedMethod : beforeInvocation");
			
		}
	
		@Override
		public void afterInvocation(IInvokedMethod method, ITestResult testResult) {
			LogUtil.info("IInvokedMethod : afterInvocation");
		}
	
	}

---
log

	
	ISuite : onStart
	IInvokedMethod : beforeInvocation
	 Test2 : beforeSuite
	IInvokedMethod : afterInvocation
	ITestContext : onStart
	IInvokedMethod : beforeInvocation
	 Test2 : beforeClass
	IInvokedMethod : afterInvocation
	ITestResult : onTestStart
	IInvokedMethod : beforeInvocation
	 Test2 : test2
	IInvokedMethod : afterInvocation
	ITestResult : onTestSuccess
	IInvokedMethod : beforeInvocation
	 Test2 : afterClass
	IInvokedMethod : afterInvocation
	PASSED: test2
	
	===============================================
	    Default test
	    Tests run: 1, Failures: 0, Skips: 0
	===============================================
	
	ITestContext : onFinish
	IInvokedMethod : beforeInvocation
	 Test2 : afterSuite
	IInvokedMethod : afterInvocation
	ISuite : onFinish