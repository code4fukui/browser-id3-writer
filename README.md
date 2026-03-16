# browser-id3-writer

> 日本語のREADMEはこちらです: [README.ja.md](README.ja.md)

JavaScript library for writing [ID3 (v2.3)](https://egoroof.github.io/browser-id3-writer/spec/) tag to MP3 files in browsers and Node.js.
It can't read the tag so use another lib to do it.

**Note**: the library removes existing ID3 tag (v2.2, v2.3 and v2.4).

## Demo
Here is an online demonstration: [egoroof.github.io/browser-id3-writer/](https://egoroof.github.io/browser-id3-writer/)

## Features
- Customize tag frames
- Add cover art
- Add lyrics
- Add comments
- Add private frames

## Installation

Take latest version [here](https://unpkg.com/browser-id3-writer) or with npm:

```
npm install browser-id3-writer --save
```

### JS modules

The library is only deployed in [native JS modules](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules), so in browsers you have to use `script` with type `module`:

```html
<script type="module">
  import { ID3Writer } from 'https://your-host/browser-id3-writer.mjs';
  // your code here..
</script>
```

Or bundle the library to your code.

In Nodejs it imports easily:

```js
import { ID3Writer } from 'browser-id3-writer';
```

## Usage

### Browser

#### Get ArrayBuffer of song

In browsers you should first get
[ArrayBuffer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)
of the song you would like to add ID3 tag.

##### FileReader

For example you can create file input and use
[FileReader](https://developer.mozilla.org/en-US/docs/Web/API/FileReader):

```html
<input type="file" id="file" accept="audio/mpeg" />
<script type="module">
  import { ID3Writer } from 'https://your-host/browser-id3-writer.mjs';

  document.getElementById('file').addEventListener('change', function () {
    if (this.files.length === 0) {
      return;
    }
    const reader = new FileReader();
    reader.onload = function () {
      const arrayBuffer = reader.result;
      // go next
    };
    reader.onerror = function () {
      // handle error
      console.error('Reader error', reader.error);
    };
    reader.readAsArrayBuffer(this.files[0]);
  });
</script>
```

##### Fetch

To get arrayBuffer from a remote server you can use
[Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):

```js
const request = await fetch(urlToSongFile);
if (!request.ok) {
  // handle error
  console.error(`Unable to fetch ${urlToSongFile}`);
}
const arrayBuffer = await request.arrayBuffer();
// go next
```

#### Add a tag

Create a new `ID3Writer` instance with arrayBuffer of your song, set frames and add a tag:

```js
// arrayBuffer of song or empty arrayBuffer if you just want only id3 tag without song
const writer = new ID3Writer(arrayBuffer);
writer
  .setFrame('TIT2', 'Home')
  .setFrame('TPE1', ['Eminem', '50 Cent'])
  .setFrame('TALB', 'Friday Night Lights')
  .setFrame('TYER', 2004)
  .setFrame('TRCK', '6/8')
  .setFrame('TCON', ['Soundtrack'])
  .setFrame('TBPM', 128)
  .setFrame('WPAY', 'https://google.com')
  .setFrame('TKEY', 'Fbm')
  .setFrame('APIC', {
    type: 3,
    data: coverArrayBuffer,
    description: 'Super picture',
  });
writer.addTag();
```

#### Save file

Now you can save it to file as you want:

```js
const taggedSongBuffer = writer.arrayBuffer;
const blob = writer.getBlob();
const url = writer.getURL();
```

For example you can save file using [FileSaver.js](https://github.com/eligrey/FileSaver.js/):

```js
saveAs(blob, 'song with tags.mp3');
```

If you are writing chromium extension you can save file using
[Downloads API](https://developer.chrome.com/docs/extensions/reference/api/downloads):

```js
chrome.downloads.download({
  url: url,
  filename: 'song with tags.mp3',
});
```

#### Memory control

When you generate URLs via `writer.getURL()` you should know
that whole file is kept in memory until you close the page or move to another one.
So if you generate lots of URLs in a single page you should manually free memory
after you finish downloading file:

```js
URL.revokeObjectURL(url); // if you know url or
writer.revokeURL(); // if you have access to writer
```

### Node.js

Simple example with blocking IO:

```js
import { ID3Writer } from 'browser-id3-writer';
import { readFileSync, writeFileSync } from 'fs';

const songBuffer = readFileSync('path_to_song.mp3');
const coverBuffer = readFileSync('path_to_cover.jpg');

const writer = new ID3Writer(songBuffer);
writer
  .setFrame('TIT2', 'Home')
  .setFrame('TPE1', ['Eminem', '50 Cent'])
  .setFrame('TALB', 'Friday Night Lights')
  .setFrame('TYER', 2004)
  .setFrame('APIC', {
    type: 3,
    data: coverBuffer,
    description: 'Super picture',
  });
writer.addTag();

const taggedSongBuffer = Buffer.from(writer.arrayBuffer);
writeFileSync('song_with_tags.mp3', taggedSongBuffer);
```

You can also create only ID3 tag without song and use it as you want:

```js
const writer = new ID3Writer(Buffer.alloc(0));
writer.padding = 0; // default 4096
writer.setFrame('TIT2', 'Home');
writer.addTag();
const id3Buffer = Buffer.from(writer.arrayBuffer);
```

## Supported frames

**array of strings:**

- TPE1 (song artists)
- TCOM (song composers)
- TCON (song genres)

**string**

- TLAN (language)
- TIT1 (content group description)
- TIT2 (song title)
- TIT3 (song subtitle)
- TALB (album title)
- TPE2 (album artist)
- TPE3 (conductor/performer refinement)
- TPE4 (interpreted, remixed, or otherwise modified by)
- TRCK (song number in album): '5' or '5/10'
- TPOS (album disc number): '1' or '1/3'
- TPUB (label name)
- TKEY (initial key)
- TMED (media type)
- TDAT (album release date expressed as 'DDMM')
- TSRC (isrc - international standard recording code)
- TCOP (copyright message)
- TCMP (iTunes compilation flag)
- TEXT (lyricist / text writer)
- WCOM (commercial information)
- WCOP (copyright/Legal information)
- WOAF (official audio file webpage)
- WOAR (official artist/performer webpage)
- WOAS (official audio source webpage)
- WORS (official internet radio station homepage)
- WPAY (payment)
- WPUB (publishers official webpage)

**integer**

- TLEN (song duration in milliseconds)
- TYER (album release year)
- TBPM (beats

## License
MIT License — see [LICENSE](LICENSE).