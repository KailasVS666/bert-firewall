# Phase 2 — Model Loader (GGUF)

## What is GGUF?
GGUF is the file format llama.cpp uses to store AI models.
It contains everything a model needs in a single file:
- **Metadata** — model name, architecture, settings
- **Tensor data** — the actual weights

---

## GGUF File Structure
[Header][Metadata][Tensor Descriptors][Tensor Data]
---

## Section 1 — Header
The first thing in the file. Contains:
- **Magic number** — 4 bytes: `G G U F` — validates the file
- **Version** — which GGUF version (currently 3)
- **Tensor count** — how many tensors are in the file
- **Metadata count** — how many metadata pairs are in the file
```cpp
// Open file
FILE* file = fopen("model.gguf", "rb");

// Read and validate magic number
char magic[4];
fread(magic, 1, 4, file);
if (magic[0] != 'G' || magic[1] != 'G' ||
    magic[2] != 'U' || magic[3] != 'F') {
    printf("Not a valid GGUF file!\n");
    return;
}

// Read header values
uint32_t version;
uint64_t tensor_count;
uint64_t metadata_count;
fread(&version, sizeof(uint32_t), 1, file);
fread(&tensor_count, sizeof(uint64_t), 1, file);
fread(&metadata_count, sizeof(uint64_t), 1, file);
```

---

## Section 2 — Metadata
A list of key-value pairs describing the model.

Each pair is stored as:
[key_length][key_characters][value_type][value]

Value types:
- `5` = int32
- `6` = float32
- `8` = string
```cpp
for (int i = 0; i < metadata_count; i++) {
    // Read key
    uint64_t key_length;
    fread(&key_length, sizeof(uint64_t), 1, file);
    char key[256];
    fread(key, 1, key_length, file);
    key[key_length] = '\0';

    // Read value type
    uint32_t value_type;
    fread(&value_type, sizeof(uint32_t), 1, file);

    // Read value based on type
    if (value_type == 5) {
        int32_t value;
        fread(&value, sizeof(int32_t), 1, file);
    } else if (value_type == 8) {
        uint64_t str_length;
        fread(&str_length, sizeof(uint64_t), 1, file);
        char value[256];
        fread(value, 1, str_length, file);
        value[str_length] = '\0';
    } else if (value_type == 6) {
        float value;
        fread(&value, sizeof(float), 1, file);
    }
}
```

---

## Section 3 — Tensor Descriptors
Each descriptor is stored as:
[name_length][name][n_dims][dim_1][dim_2]...[data_type][offset]

```cpp
for (int i = 0; i < tensor_count; i++) {
    // Read name
    uint64_t name_length;
    fread(&name_length, sizeof(uint64_t), 1, file);
    char name[256];
    fread(name, 1, name_length, file);
    name[name_length] = '\0';

    // Read number of dimensions
    uint32_t n_dims;
    fread(&n_dims, sizeof(uint32_t), 1, file);

    // Read dimensions
    uint64_t dims[4];
    for (int i = 0; i < n_dims; i++)
        fread(&dims[i], sizeof(uint64_t), 1, file);

    // Read data type
    uint32_t data_type;
    fread(&data_type, sizeof(uint32_t), 1, file);

    // Read offset
    uint64_t offset;
    fread(&offset, sizeof(uint64_t), 1, file);
}
```

---

## Data Types
| Type | Bytes | Precision |
|---|---|---|
| float32 | 4 | Full precision |
| float16 | 2 | Half precision |
| int8 | 1 | Quantized |

---

## Section 4 — Reading Tensor Data
```cpp
// Jump to tensor data position
fseek(file, offset, SEEK_SET);

// Calculate total elements
int total = 1;
for (int i = 0; i < n_dims; i++)
    total *= dims[i];

// Create tensor and read data
Tensor t(std::vector<int>(dims, dims + n_dims));
fread(t.data, sizeof(float), total, file);

// Store tensor by name
tensors[name] = t;
```

---

## Key Concepts
- Every string in GGUF → length first, then characters
- `offset` → byte position where tensor data starts in the file
- `fseek` → jumps to exact byte position in the file
- `fread` → reads raw bytes from file into memory