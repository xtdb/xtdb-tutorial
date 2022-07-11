# XTDB Tutorial

Official XTDB "Space Adventure" tutorial

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
1. Click "+ NEW" to create a new notebook on the [Nextjournal Dashboard](https://nextjournal.com/dashboard).
1. At the bottom of the page, choose "Import from a URL (e.g. GitHub)" and paste in the link to the file in GitHub (e.g. `https://github.com/xtdb/xtdb-tutorial/blob/main/1-getting-started.nextjournal.md`)
1. You do not need to name the `deps.edn` block -- the metadata names it for you.

You can now run the tutorial notebook.

## Reinstallation (for maintainers)

To reinstall the tutorial: Treat this GitHub repository as the golden store. Make edits to the Markdown file in
GitHub, then reimport the notebooks using the following instructions.

1. Log in as the `xtdb-tutorial` Nextjournal user (ask @deobald or @refset for creds, stored via `pass` `juxt.xtdb.ops/password-store` on Keybase)
2. Go to the [Nextjournal Dashboard](https://nextjournal.com/dashboard) and open notebook you want to edit.
3. Click anywhere in the document, select all (ctrl+a) and delete the contents.
4. At the bottom of the page, choose "Import from a URL (e.g. GitHub)" and paste in the link to the file in GitHub (e.g. `https://github.com/xtdb/xtdb-tutorial/blob/main/1-getting-started.nextjournal.md`)
5. Scroll to the bottom of the page and expand the Appendix. Pull from the repo to ensure the changes are brought in
6. Click "Run All" under the Play icon to evaluate all snippets
7. Select "Publish Changes" in the share dialog
8. Check to make sure the public URLs in the `"Quickstart"` work correctly.
9. If the lessons seem out-of-order, it is because they are listed reverse-chronologically. Whatever lesson you edited, publish lessons _backward_ from that point. For example, if you edited Lesson 4, you must publish #3, #2, and finally #1 (Note if you haven't edited a lesson, you only need to re-publish the old notebook without following the import steps).

TIPS: Publish last (e.g. Tutorial #8) to first so that the notebook listing is always in the correct order, i.e. import/edit backwards from the last notebook. If necessary add a non-obvious space to the end of a paragraph so that you can re-publish an otherwise unchanged notebook. Open tutorials in multiple tabs so that you can process them more efficiently in a pipelined fashion while waiting for "Run All" commands.

## Copyright & License

The MIT License (MIT)

Copyright © 2019-2021 JUXT LTD.

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
