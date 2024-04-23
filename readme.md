## Steps
1. Install `componentize-py` (python module) and `wasmtime` (runtime)
2. Create the WIT world you'd like. Below is simple `hello.wit` that would print some string when `hello` function is called 
```wit
package example:hello;
world hello {
  export hello: func() -> string;
}
```
3. To understand the internal bindings, you can always run below; 
```commandline
componentize-py -d hello.wit -w hello bindings .
```
4. Create the app in python 
```python
import hello
class Hello(hello.Hello):
    def hello(self) -> str:
        return "Hello, World!"
```
5. Generate the component 
```commandline
componentize-py -d hello.wit -w hello componentize app -o app.wasm
```
