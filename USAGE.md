# Getting started with wasm3

For a less guided getting started, check out [wasm3.h](https://github.com/wasm3/wasm3/blob/master/source/wasm3.h) and the [platform](https://github.com/wasm3/wasm3/tree/master/platforms) examples.

## Instantiating the runtime

TODO

## Function Binding

Exposing C functions to the runtime requires first creating an m3 compatible function wrapping the desired C function, then binding this to the runtime as appropriate. For example bindings, check out the [esp32 wasi](https://github.com/wasm3/wasm3/blob/master/platforms/esp32-idf-wasi/main/m3_api_esp_wasi.c) implementation.

wasm3 compatible functions are created using the `m3ApiRawFunction` macro with the created function name. The `m3ApiReturnType`, `m3ApiGetArgMem`, and `m3ApiGetArg` functions are used to map between the WASM stack and C variables for use within the function call.

```
m3ApiRawFunction(m3_log_write)
{
    // Load arguments
    m3ApiReturnType  (uint32_t)           // Return type for the function
    m3ApiGetArgMem   (uint8_t*, buff)     // Fetch this argument from memory
    m3ApiGetArg      (uint32_t, buff_len) // Fetch this argument from the stack
    
    // Check the runtime is available
    if (runtime == NULL) { m3ApiReturn(__WASI_EINVAL); }
    
    // Do whatever you need to do
    ...
    
    
    // Return your result
    m3ApiReturn(__WASI_ESUCCESS);
}
```


Functions are bound to the runtime using `m3LinkRawFunction` declared in `wasm3.h`:

```
M3Result  m3_LinkRawFunction  (IM3Module            io_module,      // wasm3 module to bind function into
                              const char * const    i_moduleName,   // module name (scope for calling the function)
                              const char * const    i_functionName, // function name (how you'll call it)
                              const char * const    i_signature,    // method signature (see below)
                              M3RawCall             i_function)     // function pointer
```

Method signatures inform the runtime of the expected arguments (and are further documented TODO: here), these consit of a string with the return type, an opening bracket, then `i` for each integer argument and `*` for any memory based argument.
For example, a function returning an integer that takes a pointer and a length would have the signature `i(*i)`.

, for example:
```
    m3_LinkRawFunction (module, idk, "delay_ms", "i(i)", &m3_delay_ms);
```


## Executing functions

TODO

## Tearing down the runtime

TODO

