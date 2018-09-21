## js一些有趣的写法记录

### 写法1

#### 源码：

	(function() {
		var Cookie = function() {
		};
		Cookie.prototype = {
			delCookie : function(name) {
				var exp = new Date();
				exp.setTime(exp.getTime() - 1);
				var cval = this.getCookie(name);
				if (cval != null) {
					document.cookie = name + "=" + cval + ";expires="
							+ exp.toGMTString();
				}
			},
			setCookie : function(name, value) {
				var Days = 30;
				var exp = new Date();
				exp.setTime(exp.getTime() + 30 * 60 * 1000);
				document.cookie = name + "=" + escape(value) + ";expires="
						+ exp.toGMTString();
			},
			getCookie : function(name) {
				var arr, reg = new RegExp("(^| )" + name + "=([^;]*)(;|$)");
				if (arr = document.cookie.match(reg)) {
					return unescape(arr[2]);
				} else {
					return null;
				}
			}
		};
		window.customCookie = new Cookie();
	})();

#### 实现方式

1. 使用自执行函数
2. 在自执行函数内部定义构造函数
3. 在函数的prototype属性上扩展属性。
4. `window.customCookie = new Cookie();` 将JS对象赋值给全局对象。

---


