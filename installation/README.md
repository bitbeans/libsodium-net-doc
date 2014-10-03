## Installation

**Windows**: For Windows, the `libsodium` library is included in the [release](https://github.com/adamcaudill/libsodium-net/releases) packages. Or just use the [NuGet version](https://www.nuget.org/packages/libsodium-net/) which has everything you need.

**OSX**: For OSX, `libsodium-net` can easily be built in Xamarin Studio, and `libsodium` can be installed easily via `brew`:

    brew install libsodium --universal

**Linux**: As with OSX, building with Xamarin Studio is simple, or there's always the option of using `xbuild`:

    xbuild libsodium-net.sln

For `libsodium`, many package managers provide older versions, so it's recommended to build the latest version from source. Thankfully, this is a fairly painless process. See the [travis-build-libsodium.sh](https://github.com/adamcaudill/libsodium-net/blob/master/travis-build-libsodium.sh) file or the `libsodium` [README](https://github.com/jedisct1/libsodium/blob/master/README.markdown) file for details.

**Other**: Support for other Mono supported platforms hasn't been determined. It may or may not work.

Note: For all platforms, it's critical that `libsodium` be compiled for the architecture that the process is running under. If they don't match, you can expect to see errors. If your process is x86/i386, you can't use a copy of `libsodium` compiled for x64.
