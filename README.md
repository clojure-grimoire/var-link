# Var-link

This document is a specification for a URI scheme for referencing
symbols in Clojure programs by Maven artifact, version and fully
qualified symbol name.

## Motivation

Frequently when browsing code, writing documentation or using
documentation it is desirable to "jump" to either the definition or
documentation of some symbol either mentioned in plain text or used in
code. In the scope of SLIME or CIDER repls, one acceptable solution is
the "M-." jump to definition which can locate and access loaded source
code thanks to the exernality (with respect to a text document) of the
loaded REPL environment and symbol resolution infrastructure.

However in general there is no standard way to textually represent a
reference either to source listings or documentation of other symbols
either in documentation, comments or tutorials which do not require an
active REPL or other Clojure instance.

The hope is that this document will serve as the basis for a linking
infrastructure spanning [Grimoire](http://grimoire.arrdem.com),
[CrossClj](http://crossclj.info), and not bound to a single documentation
target so that other Clojure documentations services and editors can
provide support as they will.

## Specification

Three URI types are provided: `var`, `var+doc` and `var+src`. These
tokens shall be termed `scheme`.

The `var` URI scheme may map either to documentation or a source
listing as defined by the link handling application. `var+doc` URIs
must resolve to documentation. `var+src` URIs must resolve to source
listings.

Var link URIs have a path, being a quads `(groupId, artifactId,
version, fullyQualifiedSymbol)` formatted as a `/` deliminated list of
URL encoded parts.

This gives the template
`$scheme:$groupId/$artifactId/$version/$fullyQualifiedSymbol`.

The handling of URIs with empty versions is defined to link to the
latest release. URIs with empty `groupId` or `artifactId` fields are
defined to be in error.

### Examples
```
var:org.clojure/clojure//clojure.core%2Fconj
var:org.clojure/clojure/1.6.0/clojure.core%2Fconj
var+doc:org.clojure/clojure//clojure.core%2Fconj
var+doc:org.clojure/clojure/1.6.0/clojure.core%2Fconj
var+src:org.clojure/clojure//clojure.core%2Fconj
var+src:org.clojure/clojure/1.6.0/clojure.core%2Fconj
var://org.clojure/clojure//clojure.core%2Fconj
var://org.clojure/clojure/1.6.0/clojure.core%2Fconj
var+doc://org.clojure/clojure//clojure.core%2Fconj
var+doc://org.clojure/clojure/1.6.0/clojure.core%2Fconj
var+src://org.clojure/clojure//clojure.core%2Fconj
var+src://org.clojure/clojure/1.6.0/clojure.core%2Fconj
```

## Known implementations

 - [var-link.clj](http://github.com/clojure-grimoire/var-link.clj), a
   reference Clojure implementation.
 - [var-link.el](http://github.com/clojure-grimoire/var-link.el), an
   emacs browser implementation.

## Legal

This specification is placed in the public domain in the hope that it
will be implemented and prove of use.
