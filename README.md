spec-dectris-pilatus
===========

[spec](http://www.certif.com/) bindings for the Pilatus 300k detector by
dectris through the camserver socket. The commands rely on passwordless
ssh login from the spec computer to the pilatus server. `ssh-copy-id`
will help you in setting this up.

Available commands
------------------

### Public interface
* `pilatuson [ip address]`:
    * set a global variable `_pilatusIsOn` to `1`. It is then used to avoid
    repeated loading of the configs if pilatus was on already.
    * set the threshold to 20 keV (hardcoded)
    * set the Nimages to 1
    * set the ImgPath to `/home/det/spec_images`
    * unload the flat field correction with `LdFlatField off`
    * hook the pilatus commands for exposure, that is the
    `_pilatus_user_precount()` function into the `user_precount`
    function of spec. This triggers an exposure with the usual spec commands
    (`ct`, `ascan`, `dscan`, and all the others)
    * hook the commands for moving files after a ct
        (`_pilatus_move_files_postcount`) or scan
        (`_pilatus_move_files_postscan`)
    `_pilatus_user_precount()` function into the `user_precount`
    function of spec. This triggers an exposure with the usual spec commands
* `pilatusoff`:
    * `_pilatusIsOn = 0` 
    * remove the `_pilatus_user_precount()` hook
    * remove the `_pilatus_move_files_postcount()` hook
    * remove the `_pilatus_move_files_postscan()` hook
    * close the socket connection

### Private functions
* `_pilatus_datafilename(_isCt)`:
    * set the output file name for the upcoming exposure, and format it
    differently depending of which command triggered the exposure (`ct` or
    `*scan`).
* `_pilatus_user_precount()`:
    * set the exposure time with `Exptime`
    * trigger the exposure with `Exposure filename`
* `_pilatus_move_files_postcount()`:
    * after `ct`, use `scp` to copy the file to the spec computer and remove
        it from the pilatus server
* `_pilatus_move_files_postscan()`:
    * after `*scan`, use `scp` to copy all the files to the spec computer and remove
        them from the pilatus server

Usage
-----

If the macro is loaded correctly, and the pilatus connection is found, you should be able to do `pilatuson` at the
beginning of your `spec` session, then all the `ct` and `*scan` commands
will automatically trigger exposures of the pilatus detector through the
socket.
