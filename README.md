
# Table of Contents

1.  [SSH2 SFTP Client](#org33d0f93)
2.  [Installation](#orgc20d50c)
3.  [Basic Usage](#org3e9d405)
4.  [Version 5.x](#org4c37418)
    1.  [Breaking Changes in Version 5.x](#org91a1101)
        1.  [Error Event Handling](#org76baac9)
        2.  [Technical Details](#org3ff08be)
    2.  [New Methods](#org2229bf1)
    3.  [Version 5.0.1](#org881495f)
    4.  [Version 5.0.2](#org85c7e53)
    5.  [Version 5.1.0](#orgb53d154)
5.  [Documentation](#org1af5e1c)
    1.  [Specifying Paths](#org01473a1)
    2.  [Methods](#org36e00e2)
        1.  [new SftpClient(name) ===> SFTP client object](#orgafb6a2c)
        2.  [connect(config) ===> SFTPstream](#org85957be)
        3.  [list(path, pattern) ==> Array[object]](#org2356be0)
        4.  [exists(path) ==> boolean](#orgaeaea56)
        5.  [stat(path) ==> object](#org66b81b4)
        6.  [get(path, dst, options) ==> String|Stream|Buffer](#org864073b)
        7.  [fastGet(remotePath, localPath, options) ===> string](#orge6f2fdc)
        8.  [put(src, remotePath, options) ==> string](#org4694c5b)
        9.  [fastPut(localPath, remotePath, options) ==> string](#org443b620)
        10. [append(input, remotePath, options) ==> string](#orgedc4474)
        11. [mkdir(path, recursive) ==> string](#org2cdb895)
        12. [rmdir(path, recursive) ==> string](#orgc015cd5)
        13. [delete(path) ==> string](#orge6ba100)
        14. [rename(fromPath, toPath) ==> string](#org8f8e4d3)
        15. [chmod(path, mode) ==> string](#orgbd9261a)
        16. [realPath(path) ===> string](#org62857a5)
        17. [cwd() ==> string](#org7430bff)
        18. [uploadDir(srcDir, dstDir) ==> string](#orga18dd1e)
        19. [downloadDir(srcDir, dstDir) ==> string](#org8c8d706)
        20. [end() ==> boolean](#org1c3c249)
        21. [Add and Remove Listeners](#org557e2b9)
6.  [FAQ](#orgb3077fd)
    1.  [Remote server drops connections with only an end event](#org971e5b5)
    2.  [How can you pass writable stream as dst for get method?](#org10ba954)
    3.  [How can I upload files without having to specify a password?](#org4c14fa9)
    4.  [How can I connect through a Socks Proxy](#orgc6aac39)
    5.  [Timeout while waiting for handshake or handshake errors](#orgdb73584)
7.  [Examples](#orgd2fc6f3)
8.  [Change Log](#org4db70a2)
    1.  [v5.1.0 (Prod Version)](#orgf9e5cba)
    2.  [v5.0.2](#org1796dfa)
    3.  [v5.0.1](#org11a716f)
    4.  [v5.0.0](#org3396e38)
    5.  [v4.3.1](#orge516713)
    6.  [v4.3.0](#org08068f4)
    7.  [v4.2.4](#org6b0ed97)
    8.  [v4.2.3](#org5901d2b)
    9.  [v4.2.2](#orga85648a)
    10. [v4.2.1](#org79f798a)
    11. [v4.2.0](#orga6d09ca)
    12. [v4.1.0](#org55e63f1)
    13. [v4.0.4](#org870fd9d)
    14. [v4.0.3](#orgb33f01a)
    15. [v4.0.2](#org5c8207d)
    16. [v4.0.0](#org1f8b53d)
    17. [v2.5.2](#org13c6577)
    18. [v2.5.1](#orgf75b2ca)
    19. [v2.5.0](#org90608a8)
    20. [v2.4.3](#org4a870ff)
    21. [v2.4.2](#org342c035)
    22. [v2.4.1](#org25bfc37)
    23. [v2.4.0](#org0c3a1c8)
    24. [v2.3.0](#orge414f27)
    25. [v3.0.0 &#x2013; deprecate this version](#orgdeb3139)
    26. [v2.1.1](#org4c2f2e1)
    27. [v2.0.1](#org5bef3c0)
    28. [v1.1.0](#orgb6295c5)
    29. [v1.0.5:](#org8b6eec6)
9.  [Troubleshooting](#org5896aff)
    1.  [Debugging Support](#orgce55b2b)
10. [Logging Issues](#org113adc6)
11. [Pull Requests](#org8a1b46e)
12. [Contributors](#org81f752d)


<a id="org33d0f93"></a>

# SSH2 SFTP Client

an SFTP client for node.js, a wrapper around [SSH2](https://github.com/mscdex/ssh2)  which provides a high level
convenience abstraction as well as a Promise based API.

Documentation on the methods and available options in the underlying modules can
be found on the [SSH2](https://github.com/mscdex/ssh2) and [SSH2-STREAMS](https://github.com/mscdex/ssh2-streams/blob/master/SFTPStream.md)  project pages.

Current stable release is **v5.1.0**.

Code has been tested against Node versions 10.18.1, 12.14.1 and 13.6.0

Node versions < 10.x are not supported.


<a id="orgc20d50c"></a>

# Installation

    npm install ssh2-sftp-client


<a id="org3e9d405"></a>

# Basic Usage

    let Client = require('ssh2-sftp-client');
    let sftp = new Client();

    sftp.connect({
      host: '127.0.0.1',
      port: '8080',
      username: 'username',
      password: '******'
    }).then(() => {
      return sftp.list('/pathname');
    }).then(data => {
      console.log(data, 'the data info');
    }).catch(err => {
      console.log(err, 'catch error');
    });


<a id="org4c37418"></a>

# Version 5.x


<a id="org91a1101"></a>

## Breaking Changes in Version 5.x

-   The auxList() method has been removed. This method was flagged as deprecated
    in version 4.x. The functionality provided by `auxList()` is available in
    `list()`, making `auxList()` unnecessary.
-   The realPath() method now returns `''` if the path does not exist
    rather than throwing an exception.
-   Improved error handling. The `ssh2` and `ssh2-streams` libraries use events
    to signal errors. Providing a clean Promise based API and managing these
    events can be challenging as an error event can fire at any time (including
    in-between the resolution of one promise and the commencement of another). As
    you cannot use `try/catch` blocks to reliably manage error events (for a
    similar reason - see Node's event documentation for details), a slightly
    more complex solution was required. See the section below on Error Event
    Handling for more details. In basic terms, a default handler is now used
    that will log the error and clear the SFTP connection if no Promise error
    handler has handled the error. This prevents the uncaughtException error
    and provides a reasonably clean way to deal with unexpected errors that
    fire in-between Promise execution activities.
-   Ignore Errors during `end()` processing. At least one SFTP server (Azure
    SFTP) seems to generate an error in response to the `end()` call. As
    `end()` has been called, we don't really care if an error occurs provided
    the connection is closed. Therefore, a new default error listener for the
    `end()` method has been added that will simply ignore any errors which
    occur during a call to end the connection.


<a id="org76baac9"></a>

### Error Event Handling

Providing a clean Promise API for the SSH2 to manage basic SFTP functionality
presents a couple of challenges for managing errors. The `SSH2` module uses
events to communicate various state changes and error conditions. These
events can fire at any time.

On the client side, we wrap basic SFTP actions in Javascript Promises,
allowing clients to use either the standard Promise API or async/await to
model SFTP interactions. Creating an SFTP connection returns a promise, which
resolves if a connection is successfully established and is rejected
otherwise. Downloading a file using `get()` or `fastGet()` generates a new
Promise which is either resolved, indicating file has been successfully
downloaded or rejected, indicating the download failed. All pretty
straight-forward.

When the Promise is created, an error event handler is added to the SFTP
object to catch any errors that fire during the execution of the promise. If
an error event fires, the Promise is rejected and the error returned to the
client as part of the rejection. After the Promise has resolved or rejected,
the error listener is removed (the error listener is specific to each promise
because it needs to call the reject method associated with that promise). As
a promise can only be resolved or rejected once, after the Promise has
completed, the error listener is of no further use.

This all works fine when an error event fires during the execution of a
Promise. However, what about outside promise execution? Consider the
following flow;

1.  You have an active SFTP connection which you use to download a file
2.  When you make the download request, a new Promise is created which will
    resolve when the file is downloaded or be rejected if the download fails
    for some reason. The promise resolves successfully.
3.  You start processing the data downloaded. At this point, you still have an
    open connection to the SFTP server, but you are not actively interacting
    with it. There is no active Promise in play.
4.  The remote SFTP server resets the connection for some reason, generating a
    ECONNRESET error that is emitted as an error event.

What happens at this point? There is no active promise executing, so no
Promise specific error handler in play. Your script is off processing the
data from the previously downloaded file, so there is no currently executing
try/catch block around the SFTP client object. Basically, there is nothing
listening of any errors at this point. What will happen?

Well, basically, the error event will bubble up to the top level of the node
process context and cause an uncaughtException error, display the error and
dump a stack trace and cause the node process to exit. In basic terms, your
process will crash. Not a great outcome.

There are a number of things we can do to improve the situation. However,
nearly all of them have some drawbacks. We could -

-   Add our own error handler. The `client.on()` method would allow you to add
    your own error handler. This would provide a way to manage error events,
    but you want to make sure you only handle error events not handled already
    by the Promise error handlers. Worse yet, you cannot know before hand the
    processing context of your script at the point the error event fires. This
    means your error handling is likely to be complex and difficult to
    manage. Worse yet, these types of errors are quite rare in most situations
    and your now being required to add significant additional complexity to
    deal with a rare edge case. However, sometimes, you just need to deal with
    this sort of complexity and the `client.on()` method does give you that
    option.
-   Another alternative is to just add an uncaughtException handler to your Node
    process object. This would also prevent node from dumping the error and
    exiting abruptly. However, now you need to think about ALL the possible
    uncaughtExceptions which might happen, not just those associated with the
    SFTP client. Again, things are getting complicated for something which only
    occurs occasionally.  .

What we really want is a solution which will be simple for the majority of
clients, but provide additional power when needed. What we have done is add a
default error handler which will only take action if no Promise error handler
has fired. All the default error handler does is log the error to
console.error() and set the SFTP connection to undefined so that any further
attempts to use the connection will throw an error inside the Promise which
attempts to use it.

The advantage of this approach is that it stops the abrupt exiting of the
node script due to an uncaught exception error and provides a reasonable
outcome for most use cases. For example, in the scenario outlined above, if
an error event fires while your script is processing the data already
downloaded, it will not impact on your script immediately. An error will be
logged to console.error(), but your script will continue to run. Once you
have completed processing your data, if you attempt another SFTP call, it
will fail with an error about no available SFTP connections. As this will
occur within the context of interacting with the SFTP server, your script can
take appropriate action to resolve the issue (such as re-connecting to the
server). On the other hand, if after processing the file your done and just
want to end, then you can just ignore the error, perform any necessary
cleanup work and exit successfully.


<a id="org3ff08be"></a>

### Technical Details

The event handlers added by each Promise are added using the `prependListener()`
function. This ensures the handler is fired before any other error handlers
which may be defined. As part of the processing, these error handler set a flag
property `this.errorHandled` to true, indicating the error has been handled.

In addition to the Promise error handlers, there is a default error handler
which will fire after any Promise error handler. The default error handler looks
to see if the `this.errorHandler` flag is true. If it is, it knows the error has
been handled and it just resets it to false, taking no other action (so taht we
are ready for the next error). If the flag is false, the default handler knows
it must handle the error. In this case, the handler will log the error to
`console.error()`, will set the SFTP connection to undefined to prevent any
further attempts to use it and finally, ensure the `this.errorHandler` flag is
reset to false in preparation for the next error.


<a id="org2229bf1"></a>

## New Methods

-   Added the method uploadDir(). This method will upload a directory
    (including any subdirectories) to the remote server. Only directories and
    regular files are uploaded (no symbolic links, FIFOs, socket FDs etc). Will
    overwrite existing files or directories, but will not delete any remote
    files or directories.
-   Added the method downloadDir(). this method will download a directory
    (including any subdirectories) to the local file system. Only directories
    and regular files are downloaded (no symbolic links, FIFOs, socket FDs
    etc).. Will overwrite existing files or directories, but will not delete
    any local files in the directories.


<a id="org881495f"></a>

## Version 5.0.1

-   The error checking was a little too stringent. The use of exist() to test
    for file types had a problem when the user does not have read/execute
    rights on the directory. Replaced with stat() method, which should avoid
    this issue.


<a id="org85c7e53"></a>

## Version 5.0.2

-   Fix error in local directory tests due to missing await statement.
-   Fix path handling under win32. Paths were not being parsed correctly due to
    the use of path.posix.parse() instead of path.parse().


<a id="orgb53d154"></a>

## Version 5.1.0

-   Add missing connection check in end() method
-   Add debugging support. Now adding a debug property to the connection
    configuration object will enable debugging. The value of the debug property
    should be a function which accepts a single string argument. Typically,
    this function will send the value passed in to stderr or a file.
-   Fix bug in checkRemotePath() relating to poor path specifications where
    you cannot determine parent directory.


<a id="org1af5e1c"></a>

# Documentation

The connection options are the same as those offered by the underlying SSH2
module. For full details, please see [SSH2 client methods](https://github.com/mscdex/ssh2#user-content-client-methods)

All the methods will return a Promise, except for `on()` and
`removeListener()`, which are typically only used in special use cases.


<a id="org01473a1"></a>

## Specifying Paths

All remote paths must either be absolute e.g. `/absolute/path/to/file` or they
can be relative with a prefix of either `./` (relative to current remote
directory) or `../` (relative to parent of current remote directory) e.g.
`./relative/path/to/file` or `../relative/to/parent/file`. It is also possible
to do things like `../../../file` to specify the parent of the parent of the
parent of the current remote directory. The shell tilde (`~`) and common
environment variables like `$HOME` are NOT supported.

It is important to recognise that the current remote directory may not always be
what you may expect. A lot will depend on the remote platform of the SFTP server
and how the SFTP server has been configured. When things don't seem to be
working as expected, it is often a good idea to verify your assumptions
regarding the remote directory and remote paths. One way to do this is to login
using a command line program like `sftp` or `lftp`.

There is a small performance hit for using `./` and `../` as the module must
query the remote server to determine what the root path is and derive the
absolute path. Using absolute paths are therefore more efficient and likely more
robust.

When specifying file paths, ensure to include a full path i.e. include the
remote filename. Don't expect the module to append the local file name to the
path you provide. For example, the following will not work

    client.put('/home/fred/test.txt', '/remote/dir');

will not result in the file `test.txt` being copied to
`/remote/dir/test.txt`. You need to specify the target filename as well e.g.

    client.put('/home/fred/test.txt', '/remote/dir/test.txt');

Note that the remote file name does not have to be the same as the local file
name. The following works fine;

    client.put('/home/fred/test.txt', '/remote/dir/test-copy.txt');

This will copy the local file `test.txt` to the remote file `test-copy.txt` in
the directory `/remote/dir`.


<a id="org36e00e2"></a>

## Methods


<a id="orgafb6a2c"></a>

### new SftpClient(name) ===> SFTP client object

Constructor to create a new `ssh2-sftp-client` object. An optional `name` string
can be provided, which will be used in error messages to help identify which
client has thrown the error.

1.  Constructor Arguments

    -   **name:** string. An optional name string used in error messages

2.  Example Use

        'use strict';

        const Client = require('ssh2-sftp-client');

        const config = {
          host: 'example.com',
          username: 'donald',
          password: 'my-secret'
        };

        const sftp = new Client('example-client');

        sftp.connect(config)
          .then(() => {
            return sftp.cwd();
          })
          .then(p => {
            console.log(`Remote working directory is ${p}`);
            return sftp.end();
          })
          .catch(err => {
            console.log(`Error: ${err.message}`); // error message will include 'example-client'
          });


<a id="org85957be"></a>

### connect(config) ===> SFTPstream

Connect to an sftp server. Full documentation for connection options is
available [here](https://github.com/mscdex/ssh2#user-content-client-methods)

1.  Connection Options

    This module is based on the excellent [SSH2](https://github.com/mscdex/ssh2#client) module. That module is a general SSH2
    client and server library and provides much more functionality than just SFTP
    connectivity. Many of the connect options provided by that module are less
    relevant for SFTP connections. It is recommended you keep the config options to
    the minimum needed and stick to the options listed in the `commonOpts` below.

    The `retries`, `retry_factor` and `retry_minTimeout` options are not part of the
    SSH2 module. These are part of the configuration for the [retry](https://www.npmjs.com/package/retry) package and what
    is used to enable retrying of sftp connection attempts. See the documentation
    for that package for an explanation of these values.

        // common options

        let commonOpts {
          host: 'localhost', // string Hostname or IP of server.
          port: 22, // Port number of the server.
          forceIPv4: false, // boolean (optional) Only connect via IPv4 address
          forceIPv6: false, // boolean (optional) Only connect via IPv6 address
          username: 'donald', // string Username for authentication.
          password: 'borsch', // string Password for password-based user authentication
          agent: process.env.SSH_AGENT, // string - Path to ssh-agent's UNIX socket
          privateKey: fs.readFileSync('/path/to/key'), // Buffer or string that contains
          passphrase; 'a pass phrase', // string - For an encrypted private key
          readyTimeout: 20000, // integer How long (in ms) to wait for the SSH handshake
          strictVendor: true // boolean - Performs a strict server vendor check
          debug: myDebug // function - Set this to a function that receives a single
                        // string argument to get detailed (local) debug information.
          retries: 2 // integer. Number of times to retry connecting
          retry_factor: 2 // integer. Time factor used to calculate time between retries
          retry_minTimeout: 2000 // integer. Minimum timeout between attempts
        };

        // rarely used options

        let advancedOpts {
          localAddress,
          localPort,
          hostHash,
          hostVerifier,
          agentForward,
          localHostname,
          localUsername,
          tryKeyboard,
          authHandler,
          keepaliveInterval,
          keepaliveCountMax,
          sock,
          algorithms,
          compress
        };

2.  Example Use

        sftp.connect({
          host: example.com,
          port: 22,
          username: 'donald',
          password: 'youarefired'
        });


<a id="org2356be0"></a>

### list(path, pattern) ==> Array[object]

Retrieves a directory listing. This method returns a Promise, which once
realised, returns an array of objects representing items in the remote
directory.

-   **path:** {String} Remote directory path
-   **pattern:** (optional) {string|RegExp} A pattern used to filter the items included in the returned
    array. Pattern can be a simple *glob*-style string or a regular
    expression. Defaults to `/.*/`.

1.  Example Use

        const Client = require('ssh2-sftp-client');

        const config = {
          host: 'example.com',
          port: 22,
          username: 'red-don',
          password: 'my-secret'
        };

        let sftp = new Client;

        sftp.connect(config)
          .then(() => {
            return sftp.list('/path/to/remote/dir');
          })
          .then(data => {
            console.log(data);
          })
          .then(() => {
            sftp.end();
          })
          .catch(err => {
            console.error(err.message);
          });

2.  Return Objects

    The objects in the array returned by `list()` have the following properties;

        {
          type: // file type(-, d, l)
          name: // file name
          size: // file size
          modifyTime: // file timestamp of modified time
          accessTime: // file timestamp of access time
          rights: {
            user:
            group:
            other:
          },
          owner: // user ID
          group: // group ID
        }

3.  Pattern Filter

    The filter options can be a regular expression (most powerful option) or a
    simple *glob*-like string where \* will match any number of characters, e.g.

        foo* => foo, foobar, foobaz
        *bar => bar, foobar, tabbar
        *oo* => foo, foobar, look, book

    The *glob*-style matching is very simple. In most cases, you are best off using
    a real regular expression which will allow you to do more powerful matching and
    anchor matches to the beginning/end of the string etc.


<a id="orgaeaea56"></a>

### exists(path) ==> boolean

Tests to see if remote file or directory exists. Returns type of remote object
if it exists or false if it does not.

1.  Example Use

        const Client = require('ssh2-sftp-client');

        const config = {
          host: 'example.com',
          port: 22,
          username: 'red-don',
          password: 'my-secret'
        };

        let sftp = new Client;

        sftp.connect(config)
          .then(() => {
            return sftp.exists('/path/to/remote/dir');
          })
          .then(data => {
            console.log(data);          // will be false or d, -, l (dir, file or link)
          })
          .then(() => {
            sftp.end();
          })
          .catch(err => {
            console.error(err.message);
          });


<a id="org66b81b4"></a>

### stat(path) ==> object

Returns the attributes associated with the object pointed to by `path`.

-   **path:** String. Remote path to directory or file on remote server

1.  Attributes

    The `stat()` method returns an object with the following properties;

        let stats = {
          mode: 33279, // integer representing type and permissions
          uid: 1000, // user ID
          gid: 985, // group ID
          size: 5, // file size
          accessTime: 1566868566000, // Last access time. milliseconds
          modifyTime: 1566868566000, // last modify time. milliseconds
          isDirectory: false, // true if object is a directory
          isFile: true, // true if object is a file
          isBlockDevice: false, // true if object is a block device
          isCharacterDevice: false, // true if object is a character device
          isSymbolicLink: false, // true if object is a symbolic link
          isFIFO: false, // true if object is a FIFO
          isSocket: false // true if object is a socket
        };

2.  Example Use

        let client = new Client();

        client.connect(config)
          .then(() => {
            return client.stat('/path/to/remote/file');
          })
          .then(data => {
            // do something with data
          })
          .then(() => {
            client.end();
          })
          .catch(err => {
            console.error(err.message);
          });


<a id="org864073b"></a>

### get(path, dst, options) ==> String|Stream|Buffer

Retrieve a file from a remote SFTP server. The `dst` argument defines the
destination and can be either a string, a stream object or undefined. If it is a
string, it is interpreted as the path to a location on the local file system
(path should include the file name). If it is a stream object, the remote data
is passed to it via a call to pipe(). If `dst` is undefined, the method will put
the data into a buffer and return that buffer when the Promise is resolved. If
`dst` is defined, it is returned when the Promise is resolved.

In general, if your going to pass in a string as the destination, you are
better off using the `fastGet()` method.

-   **path:** String. Path to the remote file to download
-   **dst:** String|Stream. Destination for the data. If a string, it
    should be a local file path.
-   **options:** Options for the `get()` command (see below).

1.  Options

    The options object can be used to pass options to the underlying readStream used
    to read the data from the remote server.

        {
          flags: 'r',
          encoding: null,
          handle: null,
          mode: 0o666,
          autoClose: true
        }

    Most of the time, you won't want to use any options. Sometimes, it may be useful
    to set the encoding. For example, to 'utf-8'. However, it is important not to do
    this for binary files to avoid data corruption.

2.  Example Use

        let client = new Client();

        let remotePath = '/remote/server/path/file.txt';
        let dst = fs.createWriteStream('/local/file/path/copy.txt');

        client.connect(config)
          .then(() => {
            return client.get(remotePath, dst);
          })
          .then(() => {
            client.end();
          })
          .catch(err => {
            console.error(err.message);
          });

    -   **Tip:** See examples file in the Git repository for more examples. You can pass
        any writeable stream in as the destination. For example, if you pass in
        `zlib.createGunzip()` writeable stream, you can both download and
        decompress a gzip file 'on the fly'.


<a id="orge6f2fdc"></a>

### fastGet(remotePath, localPath, options) ===> string

Downloads a file at remotePath to localPath using parallel reads for faster
throughput. This is the simplest method if you just want to download a file.

-   **remotePath:** String. Path to the remote file to download
-   **localPath:** String. Path on local file system for the downloaded file. The
    local path should include the filename to use for saving the
    file.
-   **options:** Options for `fastGet()` (see below)

1.  Options

        {
          concurrency: 64, // integer. Number of concurrent reads to use
          chunkSize: 32768, // integer. Size of each read in bytes
          step: function(total_transferred, chunk, total) // callback called each time a
                                                          // chunk is transferred
        }

    -   **Warning:** Some servers do not respond correctly to requests to alter chunk
        size. This can result in lost or corrupted data.

2.  Sample Use

        let client = new Client();
        let remotePath = '/server/path/file.txt';
        let localPath = '/local/path/file.txt';

        client.connect(config)
          .then(() => {
            client.fastGet(remotePath, localPath);
          })
          .then(() => {
            client.end();
          })
          .catch(err => {
            console.error(err.message);
          });


<a id="org4694c5b"></a>

### put(src, remotePath, options) ==> string

Upload data from local system to remote server. If the `src` argument is a
string, it is interpreted as a local file path to be used for the data to
transfer. If the `src` argument is a buffer, the contents of the buffer are
copied to the remote file and if it is a readable stream, the contents of that
stream are piped to the `remotePath` on the server.

-   **src:** string | buffer | readable stream. Data source for data to copy to the
    remote server.
-   **remotePath:** string. Path to the remote file to be created on the server.
-   **options:** object. Options which can be passed to adjust the write stream used
    in sending the data to the remote server (see below).

1.  Options

    The following options are supported;

        {
          flags: 'w',  // w - write and a - append
          encoding: null, // use null for binary files
          mode: 0o666, // mode to use for created file (rwx)
          autoClose: true // automatically close the write stream when finished
        }

    The most common options to use are mode and encoding. The values shown above are
    the defaults. You do not have to set encoding to utf-8 for text files, null is
    fine for all file types. However, using utf-8 encoding for binary files will
    often result in data corruption.

2.  Example Use

        let client = new Client();

        let data = fs.createReadStream('/path/to/local/file.txt');
        let remote = '/path/to/remote/file.txt';

        client.connect(config)
          .then(() => {
            return client.put(data, remote);
          })
          .then(() => {
            return client.end();
          })
          .catch(err => {
            console.error(err.message);
          });

    -   **Tip:** If the src argument is a path string, consider just using `fastPut()`.


<a id="org443b620"></a>

### fastPut(localPath, remotePath, options) ==> string

Uploads the data in file at `localPath` to a new file on remote server at
`remotePath` using concurrency. The options object allows tweaking of the fast put process.

-   **localPath:** string. Path to local file to upload
-   **remotePath:** string. Path to remote file to create
-   **options:** object. Options passed to createWriteStream (see below)

1.  Options

        {
          concurrency: 64, // integer. Number of concurrent reads
          chunkSize: 32768, // integer. Size of each read in bytes
          mode: 0o755, // mixed. Integer or string representing the file mode to set
          step: function(total_transferred, chunk, total) // function. Called every time
          // a part of a file was transferred
        }

    -   **Warning:** There have been reports that some SFTP servers will not honour
        requests for non-default chunk sizes. This can result in data loss
        or corruption.

2.  Example Use

        let localFile = '/path/to/file.txt';
        let remoteFile = '/path/to/remote/file.txt';
        let client = new Client();

        client.connect(config)
          .then(() => {
            client.fastPut(localFile, remoteFile);
          })
          .then(() => {
            client.end();
          })
          .catch(err => {
            console.error(err.message);
          });


<a id="orgedc4474"></a>

### append(input, remotePath, options) ==> string

Append the `input` data to an existing remote file. There is no integrity
checking performed apart from normal writeStream checks. This function simply
opens a writeStream on the remote file in append mode and writes the data passed
in to the file.

-   **input:** buffer | readStream. Data to append to remote file
-   **remotePath:** string. Path to remote file
-   **options:** object. Options to pass to writeStream (see below)

1.  Options

    The following options are supported;

        {
          flags: 'a',  // w - write and a - append
          encoding: null, // use null for binary files
          mode: 0o666, // mode to use for created file (rwx)
          autoClose: true // automatically close the write stream when finished
        }

    The most common options to use are mode and encoding. The values shown above are
    the defaults. You do not have to set encoding to utf-8 for text files, null is
    fine for all file types. Generally, I would not attempt to append binary files.

2.  Example Use

        let remotePath = '/path/to/remote/file.txt';
        let client = new Client();

        client.connect(config)
          .then(() => {
            return client.append(Buffer.from('Hello world'), remotePath);
          })
          .then(() => {
            return client.end();
          })
          .catch(err => {
            console.error(err.message);
          });


<a id="org2cdb895"></a>

### mkdir(path, recursive) ==> string

Create a new directory. If the recursive flag is set to true, the method will
create any directories in the path which do not already exist. Recursive flag
defaults to false.

-   **path:** string. Path to remote directory to create
-   **recursive:** boolean. If true, create any missing directories in the path as
    well

1.  Example Use

        let remoteDir = '/path/to/new/dir';
        let client = new Client();

        client.connect(config)
          .then(() => {
            return client.mkdir(remoteDir, true);
          })
          .then(() => {
            return client.end();
          })
          .catch(err => {
            console.error(err.message);
          });


<a id="orgc015cd5"></a>

### rmdir(path, recursive) ==> string

Remove a directory. If removing a directory and recursive flag is set to
`true`, the specified directory and all sub-directories and files will be
deleted. If set to false and the directory has sub-directories or files, the
action will fail.

-   **path:** string. Path to remote directory
-   **recursive:** boolean. If true, remove all files and directories in target
    directory. Defaults to false

1.  Example Use

        let remoteDir = '/path/to/remote/dir';
        let client = new Client();

        client.connect(config)
          .then(() => {
            return client.rmdir(remoteDir, true);
          })
          .then(() => {
            return client.end();
          })
          .catch(err => {
            console.error(err.message);
          });


<a id="orge6ba100"></a>

### delete(path) ==> string

Delete a file on the remote server.

-   **path:** string. Path to remote file to be deleted.

1.  Example Use

        let remoteFile = '/path/to/remote/file.txt';
        let client = new Client();

        client.connect(config)
          .then(() => {
            return client.delete(remoteFile);
          })
          .then(() => {
            return client.end();
          })
          .catch(err => {
            console.error(err.message);
          });


<a id="org8f8e4d3"></a>

### rename(fromPath, toPath) ==> string

Rename a file or directory from `fromPath` to `toPath`. You must have the
necessary permissions to modify the remote file.

1.  Example Use

        let from = '/remote/path/to/old.txt';
        let to = '/remote/path/to/new.txt';
        let client = new Client();

        client.connect(config)
          .then(() => {
            return client.rename(from, to);
          })
          .then(() => {
            return client.end();
          })
          .catch(err => {
            console.error(err.message);
          });


<a id="orgbd9261a"></a>

### chmod(path, mode) ==> string

Change the mode (read, write or execute permissions) of a remote file or
directory.

-   **path:** string. Path to the remote file or directory
-   **mode:** octal. New mode to set for the remote file or directory

1.  Example Use

        let path = '/path/to/remote/file.txt';
        let ndwMode = 0o644;  // rw-r-r
        let client = new Client();

        client.connect(config)
          .then(() => {
            return client.chmod(path, newMode);
          })
          .then(() => {
            return client.end();
          })
          .catch(err => {
            console.error(err.message);
          });


<a id="org62857a5"></a>

### realPath(path) ===> string

Converts a relative path to an absolute path on the remote server. This method
is mainly used internally to resolve remote path names. Returns '' if the
path is not valid.

-   **path:** A file path, either relative or absolute. Can handle '.' and '..', but
    does not expand '~'.


<a id="org7430bff"></a>

### cwd() ==> string

Returns what the server believes is the current remote working directory.


<a id="orga18dd1e"></a>

### uploadDir(srcDir, dstDir) ==> string

Upload the directory specified by `srcDir` to the remote directory specified by
`dstDir`. The `dstDir` will be created if necessary. Any sub directories within
`srcDir` will also be uploaded. Any existing files in the remote path will be
overwritten.

The upload process also emits 'upload' events. These events are fired for each
successfully uploaded file. The `upload` event calls listeners with 1 argument,
an object which has properties source and destination. The source property is
the path of the file uploaded and the destination property is the path to where
the file was uploaded to. The purpose of this event is to provide some way for
client code to get feedback on the upload progress. You can add your own lisener
using the `on()` method.

-   **srcDir:** A local file path specified as a string
-   **dstDir:** A remote file path specified as a string

1.  Example

            'use strict';

            // Example of using the uploadDir() method to upload a directory
            // to a remote SFTP server

            const path = require('path');
            const SftpClient = require('../src/index');

            const dotenvPath = path.join(__dirname, '..', '.env');
            require('dotenv').config({path: dotenvPath});

            const config = {
        host: process.env.SFTP_SERVER,
        username: process.env.SFTP_USER,
        password: process.env.SFTP_PASSWORD,
        port: process.env.SFTP_PORT || 22
            };

            async function main() {
        const client = new SftpClient('upload-test');
        const src = path.join(__dirname, '..', 'test', 'testData', 'upload-src');
        const dst = '/home/tim/upload-test';

        try {
          await client.connect(config);
          client.on('upload', info => {
            console.log(`Listener: Uploaded ${info.source}`);
          });
          let rslt = await client.uploadDir(src, dst);
          return rslt;
        } finally {
          client.end();
        }
            }

            main()
        .then(msg => {
          console.log(msg);
        })
        .catch(err => {
          console.log(`main error: ${err.message}`);
        });


<a id="org8c8d706"></a>

### downloadDir(srcDir, dstDir) ==> string

Download the remote directory specified by `srcDir` to the local file system
directory specified by `dstDir`. The `dstDir` directory will be created if
required. All sub directories within `srcDir` will also be copied. Any existing
files in the local path will be overwritten. No files in the local path will be
deleted.

The method also emites `download` events to provide a way to monitor download
progress. The download event listener is called with one argument, an object
with two properties, source and destination. The source property is the path to
the remote file that has been downloaded and the destination is the local path
to where the file was downloaded to. You can add a listener for this event using
the `on()` method.

-   **srcDir:** A remote file path specified as a string
-   **dstDir:** A local file path specified as a string

1.  Example

        'use strict';

        // Example of using the downloadDir() method to upload a directory
        // to a remote SFTP server

        const path = require('path');
        const SftpClient = require('../src/index');

        const dotenvPath = path.join(__dirname, '..', '.env');
        require('dotenv').config({path: dotenvPath});

        const config = {
          host: process.env.SFTP_SERVER,
          username: process.env.SFTP_USER,
          password: process.env.SFTP_PASSWORD,
          port: process.env.SFTP_PORT || 22
        };

        async function main() {
          const client = new SftpClient('upload-test');
          const dst = '/tmp';
          const src = '/home/tim/upload-test';

          try {
            await client.connect(config);
            client.on('download', info => {
        console.log(`Listener: Download ${info.source}`);
            });
            let rslt = await client.downloadDir(src, dst);
            return rslt;
          } finally {
            client.end();
          }
        }

        main()
          .then(msg => {
            console.log(msg);
          })
          .catch(err => {
            console.log(`main error: ${err.message}`);
          });


<a id="org1c3c249"></a>

### end() ==> boolean

Ends the current client session, releasing the client socket and associated
resources. This function also removes all listeners associated with the client.

1.  Example Use

        let client = new Client();

        client.connect(config)
          .then(() => {
            // do some sftp stuff
          })
          .then(() => {
            return client.end();
          })
          .catch(err => {
            console.error(err.message);
          });


<a id="org557e2b9"></a>

### Add and Remove Listeners

Although normally not required, you can add and remove custom listeners on the
ssh2 client object. This object supports a number of events, but only a few of
them have any meaning in the context of SFTP. These are

-   **error:** An error occurred. Calls listener with an error argument.
-   **end:** The socket has been disconnected. No argument.
-   **close:** The socket was closed. Boolean argument which is true when the socket
    was closed due to errors.

1.  on(eventType, listener)

    Adds the specified listener to the specified event type. It the event type is
    `error`, the listener should accept 1 argument, which will be an Error object. If
    the event type is `close`, the listener should accept one argument of a boolean
    type, which will be true when the client connection was closed due to errors.

2.  removeListener(eventType, listener)

    Removes the specified listener from the event specified in eventType. Note that
    the `end()` method automatically removes all listeners from the client object.


<a id="orgb3077fd"></a>

# FAQ


<a id="org971e5b5"></a>

## Remote server drops connections with only an end event

Many SFTP servers have rate limiting protection which will drop connections once
a limit has been reached. In particular, openSSH has the setting `MaxStartups`,
which can be a tuple of the form `max:drop:full` where `max` is the maximum
allowed unauthenticated connections, `drop` is a percentage value which
specifies percentage of connections to be dropped once `max` connections has
been reached and `full` is the number of connections at which point all
subsequent connections will be dropped. e.g. `10:30:60` means allow up to 10
unauthenticated connections after which drop 30% of connection attempts until
reaching 60 unauthenticated connections, at which time, drop all attempts.

Clients first make an unauthenticated connection to the SFTP server to begin
negotiation of protocol settings (cipher, authentication method etc). If you are
creating multiple connections in a script, it is easy to exceed the limit,
resulting in some connections being dropped. As SSH2 only raises an 'end' event
for these dropped connections, no error is detected. The `ssh2-sftp-client` now
listens for `end` events during the connection process and if one is detected,
will reject the connection promise.

One way to avoid this type of issue is to add a delay between connection
attempts. It does not need to be a very long delay - just sufficient to permit
the previous connection to be authenticated. In fact, the default setting for
openSSH is `10:30:60`, so you really just need to have enough delay to ensure
that the 1st connection has completed authentication before the 11th connection
is attempted.


<a id="org10ba954"></a>

## How can you pass writable stream as dst for get method?

If the dst argument passed to the get method is a writeable stream, the remote
file will be piped into that writeable. If the writeable you pass in is a
writeable stream created with `fs.createWriteStream()`, the data will be written
to the file specified in the constructor call to `createWriteStream()`.

The writeable stream can be any type of write stream. For example, the below code
will convert all the characters in the remote file to upper case before it is
saved to the local file system. This could just as easily be something like a
gunzip stream from `zlib`, enabling you to decompress remote zipped files as you
bring them across before saving to local file system.

    'use strict';

    // Example of using a writeable with get to retrieve a file.
    // This code will read the remote file, convert all characters to upper case
    // and then save it to a local file

    const Client = require('../src/index.js');
    const path = require('path');
    const fs = require('fs');
    const through = require('through2');

    const config = {
      host: 'arch-vbox',
      port: 22,
      username: 'tim',
      password: 'xxxx'
    };

    const sftp = new Client();
    const remoteDir = '/home/tim/testServer';

    function toupper() {
      return through(function(buf, enc, next) {
        next(null, buf.toString().toUpperCase());
      });
    }

    sftp
      .connect(config)
      .then(() => {
        return sftp.list(remoteDir);
      })
      .then(data => {
        // list of files in testServer
        console.dir(data);
        let remoteFile = path.join(remoteDir, 'test.txt');
        let upperWtr = toupper();
        let fileWtr = fs.createWriteStream(path.join(__dirname, 'loud-text.txt'));
        upperWtr.pipe(fileWtr);
        return sftp.get(remoteFile, upperWtr);
      })
      .then(() => {
        return sftp.end();
      })
      .catch(err => {
        console.error(err.message);
      });


<a id="org4c14fa9"></a>

## How can I upload files without having to specify a password?

There are a couple of ways to do this. Essentially, you want to setup SSH keys
and use these for authentication to the remote server.

One solution, provided by @KalleVuorjoki is to use the SSH agent
process. **Note**: SSH<sub>AUTH</sub><sub>SOCK</sub> is normally created by your OS when you load the
ssh-agent as part of the login session.

    let sftp = new Client();
    sftp.connect({
      host: 'YOUR-HOST',
      port: 'YOUR-PORT',
      username: 'YOUR-USERNAME',
      agent: process.env.SSH_AUTH_SOCK
    }).then(() => {
      sftp.fastPut(/* ... */)
    }

Another alternative is to just pass in the SSH key directly as part of the
configuration.

    let sftp = new Client();
    sftp.connect({
      host: 'YOUR-HOST',
      port: 'YOUR-PORT',
      username: 'YOUR-USERNAME',
      privateKey: fs.readFileSync('/path/to/ssh/key')
    }).then(() => {
      sftp.fastPut(/* ... */)
    }


<a id="orgc6aac39"></a>

## How can I connect through a Socks Proxy

This solution was provided by @jmorino.

    import { SocksClient } from 'socks';
    import SFTPClient from 'ssh2-sftp-client';

    const host = 'my-sftp-server.net';
    const port = 22; // default SSH/SFTP port on remote server

    // connect to SOCKS 5 proxy
    const { socket } = await SocksClient.createConnection({
      proxy: {
        host: 'my.proxy', // proxy hostname
        port: 1080, // proxy port
        type: 5, // for SOCKS v5
      },
      command: 'connect',
      destination: { host, port } // the remote SFTP server
    });

    const client = new SFTPClient();
    client.connect({
      host,
      sock: socket, // pass the socket to proxy here (see ssh2 doc)
      username: '.....',
      privateKey: '.....'
    })

    // client is connected


<a id="orgdb73584"></a>

## Timeout while waiting for handshake or handshake errors

Some users have encountered the error 'Timeout while waiting for handshake' or
'Handshake failed, no matching client->server ciphers. This is often due to the
client not having the correct configuration for the transport layer algorithms
used by ssh2. One of the connect options provided by the ssh2 module is
`algorithm`, which is an object that allows you to explicitly set the key
exchange, ciphers, hmac and compression algorithms as well as server
host key used to establish the initial secure connection. See the SSH2
documentation for details. Getting these parameters correct usually resolves the
issue.


<a id="orgd2fc6f3"></a>

# Examples

I have started collecting example scripts in the example directory of the
repository. These are mainly scripts I have put together in order to investigate
issues or provide samples for users. They are not robust, lack adequate error
handling and may contain errors. However, I think they are still useful for
helping developers see how the module and API can be used.


<a id="org4db70a2"></a>

# Change Log


<a id="orgf9e5cba"></a>

## v5.1.0 (Prod Version)

-   Fix bug in checkRemotePath() relating to handling of badly
    specified paths (issue #213)
-   Added additional debugging support
-   Add missing test for valid connection in end() method.


<a id="org1796dfa"></a>

## v5.0.2

-   Fix bugs related to win32 platform and local tests for valid directories
-   Fix problem with parsing of file paths


<a id="org11a716f"></a>

## v5.0.1

-   Turn down error checking to be less stringent and handle situations
    where user does not have read permission on parent directory.


<a id="org3396e38"></a>

## v5.0.0

-   Added two new methods `uploadDir()` and `downloadDir()`
-   Removed deprecated `auxList()` method
-   Improved error message consistency
-   Added additional error checking to enable more accurate and useful error
    messages.
-   Added default error handler to deal with event errors which fire outside of
    active SftpClient methods (i.e. connection unexpectedly reset by remote host).
-   Modified event handlers to ensure that only event handlers added by the
    module are removed by the module (users now responsible for removing any
    custom event handlers they add).
-   Module error handlers added using `prependListener` to ensure they are
    called before any additional custom handlers added by client code.
-   Any error events fired during an `end()` call are now ignored.


<a id="orge516713"></a>

## v4.3.1

-   Updated end() method to resolve once close event fires
-   Added errorListener to error event in each promise to catch error events
    and reject the promise. This should resolve the issue of some error events
    causing uncaughtException erros and causing the process to exit.


<a id="org08068f4"></a>

## v4.3.0

-   Ensure errors include an err.code property and pass through the error code
    from the originating error
-   Change tests for error type to use `error.code` instead of matching on
    `error.message`.


<a id="org6b0ed97"></a>

## v4.2.4

-   Bumped ssh2 to v0.8.6
-   Added exists() usage example to examples directory
-   Clarify documentation on get() method


<a id="org5901d2b"></a>

## v4.2.3

-   Fix bug in `exist()` where tests on root directory returned false
-   Minor documentation fixes
-   Clean up mkdir example


<a id="orga85648a"></a>

## v4.2.2

-   Minor documentation fixes
-   Added additional examples in the `example` directory


<a id="org79f798a"></a>

## v4.2.1

-   Remove default close listener. changes in ssh2 API removed the utility of a
    default close listener
-   Fix path handling. Under mixed environments (where client platform and
    server platform were different i.e. one windows the other unix), path
    handling was broken due tot he use of path.join().
-   Ensure error messages include path details. Instead of errors such as "No
    such file" now report "No such file /path/to/missing/file" to help with
    debugging


<a id="orga6d09ca"></a>

## v4.2.0

-   Work-around for SSH2 `end` event bug
-   Added ability to set client name in constructor method
-   Added additional error checking to prevent `connect()` being called on
    already connected client
-   Added additional examples in `example` directory


<a id="org55e63f1"></a>

## v4.1.0

-   move `end()` call to resolve into close hook
-   Prevent `put()` and `get()` from creating empty files in destination when
    unable to read source
-   Expand tests for operations when lacking required permissions
-   Add additional data checks for `append()`
    -   Verify file exists
    -   Verify file is writeable
    -   Verify file is a regular file
-   Fix handling of relative paths
-   Add `realPath()` method
-   Add `cwd()` method


<a id="org870fd9d"></a>

## v4.0.4

-   Minor documentation fix
-   Fix return value from `get()`


<a id="orgb33f01a"></a>

## v4.0.3

-   Fix bug in mkdir() relating to handling of relative paths
-   Modify exists() to always return 'd' if path is '.'


<a id="org5c8207d"></a>

## v4.0.2

-   Fix some minor packaging issues


<a id="org1f8b53d"></a>

## v4.0.0

-   Remove support for node < 8.x
-   Fix connection retry feature
-   sftp connection object set to null when 'end' signal is raised
-   Removed 'connectMethod' argument from connect method.
-   Refined adding/removing of listeners in connect() and end() methods to enable
    errors to be adequately caught and reported.
-   Deprecate auxList() and add pattern/regexp filter option to list()
-   Refactored handling of event signals to provide better feedback to clients
-   Removed pointless 'permissions' property from objects returned by `stat()`
    (same as mode property). Added additional properties describing the type of
    object.
-   Added the `removeListener()` method to compliment the existing `on()` method.


<a id="org13c6577"></a>

## v2.5.2

-   Repository transferred to theophilusx
-   Fix error in package.json pointing to wrong repository


<a id="orgf75b2ca"></a>

## v2.5.1

-   Apply 4 pull requests to address minor issues prior to transfer


<a id="org90608a8"></a>

## v2.5.0

-   ???


<a id="org4a870ff"></a>

## v2.4.3

-   merge #108, #110
    -   fix connect promise if connection ends


<a id="org342c035"></a>

## v2.4.2

-   merge #105
    -   fix windows path


<a id="org25bfc37"></a>

## v2.4.1

-   merge pr #99, #100
    -   bug fix


<a id="org0c3a1c8"></a>

## v2.4.0

-   Requires node.js v7.5.0 or above.
-   merge pr #97, thanks for @theophilusx
    -   Remove emitter.maxListener warnings
    -   Upgraded ssh2 dependency from 0.5.5 to 0.6.1
    -   Enhanced error messages to provide more context and to be more consistent
    -   re-factored test
    -   Added new 'exists' method and re-factored mkdir/rmdir


<a id="orge414f27"></a>

## v2.3.0

-   add: `stat` method
-   add `fastGet` and `fastPut` method.
-   fix: `mkdir` file exists decision logic


<a id="orgdeb3139"></a>

## v3.0.0 &#x2013; deprecate this version

-   change: `sftp.get` will return chunk not stream anymore
-   fix: get readable not emitting data events in node 10.0.0


<a id="org4c2f2e1"></a>

## v2.1.1

-   add: event listener. [doc](https://github.com/jyu213/ssh2-sftp-client#Event)
-   add: `get` or `put` method add extra options [pr#52](https://github.com/jyu213/ssh2-sftp-client/pull/52)


<a id="org5bef3c0"></a>

## v2.0.1

-   add: `chmod` method [pr#33](https://github.com/jyu213/ssh2-sftp-client/pull/33)
-   update: upgrade ssh2 to V0.5.0 [pr#30](https://github.com/jyu213/ssh2-sftp-client/pull/30)
-   fix: get method stream error reject unwork [#22](https://github.com/jyu213/ssh2-sftp-client/issues/22)
-   fix: return Error object on promise rejection [pr#20](https://github.com/jyu213/ssh2-sftp-client/pull/20)


<a id="orgb6295c5"></a>

## v1.1.0

-   fix: add encoding control support for binary stream


<a id="org8b6eec6"></a>

## v1.0.5:

-   fix: multi image upload
-   change: remove `this.client.sftp` to `connect` function


<a id="org5896aff"></a>

# Troubleshooting

The `ssh2-sftp-client` module is essentially a wrapper around the `ssh2` and
`ssh2-streams` modules, providing a higher level `promise` based API. When you
run into issues, it is important to try and determine where the issue lies -
either in the ssh2-sftp-client module or the underlying `ssh2` and
`ssh2-streams` modules. One way to do this is to first identify a minimal
reproducible example which reproduces the issue. Once you have that, try to
replicate the functionality just using the `ssh2` and `ssh2-streams` modules. If
the issue still occurs, then you can be fairly confident it is something related
to those later 2 modules and therefore and issue which should be referred to the
maintainer of that module.

The `ssh2` and `ssh2-streams` modules are very solid, high quality modules with
a large user base. Most of the time, issues with those modules are due to client
misconfiguration. It is therefore very important when trying to diagnose an
issue to also check the documentation for both `ssh2` and `ssh2-streams`. While
these modules have good defaults, the flexibility of the ssh2 protocol means
that not all options are available by default. You may need to tweak the
connection options, ssh2 algorithms and ciphers etc for some remote servers. The
documentation for both the `ssh2` and `ssh2-streams` module is quite
comprehensive and there is lots of valuable information in the issue logs.

If you run into an issue which is not repeatable with just the `ssh2` and
`ssh2-streams` modules, then please log an issue against the `ssh2-sftp-client`
module and I will investigate. Please note the next section on logging issues.

Note also that in the repository there are two useful directories. The first is
the examples directory, which contain some examples of using `ssh2-sftp-client`
to perform common tasks. A few minutes reviewing these examples can provide that
additional bit of detail to help fix any problems you are encountering.

The second directory is the tools directory. I have some very basic simple
scripts in this directory which perform basic tasks using only the `ssh2` and
`ssh2-streams` modules (no ssh2-sftp-client module). These can be useful when
trying to determine if the issue is with the underlying `ssh2` and
`ssh2-streams` modules.


<a id="orgce55b2b"></a>

## Debugging Support

You can add a `debug` property to the config object passed in to `connect()` to
turn on debugging. This will generate quite a lot of output. The value of the
property should be a function which accepts a single string argument. For example;

    config.debug = msg => {
      console.error(msg);
    };

Enabling debugging can generate a lot of output. If you use console.error() as
the output (as in the example above), you can redirect the output to a file
using shell redirection e.g.

    node script.js 2> debug.log


<a id="org113adc6"></a>

# Logging Issues

Please log an issue for all bugs, questions, feature and enhancement
requests. Please ensure you include the module version, node version and
platform.

I am happy to try and help diagnose and fix any issues you encounter while using
the `ssh2-sftp-client` module. However, I will only put in effort if you are
prepared to put in the effort to provide the information necessary to reproduce
the issue. Things which will help

-   Node version you are using
-   Version of ssh2-sftp-client you are using
-   Platform your client is running on (Linux, macOS, Windows)
-   Platform and software for the remote SFTP server when possible
-   Example of your code. By far, the most common issue is incorrect use of the
    module API. Example code can usually result in such issues being resolved very
    quickly.

Perhaps the best assistance is a minimal reproducible example of the issue. Once
the issue can be readily reproduced, it can usually be fixed very quickly.


<a id="org8a1b46e"></a>

# Pull Requests

Pull requests are always welcomed. However, please ensure your changes pass all
tests and if your adding a new feature, that tests for that feature are
included. Likewise, for new features or enhancements, please include any
relevant documentation updates.

This module will adopt a standard semantic versioning policy. Please indicate in
your pull request what level of change it represents i.e.

-   **Major:** Change to API or major change in functionality which will require an
    increase in major version number.
-   **Minor:** Minor change, enhancement or new feature which does not change
    existing API and will not break existing client code.
-   **Bug Fix:** No change to functionality or features. Simple fix of an existing
    bug.


<a id="org81f752d"></a>

# Contributors

This module was initially written by jyu213. On August 23rd, 2019, theophilusx
took over responsibility for maintaining this module. A number of other people
have contributed to this module, but until now, this was not tracked. My
intention is to credit anyone who contributes going forward.

-   **jyu213:** Original author
-   **theophilusx:** Current maintainer
-   **henrytk:** Documentation fix
-   **waldyrious:** Documentation fixes
