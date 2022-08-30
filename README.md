# Overview

YERML stands for YAML-like Entity Relationship Modeling Language.

This language is envisioned to help folks, particularly technology architects, document & abstractify thoughts, observations, and designs.

The core language focuses on supporting "is a" and "has a" relationships that may or may not have contraints.

This is a bit airy, so let's look at some use cases...

# Use Cases

- A software architect may use this to document classes and interactions, similar to UML, and eventually use a future app to generate graphs.
- A firmware architect may use this to document a sensor's registers or parameters as a middle layer to auto generate firmware drivers.
- A data scientist may use this to document data relationships e.g. data lineage.

# Why?

The overall goal is to decouple architecture description from visualizations. This ideally enables architects to quickly update models via a few key strokes and have downstream processes (like visualizers) follow changes automatically.

# How does this work?

1. Create a blank text file with your favorite text editor and save the file with a ".yerml" extension.
2. Follow the grammar rules as described below, reference the basic example below, or reference spec.yerml in this repo.
3. If you use VS Code, take advantage of the syntax highlighting extension.

# Key Features

## Entity Definition

Just about every line in a YERML file is a variation of one pattern:

[`indentation`] `entity`: `descriptors`

This pattern is called an `Entity Definition` ("`ED`") and its composition is described below.

### Indentation

`ED`s may have zero or more spaces preceding it. The relative indention difference among `ED`s indicates the role an `ED` has w.r.t. the `ED`(s) preceding it.

The two roles are [`Subject`](https://en.wikipedia.org/wiki/Subject_(grammar)) and [`Object`](https://en.wikipedia.org/wiki/Object_(grammar)), just like English grammar. And naturally, `Subjects` have `Relationships` with `Objects`.

`ED`s starting with 0 spaces are deemed `Root` definitions. They are always `Subjects`. In practice, these will often be the core subjects of a YERML document (e.g. like the main characters of a movie).

An `ED` starting with 1 or more spaces **relative** to the `ED` immediately preceding it is an `Object` of that preceding `ED` and therefore "has a" `Relationship` with it.

An `ED` starting with 0 spaces **relative** to the `ED` immediately preceding it has no `Relationship`, explicit or implied, with that `ED`. Instead, together (and perhaps with many other `ED`s) they all will have their own, *independent* `Relationship` with a preceding `Subject` `ED`.

YERML doesn't explicitly define what a `Relationship` is. That definition is currently left to the file's context, author's communicated intent, and/or audience interpretation. For example, a common `Relationship` is when an object (w.r.t. Object Oriented Programming) "has a" attribute.

As shown in `spec.yerml` it's possible to define entities in a nested format. However, this file could have been written into a flat format with more `Root` definitions. What's the best format?

There isn't one. As the author, I chose the nested format because I wanted to emphasize the [composition pattern](https://en.wikipedia.org/wiki/Composite_pattern) being described. You may prefer the flat format. Regardless, it's left up to the author and their audience to pick a format to optimize readability and comprehension for their particular context.

Regardless of format, I recommend using spaces over tabs for indentation even though both are supported in the VS Code extension for now.

### Entity

The entity definition `entity` can be either an `Entity Name` or `Entity Type`.

#### Entity Names

`Entity Names` are identifiers unique to the `Subject` `ED`.

Their regex pattern is `_*[a-z]\\w*`, which means they always start with a lower case letter or any number of underscores. They **DO NOT** start with upper case letters in order to prevent confusion with `Entity Types`.

`ED`s defined by `Entity Names` imply that all subsequent `ED`s (properly indented) belong to the **instance** of this entity and **NOT** this entity's `Entity Type` *unless* this instance's `Entity Type` is included in the descriptors section.

#### Entity Types

`Entity Types` are identifies unique to the entire file.

Their regex pattern is `[A-Z]\\w*`, which means they always start with an upper case letter. They **DO NOT** start with lower case letters or underscores in order to prevent confusion with `Entity Names`.

`ED`s defined by `Entity Type`s imply that all subsequent `ED`s (properly indented) belong to the `Entity Type` and that all **instances** of this `Entity Type` (direct or inherited from) will have all defined `Relationships` as well.

### Descriptors

The core YERML specification provides 3 kinds of descriptors: `Count`, `Type`, and `Constraints`. These describe various aspects of the `entity` or its relationship to its `Subject` `ED`.

None of the descriptors are required. Absence of some descriptors imply default values or meanings. 

The order in which descriptors are listed does not matter, but I personally like `Count` `Type` `Constraints`.

I prefer to delimit the descriptors by spaces to improve readability, but it's not strictly necessary to do so since their grammars are sufficiently unique.

See below for details.
#### Count

The `Count` descriptor specifies the `Relationship`'s quantity & scale with the `Subject` `ED`.

There can **ONLY** be 0 or 1 `Count` descriptors per `ED`.

The regex pattern for `Count` is `[*+-]|\\d+[+-]?|\\d+-\\d+`.

---

The `Count` has three forms: `Single`, `Open`, and `Range`.

`Single` `Counts` mean that the `Subject` `ED` has the same `Relationship` with *exactly* the given single quantity of **instances** of the `Object` `ED`.

For example, a Cat (`Subject`) has (`Relationship`) 4 (`Single` `Count`) paws (`Object` **instance**):

~~~~
Cat:
  paws: 4
~~~~

`Open` `Counts` mean that the `Subject` `ED` has the same `Relationship` with a given quantity, either to infinity or to zero inclusively, of **instances** of the `Object` `ED`.

For example, a Clowder (`Subject`) has (`Relationship`) 3 or more (`Open` `Count`) Cats (`Object`):

~~~~
Clowder:
  Cat: 3+
~~~~

`Range` `Counts` mean that the `Subject` `ED` has the same `Relationship` with a given range of quantities, inclusive, of **instances** of the `Object` `ED`.

For example, a Litter (`Subject`) has (`Relationship`) 1 to 12 inclusive (`Range` `Count`) kittens (`Object` **instance** of `Entity Type` Cat):

~~~~
Litter:
  kittens: 1-12 Cat
~~~~

If an `ED` does not have a `Count` quantity, it is assumed to be `1` unless `Count` is `*` as explained below.

See the following concrete examples:

- `3` means "exactly 3"
- `2+` means "2 or more"
- `5-` means "5 or less"
- `-5` is **INVALID**, not recognized
- `10-12` means "between 10 to 12, inclusively" (i.e. could be 10, 11, or 12 instances)
- `-` means "1 or less" (useful perhaps for denoting optional attributes)
- `+` means "1 or more"
- `*` means "0 or more"

#### Type

The `Type` descriptor specifies the `Object` `ED` entity's `Entity Type`.

There can **ONLY** be 0 or 1 `Type` descriptors per `ED`.

The `Type` has three forms: `Implicit`, `Explicit`, and `Anonymous`.

---

`Implicit` `Types` have the format `Entity Type`. That is, their regex pattern is the same as `Entity Type`.

This `Type` is called `Implicit` because while it specifies the "is a" relationship between an entity **instance** and `Entity Type`, any new `ED`s not listed in this `Entity Type`'s `Root` definition **implies** the creation of a new `Entity Type` borne by this **instance**.

By not naming this `Implicit` `Type` following the form below, it is impossible for other `Entity Types` to inherit from this `Implicit` `Type`.

---

`Explicit` `Types` have the format `Child Type(Parent Type)`. Both `Child Type` and `Parent Type` are `Entity Types` and consequently have the same regex pattern as `Entity Type`.

This `Type` is called `Explicit` because both the inherited type (`Parent Type`) and resulting extended type (`Child Type`) are both named.

Unlike `Implicit` `Types`, beacuse `Explicit` `Types` are named, it is possible for other `Entity Types` to inherit from the resultant `Child Type`.

---

`Anonymous` `Types` have no format because this represents the case when there is no `Type` specified.

---

Remember, regardless of `Type` form, all `Object` `ED`s (properly indented) will have `Relationships` with the `Type` `Entity Type` and **NOT** the `ED` `entity` **instance**.

#### Constraints

In some cases, entities may be thought to have an inherent `Value`. A common example are basic variables in programming languages like `int` or `string`.

The `Constraints` descriptors specify limits on the `Value` an entity can take on.

There can be 0 or more `Constraints` per `ED`.

`Constraints` always have the form of `Operator` `Value` (e.g. = 1) with each pairing normally separated by a space for readability, though this is not strictly necessary.

The following `Operators` are supported:

- <: "less than"
- \> : "greater than"
- <= : "less than or equal to"
- \>= : "greater than or equal to"
- != : "not equal to"
- := : "always equal to" (for defining constants)
- _= : "by default equal to" (for indicating the value starts as this, but can change)
- = : "equal to" (as an example, but can change)

`Values` can currently be single or double quoted text, numbers, or `entities`.

`Values` can **NOT** currently be `Entity Types`, chains of `entities` separated by dots (e.g. petrescue.cat.name) as normally seen in object oriented programming.

## Imports

As found in programming, it's best practice to keep things decoupled and reusable ([SOLID principles](https://en.wikipedia.org/wiki/SOLID)).

`Import` lines enable this behavior. Authors can split up YERML models into files that can be imported into other YERML files to help build (complex) models more quickly.

The structure of the `import` line is:

~~~~
import "some/path/to/some/file.yerml"
~~~~

As of this time of writing the parser is not complete. However, the intention is that `Import` lines will simply be replaced with the contents of the file being pointed to (which will lead to naming conflicts, but we'll get there).

The `Import` path can be any URI or local file location.

## Comments

`Comments` allow authors to leave more detailed notes throughout a file.

YERML follows the YAML comment style, which starts with `#` and has to be the last thing in a line (or otherwise could be a line on its own).

For example:

~~~~
Cat: Mammal(Animal) # This is a comment
~~~~
## Directives

Only one `Directive` is supported and that's the `%YERML` `Directive`.

All valid YERML files **MUST** start with a `%YERML` directive followed by a version number corresponding to the language version the author is writing for.

For example:

~~~~
%YERML v0.1
~~~~

## File Separators

The file start (`---`) and file end (`...`) delimiters found in YAML are implemented in YERML as well. They don't really do anything right now.

# Example

The description above can still be a little dense and abstract, so let's look at an example:

~~~~
PetRescue:
  name: = "Happy Paws"
  cats: 12- Cat(Pet)
    has_FIP: _= "no"
  dogs: 20- Dog(Pet)
    is_leash_trained: _= "no"
    
Pet:
  name
  adoptable: _= "yes"
~~~~

And here is the English translation:

A Pet Rescue called Happy Paws can support ("have") up to 12 Cats and 20 Dogs. Cats and Dogs are Pets, which have names and can be adoptable by default. Cats can have FIP, usually not, but Dogs for sure can't have FIP. Dogs can be leash trained, but not by default (i.e. they're not leash trained when they're born). Cats normally are not leash trained.

# Known Issues

YERML does not provide a method for authors to explicitly specify relationships between `Subject` and `Object` `ED`s.

YERML entity definition constraint values lack boolean support.

# Release Notes

## 0.1

Initial release of YERML.
