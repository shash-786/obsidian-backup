### The Compression Process

- The compress in mainly handled by the `deflate` function in the zlib library which requires a z_stream object. 

- Initialize `z_stream` with `deflate_init()` and specify the compression level.

- `z_stream` basically keeps track of the following 
```cpp
/* 
  strm.avail_in: Number of bytes available in the input buffer;
  strm.next_in: Pointer to the next byte to be read from the input buffer;
  strm.avail_out: Number of bytes available in the output buffer;
  strm.next_out: Pointer to the next free byte in the output buffer.
 */
```
- Read the input file and store the data in bytes in a input vector with number of bytes read stored in `strm.avail_in` 
- Check if Error in reading, If yes quit.
- If not Check if `EOF` and SET `flush` accordingly (`flush` will keep track of the current flushing state for deflate(), which is either no flushing, or flush to completion after the end of the input file is reached) reset `strm.avail_in` to the beginning of the input vector.
```cpp
// READING LOGIC
	do {
		strm.avail_out = CHUNK; // <- temp
		strm.next_out = out.data();
		ret = deflate(&strm, flush); // <- avail_out will be changed to the no of bytes compressed 
		assert(ret != Z_STREAM_ERROR);
		int have = CHUNK - strm.avail_out;
		
		if (have > 0) {
			compressed.insert(compressed.end(), out.begin(), out.begin() + have);
		}
	} while (strm.avail_out != 0)
	
} while (flush != Z_FINISH) 
assert(ret == Z_STREAM_END) ;
``` 

