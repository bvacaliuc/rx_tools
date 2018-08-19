FFTW

(http://www.fftw.org/install/windows.html)

To support, windows builds, please download and extract the following:
'fftw-3.3.4-dll32.zip' (MD5sum 757ce6282e7cab2dfa2dd40ba46f1c1f) from ftp://ftp.fftw.org/pub/fftw/fftw-3.3.4-dll32.zip into x86/
'fftw-3.3.4-dll64.zip' (MD5sum c840c5450f57a98578783fa633047d83) from ftp://ftp.fftw.org/pub/fftw/fftw-3.3.4-dll64.zip into x64/

You should have (at least) the following files extracted into each folder:

COPYRIGHT
fftw3.h
libfftw3-3.def
libfftw3-3.dll

In addition, in order to support codeblocks compilation, I used MSVC++ lib command to produce 'libfftw3-3.lib', as I was unsuccessful in using 'dllwrap' from the codeblocks:: 12.11 mingw base.

dos> lib /def:libfftw3-3.def

NOTE: as of April 30, 2016, I have incorporated these steps into a CMakeList.txt file so it is done by the cmake step.  However, extracting the .zip file requires manual intervention.
