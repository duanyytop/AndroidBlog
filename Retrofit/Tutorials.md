1. [Getting Started and Create an Android Client](https://futurestud.io/blog/retrofit-getting-started-and-android-client)

2. [Basic Authentication on Android](https://futurestud.io/blog/android-basic-authentication-with-retrofit)

3. [Token Authentication on Android](https://futurestud.io/blog/retrofit-token-authentication-on-android)

4. [OAuth on Android](https://futurestud.io/blog/oauth-2-on-android-with-retrofit)

5. [Multiple Query Parameters of Same Name](https://futurestud.io/blog/retrofit-multiple-query-parameters-of-same-name)

6. [Synchronous and Asynchronous Requests](https://futurestud.io/blog/retrofit-synchronous-and-asynchronous-requests)

7. [Send Objects in Request Body](https://futurestud.io/blog/retrofit-send-objects-in-request-body)

8. [Define a Custom Response Converter](https://futurestud.io/blog/retrofit-replace-the-integrated-json-converter)

9. [Add Custom Request Header](https://futurestud.io/blog/retrofit-add-custom-request-header)

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

10. [Optional Query Parameters](https://futurestud.io/blog/retrofit-optional-query-parameters)

   ```
   public interface TaskService {  
       @GET("/tasks")
       Call<List<Task>> getTasks(@Query("sort") String order);
   }

   // you will get the result
   https://your.api.com/tasks?sort=value-of-order-parameter
   ```

   * If you don’t want to pass it with the request, just pass **null** as the value during method call.

     ```
     // if you want to ignore query parameter, you should use Integer,Float,Long and pass null as the value
     service.getTasks(null);  
     ```

     > Retrofit skips `null` parameters and ignores them while assembling the request. Keep in mind, that **you can’t pass `null`for primitive data types like `int`, `float`, `long`, etc. Instead, use `Integer`, `Float`, `Long`, etc and the compiler won’t be grumpy.**

11. [How to Integrate XML Converter](https://futurestud.io/blog/retrofit-how-to-integrate-xml-converter)

12. [Using the Log Level to Debug Requests](https://futurestud.io/blog/retrofit-using-the-log-level-to-debug-requests)

   * add HttpLoggingInterceptor to get request and response logging

     The developers of OkHttp added a logging interceptor in release `2.6.0` for Retrofit2

     Retrofit 2 completely relies on OkHttp for any network operation. The developers of OkHttp have released a separate logging interceptor project, which implements logging for OkHttp. You can add it to your project with a quick edit of your`build.gradle`:

     ```
     compile 'com.squareup.okhttp3:logging-interceptor:3.4.1' 
     ```

     Since logging isn’t integrated by default anymore in Retrofit 2, we need to add a logging interceptor for OkHttp. Luckily OkHttp already ships with this interceptor and you only need to activate it for your OkHttpClient.

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

13. [How to Upload Files to Server](https://futurestud.io/blog/retrofit-how-to-upload-files)

14. [Series Round-Up](https://futurestud.io/blog/retrofit-series-round-up)

15. [Retrofit 2 — Upgrade Guide from 1.9](https://futurestud.io/blog/retrofit-2-upgrade-guide-from-1-9)

16. [Retrofit 2 — How to Upload Files to Server](https://futurestud.io/blog/retrofit-2-how-to-upload-files-to-server)

17. [Retrofit 2 — Log Requests and Responses](https://futurestud.io/blog/retrofit-2-log-requests-and-responses)

18. [Retrofit 2 — Hawk Authentication on Android](https://futurestud.io/blog/retrofit-2-hawk-authentication-on-android)

19. [Retrofit 2 — Simple Error Handling](https://futurestud.io/blog/retrofit-2-simple-error-handling)

20. [How to use OkHttp 3 with Retrofit 1](https://futurestud.io/blog/retrofit-how-to-use-okhttp-3-with-retrofit-1)

21. [Retrofit 2 — Book Update & Release Celebration](https://futurestud.io/blog/retrofit-2-book-update-release-celebration)

22. [Send Data Form-Urlencoded](https://futurestud.io/blog/retrofit-send-data-form-urlencoded)

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

23. [Send Data Form-Urlencoded Using FieldMap](https://futurestud.io/blog/retrofit-send-data-form-urlencoded-using-fieldmap)

24. [Retrofit 2 — Manage Request Headers in OkHttp Interceptor](https://futurestud.io/blog/retrofit-2-manage-request-headers-in-okhttp-interceptor)

25. [Retrofit 2 — How to Add Query Parameters to Every Request](https://futurestud.io/blog/retrofit-2-how-to-add-query-parameters-to-every-request)

26. [Retrofit 2 — Add Multiple Query Parameter With QueryMap](https://futurestud.io/blog/retrofit-2-add-multiple-query-parameter-with-querymap)

27. [Retrofit 2 — How to Use Dynamic Urls for Requests](https://futurestud.io/blog/retrofit-2-how-to-use-dynamic-urls-for-requests)

28. [Retrofit 2 — Url Handling, Resolution and Parsing](https://futurestud.io/blog/retrofit-2-url-handling-resolution-and-parsing)

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

     ​

29. [Retrofit 2 — Constant, Default and Logic Values for POST and PUT Requests](https://futurestud.io/blog/retrofit-2-constant-default-and-logic-values-for-post-and-put-requests)

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

30. [Retrofit 2 — How to Download Files from Server](https://futurestud.io/blog/retrofit-2-how-to-download-files-from-server)

31. [Retrofit 2 — Cancel Requests](https://futurestud.io/blog/retrofit-2-cancel-requests)

32. [Retrofit 2 — Reuse and Analyze Requests](https://futurestud.io/blog/retrofit-2-reuse-and-analyze-requests-2)

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

33. [Retrofit 2 — How to Change API Base Url at Runtime](https://futurestud.io/blog/retrofit-2-how-to-change-api-base-url-at-runtime-2)

34. [Optional Path Parameters](https://futurestud.io/blog/retrofit-optional-path-parameters)

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

35. [Retrofit 2 — How to Send Plain Text Request Body](https://futurestud.io/blog/retrofit-2-how-to-send-plain-text-request-body)

36. [Retrofit 2 — Introduction to (Multiple) Converters](https://futurestud.io/blog/retrofit-2-introduction-to-multiple-converters)

37. [Retrofit 2 — How to Upload Multiple Files to Server](https://futurestud.io/blog/retrofit-2-how-to-upload-multiple-files-to-server)

38. [Retrofit 2 — Passing Multiple Parts Along a File with @PartMap](https://futurestud.io/blog/retrofit-2-passing-multiple-parts-along-a-file-with-partmap)