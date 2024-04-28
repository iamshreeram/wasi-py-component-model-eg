## Steps
Install python modules - `componentize-py`, `wasmtime-py` and Runtime -  `wasmtime` 
1. Create the **WIT world** you'd like. Below is simple `hello.wit` that would print some string when `hello` function is called 
```wit
package example:hello;
world hello {
  export hello: func() -> string;
}
```
2. To understand the internal bindings, you can always run below and create the **GUEST**; (Optional)
```commandline
componentize-py -d hello.wit -w hello bindings hello_guest
```
3. Create the module in python and save it with name of the `app.py` (**ACTUAL APP**)
```python
import hello
class Hello(hello.Hello):
    def hello(self) -> str:
        return "Hello, World!"
```
4. Generate the component `app.wasm` with below command from `hello.wit` and the module that was created above `app.py`
```commandline
componentize-py -d hello.wit -w hello componentize --stub-wasi app -o app.wasm
```
or 
```
componentize-py -d hello.wit -w hello componentize app -o app.wasm 
```

5. Create the host-side bindings for the component `app.wasm`
```commandline
python3 -m wasmtime.bindgen app.wasm --out-dir hello_host
```

6. Now, we can write a simple host app using those bindings:
```python
from hello_host import Root
from wasmtime import Config, Engine, Store

config = Config()
config.cache = True
engine = Engine(config)
store = Store(engine)
hello = Root(store)
print(f"component says: {hello.hello(store)}")
```