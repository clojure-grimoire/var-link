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

### Example Use Cases

 - An artifact's documentation may provide links to its dependencies'
   documentation abstractly via artifact links
 - An artifact's documentation could provide links the documentation
   of its member classes, namespaces and vars using the appropriate
   links.
 - Var documentation could link to the documentation of the directly
   depended upon functions fully qualified for the artifact and
   namespace in which they occur. Imagine browsing a project, clicking
   on a used symbol and being able to jump to its documentation from
   any client without having the artifact you are exploring loaded
   into a Clojure instance!

Class links are included because various structures such as deftype
and defrecord generate Java visible class instances wich should
consequently exist in system documentation and be legitimate link
targets.

As these different case are representation independant and uniquely
declare the entity to which the link points they can be implemented
with little effort by any number of clients who need share no
implementation or representation details.

## Specification

This spec defines four kinds of links: an artifact URI, a namespace
URI, a class URI and a "var" URI. Artifact URIs are simply formatted
Maven artifact identifiers postfixed with enough information to
uniquely identify a class, namespace or var within a given artifact.

### Prior Art

The unofficial Maven standard for URIs seems to be

```
mvn:groupId/artifactId/version/extension/classifer
```

The path structure of var-link URIs should supserset this higherarchy
as it already handles uniquely identifying a Maven artifact, which is
a requirement for identifying artifact versioned entities like vars
and classes.

The term `$artifact` shall be used to refer to the template string
`$groupId/$artifactId/$version/$extension/$classifer`. Note that the
extension and classifier fields may be empty. If the version field is
empty, then the intention is to link to the newest (highest versioned)
release. All other fields in an artifact identifier must be populated.

### Artifact Links

Three URI types are provided: `mvn`, `mvn+doc` and `mvn+src`.

The `mvn` URI may resolve to either documentation or a FTP instance of
the artifact itself as defined the client. `mvn+doc` must resolve to
documentation enumerating all classes, namespaces, symbols and other
public entities in an artifact. `mvn+src` may resolve to any one of a
full source code listing, a FTP source or other host. The critical
point is that it resolves to some listing not to documetation.

#### Examples
```
mvn:org.clojure/clojure/1.6.0///
mvn+doc:org.clojure/clojure/1.6.0///
mvn+src:org.clojure/clojure/1.6.0///
```

### Var Links

Three URI types are provided: `var`, `var+doc` and `var+src`. 

The `var` URI scheme set may map either to documentation or a source
listing as defined by the link handling application. Documentation is
suggested as the default. `var+doc` URIs must resolve to
documentation. `var+src` URIs must resolve to source listings.

Var is a pair `artifact` as specified above and a
`fullyQualifiedSymbol`, being the fully namespace qualified form of a
Clojure symbol URL encoded.

This gives the template `$scheme:$artifact/$fullyQualifiedSymbol`.

#### Examples
```
var:org.clojure/clojure/1.6.0///clojure.core%2Fconj
var+src:org.clojure/clojure/1.6.0///clojure.core%2Fconj
var+doc:org.clojure/clojure/1.6.0///clojure.core%2Fconj
var:org.clojure/clojure////clojure.core%2Fconj
var+doc:org.clojure/clojure////clojure.core%2Fconj
var+src:org.clojure/clojure////clojure.core%2Fconj
```

### Namespace

The URL types `ns`, `ns+doc` and `ns+src` must be provided.

These URI types share behavior with their equivalents in the var link
spec.

#### Examples

```
ns:org.clojure/clojure/1.6.0///clojure.core
ns+doc:org.clojure/clojure/1.6.0///clojure.core
ns+src:org.clojure/clojure/1.6.0///clojure.core
ns:org.clojure/clojure////clojure.core
ns+doc:org.clojure/clojure////clojure.core
ns+src:org.clojure/clojure////clojure.core
```

### Class Links

The URI types `class`, `class+doc`, `class+src`. The behavior of the
`+doc` and `+src` suffixes is shared with Namespaces and Vars.

```
class:org.clojure/clojure/1.6.0///clojure.lang.RT
class+doc:org.clojure/clojure/1.6.0///clojure.lang.RT
class+src:org.clojure/clojure/1.6.0///clojure.lang.RT
class:org.clojure/clojure////clojure.lang.RT
class+doc:org.clojure/clojure////clojure.lang.RT
class+src:org.clojure/clojure////clojure.lang.RT
```

## Known implementations

 - [var-link.clj](http://github.com/clojure-grimoire/var-link.clj), a
   reference Clojure implementation.
 - [var-link.el](http://github.com/clojure-grimoire/var-link.el), an
   emacs browser implementation.

## Legal

This specification is placed in the public domain in the hope that it
will be implemented and prove of use.
