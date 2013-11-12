# mate-desktop.org

This repository contains the [Nikola](http://getnikola.com) configuration and
content for <http://mate-desktop.org>.

# Contributing content

If you want to add or edit content on <http://mate-desktop.org> then please
submit a [pull-request](https://help.github.com/articles/using-pull-requests).

## Contributing a new post

Contributing a new blog post to the mate-desktop.org website is simple.

  * [Fork](https://help.github.com/articles/fork-a-repo) this repository.
  
  * Copy `blog/20991231markdown-template.md` to a new file, for example:

    cp blog/20991231template-markdown.md blog/20131107my-cool-blog-post.md

  * Edit your new file making sure to change the meta data in the file header. The meta data fields are:
    * `link` is optional but should include the URL to the original article if there is one.
    * `description` is optional an should brefily describe the post.
    * `tags` any tags that are relevant. You **must include the `draft` tag**
    * `date` is the intended publication date of the post in the format YYYY/MM/DD HH:MML:SS
    * `title` is the post title
    * `slug` is how the post name will be represented in the URL. Mark sure you **modify date the date prefix**.
    * `author` is your full name.

  * Commit you changes, [submit a pull-request](https://help.github.com/articles/creating-a-pull-request)
  and one of the website maintainers will review your submission and
  publish it if it is suitable.

If you prefer ReStructuredText to Markdown, then follow the same process
as above but use `blog/20991231rest-template.rst` template file.

### Embedding images

Markdown and reStructured Test both have markup for embedding images. To embed
an image just drop your correctly sized image into the `files/assets/img/blog/`
directory and then link to it. When linking to an image assets you drop the
`'files` prefix from the Markdown and reStructured Text markup.

#### Markdown image example

    ![MATE](/assets/mate-128.png)

#### reStructured Text image example

    .. image:: /assets/mate-128.png
        :align: center

### Submitting posts for future publication

The `date` field in the meta data also controls when a post will be published. 
If you have created a post that should be published at a specific date or time 
the set the date/time accordingly and that post will not be published until that
time.

The mate-desktop.org website is redeployed every 10 minutes, so the actual
publication time will be accurate to the nearest 10 minutes.

### Preventing a post from being published

If you have a post your are working on but do not wish to publish just add
**draft** to the list of `tags` in the meta data. Posts tagged as *draft* will
not be published.

## Contributing a translation

To contribute a translated page or blog post the mate-desktop.org website do the
following:

  * [Fork](https://help.github.com/articles/fork-a-repo) this repository.

  * Copy the blog post or page you wish to translate to a new file with the same
  filename but ending with the short country code (see below). For example, if
  you want to translate the home page to German you would do the following:

    cp pages/index.md pages/index.md.de

  * Translate the meta data as well as the content. However, **do not change the date format**.

  * Commit your changes, [submit a pull-request](https://help.github.com/articles/creating-a-pull-request)
  and one of the website maintainers will review your submission.

Nikola supported languages are, the one in bold are already in the site navigation::

  * `bg`     Bulgarian
  * `ca`     Catalan
  * `de`     **German**
  * `el`     Greek
  * `en`     **English**
  * `eo`     Esperanto
  * `es`     **Spanish**
  * `fa`     Persian
  * `fr`     **French**
  * `hr`     Croatian
  * `it`     **Italian**
  * `jp`     Japanese
  * `nl`     **Dutch**
  * `pt_br`  Portuguese (Brasil)
  * `pl`     Polish
  * `ru`     Russian
  * `tr_tr`  **Turkish** (Turkey)
  * `zh_cn`  Chinese (Simplified)

## Markdown vs. ReStructured Text

mate-desktop.org converts Markdown or reStructured Text into HTML. In general we
recommend Markdown, but some of the Nikola advanced features are only exposed
via reStructured Text extension.

### Markdown

Nikola follows the [syntax rules](http://daringfireball.net/projects/markdown/syntax)
of the original `markdown.pl` with the following
[extensions](http://pythonhosted.org/Markdown/extensions/index.html)
enabled:

  * `extra`
  * `codehilite`
  * `toc`

### reStructured Text

See the Nikola [reStructuredText Primer](http://getnikola.com/quickstart.html) and
the [reStructured Text extensions](http://getnikola.com/handbook.html#restructuredtext-extensions).

# The migration

For the benefit of the MATE developers and admins, here is a run down of how
the Wordpress site was migrated to Nikola.

## Python 2.7

Nikola is being powered by Python 2.7 and some additional packages were required
on the server.

    sudo apt-get install python2.7-dev libfreetype6-dev libjpeg8-dev libxslt1-dev libxml2-dev libyaml-dev

### What are these requirements for?

  * `python2.7-dev` provides the header files for Python 2.7 so that Python modules 
  with C extensions can be built.

The following are required to build `pillow`, the Python imaging library.

  * `libjpeg8-dev`
  * `libfreetype6-dev`

The following are required to build `lxml`, a Python XML library.

  * `libxml2-dev`
  * `libxslt1-dev`

The following are required to build `python-coveralls`.

  * `libyaml-dev`

### virtualenv

[`virtualenv`](http://www.virtualenv.org/en/latest/) is used to create a sandboxed Python
environments, so you don't pollute the system Python. This is how a the `virtualenv` for
Nikola was created.

    cd ~
    curl -O https://pypi.python.org/packages/source/v/virtualenv/virtualenv-1.10.1.tar.gz
    tar xvfz virtualenv-1.10.1.tar.gz
    cd virtualenv-1.10.1
    sudo python setup.py install

### Install Nikola 6.2.1

Create an environment for Nikola.

    mkdir ~/PythonEnvs
    virtualenv -p python2.7 ~/PythonEnvs/nikola-621

Activate the environment.

    source ~/PythonEnvs/nikola-621/bin/activate

Download Nikola 6.2.1

    mkdir -p ${VIRTUAL_ENV}/src
    cd ${VIRTUAL_ENV}/src
    wget https://github.com/getnikola/nikola/archive/v6.2.1.tar.gz -O nikola-621.tar.gz
    tar zxvf nikola-621.tar.gz
    cd nikola-6.2.1

Install the Nikola requirements.

    pip install cython
    pip install -r requirements-full.txt

Actually install nikola.

    python setup.py install

## Migrate Wordpress to Nikola

Export the Wordpress content.

  * `Tools -> Export -> All Content`

Make sure the Nikola environment is activated.

    source ~/PythonEnvs/nikola-621/bin/activate

Clone the mate-desktop.org Git repository.

    cd ~/Websites
    git clone https://github.com/mate-desktop/mate-desktop.org

Run the migration.

    nikola import_wordpress ~/Downloads/mate.wordpress.2013-10-23.xml ~/Migration

The only interesting log entries form the migration were:

    [2013-10-23T15:26:02Z] WARNING: import_wordpress: Not going to import "Wiki" because it seems to contain no content.
    [2013-10-23T15:26:02Z] WARNING: import_wordpress: Not going to import "Forum" because it seems to contain no content.

`rsync` the migrated files to the git repository.

    rsync -av ~/Migration/ ~/Websites/mate-desktop.org/

Install [html2text](https://github.com/aaronsw/html2text).

    pip install https://github.com/aaronsw/html2text/archive/master.zip

Convert the Wordpress `.wp` faux HTML files to [Markdown](http://daringfireball.net/projects/markdown/)
and correct the image links.

    cd ~/Websites/mate-desktop.org/posts
    for FILE in *.wp; do html2text ${FILE} > `basename ${FILE} .wp`.md; done
    for FILE in *.md; do sed -i 's/\/martin\/Migration//g' ${FILE}; done
    rm *.wp
    cd ~/Websites/mate-desktop.org/stories
    for FILE in *.wp; do html2text ${FILE} > `basename ${FILE} .wp`.md; done
    for FILE in *.md; do sed -i 's/\/martin\/Migration//g' ${FILE}; done
    rm *.wp

The meta data and content were merged using `scripts/merge_meta.sh`.

# The clean up

At this point you have a basic Nikola site that preserves all the 
content (but not the comments) from the Wordpress site. Now the
hacking begins...

# Building and deploying the site

When the site cleanup is complete, Nikola will be integrated with
Git so that any new pushes to the mate-desktop.org Git repository
will automatically trigger a Nikola build and deploy.

In the meantime, here is how Nikola can be run manually. This
assumes you are running as me on the server however.

    source ~/PythonEnvs/nikola-621/bin/activate
    cd ~/Websites/mate-desktop.org
    nikola build
    nikola deploy

# TODO

  * Add more text the homepage.
    * Floating MATE logo
    * All the applications, with improved layout.
  * Integrate SocialSharePrivacy
    * https://github.com/panzi/SocialSharePrivacy
