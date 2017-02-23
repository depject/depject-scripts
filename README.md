# depject-scripts

apply depject to a script tags.

Javascript modules have just come full circle - originally, we just concatenated script tags,
then we loaded modules with `require()` now depject helps you use script tags again.

[depject](https://github.com/depject/depject) enables you to somewhat specify the intefaces
you need from dependency and describe the interfaces you give to dependants, and is intended
for cases where you _want_ a looser coupling than npm provides - when you want to be able to easily
switch out whole layers of modules.

`depject-scripts` enables you to take anything you would pass to [combine](https://github.com/depject/depject#combine) and add it as a script tag, 
wrapped in a `expose(module)` call.

Note, you must first load `depject-scripts/bundle.js`, and your app must give `app` plug, which
will be called at startup.

## Example

The following works (although in practice, you'll probably load files rather than embed the script tags)

``` js
<script src=depject-scripts/bundle.js></script>
<script>
expose({
  needs: {decorate: 'reduce'},
  gives: 'hello',
  create: function (sockets) {
    return function (name) {
      return sockets.decorate(name)
    }
  }
})
</script>
<script>
expose({
  gives: 'decorate',
  create: function () {
    return function (name) {
      return name.toUpperCase()
    }
  }
})
</script>
<script>
expose({
  gives: 'decorate',
  create: function () {
    return function (name) {
      return 'Hello, ' + name
    }
  }
})
</script>
<script>
// depject-scripts runs the first `app` plug once everything is loaded.
// so this needs to be provided as initial starting point otherwise your
// program won't do anything.

expose({
  gives: 'app',
  needs: {hello: 'first'},
  create: function (api) {
    return function () {
      //add text to page and console
      var s = api.hello('depject')
      document.body.appendChild(document.createTextNode(s))
      console.log(s)
    }
  }
})
</script>
```

## License

MIT

