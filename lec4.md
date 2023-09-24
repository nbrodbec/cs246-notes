# Lecture 4
## References

### Pass by reference VS Pass by value
```cpp
struct ReallyBig {};

void f(ReallyBig rb) {} // No changes reflected + copy all memory in rb -> expensive
void g(ReallyBig& rb) {} // Changes are reflected, FAST - only 8 bytes copied
void h(const ReallyBig& rb) {} // FAST but rb can't be changed!
```
When might I want to pass by value?
1. Arg is small (pointer size or smaller)
2. Explicitly need a copy (*See below*)
```cpp
void a(const ReallyBig& rb) {
    ReallyBig temp {rb}; // Just use pass-by-value; copies for you
}
```

### rvalues, lvalues

**lvalue:** A value that has a defined region in memory, i.e. you can take its address

**rvalue:** A value that is not an lvalue. It does not have a defined region in memory, e.g. literals, expressions, function calls that return non-references

### Behaviour of references, rvalues, lvalues

```cpp
void f(int& x) {}

int z;
f(z) // Valid
f(5) // Invalid

void g(const int& y) {}

g(z) // Valid
g(5) // Compiles
```

## Dynamic Memory
#### In C:
```c
int* p = malloc(n*sizeof(int));
// ...
free(p);
```

#### In C++:
```cpp
Node* np = new Node {5, nullptr};
// ...
delete np;
```

All local variables require space on the _stack_. Use `new` to acquire memory from the _heap_. Calling `delete` returns memory to OS to be reused, does not affect value of pointer at all.

**Stack allocated variables:** Automatically deallocated when they leave scope

**Heap allocated memory:** Exists until `delete` is called. A memory leak occurs when heap allocated memory is never freed

### Dynamic arrays:
```cpp
int* arr = new int[10]; // Array of 10 ints on heap arr points to first element in the array
// ...
delete[] arr; // Frees a dynamically allocated array
```
**Note:** Always follow `new` with `delete`, and `new[]` with `delete[]`.




