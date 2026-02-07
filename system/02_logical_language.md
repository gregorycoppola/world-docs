# The Logical Language

## Overview

The logical language is the intermediate representation between natural language and the factor graph. It's defined in two files:

- `logic.py` — Core data types (the primitives)
- `logical_lang.py` — Grammar, parser, and serialization

## Tiered Architecture

### Tier 1: Atoms

The basic building blocks:

```python
@dataclass(frozen=True)
class Type:
    name: str          # e.g., "e", "person", "city"

@dataclass(frozen=True)
class RoleLabel:
    name: str          # e.g., "agent", "patient", "theme"

@dataclass(frozen=True)
class Entity:
    id: str            # e.g., "socrates", "paris"