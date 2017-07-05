# uv
uv_schema.h
schema.h
callbacks.h
duv.h
handle.h
miniz.h
misc.h
refs.h
req.h
utils.h

## loop
loop.h
### run
### close
### shutdown

## stream
stream.h
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
timer.h
timer functions allow scheduling the execution of a function after a certain interval and optionally to repeat it after another given interval.

the dukluv implementation provides a functionality similar to setTimeout, setInterval, setImmediate, clearTimeout,clearInterval with the added extra feature that one can restart/reuse a timer handle.

The downside is that you are responsible for destroying the handler(?)

### new_timer():uv_timer_t
creates a new timer handle (just like libuv uv_timer_init)

    thandle=uv.new_timer();

### timer_start( _uv_timer_t_:th, _int_:timeout, _int_:repeatTimeout, _function_:onTimeout )
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


### timer_stop(uv_timer_t:th)
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
### timer_set_repeat(uv_timer_t:th)
  sets the timeout value of a timer
### timer_get_repeat(uv_timer_t:th)
  gets the timeout value of a timer


## tcp
tcp.h

Server workflows
Server : new_tcp->(bind & listen)->server
onConnection+=(getSocket->accept->readStart)
socket.onReadStart+=(?data->compileResponse->socket.write)
socket.onReadStart+=(!data->(shutdown & stopRead & close ) )

Client Workflows

Client : new_tcp->connect
onConnect+=(readStart & write)
onResponse+=(?data->doSomethingWithResponse->shutdown)
onResponse+=(!data->close)

// TODO
### new_tcp():uv_tcp_t
Initializes the handle. No socket is created.
// TODO
### tcp_open
Opens an existing file descriptor or SOCKET as a TCP handle.
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
pipe.h
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
tty.h
// TODO
### new_tty(int:number,boolean:readable):uv_stream_t
Initialize a new TTY stream with the given file descriptor. Usually the file descriptor will be:

0 = stdin
1 = stdout
2 = stderr
readable, specifies if you plan on calling uv_read_start() with this stream. stdin is readable, stdout is not.

>to check
>On Unix this function will determine the path of the fd of the terminal using ttyname_r(3), open it, and use it if the passed file descriptor refers to a TTY. This lets libuv put the tty in non-blocking mode without affecting other processes that share the tty.

example :
```js
stdout = uv.new_tty(1, false);
stdin = uv.new_tty(0, true);
stderr = uv.new_tty(2, false);
```
use those handles to read and write to the stream using the stream functions
// TODO
### tty_set_mode(uv_tty_mode_t)
Set the TTY using the specified terminal mode.

the accepted modes can be coded as follows
ecmascript6
```java
enum{ UV_TTY_MODE_NORMAL, UV_TTY_MODE_RAW, UV_TTY_MODE_IO }```

ecmascript5 and below
```js
uv.tty_mode={};
/* Initial/normal terminal mode */
uv.tty_mode[uv.tty_mode[0]="UV_TTY_MODE_NORMAL"]=0;
/* Raw input mode (On Windows, ENABLE_WINDOW_INPUT is also enabled) */
uv.tty_mode[uv.tty_mode[1]="UV_TTY_MODE_RAW"]=1;
/* Binary-safe I/O mode for IPC (Unix-only) */
uv.tty_mode[uv.tty_mode[2]="UV_TTY_MODE_IO"]=2;```

### tty_reset_mode()
To be called when the program exits. Resets TTY settings to default values for the next process to take over.
### tty_get_winsize()
Gets the current Window size. On success it returns 0.

#filesystem
fs.h

All fs functions take a callback, which is allowed to be NULL. If the callback is NULL the request is completed synchronously, otherwise it will be performed asynchronously.
#### type fs_callback function(_handle_:response,_handle_:error)
callback function(pointer:response, Error:error)
the callback is passed to all asynchronous functions as a last parameter.

when the request resulted in an error, the response will be null and the error will be not null (arguments length is 2)
when the request has a result the response will contain something (buffer, file descriptor, ... ) and the error
should be undefined ( arguments.length will be 1 )

the pattern used for callbacks is similar to the nodejs one 
```js
function(result,err){
	if(result==null){
		doSomething(err);
		return ;
	}

	doSomethingElse(result);

}
```
// TODO
### fs_close(uv_file:fd,_function()_:callback)
number of arguments : 2
// TODO
### fs_open(_String_:filename,_String_:fileMode,_function(uv_file:fd)_:callback):int
number of arguments : 4
opens the file named by filename, with the mode, the callback is optional, if no callback is present the function is synchronous
// TODO
### fs_read(uv_file:fd,int:length,int:offset,fs_callback:callback):int
number of arguments : 4
reads at most length bytes from the file,
returns the number of bytes read;
to continue reading one must invoke the function again adjusting the offset
```js
function readfile(filename,mode,onFinished){
	var _BUFFER_SIZE_=1024*1024*64;
	
	var fd=fs_open(filename,mode);

	if(!fd){
		onFinished(null,new Error("file ["+filename+"] could not be opened for ["+mode+"]"))
	}else{
		__read(0);
	}
	
	var fileContents="";
	
	function __read(offset){
		uv.fs_read(fd,_BUFFER_SIZE_,offset,function(r,e){
			if(r===null){
				onFinished(fileContents);
				return;
			}
			fileContents+=r;
			__read(fileContents.length);
		});
	}
}
```
take note that for dukluv buffers and strings are freely interchangeable
in the sense that ```string === new Buffer(string).toString() === buffer.toString()```
all string are utf8;

// TOCHECK
### fs_unlink(string:filename,_function_:callback)
number of arguments : 2
deletes a file 

// TOCHECK
### fs_write(_uv_file_:fd,_Buffer_:data,_int_:offset,_?_:next)
number of arguments : 4
writes the data buffer to a file pointed to by the file descriptor fd
beginning with the offset

### fs_mkdir(String:path,int:mode,_function_:callback)
number of arguments : 3
creates the folder
// TOCHECK : windows
### fs_mkdtemp(???)
number of arguments : 2
// TODO
### fs_rmdir(String:path,int:mode,_function_:callback)
number of arguments : 2
removes a folder
// TODO
### fs_scandir(String:path,int:mode):request
number of arguments : 2
returns a continuation reference
// TODO
### fs_scandir_next(reference:ref)
number of arguments : 1
```js
function scandir(dir){
	var files=[];
	var f;
	var req=uv.fs_scandir(dir);
	while(f=uv.fs_scandir_next(req))files.push(f);
	return files;
}
```
// TODO
### fs_stat(String:filename)
number of arguments : 2
gets the file information
// TODO
### fs_fstat
number of arguments : 2
// TODO
### fs_lstat
number of arguments : 2
// TODO
### fs_rename
number of arguments : 3
// TODO
### fs_fsync
number of arguments : 2
// TODO
### fs_fdatasync
number of arguments : 2
// TODOc
### fs_ftruncate
number of arguments : 3
// TODOe
### fs_sendfile
number of arguments : 5
// TODO
### fs_access
number of arguments : 3
// TODO
### fs_chmod
number of arguments : 3
// TODO
### fs_fchmod
number of arguments : 3
// TODO
### fs_utime
number of arguments : 4
// TODO
### fs_futime
number of arguments : 4
// TODO
### fs_link
number of arguments : 3
// TODO
### fs_symlink
number of arguments : 4
// TODO
### fs_readlink
number of arguments : 2
// TODO
### fs_chown
number of arguments : 4
// TODO
### fs_fchown
number of arguments : 4
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