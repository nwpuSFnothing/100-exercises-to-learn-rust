# 02

## 04	panic错误处理

1. panic

> 用法
>
> ```rust
> ...
> panic!("发生了严重错误，下面的代码永远无法执行")；//终止并打印输出
> ...
> ```

2. Restult<T,E>

> `Result` 是 Rust 标准库中用于表示可能失败的操作的枚举：
>
> ```RUST
> enum Result&lt;T, E&gt; {
>     Ok(T),    // 操作成功，包含结果值
>     Err(E),   // 操作失败，包含错误信息
> }
> ```
>
> - 使用match处理
>
> ```rust
> use std::fs::File;
> 
> fn main() {
>     let f = File::open("hello.txt");
> 
>     match f {
>         Ok(file) => {
>             println!("文件打开成功: {:?}", file);
>         }
>         Err(error) => {
>             println!("文件打开失败: {}", error);
>         }
>     }
> }
> ```
>
> - 使用let简化 处理
>
> ```rust
> use std::fs::File;
> 
> fn main() {
>     let f = File::open("hello.txt");
> 
>     if let Ok(file) = f {
>         println!("文件打开成功");
>         // 在这里使用 file ...
>     } else {
>         println!("文件打开失败");
>     }
> }
> ```
>

## 08	overflow(profile的使用)

## 09	saturating_mul(饱和乘法)

- 解释：

> 当乘法的结果超出数据类型能表示的范围时，结果不发生溢出而是返回该类型的最大值/最小值

- 用法

```rust
//a*b的saturating_mul
a=a.saturating_mul(b);
```

> | 类型    | 最小值             | 最大值            |
> | ------- | ------------------ | ----------------- |
> | `u8`    | `u8::MIN` (`0`)    | `u8::MAX` (`255`) |
> | `u16`   | `u16::MIN`         | `u16::MAX`        |
> | `u32`   | `u32::MIN`         | `u32::MAX`        |
> | `u64`   | `u64::MIN`         | `u64::MAX`        |
> | `u128`  | `u128::MIN`        | `u128::MAX`       |
> | `usize` | `usize::MIN`       | `usize::MAX`      |
> | `i8`    | `i8::MIN` (`-128`) | `i8::MAX` (`127`) |
> | `i16`   | `i16::MIN`         | `i16::MAX`        |
> | `i32`   | `i32::MIN`         | `i32::MAX`        |
> | `i64`   | `i64::MIN`         | `i64::MAX`        |
> | `i128`  | `i128::MIN`        | `i128::MAX`       |
> | `isize` | `isize::MIN`       | `isize::MAX`      |
> | `f32`   | `f32::MIN`         | `f32::MAX`        |
> | `f64`   | `f64::MIN`         | `f64::MAX`        |

# 03

## 01	struct

- 定义一个结构体

> ```rust
> struct Site {
>     domain: String,
>     name: String,
>     nation: String,
>     found: u32
> }
> ```

- 实例化一个结构体

> ```rust
> let runoob = Site {
>     domain: String::from("www.runoob.com"),
>     name: String::from("RUNOOB"),
>     nation: String::from("China"),
>     found: 2013
> };
> ```
>

## 02	validation

- String函数的使用

> 1. 两个String变量的比较，直接用=
> 2. String类型的参数传入&str类型需要.into()或者to_string（）方法
>
> ```rust
> //在rust语法中，所有的类型转换都必须显示进行，不允许有隐式的类型转换
> fn new(title: String, description: String, status: String) -> Self {}
> 
> Ticket::new("a".into(), "12".into(), "To-Do".into())
> 
> ```
>
> 3. 常用的函数
>
> ```rust
> let s:String=String::from("hello world");
> let s_len=s.len();
> let isempty=s.is_empty();//判断是否是空字符串
> ```

## 04	visibility

> 默认都是私有（private）。
>
> 需要跨模块访问的项，都要用 `pub` 显式公开。
>
> 结构体公开 ≠ 字段公开，每个字段需要单独加 `pub`。
>
> `pub` 不会自动传播,例如：mod公开，mod内部的函数不会公开



> | 写法              | 含义                   |
> | ----------------- | ---------------------- |
> | `pub fn foo()`    | 公共函数               |
> | `pub struct User` | 公开结构体类型         |
> | `pub field: T`    | 公开结构体字段         |
> | `pub enum Status` | 公开枚举（变体也公开） |
> | `pub mod math`    | 公开模块               |
> | `pub fn new()`    | 公开关联函数或方法     |

## 05	encapsulation(封装)

```rust
pub mod ticket {
    pub struct Ticket {
        title: String,
        description: String,
        status: String,
    }
        // TODO: Add three public methods to the `Ticket` struct:
        //  - `title` that returns the `title` field.
        //  - `description` that returns the `description` field.
        //  - `status` that returns the `status` field.
}
```

 

```rust
//正确写法
    impl Ticket {
        pub fn title(&self) -> &str {
            &self.title
        }
        pub fn description(&self) -> &str {
            &self.description
        }
        pub fn status(&self) -> &str {
            &self.status
        }
    }
```

```rust
//错误写法
    impl Ticket {
        pub fn title(&self) -> String {
            self.title
        }
        pub fn description(&self) -> String {
            self.description
        }
        pub fn status(&self) -> String {
            self.status
        }
    }
```

> 错误代码：
>
> ```rust
> pub fn title(&self) -> String {
>     self.title
> }
> ```
>
> 编译器报错：
>
> ```text
> cannot move out of `self.title` which is behind a shared reference
> ```
>
> `&self` 表示当前方法只是**借用** `Ticket` 对象，并没有获得它的所有权。因此，在方法内部不能将结构体中的字段移动（move）出去。
>
> `String` 是一个没有实现 `Copy` Trait 的类型。当执行：
>
> ```rust
> self.title
> ```
>
> 时，实际上发生的是 **Move**，而不是 Copy。这意味着 `title` 会从 `Ticket` 中被取走。
>
> 假设允许这样做：
>
> ```rust
> let ticket = Ticket::new(...);
> 
> let title = ticket.title();
> ```
>
> 那么调用结束后，`ticket` 会变成：
>
> ```text
> ticket
> ├── title        ❌ 已被移动
> ├── description  "A description"
> └── status       "To-Do"
> ```
>
> 此时 `ticket` 已经不是一个完整的对象，但它仍然存在，因此 Rust 禁止这种行为，所以编译报错。
>
> 正确的 Getter 应该返回字段的借用：
>
> ```rust
> pub fn title(&self) -> &str {
>     &self.title
> }
> ```
>
> 这里返回的是 `&str`，只是借用了字符串，没有发生所有权转移，也不会复制数据，是 Rust 中 Getter 的标准写法。
>
> 如果确实需要返回一个新的 `String`，则必须显式复制：
>
> ```rust
> pub fn title(&self) -> String {
>     self.title.clone()
> }
> ```
>
> `clone()` 会重新分配内存并复制字符串，因此可以安全地返回新的 `String`，但会有一定的性能开销。
>
> **总结：**
>
> - `&self` 表示借用对象，不能移动其中的字段。
> - `String` 是非 `Copy` 类型，读取字段会发生 Move。
> - Getter 一般返回 `&str`，既避免 Move，又避免不必要的复制。
> - 如果必须返回 `String`，需要使用 `clone()` 创建副本。

# 其他

## 关联函数和方法

# Rust：关联函数（Associated Function）与方法（Method）的区别

| 对比项               | 关联函数           | 方法                           |
| -------------------- | ------------------ | ------------------------------ |
| 第一个参数           | 没有 `self`        | `self`、`&self` 或 `&mut self` |
| 调用方式             | `Type::function()` | `instance.method()`            |
| 是否属于某个实例     | ❌ 否               | ✅ 是                           |
| 是否能访问结构体成员 | 需要传入实例       | 可以直接使用 `self`            |
| 常见用途             | 创建对象、工具函数 | 操作当前对象                   |

---

## 关联函数

**定义：**

```rust
struct Order {
    quantity: i32,
}

impl Order {
    fn is_available(order: &Order) -> bool {
        order.quantity > 0
    }
}
```

**调用：**

```rust
let order = Order { quantity: 10 };

Order::is_available(&order);
```

特点：

- 没有 `self`
- 使用 `类型名::函数名()` 调用
- 类似于其他语言中的静态函数（Static Method）

---

## 方法

**定义：**

```rust
struct Order {
    quantity: i32,
}

impl Order {
    fn is_available(&self) -> bool {
        self.quantity > 0
    }
}
```

**调用：**

```rust
let order = Order { quantity: 10 };

order.is_available();
```

特点：

- 第一个参数必须是 `self`、`&self` 或 `&mut self`
- 使用 `对象.方法()` 调用
- 可以直接访问当前对象的成员

---

## 如何选择？

### 使用关联函数

适用于：

- 创建对象（如 `new()`）
- 与实例无关的工具函数

例如：

```rust
let order = Order::new();
```

---

### 使用方法

适用于：

- 操作当前对象
- 读取或修改对象成员

例如：

```rust
order.is_available();
order.update_quantity(20);
```

---

## 一句话总结

> **没有 `self` 是关联函数，用 `Type::function()` 调用；有 `self` 是方法，用 `instance.method()` 调用。**
