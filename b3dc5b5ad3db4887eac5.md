<!--
title:   Installing ffi 1.15.0 with native extensions Gem::Ext::BuildError
tags:    Rails
id:      b3dc5b5ad3db4887eac5
private: false
-->
急にffiがインストールできなくなるエラー

```
Fetching ffi 1.15.0
Installing ffi 1.15.0 with native extensions
Gem::Ext::BuildError: ERROR: Failed to build gem native extension.

current directory: PATH_TO/vendor/bundle/ruby/2.7.0/gems/ffi-1.15.0/ext/ffi_c
/Users/USER/.rbenv/versions/2.7.1/bin/ruby -I /Users/USER/.rbenv/versions/2.7.1/lib/ruby/2.7.0 -r ./siteconf20210804-11406-5ju1ry.rb extconf.rb
checking for ffi_prep_closure_loc() in -lffi... no
checking for ffi_prep_closure_loc() in -llibffi... no
checking for ffi_prep_closure_loc() in -llibffi-8... no
checking for whether -Wl,--exclude-libs,ALL is accepted as LDFLAGS... no
creating extconf.h
creating Makefile

current directory: PATH_TO/vendor/bundle/ruby/2.7.0/gems/ffi-1.15.0/ext/ffi_c
make "DESTDIR=" clean

current directory: PATH_TO/vendor/bundle/ruby/2.7.0/gems/ffi-1.15.0/ext/ffi_c
make "DESTDIR="
/bin/sh: -c: line 0: unexpected EOF while looking for matching `"'
/bin/sh: -c: line 1: syntax error: unexpected end of file
make: *** [PATH_TO] Error 2

make failed, exit code 2

Gem files will remain installed in PATH_TO/vendor/bundle/ruby/2.7.0/gems/ffi-1.15.0 for inspection.
Results logged to PATH_TO
/vendor/bundle/ruby/2.7.0/extensions/x86_64-darwin-20/2.7.0/ffi-1.15.0/gem_make.out

An error occurred while installing ffi (1.15.0), and Bundler cannot continue.
Make sure that `gem install ffi -v '1.15.0' --source 'https://rubygems.org/'` succeeds before bundling.
```

個別インストール `gem install ffi -v '1.15.0' --source 'https://rubygems.org/'` ではインストールできても `bundle install` ではインストールできない

解決法

```
% xcode-select --install
% bundle install --path vendor/bundle
```