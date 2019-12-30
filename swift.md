## Generic function
#### Normal func
``` swift
var numbers = [1,2,3,5,7]
var str = ["h","e","l","l","o","w","o","r","l"]
func searchIndex (_ arr: [String], key: String) -> [Int]? {
    var indexs:[Int] = []
    for (index, element) in arr.enumerated() {
        if element == key {
            indexs.append(index)
        }
    }
    return indexs
}

searchIndex(str, key: "l") // [2,3,8]
searchIndex(numbers, key: 3) // Error: Cannot convert value of type '[Int]' to expected argument type '[String]'
```

#### Generics func

``` swift
var numbers = [1,2,3,5,7]
var str = ["h","e","l","l","o","w","o","r","l"]
func searchIndex<T:Comparable> (_ arr: [T], key: T) -> [Int]? {
    var indexs:[Int] = []
    for (index, element) in arr.enumerated() {
        if element == key {
            indexs.append(index)
        }
    }
    return indexs
}

searchIndex(numbers, key: 3) // [2]
searchIndex(str, key: "l") // [2,3,8]
```