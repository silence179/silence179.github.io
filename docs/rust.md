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
