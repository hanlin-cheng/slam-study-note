`resize` 和 `reserve` 都是 `std::vector` 提供的用于修改容器大小的方法，但它们的作用和使用场景有所不同。

### 1. `reserve`：

- **作用**：`reserve` 用于预留空间，它会调整 `vector` 的容量（`capacity`），但是不会改变 `vector` 的大小（`size`）。
- **目的**：通过预先分配足够的内存空间，避免在插入数据时频繁重新分配内存，减少内存分配的开销。它不会改变现有的数据，只是确保 `vector` 有足够的内存空间来存储指定数量的元素。
- **使用场景**：当你知道 `vector` 最终的元素数量时，可以调用 `reserve` 提前分配内存，这样在插入元素时就不会因为 `vector` 扩展容量而频繁进行内存重新分配，提高性能。

```
std::vector<int> v; 
v.reserve(1000); // 预留1000个元素的空间，避免之后频繁扩容
```

### 2. `resize`：

- **作用**：`resize` 用于改变 `vector` 的大小（`size`），如果增加的元素数超过当前 `vector` 的大小，它会默认初始化新元素；如果减少大小，超出部分的元素将被销毁。
- **目的**：用来改变 `vector` 的实际元素数量。如果你需要保证 `vector` 的元素数量，使用 `resize`。
- **使用场景**：当你需要调整 `vector` 的实际大小时，可以使用 `resize`。如果增加大小时没有提供初始值，新的元素会被默认初始化为 `0` 或 `nullptr`（如果是指针类型）。

```
std::vector<int> v(5, 10); 
// 初始5个元素，每个值为10 v.resize(8, 20);  
// 增加到8个元素，新元素值为20
```

### 3. 总结：

- `reserve` 用来增加容量，但不改变元素数量。通常用于优化性能，避免频繁的内存重新分配。
- `resize` 用来改变容器的实际元素数量。如果增加元素，新的元素会被默认初始化。

### 如何正确使用以节约内存和运行时间：

- **避免频繁的内存重新分配**：如果你知道 `vector` 可能会存储多少个元素，最好先调用 `reserve` 来预先分配好足够的内存。这样在后续的插入操作时，就不需要每次都进行内存重新分配。
- **避免多次调用 `resize`**：当增加元素时，尽量避免多次调用 `resize`。最好通过一次性 `resize` 来设置所需的大小，或在知道元素数量时使用 `reserve` 来避免后续调整大小。

### 示例：合理使用 `reserve` 和 `resize`

```
std::vector<int> v;

// 预估需要存储至少1000个元素 
v.reserve(1000);  
// 提前分配内存 
// 插入元素 
for (int i = 0; i < 1000; ++i) 
{ 
	v.push_back(i); 
}  
// 调整大小（如果需要）
v.resize(1200, 0);  
// 将大小调整为1200，如果元素少则用0填充
```
通过 `reserve`，我们避免了在插入过程中频繁的内存新分配，确保了 `vector` 的容量始终足够大，而 `resize` 用来确保我们最终的 `vector` 尺寸。