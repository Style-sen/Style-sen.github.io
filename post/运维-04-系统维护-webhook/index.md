# 运维-WEBHOOK



## [Webhook](https://github.com/adnanh/webhook)

1. 使用GO语言编写。
2. `sudo apt-get install webhook`安装。
3. Get或者Post都可以触发hook。
4. `service webhook status` 可以查看webhook状态。

### 使用

```
Usage of webhook:
  -cert string
        path to the HTTPS certificate pem file (default "cert.pem") HTTPS证书的路径
  -header value
        response header to return, specified in format name=value, use multiple times to set multiple headers
  -hooks string
        hook json文件的路径 (default "hooks.json")
  -hotreload
        watch hooks file for changes and reload them automatically
  -ip string
        ip the webhook should serve hooks on (default "0.0.0.0")
  -key string
        path to the HTTPS certificate private key pem file (default "key.pem")
  -nopanic
        do not panic if hooks cannot be loaded when webhook is not running in verbose mode
  -port int
        port the webhook should serve hooks on (default 9000)
  -secure
        use HTTPS instead of HTTP
  -urlprefix string
        url prefix to use for served hooks (protocol://yourserver:port/PREFIX/:hook-id) (default "hooks")
  -verbose
        show verbose output
```

#### 创建一个`hooks.json`文件

```
[
  {
    "id": "redeploy-webhook",
    "execute-command": "/var/scripts/redeploy.sh",
    "command-working-directory": "/var/webhook"
  }
]
```

1. 必须包含`id`和`execute-command`属性，其他属性为可选的。

属性列表如下：

| 属性 | 描述 |
| -- |--|
| id                                                                                           |  指定hook的ID，这个只用来创建HTTP endpoint (http://yourserver:port/hooks/your-hook-id)|
| execute-command                                                        | hook触发执行的命令|
| command-working-directory                                   | 指定hook执行命令的工作路径 |
| response-message                                                        | string回复|
| response-headers                                                          |   specifies the list of headers in format {"name": "X-Example-Header", "value": "it works"} that will be returned in HTTP response for the hook
| include-command-output-in-response                    |- boolean whether webhook should wait for the command to finish and return the raw output as a response to the hook initiator. If the command fails to execute or encounters any errors while executing the response will result in 500 Internal Server Error HTTP status code, otherwise the 200 OK status code will be returned.
| include-command-output-in-response-on-error | - boolean whether webhook should include command stdout & stderror as a response in failed executions. It only works if include-command-output-in-response is set to true.
| parse-parameters-as-json                                         |  - specifies the list of arguments that contain JSON strings. These parameters will be decoded by webhook and you can access them like regular objects in rules and pass-arguments-to-command.
|  pass-arguments-to-command                               | specifies the list of arguments that will be passed to the command. Check Referencing request values page to see how to reference the values from the request. If you want to pass a static string value to your command you can specify it as { "source": "string", "name": "argumentvalue" }
| pass-environment-to-command                            | specifies the list of arguments that will be passed to the command 作为环境变量. If you do not specify the "envname" field in the referenced value, the hook will be in format "HOOK_argumentname", otherwise "envname" field will be used as it's name. Check Referencing request values page to see how to reference the values from the request. If you want to pass a static string value to your command you can specify it as { "source": "string", "envname": "SOMETHING", "name": "argumentvalue" }
| pass-file-to-command                                                | specifies a list of entries that will be serialized as a file. Incoming data will be serialized in a request-temporary-file (otherwise parallel calls of the hook would lead to concurrent overwritings of the file). The filename to be addressed within the subsequent script is provided via an environment variable. Use envname to specify the name of the environment variable. If envname is not provided HOOK_ and the name used to reference the request value are used. Defining command-working-directory will store the file relative to this location, if not provided, the systems temporary file directory will be used. If base64decode is true, the incoming binary data will be base 64 decoded prior to storing it into the file. By default the corresponding file will be removed after the webhook exited.
| trigger-rule                                                                       | specifies the rule that will be evaluated in order to determine should the hook be triggered. Check Hook rules page to see the list of valid rules and their usage
| trigger-rule-mismatch-http-response-code      | specifies the HTTP status code to be returned when the trigger rule is not satisfied

#### Hook Rule


