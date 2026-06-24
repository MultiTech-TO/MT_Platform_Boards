# MultiTech Platform Boards
A repo for platform boards created by MultiTech.
## Boards
Each board with a hardware difference must have its own board files in the following format

```
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
```

In the future we may change the directory structure to ``` boards/MultiTech/<family>/[BOARD-PN]  ``` if further organization is needed. <br> View the [Template Board Read Me](boards/Multitech/Template/readme.md) for more details about the files.