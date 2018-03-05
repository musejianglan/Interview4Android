## res/raw和assets的使用

### 相同点

两者目录下的文件在打包后原封不动的保存在apk中，不会被编译成二进制

### 不同点
1. res/raw中的文件会被映射到R.java文件中，访问的时候直接使用资源id即R.id.filename；assets文件夹下的文件不会被映射到R.java中，访问的时候需要AssetManager类。
2. res/raw不可以有目录结构，而assets则可以有目录结构，也就是assets目录下可以再建立文件夹。
3. Raw里面，放置不需要编译成二进制的文件，与assets目录的区别就是，单个文件大小有约1m的限制，assets单个文件允许的大小要比raw大。另外，raw里面可以通过resource获取，assets里面的文件，只能通过assetmanager读取。

### 读取文件资源

1.读取res/raw下的文件资源，通过以下方式获取输入流来进行写操作
```
InputStream is = getResources().openRawResource(R.id.filename);  
```

2.读取assets下的文件资源，通过以下方式获取输入流来进行写操作
```
AssetManager am = null;  
am = getAssets();  
InputStream is = am.open("filename");  
```

#### 补充一下：
在未知目录下有哪些文件，该去和获取这些文件的名称并把文件拷贝到目标目录中呢？（用于内置文件但不知道文件名称，需要筛选出想要的文件然后拷贝到目标目录中，推荐内置在assets文件夹中）  
1.res/raw目录：  
通过反射的方式得到R.java里面raw内部类里面所有的资源ID的名称，然后通过名称获取资源ID的值来读取我们想要的文件。（这个方法我没试过，有用过的同学麻烦发一段代码看看）。
2.assets目录：  
getAssets().list("");来获取assets目录下所有文件夹和文件的名称，再通过这些名称再读取我们想要的文件。  
