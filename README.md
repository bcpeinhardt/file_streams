# Gleam File Streams

This Gleam library provides access to Erlang's file streams for reading and
writing files. If you don't require streaming behavior then consider using
[`simplifile`](https://hex.pm/packages/simplifile) instead.

This library only supports the Erlang target.

[![Package Version](https://img.shields.io/hexpm/v/file_streams)](https://hex.pm/packages/file_streams)
[![Hex Docs](https://img.shields.io/badge/hex-docs-ffaff3)](https://hexdocs.pm/file_streams/)

## Example

Add this library to your project:

```sh
gleam add file_streams
```

The following code writes data to a file using a binary file stream, reads it
back in using a binary file stream, then reads it again with a text file stream.

```gleam
import file_streams/file_stream
import file_streams/file_stream_error
import gleam/bit_array

// Write file as a binary stream
let assert Ok(stream) = file_stream.open_write("test.txt")
let assert Ok(Nil) = file_stream.write_bytes(stream, <<"Hello, world!\n":utf8>>)
let assert Ok(Nil) = file_stream.write_bytes(stream, <<"12":utf8>>)
let assert Ok(Nil) = file_stream.close(stream)

// Read file as a binary stream
let assert Ok(stream) = file_stream.open_read("test.txt")
let assert Ok(bytes) = file_stream.read_bytes(stream, 14)
let assert Ok("Hello, world!\n") = bit_array.to_string(bytes)
let assert Ok([49, 50]) =
  file_stream.read_list(stream, file_stream.read_uint8, 2)
let assert Error(file_stream_error.Eof) = file_stream.read_bytes(stream, 1)
let assert Ok(Nil) = file_stream.close(stream)

// Read file as a UTF-8 text stream
let assert Ok(stream) = file_stream.open_read_text("test.txt")
let assert Ok("Hello") = file_stream.read_chars(stream, 5)
let assert Ok(", world!\n") = file_stream.read_line(stream)
let assert Ok("12") = file_stream.read_line(stream)
let assert Error(file_stream_error.Eof) = file_stream.read_line(stream)
let assert Ok(Nil) = file_stream.close(stream)
```

Further documentation can be found at <https://hexdocs.pm/file_streams/>.

## License

This library is published under the MIT license, a copy of which is included.
