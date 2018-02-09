# 使用SharedPreferences存储数据 
> SharedPreferences是Android平台上一个轻量级的存储类，用来保存应用的一些常用配置。SharedPreferences提供了java常规的Long、Int、String等类型数据的保存接口。最终是以xml方式来保存，整体效率来看不是特别的高，对于常规的轻量级而言比SQLite要好不少，如果真的存储量不大可以考虑自己定义文件格式。xml处理时Dalvik会通过自带底层的本地XML Parser解析，比如XMLpull方式，这样对于内存资源占用比较好。其存储位置在/data/data/< >/shared_prefs目录下。 
SharedPreferences对象本身只能获取数据而不支持存储和修改，存储修改是通过Editor对象实现

### SharedPreferences数据的四种操作模式
* Context.MODE_PRIVATE  
为默认操作模式,代表该文件是私有数据,只能被应用本身访问,在该模式下,写入的内容会覆盖原文件的内容
* Context.MODE_APPEND  
模式会检查文件是否存在,存在就往文件追加内容,否则就创建新文件
* Context.MODE_WORLD_READABLE  
表示当前文件可以被其他应用读取.
* Context.MODE_WORLD_WRITEABLE  
表示当前文件可以被其他应用写入  


> 特别注意：出于安全性的考虑，MODE_WORLD_READABLE 和 MODE_WORLD_WRITEABLE 在Android 4.2版本中已经被弃用

### 使用

实现SharedPreferences存储的步骤如下：   
1. 根据Context获取SharedPreferences对象   
2. 利用edit()方法获取Editor对象。   
3. 通过Editor对象存储key-value键值对数据。   
4. 通过commit()方法提交数据。  

#### 得到SharedPreferences对象
* Context.getSharedPreferences(文件名称，操作模式)   
文件名称不存在就会创建一个，操作模式有两种： 
MODE_PRIVATE：默认操作模式，直接在把第二个参数写0就是默认使用这种操作模式，这种模式表示只有当前的应用程序才可以对当前这个SharedPreferences文件进行读写。 
MODE_MULTI_PRIVATE：用于多个进程共同操作一个SharedPreferences文件。


* Activity.getPreferences(操作模式)   
使用这个方法会自动将当前活动的类名作为SharedPreferences的文件名，底层调用的是下面这个方法 
Activity.getSharedPreferences（String name, int mode）我们也可以直接调用getSharedPreferences这个方法，传入自定义的名字。 
* PreferenceManager.getDefaultSharedPreferences(Context)   
使用这个方法会自动使用当前程序的包名作为前缀来命名SharedPreferences文件

```


 		//获取SharedPreferences对象
        Context ctx = MainActivity.this;       
        SharedPreferences sp = ctx.getSharedPreferences("SP", MODE_PRIVATE);
        //存入数据
        Editor editor = sp.edit();
        editor.putString("STRING_KEY", "string");
        editor.putInt("INT_KEY", 0);
        editor.putBoolean("BOOLEAN_KEY", true);
        editor.commit();

        //返回STRING_KEY的值
        Log.d("SP", sp.getString("STRING_KEY", "none"));
        //如果NOT_EXIST不存在，则返回值为"none"
        Log.d("SP", sp.getString("NOT_EXIST", "none"));

```

#### SharedPreference.Editor的apply和commit方法异同
1. apply没有返回值而commit返回boolean表明修改是否提交成功 
2. apply是将修改数据原子提交到内存, 而后异步真正提交到硬件磁盘, 而commit是同步的提交到硬件磁盘，因此，在多个并发的提交commit的时候，他们会等待正在处理的commit保存到磁盘后在操作，从而降低了效率。而apply只是原子的提交到内容，后面有调用apply的函数的将会直接覆盖前面的内存数据，这样从一定程度上提高了很多效率。 
3. apply方法不会提示任何失败的提示。 
由于在一个进程中，sharedPreference是单实例，一般不会出现并发冲突，如果对提交的结果不关心的话，建议使用apply，当然需要确保提交成功且有后续操作的话，还是需要用commit的。

# 文件存储数据 
## 基本概念
* 内存，我们在英文中称作memory
* 内部存储，我们称为InternalStorage  
data目录，data/data/包名/..  
```
1.data/data/包名/sharedprefs
2.data/data/包名/databases
3.data/data/包名/files
4.data/data/包名/cache
```

如果打开过data文件，应该都知道这些文件夹是干什么用的，我们在使用sharedPreferenced的时候，将数据持久化存储于本地，其实就是存在这个文件中的xml文件里，我们App里边的数据库文件就存储于databases文件夹中，还有我们的普通数据存储在files中，缓存文件存储在cache文件夹中，存储在这里的文件我们都称之为内部存储。  

* 外部存储我们称为ExternalStorage  

![存储1](https://github.com/musejianglan/Interview4Android/blob/master/img/sdcard_1.png)  

如果按照路径的特征，我们又可以将文件存储的路径分为两大类，一类是路径中含有包名的，一类是路径中不含有包名的，含有包名的路径，因为和某个App有关，所以对这些文件夹的访问都是调用Context里边的方法，而不含有包名的路径，和某一个App无关，我们可以通过Environment中的方法来访问。如下图：  
![存储2](https://github.com/musejianglan/Interview4Android/blob/master/img/sdcard_2.png)
---
外部存储路径：

* sdcard：2.3之前的sd卡路径
* mnt/sdcard：4.3之前的sd卡路径
* storage/sdcard：4.3之后的sd卡路径

## 内部存储
### 基本原理
Context提供了两个方法来打开数据文件里的文件IO流 
```
	FileInputStream fileInputStream = this.openFileInput("文件名");
	FileOutputStream fileOutputStream = this.openFileOutput("文件名", MODE_PRIVATE);
```
这两个方法第一个参数 用于指定文件名，第二个参数指定打开文件的模式。  
* MODE_PRIVATE：为默认操作模式，代表该文件是私有数据，只能被应用本身访问，在该模式下，写入的内容会覆盖原文件的内容，如果想把新写入的内容追加到原文件中。可   以使用Context.MODE_APPEND  
* MODE_APPEND：模式会检查文件是否存在，存在就往文件追加内容，否则就创建新文件。  
* MODE_WORLD_READABLE：表示当前文件可以被其他应用读取；  
* MODE_WORLD_WRITEABLE：表示当前文件可以被其他应用写入  

Context还提供了如下几个重要的方法：
```
			File dir = this.getDir("", MODE_PRIVATE);//在应用程序的数据文件夹下获取或者创建name对应的子目录
			File filesDir = this.getFilesDir();//获取该应用程序的数据文件夹得绝对路径 data/data/包名/files
			String[] strings = this.fileList();//返回该应用数据文件夹的全部文件
			File cacheDir = this.getCacheDir();//data/data/包名/cache

```

### 外部存储

#### 私有目录 sdcard/Android/data/包名/***

```
			File externalCacheDir = this.getExternalCacheDir();//sdcard/Android/data/包名/cache
			File externalFilesDir = this.getExternalFilesDir(Environment.DIRECTORY_MUSIC);////sdcard/Android/data/包名/files
//			 * {@link android.os.Environment#DIRECTORY_MUSIC},
//			 * {@link android.os.Environment#DIRECTORY_PODCASTS},
//			 * {@link android.os.Environment#DIRECTORY_RINGTONES},
//			 * {@link android.os.Environment#DIRECTORY_ALARMS},
//			 * {@link android.os.Environment#DIRECTORY_NOTIFICATIONS},
//			 * {@link android.os.Environment#DIRECTORY_PICTURES}, or
//			 * {@link android.os.Environment#DIRECTORY_MOVIES}.
```

#### 公共目录

##### 九大公共目录
`File externalStoragePublicDirectory = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_MUSIC);`  

图片、音乐、电影等目录

##### sdcard

`String externalStorageState = Environment.getExternalStorageState();`  
方法判断手机上是否插了sd卡,且应用程序具有读写SD卡的权限  

`File sdcardFile = Environment.getExternalStorageDirectory();`  
方法来获取外部存储器，也就是SD卡的目录,或者使用"/mnt/sdcard/"目录  


# SQLite数据库存储数据 

### 介绍
SQLite是轻量级嵌入式数据库引擎，它支持 SQL 语言，并且只利用很少的内存就有很好的性能。此外它还是开源的，任何人都可以使用它。许多开源项目（(Mozilla, PHP, Python）都使用了 SQLite.SQLite 由以下几个组件组成：SQL 编译器、内核、后端以及附件。SQLite 通过利用虚拟机和虚拟数据库引擎（VDBE），使调试、修改和扩展 SQLite 的内核变得更加方便。

SQLite 基本上符合 SQL-92 标准，和其他的主要 SQL 数据库没什么区别。它的优点就是高效，Android 运行时环境包含了完整的 SQLite。

SQLite 和其他数据库最大的不同就是对数据类型的支持，创建一个表时，可以在 CREATE TABLE 语句中指定某列的数据类型，但是你可以把任何数据类型放入任何列中。当某个值插入数据库时，SQLite 将检查它的类型。如果该类型与关联的列不匹配，则 SQLite 会尝试将该值转换成该列的类型。如果不能转换，则该值将作为其本身具有的类型存储。比如可以把一个字符串（String）放入 INTEGER 列。SQLite 称这为“弱类型”（manifest typing.）。 此外，SQLite 不支持一些标准的 SQL 功能，特别是外键约束（FOREIGN KEY constrains），嵌套 transcaction 和 RIGHT OUTER JOIN 和 FULL OUTER JOIN, 还有一些 ALTER TABLE 功能。 除了上述功能外，SQLite 是一个完整的 SQL 系统，拥有完整的触发器，交易等等。


### 使用
#### 继承 SQLiteOpenHelper 帮助创建一个数据库
```
public class DatabaseHelper extends SQLiteOpenHelper {   
	
	private final static String DB_NAME = "mydb";
	private final static int VERSION = 1;

  DatabaseHelper(Context context) 
  {     
    super(context, DB_NAME, null, VERSION);     
     }     

     @Override    
     public void onCreate(SQLiteDatabase db) {     
         // TODO 创建数据库后，对数据库的操作     
     }     

     @Override    
 public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {     
         // TODO 更改数据库版本的操作     
     }     

 @Override    
 public void onOpen(SQLiteDatabase db) {     
         super.onOpen(db);       
         // TODO 每次成功打开数据库后首先被执行     
     }     
 } 
```

#### 获取SQLiteDatabase实例用于操作数据
`SQLiteDatabase db=(new DatabaseHelper(getContext())).getWritableDatabase()`  
调用 getReadableDatabase() 或 getWriteableDatabase() 方法，你可以得到 SQLiteDatabase 实例，具体调用那个方法，取决于你是否需要改变数据库的内容  

当你完成了对数据库的操作（例如你的 Activity 已经关闭），需要调用 SQLiteDatabase 的 Close() 方法来释放掉数据库连接  

*  SQLiteDatabase 的 execSQL(sql语句) 方法来执行 DDL 语句
`db.execSQL("CREATE TABLE mytable (_id INTEGER PRIMARY KEY AUTOINCREMENT, title TEXT, value REAL);"); `

* 另一种方法是使用 SQLiteDatabase 对象的 insert(), update(), delete() 方法。这些方法把 SQL 语句的一部分作为参数



# 使用ContentProvider存储数据

# 网络存储
