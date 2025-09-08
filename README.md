# **Лекция: Массивы, срезы и карты в Go**  
**Продолжительность**: 2 часа  
**Аудитория**: Начинающие и средние Go-разработчики  
**Цель**: Понимание внутреннего устройства массивов, срезов и карт, их поведения, особенностей и безопасного использования.

---

## Введение

---

## Массивы (Arrays)

```go
var numbers [5]int
```

### Инициализация
```go
numbers := [5]int{1, 2, 3, 4, 5}
// или с выводом длины:
names := [...]string{"Alice", "Bob", "Charlie"} // len=3
```
---

## Срезы (Slices)

Go-внутри (упрощённо):
```go
type slice struct {
    array unsafe.Pointer
    len   int
    cap   int
}
```
---

### Создание срезов

```go
// 1. nil-срез
var nilSlice []int // nil, len=0, cap=0

// 2. Пустой срез
emptySlice := []int{} // not nil, len=0, cap=0

// 3. Через make
newSlice := make([]int, 0, 5) // len=0, cap=5

// 4. Из массива или другого среза
arr := [6]int{1, 2, 3, 4, 5, 6}
slice := arr[1:4] // [2,3,4], len=3, cap=5 (от индекса 1 до конца)
```

> **cap = len(оставшейся части массива)**

---

---

### `append()` — динамическое расширение

```go
s := []int{1, 2, 3}
s = append(s, 4) // [1,2,3,4]
```
---

### Удаление из среза

```go
import "golang.org/x/exp/slices"

data := []int{1, 2, 3, 4, 5}
data = slices.Delete(data, 1, 2) // удаляем [1:2) → индекс 1
// результат: [1,3,4,5]
```
---

### Копирование

```go
src := []int{1, 2, 3}
dst := make([]int, len(src))
copy(dst, src)
```

`copy(dst, src)` копирует **минимум из len(dst) и len(src)**.

---

### Сравнение срезов

```go
a := []int{1,2,3}
b := []int{1,2,3}
fmt.Println(a == b)
```

```go
var s []int
fmt.Println(s == nil) // true

s = []int{}
fmt.Println(s == nil) // false
```

Для сравнения:
```go
import "reflect"
fmt.Println(reflect.DeepEqual(a, b)) // true
```
---

##  Карты (Maps)

```go
m := map[string]int{
    "age": 30,
    "score": 95,
}
```
---

### Создание

```go
var m map[string]int        // nil map
m = make(map[string]int)    // пустая, но инициализированная
m = make(map[string]int, 10) // с ёмкостью ~10
```
```go
m["name"] = "Alice" // OK, если m != nil
```
---

### Операции

```go
m["age"] = 25          // добавление/обновление
delete(m, "age")       // удаление
len(m)                 // количество элементов
```

#### Безопасное чтение

```go
value, exists := m["age"]
if exists {
    fmt.Println("Age:", value)
} else {
    fmt.Println("No age")
}
```
---

### Итерация

```go
for key, value := range m {
    fmt.Println(key, value)
}
```

#### Важно: **порядок итерации — случайный!**

```go
// Каждый запуск может выдать разный порядок
// Это — фича, а не баг!
```
---

##  Практика и Best Practices

### Рекомендации

1. **Используйте срезы, а не массивы** — они удобнее.
2. **Инициализируйте map через `make`**, если не используете литерал.
3. **Не передавайте срезы без контроля cap** — может быть неожиданное поведение.
4. **Используйте `copy` при необходимости изолировать данные**.
5. **Проверяйте наличие ключа в map**, если значение может быть zero.
6. **Не полагайтесь на порядок в map**.

---
