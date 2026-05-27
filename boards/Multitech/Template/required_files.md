# Table of contents
- [Table of contents](#table-of-contents)
- [board.yml](#boardyml)
- [\[BOARD\].dts](#boarddts)
	- [\[BOARD\].dtsi](#boarddtsi)
	- [\[BOARD\]-pinctrl.dtsi](#board-pinctrldtsi)
		- [Example](#example)
	- [\[BOARD\].dts](#boarddts-1)
		- [Example](#example-1)
	- [Multi-File Example](#multi-file-example)
- [Kconfig.\[BOARD\]](#kconfigboard)
	- [Example](#example-2)

# board.yml
```
boards:
  - name: swt_043a_01
    full_name: Multitech Platform Development Board (SWT-043A-01)
    vendor: multitech
    socs:
      - name: efr32bg24a020f1024im40
```
`board.yml` is a basic description of the board.
- name
    - Name of the board, should be the MultiTech project number
    - Should be all lower case
    - e.g. b01-010a-05
- full_name
    - Human readable name
    - e.g. TRex LR
- Vendor
    - multitech
- socs
    -  "name: [SOC part number]"
    -  A full list of Zephyr supported boards is available here: [Supported Boards](https://docs.zephyrproject.org/latest/boards/index.html#)
    -  e.g. efr32bg24a020f1024im40

# [BOARD].dts
Device tree file for the board, describes how the hardware (pins, timers, clocks, etc.) are set up. These are often split into multiple files, e.g. `[BOARD].dts, [BOARD].dtsi, and [BOARD]-pinctrl.dtsi`

## [BOARD].dtsi
`.dtsi` files are devicetree files that are meant to be included in other files. For a board, it generally describes system level configurations that the board will not function without. E.g. configuring the flash map.

The `[BOARD].dtsi` file is included in `[BOARD.dts]`.

For most applications, the settings inside the `.dtsi` shouldn't be changed by the user application.

## [BOARD]-pinctrl.dtsi
"pinctrl" files describe pinctrl states. The standard states for all peripherals are `default` and `sleep`, which describe how the pins used by the peripherals should be set in each state.

### Example
```	
eusart1_default: eusart1_default {
	group0 {
		pins = <EUSART1_TX_PA4>, <EUSART1_SCLK_PA3>;
		drive-push-pull;
		output-high;
	};
	group1 {
		pins = <EUSART1_RX_PA5>;
		input-enable;
		silabs,input-filter;
	};
};
```
Creates 2 groups for the default state of `EUSART1`, `group0` sets PA4 and PA3 to be MOSI and SCK respectively, and sets them both to be push-pull and output high. `group1` sets PA5 to MISO and an input with filter.

```
eusart1_sleep: eusart1_sleep {
	group0 {
		pins = <EUSART1_TX_PC1>, <EUSART1_RX_PC2>, <EUSART1_SCLK_PC3>;
    };
};
```
Creates 1 group for the sleep state of `EUSART`, which returns the pins to the default setting (giving no properties to a group will disable the pins).

## [BOARD].dts
Describes what the peripherals are used for and the specific configurations for that purpose.

### Example
```
&usart0 {
	current-speed = <115200>;
    ...
};
```
Sets the baud rate for `usart0` to 115200

## Multi-File Example 

`pinctrl.dtsi`
```
i2c0_default: i2c0_default {
	group0 {
		pins = <I2C0_SCL_PC5>, <I2C0_SDA_PC7>;
		drive-open-drain;
		bias-pull-up;
	};
};
```
This creates a state for `i2c0` called `i2c0_default` with one pin group. The pin group puts SCL on PC5, SDA on PC7, and sets them both as `drive-open-drain` and `bias-pull-up`, which is the equivalent to `WIREDANDPULLUP` in Simplicity Studio.

`.dtsi`
```
&i2c0 {
	pinctrl-0 = <&i2c0_default>;
	pinctrl-names = "default";
	status = "okay";
};
```
This sets `pinctrl-0` for peripheral to be `i2c0_default` from the `pinctrl` file, and gives that state the name "default". That means when the SoC is in the its state named "default" it will set the pins according to `i2c0-default`. Finally it sets it's status to `okay` which enables the peripheral.

`.dts`
```
&i2c0 {
	status = "okay";

	bme280@76 {
		compatible = "bosch,bme280";
		status = "okay";
		reg = <0x76>;
	};

    ...
};
```
This ensures `i2c0` is enabled, then adds a node named `bme280` with unit address `76` to `i2c0`. It sets the node to be compatible with `bosch,bme280` which tells Zephyr it can treat this instance as a BME280 sensor from Bosch. Lastly, it sets the register to `76`. The format of `reg` varies based on the peripheral type but it is used to contain information about how to address the device. Therefore, for I2C `reg` is just the address, but it could contain other types of values for other peripherals 

# Kconfig.[BOARD]
Kconfig options for the board.

## Example
```
config BOARD_SWT_043A_01
	select SOC_EFR32BG24A020F1024IM40
```
The config snippet above creates a configuration named `BOARD_SWT_043A_01` which if selected (set to "y"), will also set `SOC_EFR32BG24A020F1024IM40` to "y". This is mandatory because it tells the build system which SoC we are compiling for.
