基本上是和c/c++不同的语法的汇集和复习.
## rust引用
- 在rust中字符串字面量是对于特定位置的一个不可变引用.  
```rust
fn main() {
let s = "Hello, world!";
}
```
这里的s的类型是&str.  
对于字符串可以用&str来接受参数,但&String并不能接受&str这样一般字符串处理都是用&str.
当我们创建一个关于字符串的切片的时候,我们也无法改变这个字符串,这要求我们创建一个可变的引用指针.而同时存在一个可变的引用指针和不可变的引用指针是不被rust允许的.  
## rust 枚举
实际上rust的枚举并不像c/c++一样像是一个整数类型,相反像是一个unit类型.枚举的多个选择可以被绑定为多个类型的变量.而通过match这样的类似swich case的语法来实现不同的分支.  
match的语法类似:
```rust
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter,
}
fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => {
            println!("Lucky penny!");
            1
        }
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
```
default可以用`_`来匹配所有的其他枚举值.
如果只需要一个特殊值的判断的话可以通过if let 指令来进行.
```rust
enum UsState {
   Alabama,
   Alaska,
}

enum Coin {
   Penny,
   Nickel,
   Dime,
   Quarter(UsState),
}
let coin = Coin::Penny;
let mut count = 0;
if let Coin::Quarter(state) = coin {
    println!("State quarter from {:?}!", state);
} else {
    count += 1;
}
}
```
这里会检查coin是不是quarter,然后将这个coin的UsState的枚举值绑定到state上,通过这样来解析枚举值.  但是如果你写的不是变量名而是字面量,类似数字42这种,就会是判断是否精确匹配.

- rust中的NULL被包装在一个Option< T >的一个枚举中.这被定义在标准库中.  
```rust
fn main() {
	enum Option<T> {
	    Some(T),
	    None,
	}
}
```
这意味这其他类型都可以不用空值判断.因为一定存在值,如果有没有初始化的变量rust编译器会提示无法通过.    
这里对于option类型的处理需要通过match来穷举判断  

## rust的模块:  
模块上的 pub 关键字只允许其访问权但是内部的内容还是private的,还有平级的mod之间是互相可见的也就是平级的可以直接访问,但模块下的内容或者子模块需要他们pub修饰.如果是外部的访问就需要pub修饰否则会报错.  
嵌套路径:  
```rust
use std::io::{self, Write};
```  

## rust 泛型编程
和c++差不太多,但是更智能一些.
```rust
struct Point<T> {
    x: T,
    y: T,
}
fn main() {
    let integer = Point { x: 5, y: 10 };
    let float = Point { x: 1.0, y: 4.0 };
}
```
这里会自动推断,只要x,y类型一致,那么就可以通过编译.而且和c++一致,在编译的时候实际上会针对代码生成多个单态的定义代码.对于一个泛型的结构体,impl也需要声明泛型:
```rust
struct Point<T> {
    x: T,
    y: T,
}
impl<T> Point<T> {
    fn x(&self) -> &T {
        &self.x
    }
}
```
这里的`impl<T>`是说明要为一个泛型为T的结构体实现方法.
## rust 错误处理
panic!直接终止整个程序.可以通过
```bash
$ RUST_BACKTRACE=1 cargo run
```
查看栈的backtrace.错误的处理和一个内置的枚举关系很大:
```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```
这里很多函数的返回值会返回这个枚举类型,这里我们可以对这个枚举进行match处理错误情况.
这里也可以简写:
```rust
fn main() {
    let f = File::open("hello.txt").unwrap();
}
```
unwrap就相当与有err就直接panic.,也可以换成expect:
```rust
fn main() {
    let f = File::open("hello.txt").expect("Failed to open hello.txt");
}
```
这里错误信息会更明确.  
不过更多时候只需要正确的处理错误就可以了,而不需要直接panic整个程序,所以我们将err return是一个比较好的方案,这里也有简写:
```rust
fn read_username_from_file() -> Result<String, io::Error> {
    let mut f = File::open("hello.txt")?;
    let mut s = String::new();
    f.read_to_string(&mut s)?;
    Ok(s)
}
```
这里直接在函数调用后接?就相当于match到了err就返回err.
## rust共享接口trait
在代码中可以声明一个summary块:
```rust
pub trait Summary {
    fn summarize(&self) -> String;
}
```
这相当与一个共享接口的集合.这个存在的意义就是为不同的struct提供类似的方法实现.  
```rust
impl Summary for XXX{
	...
}
```
这样来实现对于每个结构的同名函数.  
如果在trait中直接实现一个函数,那么这是一个默认实现.如果某个struct想用这个共享接口,只需要用一个空的impl for就可以了,但是一定要有一个impl for ,来显示地告诉编译器.