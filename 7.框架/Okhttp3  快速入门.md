
## 前言

**[OkHttp3](https://square.github.io/okhttp/)** 是一个流行的、轻量级的 HTTP 客户端库，主要用于 Android 和 Java 应用程序中处理 HTTP 和 HTTPS 请求。

>**OkHttp** 的主要功能包括：异步和同步请求、HTTP/2 支持、自动缓存响应、高效的连接池管理、文件上传和下载、超时设置等等。

## 没有这些框架以前的请求示例

### 使用 HttpURLConnection

标准库，无需额外依赖；轻量级。但代码冗长；不够灵活，异常处理复杂。

**示例代码**：

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.net.HttpURLConnection;
import java.net.URL;

public class HttpURLConnectionExample {
    public static void main(String[] args) {
        String url = "https://baidu.com/";
        String jsonBody = "{\"key\":\"value\"}"; // 请求体
        try {
            // 创建 URL 对象
            URL urlObj = new URL(url);
            
            // 打开连接
            HttpURLConnection connection = (HttpURLConnection) urlObj.openConnection();
            
            // 设置请求方法 POST
            connection.setRequestMethod("POST");
            
            // 设置请求头
            connection.setRequestProperty("Content-Type", "application/json");
            connection.setRequestProperty("Accept", "application/json");
            
            // 设置允许写入数据
            connection.setDoOutput(true);
            
            // 写入请求体
            try (OutputStream os = connection.getOutputStream()) {
                os.write(jsonBody.getBytes());
                os.flush();
            }
            
            // 读取响应
            int responseCode = connection.getResponseCode();
            System.out.println("Response Code: " + responseCode);
            
            // 读取响应体
            try (BufferedReader br = new BufferedReader(new InputStreamReader(connection.getInputStream()))) {
                StringBuilder response = new StringBuilder();
                String line;
                while ((line = br.readLine()) != null) {
                    response.append(line);
                }
                System.out.println("Response Body: " + response.toString());
            }
            
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

```

## 使用 Okhttp3 框架

### 发送同步请求

**GET 示例代码**：

```Java
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;

import java.io.IOException;

public class OkHttpExample {
    public static void main(String[] args) {
        OkHttpClient client = new OkHttpClient();

        // 创建请求
        Request request = new Request.Builder()
                .url("https://baidu.com/")
                .build();

        // 发送同步 GET 请求
        try (Response response = client.newCall(request).execute()) {
            if (response.isSuccessful()) {
                System.out.println(response.body().string());
            } else {
                System.err.println("Request failed: " + response.code());
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```

**POST** **示例代码**：

```Java
import okhttp3.MediaType;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.RequestBody;
import okhttp3.Response;

import java.io.IOException;

public class PostJsonExample {
    public static void main(String[] args) {
        OkHttpClient client = new OkHttpClient();

        // 构建 JSON 请求体
        String json = "{" +
                "\"id\":\"001\"," +
                "\"name\":\"Herbert501\"," +
                "\"personUrl\":\"blog.kangyaocoding.top\"" +
                "}";
        MediaType JSON = MediaType.get("application/json; charset=utf-8");
        RequestBody body = RequestBody.create(json, JSON);

        // 创建 POST 请求
        Request request = new Request.Builder()
                .url("https://baidu.com/")
                .addHeader("Content-Type", "application/json")
                .post(body)
                .build();

        // 发送同步 POST 请求
        try (Response response = client.newCall(request).execute()) {
            if (response.isSuccessful()) {
                System.out.println(response.body().string());
            } else {
                System.err.println("Request failed: " + response.code());
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```
### 发送异步请求

**GET 请求示例代码**：

```Java
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;
import okhttp3.Callback;

import java.io.IOException;

public class AsyncOkHttpExample {
    public static void main(String[] args) {
        OkHttpClient client = new OkHttpClient();

        // 创建请求
        Request request = new Request.Builder()
                .url("https://baidu.com/")    // 设置请求 url
                .addHeader("Authorization", "Bearer blog.kangyaocoding.top")    // 设置用于身份验证请求头
                .addHeader("Accept", "application/json")    // 设置客户端期望的响应数据类型
                .addHeader("User-Agent", "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.0.0 Safari/537.36")    // 设置用户代理
                .build();

        // 发送异步 GET 请求
        client.newCall(request).enqueue(new Callback() {
            @Override
            public void onFailure(okhttp3.Call call, IOException e) {
                e.printStackTrace();
            }

            @Override
            public void onResponse(okhttp3.Call call, Response response) throws IOException {
                if (response.isSuccessful()) {
                    System.out.println(response.body().string());
                } else {
                    System.err.println("Request failed: " + response.code());
                }
            }
        });

        System.out.println("Request sent...");
    }
}

```

**POST** **请求示例代码**：

```Java
import okhttp3.*;

import java.io.IOException;

public class OkHttpAsyncPostExample {
    public static void main(String[] args) {
        // OkHttpClient 实例
        OkHttpClient client = new OkHttpClient();

        // 请求 URL
        String url = "https://baidu.com/";

        // 构建 JSON 请求体
        String json = "{" +
                "\"id\":\"001\"," +
                "\"name\":\"Herbert501\"," +
                "\"personUrl\":\"blog.kangyaocoding.top\"" +
                "}";
        RequestBody body = RequestBody.create(json, MediaType.parse("application/json"));

        // 构建 POST 请求
        Request request = new Request.Builder()
                .url(url)                            // 设置请求 URL
                .post(body)                          // 设置 POST 方法及请求体
                .addHeader("Authorization", "Bearer your_token") // 添加请求头
                .addHeader("Content-Type", "application/json")   // 设置内容类型
                .build();

        // 异步发送 POST 请求
        client.newCall(request).enqueue(new Callback() {
            @Override
            public void onFailure(Call call, IOException e) {
                // 请求失败时处理
                e.printStackTrace();
                System.out.println("Request failed: " + e.getMessage());
            }

            @Override
            public void onResponse(Call call, Response response) throws IOException {
                try {
                    if (response.isSuccessful()) {
                        // 成功响应结果
                        System.out.println("Response Code: " + response.code());
                        System.out.println("Response Body: " + response.body().string());
                    } else {
                        // 错误状态码
                        System.err.println("Request Failed: " + response.code());
                        System.err.println("Error Body: " + response.body().string());
                    }
                } finally {
                    // 关闭响应体，防止资源泄漏
                    response.close();
                }
            }
        });

        // 主线程继续运行，不会因异步请求阻塞
        System.out.println("Async POST request sent!");
    }
}

```

## 总结

1. 使用 **OkHttp3** 实现常见的同步和异步 HTTP GET 和 POST 请求。
2. 设置请求头、处理 JSON 数据，并解决实际开发中的常见问题。

## 想了解更多？

如果你对 **OkHttp3** 和 Java 开发感兴趣，或者希望获取更多关于 HTTP 请求优化的实用技巧，欢迎访问我的博客 [哈利的小屋](https://blog.kangyaocoding.top)。这里还有更多精彩内容等你探索！