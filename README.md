# WFM file format for Rigol DHO800/900 Series Oscilloscopes

This is documentation (incomplete) for the WFM file format is used by the following Rigol oscilloscopes: DHO802, DHO804, DHO812, DHO814, DHO914, DHO914S, DHO924, and DHO924S.

It is composed of the following 4 parts:

- File header (1 per file)
- Session Data (1 per file)
- Frame Header (1 per file)
- Frame Data (combination of all active analog channels)

The file format only supports saving analog data, not digital data from the logic analyzer.  All values are written as little-endian format.

The design of the file implies the possibility that multiple Frame Headers + Frame Data sections could exist in a singe file.  However, this has not been observed.

# File Header

Length is 16 bytes in the following format:

- uint32: version?  Hardcoded to a value of 02 00 00 00.
- uint32: 32-bit CRC, computed over Session Data only.
- uint64: sessionDataLength, though the upper 32-bits are masked off so only the bottom 32-bit contain the Session Data's length.

# Session Data

Length is variable and determined in the File Header.  Contents not yet analyzed.

# Frame Header

Length is 64 byte block in the following format:
- uint64:     frameIndexFirst?  Typically 0.
- uint64:     frameIndexLast?  Typically 0.
- uint64:     frameIndexCurrent, which one is being written at this moment?  Typically 0.
- uint64:     totalSamplesAcrossChannels.  This value is the number of all samples in the Frame Data section.  Thus, 2 \* this value is the length of the Frame Data in bytes (samples are 16-bit quantities).
- uint8\[8\]: garbage padding
- uint32:     unknown value (example: F4 01 00 00)
- uint32:     unknownConstIs4D.  Always a constant of 0x4d ('M')
- uint32:     channelSamples1.  This is the number of samples per channel.
- uint32:     channelSamples2.  Same value as channelSamples1.
- uint8:      unknownConstIs1.  Always the constant value of 01.
- uint8\[7\]: garbage padding

# Frame Data

The frame data section contains data from all active channels such that all CH1 data is written first, then CH2 data, then CH3, and then CH4 (only for the active channels).  Each sample is written as a 16-bit quantity.  The total number of samples is specified in the Frame Header, as is the number of samples per channel.
