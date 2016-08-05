* [Getting Started and Create an Android Client](https://futurestud.io/blog/retrofit-getting-started-and-android-client)

* [Basic Authentication on Android](https://futurestud.io/blog/android-basic-authentication-with-retrofit)

* [Token Authentication on Android](https://futurestud.io/blog/retrofit-token-authentication-on-android)

* [OAuth on Android](https://futurestud.io/blog/oauth-2-on-android-with-retrofit)

* [Multiple Query Parameters of Same Name](https://futurestud.io/blog/retrofit-multiple-query-parameters-of-same-name)

* [Synchronous and Asynchronous Requests](https://futurestud.io/blog/retrofit-synchronous-and-asynchronous-requests)

* [Send Objects in Request Body](https://futurestud.io/blog/retrofit-send-objects-in-request-body)

* [Define a Custom Response Converter](https://futurestud.io/blog/retrofit-replace-the-integrated-json-converter)

* [Add Custom Request Header](https://futurestud.io/blog/retrofit-add-custom-request-header)

  * add static request headers

    ```
    // use @Headers to add header
    public interface UserService {  
        @Headers({
            "Accept: application/vnd.yourapi.v1.full+json",
            "User-Agent: Your-App-Name"
        })
        @GET("/tasks/{task_id}")
        Call<Task> getTask(@Path("task_id") long taskId);
    }

    or 

    // add headers in OkHttp interceptor
    OkHttpClient.Builder httpClient = new OkHttpClient.Builder();  
    httpClient.addInterceptor(new Interceptor() {  
        @Override
        public Response intercept(Interceptor.Chain chain) throws IOException {
            Request original = chain.request();
            Request request = original.newBuilder()
                .header("User-Agent", "Your-App-Name")
                .header("Accept", "application/vnd.yourapi.v1.full+json")
                .method(original.method(), original.body())
                .build();
            return chain.proceed(request);
        }
    }

    OkHttpClient client = httpClient.build();  
    Retrofit retrofit = new Retrofit.Builder()  
        .baseUrl(API_BASE_URL)
        .addConverterFactory(GsonConverterFactory.create())
        .client(client)
        .build();
    ```

  * dynamic headers

    ```
    public interface UserService {  
        @GET("/tasks")
        Call<List<Task>> getTasks(@Header("Content-Range") String contentRange);
    }
    ```

  * add repeat header override or not override

    1. **header(key, value)**: overrides the respective header key with value if there is already an existing header identified by key     **(override)**
    2. **addHeader(key, value)**: adds the respective header key and value even if there is an existing header field with the same key       **(not override)**

* [Optional Query Parameters](https://futurestud.io/blog/retrofit-optional-query-parameters)

  ```
  public interface TaskService {  
         @GET("/tasks")
         Call<List<Task>> getTasks(@Query("sort") String order);
     }

     // you will get the result
     https://your.api.com/tasks?sort=value-of-order-parameter 
  ```

*    If you don’t want to pass it with the request, just pass **null** as the value during method call.

     ```
     // if you want to ignore query parameter, you should use Integer,Float,Long and pass null as the value
     service.getTasks(null);  
     ```

     > Retrofit skips `null` parameters and ignores them while assembling the request. Keep in mind, that **you can’t pass `null`for primitive data types like `int`, `float`, `long`, etc. Instead, use `Integer`, `Float`, `Long`, etc and the compiler won’t be grumpy.**

*    [How to Integrate XML Converter](https://futurestud.io/blog/retrofit-how-to-integrate-xml-converter)

*    [Using the Log Level to Debug Requests](https://futurestud.io/blog/retrofit-using-the-log-level-to-debug-requests)

     * add HttpLoggingInterceptor to get request and response logging

       The developers of OkHttp added a logging interceptor in release `2.6.0` for Retrofit2

       Retrofit 2 completely relies on OkHttp for any network operation. The developers of OkHttp have released a separate logging interceptor project, which implements logging for OkHttp. You can add it to your project with a quick edit of your`build.gradle`:

       ```
       compile 'com.squareup.okhttp3:logging-interceptor:3.4.1' 
       ```

       ​	Since logging isn’t integrated by default anymore in Retrofit 2, we need to add a logging interceptor for OkHttp. Luckily OkHttp already ships with this interceptor and you only need to activate it for your OkHttpClient.

       ```
       HttpLoggingInterceptor logging = new HttpLoggingInterceptor();  
       // set your desired log level
       logging.setLevel(Level.BODY);

       OkHttpClient.Builder httpClient = new OkHttpClient.Builder();  
       // add your other interceptors …

       // add logging as last interceptor
       httpClient.addInterceptor(logging);  // <-- this is the important line!

       Retrofit retrofit = new Retrofit.Builder()  
          .baseUrl(API_BASE_URL)
          .addConverterFactory(GsonConverterFactory.create())
          .client(httpClient.build())
          .build();
       ```

       **Keep in mind, you should add HttpLoggingInterceptor as last interceptor **

     * OkHttp’s logging interceptor has four log levels: `NONE`, `BASIC`, `HEADERS`, `BODY`. 

       * **None:** No logging

       * **Basic:** Log request type, url, size of request body, response status and size of response body.

         ```
         D/HttpLoggingInterceptor$Logger: --> POST /upload HTTP/1.1 (277-byte body)  
         D/HttpLoggingInterceptor$Logger: <-- HTTP/1.1 200 OK (543ms, -1-byte body)
         ```

       * **Headers:** Log request and response headers, request type, url, response status.

         **If you add request headers yourself, make sure the logging interceptor is the last interceptor added to the OkHttp client.**

         ```
         D/HttpLoggingInterceptor$Logger: --> POST /upload HTTP/1.1  
         D/HttpLoggingInterceptor$Logger: Accept: application/json  
         D/HttpLoggingInterceptor$Logger: Content-Type: application/json  
         D/HttpLoggingInterceptor$Logger: --> END POST  
         D/HttpLoggingInterceptor$Logger: <-- HTTP/1.1 200 OK (1039ms)  
         D/HttpLoggingInterceptor$Logger: content-type: text/html; charset=utf-8  
         D/HttpLoggingInterceptor$Logger: cache-control: no-cache  
         D/HttpLoggingInterceptor$Logger: vary: accept-encoding  
         D/HttpLoggingInterceptor$Logger: Date: Wed, 28 Oct 2015 08:24:20 GMT  
         D/HttpLoggingInterceptor$Logger: Connection: keep-alive  
         D/HttpLoggingInterceptor$Logger: Transfer-Encoding: chunked  
         D/HttpLoggingInterceptor$Logger: OkHttp-Selected-Protocol: http/1.1  
         D/HttpLoggingInterceptor$Logger: OkHttp-Sent-Millis: 1446020610352  
         D/HttpLoggingInterceptor$Logger: OkHttp-Received-Millis: 1446020610369  
         D/HttpLoggingInterceptor$Logger: <-- END HTTP
         ```

       * **Body:** Log request and response headers and body.

         ```
         D/OkHttp: --> GET https://oa-api.qima-inc.com/open/performance/scheme/1?token=4d7478e4-2ca1-4af7-a34b-442fa30cb989 http/1.1
         D/OkHttp: --> END GET
         D/OkHttp: <-- 200 OK https://oa-api.qima-inc.com/open/performance/scheme/1?token=4d7478e4-2ca1-4af7-a34b-442fa30cb989 (188ms)
         D/OkHttp: Date: Wed, 03 Aug 2016 11:33:43 GMT
         D/OkHttp: Content-Type: application/json; charset=utf-8
         D/OkHttp: Vary: Accept-Encoding
         D/OkHttp: Node: qabb-qa-oa0
         D/OkHttp: Node: qabb-qa-mutiliron0
         D/OkHttp: Node: qabb-qa-httpgw0
         D/OkHttp: Set-Cookie: route=;Path=/
         D/OkHttp: Transfer-Encoding: chunked
         D/OkHttp: Connection: Keep-alive
         D/OkHttp: {"code":0,"msg":"ok","data":{"components":[{"id":1,"title":"个人自评","type":0,"apply_type":["1","2","3"]}
         D/OkHttp: <-- END HTTP (1431-byte body)
         ```

*    [How to Upload Files to Server](https://futurestud.io/blog/retrofit-how-to-upload-files)

*    [Series Round-Up](https://futurestud.io/blog/retrofit-series-round-up)

*    [Retrofit 2 — Upgrade Guide from 1.9](https://futurestud.io/blog/retrofit-2-upgrade-guide-from-1-9)

*    [Retrofit 2 — How to Upload Files to Server](https://futurestud.io/blog/retrofit-2-how-to-upload-files-to-server)

     ```
     public interface FileUploadService {  
         @Multipart
         @POST("upload")
         Call<ResponseBody> upload(@Part("description") RequestBody description,
                                   @Part MultipartBody.Part file);
     }

     private void uploadFile(Uri fileUri) {  
         // create upload service client
         FileUploadService service =
                 ServiceGenerator.createService(FileUploadService.class);

         // https://github.com/iPaulPro/aFileChooser/blob/master/aFileChooser/src/com/ipaulpro/afilechooser/utils/FileUtils.java
         // use the FileUtils to get the actual file by uri
         File file = FileUtils.getFile(this, fileUri);

         // create RequestBody instance from file
         RequestBody requestFile =
                 RequestBody.create(MediaType.parse("multipart/form-data"), file);

         // MultipartBody.Part is used to send also the actual file name
         MultipartBody.Part body =
                 MultipartBody.Part.createFormData("picture", file.getName(), requestFile);

         // add another part within the multipart request
         String descriptionString = "hello, this is description speaking";
         RequestBody description =
                 RequestBody.create(
                         MediaType.parse("multipart/form-data"), descriptionString);

         // finally, execute the request
         Call<ResponseBody> call = service.upload(description, body);
         call.enqueue(new Callback<ResponseBody>() {
             @Override
             public void onResponse(Call<ResponseBody> call,
                                    Response<ResponseBody> response) {
                 Log.v("Upload", "success");
             }

             @Override
             public void onFailure(Call<ResponseBody> call, Throwable t) {
                 Log.e("Upload error:", t.getMessage());
             }
         });
     }
     ```

     ​

*    [Retrofit 2 — Log Requests and Responses](https://futurestud.io/blog/retrofit-2-log-requests-and-responses)

*    [Retrofit 2 — Hawk Authentication on Android](https://futurestud.io/blog/retrofit-2-hawk-authentication-on-android)

*    [Retrofit 2 — Simple Error Handling](https://futurestud.io/blog/retrofit-2-simple-error-handling)

*    [How to use OkHttp 3 with Retrofit 1](https://futurestud.io/blog/retrofit-how-to-use-okhttp-3-with-retrofit-1)

*    [Retrofit 2 — Book Update & Release Celebration](https://futurestud.io/blog/retrofit-2-book-update-release-celebration)

*    [Send Data Form-Urlencoded](https://futurestud.io/blog/retrofit-send-data-form-urlencoded)

     * @FormUrlEncoded adjust the proper mime type of your request automatically to `application/x-www-form-urlencoded`

       ```
       public interface TaskService {  
           @FormUrlEncoded
           @POST("tasks")
           Call<Task> createTask(@Field("title") String title);
       }
       ```

       **Please be aware of the fact, that you can’t use this annotation for `GET` requests. Form encoded requests are intended to send data to the server!**

       ```
       service.createTask("Research Retrofit form encoded requests"); 

       // the result from encoded request body
       title=Research+Retrofit+form+encoded+requests 
       ```

     * You can pass an array of values using the same `key` for your form encoded requests

       ```
         public interface TaskService {  
              @FormUrlEncoded
              @POST("tasks")
              Call<List<Task>> createTasks(@Field("title") List<String> titles);
          }

          List<String> titles = new ArrayList<>();  
          titles.add("Research Retrofit");  
          titles.add("Retrofit Form Encoded")

          service.createTasks(titles);  

          // the result of form encoded request body
          title=Research+Retrofit&title=Retrofit+Form+Encoded 
       ```

          Each item within the list of task titles will be mapped to a key-value-pair by Retrofit. The `title` key will be the same for every pair. The key-value-pairs are separated by an ampersand (`&`) and the values are separated from their keys by an equal symbol `=`.

     * encoded parameter

       ```
       @Field(value = "title", encoded = true) String title
       ```

       The `encoded` option defines whether your provided key-value-pair is already url encoded

     * Form-Urlencoded vs. Query Parameter

       In essence: the request type

       * form-urlencoded: Post
       * query

       Use form-urlencoded requests to send data to a server or API. The data is sent within the request body and not as an url parameter.

       Query parameters are used when requesting data from an API or server using specific fields or filter.

*    [Send Data Form-Urlencoded Using FieldMap](https://futurestud.io/blog/retrofit-send-data-form-urlencoded-using-fieldmap)

*    [Retrofit 2 — Manage Request Headers in OkHttp Interceptor](https://futurestud.io/blog/retrofit-2-manage-request-headers-in-okhttp-interceptor)

*    [Retrofit 2 — How to Add Query Parameters to Every Request](https://futurestud.io/blog/retrofit-2-how-to-add-query-parameters-to-every-request)

*    [Retrofit 2 — Add Multiple Query Parameter With QueryMap](https://futurestud.io/blog/retrofit-2-add-multiple-query-parameter-with-querymap)

*    [Retrofit 2 — How to Use Dynamic Urls for Requests](https://futurestud.io/blog/retrofit-2-how-to-use-dynamic-urls-for-requests)

*    [Retrofit 2 — Url Handling, Resolution and Parsing](https://futurestud.io/blog/retrofit-2-url-handling-resolution-and-parsing)

     * baseUrl should always end with '/'

       each endpoint definition with a relative path address will resolve correctly, because it appends itself to the base url that already defines or includes path parameters.

       ```
       # Good Practise
       base url: https://futurestud.io/api/  
       endpoint: my/endpoint  
       Result:   https://futurestud.io/api/my/endpoint

       # Bad Practise
       base url: https://futurestud.io/api  
       endpoint: /my/endpoint  
       Result:   https://futurestud.io/my/endpoint
       ```

     * dynamic urls or passing a full url

       ```
        # Example 3 — completely different url
        base url: http://futurestud.io/api/  
        endpoint: https://api.futurestud.io/  
        Result:   https://api.futurestud.io/

       # Example 4 — Keep the base url’s scheme
       base url: https://futurestud.io/api/  
       endpoint: //api.futurestud.io/  
       Result:   https://api.futurestud.io/

       # Example 5 — Keep the base url’s scheme
       base url: http://futurestud.io/api/  
       endpoint: //api.github.com  
       Result:   http://api.github.com 
       ```

*    [Retrofit 2 — Constant, Default and Logic Values for POST and PUT Requests](https://futurestud.io/blog/retrofit-2-constant-default-and-logic-values-for-post-and-put-requests)

     * use @Body instead of @Field, some variables are completed automatically in model

       ```
       # Before:
       @FormUrlEncoded
       @POST("/feedback")
       Call<ResponseBody> sendFeedbackSimple(  
           @Field("osName") String osName,
           @Field("osVersion") int osVersion,
           @Field("device") String device,
           @Field("message") String message,
           @Field("userIsATalker") Boolean userIsATalker);
           
       # After:
       @POST("/feedback")
       Call<ResponseBody> sendFeedbackConstant(@Body UserFeedback feedbackObject);

       public class UserFeedback {
           private String osName = "Android";
           private int osVersion = android.os.Build.VERSION.SDK_INT;
           private String device = Build.MODEL;
           private String message;
           private boolean userIsATalker;

           public UserFeedback(String message) {
               this.message = message;
               this.userIsATalker = (message.length() > 200);
           }
       }
       ```

*    [Retrofit 2 — How to Download Files from Server](https://futurestud.io/blog/retrofit-2-how-to-download-files-from-server)

     ```
     // option 1: a resource relative to your base URL
     @GET("/resource/example.zip")
     Call<ResponseBody> downloadFileWithFixedUrl();

     // option 2: using a dynamic URL
     @GET
     Call<ResponseBody> downloadFileWithDynamicUrlSync(@Url String fileUrl);
     ```

     ```
     FileDownloadService downloadService = ServiceGenerator.create(FileDownloadService.class);
     Call<ResponseBody> call = downloadService.downloadFileWithDynamicUrlSync(fileUrl);
     call.enqueue(new Callback<ResponseBody>() {  
         @Override
         public void onResponse(Call<ResponseBody> call, Response<ResponseBody> response) {
             if (response.isSuccess()) {
                 boolean writtenToDisk = writeResponseBodyToDisk(response.body());
             } else {
                 Log.d(TAG, "server contact failed");
             }
         }
         @Override
         public void onFailure(Call<ResponseBody> call, Throwable t) {
             Log.e(TAG, "error");
         }
     });

     private boolean writeResponseBodyToDisk(ResponseBody body) {  
         try {
             // todo change the file location/name according to your needs
             File futureStudioIconFile = new File(getExternalFilesDir(null) + File.separator + "Future Studio Icon.png");
             InputStream inputStream = null;
             OutputStream outputStream = null;
             try {
                 byte[] fileReader = new byte[4096];
                 long fileSize = body.contentLength();
                 long fileSizeDownloaded = 0;
                 inputStream = body.byteStream();
                 outputStream = new FileOutputStream(futureStudioIconFile);
                 while (true) {
                     int read = inputStream.read(fileReader);
                     if (read == -1) {
                         break;
                     }
                     outputStream.write(fileReader, 0, read);
                     fileSizeDownloaded += read;
                     Log.d(TAG, "file download: " + fileSizeDownloaded + " of " + fileSize);
                 }
                 outputStream.flush();
                 return true;
             } catch (IOException e) {
                 return false;
             } finally {
                 if (inputStream != null) {
                     inputStream.close();
                 }
                 if (outputStream != null) {
                     outputStream.close();
                 }
             }
         } catch (IOException e) {
             return false;
         }
     }
     ```

*    [Retrofit 2 — Cancel Requests](https://futurestud.io/blog/retrofit-2-cancel-requests)

```
String fileUrl = "http://futurestud.io/test.mp4";  
Call<ResponseBody> call =  
    downloadService.downloadFileWithDynamicUrlSync(fileUrl);
call.enqueue(new Callback<ResponseBody>() {  
    @Override
    public void onResponse(Call<ResponseBody> call, Response<ResponseBody> response) {
        Log.d(TAG, "request success");
    }
    @Override
    public void onFailure(Call<ResponseBody> call, Throwable t) {
        if (call.isCanceled()) {
        	Log.e(TAG, "request was cancelled");
        }
        else {
        	Log.e(TAG, "other larger issue, i.e. no network connection?");
        }
    }
});
    }

// something happened, for example: user clicked cancel button
call.cancel();  
}
```



* [Retrofit 2 — Reuse and Analyze Requests](https://futurestud.io/blog/retrofit-2-reuse-and-analyze-requests-2)

  * send the same request multiple times with clone method

    ```
    // correct usage:
    originalCall.enqueue(downloadCallback);
    // incorrect reuse:
    // if you need to make the same request again, don't use the same originalCall again!
    // it'll crash the app with a java.lang.IllegalStateException: Already executed.
    originalCall.enqueue(downloadCallback); // <-- would crash the app

    // correct usage with clone method
    Call<ResponseBody> newCall = originalCall.clone();  
    newCall.enqueue(downloadCallback);
    ```

* [Retrofit 2 — How to Change API Base Url at Runtime](https://futurestud.io/blog/retrofit-2-how-to-change-api-base-url-at-runtime-2)

* [Optional Path Parameters](https://futurestud.io/blog/retrofit-optional-path-parameters)

  * if your path parameter in the middle of the url, you can not pass "" 

    ```
    public interface TaskService {  
        @GET("tasks/{taskId}/subtasks")
        Call<List<Task>> getSubTasks(@Path("taskId") String taskId);
    }
    // pass "" to taskId will get url as follow
    https://your.api.url/tasks//subtasks 
    ```

  * you can not pass null to path parameter wherever the path parameter in

* [Retrofit 2 — How to Send Plain Text Request Body](https://futurestud.io/blog/retrofit-2-how-to-send-plain-text-request-body)

* [Retrofit 2 — Introduction to (Multiple) Converters](https://futurestud.io/blog/retrofit-2-introduction-to-multiple-converters)

* [Retrofit 2 — How to Upload Multiple Files to Server](https://futurestud.io/blog/retrofit-2-how-to-upload-multiple-files-to-server)

* [Retrofit 2 — Passing Multiple Parts Along a File with @PartMap](https://futurestud.io/blog/retrofit-2-passing-multiple-parts-along-a-file-with-partmap)