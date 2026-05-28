Board Template
==============

The `Zephyr Board Porting Guide`
` <https://docs.zephyrproject.org/latest/hardware/porting/board_porting.html>`_ 


and `Board Directory Documentation <https://docs.zephyrproject.org/latest/hardware/porting/board_porting.html#create-your-board-directory>`__
give a good overview on board files. This read me will walk through the
example template.

Board Directory
---------------
:: 
   boards/MultiTech/[BOARD-PN]
   |-- board.yml
   |-- board.cmake             (optional)
   |-- CMakeLists.txt          (optional)
   |-- <SOC-PN>.svd            (optional)
   |-- Doc                     (optional)
   |   |-- readme.md
   |   |-- image.png
   |-- Kconfig.[BOARD-PN]     
   |-- Kconfig.defconfig       (optional)
   |-- [BOARD-PN]_defconfig    (optional)
   |-- [BOARD-PN].dts
   |-- [BOARD-PN].yaml         (optional)
   |-- support                 (optional)
   |   |-- ...
   |-- pre_dt_board.cmake      (optional)

When creating files, the names of the files are case sensitive and
normally are all lower-case. Board names are also generally lower-case
and must be only alphanumeric characters and hyphens.

Required Files
==============

The 3 required files, ``board.yml``, ``Kconfig.[BOARD-PN]``, and
``[BOARD-PN].dts`` are required for Zephyr to properly detect that the
board exists.

`Required File Documentation <required_files.md>`__

Optional Files
==============

The remainder of the files are optional in that some boards and SoCs
don’t need them, while others do. If a file is not need by the board and
would be empty, you can omit the file.

`Optional File Documentation <optional_files.md>`__
