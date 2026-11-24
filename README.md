# CVFPU - the CORE-V Floating-Point Unit with Transprecision Capabilities

Parametric floating-point unit with support for standard RISC-V formats and operations as well as transprecision formats, written in SystemVerilog.

Originally developed by Stefan Mach <smach@iis.ee.ethz.ch> and Luca Bertaccini <lbertaccini@iis.ee.ethz.ch> as the FPnew project.<br>
Later transfered to the OpenHW Foundation and rebranded as CVFPU.<br>
Past maintainers: Pasquale Davide Schiavone, Pascal Gouedo.

## Features

The CVFPU is a parametric design that allows generating FP hardware units for various use cases.
CVFPU is compliant with IEEE 754-2008 and provides the following features:

### Formats
Any IEEE 754-2008 style binary floating-point format can be supported, including single-, double-, quad- and half-precision (`binary32`, `binary64`, `binary128`, `binary16`).
Formats can be defined with arbitrary number of exponent and mantissa bits through parameters and are always symmetrically biased.
Multiple FP formats can be supported concurrently, and the number of formats supported is not limited.

Multiple integer formats with arbitrary number of bits (as source or destionation of conversions) can also be defined.

### Operations
- Addition/Subtraction
- Multiplication
- Fused multiply-add in four flavours (`fmadd`, `fmsub`, `fnmadd`, `fnmsub`)
- Division<sup>1</sup>
- Square root<sup>1</sup>
- Minimum/Maximum<sup>2</sup>
- Comparisons
- Sign-Injections (`copy`, `abs`, `negate`, `copySign` etc.)
- Conversions among all supported FP formats
- Conversions between FP formats and integers (signed & unsigned) and vice versa
- Classification

Multi-format FMA operations (i.e. multiplication in one format, accumulation in another) are optionally supported.

Optionally, *packed-SIMD* versions of all the above operations can be generated for formats narrower than the FPU datapath width.
E.g.: Support for double-precision (64bit) operations and two simultaneous single-precision (32bit) operations.

It is also possible to generate only a subset of operations if e.g. divisions are not needed.

<sup>1</sup>Two DivSqrt units are supported, selected by the `DivSqrtSel` parameter: `THMULTI`, a multi-format unit (FP64, FP32, FP16, FP16ALT, packed-SIMD) integrated from the T-Head OpenC910, and `TH32`, an FP32-only unit integrated from the T-Head OpenE906. FP8 division and square root are not supported.<br>
<sup>2</sup>Implementing IEEE 754-201x `minimumNumber` and `maximumNumber`, respectively

### Rounding modes
All IEEE 754-2008 rounding modes are supported, namely
- `roundTiesToEven`
- `roundTiesToAway`
- `roundTowardPositive`
- `roundTowardNegative`
- `roundTowardZero`

### Status Flags
All IEEE 754-2008 status flags are supported, namely
- Invalid operation (`NV`)
- Division by zero (`DZ`)
- Overflow (`OF`)
- Underflow (`UF`)
- Inexact (`NX`)

## Getting Started

### Dependencies

CVFPU currently depends on the following:
- `lzc` and `rr_arb_tree` from the `common_cells` repository (https://github.com/pulp-platform/common_cells.git)

This repository is included in the source code directory as a git submodule, use
```bash
git submodule update --init --recursive
```
if you want to load this dependency there.

Consider using [Bender](https://github.com/fabianschuiki/bender.git) for managing dependencies in your projects.
CVFPU comes with Bender support!

### Usage

The top-level module of the FPU is called `fpnew_top` and can be directly instantiated in your design.
Make sure you compile the package `fpnew_pkg` ahead of any files making references to types, parameters or functions defined there.

It is discouraged to `import` all of `fpnew_pkg` into your source files. Instead, explicitly scope references into the package like so: `fpnew_pkg::foo`.

#### Example Instantiation

```SystemVerilog
// FPU instance
fpnew_top #(
  .Features       ( fpnew_pkg::RV64D          ),
  .Implementation ( fpnew_pkg::DEFAULT_NOREGS ),
  .TagType        ( logic                     )
) i_fpnew_top (
  .clk_i,
  .rst_ni,
  .operands_i,
  .rnd_mode_i,
  .op_i,
  .op_mod_i,
  .src_fmt_i,
  .dst_fmt_i,
  .int_fmt_i,
  .vectorial_op_i,
  .simd_mask_i,
  .tag_i,
  .in_valid_i,
  .in_ready_o,
  .flush_i,
  .result_o,
  .status_o,
  .tag_o,
  .out_valid_o,
  .out_ready_i,
  .busy_o,
  .early_valid_o
);
```

### Documentation

More in-depth documentation on the CVFPU configuration, interfaces and architecture is provided in [`docs/README.md`](docs/README.md).

### Repository Structure

HDL source code can be found in the `src` directory while documentation is located in `docs`.
A changelog is kept at [`docs/CHANGELOG.md`](docs/CHANGELOG.md).

This repository loosely follows the [GitFlow](https://nvie.com/posts/a-successful-git-branching-model/) branching model.
This means that the `master` branch is considered stable and used to publish releases of the FPU while the `develop` branch contains features and bugfixes that have not yet been properly released.

Furthermore, this repository tries to adhere to [SemVer](https://semver.org/), as outlined in the [changelog](docs/CHANGELOG.md).

## Licensing

CVFPU is released under the *SolderPad Hardware License*, which is a permissive license based on Apache 2.0. Please refer to the [SolderPad license file](LICENSE.solderpad) for further information.

The T-Head E906 DivSqrt unit, integrated into CVFPU in [`vendor/opene906`](vendor/opene906), is reseased under the *Apache License, Version 2.0*. Please refer to the [Apache 2.0 license file](LICENSE.apache) for further information.

## Publication

If you use CVFPU in your work, please cite the OpenHW Foundation and the PULP-Platform team.
Citations for the 'FPnew' project are below:

<details>
<summary>FPnew Publication</summary>
<p>

```
@article{mach2020fpnew,
  title={Fpnew: An open-source multiformat floating-point unit architecture for energy-proportional transprecision computing},
  author={Mach, Stefan and Schuiki, Fabian and Zaruba, Florian and Benini, Luca},
  journal={IEEE Transactions on Very Large Scale Integration (VLSI) Systems},
  volume={29},
  number={4},
  pages={774--787},
  year={2020},
  publisher={IEEE}
}
```

</p>
</details>


## Acknowledgement

The 'FPnew' project received funding from the European Union's Horizon 2020 research and innovation programme under grant agreement No 732631.

For further information, visit [oprecomp.eu](http://oprecomp.eu).

![OPRECOMP](docs/fig/oprecomp_logo_inline1.png)
