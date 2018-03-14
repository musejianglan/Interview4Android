### LruCache缓存

```
	// 获取到可用内存的最大值，使用内存超出这个值会引起OutOfMemory异常。  
    // LruCache通过构造函数传入缓存值，以KB为单位。  
    int maxMemory = (int) (Runtime.getRuntime().maxMemory() / 1024);
    
    // 使用最大可用内存值的1/8作为缓存的大小。  
    int cacheSize = maxMemory / 8;  
    private LruCache<String, Bitmap> lruCache = new LruCache<String, Bitmap>(cacheSize) {  
        @Override  
        protected int sizeOf(String key, Bitmap bitmap) {  
            // 重写此方法来衡量每张图片的大小，默认返回图片数量。  
            return bitmap.getByteCount() / 1024;  
        }  
    }; 
    
   // 把Bitmap对象加入到缓存中
    public void addBitmapToMemory(String key, Bitmap bitmap) {
        if (getBitmapFromMemCache(key) == null) {
            lruCache.put(key, bitmap);
        }
    }

    // 从缓存中得到Bitmap对象
    public Bitmap getBitmapFromMemCache(String key) {
　　　　Log.i(TAG, "lrucache size: " + lruCache.size());
        return lruCache.get(key);
    }

    // 从缓存中删除指定的Bitmap
    public void removeBitmapFromMemory(String key) {
        lruCache.remove(key);
    }
	
```

### 本地缓存
```
/** 
 * 本地缓存 
 *  
 * @author ZHY 
 *  
 */  
public class LocalCacheUtils {  
    /** 
     * 文件保存的路径 
     */  
    public static final String FILE_PATH = Environment  
            .getExternalStorageDirectory().getAbsolutePath() + "/cache/pics";  
  
    /** 
     * 从本地SD卡获取网络图片，key是url的MD5值 
     *  
     * @param url 
     * @return 
     */  
    public Bitmap getBitmapFromLocal(String url) {  
        try {  
            String fileName = MD5Encoder.encode(url);  
            File file = new File(FILE_PATH, fileName);  
            if (file.exists()) {  
                Bitmap bitmap = BitmapFactory.decodeStream(new FileInputStream(  
                        file));  
                return bitmap;  
            }  
  
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
  
        return null;  
  
    }  
  
    /** 
     * 向本地SD卡写网络图片 
     *  
     * @param url 
     * @param bitmap 
     */  
    public void setBitmap2Local(String url, Bitmap bitmap) {  
        try {  
            // 文件的名字  
            String fileName = MD5Encoder.encode(url);  
            // 创建文件流，指向该路径，文件名叫做fileName  
            File file = new File(FILE_PATH, fileName);  
            // file其实是图片，它的父级File是文件夹，判断一下文件夹是否存在，如果不存在，创建文件夹  
            File fileParent = file.getParentFile();  
            if (!fileParent.exists()) {  
                // 文件夹不存在  
                fileParent.mkdirs();// 创建文件夹  
            }  
            // 将图片保存到本地  
            bitmap.compress(CompressFormat.JPEG, 100,  
                    new FileOutputStream(file));  
  
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
    }  
  
}  
```

### bitmap二次采样

1.第一次采样  
第一次采样我主要是想要获得图片的压缩比例，假如说我有一张图片是200*200，那么我想把这张图片的缩略图显示在一个50*50的ImageView上，那我的压缩比例应该为4，那么这个4应该怎么样来获得呢？这就是我们第一步的操作了，我先加载图片的边界到内存中，这个加载操作并不会耗费多少内存，加载到内存之后，我就可以获得这张图片的宽高参数，然后根据图片的宽高，再结合控件的宽高计算出缩放比例。

2.第二次采样  
在第一次采样的基础上，我来进行二次采样。二次采样的时候，我把第一次采样后算出来的结果作为一个参数传递给第BitmapFactory，这样在加载图片的时候系统就不会将整张图片加载进来了，而是只会加载该图片的一张缩略图进来，这样不仅提高了加载速率，而且也极大的节省了内存，而且对于用户来说，他也不会有视觉上的差异。

```

public class BitmapUtils {  
    /** 
     * @param filePath   要加载的图片路径 
     * @param destWidth  显示图片的控件宽度 
     * @param destHeight 显示图片的控件的高度 
     * @return 
     */  
    public static Bitmap getBitmap(String filePath, int destWidth, int destHeight) {  
        //第一次采样  
        BitmapFactory.Options options = new BitmapFactory.Options();  
        //该属性设置为true只会加载图片的边框进来，并不会加载图片具体的像素点  
        options.inJustDecodeBounds = true;  
        //第一次加载图片，这时只会加载图片的边框进来，并不会加载图片中的像素点  
        BitmapFactory.decodeFile(filePath, options);  
        //获得原图的宽和高  
        int outWidth = options.outWidth;  
        int outHeight = options.outHeight;  
        //定义缩放比例  
        int sampleSize = 1;  
        while (outHeight / sampleSize > destHeight || outWidth / sampleSize > destWidth) {  
            //如果宽高的任意一方的缩放比例没有达到要求，都继续增大缩放比例  
            //sampleSize应该为2的n次幂，如果给sampleSize设置的数字不是2的n次幂，那么系统会就近取值  
            sampleSize *= 2;  
        }  
        /********************************************************************************************/  
        //至此，第一次采样已经结束，我们已经成功的计算出了sampleSize的大小  
        /********************************************************************************************/  
        //二次采样开始  
        //二次采样时我需要将图片加载出来显示，不能只加载图片的框架，因此inJustDecodeBounds属性要设置为false  
        options.inJustDecodeBounds = false; 
        options.inPreferredConfig=Config.RGB_565;
        //设置缩放比例  
        options.inSampleSize = sampleSize;  
        options.inPreferredConfig = Bitmap.Config.ARGB_8888;  
        //加载图片并返回  
        return BitmapFactory.decodeFile(filePath, options);  
    }  
}  


图片格式占用内存的计算方法：以一张100*100px的图片占用内存为例
ALPHA_8：
 图片长度*图片宽度
 100*100=10000字节
ARGB_4444：
 图片长度*图片宽度*2
 100*100*2=20000字节 
ARGB_8888：
 图片长度*图片宽度*4
 100*100*4=40000字节 
RGB_565：
 图片长度*图片宽度*2
 100*100*2=20000字节
```


### 图片压缩

对图片质量进行压缩

```
        public static Bitmap compressImage(Bitmap bitmap){  
            ByteArrayOutputStream baos = new ByteArrayOutputStream();  
            //质量压缩方法，这里100表示不压缩，把压缩后的数据存放到baos中  
            bitmap.compress(Bitmap.CompressFormat.JPEG, 100, baos);  
            int options = 100;  
            //循环判断如果压缩后图片是否大于50kb,大于继续压缩  
            while ( baos.toByteArray().length / 1024>50) {  
                //清空baos  
                baos.reset();  
                bitmap.compress(Bitmap.CompressFormat.JPEG, options, baos);  
                options -= 10;//每次都减少10  
            }  
            //把压缩后的数据baos存放到ByteArrayInputStream中  
            ByteArrayInputStream isBm = new ByteArrayInputStream(baos.toByteArray());  
            //把ByteArrayInputStream数据生成图片  
            Bitmap newBitmap = BitmapFactory.decodeStream(isBm, null, null);  
            return newBitmap;  
        }  
```


对图片尺寸进行压缩  

```
    /**
     * 按图片尺寸压缩 参数是bitmap
     * @param bitmap
     * @param pixelW
     * @param pixelH
     * @return
     */
    public static Bitmap compressImageFromBitmap(Bitmap bitmap, int pixelW, int pixelH) {
        ByteArrayOutputStream os = new ByteArrayOutputStream();
        bitmap.compress(Bitmap.CompressFormat.JPEG, 100, os);
        if( os.toByteArray().length / 1024>512) {//判断如果图片大于0.5M,进行压缩避免在生成图片（BitmapFactory.decodeStream）时溢出
            os.reset();
            bitmap.compress(Bitmap.CompressFormat.JPEG, 50, os);//这里压缩50%，把压缩后的数据存放到baos中
        }
        ByteArrayInputStream is = new ByteArrayInputStream(os.toByteArray());
        BitmapFactory.Options options = new BitmapFactory.Options();
        options.inJustDecodeBounds = true;
        options.inPreferredConfig = Bitmap.Config.RGB_565;
        BitmapFactory.decodeStream(is, null, options);
        options.inJustDecodeBounds = false;
        options.inSampleSize = computeSampleSize(options , pixelH > pixelW ? pixelW : pixelH ,pixelW * pixelH );
        is = new ByteArrayInputStream(os.toByteArray());
        Bitmap newBitmap = BitmapFactory.decodeStream(is, null, options);
        return newBitmap;
    }


    /**
     * 动态计算出图片的inSampleSize
     * @param options
     * @param minSideLength
     * @param maxNumOfPixels
     * @return
     */
    public static int computeSampleSize(BitmapFactory.Options options, int minSideLength, int maxNumOfPixels) {
        int initialSize = computeInitialSampleSize(options, minSideLength, maxNumOfPixels);
        int roundedSize;
        if (initialSize <= 8) {
            roundedSize = 1;
            while (roundedSize < initialSize) {
                roundedSize <<= 1;
            }
        } else {
            roundedSize = (initialSize + 7) / 8 * 8;
        }
        return roundedSize;
    }

    private static int computeInitialSampleSize(BitmapFactory.Options options, int minSideLength, int maxNumOfPixels) {
        double w = options.outWidth;
        double h = options.outHeight;
        int lowerBound = (maxNumOfPixels == -1) ? 1 : (int) Math.ceil(Math.sqrt(w * h / maxNumOfPixels));
        int upperBound = (minSideLength == -1) ? 128 :(int) Math.min(Math.floor(w / minSideLength), Math.floor(h / minSideLength));
        if (upperBound < lowerBound) {
            return lowerBound;
        }
        if ((maxNumOfPixels == -1) && (minSideLength == -1)) {
            return 1;
        } else if (minSideLength == -1) {
            return lowerBound;
        } else {
            return upperBound;
        }
    }
```