# jinja.cpp

![License](https://img.shields.io/badge/license-Apache%20License%202.0-green)
![Build Status](https://github.com/wangzhaode/jinja.cpp/actions/workflows/build.yml/badge.svg)
[![中文版本](https://img.shields.io/badge/Language-%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87-green)](README_CN.md)

A lightweight, single-header C++11 Jinja2 template engine designed for LLM chat templates. ** (HuggingFace style).

It focuses on supporting the subset of Jinja2 used by modern Large Language Models (LLMs) like Llama 3, Qwen 2.5/3, DeepSeek, and others, enabling seamless inference integration in C++ environments.

## Features

- **C++11 Compatible**: Ensures maximum compatibility across older compiler versions and embedded systems.
- **Flexible JSON Backend**: Supports both `nlohmann/json` (default) and `RapidJSON` via a unified `ujson` bridge.
- **Lightweight**: Minimal dependencies, with all required headers included in `third_party/`.
- **LLM Focused**: Native support for `messages`, `tools`, `add_generation_prompt`, and special tokens.
- **Unified Context**: Uses `jinja::json` (an alias to `ujson::json`) for seamless context management.
- **Custom Function Interop**: Easily inject C++ functions (e.g., `strftime_now`) into templates.
- **Robust**: Validated against official Python `transformers` outputs using fuzzy matching tests on 390+ cases.

## Integration

### Headers
The library consists of two main headers:
- `jinja.hpp`: Core template engine.
- `third_party/ujson.hpp`: Unified JSON bridge.

Just copy the `jinja.hpp` and `third_party` directory to your project.

### Feature Checking (Versioning)

You can check the library version using standard macros:

```cpp
#include "jinja.hpp"

#if JINJA_VERSION_MAJOR >= 0
    // Use jinja.cpp features
#endif
```

## Supported Models

Tested and verified with templates from:
- **Qwen 2.5 / 3** (Coder, Math, VL, Omni, Instruct, Thinking, QwQ)
- **DeepSeek** (V3, R1)
- **Llama 3 / 3.1 / 3.2** (Instruct & Vision)
- **Mistral**
- **Gemma**
- **SmolLM**
- **Phi**
- And more...

## Build Instructions

### Prerequisites
- CMake 3.10+
- C++11 compatible compiler (GCC, Clang, MSVC)

```bash
mkdir build
cd build
cmake ..
make
```

### Enable RapidJSON Backend
To use `RapidJSON` instead of `nlohmann/json` for better performance:
```bash
cmake .. -DUJSON_USE_RAPIDJSON=ON
```
*Note: Ensure `third_party/rapidjson` is available.*

### Run Tests

The project includes a comprehensive test suite based on real-world model templates.

```bash
./test_main
```

## Usage

### Basic Rendering

```cpp
#include "jinja.hpp"
#include <iostream>

int main() {
    std::string template_str = "Hello {{ name }}!";
    jinja::Template tpl(template_str);

    jinja::json context;
    context["name"] = "World";

    std::string result = tpl.render(context);
    std::cout << result << std::endl; // Output: Hello World!
    return 0;
}
```

### LLM Chat Template

```cpp
#include "jinja.hpp"

// Load your tokenizer_config.json's "chat_template"
std::string chat_template_str = "...";
jinja::Template tpl(chat_template_str);

jinja::json messages = jinja::json::array({
    {{"role", "user"}, {"content", "Hello!"}}
});

// Apply template
std::string prompt = tpl.apply_chat_template(
    messages,
    true, // add_generation_prompt
    jinja::json::array() // tools
);
```

### Custom Functions

You can register custom C++ functions to be called from within the template.

```cpp
tpl.add_function("strftime_now", [](const std::vector<jinja::json>& args) {
    // Return current time string
    return "2025-12-16";
});
```

## Documentation

For detailed implementation details, see [doc/implementation_details.md](doc/implementation_details.md).

## License

Apache License 2.0. See [LICENSE](LICENSE) file for details.