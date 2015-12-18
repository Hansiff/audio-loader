# audio-loader [![npm](https://img.shields.io/npm/v/audio-loader.svg)](https://www.npmjs.com/package/audio-loader)

A powerful but easy audio buffer loader for browser:

```js
var ac = new AudioContext()
var load = require('audio-loader')(ac)

load({ snare: 'samples/snare.wav', kick: 'samples/kick.wav' }).then(loaded) {
  console.log(loaded) // => { snare: <AudioBuffer>, kick: <AudioBuffer> }
})
```

Used by: [smplr](https://github.com/danigb/smplr)


## Features

- Load single audio files or collection of them (either using arrays or data objects)
- Load base64 encoded audio strings
- Compatible with midi.js pre-rendered soundfonts packages
- Can load from a server or from a github repository

## Install

Via npm: `npm i --save audio-loader` or grab the [browser ready file](https://raw.githubusercontent.com/danigb/smplr/master/packages/audio-loader/dist/audio-loader.min.js) (4kb) which exports `loader` as window globals.

## Usage

`audio-loader` is a flexible function to load audio samples. You can create a loader with an AudioContext instance and an (optional) options hash map:

```js
var loader = require('audio-loader')
var ac = new AudioContext()
var load = loader(ac, { /* options, not required */ })
```

The options is an (optional) hash map with the following:

- sources <HashMap>: a hash map of audio sources (see below)
- fetch <Function>: a function to retrieve data from urls

The returned `load` function receives only one parameter (the samples to load) and __returns always a Promise__.

#### Load audio files

You can load individual or collection of files:

```js
load('http://path/to/file.mp3').then(function (buffer) {
  // buffer is an AudioBuffer
  play(buffer)
})

load(['samples/snare.mp3', 'samples/kick.mp3']).then(function (buffers) {
  // buffers is an array of AudioBuffers
  play(buffers[0])
})

load({ snare: 'samples/snare.mp3', kick: 'samples/kick.mp3' }).then(function (buffers) {
  // buffers is a hash of names to AudioBuffers
  play(buffers['snare'])
})
```

#### Using data objets to load audio files

You can use any data object and `audio-loader` will load the values that references audio files while keep the rest of the data intact:

```js
var inst = { name: 'piano', gain: 0.2, audio: 'samples/piano.mp3' }
load(inst).then(function (piano) {
  console.log(piano.name) // => 'piano' (it's not an audio file)
  console.log(piano.gain) // => 0.2 (it's not an audio file)
  console.log(piano.audio) // => <AudioBuffer> (it loaded the file)
})
```

#### Add audio sources

You can define audio sources with the sources options:

```js
var load = loader(ac, { sources: {
  '@drums': 'http://server1.com/audio/drums',
  '@percussion': 'http://server2.com/samples/percussion'
}})
```

and then:

```js
load({ kick: '@drums/kick.mp3', snare: '@drums/snare.mp3', clave: '@percussion/clave.mp3'}
```

The source definition can be a function:

```js
var load = loader(ac, { sources: {
  '@hi-res': function (name, load) {
    return load('http://server.com/audio/' + name + '.wav')
  },
  '@lo-res': function (name, load) {
    return load('http://server.com/audio/' + name + '.mp3')
  }
}})

load('@hi-res/drum-loop').then(...)
load('@lo-res/drum-loop').then(...)
```

#### Load soundfont files

You can load [midi.js](https://github.com/mudcube/MIDI.js) soundfont files, and works out of the box with Benjamin Gleitzman's package of
[pre-rendered sound fonts](https://github.com/gleitz/midi-js-soundfonts). No server setup, just prepend `@soundfont` before the instrument name:

```js
load('@soundfont/acoustic_grand_piano').then(function(buffers) {
  play(buffers['C2'])
})
```

#### Other instruments

Can load [drum-machines](https://github.com/danigb/smplr/tree/master/packages/drum-machines) by prepending `@drum-machines` before the instrument name:

```js
load('@drum-machines/CR-78').then(function (buffers) {
  play(buffers['snare'])
})
```

## Run tests and examples

To run the test, clone this repo and:

```bash
npm i
npm test
```

To run the example:

```bash
npm i -g beefy
beefy example/example.js
```

## License

MIT License
