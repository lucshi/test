WebAssembly Micro Runtime
=========================
WebAssembly Micro Runtime (WAMR) is a small footprint and standalone WebAssembly (WASM) runtime. It provides a framework for dynamic WASM application management.

Features
=========================
- WASM interpreter (AOT is planned)
- Provide built-in Libc subset, support side_module=1 EMCC compilation option only
- Provide APIs for embedding runtime into production software
- Provide mechanism for exporting native APIs to WASM applications
- Support programming firmware apps in multi languages (C/C++/Java/Rust/Go/TypeScript etc.)
- App sandbox execution environment on embedded OS
- Pure asynchronized programming model
- Menu configuration for easy platform integration
- Support micro service and pub-sub event inter-app communication models
- Easy to extend to support remote FW application management from host or cloud

Architecture
=========================
WAMR is basically consist of three portions, WASM runtime engine, memory management, messaging and micro service support module.

<img src="./pics/architecture.PNG" width="80%" height="80%">
 
  
The core function of WAMR is loading and running WASM application binary from local, and WASM applicaiton execution starts from the main entry. Belowing sections are about how to build WAMR core and WASM app, as well as run the WASM app by loading into WASM core.

Build WAMR Core
=========================
Please follow below instructions to build WAMR core on different platforms.

Linux
-------------------------
``` Bash
cd products/linux/
mkdir build
cd build
cmake ..
make
```
Zephyr
-------------------------
You need download Zephyr source code first and embeded WAMR into it.
``` Bash
git clone https://github.com/zephyrproject-rtos/zephyr.git
cd zephyr/samples/
cp -a <iwasm_dir>/products/zephyr/simple .
cd simple
ln -s <iwam_dir> iwasm
ln -s <shared_lib_dir> shared-lib
mkdir build && cd build
source ../../../zephyr-env.sh
cmake -GNinja -DBOARD=qemu_x86 ..
ninja
```

Build WASM app
=========================
A popular method to build out WASM binary is to use ```emcc```. 
Assuming you are using Linux. Please install emcc from Emscripten EMSDK following below steps:
```
git clone https://github.com/emscripten-core/emsdk.git
emsdk install latest
emsdk activate latest
```
add ```./emsdk_env.sh``` into path to ease future use, or source it everytime.
Emscripten website provides other installtion method beyond Linux.

todo: user should copy the app-libs folder into project and include and build.

You can write a simple ```test.c```as the first sample.
``` C
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char **argv)
{
  char *buf;

  printf("Hello world!\n");

  buf = malloc(1024);
  if (!buf) {
    printf("malloc buf failed\n");
    return -1;
  }

  printf("buf ptr: %p\n", buf);

  sprintf(buf, "%s", "1234\n");
  printf("buf: %s", buf);

  free(buf);
  return 0;
}
```
Use below emcc commmand to build the WASM C source code into WASM binary.
``` Bash
emcc -g -O3 *.c -s WASM=1 -s SIDE_MODULE=1 -s ASSERTIONS=1 -s STACK_OVERFLOW_CHECK=2 \
                -s TOTAL_MEMORY=65536 -s TOTAL_STACK=4096 -o test.wasm
```
You will get ```test.wasm``` which is the WASM app binary.

Run WASM app
========================
Assume you are using Linux, the command to run the test.wasm is 
``` Bash
cd iwasm/products/linux/bin
./iwasm test.wasm
```
You will get output:
```
Hello world!
buf ptr: 0x000101ac
buf: 1234
```
If you would like to run test app on Zephyr, we have embedded test sample into its OS image. You need to execute 
```
ninja run
```

Embed WAMR into software production
=====================================
WAMR provided methodology to embed WAMR into your own software product, and defines APIs to enable software code (native) to invoke embedded WASM code.
<img src="./pics/embed.PNG" width="60%" height="60%">
The native code and WASM code execution flows are connected, but the stacks are seperated. WAMR enables the native code to invoke WASM code as invoking own native code transparently. Meanwhile WAMR guarantees the WASM code running inside sandbox.

A typical WAMR APIs usage is as below:
``` C
  wasm_module_t module;
  wasm_module_inst_t inst;
  wasm_function_inst_t func;
  wasm_exec_env_t env;
  wasm_runtime_init();
  module = wasm_runtime_load(buffer, size, err, err_size);
  inst = wasm_runtime_instantiate(module, 0, err, err_size);
  func = wasm_runtime_lookup_function(inst, "fib", "(i32i32");
  env = wasm_runtime_create_exec_env(stack_size);

  if (!wasm_runtime_call_wasm(inst, env, func, 1, argv_buf) ) {
          wasm_runtime_clear_exception(inst);
    }

  wasm_runtime_destory_exec_env(env);
  wasm_runtime_deinstantiate(inst);
  wasm_runtime_unload(module);
  wasm_runtime_destroy();
```


WASM application library and extension
=======================================
WAMR provides a set of basic application APIs.There are 3 sources of APIs for programming the WASM application:
- Built-in APIs: WAMR has already provided a minimal API set for developers. The minimal API includes:
  - Libc APIs, which is the minimal Libc APIs like memory allocation and string copy etc. It is defined in lib/app-libs/libc/lib-base.h;
  - Base library, which is the basic support like communication, timers and request/sub etc. It is defined is lib/app-libs/base/wasm_app.h;
  - Extension library, which is a reference code of library extension. Currently we provide an example of extending library to support sensors, the header file lib/app-libs/extension/sensor/sensor.h. It is a reference implementation for board vendors.
- 3rd party APIs: Programmer can download include any 3rd party C source code, and added into their own WASM app source tree.
- Platform native APIs: The board vendors define these APIs during their making board firmware. They are provided WASM application to invoke like built-in and 3rd party APIs. In this way board vendors extend APIs which can make programmers develop more complicated WASM apps.

<img src="./pics/extend_library.PNG" width="60%" height="60%">





Submit issues and request
=========================
[Click here to submit. Your feedback is always welcome!](https://github.com/lucshi/test/issues/new)

