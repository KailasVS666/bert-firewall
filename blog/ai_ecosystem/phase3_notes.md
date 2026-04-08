# Phase 3 - Quantization

## Overview

Quantization reduces model size and improves inference speed by storing weights in lower precision formats such as Q4 (4-bit) and Q8 (8-bit), instead of float32.

## Why Quantization?

- Reduce memory usage
- Improve cache efficiency
- Reduce memory bandwidth usage
- Enable CPU inference

## Key Idea

Instead of storing full float values:

```text
float32 -> 32 bits
```

We store:

```text
quantized_value (int) + scale
```

Reconstruction:

```text
real_value = scale * quantized_value
```

## Block-Based Storage

Weights are stored in blocks.

Each block contains:

```text
[ scale | quantized_values ]
```

- One scale per block
- Multiple quantized values share that scale

## Q8 Format

### Structure

```text
[ scale (float) | int8 values ]
```

### Formula

```text
real_value = scale * q[i]
```

### Example

```text
scale = 0.05
q = [10, -4, 20]

output = [0.5, -0.2, 1.0]
```

## Q4 Format

### Structure

- 1 byte stores 2 values (4 bits each)

```text
[ high 4 bits | low 4 bits ]
```

### Extraction

```cpp
uint8_t low  = byte & 0x0F;
uint8_t high = (byte >> 4) & 0x0F;
```

### Convert to Signed

```text
signed_value = q - 8
range: [0..15] -> [-8..7]
```

### Final Formula

```text
real_value = scale * (q - 8)
```

### Q4 Example

```text
byte  = 0b01100101
scale = 0.1

low  = 5 -> -3 -> -0.3
high = 6 -> -2 -> -0.2
```

## Dequantization Functions

### Q4

```cpp
void dequantize_q4(const uint8_t* data, float scale, float* output, int n) {
    int out_idx = 0;

    for (int i = 0; i < n / 2; i++) {
        uint8_t byte = data[i];

        uint8_t low  = byte & 0x0F;
        uint8_t high = (byte >> 4) & 0x0F;

        int8_t low_s  = low - 8;
        int8_t high_s = high - 8;

        output[out_idx++] = scale * low_s;
        output[out_idx++] = scale * high_s;
    }
}
```

### Q8

```cpp
void dequantize_q8(const int8_t* data, float scale, float* output, int n) {
    for (int i = 0; i < n; i++) {
        output[i] = scale * data[i];
    }
}
```

## Quantized Matrix Multiplication

Instead of:

```text
dequantize -> store -> multiply
```

Use:

```text
(dequantize on-the-fly) + multiply
```

### Q8 MatMul

```cpp
sum += input[i] * (scale * q[i]);
```

### Q4 MatMul

```cpp
for (int i = 0; i < n / 2; i++) {
    uint8_t byte = data[i];

    uint8_t low  = byte & 0x0F;
    uint8_t high = (byte >> 4) & 0x0F;

    int8_t low_s  = low - 8;
    int8_t high_s = high - 8;

    sum += input[2 * i] * (scale * low_s);
    sum += input[2 * i + 1] * (scale * high_s);
}
```

## Why Not Fully Dequantize?

- Memory blow-up (Q4 -> float32 is 8x larger)
- Increased memory bandwidth
- Slower computation

## Key Concepts Summary

- Block-based quantization
- Scale + quantized values
- Bit packing (Q4)
- On-the-fly dequantization
- Memory bandwidth optimization

## Mental Model

- Q8 -> simple scaling
- Q4 -> packed values + offset
- Quantization = compression + reconstruction