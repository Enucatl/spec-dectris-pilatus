spec-pilatus
===========

[spec](http://www.certif.com/) bindings for the Pilatus 300k detector by
dectris through the camserver socket

Available commands
------------------

### Public interface
* `pilatuson [ip address]`:
    * set a global variable `_pilatusIsOn` to `1`. It is then used to avoid
    repeated loading of the configs if pilatus was on already.
    * set the threshold to 20 keV (hardcoded)
    * hook the pilatus commands for exposure, that is the
    `_pilatus_user_precount()` function into the `user_precount`
    function of spec. This triggers an exposure with the usual spec commands
    (`ct`, `ascan`, `dscan`, and all the others)
* `pilatusoff`:
    * `_pilatusIsOn = 0` 
    * remove the `_pilatus_user_precount()` hook

### Private functions
* `_pilatus_datafilename(_isCt)`:
    * set the output file name for the upcoming exposure, and format it
    differently depending of which command triggered the exposure (`ct` or
    `*scan`).
* `_pilatus_user_precount()`:
    * set the file index to `0` with `sls_detector_put` since spec already
    increases the index on its own 
    * set the exposure time with `sls_detector_put`
    * trigger the exposure with `sls_detector_acquire`

Usage
-----

If the macro is loaded correctly, and the pilatus connection is found, you should be able to do `pilatuson` at the
beginning of your `spec` session, then all the `ct` and `*scan` commands
will automatically trigger exposures of the pilatus detector through the
socket.
