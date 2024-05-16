# ngx_core_patches

Enhance nginx core to implement more functions

## gx_http_slice_module_ext_1.21.4+.patch

This patch introduces a directive slice_allow_methods to control which request methods can be sliced. In addition, slice_check_etag and slice_check_last_modified are also introduced to control whether to check the consistency of these two headers.

### Directive

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

## ngx_http_listen_https_allow_http_1.21.4+.patch

This patch allows accepting http or https requests in the same port, which is useful for scenarios where special ports are used. The original work is from Tengine.

### Directive

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

### Directive

* **Syntax:** *proxy_set_header_inherit on | off;*

* **Default:** *proxy_set_header_inherit off;*

* **Context:** *http, server, location*

Allows the merge inheritance of proxy_set_header in receiving contexts.

* **Syntax:** *grpc_set_header_inherit on | off;*

* **Default:** *grpc_set_header_inherit off;*

* **Context:** *http, server, location*

Allows the merge inheritance of grpc_set_header in receiving contexts.