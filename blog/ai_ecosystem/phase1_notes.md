# Phase 1 — Tensor / Math Engine

## What is a Tensor?
A tensor is a multi-dimensional block of numbers.
- 1D → a simple list: `[1, 2, 3]`
- 2D → a table with rows and columns: `[[1,2,3], [4,5,6]]`
- 3D → multiple tables stacked together

In an AI model, everything — weights, inputs, outputs — is a tensor.

---

## How Tensors Live in Memory
Memory is a flat line of boxes. A 2D matrix gets flattened into a 1D array (row-major order).
```
[1, 2, 3, 4, 5, 6]
 0  1  2  3  4  5
```

To find element at row `i`, column `j`:
```
index = i * number_of_columns + j
```

---

## The Tensor Class
Every Tensor stores 3 things:
- `float* data` — raw pointer to numbers on the heap
- `std::vector<int> shape` — dimensions e.g. `{2, 3}`
- `int size` — total elements = product of shape values

---

## Memory Management (Rule of Five)

| Method | Allocate? | Free first? | Steal pointer? |
|---|---|---|---|
| Constructor | yes | no | no |
| Destructor | no | yes | no |
| Copy constructor | yes | no | no |
| Copy assignment | yes | yes | no |
| Move constructor | no | no | yes |
| Move assignment | no | yes | yes |

---

### Constructor
Allocates heap memory and zeros everything out.
```cpp
Tensor::Tensor(std::vector<int> s) {
    shape = s;
    size = 1;
    for (int i = 0; i < shape.size(); i++)
        size *= shape[i];
    data = new float[size];
    for (int i = 0; i < size; i++)
        data[i] = 0.0f;
}
```

### Destructor
Frees heap memory.
```cpp
Tensor::~Tensor() {
    delete[] data;
}
```

### Copy Constructor
Makes a deep copy — brand new memory, same values.
```cpp
Tensor::Tensor(const Tensor& other) {
    shape = other.shape;
    size = other.size;
    data = new float[size];
    for (int i = 0; i < size; i++)
        data[i] = other.data[i];
}
```

### Copy Assignment
Frees existing memory first, then deep copies.
```cpp
Tensor& Tensor::operator=(const Tensor& other) {
    if (this == &other) return *this;
    delete[] data;
    shape = other.shape;
    size = other.size;
    data = new float[size];
    for (int i = 0; i < size; i++)
        data[i] = other.data[i];
    return *this;
}
```

### Move Constructor
Steals pointer from a temporary.
```cpp
Tensor::Tensor(Tensor&& other) {
    shape = other.shape;
    size = other.size;
    data = other.data;
    other.data = nullptr;
    other.size = 0;
}
```

### Move Assignment
Frees existing memory first, then steals pointer.
```cpp
Tensor& Tensor::operator=(Tensor&& other) {
    if (this == &other) return *this;
    delete[] data;
    shape = other.shape;
    size = other.size;
    data = other.data;
    other.data = nullptr;
    other.size = 0;
    return *this;
}
```

---

## Element Access
```cpp
float& Tensor::at(int i, int j) {
    return data[i * shape[1] + j];
}
```
Returns a reference so you can both read and write.

---

## Operations

### Addition
```cpp
Tensor Tensor::operator+(const Tensor& other) {
    Tensor result(shape);
    for (int i = 0; i < size; i++)
        result.data[i] = data[i] + other.data[i];
    return result;
}
```

### Subtraction
```cpp
Tensor Tensor::operator-(const Tensor& other) {
    Tensor result(shape);
    for (int i = 0; i < size; i++)
        result.data[i] = data[i] - other.data[i];
    return result;
}
```

### Scalar Multiply
```cpp
Tensor Tensor::operator*(float scalar) {
    Tensor result(shape);
    for (int i = 0; i < size; i++)
        result.data[i] = data[i] * scalar;
    return result;
}
```

### Matrix Multiply
```cpp
Tensor Tensor::matmul(const Tensor& other) {
    int M = shape[0];
    int K = shape[1];
    int N = other.shape[1];
    Tensor result({M, N});
    for (int i = 0; i < M; i++)
        for (int j = 0; j < N; j++) {
            float sum = 0.0f;
            for (int k = 0; k < K; k++)
                sum += data[i * K + k] * other.data[k * N + j];
            result.data[i * N + j] = sum;
        }
    return result;
}
```

### Transpose
```cpp
Tensor Tensor::transpose() {
    int M = shape[0];
    int N = shape[1];
    Tensor result({N, M});
    for (int i = 0; i < M; i++)
        for (int j = 0; j < N; j++)
            result.data[j * M + i] = data[i * N + j];
    return result;
}
```