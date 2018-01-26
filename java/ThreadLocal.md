### ThreadLocal

```java
package com.test;

public class ThreadLocalTest {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		Thread thread1 = new Thread(new Task());
		Thread thread2 = new Thread(new Task());
		thread1.start();
		thread2.start();
	}
}

class Task implements Runnable {
	
	@Override
	public void run() {
		
		PortalSessionService session = new PortalSessionService();
		
//		PortalSessionService.init();
//		PortalSessionService session = PortalSessionService.get();
		session.setName(Thread.currentThread().getName());
		session.setAge(System.currentTimeMillis());
		System.out.println(session.toString());
	}
	
}

class PortalSessionService {
	private String name;
	
	private Long age;
	
	private static ThreadLocal<PortalSessionService> threadLocal = new ThreadLocal<PortalSessionService>();
	
	public PortalSessionService() {
	}
	
	public static void init() {
		PortalSessionService service = get();
		if (service == null) {
			service = new PortalSessionService();
			threadLocal.set(service);
		} 
	}
	
	public static PortalSessionService get() {
		return threadLocal.get();
	}
	
	public static void remove() {
		threadLocal.remove();
	}
	
	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public Long getAge() {
		return age;
	}

	public void setAge(Long age) {
		this.age = age;
	}

	@Override
	public String toString() {
		return "PortalSessionService [name=" + name + ", age=" + age + "]";
	}
	
}
```

### 参考
* [【Java 并发】详解 ThreadLocal](https://www.cnblogs.com/zhangjk1993/archive/2017/03/29/6641745.html)
