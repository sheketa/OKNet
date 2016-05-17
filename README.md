# OKNet

- GET、POST、HEAD、OPTIONS、PUT、DELETE
- POST文件上传(字段、文件混合接口)
- 文件下载进度回调
- Https，多种证书设置
- 取消某个请求
- 自定义Callback
- 支持重定向
- 完善的Log日志输出
- 完善的错误分发链，支持自定义添加错误解析器
- 支持协议304缓存(解析Bean对象及其包含子对象需要implements Serializable)


----------
## in Application onCreate() ##

	OkHttpUtils.init(this);
    OkHttpUtils.getInstance()//
                .debug(true, true, "OKNet")                                              //是否打开调试
                .setConnectTimeout(OkHttpUtils.DEFAULT_MILLISECONDS)               //全局的连接超时时间
                .setReadTimeOut(OkHttpUtils.DEFAULT_MILLISECONDS)                  //全局的读取超时时间
                .setWriteTimeOut(OkHttpUtils.DEFAULT_MILLISECONDS)            //全局的写入超时时间
                /*.addCommonHeaders(headers)   */                                      //设置全局公共头
                .addCommonParams(params);

## GET、POST ##

	OkHttpUtils.get(url)//post
				.tag(this)
                .execute(new DialogCallback<CityResponse.DataBean>(this) {
                    @Override
                    public void onSimpleError(Cons.Error error, String message) {
                        System.out.println();
                    }

                    @Override
                    public void onResponse(boolean isFromCache, CityResponse.DataBean dataBean, Request request, @Nullable Response response) {
                        System.out.println();
                    }
                });

## POST File and Text ##

    OkHttpUtils.post(url)
				.tag(this)
                .headers("h1","h1")
                .params("p1","p1")
                .params("p2",file)//file
                .execute(new DialogCallback<CityResponse.DataBean>(this) {
                    @Override
                    public void onResponse(boolean isFromCache, CityResponse.DataBean dataBean, Request request, @Nullable Response response) {
                        
                    }

                    @Override
                    public void onSimpleError(Cons.Error error, String message) {

                    }
					//上传回调
                    @Override
                    public void upProgress(long currentSize, long totalSize, float progress, long networkSpeed) {
                        super.upProgress(currentSize, totalSize, progress, networkSpeed);
                    }
                });
## DownLoad File ##
    OkHttpUtils.get(url)
				.tag(this)
                .execute(new DialogCallback<CityResponse.DataBean>(this) {
                    @Override
                    public void onResponse(boolean isFromCache, CityResponse.DataBean dataBean, Request request, @Nullable Response response) {

                    }

                    @Override
                    public void onSimpleError(Cons.Error error, String message) {

                    }
					//下载回调
                    @Override
                    public void downloadProgress(long currentSize, long totalSize, float progress, long networkSpeed) {
                        super.downloadProgress(currentSize, totalSize, progress, networkSpeed);
                    }
                });

## Bitmap File ##

    OkHttpUtils.get(url)//
                .tag(this)//
                .execute(new BitmapDialogCallback(this) {
                    @Override
                    public void onResponse(boolean isFromCache, Bitmap bitmap, Request request, Response response) {
                        handleResponse(isFromCache, bitmap, request, response);
                        imageView.setImageBitmap(bitmap);
                    }

                    @Override
                    public void onError(boolean isFromCache, Call call, @Nullable Response response, @Nullable Exception e) {
                        super.onError(isFromCache, call, response, e);
                        handleError(isFromCache, call, response);
                    }
                });

## Https ##

	OkHttpUtils.get("https://kyfw.12306.cn/otn")//
                    .tag(this)//
                    .headers("Connection", "close")           //如果对于部分自签名的https访问不成功，需要加上该控制头
                    .setCertificates(new Buffer().writeUtf8(CER_12306).inputStream())  //方法一：设置自签名网站的证书（选一种即可）
                    .setCertificates(getAssets().open("srca.cer"))                     //方法二：也可以设置https证书（选一种即可）
                    .setCertificates()                                                 //方法三：信任所有证书（选一种即可）
                    .execute(new HttpsCallBack(this));

## 自定义Callback ##

	public abstract class CommonCallback<T> extends AbsCallback<T>

## 自定义异常处理 ##
> 在AbsCallback类中有个addExceptionParser方法<br>
> 通常配合具体业务回调更精准的错误分发


	addExceptionParser(new ExceptionParser() {
            @Override
            protected boolean handler(Throwable e, IHandler handler) {

                if (e != null) {
                    String s = !TextUtils.isEmpty(e.getMessage()) ? e.getMessage() : e.getClass().getSimpleName();

                    if (JSONException.class.isAssignableFrom(e.getClass())) {
                        handler.onHandler(Cons.Error.NetWork, s);
                        return true;
                    }
                }
                return false;
            }
        });


----------

## Gradle ##
compile 'com.ricky:oknet:1.1.0'


