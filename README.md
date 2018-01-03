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
git clone https://github.com/bunnymatic/rails-collection-render-speed-test
cd rails-collection-render-speed-test
bin/setup

rails s

time curl -s http://localhost:3000/things?template=0 > /dev/null
time curl -s http://localhost:3000/things?template=1 > /dev/null
time curl -s http://localhost:3000/things?template=2 > /dev/null

```

Note: `bin/setup` runs `db:seed` which for this test app creates 1000 `Thing` records so that
we at least have something to render.  If you run db:seed again, you'll get another 1000.

Compare your results

### My output for rendering 2000 Things (after running db:seed twice)
```
# rendering partial inside the each block
$ time curl -s http://localhost:3000/things?template=0 > /dev/null

real	0m21.815s
user	0m0.005s
sys	0m0.005s

# with no partial (inlined into the main template)
$ time curl -s http://localhost:3000/things?template=1 > /dev/null

real	0m1.274s
user	0m0.005s
sys	0m0.005s

# using `render partial: partial, collection: @collection`
$ time curl -s http://localhost:3000/things?template=2 > /dev/null

real	0m0.834s
user	0m0.005s
sys	0m0.005s

```
