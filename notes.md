✅ 1. Why Pydantic?

Pydantic provides data validation + data parsing using Python type hints.

Ensures your input data is valid

Converts raw data into correct types

Gives clear error messages

Perfect for API/data pipelines/FastAPI

🟢 2. BaseModel – Why?

BaseModel is the core class used to create validated data models.

from pydantic import BaseModel

class User(BaseModel):
    id: int
    name: str

Why used?

Automatically validates each field

Converts types (e.g., "3" → int)

Gives .model_dump() / .model_dump_json()

Makes data structured and safe

🟢 3. Type Annotations – Why?

Pydantic uses annotations to know what to validate.

age: int
name: str
price: float
tags: list[str]

Why used?

They tell Pydantic what type each field should be

Pydantic auto-converts values into those types

Python type hints become runtime validators

🟢 4. Field() – Why?

Field() adds extra rules or metadata to a field.

from pydantic import BaseModel, Field

class Item(BaseModel):
    name: str = Field(..., min_length=3)
    qty: int = Field(1, ge=1)

Why used?

Set constraints (min/max, regex, length, etc.)

Add description, titles, examples

Provide default factories

Mark required fields (...)

🟢 5. EmailStr, AnyUrl, PastDate, PositiveInt… – Why?

Pydantic provides special data types with built-in validation.

from pydantic import BaseModel, EmailStr, AnyUrl

class User(BaseModel):
    email: EmailStr
    website: AnyUrl

Why used?

EmailStr → ensures valid email format

AnyUrl → ensures valid URL format

IPvAnyAddress, PaymentCardNumber, UUID4, etc.

Saves time writing your own validators

🟡 6. field_validator – Why?

Use when a single field needs custom validation beyond type checking.

from pydantic import BaseModel, field_validator

class User(BaseModel):
    username: str

    @field_validator("username")
    def no_space(cls, v):
        if " " in v:
            raise ValueError("no spaces allowed")
        return v

Why used?

Add custom logic for a field

Clean and reusable validation rules

Replaces deprecated @validator from v1

🟡 7. model_validator – Why?

Runs validation after the whole model is created.

from pydantic import BaseModel, model_validator

class Person(BaseModel):
    first: str
    last: str

    @model_validator(mode="after")
    def check_names(self):
        if self.first == self.last:
            raise ValueError("names must differ")
        return self

Why used?

Validate relationships between fields

Cross-field logic (e.g., password=confirm_password)

Replaces v1 root_validator

🟡 8. default_factory – Why?

Use when the default value must be generated dynamically.

from datetime import datetime

timestamp: datetime = Field(default_factory=datetime.utcnow)

Why used?

For dates, UUIDs, tokens

Avoids mutable defaults (e.g., [], {})

🔵 9. model_config – Why?

Controls global behavior of your model.

class User(BaseModel):
    model_config = {
        "extra": "ignore",
        "validate_assignment": True
    }

Why used?

Strict/lenient mode for extra data

Revalidate fields on assignment

Serialization options

Performance controls

🔵 10. Aliases – Why?

Map external data names → your field names.

class User(BaseModel):
    user_name: str = Field(alias="username")

Why used?

When API or DB uses different names

Allows snake_case inside Python, camelCase outside

🔵 11. RootModel – Why?

Use when the model represents a single value, not key-value fields.

from pydantic import RootModel

class Numbers(RootModel[list[int]]):
    pass

Why used?

Validate pure lists/dicts

Wrap primitive types (like RootModel[int])

Cleaner than v1’s __root__

🔵 12. TypeAdapter – Why?

Validates any type, without creating a model.

from pydantic import TypeAdapter

adapter = TypeAdapter(list[int])
adapter.validate_python(["1", 2, "3"])  # [1, 2, 3]

Why used?

Fastest way to validate lists, dicts, JSON

Great for APIs used outside Pydantic or for bulk data

🔵 13. from_attributes (ORM Mode) – Why?

Allows validating non-dict objects like ORM models.

class User(BaseModel):
    id: int
    name: str
    model_config = {"from_attributes": True}

User.model_validate(orm_user)

Why used?

Integrates with Django ORM, SQLAlchemy

Reads object attributes safely

🔥 14. Advanced Patterns (Why?)
✔ Union types
value: int | str


Useful when input can be multiple types.

✔ Literal
status: Literal["active", "inactive"]


For fixed choices.

✔ Partial updates
User.model_construct(...)  


Skip validation → fast object creation.

✔ Strict mode
Field(strict=True)


Disallow coercion (e.g., "1" → int)

✔ Custom Serialization
def model_dump(..., exclude=..., include=...)


Control output precisely.

⭐ Final Summary (Exam-style)**
Feature	Why it is used
BaseModel	Structured validated data models
Type hints	Tell Pydantic what type to expect
Field()	Add constraints, metadata, defaults
EmailStr / AnyUrl	Built-in strict validators
field_validator	Validate/transform specific field
model_validator	Validate relationships between fields
default_factory	Dynamic defaults (datetime, uuid)
model_config	Global settings for validation & serialization
TypeAdapter	Validate lists/dicts without models
RootModel	Single-value or collection models
from_attributes	Support ORM models (non-dict objects)