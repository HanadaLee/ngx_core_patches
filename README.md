# ngx_core_patches

Enhance nginx core to implement more functions

## ngx_http_slice_filter_module_ext_1.21.4+.patch

This patch introduces a directive slice_allow_methods to control which request methods can be sliced. In addition, slice_check_etag and slice_check_last_modified are also introduced to control whether to check the consistency of these two headers.

* **Syntax:** *slice_allow_methods GET | HEAD ...;*

* **Default:** *slice_allow_methods GET HEAD;*

* **Context:** *http, server, location*

Allow splitting responses into slices if the client request method is listed in this directive. Note that if the slice directive is unset or has the zero value, splitting the response into slices will still be disabled.

* **Syntax:** *slice_check_etag on | off;*

* **Default:** *slice_check_etag on;*

* **Context:** *http, server, location*

Whether to check the consistency of the Etag header in the slice. If it is enabled, the request will be terminated and an error will be reported when Etag mismatch in slice response occurs.

* **Syntax:** *slice_check_last_modified on | off;*

* **Default:** *slice_check_last_modified off;*

* **Context:** *http, server, location*

Whether to check the consistency of the Last-Modified header in the slice. If it is enabled, the request will be terminated and an error will be reported when Last-Modified mismatch in slice response occurs.

## ngx_http_sub_filter_module_ext_1.25.3+.patch

This patch introduces a directive sub_filter_bypass to bypass sub_filter based on the value of a set of variables.

* **Syntax:** *sub_filter_bypass string ...;*

* **Default:** *—*

* **Context:** *http, server, location*

Defines conditions under which the response will not be replaced. If at least one value of the string parameters is not empty and is not equal to “0” then the response will not be replaced.

```
sub_filter_bypass $cookie_nocache $arg_nocache$arg_comment;
sub_filter_bypass $http_pragma    $http_authorization;
```

## ngx_http_listen_https_allow_http_1.21.4+.patch

This patch allows accepting http or https requests in the same port, which is useful for scenarios where special ports are used. The original work is from [Tengine](https://github.com/alibaba/tengine).

* **Syntax:** *listen address[:port] [ssl] **[https_allow_http]** ...;*

* **Default:** *listen *:80 | *:8000;*

* **Context:** *server*

When both the ssl and https_allow_http parameters are enabled for the listen directive, both https or http requests will be allowed.

## ngx_http_proxy_and_grpc_header_control_inherit_1.25.3+.patch

This patch introduces 'inherit' argument to proxy_set_header
directive. When marked as such it will be merge inherited into child
contexts regardless of the presence of other proxy_set_header
directives within those contexts.

This patch also introduces the 'proxy_set_header_inherit' directive
which blocks the merge inheritance in receiving contexts when set to off.

The purpose of the added mechanics is to reduce repetition within the
nginx configuration for universally set (or boilerplate) request
headers, while maintaining flexibility to set additional headers for
specific paths.

There is no change in behavior for existing configurations.

The original patch is from https://mailman.nginx.org/pipermail/nginx-devel/2023-November/XUGFHDLSLRTFLWIBYPSE7LTXFJHNZE3E.html

This patch additionally provides grpc support, Also allows setting the :authory header (From https://github.com/api7/apisix-nginx-module/blob/main/patch/1.25.3.1/nginx-grpc_set_header_authority.patch).

* **Syntax:** *proxy_set_header_inherit on | off;*

* **Default:** *proxy_set_header_inherit off;*

* **Context:** *http, server, location*

Allows the merge inheritance of proxy_set_header in receiving contexts.

* **Syntax:** *grpc_set_header_inherit on | off;*

* **Default:** *grpc_set_header_inherit off;*

* **Context:** *http, server, location*

Allows the merge inheritance of grpc_set_header in receiving contexts.

## ngx_http_realip_module_ext_1.25.3+.patch

* **Syntax:** *real_ip_header field | X-Real-IP | X-Forwarded-For | proxy_protocol;*

* **Default:** *real_ip_header X-Real-IP;*

* **Context:** *http, server, location*

Defines the request header fields whose value will be used to replace the client address. 

If multiple request fields are defined, the header values ​​will be checked in the order defined in the configuration, and the first header with a valid value will be used:

```
real_ip_header X-Real-IP Cdn-Src-Ip X-Forwarded-For;
```
The values ​​of the above headers will be checked in turn until a valid value is found.

The request header field value that contains an optional port is also used to replace the client port (1.11.0). The address and port should be specified according to RFC 3986.

The proxy_protocol parameter (1.5.12) changes the client address to the one from the PROXY protocol header. The PROXY protocol must be previously enabled by setting the proxy_protocol parameter in the listen directive.

## ngx_http_rewrite_module_if_extend_1.25.3+.patch

The original work [SEnginx](https://github.com/NeusoftSecurity/SEnginx)

The "If Extend" module extends the "if" directive of the original NGINX "rewrite" module. It has the following features:

### more conditions for "if" directive

Supports matching multiple conditions and the matching conditions can be "and" or "or".
Except for the original "if" condition operators, also supports:
* <
* \>
* !< or >=
* !> or <=

### "if" with multi conditions

* **Syntax:** *if_all (condition 1) (condition 2) ... {...}*

* **Default:** *-*

* **Context:** *server, location*

Specify multiple conditions. If all conditions are true, then execute the directives inside the braces"{}". This directive has the same behavior as the original "if" directive, but the following condition operators are added:
* <
* \>
* !< or >=
* !> or <=

Example:
```
if_all ($remote_addr = 192.168.1.1) ($http_user_agent ~ 'Mozilla') ($server_port > 808) {
    return 404;
}
```

* **Syntax:** *if_any (condition 1) (condition 2) ... {...}*

* **Default:** *-*

* **Context:** *server, location*

Specify multiple conditions. If any condition is true, then execute the directives inside the braces "{}". The other parts are the same as the "if_all" directive.

Known limits: 

The last character of a conditional statement cannot be ')', even if it is enclosed in quotes. For example, the following expression will cause a configuration test error.
```
if_all ($test_var = "test)") ($http_user_agent ~ 'Mozilla') ($server_port > 808) {
    return 404
}
```
If you must use a string ending with ')'， you might consider using a variable to back it up.
```
set $value "test)";
if_all ($test_var = $value) ($http_user_agent ~ 'Mozilla') ($server_port > 808) {
    return 404
}
```
If it is a regular expression, we can avoid using ')' at the end in many ways.