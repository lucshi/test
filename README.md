WebAssembly Micro Runtime
=========================
WebAssembly Micro Runtime (WAMR) is a small footprint standalone WebAssembly(WASM) runtime. By default it support running WASM binary on resource restricted (low as 150KB total memory) devices.

Feature
=========================
- Support programming fw apps in multi languages (C/C++/Java/Rust/Go/TypeScript etc)
- App sandbox execution environment on embedded OS
- Pure asynchronized communication
- Menu configuration for easy platform integration
- Support micro service and pub-sub event inter-app communication models
- Easy to extend to support remote FW application management from host or cloud

Architecture
=========================
WAMR is basically consist of three portions, WASM runtime engine, memory management, messaging and micro service support module.

![](./pics/architecture.PNG)



Extension 
=========================
WAMR defined methodology and APIs to extend library, support app managment, extend to more language supppr, and enabled on more platforms.
A typical extention architecure is as below:

- App manager is the component to install and uninstall WASM apps from host or cloud.
- Communication is enabled inter WASM app as well as between WASM app and host/cloud
- Runtime glue and API extention is a layer to easily integrate other runtime, e.g. Jerryscript, Intel Java micro runtime and Lua runtime etc. into WAMR

![](./pics/architecture_extend.PNG)

Program models 
=========================
After extention, WAMR supports two typical programming models, micro service model and subscription model. Each WASM app runs in dedicate thread and they communicate in pure asynchronized style so there is no blocking operations.

Micro service model
-------------------------

Subscription model
-------------------------


Security
=========================

Discussing
=========================
- [submit issue](https://github.com/meolu/walle-web/issues/new)

