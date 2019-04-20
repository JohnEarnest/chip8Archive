Chip8 Community Archive
=======================
Since 1977 the [Chip8](https://en.wikipedia.org/wiki/CHIP-8) platform has provided an outlet for programming creativity. The [Octo](http://octo-ide.com) toolchain makes it easier than ever to get started, and has lead to the creation of a wide variety of fun and interesting software. This repository is meant to serve as a stable archive of Chip8 programs along with their metadata, with attribution, supporting material, and clear licensing.

Check out the [Online Gallery](https://johnearnest.github.io/chip8Archive/) of submitted programs!

Schema
------
The `programs.json` file is the central registry of program metadata. Each entry may contain the following fields:

- `title` (required): The full text title of the program.
- `authors` (required): A list of one or more keys for the entries in `authors.json`.
- `images` (required): A list of zero or more filenames inside the directory in `src` corresponding to this entry which can be used to visually represent the program in a gallery.
- `desc` (optional): A short textual description of the program.
- `event` (optional): A short string identifying any game jam or gathering this program was created during.
- `release` (optional): An ISO 8601 date (`YYYY-MM-DD`) indicating when this program was released to the public.
- `platform` (optional): The Chip8 platform this program runs on (`chip8`, `schip`, or `xochip`). If the program works on multiple platforms, go with the least powerful it works on properly.
- `options` (optional, natch): Configuration for Octo's quirks flags and palette. If not provided, Octo's defaults may be assumed.

The `authors.json` file contains a set of keys referenced in `programs.json`, and consist of entries with the following fields about the creators of programs:

- `email` (optional): A contact email address.
- `url` (optional): A personal website.

The `src` directory contains directories corresponding to keys in `programs.json`. Each directory can have source code or supporting material for programs, such as screenshots or documentation. Projects are encouraged to provide readmes for each program directory. Octo source files should use the standard `.8o` extension.

The `roms` directory contains compiled Chip8 binaries, ready for use in any emulator. Filenames should match keys in `programs.json`, plus a `.ch8` extension.

Notes for Conservators
----------------------
If you are recording animated GIFs of a game with Octo, be sure to run the output through an optimizer before checking it in- Octo tends to produce very large files. One way to optimize a GIF is to use [gifsicle](https://www.lcdf.org/gifsicle/) like so:

	gifsicle src/mygame/cool.gif --optimize=3 --colors=2 -o src/mygame/cool.gif

Old shared Octo programs used GitHub gists to store their source and metadata. If you have a URL which looks like

	http://johnearnest.github.io/Octo/index.html?gist=bdd2840b9fe5fad7bd5995423ea57a23

You can use the gist ID to view the `options` dictionary and original submission date:

	https://gist.github.com/bdd2840b9fe5fad7bd5995423ea57a23

Octo's newer sharing system uses a custom storage API, and URLs look like this:

	http://johnearnest.github.io/Octo/index.html?key=AcfYNFAu

The `key` can be used to retrieve a JSON file with `options` and other metadata like this:

	https://vectorland.nfshost.com/storage/octo/AcfYNFAu

Licensing
---------
Chip8 software, like most console homebrew, tends to exist in a murky gray area of copyright. In the interest of ensuring that others will be able to freely build upon and remix the works in this archive, everything in this repository is placed under the [Creative Commons 0](https://creativecommons.org/share-your-work/public-domain/cc0/) "No Rights Reserved" license.

Contributing
------------
Anyone who has developed Chip8 software is encouraged to submit a pull request containing new entries in the author and program registries, a new Chip8 rom, and other materials as desired. Submitting a pull request indicates that you're willing to adhere to the licensing agreement as indicated above. _Please do not submit programs which are not yours to license!_ If you are considering contributing but are unhappy with CC0 licensing, feel free to contact me (John Earnest) and see if we can work something out.

Programs which were not created with Octo are perfectly welcome in this archive. For the sake of consistency in future emulation we do ask that you test your programs using Octo and determine the necessary adjustments to its "quirks flags" when preparing your metadata entries. The Octo project captures extensive research and testing with historical software and can accurately reproduce most of the variation between Chip8 interpreters in the wild today, with the notable exception (for now) of support for RCA 1802 machinecode.
