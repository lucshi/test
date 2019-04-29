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

<img src="./pics/architecture.PNG" width="80%" height="80%">



Extension 
=========================
WAMR defined methodology and APIs to extend library, support app managment, extend to more language supppr, and enabled on more platforms.
A typical extention architecure is as below:

- App manager is the component to install and uninstall WASM apps from host or cloud.
- Communication is enabled inter WASM app as well as between WASM app and host/cloud
- Runtime glue and API extention is a layer to easily integrate other runtime, e.g. Jerryscript, Intel Java micro runtime and Lua runtime etc. into WAMR
<img src="./pics/architecture_extend.PNG" width="120%" height="120%">


Programming models 
=========================
After extention, WAMR supports two typical programming models, micro service model and subscription model. Each WASM app runs in dedicate thread and they communicate in pure asynchronized style so there is no blocking operations.

Micro service model
-------------------------
The micro service model is also referred as request and response model. One WASM app acts as server app which provides a specific service. Other WASM apps or host/cloud apps request from that service and get response.
<img src="./pics/request.PNG" width="60%" height="60%">

Subscription model
-------------------------
The micro service model is also referred as monitor model. One WASM app acts as the event broadcastor. It broadcast events to WASM apps or host/cloud apps to notify their subscribed events occur.
<img src="./pics/sub.PNG" width="60%" height="60%">

Use case on sensor hub firmeware
-------------------------
Programmers follow below steps to finish their app development:
- write WASM app code for firmware using C/C++ or other languages
- compile and test
- pack host app and WSAM app
- deploy in host
- host app installs WASM app onto device
- WASM app filters and fuses sensor events and provide UI display and interaction
<img src="./pics/workflow.PNG">



Security
=========================

Discussing
=========================
- [submit issue](https://github.com/meolu/walle-web/issues/new)

