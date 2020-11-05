# Getclassfromstaticmethod


```java

//如果再抽象类内使用,只能拿到抽象类的类名,不能拿到继承抽象类,然后调用静态方法的类名
//方案1 10万次 30ms
		private static final Class getCurrentClass(){

			return new Object(){
				public Class getClassForStatic(){
					return this.getClass();
				}
			}.getClassForStatic();
		}

		//方案2 null exception 风险 10万次 220ms
		private static final String getCurrentClass1(){

			return new SecurityManager(){
				public String getClassName(){
					return getClassContext()[1].getName();
				}
			}.getClassName();
		}

		//方案3 10万次 955ms
		private static final String getCurrentClass2(){

			return new Throwable().getStackTrace()[1].getClassName();
		}

		//方案4 10万次 30ms
		private static final String getCurrentClass3(){

			return new Object(){
				public String getClassName(){
					return this.getClass().getName().substring(0, this.getClass().getName().lastIndexOf('$'));
				}
			}.getClassName();
		}
```

