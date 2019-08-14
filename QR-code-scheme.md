# 基本定义

定义 `base64` 为 `URL safe base64`, 且不带 `padding` (没有末尾的等于号), 具体格式如下：

`
ssr://base64(host:port:protocol:method:obfs:base64pass/?obfsparam=base64param&protoparam=base64param&remarks=base64remarks&group=base64group&udpport=0&uot=0&ot_enable=0&ot_domain=base64domain&ot_path=base64path)
`

其中, `base64pass` 及之前以 `:` 分隔的, 不可省略, 而 `/?` 及其后面的内容, 可按需要写上.

字符串使用 `UTF8` 编码, 编码后必须以 `urlsafebase64` 编码，包括密码、混淆参数、协议参数、备注、group,

`udpport` 参数及 `uot` 目前仅 C# 客户端支持

示例：
```
服务器IP： 127.0.0.1
端口： 1234
密码： aaabbb
加密： aes-128-cfb
协议： auth_aes128_md5
协议参数： （空）
混淆： tls1.2_ticket_auth
混淆参数： breakwa11.moe
备注： 测试中文
```
生成的带备注结果：
`
ssr://MTI3LjAuMC4xOjEyMzQ6YXV0aF9hZXMxMjhfbWQ1OmFlcy0xMjgtY2ZiOnRsczEuMl90aWNrZXRfYXV0aDpZV0ZoWW1KaS8_b2Jmc3BhcmFtPVluSmxZV3QzWVRFeExtMXZaUSZyZW1hcmtzPTVyV0w2Sy1WNUxpdDVwYUg
`

生成的不带备注的标准结果（结果唯一）：
`
ssr://MTI3LjAuMC4xOjEyMzQ6YXV0aF9hZXMxMjhfbWQ1OmFlcy0xMjgtY2ZiOnRsczEuMl90aWNrZXRfYXV0aDpZV0ZoWW1KaS8_b2Jmc3BhcmFtPVluSmxZV3QzWVRFeExtMXZaUQ
`

如果你生成的不带备注的结果结果与上面的不一致，那么请检查实现代码，否则可能导致部分环境下识别错误。

多链接组合
用于同时导入或导出多个链接使用

标准导出格式形如：
```
ssr://aaa
ssr://bbb
ssr://ccc
```
或者
```
ssr://aaa ssr://bbb ssr://ccc
```
即使用换行分隔或空格分隔，注意换行可能因平台不同而产生三种不同的换行格式

其它格式例如使用"|"作为分隔符，尽管多数客户端仍然能识别，但也不建议使用此格式，请不要以客户端能识别作为判断标准。
