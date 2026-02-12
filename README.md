# llama-kernel toolset

Minimal kernel dump + LLVM IR helper for llama.cpp (MMVQ only for now).

## Config (required once)

Edit `llama-kernel.toml`:

```
llamacpp = "/app/llama.cpp"
out = "/app/kernel_dumps"
kernel = "mmvq"
# build_dir = "/app/llama.cpp/build"  # optional
```

The tool searches for `compile_commands.json` under the build dir.

## Commands

Dump AMDGCN + device BC + mangled map:

```
./llama-kernel dump
```

Generate LLVM IR log for a chosen MMVQ symbol:

```
./llama-kernel llvmir --filter "mul_mat_vec_q<(ggml_type)3, 1, false>"
```

If `--filter` matches multiple symbols (or is omitted), the tool will prompt you to select one.

## Output layout

```
<out>/mmvq/
  amdgcn.s
  device.bc
  mangled_map.tsv
  llvmir/<symbol>/
    llvmir.log
    asm.s
    metadata.txt
```

## Notes

- Requires ROCm LLVM tools (`llvm-dis`, `llvm-objcopy`) and `c++filt` in PATH.
- `metadata.txt` is extracted from the device code object using `llvm-readobj --notes` and filtered to the selected symbol.
- For new builds, ensure `compile_commands.json` is generated.
