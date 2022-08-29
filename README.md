# Overview

YERML stands for YAML-like Entity Relationship Modeling Language.

This language is envisioned to help folks, particularly technology architects, document & abstractify thoughts, observations, and designs.

The core language focuses on supporting "is a" and "has a" relationships that may or may not have contraints.

This is a bit airy, so let's look at some use cases...

# Use Cases

1. A software architect may use this to document classes and interactions, similar to UML, and eventually use a future app to generate graphs.
2. A firmware architect may use this to document a sensor's registers or parameters as a middle layer to auto generate firmware drivers.
3. A data scientist may use this to document data relationships e.g. data lineage.

# Why?

The overall goal is to decouple architecture description from visualizations. This ideally enables architects to quickly update models via a few key strokes and have downstream processes (like visualizers) follow changes automatically.

# How does this work?

1. Create a blank text file with your favorite text editor and save the file with a ".yerml" extension.
2. Follow the grammar rules as described below, reference the basic example below, or reference spec.yerml in this repo.
3. If you use VS Code, take advantage of the syntax highlighting extension.

## Key Features

### Entity Definition (EntDef)

Just about every line in a YERML file is a variation of one pattern:

`indentation``subject`:`descriptors`

This pattern is called an `Entity Definition` (shorted to `EntDef` in this document) and its composition is described below.

#### Indentation

`EntDef`s may have zero or more spaces preceding it. The relative spacing indicates the `role` an `EntDef` has w.r.t. the `EntDef`(s) preceding it.

The two `role`s are `Subject` and `Object` (just like in basic English sentence structure). And naturally, `Subject`s have `relationship`s with `Object`s.

`EntDef`s starting with 0 spaces are deemed `root` definitions. They are always in the `Subject` `role`. In practice, these will often be the core subjects of a YERML document (e.g. the main characters of a movie).

`EntDef`s starting with 1 or more spaces **relative** to the `EntDef` preceding it are in the `Object` `role`. Therefore, this `EntDef` "has a" `relationship` with the `EntDef` preceding it.

`EntDef`s starting with 0 spaces **relative** to the `EntDef` preceding it has no `relationship`, explicit or implied, with that entity. However, together (and perhaps with many others) they all will have their own, *independent* `relationship` with a preceding `Subject` `EntDef`.

YERML doesn't explicitly define what a `relationship` is. That definition is currently left to the file's context, author's communicated intent, and/or audience interpretation. For example, a common `relationship` is when an Object (w.r.t. Object Oriented Programming) "has a" attribute.

As shown in `spec.yerml` it's possible to define entities in a nested manner (akin to "in-line"). This file could have been rewritten into separate `root` definitions. However, as the author, I chose the nested manner because I wanted to emphasize the "composition" pattern being described. I don't have a general recommendation either way. It's left up to the author and their audience to pick a format to optimize readability and comprehension for their particular context.

In practice, I recommend using spaces over tabs. Regardless, both are supported in the VS Code extension for now.

#### Subject

The entity definition `subject` (not to be confused with the `Subject` `role` as described above) can be either an `Entity Name` or `Entity Type`.

##### Entity Names

`Entity Name`s are unique-to-the-peer-`subject` identifiers. 

Their regex is `_*[a-z]\\w*`. Importantly, they always start with a lower case letter or any number of underscores. They DO NOT start with upper case letters in order to prevent confusion with `Entity Type`s.

`EntDef`s whose `subject`s are `Entity Name`s imply that all subsequent `EntDef`s (properly indented) belong to this specific entity **instance** and NOT this entity's `Entity Type` unless this instance's `Entity Type` is included in the descriptors section.

##### Entity Types

`Entity Type`s are globally unique identifiers.

Their regex is `[A-Z]\\w*`. Importantly, they always start with an upper case letter. They DO NOT start with lower case letters or underscores in order to prevent confusion with Entity Names.

`EntDef`s whose subjects are `Entity Type`s imply that all subsequent `EntDef`s (properly indented) belong to this `Entity Type` and all entity instances that are this `Entity Type` or inherit from it.

#### Descriptors

The core YERML specification provides 3 kinds of descriptors, which describe various aspects of the entity or its relationship to its "peer".

None of the descriptors are required. Absence of some descriptors imply default values or meanings. 

The order in which descriptors are listed does not matter, but I personally like `Count` `EntityType` `Constraints`.

Finally, I prefer to delimit the descriptors by spaces to improve readability, but it's not strictly necessary to do so since their grammars are sufficiently unique.

See below for details.
##### Count

The Count descriptor specifies the `relationship`'s quantity & scale with the `Subject` `EntDef`.

There can **ONLY** be 0 or 1 Count descriptors per `EntDef`.

The regex for Count is `[*+-]|\\d+[+-]?|\\d+-\\d+`.

The Count has three forms: single quantity, open ended, and range.

When the Count form is single quantity, it means that the `Subject` `EntDef` has the same `relationship` with *exactly* the given quantity of **instances** of the `Object` `EntDef`.

When the Count form is open ended, it means that the `Subject` `EntDef` has the same `relationship` with some quantity, to infinity or to zero, of **instances** of the `Object` `EntDef`.

When the Count form is range, it means that the `Subject` `EntDef` has the same `relationship` with some range of quantities, inclusive, of **instances** of the `Object` `EntDef`.

When Count does not have a quantity, it is assumed to be `1` unless Count is `*`.

See the following concrete examples.
###### Count Examples

`3` means "exactly 3"
`2+` means "2 or more"
`5-` means "5 or less"
`-5` is **invalid**, not supported
`10-12` means "between 10 to 12, inclusively" (i.e. could be 10, 11, or 12 instances)
`-` means "1 or less" (useful perhaps for denoting optional attributes)
`+` means "1 or more"
`*` means "0 or more"

##### Entity Type (with single inheritance)

To Be Improved:

Akin to programming variable types and the ability for objects to inherit other objects. Only allows for single inheritance.

##### Constraints

To Be Improved:

Entities can have values. These values can be constrained. Typical numerical constraints apply. Can also have "always equals" (i.e. constant), "default equals", and "just equals" (arbitrary equals perhaps).

### Imports

To Be Improved:

Other YERML files can be imported, more or less copying their contents directly in line with the import statement. Helps keep files small & reusable.

### Comments

To Be Improved:

Just like comments in programming. Help readers understand what's going on.

### Directives

To Be Improved:

Only one supported and that's the beginning YERML directive to specify the YERML version for future use.

## Example

To Be Improved:

Let's look at an example:

~~~~
PetRescue:
  name: = "Heaven on Merf"
  cats: 12- Cat(Pet)
    has_FIP: _= "no"
  dogs: 20- Dog(Pet)
    is_leash_trained: _= "no"
    
Pet:
  name:
  adoptable: _= "yes"
~~~~

## Known Issues

YERML does not provide a method for authors to explicitly specify relationships between `Subject` and `Object` `EntDef`s.

YERML entity definition constraint values lack boolean support.

## Release Notes

### 0.1

Initial release of YERML.
