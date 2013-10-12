@author: Manuel Arwed Schmidt <manschmidt@gmail.com>
@date: 10/12/2013

This package contains all chans needed to run a benchmark of the following
combination: Gwan as HTTP-Server, weighttp as http benchmark, .c-handler to
put load on the database and CouchBase 2.10 as NoSQL DB.

1) Under debian 6, first install the following things: gwan, weighttp, 
couchbase, libcouchbase.

2) Rename the memcached file in /opt/couchbase/bin/ to memcached.bin and put the
changed memcached file of this package in its old place. This will allow cb
to scale further by running memcached with more threads (wrapper script).
You will also have to make it executable: chmod +x /opt/couchbase/bin/memcached


3) Put the couchtest.c handler into gwan's directory. Navigate down the whole
folder structure until you see the example handlers provided in the csp folder
Change the user/password and bucket configuration in couchtest.c-file.

4) Start gwan by using "./gwan -k && ./gwan". It will automatically compile 
couchtest.c and open up the webserver at port 8080. When u run into problems
with undefined things you might need to change global include path of gcc or
link libcouchbase.so into proper location: "ln -sf 
/opt/couchbase/include/lib/libcouchbase.so.2.0.7 /usr/lib/libcouchbase.so".

5) (You might need to use another terminal window for this)
Move into the weighttp folder and use "./waf build" and "./waf install" to 
build the custom benchmark tool. It will use an incrementing variable to make
sure gwan's micro caching does not kick-in. This way, it will always run the
couchbase.c-handler when weighttp sends a request.

6) Have fun by using "./build/default/weighttp -n 9000000 -t 6 -c 50 
"http://127.0.0.1:8080/?couchtest.c&name=-&amount=4444444&rate=3.554&term=3"
to put load on the database. You can watch the couchbase performance in the
web interface provided at http://<<ip-of-your-server>>:8091/.
Change -t for the amount of benchmarking threads and -c for the amount of 
parallel http-connections each thread should use. The couchtest.c handler
will automatically open up new connections to cb when its conn pool runs out 
of them. During testing, it might crash sometimes. Restart ur network then
to clean up unterminated connections: "/etc/init.d/networking restart"


