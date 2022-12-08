# mulle-objc-cc

⏩ make mulle-clang the default Objective-C compiler

This project introduces a build setting for `COBJC`, which will affect all
dependencies afterwards. When you add this as a dependency, ensure that
this dependency does not get set to `no-singlephase`.


## mulle-clang support

There is also `clang.musl` and `mulle-clang.musl`. These files attempt to
do that `musl-gcc` does but for the clang compiler. But they always force
static compilation and linking, whereas `gcc.musl` will also work in
shared library scenarios.


## Add mulle-objc-cc to your mulle-sde project

``` sh
mulle-sde add --marks no-header,no-link --github mulle-cc mulle-objc-cc
```


> #### Note
>
> This project does not build the mulle-clang compiler. You should install
> the compiler manually.

