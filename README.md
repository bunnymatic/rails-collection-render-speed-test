# tl;dr

Where possible inline your partials or use `render partial: partial_name, collection: @collection` for collection rendering

# The long story

Rails can efficiently render a collection into a partial with `render partial: :partial, collection: @collection` 
and it's way way faster.

The following repo shows rendering 1000 `Thing`s (with name and description) in 3 different ways
* using a partial with a typical each block

```slim
  = @things.each { |thing| render :thing, thing: thing } 
```

* with an inline partial
```slim
  - @things.each do |thing|
    p = thing.name
    p = thing.description
```
* with a collection rendering partial
```slim
  = render template: :thing, collection: @things
```

where the second 2 are %1200 faster.


Try it for yourself.

```
git clone <this repo>

bundle install

bin/setup

bundle exec rails s


$ ab -n 2 http://localhost:3000/things
$ ab -n 2 http://localhost:3000/things?template=1
$ ab -n 2 http://localhost:3000/things?template=2
```

Compare your results


### My output
```

$ ab -n 2 http://localhost:3000/things
This is ApacheBench, Version 2.3 <$Revision: 1807734 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking localhost (be patient).....done


Server Software:
Server Hostname:        localhost
Server Port:            3000

Document Path:          /things
Document Length:        773289 bytes

Concurrency Level:      1
Time taken for tests:   24.226 seconds
Complete requests:      2
Failed requests:        0
Total transferred:      1547950 bytes
HTML transferred:       1546578 bytes
Requests per second:    0.08 [#/sec] (mean)
Time per request:       12113.100 [ms] (mean)
Time per request:       12113.100 [ms] (mean, across all concurrent requests)
Transfer rate:          62.40 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.0      0       0
Processing: 12055 12113  81.9  12171   12171
Waiting:    12054 12112  81.9  12170   12170
Total:      12055 12113  81.9  12171   12171

Percentage of the requests served within a certain time (ms)
  50%  12171
  66%  12171
  75%  12171
  80%  12171
  90%  12171
  95%  12171
  98%  12171
  99%  12171
 100%  12171 (longest request)


$ ab -n 2 http://localhost:3000/things?template=1
This is ApacheBench, Version 2.3 <$Revision: 1807734 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking localhost (be patient).....done


Server Software:
Server Hostname:        localhost
Server Port:            3000

Document Path:          /things?template=1
Document Length:        773289 bytes

Concurrency Level:      1
Time taken for tests:   1.813 seconds
Complete requests:      2
Failed requests:        0
Total transferred:      1547948 bytes
HTML transferred:       1546578 bytes
Requests per second:    1.10 [#/sec] (mean)
Time per request:       906.414 [ms] (mean)
Time per request:       906.414 [ms] (mean, across all concurrent requests)
Transfer rate:          833.87 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.0      0       0
Processing:   901  906   8.1    912     912
Waiting:      900  906   8.2    912     912
Total:        901  906   8.1    912     912

Percentage of the requests served within a certain time (ms)
  50%    912
  66%    912
  75%    912
  80%    912
  90%    912
  95%    912
  98%    912
  99%    912
 100%    912 (longest request)



$ ab -n 2 http://localhost:3000/things?template=2
This is ApacheBench, Version 2.3 <$Revision: 1807734 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking localhost (be patient).....done


Server Software:
Server Hostname:        localhost
Server Port:            3000

Document Path:          /things?template=2
Document Length:        544608 bytes

Concurrency Level:      1
Time taken for tests:   1.706 seconds
Complete requests:      2
Failed requests:        0
Total transferred:      1090586 bytes
HTML transferred:       1089216 bytes
Requests per second:    1.17 [#/sec] (mean)
Time per request:       852.817 [ms] (mean)
Time per request:       852.817 [ms] (mean, across all concurrent requests)
Transfer rate:          624.42 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.0      0       0
Processing:   767  853 121.3    938     938
Waiting:      767  852 121.2    938     938
Total:        767  853 121.2    938     938

Percentage of the requests served within a certain time (ms)
  50%    938
  66%    938
  75%    938
  80%    938
  90%    938
  95%    938
  98%    938
  99%    938
 100%    938 (longest request)
```
