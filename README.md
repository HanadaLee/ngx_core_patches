# ngx_http_slice_filter_module_patches


Enhance ngx_http_slice_filter_module to implement the following functions

## Directive

- Syntax: slice_allow_methods GET | HEAD ...;
- Default: slice_allow_methods GET HEAD;
- Context: http, server, location

Allow splitting responses into slices if the client request method is listed in this directive. Note that if the slice directive is unset or has the zero value, splitting the response into slices will still be disabled.
