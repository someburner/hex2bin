# hex2bin 2.5

Git for `hex2bin` on [sorceforge](https://sourceforge.net/projects/hex2bin/).

I did not write this, but I use it a lot and it has no official vcs it appears.
The following is taken from [this](https://hex2bin.sourceforge.net/) webpage.

## Intro

This tool is used for converting hexadecimal files (either Motorola or Intel format) into a binary file.
It's a command line tool with basic capabilities.

* It can handle the extended Intel hex format in segmented and linear address modes.
* Records need not be sorted and there can be gaps between records.
* Holes/unused bytes can be specified as any other value than `FF`.
* A checksum can be inserted into the binary file.

## Building

```sh
git clone git@github.com:someburner/hex2bin.git
cd hex2bin
make
# optionally, you may install system-wide
sudo make install
```

### Usage

Options are case sensitive and options with parameters need a space between
option and parameter. I.e. `-s 0000` instead of `-s0000`.

A successful execution exits with the error code = `0`, If any error occurs,
the program exits immediately with the error code = `1`.

All values are in hexadecimal, no `0x` needed: ex. not `0x0100` but `0100`.


### Batch/script mode

Normally, if the specified hex file doesn't exist, `hex2bin`/`mot2bin` ask
repeatedly for a valid filename. A batch/script mode option is provided for
exiting with an error instead of asking for a file.

```sh
hex2bin -b xxxx.hex
```

If the file `xxxx.hex` doesn't exist, the program exits immediately with the
error code = `1`.

#### Checksum of source file

By default, it ignores checksum errors, so that someone can change by hand some
bytes allowing quick and dirty changes.

If you want checksum error reporting, specify the option `-c`.

```sh
hex2bin -c example.hex
```

If there is a checksum error somewhere, the program will continue the conversion
anyway. For convenience, `hex2bin`/`mot2bin` displays the expected checksum at
each faulty records.

<br>

#### Extension for output file

By default, the extension will be .bin. Another value can be specified.

```sh
hex2bin -e xxx example.hex
```

A file `example.xxx` will be generated.

<br>

#### Padding byte

By default, unused locations will be filled with `FF`. Another value can be
specified.

```sh
hex2bin -p AA example.hex
```

<br>

#### Starting Address and Length

If the lowest address isn't `0000`, ex: `0100`: (the first record begins with
`:nn010000xxx`), there will be problems when using the binary file to program a
EPROM since the first byte is supposed to be at `0100` is stored in the binary
file at `0000`. You can specify the binary file's starting address using `-s`:

```sh
hex2bin -s 0000 start_at_0100.hex
```

The bytes will be stored in the binary file with a padding from `0000` to the lowest address,
minus `1` (`00FF` in this case).

Similarly, the binary file can be padded up to Length `-1` with `FF` or another byte.

Here, the space between the last byte and `07FF` will be filled with `FF`.

```sh
hex2bin -l 0800 ends_before_07FF.hex
```

EPROM, EEPROM and Flash memories contain all `FF` when erased.

When the source file name is `for-example.test.hex` the binary created will have
the name `for-example.bin`. (the ".test" part will be dropped).

`hex2bin`/`mot2bin` assume the source file doesn't contain overlapping records.
If they do, overlaps will be reported.

<br>

#### Minimum Block Size

The output file size will be a multiple of Minimum block size. It will be filled
with `FF` or the specified pattern.

Length must be a power of **2** in hexadecimal [see `-l` option].

**Attention** this option *supercedes* (invalidates) Maximal Length.

```sh
hex2bin -m [size] example.hex
```

<br>

#### Checksum or CRC inserted inside binary file

A checksum value can be inserted in the resulting binary file.

```sh
hex2bin -k [0-5] -E [0|1] -r [start] [end] -f [address] [value]
```

* `-k`: Select checksum type:
  - `0` - 8-bit checksum
  - `1` - 16-bit checksum (adds 16-bit words into a 16-bit sum, data, and result BE or LE)
  - `2` - 8-bit CRC
  - `3` - 16-bit CRC
  - `4` - 32-bit CRC
  - `5` - 16-bit checksum (adds bytes into a 16-bit sum, result BE or LE)
* `-E`: Endianness of result to store
  - `0` - little endian
  - `1` - big endian
* `-r`: Rangne to compute checksum or CRC over (default is min and max addresses)
* `-f`: Address of checksum or CRC to write
* `-d`: Displays the list of checksum types and exits (`hex2bin -d`)

<br>

#### Value inserted directly inside binary file

A value can be inserted directly (forced) in the resulting binary file.

```sh
hex2bin -k [0|1|2] -E [0|1] -F [address] [value]
```

* `-k`: Select value length type:
  - `0` - 8-bit value
  - `1` - 16-bit value
  - `2` - 8-bit value
* `-E`: Endianness
  - `0` - little endian
  - `1` - big endian
* `-F`: Address and value checksum to write

<br>

#### Support for byte-swapped hex/S19 files

Some compilers such as Microchip's MPLAB IDE can generate byte swapped hex files.

```sh
hex2bin -w test-byte-swap.hex
```

* `-w`: Wordwise swap - For each pair of bytes, exchange the low and high part.

<br>

#### Support for word sized hex files (hex2bin only)

Hex with record type, where data is represented in Word (2 Byte)

e.g Texas Instruments: TMS320F2835, TMS320F28065.

```sh
hex2bin -a example-ti.hex
```

* `-a`: Address Alignment Word.

<br>

#### Filter for records within range

Records outside that range are discarded

```sh
hex2bin -t 0110 -T 0256 example.hex
```

* `-t`: Floor address
* `-T`: Ceiling address

<br>

## Status

hex2bin and mot2bin are in production status. It is working well for many small
applications.

While I'm now working on other projects, hex2bin and mot2bin are still open for
patches, feature request etc. Submit them
[here](https://sourceforge.net/projects/hex2bin/support?source=navbar).

## Similar tools

`SRecord` has many more features and support many other formats.

## License

hex2bin and mot2bin are released with a BSD license.
