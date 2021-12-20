# Go:复合数据类型

# 复合数据类型

主要的四种复合数据类型：
* 数组：同构的元素组成，内存大小固定
* 结构体：异构元素组成，内存大小固定
* 切片：动态数据类型
* 映射：动态数据类型

## 4.1 数组

数组是由固定长度的特定类型元素组成的序列，可以通过索引下标来访问，下标的范围从0到长度减1。

**数组的定义**
```
var a [3]int //根据元素类型的初始值来初始化 int类型初始值为0

var b [3]int = [3]{1,2,3} //自定义数组的初始值

// 也可以用简短声明

d := [...]int{1,2,3} //省略号 表示根据初始值个数来自动推断
```
当数组的元素是可比价的，那么数组也是可以比较的，仅当元素个数相等、所有元素都相等，数组才相等。
```
a := [2]int{1, 2}
b := [...]int{1, 2}
c := [2]int{1, 3}

fmt.Println(a == b, a == c, b == c) // true,false,false
```
数组的长度等于其容量

Go语言函数调用传递参数的方式为值传递，即形参是实参的副本。这就导致传递大的数组类型是低效的，并且对数组参数的任何的修改都是发生在复制的数组上，并不能直接修改调用时原始的数组变量。

## 4.2 切片

Slice（切片）代表变长的序列，序列中每个元素都有相同的类型。一个slice类型一般写作[]T，其中T 代表slice中元素的类型；slice的语法和数组很像，只是没有固定长度而已。

切片的定义：类似数组但是没有指定元素个数，隐式创建一个合适大小的数组。
```
//1 不加索引
weekdays := []string{"Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"}
//2 加索引
weekdays := []string{0: "Sunday", 1: "Monday", 2: "Tuesday", 3: "Wednesday", 4: "Thursday", 5: "Friday", 6: "Saturday"}
// 3 加索引乱序(对数组也有效果)
weekdays := []string{6: "Saturday", 0: "Sunday", 1: "Monday", 2: "Tuesday", 3: "Wednesday", 4: "Thursday", 5: "Friday"}
```

切片包含三部分属性，1是切片里的元素、2是切片的长度`len()`、3是切片的容量`cap()`
切片的容量总是不小于切片的长度。如果切片操作超出了容量上限，会导致恐慌；如果超出长度，就是扩展了切片。
![20211218-1620-MBfams-c480](https://cdn.jsdelivr.net/gh/ZevaXu/picupload@master/uPic/20211218-1620-MBfams.png)
```
fmt.Println(summer[:10])// 恐慌
fmt.Println(summer[:5])// [June July August September October]
```

由于切片在底层是共享的一个数组，所以对切片复制时，改变新的切片内容将会同时改变原来的切片。
```
months2 := months[:]
months2[0] = "Go"
fmt.Println(months) //[Go January February March April May June July August September October November December]
fmt.Println(months2) //[Go January February March April May June July August September October November December]
```
不同于数组，切片是不能比较大小的，原因：
1. 切片内元素是间接引用的，一个切片甚至可以包含自身。
2. 一个切片可能在不同时间内包含不同的元素（底层的数组改变）

切片可以和nil比较：
```
var s []int // len(s) == 0, s == nil 
s = nil // len(s) == 0, s == nil 
s = []int(nil) // len(s) == 0, s == nil 
s = []int{} // len(s) == 0, s != nil
```
### 4.2.1 append函数
内置的append函数用于向切片追加元素。
```
months := []string{1: "January", 2: "February", 3: "March", 4: "April", 5: "May", 6: "June", 7: "July", 8: "August", 9: "September", 10: "October", 11: "November", 12: "December"}
fmt.Println("len:",len(months), "cap",cap(months)) // len: 13 cap 13
fmt.Printf("%p \n", months) //0x14000064b60

months = append(months, "Go")
fmt.Println(months) //[ January February March April May June July August September October November December Go] 
fmt.Println("len:",len(months), "cap",cap(months)) // len: 14 cap 26
fmt.Printf("%p \n", months) //0x140000016c0 
```
append函数的底层实现：
1. 如果切片容量够，那么直接追加
2. 如果不够，先建立一个容量更大的切片，复制原来的元素，再追加

append可以一次追加多个元素：
```
months = append(months, "Python","C++","Java")
```
### 4.2.2 切片的内存技巧

1. 修改新切片会照成原来切片的改变
```
func Remove_empty_string(strings []string) []string {
	i := 0
	for _, j := range strings {
		if j != "" {
			strings[i] = j
			i++
		}
	}
	return strings[:i]
}

func main() {
	s := []string{"1", "", "3"}
	fmt.Println(Remove_empty_string(s)) //[1,3]
	fmt.Println(s) // [1,3,3] 可以 s = Remove_empty_string(s)
}
```
2. 删除切片中间某个元素
* 通过`copy()`函数复制覆盖
```
func remove(slice []int, i int) []int { 
    copy(slice[i:], slice[i+1:]) 
    return slice[:len(slice)-1] 
}

func main() {
	s := []int{4, 5, 6, 7}
	fmt.Println(remove(s, 2)) // [4 5 7]
}
```

## 4.3 Map映射
哈希表是一种巧妙并且实用的数据结构。它是一个无序的key/value对的集合，其中所有的key都是不同 的，然后通过给定的key可以在常数时间复杂度内检索、更新或删除对应的value。
所有的key必须是同一类型元素，value也必须是同一类型元素。

**Map的创建**
```
//1
ages := make(map[string]int) //键：string 值：int

//2
ages := map[string]int{ "alice": 31, "charlie": 34, } //初始化 

//3
ages := make(map[string]int) 
ages["alice"] = 31 
ages["charlie"] = 34
```

**删除元素**
```
delete(ages, "alice") // remove element ages["alice"]
```
上述操作是安全的，即使要删除的元素不在map中。

**特点**
如果查找map中元素失败，即无该key-value，那么会返回value的一个0值。

禁止对map中元素取地址，map可能随着元素数量的增长而重新分配更大的内存空间，从而可能导致之前的地址无效。

Map的迭代顺序是不确定的，遍历的顺序是随机的，每一次遍历的顺序都不相同。如果要按顺序遍历key/value对，我们必须显式地对key进行排序，可以使用sort包的Strings函数对字符串slice进行排序。

map的零值是nil,即没有引用任何元素。

map的创建和初始化：
```
var ages map[string]int // map的零值是nil，还没有创建。查找、删除、len和range循环都可以安全工作在nil值的map上，但是不可以存入元素
fmt.Println(ages == nil) // true
fmt.Println(ages) // map[]
ages["alice"] = 31 // 引起panic

age := make(map[string]int) // 创建map,可以存入元素
fmt.Println(age == nil) // false
fmt.Println(age) // map[]
age["alice"] = 31 // 没有panic
```
查看键值对是否在map中：
```
//age["alice"] = 31 会返回两个值，一个是“alice”键对应的值，一个是“alice”键对应的值是否存在，不存在时，第一个值为0，第二个值为false。防止混淆

if age, ok := ages["alice"]; ok {
	fmt.Println(age)
} else {
	fmt.Println("alice is not in the map")
}
```

## 4.4 结构体

结构体是一种聚合的数据类型，是由零个或多个任意类型的值聚合成的实体。每个值称为结构体的成员。
**结构体的定义**
```

// 结构体成员的顺序不同，结构体类型也不同。
type Employee struct {
	ID int
	Name string
	Address string
	Date time.Time
	Position string
	Salary int
	ManagerID int
}
var dilbert Employee

// 初始化，用.来访问成员
dilbert = Employee{
	ID: 123,
	Name: "Dilbert",
	Address: "123 Acacia Road",
	Date: time.Now(),
	Position: "Senior Engineer",
	Salary: 12345,
	ManagerID: 123,
}
```
如果成员的名字是以大写字母开头，那么它就是对外的接口，可以被外部代码访问。

结构体成员类型不可以是它自己，因为一个聚合类型的值不能包含自身。但是，如果结构体成员类型是聚合类型的指针，那么它就可以包含自身。这可用于创建递归类型数据结构，链表和树结构。
```
type tree struct {
	value int
	left, right *tree
}
```
如果结构体的全部成员都是可以比较的，那么结构体也是可以比较的，那样的话两个结构体将可以使用 ==或!=运算符进行比较。

结构体类型的零值是每个成员都是零值。

### 4.4.1 结构体赋值

```
//1 适用于简单的结构体,要求赋值顺序一致
type Point struct {
	X, Y int
}
p := Point{1, 2}

//2 适用于复杂的结构体，顺序不重要，如果有缺失的成员，则赋值为零值
p := Point{X: 1, Y: 2}
```

### 4.4.2 结构体和结构体指针

**结构体和结构体指针的相同点：**
* 实际都是指针
* 都可以用`结构体名.成员名`的方式访问成员

**结构体和结构体指针的不同点：**
* 用结构体指针会改变结构体的值，用结构体不会改变结构体的值


```
type Rect struct {
	width, length float64
}

//结构体形式
func double_area(rect Rect) float64 {
	rect.width *= 2
	rect.length *= 2
	return rect.width * rect.length
}
func main() {
	var rect = Rect{100, 200}
	fmt.Println(double_area(rect))//8000
	fmt.Println("Width:", rect.width, "Length:", rect.length)//Width: 100 Length: 200
}

//结构体指针形式
func double_area(rect *Rect) float64 {
	rect.width *= 2
	rect.length *= 2
	return rect.width * rect.length
}
func main() {
	var rect = Rect{100, 200}
	fmt.Println(double_area(&rect))//4000
	fmt.Println("Width:", rect.width, "Length:", rect.length)//Width: 200 Length: 400
}
```
如果要在函数内部修改结构体成员的话，用指针传入是必须的；因为在Go语言中，所有的函数参数都是值拷贝传入的，函数参数将不再是函数调用时的原始变量。


### 4.4.3 结构体嵌入和匿名成员

结构体嵌入机制让一个命名的结构体包含另一个结构体类型的匿名成员，这样就可以通过简单的点运算符x.f来访问匿名成员链中嵌套的x.d.e.f成员。
结构体字面值并没有简短表示匿名成员的语法。
因为匿名成员也有一个隐式的名字，因此不能同时包含两个类型相同的匿名成员，这会导致名字冲突。

```
type Point struct {
	X, Y int
}
type Circle struct {
	Point
	Radius int
}
type Wheel struct {
	Circle
	Spokes int
}
var w Wheel 
w.X = 8 // w.Circle.Point.X = 8
w.Y = 8  // w.Circle.Point.Y = 8
w.Radius = 5  // w.Circle.Radius = 5
w.Spokes = 20

w = Wheel{X: 8, Y: 8, Radius: 5, Spokes: 20} // 但是不能这样写 编译不通过
w = Wheel{
	Circle: Circle{
		Point: Point{X: 8, Y: 8},
		Radius: 5,
	},
	Spokes: 20,
}
```
简短的点运算符语法可以用于选择匿名成员嵌套的成员，也可以用于访问它们的方法。


