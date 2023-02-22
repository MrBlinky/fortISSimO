# fortISSimO

[![CC0 licensed (public domain)](https://licensebuttons.net/p/zero/1.0/80x15.png)](http://creativecommons.org/publicdomain/zero/1.0/)

A speed- and size-focused Game Boy music driver.
A table [comparing pros and cons of many GB music drivers](https://github.com/ISSOtm/fortISSimO/wiki/Drivers-comparison) is available on the wiki.

For documentation and example of usage, see [fortISSimO-demo](https://github.com/ISSOtm/fortISSimO-demo).

## Comparison with hUGEDriver

This driver can be used as a drop-in replacement of [SuperDisk's GB sound driver][hUGEDriver].
It has, however, a few differences:

- This driver should perform a bit faster than hUGEDriver, and be somewhat smaller.
- Vibrato is **not** supported at all in subpatterns!
  Please use `1xx` and `2xx` instead.
- The instrument field is ignored on a row that contain a toneporta effect (`3`).
  hUGEDriver reloads the instrument instead.
- Since the driver's internals are different, routines are **not** compatible between hUGEDriver and fortISSimO.

## Quick start

fortISSimO should be usable by simply replacing `hUGEDriver.asm` with `fortISSimO.asm`.
I am for that to be the case, anyway; please feel free to report any incompatibilities [as issues](https://github.com/ISSOtm/fortISSimO/issues/new).

### RGBDS

0. **Required**: RGBDS 0.5.0 or later.
1. [Include](https://rgbds.gbdev.io/docs/v0.6.0/rgbasm.5/#Including_other_source_files) `fortISSimO.asm` in your project.
   See below about customising the build.
2. Export your songs from hUGETracker using the "Export to RGBDS .asm..." option.
3. Both `fortISSimO.asm` and all files exported by hUGETracker contain `INCLUDE "include/hUGE.inc"` (referring to [this file](https://github.com/ISSOtm/fortISSimO/blob/master/include/hUGE.inc)).
   You may need to pass [a `-I` flag](https://rgbds.gbdev.io/docs/v0.6.0/rgbasm.1#I) to RGBASM for it to work (e.g. [`-I src/fortISSimO`](https://github.com/ISSOtm/fortISSimO-demo/blob/d10a2107ac46cef3933f6ec21d9cfef91b232743/Makefile#L29)).
4. At least once before the first call to `hUGE_TickSound` (so, for example, [during boot-up](https://github.com/ISSOtm/fortISSimO-demo/blob/d10a2107ac46cef3933f6ec21d9cfef91b232743/src/main.asm#L70-L74)), you must set `hUGE_MutedChannels` (usually to 0 to enable all channels).

### GBDK

<details><summary>rgb2sdas currently does not support all relocation types used by fortISSimO, though the conversion is, strictly speaking, possible. Click to see the instructions nonetheless.</summary>

0. **Required**: RGBDS 0.5.0 or later, `rgb2sdas` from hUGEDriver (a pre-built Windows binary lies [in `gbdk_example/`](https://github.com/SuperDisk/hUGEDriver/tree/master/gbdk_example), and its source code in [`tools/`](https://github.com/SuperDisk/hUGEDriver/tree/master/tools)).
1. Build `fortISSimO.asm`:
   ```bash
   rgbasm fortISSimO.asm -o fortISSimO.obj
   ```
2. Convert it to a SDCC object file:
   ```bash
   rgb2sdas fortISSimO.obj
   ```
3. Use `#include <fortISSimO.h>` (you may need to adapt that path and/or use `-I`) as desired.
4. Link `fortISSimO.obj.o` as part of your build.

</details>

## Customising fortISSimO

You can customise the way fortISSimO integrates with your project by defining some symbols when assembling `fortISSimO.asm`.
(You can do so either by `INCLUDE`ing `fortISSimO.asm` from another file, and/or by using [`-D`](https://rgbds.gbdev.io/docs/v0.6.1/rgbasm.1/#D) options when running `rgbasm`.)

- **`FORTISSIMO_ROM`**: Attributes for fortISSimO's ROM [section](https://rgbds.gbdev.io/docs/v0.6.1/rgbasm.5/#SECTIONS). Examples: `ROM0`, `ROMX, BANK[42]`. Default: `ROM0`.
  
  In particular, if this is empty, no `SECTION` directive will be emitted; this is useful when using `INCLUDE "fortISSimO.asm"` inside of a section, and you want fortISSimO to be placed in that same section.
- **`FORTISSIMO_RAM`**: Attributes for fortISSimO's RAM section. Examples: `WRAM0`, `WRAMX, ALIGN[4]`.

## Using within hUGETracker

hUGEDriver can be used within hUGETracker!
To do so:

0. Optionally (but recommended), back up the files you are about to overwrite.
1. Find the `hUGEDriver/` directory in your hUGETracker installation.
2. In that directory, replace `hUGEDriver.asm` by `fortISSimO.asm` (make sure to rename it to `hUGEDriver.asm`), **and also** replace `hUGE.inc` with fortISSimO's—otherwise your song will break!!
3. Follow the instructions at the top of the new `hUGEDriver.asm`; you should get error messages from hUGETracker if you don't.

Note that this is **experimental** and **not supported by hUGETracker**.
**Any troubles** encountered while using fortISSimO in hUGETracker should be reported **here**.

hUGETracker *might* lock up and/or crash during playback, so it's advisable to save often.
Please only use hUGETracker 1.0b10: earlier versions are just not compatible, and later versions may or may not be.

## License

To follow the license of hUGETracker and hUGEDriver, fortISSimO is dedicated to the public domain.

<p xmlns:dct="http://purl.org/dc/terms/" xmlns:vcard="http://www.w3.org/2001/vcard-rdf/3.0#">
  To the extent possible under law, all copyright and related or neighboring rights to
  <span property="dct:title">fortISSimO</span> have been waived.
  This work is published from <span property="vcard:Country" datatype="dct:ISO3166" content="FR" about="https://eldred.fr">France</span>.
</p>

[hUGEDriver]: https://github.com/SuperDisk/hUGEDriver
