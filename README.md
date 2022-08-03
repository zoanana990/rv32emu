# RISC-V RV32I[MAC] emulator with ELF support

`rv32emu` is an instruction set architecture (ISA) emulator implementing the 32 bit [RISC-V processor model](https://riscv.org/technical/specifications/).

## Build and Verify

`rv32emu` relies on some 3rd party packages to be fully usable and to provide you full
access to all of its features. You need to have a working [SDL2 library](https://www.libsdl.org/)
on your target system.
* macOS: `brew install sdl2`
* Ubuntu Linux / Debian: `sudo apt install libsdl2-dev`

Build the emulator.
```shell
make
```

Run sample RV32I[MA] programs:
```shell
make check
```

Run [Doom](https://en.wikipedia.org/wiki/Doom_(1993_video_game)), the classical video game, via `rv32emu`:
```shell
make demo
```

The build script will then download data file for Doom automatically. SDL2 based window
should appear when Doom is loaded and executed.

## riscv-arch-test

The RISC-V Architectural Tests, [riscv-arch-test](https://github.com/riscv-non-isa/riscv-arch-test), is the basic set of tests that can ensure the risc-v model's behavior that matches RISC-V specifications while implementing specific software.(not a **substitute for rigorous design verification**)

There are **reference signatures** that generated by the formal RISC-V model [RISC-V SAIL](https://github.com/riscv/sail-riscv) and the Executable and Linkable Format(`ELF`) files. The `ELF` files such as `cadd-01.elf` that contain several testing instructions, data, and **signatures**. The **test signatures** are the specific data locations that need to be written by testing model(this emulator) during the test. Once the test is executed, the **test signatures** will be written and compared to the **reference signature**. The test will pass if both signatures exactly match.

[RISC-V GNU Compiler Toolchain](https://github.com/riscv-collab/riscv-gnu-toolchain) should be prepared in advance. You can obtain prebuilt GNU toolchain for `riscv32-elf` via [Automated Nightly Release](https://github.com/riscv-collab/riscv-gnu-toolchain/releases). Then, run the following command:
```shell
make arch-test
```

* To run the tests for specific extension, set the environmental variable `RISCV_DEVICE` to one of `I`,`M`,`C`,`Zifencei`,`privilege`.
```shell
make arch-test RISCV_DEVICE=I
```
* To run a specific test case,set both `RISCV_DEVICE` and `RISCV_TEST`.For example:
```shell
make arch-test RISCV_DEVICE=M RISCV_TEST=div-01
```
The details about the setup environment variables can be found in the [RISC-V Architectural Testing Framework](https://github.com/riscv-non-isa/riscv-arch-test/blob/master/doc/README.adoc), **5.1 Setup environment variables**.

Current progress of this emulator in riscv-arch-test(RV32):
* Passed Tests
    - `I`: Base Integer Instruction Set
    - `M`: Standard Extension for Integer Multiplication and Division
    - `Zifencei`: Instruction-Fetch Fence
* Failed Tests
    - `C`: Standard Extension for Compressed Instruction
        + `cebreak`
    - `privilege`: RISCV Privileged Specification
        + 2 system calls
            * `ebreak`
            * `ecall`
        + 5 misaligned CB-type instructions
            * `misalign-beq`
            * `misalign-bge`
            * `misalign-bgeu`
            * `misalign-blt`
            * `misalign-bltu`
        + 1 misaligned CL-type instructions
            * `misalign-lw`
        + 1 misaligned CS-type instructions
            * `misalign-sw`
* Unsupported tests
    - `F` Standard Extension for Single-Precision Floating-Point

Detail in riscv-arch-test:
* [riscv-arch-test repository](https://github.com/riscv-non-isa/riscv-arch-test)
* [RISC-V Architectural Testing Framework](https://github.com/riscv-non-isa/riscv-arch-test/blob/master/doc/README.adoc)
* [RISC-V Architecture Test Format Specification](https://github.com/riscv-non-isa/riscv-arch-test/blob/master/spec/TestFormatSpec.adoc)

## Customization

`rv32emu` is configurable, and you can modify `Makefile` to fit your expectations:
* `ENABLE_RV32M`: Standard Extension for Integer Multiplication and Division
* `ENABLE_RV32A`: Standard Extension for Atomic Instructions
* `ENABLE_RV32C`: Standard Extension for Compressed Instructions (RV32C.F excluded)
* `Zicsr`: Control and Status Register (CSR)
* `Zifencei`: Instruction-Fetch Fence

Add `-D` to enable and `-U` to disable the specific ISA extensions.

## External sources

In `rv32emu` repository, there are some prebuilt ELF files for testing purpose.
* `quake.elf` : See [sysprog21/quake-embedded](https://github.com/sysprog21/quake-embedded)
* `coremark.elf` : See [eembc/coremark](https://github.com/eembc/coremark)
* `pi.elf` : See [tests/pi.c](tests/pi.c)

## Reference

* [Writing a simple RISC-V emulator in plain C](https://fmash16.github.io/content/posts/riscv-emulator-in-c.html)
* [Writing a RISC-V Emulator in Rust](https://book.rvemu.app/)
* [libriscv: RISC-V userspace emulator library](https://github.com/fwsGonzo/libriscv)
* [Threaded Code](https://www.complang.tuwien.ac.at/forth/threaded-code.html)

## License
`rv32emu` is released under the MIT License.
Use of this source code is governed by a MIT license that can be found in the LICENSE file.
