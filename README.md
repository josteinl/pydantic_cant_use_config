# pydantic_cant_use_config

Investigate a problem with inheritance and Pydantic 2.7.0.

## Problem

```python
import dataclasses

from pydantic import BaseModel

@dataclasses.dataclass
class Parent:
    name: str

class Child(BaseModel):
    parent: Parent = Parent(name="unknown parent")
    name: str


if __name__ == "__main__":

    model_schema = Child.model_json_schema()
    print(model_schema)
```

When using Pydantic 2.7.0, the following error is raised (full stack trace in file):

```bash
pydantic.errors.PydanticUserError: Cannot use `config` when the type is a BaseModel, dataclass or TypedDict. These types can have their own config and setting the config via the `config` parameter to TypeAdapter will not override it, thus the `config` you passed to TypeAdapter becomes meaningless, which is probably not what you want.
```

When using Pydantic 2.6.4, the code works as expected:

```bash
(pydantic-cant-use-config-py3.11) C:\dev\pydantic_cant_use_config>python main.py
{'$defs': {'Parent': {'properties': {'name': {'title': 'Name', 'type': 'string'}}, 'required': ['name'], 'title': 'Parent', 'type': 'object'}}, 'properties': {'parent': {'allOf': [{'$ref': '#/$defs/Parent'}], 'default': {'name': 'unknown parent'}}, 'name': {'title': 'Name', 'type': 'string'}}, 'required': ['name'], 'title': 'Child', 'type': 'object'}
```

## Setup

- Windows 11
- Poetry 1.8.2
- Python 3.11.6
- Pydantic 2.7.0 (fail) / 2.6.4 (works)


