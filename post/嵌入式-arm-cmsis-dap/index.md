# CMSIS-DAP


## openocd

```
-f 指定配置文件（一般一个接口配置一个target配置）
-c 命令
-c cmsis-dap-serial 指定cmsis-dap烧写器序列号
```

[Programming nRF52 with OpenOCD](https://devzone.nordicsemi.com/f/nordic-q-a/29029/programming-nrf52-with-openocd)

## [pyocd](https://github.com/mbedmicro/pyOCD)

```
PyOCD debug tools for Arm Cortex devices

optional arguments:
  -h, --help      show this help message and exit
  -V, --version   show program's version number and exit
  --help-options  Display available session options.

subcommands:

    commander     Interactive command console.
    cmd           Alias for 'commander'.
    erase         Erase entire device flash or specified sectors.
    flash         Program an image to device flash.
    gdbserver     Run the gdb remote server(s).
    gdb           Alias for 'gdbserver'.
    json          Output information as JSON.
    list          List information about probes, targets, or boards.
    pack          Manage CMSIS-Packs for target support.
```
