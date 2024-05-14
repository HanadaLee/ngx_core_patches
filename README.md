# ngx_core_patches

Enhance nginx core to implement more functions

## ngx_http_slice_allow_methods_directive_1.21.4+.patch

This patch introduces a directive slice_allow_methods to control which request methods can be sliced.

### Directive

* **Syntax:** *slice_allow_methods GET | HEAD ...;*

* **Default:** *slice_allow_methods GET HEAD;*

* **Context:** *http, server, location*

Allow splitting responses into slices if the client request method is listed in this directive. Note that if the slice directive is unset or has the zero value, splitting the response into slices will still be disabled.

Test pass: Nginx 1.21.4 with OpenResty

## ngx_http_listen_https_allow_http_1.21.4+.patch

This patch allows accepting http or https requests in the same port, which is useful for scenarios where special ports are used. The original work is from Tengine.

### Directive

* **Syntax:** *listen address[:port] [ssl] **[https_allow_http]** ...;*

* **Default:** *listen *:80 | *:8000;*

* **Context:** *server*

When both the ssl and https_allow_http parameters are enabled for the listen directive, both https or http requests will be allowed.

Test pass: Nginx 1.21.4 with OpenResty

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

This patch additionally provides grpc support.

### Directive

* **Syntax:** *proxy_set_header_inherit on | off;*

* **Default:** *proxy_set_header_inherit off;*

* **Context:** *http, server, location*

Allows the merge inheritance of proxy_set_header in receiving contexts.

* **Syntax:** *grpc_set_header_inherit on | off;*

* **Default:** *grpc_set_header_inherit off;*

* **Context:** *http, server, location*

Allows the merge inheritance of grpc_set_header in receiving contexts.