WebIDL to Bikeshed
=========

This is the pipeline used to convert the WebIDL spec to Bikeshed.

It is kept in its own repository essentially for archival purposes.

You can checkout the [current output][spec] as well as the [generated Bikeshed source file][src].

Conversion pipeline
----------

The conversion is fully automatic.

The [original XML source file][1] is transformed into a tmp file using an [XSLT stylesheet][2] (which is just a modified version of the origin XSLT stylesheet already used to transform the src file into HTML).

It is then turned into the final Bikeshed file by running through [a series of line-by-line JS parsers][3].

This has us 99% covered. The final 1% fixes will be done manually, after this initial review, but as part of the pull request.

The whole conversion pipeline will be discarded after the fact (but retrievable through Git).

Testing
-------

Manual conversion of specs are highly error prone. The pipeline described above avoids a number of issues normally caused by manual transforms, but still needs tests. Especially to ensure that:

### 1) All anchors exposed by the original spec are preserved.

This uses [a small JS script][4].

They are all preserved with the exception of:

* anchors prefixed with `ref-`: those are biblio links.
* anchors prefixed with `proddef-`: those are internal links to the grammar snippets. The `prod-` prefixes have been preserved, however.
* `#appendices`
* `#informative-references`
* `#normative-references`
* `#sections`

This seems good enough.

### 2) The [DFN contract][8] is respected.

This uses [another small JS script][7].

They are all preserved with the exception of:

* `call a user object's operation`
* `get a user object's attribute value`
* `set a user object's attribute value`
* `ArrayBufferView`
* `BufferSource`
* `DOMTimeStamp`
* `Function`
* `VoidFunction`

which will need to be fixed manually post conversion.

### 3) The markdownified version of the spec renders the same HTML as the non-markdownified one.

This is achieved by building both versions of the spec. Munging their output [using a dedicated script][5] and diff'ing them.

The results are, here also, good enough. (There are a few negligible difference that do not affect the normative content of the spec.) 

Running the conversion build
----------------------------

Running the conversion is rather painful. You need to manually edit some Bikeshed data files (remove the last line of `/spec-data/link-defaults.infotree`), install a Java XSLT processor (for real), node.js, etc.

If you did all of the above, all you need to do is run:

```
$ make 
```
Of course, as soon as we merge this into the main repo (and @tabatkins and I modify the Bikeshed data file accordingly), the only thing you'll need to do to build the spec is to run Bikeshed.

Outstanding todos and issues
----------------------------

I'm tracking a bunch of issues on [my GitHub fork][6]. Please report things I missed there.

Note the grammar snippets rely on a small JS script for styling and linking until a BNF parser is built into Bikeshed.

[spec]: https://rawgit.com/tobie/webidl/bikeshed/index.html
[src]: https://github.com/tobie/webidl/blob/bikeshed/index.bs
[1]: https://github.com/tobie/webidl/blob/bikeshed/index.xml
[2]: ./WebIDL-bs.xsl
[3]: ./post-process
[4]: ./check-anchors.js
[5]: ./check-structure.js
[6]: https://github.com/tobie/webidl/issues
[7]: ./check-dfn-contract.js
[8]: https://github.com/tabatkins/bikeshed/blob/master/docs/dfn-contract.md
