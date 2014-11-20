spec-mythen
===========

[spec](http://www.certif.com/) bindings for the mythen detector with sls_detector_* commandline binaries

Available commands
------------------

### Public interface
* `mythenon`:
    * set a global variable `_mythenIsOn` to `1`. It is then used to avoid
    repeated loading of the configs if mythen was on already.
    * load the mythen config file, hardcoded to `~/slsDetectorsPackage/examples/mythen.config`
    * `sls_detector_put settings standard`
    * set the threshold to 20 keV (hardcoded)
    * hook the mythen commands for exposure, that is the
    `_mythen_user_precount()` function into the `user_precount`
    function of spec. This triggers an exposure with the usual spec commands
    (`ct`, `ascan`, `dscan`, and all the others)
* `mythenoff`:
    * `_mythenIsOn = 0` 
    * remove the `_mythen_user_precount()` hook

### Private functions
* `_mythen_datafilename(_isCt)`:
    * set the output file name for the upcoming exposure, and format it
    differently depending of which command triggered the exposure (`ct` or
    `*scan`).
* `_mythen_user_precount()`:
    * set the file index to `0` with `sls_detector_put` since spec already
    increases the index on its own 
    * set the exposure time with `sls_detector_put`
    * trigger the exposure with `sls_detector_acquire`

Usage
-----

If the macro is loaded correctly, and the mythen software and config are
found in the folders above, you should be able to do `mythenon` at the
beginning of your `spec` session, then all the `ct` and `*scan` commands
will automatically trigger exposures of the mythen detector through the
`sls_detector_acquire` command.
