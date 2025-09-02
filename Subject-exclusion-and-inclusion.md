It is possible to exclude certain subjects from a vocabulary in project configuration. This can be useful for example to drop problematic concepts that are often wrongly suggested or to define specialized projects that only operate on a subset of subjects, for example only place names or persons. Excluding a subject means that it is not used during training of projects and it will never be suggested, as if the whole concept didn't exist in the vocabulary.

Exclusion and inclusion of subjects is done in project configuration by adding keyword arguments to the `vocab` setting. Here is a simple example that excludes two STW Thesaurus subjects (USA and Theory) that are often wrongly suggested:

```
vocab=stw(en,exclude=http://zbw.eu/stw/descriptor/19073-6|http://zbw.eu/stw/descriptor/17829-1)
```

Abbreviated URIs (CURIEs) may also be used as long as the namespace prefix is known to Annif, for example if the vocabulary was loaded from a SKOS file in Turtle or RDF/XML format that defines the prefixes. Some common prefixes (e.g. `skos`, `rdf`, `rdfs`, `owl`) are also supported internally (as defined by rdflib).

The following keywords are supported:

* `exclude=<uri>` will exclude a specific concept (can be many, separated by `|`)
* `exclude=*` will exclude all concepts (useful before setting include rules)
* `exclude_type=<type>` excludes all concepts of a given RDF type (can be many, separated by `|`)
* `exclude_scheme=<scheme>` excludes all concepts of a given SKOS concept scheme (can be many, separated by `|`)
* `exclude_collection=<collection>` excludes all concepts that are members of a given SKOS collection (can be many, separated by `|`)
* `include_type`, `include_scheme` and `include_collection` work the same way, but in the other direction (i.e. dropping entries from the list of excluded concepts, which means including them after they have first been excluded by another rule)

The starting point when processing these rules is that no concepts are excluded. The given rules are then applied in the order they are specified.

More examples of exclude/include rules:

```
# YSO, but without concepts of type yso-meta:Hierarchy (that shouldn't be used for subject indexing)
vocab=yso(exclude_type=yso-meta:Hierarchy)

# YSO Places only
vocab=yso(exclude=*,include_scheme=yso:places)

# YSO, but only concepts in the Mathematics or Physics concept groups / collections
vocab=yso(exclude=*,include_collection=yso:p26574|yso:p26565)

# STW Thesaurus, but without concepts of type zbwext:Thsys
vocab=stw(exclude_type=zbwext:Thsys)
```
