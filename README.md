# rx\_tools

`rx_fm`, `rx_power`, and `rx_sdr` tools for receiving data from SDRs,
based on `rtl_fm`, `rtl_power`, and `rtl_sdr` from [librtlsdr](https://github.com/librtlsdr/librtlsdr),
but using the [SoapySDR](https://github.com/pothosware/SoapySDR) vendor-neutral SDR support library instead, intended
to support a wider range of devices than RTL-SDR.

## Building

[Install SoapySDR](https://github.com/pothosware/SoapySDR/wiki#installation), then:

### Linux

    mkdir build
    ( cd build && cmake .. && make )

*NOTE: not tested at the moment...*

### Windows 10

#### [VS2015 Community Edition](https://stackoverflow.com/questions/44290672/how-to-download-visual-studio-community-edition-2015-not-2017)

Using `VS2015 x64 Native Tools Command Prompt`, perform:

    mkdir build
    cd build
    cmake -G "Visual Studio 14 2015 Win64" ..
    msbuild rx_tools.sln /P:Configuration=Release

After building, these binaries below should be available in `build/Release/`

#### [VS2017 Community Edition](https://visualstudio.microsoft.com/vs/community/)

Using `VS2017 x64 Native Tools Command Prompt`, perform:

    mkdir build
    cd build
    cmake -G "Visual Studio 15 2017 Win64" ..
    msbuild rx_tools.sln /P:Configuration=Release

After building, these binaries below should be available in `build/Release/`

## Tools included

After building, these binaries should then be available at the root directory:

* `rx_fm` (based on `rtl_fm`): demodulator for FM and other modes, see [rtl\_fm guide](http://kmkeen.com/rtl-demod-guide/index.html)

* `rx_power` (based on `rtl_power`): FFT logger, see [rtl\_power](http://kmkeen.com/rtl-power/)

* `rx_sdr` (based on `rtl_sdr`): emits raw I/Q data

* `rx_rss` (based on `rtl_sdr`): use FFTW, compute averaged spectograms and format to socket server for Radio Sky Spectrograph display

### Not included

Tools from librtlsdr not included in this repository:

* `rtl_eeprom`, `rtl_test`, `rtl_ir`: specific to RTL-SDR devices
* `rtl_tcp`, `rtl_rpcd`: remote networking, see [SoapyRemote](https://github.com/pothosware/SoapyRemote) instead
* `rtl_adsb`: see [dump1090](https://github.com/mutability/dump1090)

## Device support

Planned testing
* [ ] RTL-SDR dongle(s)
* [ ] [LimeSDR](https://www.crowdsupply.com/lime-micro/limesdr)
* [ ] [LimeSDR Mini](https://www.crowdsupply.com/lime-micro/limesdr-mini)
* [ ] [XTRX-CS](https://www.crowdsupply.com/fairwaves/xtrx)
* [ ] [BladeRF x15/x40](https://www.nuand.com/product/bladerf-x40/)
* [ ] [BladeRF xA4](https://www.nuand.com/product/bladerf-xa4/)


## Version history

See [ChangeLog.md](./ChangeLog.md)
