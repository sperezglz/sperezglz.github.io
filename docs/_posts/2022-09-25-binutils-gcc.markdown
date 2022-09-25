---
layout: post
title:  "Building binutils and gcc"
date:   2022-09-25 11:31:01 -0500
categories: compilers 
---

Step 1: Create a folder to keep binutils and gcc source code. \<target\> = CPU arch you want to build for.
{% highlight ruby %}
$ mkdir <target>-build; cd <target>-build
{% endhighlight %}

Step 2: Clone or download binutils and gcc source code.

Step 3: Define the path where the built binaries will be saved.
{% highlight ruby %}
$ export PREFIX=${HOME}/<target>
$ export PATH="${PREFIX}/bin:${PATH}"
{% endhighlight %}

Step 4: Configure and build binutils.
{% highlight ruby %}
$ cd <binutils-source-folder>
$ ./configure --prefix="$PREFIX" --target=<target> --disable-nls --disable-werror
$ make
$ make install
{% endhighlight %}

The --enable-nls option enables Native Language Support (NLS), which lets GCC output diagnostics in languages other than American English. Native Language Support is enabled by default if not doing a canadian cross build. The --disable-nls option disables NLS. [GCC documentation][gcc-configure-docs]

Step 5: Download or build from source gcc dependences. In this case we will just download them.

{% highlight ruby %}
$ cd <gcc-source-folder>
$ ./contrib/download_prerequisites
{% endhighlight %}

Step 6: Build gcc.

{% highlight ruby %}
$ mkdir build-gcc
$ cd build-gcc
$ ../gcc-x.y.z/configure --prefix="$PREFIX" --disable-nls --enable-languages=c,c++
$ make
$ make install
{% endhighlight %}

[gcc-configure-docs]: https://gcc.gnu.org/install/configure.html
