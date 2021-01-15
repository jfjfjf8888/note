# Note
Google搜索笔记

# 一  前言
 　　ContentProvider是不同应用程序之间进行数据交换的标准API，ContentProvide以Uri的形式对外提供数据，允许其他应用访问和修改数据；  其他应用使用ContentResolve根据Uri进行访问操作指定的数据。 

　　虽然使用其他方法也可以对外共享数 据，但数据访问方式会因数据存储的方式而不同，  如：采用文件方式对外共享数据，需要进行文件操作读写数据；
  采用sharedpreferences共享数 据，需要使用sharedpreferences API读写数据。而使用**ContentProvider共享数据的好处是统一了数据访问方式**。
  Android内置的许多数据都是使用ContentProvider形式，供开发者调用的(如视频，音频，图片，通讯录等)。
 
　　使用ContentProvider我们经常会用到三个类：ContentProvider、Uri、ContentResolver；接下来逐一介绍这三个类。
  
# 二   ContentProvider(内容提供者)类
 ## 1 使用方法
### （1）定义自己的ContentProvider类，该类需要继续Android提供的ContentProvider基类。
### （2）ContentProvider是Android中的四大组件之一，所以需要在AndroidManifest.xml文件中进行注册。注册的时候，与注册Activity类似；
注册代码如下：
```java
<provider
    android:authorities="com.lzb.provide.myContentProvide"
    android:name=".MyContentProvide"
    android:exported="true">
</provider>
```

说明：
　　属性authorities是Uri标识，ContentProvide就是以这个Uri的形式对外提供数据，ContentResolve也是根据该Uri进行访问操作该应用的数据的，可以理解为网站的域名；
　　属性exported指定是否对外暴露数据，只有为true时，其他应用才可以访问该应用的数据；
　　属性name指定ContentProvide实现类的类名；
### （3）注册成功后就是实现ContentProvide的抽象方法，其实就是实现对数据的CRUD（create，retrieve，update，delete）操作方法；方法如下：
```java
public boolean onCreate()
```
说明：该方法在ContentProvider创建后会被调用，当其他应用程序第一次访问ContentProvide时，该ContentProvider会被创建出来，并立即回调该onCreate方法
```java
public Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder)
```
说明：根据Uri查询出select条件所匹配的全部记录，projection表示一个列名列表，表明只选择指定的数据列
```java
public String getType(Uri uri)
```
说明：返回 当前Uri所代表的数据的MIME类型。如果该Uri对应数据可能包括多条记录，那么MIME类型字符串应该以 vnd.android.cursor.dir/开头；如果该Uri对应的数据只包含一条记录，那么返回MIME类型字符串应该以vnd.android.cursor.item/开头。
```java
public Uri insert(Uri uri, ContentValues values)
```
说明：根据该Uri插入values对应的数据
```java
public int delete(Uri uri, String selection, String[] selectionArgs)
```
说明：根据Uri，删除selection条件匹配全部记录。
```java
public int update(Uri uri, ContentValues values, String selection, String[] selectionArgs)
```
说明：根据Uri，更新selection条件匹配的记录。

# 三 Uri类

在介绍Android的Uri之前我们先看一个网站的Uri如下：
　　https://www.oracle.com/index.html

其规则分为三部分如下：
* https：协议部分，通过https协议来访问该网站，这个是固定的；
* www.oracle.com：域名部分，要访问指定网站，这个也是固定的；
* index.html：网络资源部分，访问者可以访问不同的资源，这个是动态的；

而ContentProvide的Uri与此类似，例如上述注册的ContentProvide，要访问该应用的数据的Uri可以是这样的：
```
content://com.lzb.provide.myContentProvide/students
```

其规则分为三部分如下：
* content://：协议部分，表示ContentProvider使用的协议，这个是固定的；
* com.lzb.provide.myContentProvide：URI 的标识，也是属性authorities定义的部分，系统就是通过这个部分找到要操作那个ContentProvide，这个也是固定的。为了保证URI标识的唯一性，它一般是一个完整的、小写的类名（包.小写的类名）；
* students：资源部分（资源所在的路径），访问者可以访问不同的资源，这个是动态的；如果想要访问具体那行数据，可以在后面加上该行的ID，例如上述要该路径下的第1条记录，那么Uri可以写为
```
content://com.lzb.provide.myContentProvide/students/1
```
## 【知识扩展】
网站的Uri都有个默认的端口8080，其实我也可以给ContentProvider添加端口，其规则如下：
```java
content://com.example.project:200/folder/subfolder/etc
```
但是要注意在注册ContentProvide、访问ContentProvide时也要添加上该端口号，例如加上端口号200时，访问该ContentProvide的Uri如下：
```
content://com.lzb.provide.myContentProvide:200/students
```

# 四  ContentResolver类
通过调用Content的 getContentResolver() 方法获取 ContentResolver对象实例，其实ContentResolver的作用类似于HttpClient，获取对象后就可以根据Uri对应用的数据进行CRUD操作了。ContentResolver有如下方法：
```java
public Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder)
```
说明：根据Uri查询出select条件所匹配的全部记录，projection表示一个列名列表，表明只选择指定的数据列

```java
public Uri insert(Uri uri, ContentValues values)
```java
说明：根据该Uri插入values对应的数据

```java
public int delete(Uri uri, String selection, String[] selectionArgs)
```
说明：根据Uri，删除selection条件匹配全部记录。

```java
public int update(Uri uri, ContentValues values, String selection, String[] selectionArgs)
```
说明：根据Uri，更新selection条件匹配的记录。
## 【知识扩展】
那么ContentProvider、Uri、ContentResolver之间的关系是如何的呢？如下关系图：
![avatar](https://img-blog.csdn.net/20171021143953945?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbHUxMDI0MTg4MzE1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

说明：从上图可以看出通过Uri标识，ContentProvider间接调用了ContentResolver的CRUD方法。
A应用调用ContentResolver的insert方法，其实是调用了B应用Uri对应的ContentProvider的insert方法；
A应用调用ContentResolver的update方法，其实是调用了B应用Uri对应的ContentProvider的update方法；
A应用调用ContentResolver的delete方法，其实是调用了B应用Uri对应的ContentProvider的delete方法；
A应用调用ContentResolver的query方法，其实是调用了B应用Uri对应的ContentProvider的query方法；
# 五 UriMatcher类
该类主要是是对传入的 Uri 进行匹配，确保传来的 Url 对 ContentProvider 确实可以处理的。该类主要提供了如下两个方法：

```java
public void  addURI(String authority, String path, int code)
```
说明：该方法用于向UriMatcher注册Uri，其中参数authority与path组成一个Uri，参数code 是该Uri对应的标识码
例如：
```java
/**标识码*/
public static final int CODE_ID_1 = 1;
public static final int CODE_ID_2 = 2;
public static final String HOST = "com.lzb.provide.myContentProvide";
/**路径*/
public static final String PATH = "students";
/**初始化UriMatcher工具类*/
private static UriMatcher uriMatcher = new UriMatcher(UriMatcher.NO_MATCH);
static {
    // # 为通配符
    uriMatcher.addURI(HOST,PATH,CODE_ID_1);
    uriMatcher.addURI(HOST,PATH + "/#",CODE_ID_2);
}
public int  match(Uri uri)     
```
说明：根据前面注册的Uri返回其对应的标识码，如果在UriMatcher中找不到对应的Uri则返回-1。
例如上述示例注册了content://com.lzb.provide.myContentProvide:200/students与content://com.lzb.provide.myContentProvide:200/students/# 。
那么 若Uri为content://com.lzb.provide.myContentProvide:200/student返回1，若Uri为content://com.lzb.provide.myContentProvide:200/students/1(或者2,3,4.....)返回2，否则的话返回-1。
# 六  ContentUris类
这个类是一个操作 Uri 字符串的工具类，主要是拼接 Uri 字符串用，它有两个方法：
```
public static  Uri withAppendedId(Uri uri, long  id)   用于为路径加上id部分
```
例如：Uri  uri = Uri.parse("content://com.lzb.provide.myContentProvide:200/students");

Uri newUri = ContentUris.withAppendedId(uri,2);

那么 newUri 就变成了content://com.lzb.provide.myContentProvide:200/students/2。

public static long parseId(Uri uri)   用于从指定的Uri中解析出所包含的id

            例如上述newUri解析出来的ID为2。
# 七  示例
## 1 访问系统短信
```java
        //读取系统短信
         Uri uri=Uri.parse( "content://sms/inbox" );
         Cursor cursor=getContentResolver().query(uri,  new  String[]{ "address" , "body" },  null ,  null ,  null );
         while (cursor.moveToNext()){
             Log.e( "短信" , cursor.getString( 0 )+ " " +cursor.getString( 1 ));
          }
         //向系统中写入短信
         ContentResolver resolver = getContentResolver();
         Uri uri = Uri.parse( "content://sms/inbox" );
         ContentValues values =  new  ContentValues();
         values.put( "address" ,  "95558" );
         values.put( "body" ,  "尊敬的xxx,你尾号为xxxx的招商银行卡入账....." );
         values.put( "type" ,  1 );
         values.put( "person" ,  "招商银行" ); 
         values.put( "read" ,  0 );
         resolver.insert(uri, values);
```
说明：需要如下权限：

```java
<uses-permission android:name="android.permission.READ_SMS"/>

<uses-permission android:name="android.permission.WRITE_SMS"/>
```
 ## 2 访问手机联系人

Android系统提供了Contacts应用程序来管理联系人，而且还为联系人提供了ContentProvider，所以其他应用程序也可以来管理联系人。
```java
Uri uri1 = Uri.parse( "content://com.android.contacts/raw_contacts" );
Uri uri2 = Uri.parse( "content://com.android.contacts/data/phones" );
ContentResolver resolver = getContentResolver();
Cursor cursor = resolver.query(uri1,  new  String[]{ "contact_id" ,  "display_name" },  null ,  null ,  null );
while (cursor.moveToNext()){
       int  contact_id = cursor.getInt(cursor.getColumnIndex( "contact_id" ));
       String name = cursor.getString(cursor.getColumnIndex( "display_name" ));
       Cursor cursor2 = resolver.query(uri2,  new  String[]{ "data1" },  "raw_contact_id=?" ,  new  String[]{contact_id+ "" },  null );
       while (cursor2.moveToNext()){
               //int mimetype_id = cursor2.getInt(cursor2.getColumnIndex("mimetype"));
               String data1 = cursor2.getString(cursor2.getColumnIndex( "data1" ));
               Log.e( "联系人..." , data1);
       }
}
```
说明： 管理模拟器手机通讯录数据库在data->data->com.android.contact。
