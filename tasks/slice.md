### Итерация по слайсу

Итерация с копированием значений слайса

```go
type myStruct struct {
    h     uint64
    cache [64]byte
    raw   []byte
}

func main() {
   sl := make([]myStruct,10)

    for i := 0; i < 10; i++ {
        sl[i].h = uint64(i)
        sl[i].raw = make([]byte, 1024*1024)
    }
	
    var sum uint64 = 0
    for _, v := range sl {
        sum += v.h
   }
}
```

Итерация без копирования

1. Вариант

```go
type myStruct struct {
    h     uint64
    cache [64]byte
    raw   []byte
}

func main() {
    sl := make([]myStruct,10)

    for i := 0; i < 10; i++ {
        sl[i].h = uint64(i)
        sl[i].raw = make([]byte, 1024*1024)
    }
	
    var sum uint64 = 0
    for i := range sl {
        sum += sl[i].h
   }
}
```

2. Вариант

```go
type myStruct struct {
    h     uint64
    cache [64]byte
    raw   []byte
}

func main() {
    sl := make([]myStruct,10)

    for i := 0; i < 10; i++ {
        sl[i].h = uint64(i)
        sl[i].raw = make([]byte, 1024*1024)
    }
	
    var sum uint64 = 0
    for ii := range sl {
        sum += (&sl[ii]).h
   }
}
```

### Склейка 2х слайсов
```go
func main() {

    arr := []int{1, 2, 3, 4, 5, 6, 7, 8, 9}
    arr2 := []int{20, 21, 22}
    arr2 = append(arr2, arr...)
    fmt.Println(arr2)
}

```

### Передача слайса в функцию

1. Вариант
```go
func main() {
    arr := make([]int, 2, 10)
    
    arr[0] = 111 // 1
    arr[1] = 222

    Add(arr)
    
    fmt.Println("arr", arr) // 3
    fmt.Println("arr", arr[:cap(arr)]) // 4
    fmt.Println("len(arr)", len(arr))
    fmt.Println("cap(arr)", cap(arr))
}

func Add(v []int) {
     v = append(v, 1024, 1025, 1026, 1027) // 2
}
```

Вывод:

`arr [111 222]`

`arr [111 222 1024 1025 1026 1027 0 0 0 0]`

`len(arr) 2`

`cap(arr) 10`


Пояснения :

    1. Обновляем значения первых 2х элементов
    2. Выполняется append, но без выделения памяти под новый слайс, потому что cap < количества добавляемых значений
    3. Выводится массив согласно len
    4. Операция слайсинга, по cap. Что позволяет заглянуть за границу len

2. Вариант
```go
func main() {
    arr := make([]int, 2, 10)
    
    arr[0] = 111 // 1
    arr[1] = 222

    Add(arr)
    
    fmt.Println("arr", arr) // 3
    fmt.Println("arr", arr[:cap(arr)]) // 4
    fmt.Println("len(arr)", len(arr))
    fmt.Println("cap(arr)", cap(arr))
}

func Add(v []int) {
     v = append(v, 1024, 1025, 1026, 1027, 1028, 1029, 1030, 1031, 1032) // 2
}
```
Вывод:

`arr [111 222]`

`len(arr) 2`

`cap(arr) 10`

`arr [111 222 0 0 0 0 0 0 0 0]`

Пояснения:

    1. Обновляем значения первых 2х элементов
    2. Выполняется append, происходит перевыделение памяти
    3. Выводится массив согласно len
    4. Операция слайсинга, по cap, но так как в нутри функции был создан новый массив, изменения в исходном массиве не произошли 

3.  Вариант

```go
func main() {
    arr := make([]int, 5, 10)
	
    Add(arr[1:4]) // 1
	
    fmt.Println("arr", arr) // 5
    fmt.Println("len(arr)", len(arr)) // 6
    fmt.Println("cap(arr)", cap(arr)) // 7 
    fmt.Println("arr", arr[:cap(arr)]) // 9
}

func Add(v []int) {
    fmt.Println("len(arr) in Add", len(v)) // 2 
    fmt.Println("cap(arr) in Add", cap(v)) // 3 
    v = append(v, 1024, 1025, 1026, 1027, 1028) // 4
}
```

Вывод:

`len(arr) in Add 3`

`cap(arr) in Add 9`

`arr [0 0 0 0 1024]`

`len(arr) 5`

`cap(arr) 10`

`arr [0 0 0 0 1024 1025 1026 1027 1028 0]`

Пояснения:

    1 - 3. Выполняем операцию слайсинга. Поучаем массив длиной 3 элменет, при это мемкость будет равна 9 (исходной емкости-1), 
        потому что берм подслайс с 1 элемента,а не с 0.
    4. Добавление элементов не вызывает перевыделение памяти
    5-7. исходная длина будет такой же, ка ки емкость. Сам массив будет изменен 

### Подводные камни слайсинга

```go
func main() {
    s1 := make([]int, 1)
    s2 := s1
    s2[0] = 1
    s1 = append(s1, 42)
    s2[0] = 21
    fmt.Println("s1", s1)
    fmt.Println("s2", s2)
}
```
Вывод: 
```
s1 [1 42]
s2 [21]
```

Пояснение:

При выполнении операции `s1 = append(s1, 42)`, будет создан новый слайс. И s2 ни как не будет на него влиять.  

### писькодрочерство

```go
func main() {
    ts := [2]T{}
    for i := range ts[:] {
        switch t := &ts[i]; i {
        case 0:
            t.n = 3
            ts[1].n = 9
        case 1:
            fmt.Print(t.n, "")
        }
    }
    fmt.Print(ts)
}
```

Пояснение:

В range мы делаем копию того, что перебираем. Могут быть нюансы. Но тут мы работает с индексами и все норм!

Далее в switch мы получаем ссылку на элемент. Но это switch и все в одной области видимости.

Также, обращение к элементам массива происходит либо по ссылке на сам объект, либо через индекс, и мы меняем сам объект, а не копию. 

Ну и принт (Не принтЛн) все выводит в строку.

________________________________________________________________

```go
func main() {
	var a []int = nil
	a, a[0] = []int{1, 2, 3}, 9
	fmt.Println(a)
}
```

Пояснение:

Здесь действует простое правило - выражение вычисляется позже, чем его подвыражения.
Мы сначала пытаемся записать под нулевым индексом в nil-массив элемент, а затем заполняем его {1, 2}. 
Получаем ошибку panic: runtime error: index out of range [0] with length 0.