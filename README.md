# Photos

The Arche phone's gallery, written in [Arche](https://github.com/arche-family/arche-lang): a WebAssembly component of the Arche phone's world, which the phone has from its first start. That world is Nimadir's mini-app world and what a phone adds to it, here the phone's shared files; its WIT is in `wit/` (from arche-os's `phone/wit`), and `world` names it. Nimadir's own shell, which has none of it, does not run it.

It works like Android's gallery:

- How many there are, and the pictures in the phone's shared Pictures folder (PNG and JPEG) and the videos in its Movies folder as a grid, the newest first: a camera names its photos and videos by when they were taken (`IMG_20261110_093000.png`, `VID_20261110_100000.avi`). A video's cell is its first frame, marked ▶.
- A tap on a video plays it, the phone keeping its image up to date frame by frame (the phone world's `video` interface), with its name, Back and Delete.
- A tap on one shows it whole, with its name, Back and Delete.
- A picture the camera takes while the app is behind is there when it comes back: the folder is looked at again on each update, and only pictures new to the app are read.

It reads the shared files through the phone world's `files` interface, which the phone gives an app whose manifest needs `files`, and hands each picture, and each video's first frame (a JPEG: the camera's videos are Motion JPEG in AVI), to the shell to decode (`image-load`), drawing it by its number in an `img`. It is an app of the page: its taps come as click events.

## Build

With [arche-lang](https://github.com/arche-family/arche-lang) checked out next to this repository and built (`make -C stage0 && make -C compiler && make -C wasm`):

```sh
../arche-lang/wasm/nimadir-app.sh . photos.component.wasm
```

`manifest` is what the phone's repository packages with the component. It gives the app's name, version and title, and what it may do: read and change the shared files (`files`).

## Test

`media/Pictures` holds two small PNGs and a text file, which is not a picture; `media/Movies` a two-frame Motion JPEG video made by ffmpeg. `headless` holds the taps of a headless run, with `media/` shared as the phone shares its files (arche-wasm's `--share`), and the functions the phone world adds for videos: the video opened, its play asked for, and Back, which stops it; then the newer picture shown and deleted. `expected.txt` holds what arche-wasm's interpreter said, as Nimadir's shell does not run the phone's world. `app-check.sh` runs the app with arche-wasm, by the interpreter and by the JIT, each in a fresh copy of this directory, and compares what it says with `expected.txt`. CI runs it on every push.

```sh
../arche-lang/wasm/app-check.sh .
NIMADIR=~/nimadir ../arche-lang/wasm/app-expected.sh .   # expected.txt again, from arche-wasm
```

## License

MIT or Apache-2.0, at your option ([LICENSE](LICENSE)).
