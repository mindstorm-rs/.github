# Mindstorm-RS

 This is an SDK to use Rust on top of the ev3rt real-time operating system.

## Available Repositories

 There are several subprojects, these are the useful ones:

 - `ev3rt-hrp2` is our fork of ev3rt; we mainly added a `Dockerfile` to simplify rebuilding the kernel image
 - `ev3rt-hrp2-sdk` is our fork of the ev3rt C SDK; we mainly added a few additional APIs to the "user space" SDK
 - `ev2rt-rs` is the Rust wrapper of the C SDK: it is the one you should import in your `Cargo.toml` and contains the base APIs you can use to fully control an EV3
 - `ev3-rs` is a very opinionated high-level API on top of `ev3rt-rs`, we like it but you can safely ignore it
 - `ev3rtrs-link` contains a pre-built kernel image and SDK object files that are needed to build your application, and scripts to link and upload the ecexutable
 - `ev3rt-rs1example` is a toy example that puts everything together

## Getting Started

### Setting up your code

Create a crate using `ev3rt-rs1example` as a template.

You will use `ev2rt-rs` and, optionally, `ev2-rs` as dependencies.
Building your crate will produce a statically linked library containing your code.
Be sure to use `cargo xbuild` to do the build otherwise the linked code will be too large (you *will* need to recompile the core library, this is what the `xbuild` command is for in this case).
The `rust-toolchain` file in the example points to a known-working toolchain.

### Prepare the EV3

Clone the `ev3rtrs-link` project, find the `uImage` file in the firmware directory, copy it to the root of a micro-sd card and insert the card into your EV3.
This will make the EV3 boot into the ev3rt OS.
If you want to *rebuild* the image use the `ev3rt-hrp2` and `ev3rt-hrp2-sdk` projects, but to just use the provided Rust bindings this step is not needed.

### Link your Executable

From the cloned `ev3rtrs-link`, execute the `link.sh` script pointing it to the static library (`app.a`) produced by building your application.
Note that you will need to have installed an arm cross-compilation toolchain to do this, in general any version of `arm-none-eabi-gcc` will work.
We found no way around this step: the ev3rt executable loader is very picky about the binary file layout, and we could replicate it only using the priginal linker script.
The pre-compiled object files of the C SDK are provided in the `base-objs` directory and can be rebuilt from the `ev3rt-hrp2` and `ev3rt-hrp2-sdk` projects, but again, to use the provided Rust bindings you can use the pre-compiled SDK object files.

At the end of the linking step you will have an executable that can be loaded by ev3rt on the EV3.

### Run your executable

In any case your executable must end up in the ev3rt application folder on the sd card.

You have three ways to get there:

- manually copy it on the card (extracting it from the EV3 and inserting it in your PC): this is not reccommended but it works fine
- connecting the EV3 to your PC with USB while ev3rt is running will expose the SD card as a disk, you can then copy the executable there
- pairing the EV3 using bluotooth and connecting it (see instructions [here](http://ev3rt-git.github.io/get_started/)) allows you to copy the executable over HTTP (see the `upload.sh` script for how to do it).
