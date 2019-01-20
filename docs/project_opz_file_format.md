# OP-Z Project file format

## Foreword

Project files are available in folder projects when the device is connected in disk mode.
They are numbered from 01 to 10, corresponding to the project orders.

The OP-Z run on a Blackfin ADSP-BF703 processor.
This processor use little-endian memory processing.

This file seem to be memory mapped so all data longer than 1 byte in length are stored LSB first.

Example:
`0xFF3F` should be read `0x3FFF`




| Offset | Bytes | Type | Name | Description |
|:------:|:-----:|------|------|-------------|
| 0 | 4 | ? | File identifier | value meaning unknown, but it's always a byte, followed by 3 0x00 bytes. The first byte value increase with firmware version number from under which the project was created. With firmware 1.1.17 the value is 0x49 |
| 4 | 480 | UINT[] | First saved chain pattern | List of 32 bytes blocks containing the chained pattern numbers (indexed to 0 ) |
| 484 | 32 | Array | Current chain pattern | List of 32 bytes containing the chained pattern numbers (indexed to 0 ) |
| 516 | 4 | ? | ? | ? |
| 520 | 1 | Unsigned integer | Project tempo | Project tempo from 40 to 200 |
| 521 | ? | ?? | ?? | ?? |
| 565 | 1 | UINT | Swing | Project's Swing level from 0 to 255 |
| 566 | 1 | UINT | Metronome level | Metronome sound level |
| 567 | 1 | UINT | Metronome sound | Metronome sound selection from 0x00 to 0xFF. Values might be mapped with some linear interpolated indexes |
| 576 | 1 | UINT | Track 1 length | Track 1 length in steps from 1 to 16 |
| 578 | 1 | UINT | Track 1 step length | Track 1 step length from 1 to 10 |
| 579 | 1 | UINT | Track 1 quantize | Track 1 quantize |
| 581 | 1 | UINT | Track 1 note duration | Track 1 note duration |
| 726 | 1 | ? | ? | passe de 3C à 0B lorsqu'on met un step component sur la T1S1 |
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
| 7041 | 13822 | UINT[] | ?? | ?? |

They should have been some pattern specific header or footer.

### Tracks steps ordering.

**Notes:**
- T mean track with the track number from 1 to 16 : T1 for track 1, T2 for track 2.
- S mean step with the step number from 1 to 16: S1 for step 1, S2 for step 2.
- Each drum track (from track 1 to 4) can have two notes played at the same time.
- Each Synth track can have four notes played at the same time.

| Index | Bytes | Content |
| T1S1 | T2S1 | T3S1 | T4S1 | ... |

| N1 | N2 | N1 | N2 | N1 |N2 | ...|

## Track step note chunk

**Notes:**
- Offsets are relatives to note chunk start.
- Micro step adjustments are the same for all notes for the same step

| Offset | Bytes | Type | Name | Description |
|:------:|:-----:|------|------|-------------|
| 0 | 1 | ? | ? | passe de 00 a FF lorsqu'on met la longueur de note a fond, revient à 0 lorsqu'on n'est plus a fond |
| 1 | 1 | ? | Note length | Note length. Default value is 0x0A. Should be linear interpolated from 0-16 to 0-255.  |
| 2 | 1 | ? | ? | passe de 00 à 01 lorsqu'on met la longueur de note à fond , revient à 0 lorsqu'on n'est plus a fond |
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
...
Size: 54 bytes

### Step components bitmask

The step component bitmask indicate which step components are enabled.

The first byte bits are numbered from 8 to 1, the second byte bits from 16 to 09.

| 08 | 07 | 06 | 05 | 04 | 03 | 02 | 01 | 16 | 15 | 14 | 13 | 12 | 11 | 10 | 09 |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| Glide | Random | Ramp Down | Ramp Up | Velocity | Jump | Parameter Spark | Pulse | Unused | Unused | Component Spark | Trigger Spark | Tonality | Pulse Hold | Sweep | Multiply |

## pense bete:

chaque step peut avoir 14 step component (possible bitmask)
chaque step component peut avoir 1 valeur de 1 à 10

les steps components sont codés bizarrement,l'ordre ne semble pas être respecté
par contre il n'y a pas de type, ils ont une place bien précise

les steps components qui peuvent avoir une valeur 3 positions (les sparks) sont codés comme suit:
MSB = type de position:
0 Trig on value
1 Trig on 0
2 Trig on everything else the value


Ajouter des notes à une track modifie aussi le byte 583

valeur vide:
valeur avec step 1 set: 3C
valeur avec step 1 et 2 set: 48
avec des notes sur la track 2: le byte 594 passe de 3C à 40

avec des notes sur la track 3 le byte 606 passe de 3C à 35 <- ca doit avoir rapport avec le plug/preset sélectionné comparer ca avec le plugs.json
