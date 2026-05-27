## board.cmake  

```
board_runner_args(jlink "--device=EFR32BG24AxxxF1024")
include(${ZEPHYR_BASE}/boards/common/jlink.board.cmake)

board_runner_args(silabs_commander "--device=${CONFIG_SOC}")
include(${ZEPHYR_BASE}/boards/common/silabs_commander.board.cmake)

board_runner_args(openocd)
include(${ZEPHYR_BASE}/boards/common/openocd.board.cmake)
```
board.cmake is used to provide extra args to the board runner. The board runner is the software that uploads or debugs your board, e.g. `board_runner_args(jlink "--device=EFR32BG24AxxxF1024")` Adds the argument `--device=EFR32BG24AxxxF1024` for the J-Link runner.<br> <br>
Generally the below snippet is sufficient for a J-Link runner.
```
board_runner_args(jlink "--device=<SOC_PN>")
include(${ZEPHYR_BASE}/boards/common/jlink.board.cmake)
``` 

## CMakeLists.txt
`CMakeLists.txt` allows you to add CMake code specific for your board. Normally it can be left empty.

## [SOC-PN].svd
A `.svd` file maps the memory addresses on the SoC to registers for the debugger to display. When adding a new board, place the file in the board directory. Technically you don't need to use the file but it is useful for low level debugging.

`.svd` files are available here [CMSIS Packs](https://www.keil.arm.com/packs/). Search for you part number and download the `.pack` file. You can unzip it as if it was `.zip` and it contains the `.svd` among other things.

## Doc
`Doc` is the documentation folder, a picture of the board (3D render or photo) should be included as well as a read me file that describes the board. See the [Template Doc Readme](doc/readme.md) for more details

## Kconfig.defconfig
```
if BOARD_SWT_043A_01

config FPU
	default y if SOC_GECKO_USE_RAIL || BT

if BT

config MAIN_STACK_SIZE
	default 3072 if PM
	default 2304

endif # BT

endif

config BOARD_SWT_043A_01
	select SOC_EFR32BG24A020F1024IM40
```

`Kconfig.defconfig` is a file that chooses settings based on other settings.

### Example
```
if BT
config MAIN_STACK_SIZE
	default 3072 if PM
	default 2304

endif # BT
```
This does the following.
- If BT is enabled
    - Set default MAIN_STACK_SIZE to
        - 3072 if PM is enabled
        - 2304 otherwise <br>
  
Note that from the file, only the default is set, therefore if MAIN_STACK_SIZE is set to something elsewhere, it will overide this.

```
config BOARD_SWT_043A_01
	select SOC_EFR32BG24A020F1024IM40
```
This part is mandatory for boards as it selects the SoC on the board.


## [BOARD-PN]_defconfig
Default configuration for the board. These settings should be low level settings that the board should have on to function. E.g. when compiling for EFR32BG24, pre-compiled libraries are used that use an FPU, therefore in `[BOARD-PN]_defconfig`, there must be `CONFIG_FPU=y` or the FPU won'y be enabled.


## [BOARD-PN].yaml
This is for a more detailed description of the board. If multiple boards are contained in the same direction, e.g. they are variants of each other, then both should be listed in this file.

## pre_dt_board.cmake
An optional file that adds CMake code before the board does the devicetree (dt) compilation for the board. Often this file can be omitted.
```
# SPI is implemented via usart so node name isn't spi@...
list(APPEND EXTRA_DTC_FLAGS "-Wno-spi_bus_bridge")
```
This example or something similar must be used with EFR32XG24 SoCs since their SPI is implemented via EUSART or USART, the instructions tell the devicetree compiler (dtc) not to call SPI nodes "spi@.." since they should be USART.
