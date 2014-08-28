# Var-link

This document is a specification for a URL scheme for referencing
symbols in Clojure programs by Maven artifact, version and fully
qualified symbol name.

## Motivation

Frequently when browsing code, writing documentation or using
documentation it is desirable to "jump" to either the definition or
documentation of some symbol either mentioned in plain text or used in
code. In the scope of SLIME or CIDER repls, one acceptable solution is
the "M-." jump to definition which can locate and access loaded source
code thanks to the exernality (with respect to a document) of the
loaded language and REPL. However in general especially for non-REPL
contexts the fact that text can be resolved to bound Clojure symbols
is not an acceptable solution because it fails to cover the general
case of linking to Clojure documentation from the general case where
there is no Clojure environment at hand.

The hope is that this document will serve as the basis for a linking
infrastructure spanning [Grimoire](http://grimoire.arrdem.com),
[CrossCLJ](crossclj.info), and not bound to a single documentation
target so that other Clojure documentations services can provide
support as they will.

## Fully Qualified URLs

Three URL prefixes are proposed: `var`, `var+doc` and `var+src`. These
tokens shall be termed PREFIX.

The `var` URL prefix may map either to documentation or source as
defined by the link handling application. `var+doc` URLs must resolve
to documentation. `var+src` URLs must resolve to source code. These
distinctions are provided so that it is possible to abstractly link to
documentation in addition to source code.

Var link URLs are quads `(groupId, artifactId, version,
fullyQualifiedSymbol)` formatted as
`$prefix://$groupId/$artifactId/$version/$fullyQualifiedSymbol` with
all terms URL encoded to escape conflicts with the `/` delimiter
used. The handling of URLs with empty versions is defined to link to
the latest release. URLs with empty `groupId` or `artifactId` fields
are defined to be in error.

### Reference Implementation

```Clojure
(defn url-encode
  [string]
  (some-> string str (java.net.URLEncoder/encode "UTF-8") (.replace "+" "%20")))

(defn var-link [proto group artifact version symbol]
  {:pre [(#{"var" "var+doc" "var+src"
            :var :var/doc :var/src}
          proto)

         (and (string? group)
              (not (empty? group)))

         (and (string? artifact)
              (not (empty? artifact)))

         (and (string? version)
              (not (empty? version)))

         (or (string? symbol)
            (symbol? symbol))]}
  (let [proto (if-not (string? proto)
                ({:var "var" :var/doc "var+doc" :var/src "var+src"} proto))]
    (str proto "://" (url-encode group)
                "/"  (url-encode artifact)
                "/"  (url-encode version)
                "/"  (url-encode (str symbol)))))
```

### Examples
```
var://org.clojure/clojure//clojure.core%2Fconj
var://org.clojure/clojure/1.6.0/clojure.core%2Fconj
var+doc://org.clojure/clojure//clojure.core%2Fconj
var+doc://org.clojure/clojure/1.6.0/clojure.core%2Fconj
var+src://org.clojure/clojure//clojure.core%2Fconj
var+src://org.clojure/clojure/1.6.0/clojure.core%2Fconj
```

## Known implementations

 - [var-link.el](http://github.com/clojure-grimoire/var-link.el) for Emacs

## Known users

None yet unfortunately.

## Legal

This specification is placed in the public domain in the hope that it
will be implemented and prove of use.
