# uv
all functions below are js bindings to libuv C functions **uv_*** for more details please refer to [libuv documentation](http://docs.libuv.org/en/v1.x/api.html)
### run
### close
### shutdown

## stream
### walk
### cancel
### listen
### accept
### read_start
### read_stop
### write
### is_readable
### is_writable
### stream_set_blocking

## timer
>timer functions allow scheduling the execution of a function after a certain interval and optionally to repeat it after another given interval.
>
>the dukluv binding provides a functionality similar to setTimeout, setInterval, setImmediate, clearTimeout,clearInterval with the added extra feature that one can restart/reuse a timer handle.
>
>The downside is that you are responsible for destroying the handler(?)

### Timer related functions
#### new_timer():_uv_timer_t_
creates a new timer handle (just like libuv uv_timer_init)

    thandle=uv.new_timer();

#### timer_start( _uv_timer_t_:th, _int_:timeout, _int_:repeatTimeout, _function_:onTimeout )
starts the timer pointed to by the timer_handle
```js
thandle=uv.new_timer();
timer_start(thandle,1000,1000,function(x){print("coucou",x);});
```

Example : 
```js
function setTimeout(fn,timeout){
  var thandle=uv.new_timer();
  timer_start(thandle,timeout,0,fn);
  return thandle;
}
```


#### timer_stop( _uv_timer_t_:th)
stops the th timer
```js
function clearTimeout(thandle){
  timer_stop(thandle);
}
```
timer_again(uv_timer_t:th)
restarts a timer
```js
function rerunTimeout(uv_timer_t:thandle){
  timer_stop(thandle);
}
```
#### timer_set_repeat(uv_timer_t:th)
  sets the timeout value of a timer
#### timer_get_repeat(uv_timer_t:th)
  gets the timeout value of a timer


## tcp
// TODO
### new_tcp
// TODO
### tcp_open
// TODO
### tcp_nodelay
// TODO
### tcp_keepalive
// TODO
### tcp_simultaneous_accepts
// TODO
### tcp_bind
// TODO
### tcp_getpeername
// TODO
### tcp_getsockname
// TODO
### tcp_connect
// TODO

##pipe
// TODO
### new_pipe
// TODO
### pipe_open
// TODO
### pipe_bind
// TODO
### pipe_connect
// TODO
### pipe_getsockname
// TODO
### pipe_pending_instances
// TODO
### pipe_pending_count
// TODO
### pipe_pending_type
// TODO

##tty
// TODO
### new_tty
// TODO
### tty_set_mode
// TODO
### tty_reset_mode
// TODO
### tty_get_winsize
// TODO

#filesystem
// TODO
### fs_close
// TODO
### fs_open
// TODO
### fs_read
// TODO
### fs_unlink
// TODO
### fs_write
// TODO
### fs_mkdir
// TODO
### fs_mkdtemp
// TODO
### fs_rmdir
// TODO
### fs_scandir
// TODO
### fs_scandir_
// TODOnext
### fs_stat
// TODO
### fs_fstat
// TODO
### fs_lstat
// TODO
### fs_rename
// TODO
### fs_fsync
// TODO
### fs_fdatasyn
// TODOc
### fs_ftruncat
// TODOe
### fs_sendfile
// TODO
### fs_access
// TODO
### fs_chmod
// TODO
### fs_fchmod
// TODO
### fs_utime
// TODO
### fs_futime
// TODO
### fs_link
// TODO
### fs_symlink
// TODO
### fs_readlink
// TODO
### fs_chown
// TODO
### fs_fchown
// TODO

# other
// TODO
### getrusage
// TODO
### chdir
// TODO
### hrtime
// TODO
### update_time
// TODO

## to categorize
// TODO
### inflate
// TODO
### deflate
// TODO

## dukluv process
// TODO
### version
// TODO
### version_string
// TODO
### argv()
gets the argv
// TODO
### cwd
// TODO
### guess_handle
// TODO
### get_process_title
// TODO
### set_process_title
// TODO
### uptime
// TODO
### resident_set_memory
// TODO
### loadavg
// TODO

## system information
### cpu_info
### get_total_memory
### interface_addresses
### exepath
### os_homedir
### now
