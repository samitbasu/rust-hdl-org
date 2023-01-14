+++
title = "Changelog"
description = "Changelog"
weight = 2
+++

# v0.43.0 - 2023-Jan-7

- Add support for right and left shifts by different bit width amounts.  For example,
the following construct used to not be allowed, since RustHDL would insist that the
two arguments to both the left and right shift operators be the same bitwidth.

```rust
#[derive(LogicBlock)]
struct Foo {
    pub sig1: Signal<In, Bits<4>>,
    pub sig2: Signal<In, Bits<2>>,
    pub sig3: Signal<Out, Bits<4>>,
}

impl Logic for Foo {
    #[hdl_gen]
    fn update(&mut self) {
        self.sig3.next = self.sig1.val() << self.sig2.val(); // <-- used to require a bitcast - now it doesn't
    }
}
```

# v0.42.0 - 2023-Jan-2

- Support for comparison operations on signed arguments.  Support for signed quantities is
still improving in RustHDL.  Now you can do:

```rust
#[derive(LogicBlock)]
struct Foo {
    pub sig1: Signal<In, Signed<4>>,
    pub sig2: Signal<In, Signed<4>>,
    pub sig3: Signal<Out, Bit>,
}

impl Logic for Foo {
    #[hdl_gen]
    fn update(&mut self) {
        self.sig3.next = self.sig1.val() < self.sig2.val(); // <-- used to not work at all.  Now it works
    }
}
```

The generated Verilog should have the proper `$signed` and `$unsigned` decorations, but it's good to double
check the output to make sure it looks reasonable.

# v0.41.0 - 2022-Dec-2

No changes.

# v0.40.0 - 2022-Dec-2

- Refactored the I2C bus to make it slightly easier to use.  Now the bus looks like this:

```rust
#[derive(LogicInterface, Default)]
#[join = "I2CBusReceiver"]
pub struct I2CBusDriver {
    pub sda: OpenDrainDriver,
    pub scl: OpenDrainDriver,
}
```

The `OpenDrainDriver` is also a bus definition:

```rust
#[derive(LogicInterface, Default)]
#[join = "OpenDrainReceiver"]
pub struct OpenDrainDriver {
    pub drive_low: Signal<Out, Bit>,
    pub line_state: Signal<In, Bit>,
}
```

This constrains (through the types) the operations that can be legally completed on an open drain pin.

# v0.39.0 - 2022-Nov-30

- There are some issues with synthesis tools that do not properly route analog signals into the design (no names...)
So to be safe you should move your tristate buffers as close to the top of your design hierarchy as possible.  
This release removed the I2C tristate buffers from the controller and moved them out.

# v0.38.0 - 2022-Sep-14

- Clean up some of the doc tests, and remove some debug statements.  Update the homepage in the Cargo manifest to 
point to the [website](https://www.rust-hdl.org).



