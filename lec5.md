# Lecture 5
## Returning
We have seen pass-by-value, pass-by-pointer, pass-by-reference. Which is best for returning?

### Return by value
```cpp
// Makes a copy from getNode's stack frame into the caller's frame. Is it too slow?
Node getNode(int n) { 
    return Node {n, nullptr} 
};
```
### Return by pointer
```cpp
// Dangling pointer! x will be cleaned up when function returns, pointing at memory we no longer own.
Node* getNode(int n) {
    Node x {n, nullptr};
    return &x;
}
```
alternatively,
```cpp
// This does work, is fast, but user must clean up the heap allocated memory.
Node* getNode(int n) {
    Node* p = new Node {n, nullptr};
    return p;
}
```

### Return by reference

```cpp
// Dangling reference! x will be freed after the function returns
Node& getNode(int n) {
    Node x {n, nullptr};
    return x;
}
```
While we could return a reference to heap allocated memory, it is unclear to the user that they must eventually free the memory at that reference's address. For that reason, returning by reference is rarely used.

***
#### Advice:
Return by value is the easiest to use and not as slow as one may think thanks to compiler optimizations & move semantics.

## Operator Overloading
Allows us to define behaviour for operators acting on user-defined types/structs.

```cpp
struct vec {
    int x, y;
};

// Overloading '+' operator
vec operator+(const vec& v1, const vec& v2) {
    vec r {v1.x + v2.x, v1.y + v2.y};
    return r;
}

// Overloading '*' operator
vec operator*(int k, const vec& v) {
    return {k * v.x, k * v.y}; // Note: Implicitly constructs struct vec due to function return type
}

// Overload this function to account for different order of arguments
vec operator*(const vec& v, int k) {
    return k * v;
}

vec v {1, 2};
vec w {3, 4};
vec x = v + w; // {4, 6}
vec y = 4 * v; // {4, 8}
vec z = v * 4; // {4, 8}
```
**Note:** You cannot overload the operators:

`. .* :: ?:`

You *can* overload every other operator:

`+	-	*	⁄	%	‸	&	|	~
!	=	<	>	+=	-=	*=	⁄=	%=
‸=	&=	|=	<<	>>	<<=	>>=	==	!=
<=	>=	&&	||	++	--	,	->*	->
( )	[ ]	new	delete	new[]	delete[]`

Additionally, `+ - & *` can be overloaded in both their unary and binary form.

**Ex:** Let's overload `<<` and `>>`
```cpp
struct Grade {
    int n;
}

istream& operator>>(istream& in, Grade& g) {
    in >> g.n;
    if (g.n < 0) g.n = 0;
    if (g.n > 100) g.n = 100;
    return in;
}

ostream& operator<<(ostream& out, const Grade& g) {
    return out << g.n << '%';
}

Grade g;
cin >> g;
cout << g;
```

## Separate Compilation

The goal of separate compilation is to speed up compilation times by only recompiling what we need to.

#### Recall:
* Interface files (.h) provide declarations
* Implementation files (.c) provide definitions
#### New (C++20 modules):
```cpp
// Interface (vec.cc)
export module vec;      // Indicates an interface file

export struct vec {     // Anything marked 'export' can be used by clients
    int x, y;
}

export vec operator+(const vec& v1, const vec& v2);
```
```cpp
// Implementation (vec-impl.cc)
module vec;

vec operator+(const vec& v1, const vec& v2) {
    return {v1.x + v2.x, v1.y + v2.y};
} 
```
```cpp
// Client (main.cc)
import vec;

int main() {
    vec v {1, 2};
    vec w = v + v;
}
```

Note that compiler support for C++20 modules is not yet widespread and we will probably switch back to `#include` at some point in the semester.

To summarize,

* Interface files: `export module x;`
* Implementation files: `module x;`
* Client file: `import module x;`