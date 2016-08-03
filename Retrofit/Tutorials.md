1. [Getting Started and Create an Android Client](https://futurestud.io/blog/retrofit-getting-started-and-android-client)

2. [Basic Authentication on Android](https://futurestud.io/blog/android-basic-authentication-with-retrofit)

3. [Token Authentication on Android](https://futurestud.io/blog/retrofit-token-authentication-on-android)

4. [OAuth on Android](https://futurestud.io/blog/oauth-2-on-android-with-retrofit)

5. [Multiple Query Parameters of Same Name](https://futurestud.io/blog/retrofit-multiple-query-parameters-of-same-name)

6. [Synchronous and Asynchronous Requests](https://futurestud.io/blog/retrofit-synchronous-and-asynchronous-requests)

7. [Send Objects in Request Body](https://futurestud.io/blog/retrofit-send-objects-in-request-body)

8. [Define a Custom Response Converter](https://futurestud.io/blog/retrofit-replace-the-integrated-json-converter)

9. [Add Custom Request Header](https://futurestud.io/blog/retrofit-add-custom-request-header)

10. [Optional Query Parameters](https://futurestud.io/blog/retrofit-optional-query-parameters)

11. [How to Integrate XML Converter](https://futurestud.io/blog/retrofit-how-to-integrate-xml-converter)

12. [Using the Log Level to Debug Requests](https://futurestud.io/blog/retrofit-using-the-log-level-to-debug-requests)

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

     ​

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

     ​

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