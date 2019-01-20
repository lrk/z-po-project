# OP-Z Project file format

***This is a work in progress***

Feel free to contribute via pull request / issues.


## Forewords

Project files are available in "projects" folder when the device is connected in disk mode.
They are numbered from 01 to 10, corresponding to the projects orders.

The OP-Z run on a Blackfin ADSP-BF703 processor, this processor use **little-endian** memory processing.

This file seem to be memory mapped, so all data are stored LSB first.

Example:

`0xFF3F` should be read `0x3FFF`


## File format


| Offset | Bytes | Type | Name | Description |
|:------:|:-----:|------|------|-------------|
| 0 | 4 | ? | File identifier | value meaning unknown, but it's always a byte, followed by 3 0x00 bytes. The first byte value increase with firmware version number from under which the project was created. With firmware 1.1.17 the value is 0x49 |
| 4 | 480 | UINT[] | saved chained pattern | Array of  32 bytes arrays containing the saved chained patterns |
| 484 | 32 | UINT[] | Current chain pattern | Array of 32 bytes containing the current chained pattern, each byte represent the pattern number indexed to 0 |
| 516 | 4 | ? | ? | ? |
| 520 | 1 | UINT | Project tempo | Project tempo from 40 to 200 |
| 521 | ? | ?? | ?? | ?? |
| 565 | 1 | UINT | Swing | Project's Swing level from 0 to 255 |
| 566 | 1 | UINT | Metronome level | Metronome sound level |
| 567 | 1 | UINT | Metronome sound | Metronome sound selection from 0x00 to 0xFF. Values might be mapped with some linear interpolated indexes |
| 576 | 1 | UINT | Track 1 length | Track 1 length in steps from 1 to 16 |
| 578 | 1 | UINT | Track 1 step length | Track 1 step length from 1 to 10 |
| 579 | 1 | UINT | Track 1 quantize | Track 1 quantize |
| 581 | 1 | UINT | Track 1 note duration | Track 1 note duration |
| 726 | 1 | ? | ? | unknown |
| 764 | 7040  | list | Tracks steps notes | list of per track per steps notes |
| 7804 | 13822 | list | Tracks steps metadata | list of per track per steps metadata (parameter locks, step components) |
| 21640 | 1 | UINT | Track 1 glide | Track 1 glide |
| 21645 | 1 | UINT | Track 1 play mode | Track 1 play mode |

Notes:
- Empty chain pattern block is filled with 16 0xFF bytes, the rest is garbage to be ignored.
- Chain pattern less longer than 32 is padded with 0xFF bytes.


## Pattern chunk

**Notes:**
- Offsets are relatives to note block start.

| Offset | Bytes | Type | Name | Description |
|:------:|:-----:|------|------|-------------|
| 0 | 7040 | UINT[] | Track steps notes | An array of bytes for tracks steps notes. See below for tracks steps arrangement.|
| 7041 | 13822 | UINT[] | Track steps metadata | An array of bytes for tracks steps metadatas. |

They should have been some pattern specific header or footer.

### Tracks steps ordering

**Notes:**
- T mean track with the track number from 1 to 16 : T1 for track 1, T2 for track 2.
- S mean step with the step number from 1 to 16: S1 for step 1, S2 for step 2.
- Each drum track (from track 1 to 4) can have two notes played at the same time.
- Each Synth track can have four notes (up to 8 for the chord track) played at the same time.

***(WIP)***

## Track step note chunk

**Notes:**
- Offsets are relatives to note chunk start.
- Micro step adjustments are the same for all notes for the same step

| Offset | Bytes | Type | Name | Description |
|:------:|:-----:|------|------|-------------|
| 0 | 1 | ? | ? | unknown |
| 1 | 1 | ? | Note length | Note length. Default value is 0x0A. Should be linear interpolated from 0-16 to 0-255.  |
| 2 | 1 | ? | ? | unknown |
| 3 | 1 | ? | ? | Seem to be always 0x00 |
| 4 | 1 | UINT | Note | Note value, it's 0 based for C1 and increase by one for each semitone. Divide the note by 12 give the octave number |
| 5 | 1 | UINT | Velocity | Velocity value. Default value is 100 |
| 6 | 1 | INT | Micro step adjustment | Micro step adjustment from -23 to 24 |
| 7 | 1 | ? | ? | Seem to be always 0x00 |

Size: 8 bytes

## Trask step metadata chunk

**Notes:**
- Offsets are relatives to chunk start.

| Offset | Bytes | Type | Name | Description |
|:------:|:-----:|------|------|-------------|
| 00 | 2 | UINT | Step components enabled | Bit mask on 2 bytes to show which step component are enabled. See below for bitmask values|
| 02 | 1 | UINT | Pulse parameter | Pulse parameter value, default value 0x04 |
| 03 | 1 | UINT | Step component ? parameter | Step component ? parameter value, default value is 0x02|
| 04 | 1 | UINT | Step component ? parameter | Step component ? parameter value, default value is 0x04 |
| 05 | 1 | UINT | Velocity parameter | Velocity parameter value, default value is 0x05|
| 06 | 1 | UINT | Ramp Up parameter | Ramp Up parameter value, default value is 0x04 |
| 07 | 1 | UINT | Ramp Down parameter | Ramp Down parameter value, default value is 0x04 |
| 08 | 1 | UINT | Random parameter | Random parameter value, default value is 0x04 |
| 09 | 1 | UINT | Glide parameter | Glide parameter value, default value is 0x04 |
| 10 | 1 | UINT | Multiply parameter | Multiply parameter, default value 0x02 |
| 11 | 1 | UINT | Step component ? parameter | Step component ? parameter value, default value is 0x02 |
| 12 | 1 | UINT | Pulse hold parameter | Pulse hold parameter, default value 0x04 |
| 13 | 1 | UINT | Step component ? parameter | Step component ? parameter value, default value is 0x04 |
| 14 | 1 | UINT | Step component ? parameter | Step component ? parameter value, default value is 0x02 |
| 15 | 1 | UINT | Step component ? parameter | Step component ? parameter value, default value is 0x02 |
| 16 | 38 | ?? | ??? | WIP |

Size: 54 bytes

### Step components bitmask

The step component bitmask indicate which step components are enabled.

The first byte bits are numbered from 8 to 1, the second byte bits from 16 to 09.

| 08 | 07 | 06 | 05 | 04 | 03 | 02 | 01 |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| Glide | Random | Ramp Down | Ramp Up | Velocity | Jump | Parameter Spark | Pulse |

| 16 | 15 | 14 | 13 | 12 | 11 | 10 | 09 |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| Unused | Unused | Component Spark | Trigger Spark | Tonality | Pulse Hold | Sweep | Multiply |
