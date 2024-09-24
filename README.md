# Qwen2.java

Practical [Qwen2](https://github.com/QwenLM/Qwen2) inference implemented in a single Java file.

This project is the successor of [llama2.java](https://github.com/mukel/llama2.java)
based on [llama2.c](https://github.com/karpathy/llama2.c) by [Andrej Karpathy](https://twitter.com/karpathy) and his [excellent educational videos](https://www.youtube.com/c/AndrejKarpathy).

Besides the educational value, this project will be used to test and tune compiler optimizations and features on the JVM, particularly for the [Graal compiler](https://www.graalvm.org/latest/reference-manual/java/compiler).

## Features

 - Single file, no dependencies
 - [GGUF format](https://github.com/ggerganov/ggml/blob/master/docs/gguf.md) parser
 - Qwen 2 tokenizer based on [minbpe](https://github.com/karpathy/minbpe)
 - Qwen 2 inference with Grouped-Query Attention
 - Support for Q8_0 and Q4_0 quantizations
 - Simple CLI with `--chat` and `--instruct` modes.
 - Compatible with GraalVM's native-image.

## Setup

Download pure `Q4_0` and/or `Q8_0` quantized .gguf files from:
https://huggingface.co/collections/mukel/qwen2-666644562f3762a838f035de

Please be gentle with [huggingface.co](https://huggingface.co) servers:
```
# Download the 1.5B parameter Q8_0 quantized model
curl -L -O https://huggingface.co/mukel/Qwen2-1.5B-Instruct-GGUF/resolve/main/Qwen2-1.5B-Instruct-Q8_0.gguf
```

#### Optional: quantize to pure `Q4_0` manually

In the wild, `Q8_0` quantizations are fine, but `Q4_0` quantizations are rarely pure e.g. the `output.weights` tensor is quantized with `Q6_K`, instead of `Q4_0`.  
A **pure** `Q4_0` quantization can be generated from a high precision (F32, F16, BFLOAT16) .gguf source 
with the `quantize` utility from [llama.cpp](https://github.com/ggerganov/llama.cpp) as follows:

```bash
./llama-quantize --pure Qwen2-1.5B-Instruct-F32.gguf Qwen2-1.5B-Instruct-Q4_0.gguf Q4_0
```

## Build and run

[`jbang`](https://www.jbang.dev/) is a perfect fit for this use case, just:
```
jbang Qwen2.java --help
```
Or execute directly, also via [`jbang`](https://www.jbang.dev/):
```bash 
chmod +x Qwen2.java
./Qwen2.java --help
```

## Run from source

```bash
java Qwen2.java --model Qwen2-1.5B-Instruct-Q8_0.gguf --chat
```

#### Optional: Makefile + manually build and run

A simple [Makefile](./Makefile) is provided, run `make` to produce `qwen2.jar` or manually:
```bash
javac -g -d target/classes Qwen2.java
jar -cvfe qwen2.jar com.llama4j.Qwen2 LICENSE -C target/classes .
```

Run the resulting `qwen2.jar` as follows:
```bash
java -jar qwen2.jar --help
java -jar qwen2.jar --model Qwen2-1.5B-Instruct-Q8_0.gguf --chat

```

#### Native Image

Build a native image:

```bash
native-image -jar qwen2.jar -o qwen2
```

Run:

```bash
./qwen2 --help
```

For example:

```bash
./qwen2 --model Qwen2-1.5B-Instruct-Q8_0.gguf --chat
```

## License

MIT
