# This. Is. YERML!!!

YERML stands for YAML-like Entity Relationship Modeling Language and it's incredibly fun to say! (At least I think so...)

This language is envisioned to help folks, particularly technology architects, document & abstractify thoughts, observations, and designs.

The core language focuses on supporting "is a" and "has a" relationships that may or may not have contraints.

This is a bit airy so let's look at some use cases...

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

### Entity Definition

Just about every line in a YERML file is a variation of one pattern:

{"has a" indentation}{subject}:{descriptors}

#### Indentation

Entity definition lines may have zero or more spaces preceding it. The spacing indicates what subject the definition belongs to (i.e. subject "has a" attribute as described by the entity definition line). 

Entity definitions starting with 0 spaces are called "root" definitions. This means they don't have a "belong" to relationship with any other entity except the implied global entity called the YERMLverse.

In practice, I recommend using spaces over tabs, but both are supported in the VS Code extension for now.

#### Subject

The entity definition subject can be either an `entity_name` OR `EntityType`.

##### Entity Names

Entity names are unique-to-the-belonging-subject identifiers. 

Their regex is `_*[a-z]\\w*`. Importantly, always start with a lower case letter or any number of underscores. They DO NOT start with upper case letters in order to prevent confusion with Entity Types.

Entity definitions whose subjects are entity names imply that all subsequent entity definitions (properly indented right 1 level) belong to this specific entity **instance** and NOT this entity's type unless this instance's type is included in the descriptors section.

##### Entity Types

Entity types are globally unique identifiers.

Their regex is `[A-Z]\\w*`. Importantly, they always start with an upper case letter. They DO NOT start with lower case letters or underscores in order to prevent confusion with Entity Names.

Entity definitions whose subjects are entity types imply that all subsequent entity definitions (properly indented right 1 level) belong to this entity **type** and all instances that are this entity type or inherit from it.

#### Descriptors

The core YERML specification implements 3 kinds of descriptors, which apply to the entity definition subject only.

None of the descriptors are required. Absence of some descriptors imply default values or meanings. 

The order in which descriptors are listed does not matter, but I personally like `Count` `EntityType` followed by a list of `Constraints`.

Finally, I prefer to delimit the descriptors by spaces to improve readability, but it's not strictly necessary to do so.

See below for details.
##### Count

The Count descriptor specifies the quantity & scale of this entity subject's relationship with the entity it "belongs" to.

There can ONLY be 0 or 1 Count per entity definition.

The regex for Count is `[*+-]|\\d+[+-]?`.

The Count typically has two parts: the quantity (scalar?) and the scaler. Additionally, there is one shorthand notation: `*`

The `quantity` sets a base number (0 and positive integers only) for how many of this entity there are in its relationship with its owner.

The `scaler` sets an open ended direction of growth for the Count. `+` denotes "or more". `-` denotes "or less" (down to and including 0).

If the `scaler` is present without a `quantity`, then the quantity is assumed to be `1`.

If the `quantity` is present without a `scaler`, then the Count is assumed to be exactly `quantity`.

`*` denotes 0 or more.

###### Count Examples

`2+` means "2 or more"
`5-` means "5 or less"
`-` means "1 or less" (useful perhaps for denoting optional attributes)
`+` means "1 or more"

##### Entity Type (with single inheritance)

##### Constraints

### Imports

### Comments

### Directives

## Example


 Let's look at an example:

~~~~
Cat:
  ear: 2- Ear(BodyPart) 
~~~~



{some number of spaces to denote "has a"}{entity_name or EntityType}{colon}{any of the following descriptors, if any, in any order, generally separated by spaces: {count (only one allowed)} {"is a" type with optional single inheritance} {constraints} }

None.
## Known Issues

None.

## Release Notes

### 0.1

Initial release of YERML VS Code extension. It follows with YERML v0.1 release.
