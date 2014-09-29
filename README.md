# libsodium-net 
libsodium-net, or better said, [libsodium](https://github.com/jedisct1/libsodium) for .NET, is a C# wrapper around libsodium. For those that don't know, libsodium is a portable implementation of [Daniel Bernstein's](http://cr.yp.to/djb.html) fantastic [NaCl](http://nacl.cr.yp.to/) library. If you aren't familiar with NaCl, I highly suggest that you look into libsodium and NaCl before using this library.


## Downloading libsodium-net

- [Github repository](https://github.com/adamcaudill/libsodium-net)
- [Tarballs and pre-compiled binaries](https://github.com/adamcaudill/libsodium-net/releases)

## Requirements & Versions

This library can be built in Visual Studio 2010, Xamarin Studio (MonoDevelop 3.x supported), and targets .NET 4.0; it is compiled against libsodium v0.7.0.

On OSX & Linux, your copy of `libsodium` must be compiled for the same architecture as your copy of Mono. If you are running a 32bit process, your copy of `libsodium` must be 32bit as well.

## File Signing

Starting with version 0.4.0, all files are signed via a Certum.pl Code Signing certificate. The files are signed under the name `Open Source Developer, Adam Caudill` - this can be used to ensure that the files haven't been altered.

## License

NaCl has been released to the public domain to avoid copyright issues. libsodium is subject to the [ISC license](https://en.wikipedia.org/wiki/ISC_license), and this software is subject to the [MIT license](https://en.wikipedia.org/wiki/MIT_License).

## About this documentation

This documentation is an incomplete copy of the [original](http://doc.libsodium.org/) written by Frank Denis (@jedisct1).

**!THIS DOCUMENTATION OF LIBSODIUM-NET IS CURRENTLY INCOMPLETE!**

*Incomplete parts are marked with a **(wip)** = Work in progress*

Feel free to contribute.

## Status

Not yet published.