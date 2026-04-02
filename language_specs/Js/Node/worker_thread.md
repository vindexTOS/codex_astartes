When you call `new Worker()`, Node.js isn't just starting a thread; it is starting a **completely new V8 Isolate**. This involves:

- **V8 Initialization:** Booting a fresh instance of the V8 JavaScript engine.
    
- **New Event Loop:** Setting up a separate libuv event loop.
    
- **Memory Overhead:** Allocating a fresh heap (roughly **10MB–30MB** of RAM just to exist).
    
- **Module Loading:** Re-parsing and re-compiling the JavaScript file you want the worker to run.