---
name: "voice-transcription"
description: "Transcribe Telegram voice notes using local Whisper tiny.en + Opus decoder (no external API needed)"
metadata: { "openclaw": { "requires": {} } }
---

# Voice Transcription Skill

Transcribe Telegram Ogg Opus voice notes to text using a fully local pipeline (no external API calls).

## Prerequisites

The following npm packages must be installed in a working directory:

```sh
npm install @xenova/transformers @discordjs/opus
```

They will be installed already if previously run from `/tmp`.

## Usage

Run this Node.js script on the Ogg file from Telegram. The file is stored at:
```
/data/.openclaw/media/inbound/<UUID>.ogg
```

### Full Transcription Script

```js
const fs = require('fs');
const { pipeline } = require('@xenova/transformers');
const { OpusEncoder } = require('@discordjs/opus');

// Parse Ogg pages and extract Opus packets (skip header pages 0-1)
function parseOggPackets(data, startPage = 2) {
  let offset = 0;
  let pageIndex = 0;
  const allPackets = [];
  while (offset < data.length && data.readUInt32BE(offset) === 0x4f676753) {
    const numSegments = data[offset + 26];
    const segTableOffset = offset + 27;
    let totalSegSize = 0;
    for (let i = 0; i < numSegments; i++) totalSegSize += data[segTableOffset + i];
    const payloadStart = segTableOffset + numSegments;
    if (pageIndex >= startPage) {
      const payload = Buffer.from(data.slice(payloadStart, payloadStart + totalSegSize));
      let ptr = 0;
      for (let i = 0; i < numSegments; i++) {
        const sz = data[segTableOffset + i];
        if (sz < 255 || i === numSegments - 1) {
          allPackets.push(payload.slice(ptr, ptr + sz));
          ptr += sz;
        } else { ptr += sz; }
      }
    }
    offset = payloadStart + totalSegSize;
    pageIndex++;
  }
  return allPackets;
}

async function transcribe(inputPath) {
  // Check if packages exist
  let whDir = __dirname;
  // Find node_modules
  while (whDir && !fs.existsSync(whDir + '/node_modules/@xenova/transformers')) {
    const next = path.dirname(whDir);
    if (next === whDir) break;
    whDir = next;
  }

  const buf = fs.readFileSync(inputPath);
  
  // 1. Parse Ogg → Opus packets
  const packets = parseOggPackets(buf);
  
  // 2. Decode Opus → PCM int16 at 48kHz
  const decoder = new OpusEncoder(48000, 1);
  let allPcm = Buffer.alloc(0);
  for (const pkt of packets) {
    try {
      allPcm = Buffer.concat([allPcm, decoder.decode(pkt)]);
    } catch(e) { /* skip corrupt packets */ }
  }
  
  // 3. Downsample 48kHz → 16kHz (take every 3rd sample)
  const samples16 = new Int16Array(allPcm.buffer, allPcm.byteOffset, allPcm.length / 2);
  const samples8k = new Int16Array(Math.ceil(samples16.length / 3));
  for (let i = 0; i < samples8k.length; i++) {
    if (i * 3 < samples16.length) samples8k[i] = samples16[i * 3];
  }
  
  // 4. Convert Int16 → Float32 (normalize)
  const float32 = new Float32Array(samples8k.length);
  for (let i = 0; i < samples8k.length; i++) {
    float32[i] = samples8k[i] / 32768.0;
  }
  
  // 5. Run Whisper tiny.en
  const pipe = await pipeline('automatic-speech-recognition', 'Xenova/whisper-tiny.en');
  const result = await pipe(float32);
  
  return result.text;
}

// Export
module.exports = { transcribe };
```

Or more concisely for ad-hoc use via `node -e`:

```sh
node -e "
$(cat script.js)
transcribe('/data/.openclaw/media/inbound/FILE.ogg').then(t => console.log('TRANSCRIPTION:', t));
"
```

## Performance

- **First run** (model download): ~30-40 seconds total (downloads ~75MB ONNX model)
- **Subsequent runs** (model cached): ~3-5 seconds for a ~5 second clip
- Model: `Xenova/whisper-tiny.en` (tiny English-only, fastest option)
- For even faster: use `Xenova/whisper-tiny-int8` (quantized, slightly lower accuracy)

## Finding the input file

Telegram voice notes are stored at:
```
/data/.openclaw/media/inbound/
```
Identify the most recent `.ogg` file:
```sh
ls -lt /data/.openclaw/media/inbound/*.ogg | head -3
```
