# OP-Z Project file format

## Disclaimer

Thoses researchs are for educational purposes.
I like to learn and explore possibilities of devices that I purchase, that’s all.

Please note that I will not explain operations described in OP-Z user guides.
If you don’t know how to do something, please read the fun manual !
You will learn a lot about the device you just have bought.

I would not be held responsible for any misuse of patented / copyrighted content.
I will not be responsible if you harm or broke your device / computer / cat or dog.

## Forewords

Project files are available in "projects" folder when the device is connected in disk mode.
They are numbered from 01 to 10 matching the project mapping order.

Project snapshot is a copy of the project, matching the project's file name with a `f` suffix.
Bounced project files are copies of the source project.

This document apply to the following firmware versions:
- 1.1.17

### Endianness
The OP-Z run on a Blackfin ADSP-BF703 processor, this processor use **little-endian** memory structure.

This file seem to be memory mapped, so all data are stored LSB first.

Example: `0xFF3F` should be read `0x3FFF`

### Offsets

Unless stated otherwise, offsets are relative to the start of the described part.

- For the whole file, it's from the file's start.
- For a chunk or another structure, it's from the start of that structure.

## Project file format

| Offset | Size | Type | Name | Description |
|:------:|:-----:|------|------|-------------|
| 0 | 4 | UINT32 | Firmware version | seem to be related to the firmware version number from under which the project was created. See below |
| 4 | 512 | Pattern chain[16] | Saved pattern chains  | Array of saved pattern chains |
| 516 | 1 | UINT8 | Drum level | Mixer's drum group level |
| 517 | 1 | UINT8 | Synth level | Mixer's synth group level |
| 518 | 1 | UINT8 | Punch level | Mixer's punch level |
| 519 | 1 | UINT8 | Master level | Mixer's master level |
| 520 | 1 | UINT8 | Project tempo | Project tempo from 40 to 200 |
| 521 | 44 | ?? | unknown | unknown, values are often 0x00 |
| 565 | 1 | UINT8 | Swing | Swing level from 0 to 255 |
| 566 | 1 | UINT8 | Metronome level | Metronome sound level |
| 567 | 1 | UINT8 | Metronome sound | Metronome sound selection from 0x00 to 0xFF. Values might be mapped with some linear interpolated indexes |
| 568 | 4 | UINT32 (?) | unknown | unknown, mostly 0x000000FF |
| 572 | 342272 | Pattern[16] | Patterns | Pattern chunks |

### Known Firmware version values

The first 4 bytes of the file seem to be related to the firmware version.
Here is a list of known values and related firmware version.

| Firmware version | Value |
|:----------------:|:-----:|
| 1.1.17 | 0x00000049 |

## Pattern chunk

| Offset | Bytes | Type | Name | Description |
|:------:|:-----:|------|------|-------------|
| 0 | 192 | Track[16] | Tracks | Track parameters chunks |
| 192 | 7040 | Notes[220] | Notes | Notes chunks |
| 7232 | 13824 | Step[256] | Steps | Steps chunks |
| 21056 | 288 |  UINT8[288] | Parameters values | parameters values, there is 18 parameters for each 16 tracks |
| 21344 | 40 | UINT8[40] | Mutes | mute config, tracks are mapped with bitmask |
| 21384 | 2 | UINT16 | SendTape | Send mapping for Tape track using bitmask|
| 21386 | 2 | UINT16 | SendMaster | Send mapping for Master track using bitmask |
| 21388 | 1 | UINT8 | Active mute group | Active mute group |
| 21389 | 3 | UINT8 | Unused | Unused |

Size: 21392 bytes

### Track parameters

**Notes**
- The order is to be confirmed.

| Offset | Parameter |
|:------:|-----------|
| 00 | Parameter 1 |
| 01 | Parameter 2 |
| 02 | Envelope attack |
| 03 | Envelope decay |
| 04 | Envelope sustain |
| 05 | Envelope release |
| 06 | Effect 1 send |
| 07 | Effect 2 send |
| 08 | Filter |
| 09 | Resonance |
| 10 | Pan |
| 11 | Volume |
| 12 | Portamento |
| 13 | LFO P1 |
| 14 | LFO P2 |
| 15 | LFO P3 |
| 16 | LFO P4 |
| 17 | Note style |

### Tape track mapping
You can select which track is affected by the tape track (send effect).

Track numbers are mapped to bit numbers from 1 to 8.
The first byte being tracks from 1 to 8 and the second byte for tracks from 9 to 16.

### Master track mapping
You can select which track is affected by the master track (send effect).

Track numbers are mapped to bit numbers from 1 to 8.
The first byte being tracks from 1 to 8 and the second byte for tracks from 9 to 16.

## Pattern chain chunk

**Notes:**
- Chain pattern less longer than 32 is padded with 0xFF bytes.
- Empty chain pattern chunk is filled with 16 0xFF bytes, the rest is garbage to be ignored.

| Offset | Bytes | Type | Name | Description |
|:------:|:-----:|------|------|-------------|
| 0 | 32 | UINT8 | Patterns | Array of 32 bytes for the patterns id (from 0 to 15).  |

Size: 32 bytes

## Track chunk

| Offset | Bytes | Type | Name | Description |
|:------:|:-----:|------|------|-------------|
| 0 | 4 | UINT32 | Plug ID | the active engine plug id |
| 4 | 1 | UINT8  | Step count | Step count |
| 5 | 1 | UINT8  | ?? | ?? seem to be 0x05, maybe some kind of timing |
| 6 | 1 | UINT8  | Step length | Step length |
| 7 | 1 | UINT8  | Quantize | Quantize level |
| 8 | 1 | UINT8  | Note style | the track notes styles |
| 9 | 1 | UINT8  | Note length | the track notes length |
| 10 | 2 | UINT8  | Unused | Unused |

Size: 12 bytes

## Note chunk

**Notes:**
- Micro step adjustments are the same for all notes for the same step

| Offset | Bytes | Type | Name | Description |
|:------:|:-----:|------|------|-------------|
| 0 | 4 | INT32 | Note duration | How long the note is held |
| 4 | 1 | UINT8 | Note | Note value, it's 0 based for C1 and increase by one for each semitone. Divide the note by 12 give the octave number |
| 5 | 1 | UINT8 | Velocity | Velocity value. Default value is 100 |
| 6 | 1 | INT8 | Micro adjustment | Micro adjustment tick from -23 to 24 |
| 7 | 1 | UINT8 | Age | Seem to be always 0x00 |

Size: 8 bytes

### Notes ordering

**Notes:**
- T mean track with the track number from 1 to 16 : T1 for track 1, T2 for track 2.
- S mean step with the step number from 1 to 16: S1 for step 1, S2 for step 2.
- Each drum track (from track 1 to 4) can have two notes played at the same time.
- Each Synth track can have four notes played at the same time.


## Step chunk

| Offset | Bytes | Type | Name | Description |
|:------:|:-----:|------|------|-------------|
| 0 | 2 | UINT16 | Step components bitmask | Bit mask on 2 bytes to show which step component are enabled. See below for bitmask values |
| 2 | 16 | UINT8[16] |  Step components parameters | Array of selected parameter value for each step components |
| 18 | 18 | UINT8[18] |  Step parameter locked values | Array of current lock value for each tracks parameters |
| 36 | 18 | UINT8[18] |  Step parameter mask | Array of actually enabled parameter lock |

Size: 54 bytes

### Step components bitmask

The step component bitmask indicate which step components are enabled.

The first byte bits are numbered from 8 to 1, the second byte bits from 16 to 09.

| 08 | 07 | 06 | 05 | 04 | 03 | 02 | 01 | 16 | 15 | 14 | 13 | 12 | 11 | 10 | 09 |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| Glide | Random | Ramp Down | Ramp Up | Velocity | Jump | Parameter Spark | Pulse | Unused | Unused | Component Spark | Trigger Spark | Tonality | Pulse Hold | Sweep | Multiply |

### Step components values
When a step component value can be used in more than one way, for example for spark components, the MSB of the value is code like that:

| MSB | Description |
|:---:|-------------|
| 0 | trig on the LSB value |
| 1 | trig on the first pass |
| 2 | trig on every other pass but the LSB value |

For example:
- `0x04` for the trigger spark step component : trig on the 4th pass
- `0x14` for the trigger spark step component : trig on the 1st pass every 4 pass
- `0x24` for the trigger spark step component : trig on all pass but the 4th every 4 pass
