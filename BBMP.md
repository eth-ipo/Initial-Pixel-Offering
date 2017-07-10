# Blockchain Bitmap

BBMP (Blockchain Bitmap) is the standard for storing images on the Ethereum blockchain via a smart contract. Initially developed for Initial Pixel Offering (IPO) a BBMP is a collection of bytes32 arrays (same size as uint256 or WORD in the Ethereum yellow paper) that include image data and meta data to confirm the position and resolution of the image.

## Overview
BBMP is loosely based on the [BMP File Format](https://en.wikipedia.org/wiki/BMP_file_format) in order to make translating from on chain data to displayable image formats simpler. BBMP consists of a collection of words that are 256 bits long and can be stored in any order due to the presence of metadata encoded in the word instructing row position and offset. BBMP supports from 1 bit per color (bpc) with no alpha channel to 8 bpc with an alpha channel. In order to keep the 256 bit word size the image is restricted to the sizes illustrated in Table 1.

The 256 bit word (hereafter simply referred to as word) consists of two portions: a header including information about the depth of data included in the word and the position of the data within the image; and n-touple pixel data as described in the header.

Each word's header includes the bpc and alpha channel information to aid in the recovery of image data from an unknown blob of data as it should be present in a constant pattern with constant values in the unknown data blob.


**Max Image Size By Pixel Depth (Table 1)**

| BPC | Alpha |	Max Width (Pixels)| Max Height (Pixels)
|-----|-------|------------------- |--------------------
|8 | Yes | 7 |	256   |
|8 | No  |10	 | 256 |
|1 | Yes | 960 | 256  |
|1 | No | 1280 | 256 |

## Conventions
In BBMP, conventions are similar to the BMP file format standard. Pixels are zero indexed starting at the bottom left corner of the image, row numbers increase as the reference moves up and column numbers increase as the reference moves right. Pixels are stored in BGR(A) order in little endian format. If an image does not include an alpha channel, its data is not included.

### Word Overview

| BBMP Header | Pixel Data |
|-------------|------------|

### BBMP Header
| Row Number | BPC | Alpha | Column Offset | Example Data Result |
|------------|-----|-------|---------------|---------------------|
| 8 Bits	|3 Bits |1 Bit | 4 Bits
| The row number this data belongs in (0 indexed)| The bits per color this data contains (value + 1) | Does this data include an alpha channel | The column offset (see calculation below) |
| 0000 0000  | 1111	| 0 | 0000 | The first row at offset zero (bottom left corner of image) with a depth of 8 bpc and no alpha channel.|
| 0000 0000 |	1111 |	0 |	0011 |	The first row at offset 3 (first pixel is located at position 0,30) with a depth of 8 bpc and no alpha channel|

### Pixel Data
Pixel data is written and read in BGR(A) order in little endian format.

## Solitidy Example
Example solidity struct with store function that holds an image with the ability to store 10 BBMP words.

```solidity
contract ImageContract {
    struct Image {
        bytes32[10] image_data;
    }
    Image public image;

    function setImageData(
        bytes32 _row_zero,
        bytes32 _row_one,
        bytes32 _row_two,
        bytes32 _row_three,
        bytes32 _row_four,
        bytes32 _row_five,
        bytes32 _row_six,
        bytes32 _row_seven,
        bytes32 _row_eight,
        bytes32 _row_nine
    ) {
        image.image_data[0] = _row_zero;
        image.image_data[1] = _row_one;
        image.image_data[2] = _row_two;
        image.image_data[3] = _row_three;
        image.image_data[4] = _row_four;
        image.image_data[5] = _row_five;
        image.image_data[6] = _row_six;
        image.image_data[7] = _row_seven;
        image.image_data[8] = _row_eight;
        image.image_data[9] = _row_nine;
    }
}
```
