### The Decompression Process

- The compress in mainly handled by the `inflate` function in the zlib library which requires a z_stream object. 

- Initialize `z_stream` with `inflateInit()`.
```cpp

  do {
    file.read(reinterpret_cast<char *>(in.data()), CHUNK);
    /*
	    istream& read(char* s, streamsize n);
	    Both char and uint8_t are (on virtually all systems) types that represent a single byte 
	    of memory. You are simply reading raw bytes from a file into a buffer of raw bytes. 
	    The cast is purely to satisfy the function's strict signature.
    */
    strm.avail_in = file.gcount();
    if (file.fail() && !file.eof()) { // Check for read errors (excluding EOF)
      (void)inflateEnd(&strm);
      file.close(); // Close the file
      fprintf(stderr, "Z_ERRNO: %d\n", Z_ERRNO);
      return errorcode::ReadError;
    }
    
    if (strm.avail_in == 0)
      break;
    strm.next_in = in.data();
    
    do {
      strm.avail_out = CHUNK;
      strm.next_out = out.data();
      ret = inflate(&strm, Z_NO_FLUSH);
      assert(ret != Z_STREAM_ERROR);

      switch (ret) {
	    case Z_NEED_DICT:
	        ret = Z_DATA_ERROR; /* and fall through */
	    case Z_DATA_ERROR:
	    case Z_MEM_ERROR:
	        fprintf(stderr, "inflate error with ret %d\n", ret);
	        file.close();
	        (void)inflateEnd(&strm);
	        return errorcode::InternalError;
      }
      
      have = CHUNK - strm.avail_out;
      
    if (have > 0) {
	    raw_content.insert(raw_content.end(), out.begin(), out.begin() + have);
    }
      
    } while (strm.avail_out == 0);
  } while (ret != Z_STREAM_END);

```

* No need to flush out `in` and `out` because after every cycle we initialize the `next_in` and `next_out` to the beginning and only read till `avail_in` and `avail_out`
* `strm.avail_out` is the **remaining empty space** in the output buffer. Because the buffer starts full (CHUNK), the amount of data **written** (have) is the difference between the initial space and the remaining space. Therefore, have must be calculated as `CHUNK − strm.avail_out` to get the number of bytes successfully produced by `deflate` or `inflate`.
* Call `inflate()` with `Z_NO_FLUSH` which is the default flag which basically says  decompresses data and will stop when any of these three conditions are met:
		1. Input is exhausted (**avail_in** becomes 0).
		2. The output buffer is full (**avail_out** becomes 0).
		3. The decompression is complete (the function returns **Z_STREAM_END**).
