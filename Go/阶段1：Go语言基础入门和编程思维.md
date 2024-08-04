## 变量

### 定义变量

```go
var greet string = "Hi"
```

使用 `var` 关键字定义变量，并声明变量类型，同时对变量初始化。

也可以先声明后赋值

```go
var greet string
greet = "Hey!"
```

### 类型推导

可以省略类型声明让编译器自动进行推导：

```go
var greet = "Hello!"
```

### 缺省关键字

也可以缺省定义变量的关键字。

<aside>
⚠️ 只能在函数作用域中使用。

</aside>

```go
func main(){
	greet:="Are you ok?"
}
```

### 批量定义变量

```go
// 完整的
var a,b,c string = "are", "you", "ok"
var d,e,f = "hello", "hi", "hey"
var (
	g = "hello"
	h = "hi" 
	i = "hey" 
)
var (
	j int = 1
	k bool = true
	l string = "hey" 
)
```

### 交换变量值

```go
func exchangeVariable(){
	a,b string = 'a', 'b'
	b,a = a,b
}
```

## 常量

### 定义常量

使用 `const` 关键字定义，关键字不可省略。

```go
const bar int = 1
const foo = "hello"
```

数值类型的常量如果没有严格声明类型，可以同时分配给整数或浮点数。

```go
const bar = 1
const foo int = 2
const bz float64 = bar    // ✅
const bzfoo float64 = foo // ❌
```

> 常量本质是值替换。
> 

### iota 表达式

## 内置类型

### bool

布尔类型。

### string

字符串类型。

### (u)int

整数类型， `u` 表示无符号整数。

| (u)init | 长度由当前的系统决定 |
| --- | --- |
| (u)int8 |  |
| (u)init16 |  |
| (u)init32 |  |
| (u)init64 |  |
| uintptr |  |

### float

浮点数类型

### byte

字节,8bit

### runne

`char` 类型，4B，32bit

## 条件语句

### if 语句

```go
if condition { statement }

// 多分支。
if condition { statement }
else if condition { statement }
else { statement }
```

<aside>
⚠️ 注意：条件没有括号。

</aside>

if 语句还支持创建**块级作用域**来执行**一个**简单语句，并用执行结果来作为条件表达式。

```go
func readFile() {
	if buffer, err := os.ReadFile("test.txt"); err == nil {
		fmt.Printf("%s\n", buffer)
	} else {
		fmt.Println(err)
	}
}
```

> 要执行的简单语句与条件表达式要用分号 `;` 隔开。
> 

### switch 语句

```go
switch [expression] {
	case value 1:
			statement
	case value 2:
			statement		
	default:
			statement
}
```

<aside>
⚠️ 注意：switch 语句的每个 case 分支自动 break 的。

</aside>

```go
func counter(a,b int, op string) int {
	var result int;
	switch op {
		case "+":
			result = a + b
		case "-":
			result = a - b
		case "*":
			result = a * b
		default:
			panic("unsupported operator:" + op)
	}
	return result;
}
```

switch 语句另一种常用格式就是省略表达式，只保留 case 分支匹配。

```go
func grade(score int) string {
	switch {
	case score < 60:
		return "F"
	case score < 80:
		return "C"
	case score < 90:
		return "B"
	case score <= 100:
		return "A"
	default:
		panic("incorrect score!")
	}
}
```

## 循环语句

### for

go 中只有一种循环 for 循环，与条件语句一样，循环的表达式不需要用括号括起来。

下面是标准的格式：

```go
for initial; condition; update {
	statement;
}
```

- initial 表达式只会在循环开始时执行一次，用于初始化循环条件。
- condition：循环条件表达式，只有为“真”时才会执行 statement。
- update：statement 执行完成后，用于更新循环条件，然后再判定 condition 表达式是否成立，以决定是否再次执行 statement。

一个具体的示例，用于将数值转换为二进制数输出。

```go
func decimalToBinary(decimal int) string {

	if decimal == 0 {
		return "0"
	}

	var result string = ""

	for d := decimal; d > 0; d /= 2 {
		result = result + strconv.Itoa(d%2)
	}

	return result
}
```

一些特殊的使用场合下，也可以省去 initial 与 update 表达式，只保留 condition 表达式。

```go
for condition { statement }
```

例如，逐行读取文件内容：

```go
func readFile() {

	file, err := os.Open("test.text")

	if err != nil {
		panic(err)
	}

	scanner := bufio.NewScanner(file)

	for scanner.Scan() {
		fmt.Println(scanner.Text())
	}

}
```

省略所有的循环表达式，以执行死循环

```go
func loopEver() {
	for {
		fmt.Println("loop ever!")
	}
}
```

### for range

类似于 JavaScript 的 `forEach` 方法，专门用于数组的遍历迭代。

对比 `for` 语句，它不需循环的相关条件表达式。

```go
for i, v := range arrs {
	fmt.Println(i, v);
}
```

## 函数

### 定义函数

使用 `func` 关键字定义一个函数。

```go
func div(a,b int)(int, int) {
	return a/b, a%b;
}
```

### **剩余参数**

将一个不定数量的参数表示为一个数组。

```go
func forEach(values ...int) int {
	s := 0
	for i := range values {
		s += values[i]
	}
	return s
}
```

### 函数返回值

Go 函数返回值通常有两个，一个是结果，另一个则是 error。

```go
func readTestFile() (*os.File, error) {
	content, err := os.Open("test.txt")
	return content, err
}

func main() {
	if content, err := readTestFile(); err != nil {
		panic(err)
	} else {
		fmt.Println(content)
	}
}
```

如果需要忽略某个返回值，则可以用 `_` 下划线。

```go
q, _ = div(5, 2);
```

### 匿名函数

```go
func apply(fn func(a, b int) int, a, b int) int {
	return fn(a, b)
}

func main() {
	apply(func(a, b int) int { return a + b }, 1, 2)
}
```

Go 没有 lambda  函数。

## 指针

### 定义指针

使用 `*` 运算符定义指针类型；使用 `&` 运算符获取当前变量的指针。

Go 默认采用值传递，即 copy 值到一个函数中，但是也可以使用指针 pointer 实现引用传递。

```go
func passByValue(a int) int {
	a++
	return a
}

func passByRef(pa *int) *int {
	*pa++
	return pa
}

func main(){
	var a int = 0
	passByValue(a)
	fmt.Println(a) // 0
	passByValue(&a)
	fmt.Println(a) // 1
}
```

|  | 能否运算？ | 能否赋值？ | 参数传递 |
| --- | --- | --- | --- |
| GO | NO | YES | 值传递 |
| C++ | YES | YES | 引用传递 |

## 数组

- Go 中数组的定义格式：`[length] type` ，要遵循数组长度在前、类型在后。
- 相同的数组类型，必须满足长度相同类型也相同。因此 `[3]int` 与 `[5]int` 不是同一个类型。
- Go 中数组是值传递的，这与很多其它语言并不相同。

<aside>
💡 在具体的使用场景中只有对长度明确且有限的情况下才会使用数组，更多的是使用“切片”。

</aside>

### 定义数组

与定义变量相同，不过数组只能使用 `var` 关键字定义：

```go
var arr1 [3]int // 先声明后赋值
fmt.Println(arr1) //[0,0,0] 
```

对于先声明后赋值的数组，go 赋予一个默认值，也就是对应类型的 `nil` 值。

当然，也可以省略关键字，声明数组的同时进行赋值

```go
arr2 := [3]int{1, 2, 3}
```

也可以省略长度，用 `…` 符号代替，让 Go 编译器帮我们设置数组长度。

```go
arr3 := [...]int{1, 2, 3, 4}
```

### 二维数组

行*列的顺序

```go
var arr4 [2][3]bool
arr5 := [2][3]int{
	{1, 2, 3},
	{4, 5, 6},
}
```

### 值传递

Go 中的数组默认是值传递的

```go
func updateArr(arr [3]int) [3]int{
	arr[0] = 100
	return arr
}

func main(){
	arr1 := [3]
	fmt.Println(updateArr(arr1), arr1) // [100 0 0] [0 0 0]
	
	// ❌
	arr2 := [...]int{1,2,3,4}
	updateArr(arr2) // incorrrect, [3]int 与 [4]int 不是一个类型
}
```

### 引用传递数组

如果需要在另一个方法中修改数组元素的值，可以将数组的指针传递进去。

```go
func updateArrByRef(arr *[3]int) {
		arr[0] = 100
}

func main(){
		var arr1 [3]int
		fmt.Println(arr1) //[0,0,0]
		
		updateArrByRef(&arr1)
		fmt.Println(arr1) //[100,0,0]
}
```