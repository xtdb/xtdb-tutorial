# XTDB Tutorial

Official xtdb "Space Adventure" tutorial

## Quickstart

* Read the tutorial: https://nextjournal.com/xtdb-tutorial/
* Try online:
    1. https://nextjournal.com/try/xtdb-tutorial/start
    2. https://nextjournal.com/try/xtdb-tutorial/put
    3. https://nextjournal.com/try/xtdb-tutorial/datalog
    4. https://nextjournal.com/try/xtdb-tutorial/bitemporality
    5. https://nextjournal.com/try/xtdb-tutorial/match
    6. https://nextjournal.com/try/xtdb-tutorial/delete
    7. https://nextjournal.com/try/xtdb-tutorial/evict
    8. https://nextjournal.com/try/xtdb-tutorial/await



## Install Tutorial on Nextjournal

The Quickstart is the easiest way to run the tutorial and you can use the Nextjournal
"Remix" feature to bring the tutorial into your own Nextjournal Clojure notebooks if you
like. However, you can import the Markdown files in this repository directly, if you would
prefer.

1. Create a new account on [Nextjournal](https://nextjournal.com).
2. Download a Markdown file from this repository root.
3. Click "+ NEW" to create a new notebook on the [Nextjournal Dashboard](https://nextjournal.com/dashboard).
4. At the bottom of the page, choose "Import: Select (or drag & drop) a Markdown file to import" and upload the `.md` file you downloaded in Step 2.
Do not use "Import from a URL (e.g. GitHub)" -- it will not work.
5. You do not need to name the `deps.edn` block -- the metadata names it for you.
6. You do not need to configure the Clojure runtime or mounts -- the metadata configures it for you.
Click "Save changes and start" in the dialog that appears at the top.

You can now run the tutorial notebook.

## Reinstallation (for maintainers)

To reinstall the tutorial: Treat this GitHub repository as the golden store. Make edits to the Markdown file in
GitHub, then reimport the notebooks using the following instructions.

1. Log in as the `xtdb-tutorial` Nextjournal user (ask @deobald, @refset, or @johantonelli for creds)
2. Go to the [Nextjournal Dashboard](https://nextjournal.com/dashboard) and open notebook you want to edit.
3. Open the "Share" dialog (in the upper-right):
   1. Select "Notebook Visibility: Private"
   2. Select "Edit Slug" and change the slug to a name like `evict-old-2021-10-26` so it won't collide with the notebook you are about to import.
   3. Select "Published Version: Unpublish this notebook..."
7. Edit the title of the document to `"Evict - Old 2021-10-26"` or something similar so it's easy to identify
in the list of archived notebooks.
8. Refresh the [Nextjournal Dashboard](https://nextjournal.com/dashboard). You should see `"Evict - Old 2021-10-06"` (or similar) in the list. Select "Actions: Archive". (At present, you cannot completely delete a notebook.)
9. Follow the `"Installation"` instructions above, with the following additional steps from the "Share" dialog:
   1. Select "Notebook Visibility: Public"
   2. Select "Edit Slug" and change the slug to the original (`evict`, in the above example)
   3. Select "Publish Changes" to see the preview dialog. Click "Publish".
10. Check to make sure the public URLs in the `"Quickstart"` work correctly.
11. If the lessons seem out-of-order, it is because they are listed reverse-chronologically. Whatever lesson you edited, publish lessons _backward_ from that point. For example, if you edited Lesson 4, you must publish #3, #2, and finally #1 (Note if you haven't edited a lesson, you only need to re-publish the old notebook without following the import steps).


## Copyright & License

The MIT License (MIT)

Copyright © 2019-2021 JUXT LTD.

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
