# Photos

The Arche phone's gallery, written in [Arche](https://github.com/arche-family/arche-lang): a WebAssembly component of the Arche phone's world, which the phone has from its first start. That world is Nimadir's mini-app world and what a phone adds to it, here the phone's shared files; its WIT is in `wit/` (from arche-os's `phone/wit`), and `world` names it. Nimadir's own shell, which has none of it, does not run it.

It works like a phone's gallery:

- How many there are, and the pictures in the phone's shared Pictures folder (PNG and JPEG) and in its folders (the screenshots in `Pictures/Screenshots`, named `Screenshot_20261118_101742.png`, as Android keeps them) and the videos in its Movies folder (Motion JPEG in AVI, MP4, QuickTime and Matroska), the newest first, under the day each was taken: Today, Yesterday, a weekday, or the date. A camera names its photos and videos by when they were taken (`IMG_20261110_093000.jpg`, `VID_20261110_100000.mp4`). They are squares of a grid four across, edge to edge; a video's has its length in its corner.
- A tap on a picture shows it whole on black to the screen's edges, when it was taken at the top by Back's arrow, and Delete; a tap on a video plays it there, with its sound; a video the phone cannot play says so. Each picture has its name as a screen reader says it (`aria-label`: "Photo …", "Video …"). The phone's Back goes back to the grid.
- A swipe sideways, a picture shown whole, goes to the next one (to the left) or the one before, as a gallery's does.
- A picture the camera takes while the app is behind is there when it comes back: the folder is looked at again on each update, and only pictures new to the app are read.

It speaks the phone's language, English, Uzbek or Russian, as the phone's locale says (`host-api`'s `locale`): its words, the counts ("2 ta surat · 1 ta video", "2 фото · 1 видео") and the days ("Bugun", "10-noyabr"; "Сегодня", "10 ноября"), and changes with it at once.

It reads the shared files through the phone world's `files` interface, which the phone gives an app whose manifest needs `files`, and asks the phone for each picture fitted to a square (`files.image`), which decodes a camera's 12 megapixels and keeps the thumbnail, so that the gallery opens at once the next time; a picture shown whole is fitted to the screen. An AVI's first frame is decoded by the shell (`image-load`); an MP4's picture is the poster the phone made of it, as Android's media scanner does (`files.image`), and its length is read from its movie header (`files.read-part`). The phone plays both (`video.play`), an MP4 by its codec, FFmpeg's decoders as WebAssembly. It is an app of the page: its taps come as click events.

## Build

With [arche-lang](https://github.com/arche-family/arche-lang) checked out next to this repository and built (`make -C stage0 && make -C compiler && make -C wasm`):

```sh
../arche-lang/wasm/nimadir-app.sh . photos.component.wasm
```

`manifest` is what the phone's repository packages with the component. It gives the app's name, version and title, and what it may do: read and change the shared files (`files`).

## Test

`media/Pictures` holds two small PNGs and a text file, which is not a picture, and in `Screenshots` a screenshot of the day before; `media/Movies` a two-frame Motion JPEG video made by ffmpeg. `headless` holds the taps of a headless run, with `media/` shared as the phone shares its files (arche-wasm's `--share`), and the functions the phone world adds for videos: the video opened, its play asked for, and Back, which stops it; then the newer picture shown and deleted. `expected.txt` holds what arche-wasm's interpreter said, as Nimadir's shell does not run the phone's world. `app-check.sh` runs the app with arche-wasm, by the interpreter and by the JIT, each in a fresh copy of this directory, and compares what it says with `expected.txt`. CI runs it when started by hand (Actions' Run workflow).

```sh
../arche-lang/wasm/app-check.sh .
NIMADIR=~/nimadir ../arche-lang/wasm/app-expected.sh .   # expected.txt again, from arche-wasm
```

## License

MIT or Apache-2.0, at your option ([LICENSE](LICENSE)).
