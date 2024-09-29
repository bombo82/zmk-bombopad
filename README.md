#

## Build the firmware

Run from `zmk/app` directory:
```
$ west build -p -b nice_nano_v2 -- -DSHIELD="nice_view_adapter nice_view bombopad" -DZMK_EXTRA_MODULES="/home/gianni/workspace/macropad/zmk-bombopad"
```
