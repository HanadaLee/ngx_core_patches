# ngx_core_patches

Enhance nginx core to implement more functions

## ngx_http_slice_allow_methods_directive_1.21.4+.patch

### Directive

- Syntax: slice_allow_methods GET | HEAD ...;
- Default: slice_allow_methods GET HEAD;
- Context: http, server, location

Allow splitting responses into slices if the client request method is listed in this directive. Note that if the slice directive is unset or has the zero value, splitting the response into slices will still be disabled.

Test pass: Nginx 1.21.4 with OpenResty

## ngx_http_listen_https_allow_http_1.21.4+.patch

### Directive

- Syntax: listen address[:port] [ssl] **[https_allow_http]** ...;
- Default: listen *:80 | *:8000;
- Context: server

When both the ssl and https_allow_http parameters are enabled for the listen directive, both https or http requests will be allowed. This patch comes from Tengine.

Test pass: Nginx 1.21.4 with OpenResty