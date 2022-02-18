# go-rpi-ws281x (for easy cross-compilation)

[![GoDoc](https://godoc.org/github.com/mcuadros/go-rpi-ws281x?status.svg)](http://godoc.org/github.com/mcuadros/go-rpi-ws281x) 

Go bindings for [rpi_ws281x](https://github.com/jgarff/rpi_ws281x), a userspace Raspberry Pi PWM library for WS281X LEDs. Supports any Raspberry and  WS2812, SK6812RGB and [SK6812RGBW](https://www.adafruit.com/category/168?q=SK6812RGBW&) LEDs strips, this includes [Unicorn pHAT](https://shop.pimoroni.com/products/unicorn-phat) and [NeoPixels](https://www.adafruit.com/category/168).

This fork adds compatibility to the latest version of `rpi_ws281x` (by linking `libm`) and adjusts the install instructions to assume that cross-compilation is the default (as opposed to directly compiling on a Raspberry Pi).

## Installation

The recommended way to install `go-rpi-ws281x` (on Debian) is by cross-compiling the original `rpi_ws281x` library (using `scons` and the appropriate `gcc` cross-compiler) and copying the header and library files into the correct multiarch locations:

```shell
sudo apt install scons gcc-aarch64-linux-gnu
git clone https://github.com/jgarff/rpi_ws281x
cd rpi_ws281x

scons V=yes TOOLCHAIN=aarch64-linux-gnu

cp ws2811.h /usr/aarch64-linux-gnu/include/ws2811.h
cp rpihw.h /usr/aarch64-linux-gnu/include/rpihw.h
cp pwm.h /usr/aarch64-linux-gnu/include/pwm.h
cp libws2811.a /usr/aarch64-linux-gnu/lib/libws2811.a
```

## Examples

```go
// create a new canvas with the given width and height, and the config, in this
// case the configuration is for a Unicorn pHAT (8x4 pixels matrix) with the
// default configuration
c, _ := ws281x.NewCanvas(8, 4, &ws281x.DefaultConfig)


// initialize the canvas and the matrix
c.Initialize()

// since ws281x implements image.Image any function like draw.Draw from the std
// library may be used with it.
// 
// now we copy a white image into the ws281x.Canvas, this turn on all the leds
// to white
draw.Draw(c, c.Bounds(), image.NewUniform(color.White), image.ZP, draw.Over)

// render and sleep to see the leds on
c.Render()
time.Sleep(time.Second * 5)

// don't forget close the canvas, if not you leds may remain on
c.Close()
```

## License

MIT, see [LICENSE](LICENSE)