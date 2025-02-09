# uninitialized_value_construct
* memory[meta header]
* std[meta namespace]
* function template[meta id-type]
* cpp17[meta cpp]

```cpp
namespace std {
  template <class ForwardIterator>
  void uninitialized_value_construct(ForwardIterator first,
                                     ForwardIterator last); // (1)

  template <class ExecutionPolicy, class ForwardIterator>
  void uninitialized_value_construct(ExecutionPolicy&& exec,
                                     ForwardIterator first,
                                     ForwardIterator last); // (2)
}
```

## 概要
未初期化領域の範囲の各要素を値構築する。

未初期化領域の入力イテレータ範囲`[first, last)`の各要素を値構築する (ゼロ初期化する)。


## 効果
以下と等価：

```cpp
for (; first != last; ++first)
  ::new (static_cast<void*>(addressof(*first)))
    typename iterator_traits<ForwardIterator>::value_type();
```
* addressof[link addressof.md]
* iterator_traits[link /reference/iterator/iterator_traits.md]


## 戻り値
なし


### 例
```cpp example
#include <iostream>
#include <memory>

struct Vector {
  int x, y;
};

int main()
{
  std::allocator<Vector> alloc;

  // メモリ確保。
  // この段階では、[p, p + size)の領域は未初期化
  const std::size_t size = 3;
  Vector* p = alloc.allocate(size);

  // 未初期化領域[p, p + size)を値構築
  std::uninitialized_value_construct(p, p + size);

  // 各要素を出力
  for (std::size_t i = 0; i < size; ++i) {
    const Vector& v = *(p + i);
    std::cout << v.x << ',' << v.y << std::endl;
  }

  // オブジェクトを破棄
  std::destroy(p, p + size);

  // メモリ解放
  alloc.deallocate(p, size);
}
```
* std::uninitialized_value_construct[color ff0000]
* std::allocator[link allocator.md]
* alloc.allocate[link allocator/allocate.md]
* alloc.deallocate[link allocator/deallocate.md]
* std::destroy[link destroy.md]

### 出力
```
0,0
0,0
0,0
```

## バージョン
### 言語
- C++17

### 処理系
- [Clang](/implementation.md#clang): 4.0.1
- [GCC](/implementation.md#gcc): 7.3
- [Visual C++](/implementation.md#visual_cpp): ??


## 関連項目
- [`ranges::uninitialized_value_construct`](ranges_uninitialized_value_construct.md)


## 参照
- [P0040R3 Extending memory management tools](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2016/p0040r3.html)
