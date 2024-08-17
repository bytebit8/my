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

缺省 `var` 关键字与“类型声明”。以字面量的形式定义变量：

> [!warning]
> 只能在函数作用域中使用。
 
```go
func main(){
	greet:="Are you ok?"
}
```

### 批量定义变量

```go
var a,b,c string = "are", "you", "ok"
var (
	j int = 1
	k bool = true
	l string = "hey" 
)

// 类型推导
var d,e,f = "hello", "hi", "hey"
var (
	g = "hello"
	h = "hi" 
	i = "hey" 
)
```

### 交换变量值

```go
func exchangeVariable(){
	a, b := 'a', 'b'
	b,a = a,b
}
```

## 常量

### 定义常量

使用 `const` 关键字定义，**关键字不可省略。**
```go
const bar int = 1
const foo = "hello" //自动类型推导
```

数值类型的常量如果没有严格声明类型，可以同时分配给整数或浮点数。
```go
const bar = 1
const foo int = 2
const bz float64 = bar    // ✅
const bzfoo float64 = foo // ❌
```
> 常量本质是值替换。

### iota 表达式

## 内置类型

### bool

布尔类型。
```go
var truth bool = true
var fasely bool = false
```
### string

字符串类型。
### (u)int

整数类型。可以分为有符号与无符号整型，`u` 表示无符号整数。

| (u)init   | 长度由当前的系统决定 |
| --------- | ---------- |
| (u)int8   |            |
| (u)init16 |            |
| (u)init32 |            |
| (u)init64 |            |
| uintptr   |            |

### float

浮点数类型

### byte

字节,8bit
```go
var b byte = 255
```
### runne

`char` 类型，4B 大小，32bit。

## 条件语句

### if 语句

```go
if condition { statement }

// 多分支。
if condition { statement }
else if condition { statement }
else { statement }
```

> [!tip]
> 
> 在 `Go` 语法中，条件是没有括号括起来的。

if 语句还支持创建“**块级作用域**”来执行**一个**简单语句，并用执行结果来作为条件表达式。
```go
func readFile() {
	if buffer, err := os.ReadFile("test.txt"); err == nil {
		fmt.Printf("%s\n", buffer)
	} else {
		fmt.Println(err)
	}
}
```

> [!tip]
> 要执行的简单语句与条件表达式要用分号 `;` 隔开。

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

> [!warning]
> `Go` 语法中 `switch` 语句的每个 `case` 会自动 `break`。

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

`switch` 语句的表达式是可以省略的，只保留 case 分支匹配。
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

`Go` 中只有一种循环 —— **for 循环**。与条件语句一样，循环的表达式也不需要用括号括起来。
```go
for initial; condition; update {
	statement;
}
```

- `initial` : 表达式只会在循环开始时执行一次，用于初始化循环条件。
- `condition` : 循环条件表达式，只有为“真”时才会执行 statement。
- `update` ：statement 执行完成后，用于更新循环条件，然后再判定 condition 表达式是否成立，以决定是否再次执行 statement。

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

一些特殊的使用场合下，也可以省去 `initial` 与 `update` 表达式，只保留 `condition` 表达式。
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

也省略所有的循环表达式，以执行死循环
```go
func loopEver() {
	for {
		fmt.Println("loop ever!")
	}
}
```

### for range

类似于 JavaScript 的 `forEach` 方法，专门用于数组的遍历迭代。
对比 `for` 语句，它不需循环的相关条件表达式。`range` 关键字可以遍历数组或切片，返回索引下标与数组元素。
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

### 可变参数

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

>[!tip]
>如果需要忽略某个返回值，则可以用 `_` 下划线。

```go
q, _ = div(5, 2);
```

### 匿名函数

匿名函数对比普通函数，主要就是没有函数名。
Go 没有 lambda  函数。
```go
func apply(fn func(a, b int) int, a, b int) int {
	return fn(a, b)
}

func main() {
	apply(func(a, b int) int { return a + b }, 1, 2)
}
```

## 指针

### 定义指针

使用 `*` 运算符可以定义特定类型的指针。
使用 `&` 运算符获取当前变量或标识符的指针值。

`Go` 默认采用的是值传递，即 copy 值到一个函数中，但是也可以使用指针(pointer) 实现引用传递。
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

- `Go` 中数组的定义格式：`[length] type` ，要遵循数组长度在前、类型在后。
- 相同类型的数组，必须满足长度与类型都相同。因此 `[3]int` 与 `[5]int` 不是同一个类型。
- `Go` 中数组的长度是固定的。
- `Go` 中数组是值传递的。

>[!tip]
>💡 在具体的使用场景中只有对长度明确且有限的情况下才会使用数组，更多的是使用“切片(slice)”。

### 定义数组

与定义变量相同，不过数组只能使用 `var` 关键字定义：
```go
var arr1 [3]int // 先声明后赋值
fmt.Println(arr1) //[0,0,0] 
```

对于先声明后赋值的数组，Go 赋予一个默认值，也就是对应类型的 `nil` 值。
当然，也可以省略关键字，声明数组的同时进行赋值
```go
arr2 := [3]int{1, 2, 3}
```

也可以省略长度，用 `…` 符号代替，让 Go 编译器帮我们设置数组长度。该种写法只支持数组的字面量定义形式。
```go
var arr3 = [...]int{1, 2, 3, 4}
arr4 := [...]int{1, 2, 3, 4}
```

### 二维数组

`[行][列]`
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

## 切片

### 基本概念

切片（slice）是对底层数组建立的一层视图（view）。视图的范围由切片的下标区间指定，遵循着前开后闭的区间特性。
```go
arr := [...]int{0, 1, 2, 3}
var s = arr[0:2] //[0, 1]
```

切片的下标区间有多种写法：
* **`[start:end]`**：通过起始与结束下标从数组中建立切片（注：不含结束下标）
* **`[start:]`**：从起始下标开始到整个数组末尾来建立切片。
* **`[:end]`**：从数组的起始下标到指定的结束下标来建立切片。
* **`[:]`**：建立对整个数组长度的切片。
```go
var s1 = arr[0, 2]
var s2 = arr[1:]
var s3 = arr[2:]
var s4 = arr[:]
```

在 `Go` 中数组是“值类型”的，但切片（slice）则是“引用类型”，因为它本质是对数组 的视图（view），是指向原数组的指针。
```go
func updateSlice(s []int){
	var sum int
	for _, v := range s {
		sum+=v
	}
	s[0] = sum
}

func main(){
	arr:= [...]int{0, 1, 2, 3}
	updateSlice(arr[:]) //[6, 1, 2, 3]
}
```

> [!tip]
> 切片的类型是 `[]int` ，对比数组需要同时满足长度与类型，切片只需要满足类型即可。也因此在 `Go` 中切片比数组更加的常用。

> [!note]
> 可以将切片简单理解为对底层数组某个区域的映射。
### 重组切片

切片支持拓展，其最大可拓展长度是其底层数组的最大长度。
```go
arr := [...]int{0, 1, 2, 3}
var s = arr[0:3]
fmt.Println(s) //[0, 1, 2]

// extend slice
s = s[0:4];
fmt.Println(s) //[0, 1, 2, 3]
fmt.Println(s[3]) //3
```

切片的拓展只能通过切片的下标区间语法进行扩展，如果直接使用索引下标访问一个不存在的元素，则会报数组越界 `(index out of range)` 的错误。
```go
s = arr[0:3]
fmt.Println(s[3]) //index out of range ❌
```

> [!tip]
> 这么做的好处是当切片达到容量上限后可以扩容。
> 改变切片长度的过程称之为切片重组 `Reslice`。

切片重组的原理如下图所示：

![[slice.svg]]
结合上图，我们定义了两个切片，`s` 与 `s2`, 每个切片都存储了以下三个字段：
* **ptr** : 切片指向的底层数组的起始位置。对于切片来说，它总是从下标为 0 的索引开始，但对于指向的底层数组的位置而言并不总是这样。例如，切片 `s` 是从数组的第 `1` 个元素开始；切片 `s2` 是从数组的第 `0` 个元素开始。
* **len** : 切片的长度。也是当前切片的实际容量，切片遵循前开后闭的原则，因此切片 `s` 不包含底层数组下标为 3 的元素，到下标为 `2` 的数组元素截止；切片 `s2` 同理，不包含下标为 `4` 的数组元素，而是到下标为 `3` 的数组元素为止。
* **cap** ：切片的最大容量。通常来说 `len <= cap`，如果 `len < cap` 则说明底层数组有部分元素不能被切片放问，若强行通过索引下标访问这些无法访问的元素，则会导致 `panic`。

拓展切片时，实际上就是把 `len` 的值向 `cap` 增大，扩充切片的容量，使其能访问底层数组更多的元素。

> [!warning]
> 切片只能向后拓展，不能向前拓展！

现在基于 `s2` 创建 `s3` 切片，并对切片进行重组，让切片 s3 可以访问 s2 无法访问的下标为 4 的数组元素。
```go
var s3 = s2[2:5]
s3[0] = 10
s3[1] = 11
fmt.Println(arr) //[1, 2, 10, 11, 5]
fmt.Println(s3[3]) //[10, 11, 5]
```
由此，我们可以得出，通过切片创建的切片，本质还是从上个切片所映射的数组区域来创建新的切片，多个切片的元素下标可能不一样，但是对应关联的底层数组元素则是同一个。

![[slice-extends.svg]]

### 创建切片

#### 声明切片

声明一个 slice，并用 `nil` 值填充
```go
var s []int
```

#### 字面量

使用 slice 的字面量定义：
```go
var s = []int{1, 2, 3, 4}
```

#### 下标区间

基于已有数组创建 slice —— 使用切片的下标区间语法：
```go
var s = arr[:]
```

#### make 函数

使用 `make` 函数创建 slice：
```go
var s = make([]int, 10, 20);
```
> 会使用指定类型的 `nil` 值作为切片元素的初始值。

### 切片操作
#### len

获取切片的元素数量
```go
var s = []int{1, 2, 3}
len(s) //3
```

#### cap

获取切片的 `capacity`，也就是切片底层数组的长度。
```go
var s = make([]int, 10, 20)
cap(s) //20
```

#### make

创建切片，并使用对应类型的 nil 值初始化切片元素。
可以指定切片的 `len` 与 `cap` 长度。

`make(type, len, arrayLength)`
* `type`: 切片的类型。
* `len`：切片的长度。
* `arrayLength`：切片底层数组的长度，也就是切片的 `cap` 值。

```go
var s1 = make([]int, 5, 15)
var s2 []int

for i:=0; i<10; i++{
	s1 = append(s1, 2*i+1)
	s2 = append(s2, 2*i+1)
}

fmt.Println(s1) // [0 0 0 0 0 1 3 5 7 9 11 13 15 17 19]
fmt.Println(s2) // [1 3 5 7 9 11 13 15 17 19]
```

>[!tip]
>`make` 函数主要用于事先创建具有长度的切片。切片的元素则在创建之后再设置。

#### append

1. 使用 `append` 函数向切片追加元素
``` go 
var s []int

s = append(s, 1)
s = append(s, 2)
s = append(s, 3)
s = append(s, 4)

fmt.Println(s) //[1, 2, 3, 4]
```

> [!warning] 
> 使用 `append` 函数向切片追加袁旭，会改变底层数组元素的值。

```go
var arr = [...]int{1, 2, 3, 4}
var s = arr[1:3] //[2, 3]

s = append(s, 6) 
fmt.Println(s) //[2, 3, 6]
fmt.Println(arr) //[1, 2, 3, 6]
```

2. `append` 函数可以为切片追加大于底层数组长度的元素数量，既 `len(s) > cap(s)`。当为切片追加的元素数量大于其底层数组的长度时，`Go` 编译器会拷贝底层数组创建一个新的数组来保存这些追加的元素。
```go
s = append(s, 7)
s = append(s, 8)

fmt.Println(s) //[2, 3, 6, 7, 8]
fmt.Println(arr) //[1, 2, 3, 6]
```

>[!tip]
>可以发现追加的超出原始数组长度的元素并没有保存在原始数组中。

3. `append(slice []Type, elems ...Type) []Type`。append 方法第二个参数支持可变参数，因此可以传递一个展开的切片或数组，以实现一次性追加多个元素的效果。
```go
var s1 = []int{1, 2, 3}
var s2 = []int{4, 5, 6}
var s = append(s1, s2...)

fmt.Println(s) //[1, 2, 3, 4, 5, 6]
```

#### 删除切片元素

1. 删除切片的第一个元素。
```go
func shift(){
	s := []int {1, 2, 3}
	first := s[0]
	s = s[1:]
}
```

2. 删除切片最后一个元素
```go
func pop(){
	s := []int{1, 2, 3}
	end := s[len(s)-1]
	s = s[:len(s)-1]
}
```

3. 删除切片中间的一个元素
```go
func splice() {
	s := []int{1, 2, 3, 4}
	s = append(s[:1], s[2:]...)
	fmt.Println(s) //[1, 3, 4]
}
```

## Map
