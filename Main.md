# Nexora开发标准约定
该开发标准由Nexora（前TFS）Daxia制定，用于规范项目开发过程中的各种情况。主要适用于强类型语言，代码多使用java作为标准语言进行示例，也大多同样适用于C#，C，C++等强类型语言。

## 命名标准
- 变量一律使用**全名**，（除**世界公认**的缩写以外）
```java
Thread serverThread; //√
Thread serThr; //X

String num; //√
String number; //√

User admin;//√
User administrator;//√
```

- 禁止**单一字母**，**字母加数字变量**（除短声明周期变量，遍历序号（某些情况下也不能使用字母））
```java
User u1; //X
User administrator; //√

try{
    //...
}catch(Exception e){
    //...
}
//√
```
***
- 禁止**拼音命名**。
```java
String yonghuming;
//X
String userName;
//√
```

***
- **禁止**`Util`类，`Tool`类。类名必须见名知意。
```java
public class Utils{
    public static void playMusic(){
        //...
    }

    public static void receiveMessage(){
        //...
    }
}
//X
```
```java
public class Music{
    public static void playMusic(){
        //...
    }
}

public class ChatRoom{
    public static void receiveMessage(){
        //...
    }
}
//√
```
---

- 除非业务需求等极端特殊情况，禁止在代码中出现脏话，违禁词等。

```java
var fxxxedList = //...
    // X
var handledList = //...
    // √
```

***
- 变量命名必须是有**明确意义的名词**，方法命名必须是有**明确意义的动词**。
```java
class Test{
    public void help(){
        //X
    }

    public void vertifyUser(){
        //√
    }
}
```

- 变量命名，方法命名（如果是java）均使用**驼峰命名法**，即小写字母开头，其他单词开头字母为大写。
- 常量命名，枚举类型命名**全大写**，中间以下划线进行分割。
- 方法命名（如果是C#），类名**帕斯卡命名法**，即单词首字母均大写。
- 包名**全小写**（如果是C#则使用帕斯卡命名法）。

***
## 编写规范
- **以方法最外层缩进为参考，缩进不得超过五层**
```java
public void func(){
    //此处为0层缩进
    System.out.println("Running");
    if(){
        //1
        if(){
            //2
            if(){
                //3
                if(){
                    //4
                    if(){
                        //此处为5层缩进，再缩进即违反规则。
                    }
                }
            }
        }
    }
}
```

***
- 验证过程使用**提前退出**的方法，不要使用无意义的`else`分支。尽量减少缩进。
```java
public Socket connect(String ip, int port){
    if(!Vertifier.isValidIP(ip)){
        System.out.println("Invalid IP");
        return null;
    }else{
        if(!Vertifier.isValidPort(port)){
            System.out.println("Invalid Port");
            return null;
        }else{
            //...
        }
    }
}
//X

public Socket connect(String ip, int port){
    if(!Vertifier.isValidIP(ip)){
        System.out.println("Invalid IP");
        return null;
    }
    if(!Vertifier.isValidPort(port)){
        System.out.println("Invalid Port");
        return null;
    }

    //...
}
//√
```
***
- 涉及到**大量字符串加减**运算，一律使用`StringBuilder`而非`String`。
```java
String a = "";
for(var c : str.toCharArray()){
    a += c;
}
//X

StringBuilder a = new StringBuilder();
for(var c : str.toCharArray()){
    a.append(c);
}
//√
```
***
- 分支存在$>=4$个情况的使用`switch case`而非`if-elseif`（非常量判断除外）
```java
switch(type){
    case A:
    case B:
        //...
}
//√

if(type == E.A){
    //...
}else if(type == E.B){
    //...
}else if(type == E.C){
    //...
}
//X
```
***
- 如果存在`ErrorLog`等错误处理，错误记录，服务器信息类，必须使用相应方法而不是`System.out.println()`。


- `ErrorLog`，`Log`，`Warn`等必须输出信息**类型**，**线程类型，信息时间**，必要情况下需要输出信息源ip，用户名。如果遇到严重的`Exception`异常，需要打印调用堆栈信息。

```txt
[ERROR/SeverThread][00:00:00] User GLJ is trying to kill administrator, which is not allowed.
```
```java
public static void main(String[] args) {
    try{
    }catch (Exception e){
        e.printStackTrace();
    }
}
```
- **禁止**忽略`Exception`，不进行处理或者记录就继续运行，除非这种异常是你手动用来跳出深层调用的。

- **禁止**方法根据传入的`boolean`，`enum`确定基本运行模式，而是使用重载或者命名不同。

```java
public void save(SaveType type){
    if(type == SaveType.JSON){

    }else{

    }
}
//X

public void jsonSave(){
    
}

public void otherSave(){
    //...
}

//√
```
***
- 不允许类中出现过多与类毫不相关的辅助方法，做到类功能的单一化。
```java
public class Server{
    public void saveFile(){
        //...
    }

    public Music getMusic(){
        //...
    }
}
//X

public class Files{
    public void saveFile(){}
}

public class Music{
    public Music getMusic(){
    }
}
//√
```

***
- **提交修改时除特殊情况外禁止保留大段注释掉的代码**。
```java
public void playMusic(){
    Music mus = Music.getMusic();
    mus.play();

    // Music mus = getMusic();
    // MusicPlayer.playMusic(mus);
    // 不知道为什么这个实现不了，烦死了
}
// X
```
***
- 不要保留**不可到达的代码**
```java
public void function(){
    if(){
        //...
    }else{
        //...
    }
    return;

    for(int i = 0; i < 5; i++){
        //...
    }
    //X
}
```
***
- 方法**不得过长**，尽量拆分功能。具体的来说，一般到简单难度的代码不应当超过`50`行，逻辑较为复杂的代码不应当超过`150`行。
***
- 如果重写`equals()`方法，必须重写`hashCode()`方法。

```java
public class SomeObject {
    private int value;
    
    @Override
    public boolean equals(Object other) {
        // ...
    }
    
    // 必须有hashCode方法！！！
    @Override
    public long hashCode() {
        // ...
    }
}
```

`hashCode()`的要求是，`equals`定义的相等条件是`hashCode`相等的充分不必要条件。（即如果两个对象相等，它们的`hashcode`一定相等，两个`hashcode`相等的对象，不要求他们必须为相等，但尽量保证如此）

因为如果不进行重写，依赖`hashcode`的容器如`HashMap`，`HashSet`将工作异常。

***
- 禁止滥用单例模式，**除逻辑上理应只能有一个存在的类。**

```java
public class User{
    private static User instance;
}
//X

public class Server{
    private static Server instance;
}
//√
```
***
- 只需要使用静态方法的类**必须私有化构造方法**。
```java
public class Vertifier{
    private Vertifier(){}
}
//√
```

---

- **禁止使用魔数**（xxx-code），（原则上禁止）**字符串**进行`switch-case`，能尽量使用**枚举类**的就使用枚举类

```java
int code = something.getCode();
switch (code) {
    case 1:
    case 2:
    case 3:
}
// X

String status = something.getStatus();
switch (status) {
    case "ok":
    case "fail":
    case "unknown":
}
// X

Status status = something.getStatus();
switch(status) {
    case STATUS_OK:
    case STATUS_FAIL:
    case STATUS_UNKNOWN:
}
// √
```

---

- 能使用`lombok`进行简化的操作**尽量使用**`lombok`

```java
public class DataClass {
	int data;
    
    public int getData() {
        return data;
    }
    
    
    public void setData(int data) {
    	this.data = data;
    }
}
// X

@Data
public class DataClass {
    
}
// √
```

---

- 异步操作时，能使用线程池的就使用线程池，尽量不要使用`new Thread()`，如果使用`@Async`，**必须指定线程池**，不可使用默认线程池。

```java
public class SomeService {
    @Async
    public void load() {
        
    }
    // X
    
    public void someAction() {
        new Thread(() -> {/*...*/})
    }
    // X

	@Async("SomePool")
    public void load() {
	
    }
    // √
}
```

---

- 除使用`@Autowired`进行自动注入的类之外，凡是要使用手动调用构造函数的类，其构造函数参数**均不得超过5个**

```java
public class MyClass {
    public MyClass(int arg1, int arg2, int arg3, int arg4, int arg5, int arg6 /*...*/)
	// ×
        
    public MyClass(MyClassConfig config) {
        
    }
    // √
}
```

---

- 一些临时使用的类，**能不将其定义暴露在外部就不暴露**。

```java
public class MyIterableClass implements Iterable {
    @Override
	public Iterator iterator() {
        return new MyClassIterator(this);
    }
}

public class MyClassIterator implements Iterator {
    // ...
}

// ×
// ====

public class MyIterableClass implements Iterable {
    @Override
	public Iterator iterator() {
        return new MyClassIterator(this);
    }

	private class MyClassIterator implements Iterator {
    	// ...
	}
}
// √
```

---

- 对于强类型语言，不允许将其作为弱类型使用，例如对外返回数据类时，应当建立临时DTO而非直接使用`JSONObject`，或者`Map<String, Object>`等等非强类型限制的方式进行处理。

```java
public String someMethod() {
    // ...
    return jsonObject.toString();
    // X 完全错误，不允许这样写，容易造成难以调试的问题
}

public SomeDTO someMethod() {
    // ...
    return someDTO;
}
// √
```

如果是C#，则禁止使用`dynamic`作为返回的类型。

---

- 如果有其他替代方案，就禁止任何使用反射的方式来对类的方法进行间接调用。

---

- 在多模块项目下，对同一行为的实现包应当统一，避免组内不同人员之间的高理解成本。不得出现这个模块使用JPA，而那一个模块使用MyBatis的情况，对使用什么包没有明确的限制，但至少保持统一。

---

- 对数据库的查询，远程调用等昂贵耗时操作应当尽量少使用，不得出现毫无意义的重复调用
