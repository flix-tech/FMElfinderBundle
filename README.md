[ElFinder](/Studio-42/elFinder) integration in Symfony2

elFinder is an open-source file manager for web, written in JavaScript using jQuery UI.
Creation is inspired by simplicity and convenience of Finder program used in Mac OS X operating system.

## Installation

To install this bundle, you'll need both the [ElFinder](/Studio-42/elFinder)
and this bundle.

This instruction explain how to setup bundle on Symfony 2.1 RC

### Step 1: Installation

Add FMElfinderBundle in your deps:

```
[FMElfinderBundle]
    git=git@github.com:EricReiche/FMElfinderBundle.git
    target=/bundles/FM/ElfinderBundle

[FMElfinder]
    git=git@github.com:EricReiche/elFinder.git
    target=/studio-42/elfinder
```

Now download the bundles by running the command:

``` bash
$ bin/vendors install
```

### Step 2: Enable the bundle

Enable the bundle in the kernel:

``` php
<?php
// app/AppKernel.php

public function registerBundles()
{
    $bundles = array(
        // ...
        new FM\ElfinderBundle\FMElfinderBundle(),
    );
}
```
### Step 3: Import FMElfinderBundle routing file

``` yaml
# app/config/routing.yml
elfinder:
     resource: "@FMElfinderBundle/Resources/config/routing.yml"
```

### Step 4: Configure your application's security.yml

Secure ElFinder with access_control:
``` yaml
# app/config/security.yml
security:

    //....
    access_control:
        - { path: ^/efconnect$, role: ROLE_USER }
        - { path: ^/elfinder, role: ROLE_USER }

```
### Step 5: Configure assetic
Under assetic section of your config.yml
``` yaml
assetic:
    debug:          %kernel.debug%
    use_controller: false
```

### Step 6: Install and dump assets

Install and dump assets using symfony built-in commands:

```sh
app/console assets:install web
```

### Basic configuration

## Add configuration options to your config.yml

fm_elfinder:
    path: uploads
    driver: LocalFileSystem
    locale: %locale%
    editor: simple
    showhidden: false

path option - define root directory for the files inside web/ directory, default is "uploads". Make sure to set
proper write/read permissions to this directory.
driver - can be LocalFileSystem, FTP or MySQL2, currently supported only LocalFileSystem, default is LocalFileSystem
locale - locale determines, which language, ElFinder will use, to translate user interface, default is en_US.UTF8
editor - determines what template to render, to be compatible with WYSIWYG web editor, currently supported options are:
 "ckeditor" and "simple". How to configure CKEDitor to work with this bundle, will be explained further in this document.
 "Simple" can be used as standalone filebrowser for managing and uploading files.
showhidden - hides directories starting with . (dot)

### Using ElFinder with CKEditor

Mostly filebrowsers used with WYSIWYG editors to upload images and other files. The example above will show how to
configure CKEditor to work with ElFinder through [FMElFinderBundle]

## Step 1: Define class or id for the textarea inside your template:

```jinja
{# example.html.twig #}
////
<textarea id="ck" required="required" name="ckeditor"></textarea>

////
```

## Step 2: Place ElFinder URL into attribute "browser-url"

```jinja
{# example.html.twig #}
////
<textarea id="ck" required="required" name="ckeditor" browser-url="{{path('elfinder')}}"></textarea>

////
```

## Step 3: Add necessary javascript files with ckeditor library, and add configuration/initialisation script inside your
template:
```jinja
{# example.html.twig #}
////
<script type="text/javascript" charset="utf-8">
{
    var action = $('textarea').attr('browser-url');
    var config = {
            toolbar : 'Full',
            uiColor : 'White',
            filebrowserBrowseUrl : action+'?mode=file',
            filebrowserImageBrowseUrl : action+'?mode=image',
            filebrowserFlashBrowseUrl : action+'?mode=flash',
            filebrowserImageWindowWidth : '950',
            filebrowserImageWindowHeight : '520',
            filebrowserWindowWidth : '950',
            filebrowserWindowHeight : '520',

    };
    ];
    $('textarea#ckeditor').ckeditor(config);
})

////
```

After that, you can use "Browse on server" ability that can be found inside insert image or link dialogs.