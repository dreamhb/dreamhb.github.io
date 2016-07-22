---
layout: post
title: Android Bitmap 
excerpt: "如何避免因为Bitmap而引起的OOM"
modified: 2015-11-30
tags: [android]
comments: true
---

### 高效的加载大的Bitmap
从各种图片源获取的Bitmap往往都是比较大的，如果直接加载到内存中容易造成OOM异常，现提供如下思路：

###### 先获取Bitmap的大小和类型
示例代码：

<pre>
<code>
BitmapFactory.Options options = new BitmapFactory.Options();
options.inJustDecodeBounds = true;
BitmapFactory.decodeResource(getResource(), R.id.image, options);
int imageHeight = options.outHeight;
int imageWidth = options.outWidth;
String imageType = options.outMimeType;
</code>
</pre>

将inJustDecodeBounds设置为true，从而避免分配内存，因为在这里我们只需要Bitmap相关的一些参数。

##### 将缩小的图片加载到内存中
通过上面的计算，我们已经知道bitmap的相关信息，这些足以帮助我们来判断是直接加载全尺寸大图，还是一个压缩图了。一般我们基于如下的考虑因素：

1. 评估全图的内存使用大小
2. 应用的其他部分对内存的需求量
3. ImageView的尺寸或者将要加载图片的尺寸
4. 当前设备的屏幕大小和分辨率

<pre>
<code>
public static int calculateInSampleSize(
            BitmapFactory.Options options, int reqWidth, int reqHeight) {
    // Raw height and width of image
    final int height = options.outHeight;
    final int width = options.outWidth;
    int inSampleSize = 1;

    if (height > reqHeight || width > reqWidth) {

        final int halfHeight = height / 2;
        final int halfWidth = width / 2;

        // Calculate the largest inSampleSize value that is a power of 2 and keeps both
        // height and width larger than the requested height and width.
        while ((halfHeight / inSampleSize) > reqHeight
                && (halfWidth / inSampleSize) > reqWidth) {
            inSampleSize *= 2;
        }
    }

    return inSampleSize;
}
</code>
</pre>


### 在非UI线程中处理Bitmap
BitmapFatory.decode方法，如果源数据来自磁盘或者网络地址，那么不能放在主线程中。

### 缓存Bitmap
##### Memory Cache
LruCache 使用LinkedHashMap结构

##### 磁盘缓存
ContentProvider或许是一个很合适的选择，用来存储缓存的图片。
DiskLruCache

### 管理Bitmap内存
推荐的策略是根据不同的系统版本采用不同的方法。

1.	在Android2.2（API 8）或者更低的版本，当发生垃圾收集时，app的进程会被停止。这个会导致延迟，是个性能上的问题。Android2.3增加了同步GC，这意味着，如果bitmap不再引用时，内存会被立马回收。

2.	在Android2.3.3(API 10)或者以下，bitmap背后的像素数据是保存在native内存中的。和bitmap本身是分开的，而bitmap是保存在Dalvik heap上的。在本地内存中的像素数据不会像预想的那样释放，将潜在的导致内存溢出，并导致应用奔溃。在Android3.0（API 11）中，像素数据和bitmap一起存放在了Dalvik的堆上。

下面的例子将介绍在不同的Android版本上如何优化bitmap的内存管理
#### Android 2.3.3 及以下
在这种情况下，建议使用Bitmap的recycle方法。如果你显示大量的bitmap数据，那么很有可能导致OOM错误。**recycle**方法让app尽可能快的回收内存。

#### Anroid 3.0 或者更高
Android3.0（API 11）引入了BitmapFactory.Options.inBitmap。如果这个值被设置了，那么bitmap的内存将被复用，并且提升了性能，因为不再分配和释放内存了。然后它也有确定的限制，在Android 4.4（API 19） 中，只有相同大小或者小于它的bitmap才被支持。


