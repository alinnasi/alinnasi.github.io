---
title: Golang学习笔记：JSON数据处理
date: 2017-07-16
category: Golang
---

JSON是网络传输中普遍使用的一种数据格式，Go提供了json包来方便开发者对JSON进行处理。

Go与JSON的数据类型对应关系如下：

* bool对应JSON的boolean
* float64对应JSON的numbers
* string对应JSON的strings
* nil对应JSON的null

## 序列化

```
type T struct {
    S string  `json:"key"`
    F float64
    I int32
    b bool
}

func main() {
    t := T{"Hello JSON", 1.23, 4, true}
    js, _ := json.Marshal(t)
    fmt.Printf("JSON format: %s\n", js)

    tp := &t
    js, _ = json.Marshal(tp)
    fmt.Printf("JSON format: %s\n", js)
    
    map1 := map[int]int{}
    map1[1] = 1
    js, err := json.Marshal(map1)
    fmt.Printf("JSON format: %s\n", err)
    
    map2 := map[string]int{}
    map2["hello"] = 1
    js, _ = json.Marshal(map2)
    fmt.Printf("JSON format: %s\n", js)
}

/*
  输出：
  JSON format: {"key":"Hello JSON","F":1.23,"I":4}
  JSON format: {"key":"Hello JSON","F":1.23,"I":4}
  JSON format: json: unsupported type: map[int]int
  JSON format: {"hello":1}
*/
```

从上面的程序可以得出几点结论：

* 私有（首字母为小写）字段无法被序列化
* 可以通过`` `json:"key"` ``的形式指定Key
* 指针也可以被序列化，等价于指针所指向结构体的序列化
* 只能序列化key为string类型的map

## 反序列化

```
type T struct {
    S string  `json:"key"`
    F float64
    I int32
    b bool
}

func main() {
    t := T{"Hello JSON", 1.23, 4, true}
    jsonStr, _ := json.Marshal(t)
    fmt.Printf("JSON format: %s\n", jsonStr)

    var t2 T
    json.Unmarshal(jsonStr, &t2)
    fmt.Printf("Object data: %s\n", t2)
}

/*
  输出：
  JSON format: {"key":"Hello JSON","F":1.23,"I":4}
  Object data: {Hello JSON %!s(float64=1.23) %!s(int32=4) %!s(bool=false)}
*/
```

## 万能的interface

Go的基本类型都是有初值的，比如int32的初值是0，因此无法表示值不存在的情况（即nil），这时候就需要`interface{}`登场了。

```
type T struct {
    S  string
    F  float64
    I1 interface{}
    I2 interface{}
}

func main() {
    t := T{}
    t.S = "Hello JSON"
    t.I1 = "can be any type"
    jsonStr, _ := json.Marshal(t)
    fmt.Printf("JSON format: %s\n", jsonStr)

    map1 := map[string]interface{}{}
    map1["number"] = 1.23
    map1["string"] = "Hello JSON"
    jsonStr, _ = json.Marshal(map1)
    fmt.Printf("JSON format: %s\n", jsonStr)
}

/*
  输出：
  JSON format: {"S":"Hello JSON","F":0,"I1":"can be any type","I2":null}
  JSON format: {"number":1.23,"string":"Hello JSON"}
*/
```

`interface{}`本质上是一个指针，因此该类型的初值为nil，但它可以被赋予任何值。如果map的value是`interface{}`类型，那么相当于创建了一个python的map，值可以为任何类型。

除此之外，也可以将一个JSON字符串直接反序列化到一个`interface{}`对象中：

```
func main() {
    b := []byte(`{"Name": "Jack", "Age": 18}`)
    var f interface{}
    json.Unmarshal(b, &f)
    m := f.(map[string]interface{})
    fmt.Println(m)
}

```