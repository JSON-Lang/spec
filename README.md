# JSON-Lang Specification
*For JSON Schema definition of the JSON-Lang specification, go to schemas/latest/schema.json file.*
*Quick introductory examples are below.*
**This document is to be completed.**
## 	Introduction
JSON-Lang is a JSON sub-specification designed to provide a global Internationalization exchange file, especially for programming environments rather than documents. Ideally, you would have one unified format for all of your internalization, localization configuration and translations, which you could move between platforms with no touch. If the platform or environment has a JSON-Lang parser, you wouldn't need any modification on the file to use it throughout your system.

You can consider this specification as a highly thin, programming oriented JSON version of XLIFF standard.
##	Definitions
#### 	Strings
Strings are unique, multilingual text pieces. JSON-Lang does not come with a "source text > translation" relation, but instead, every string contains multilingual content.

Translations of a String is listed as an array under `translations` key. If you have specific variables (that are supposed to be imported from an external source) for a String, you can put them under `variables` key.

Structure of a String object follows this schema:
```
StringIDValue: {
	"translations": [TranslationObject],
	"variables": {
		VariableIDValue: {
			"type": VariableTypeValue
		}
	},
	"comments": [CommentObject]
}
```

| Property name | Is required? | Content  |
|---------------|--------------|----------|
| translations | yes | Array of Translation objects. |
| variables | no | A list of variables in a key-value fashion. Value must be a Variable object. |
| comments| no | An array of Comment objects. |

Below is a basic example of a String object.

```json
"welcome": {
    "translations": [
        {
            "language": "en-US",
            "text": "Welcome home, __first_name__!"
        },
        {
            "language": "tr-TR",
            "text": "Tekrar hoşgeldin, __first_name__!"
        }
    ],
    "variables": {
        "first_name": {
            "type": "string"
        }
    },
    "comments": [
        {
            "text": "This is my comment for this specific string.",
            "author": {
            	"$ref": "#/authors/0"
            },
            "time": "2015-06-07T15:51:41.297Z"
        }
    ]
}
```
#### Translations
A Translation object is the representation of a String in a specific language. JSON-Lang does not come with a "source > translation" relation. Instead, the content in different languages (including the one in "source language") are all represented in the same way, as a Translation object.

Translation object can hold these values:

| Property name | Is required? | Content |
|---------------|--------------|---------|
| language | yes | Language code that follows ISO 639-1 standard. Example: en-US, tr, fr, zh-TW. |
| text | yes | Content of this translation. If a `context` is specified, this value is considered as "singular" context. |
| contexts | no | A list of contextual translation as a key-value list. Value must be a Translation object or a JSONPath to a String object. |
| author | no | An Author object or a JSONPath to an Author object. |
| time | no | Date value as defined in RFC 3339, section 5.6. |
| status | no | One of the following: `initial`, `translated`, `reviewed`, `final` |
| comments | no | An array of Comment objects. |
| alternatives | no | An array of Translation objects. This is to keep a list of previous or alternative versions of this Translation. This value should not perceived as usable value. |

Basic example:
```json
{
    "language": "en-US",
    "text": "Welcome back, __first_name__!"
}
```

Advanced example:
```json
{
	"language": "fr-FR",
	"text": "Bonjour l'homme!",
	"contexts": {
		"plural": {
			"text": "Bonjour les gens, en __current_year__!"
		},
		"sincere": {
			"text": "Bonjour mon cher, __first_name__"
		},
		"morning": {
			"$ref": "#/strings/hello_morning"
		}
	},
	"comments": [
		{
			"text": "This is my comment for this specific French translation.",
			"time": "2015-06-07T15:51:41.297Z",
			"author": {
				"$ref": "#/authors/0"
			}
		}
	],
	"time": "2015-06-07T15:51:41.297Z",
	"author": {
		"$ref": "#/authors/0"
	},
	"alternatives": [
		{
			"text": "Hello guy!",
			"contexts": {
				"plural": {
					"text": "Hello guys!"
				}
			},
			"time": "2015-06-07T15:51:41.297Z"
		}
	],
}
```

#### Language codes
All language codes must follow ISO 639-1 standard. When required, append the locale code as well: `pt-BR`.

#### Contexts
Contexts are variations of translations in different contexts. For example, a translation can have a singular form, which is specified in `text` value and various contextual translations such as `plural`, `female`, `male` forms. As languages and technical needs can be highly various, unifying them under contexts is providing infinite opportunities for different use cases. You can, for example, have a base string "Good day at MotaWord!" and a couple of other contexts such as "Good morning at MotaWord!", "Good night at MotaWord!", "Good Sunday at MotaWord!".

`contexts` value of a Translation object accepts Translation objects with context name as the key.

JSON-Lang provides default contexts for ease of use and standardization and they should be handled accordingly by parsers. These are:

| Context name | Description |
|--------------|-------------|
| plural | Plural form of the Translation. A JSON-Lang parser must return this value when the given numeric condition is bigger than 1 or the specified context is `plural`.|
| plural_x | Where `x` is an `integer`. Specific plural form of the Translation for conditions where the value is exactly `x`. A JSON-Lang parser must return this value when the given numeric condition is exactly `x`. The same format can be formed for all numeric values: `plural_2`, `plural_3`, `plural_4`, `plural_5`. This is especially useful in languages such as Russian.|
| plural>x | Where `x` is an `integer`. Specific plural form of the Translation for conditions where the value is bigger than `x`. A JSON-Lang parser must return this value when the given numeric consition is bigger than `x`. The same format can be formed for all numeric values: `plural>2`, `plural>3`, `plural>4` etc.|
| female | Female form of the Translation. |
| male | Male form of the Translation. |

The suggested way to provide the condition for contexts is along these lines:
```javascript
// This is a pseudo code.
jsonLang.get('welcome', 3); // returns one of `plural_3`, `plural>2`, `plural` contexts in this order. If not, falls back to normalized value, `text` property of the Translation object.
jsonLang.get('welcome', 'female'); // all other contexts are specified by their name.
```
Example:
```json
"contexts": {
	"plural": {
		"text": "Bonjour les gens, en __current_year__!"
	},
	"sincere": {
		"text": "Bonjour mon cher, __first_name__",
        "time": "2015-06-07T15:51:41.297Z",
        "author": {
            "$ref": "#/authors/0"
        },
	},
	"morning": {
		"$ref": "#/strings/hello_morning"
	}
}
```

**Note:** While contexts accept Translation objects or references to Translation objects, `language` property is not required. The Translation object they belong to defines the language of the Translation.

#### Variables
You can easily place a dynamic value in your strings by following this format: `__variable_name__`. If you provide `variable_name` parameter on your call to the JSON-Lang parser, this syntax will be replaced with the actual value.

`Variables` schema, on the other hand, defines a list of dynamic parameters accepted and their valid formats. They are basically validation rules for the variables used in Translation values. When a JSON-Parser receives a variable parameter, it should validate its format against variable's `type`, if specified. Otherwise, the parser should replace all of the provided variables regardless.

Variable validations have two scopes: a String object and document. A variable definition in a String object can override the one in the document scope.

Variable validation rules accept the following options:
| Property name | Required | Description |
|---------------|----------|-------------|
| type | yes | Type of the variable. See the Variable Types and Formats table below. |
| format | no | Format of the variable. A variable can be of a type and the format specifies the presentation and format of that type. Example: type = string, format = date-time. See the Variable Types and Formats table below. |

| type | format | description |
|------|--------|-------------|
| array | array |	A JSON array. |
| boolean | boolean |	A JSON boolean. |
| integer | integer |	A JSON number without a fraction or exponent part. |
| number | number | 	Any JSON number. Number includes integer. |
| null | null |	The JSON null value. |
| object | object |	A JSON object. |
| string | string |	A JSON string. |
| string | date-time |	A date time string defined by RFC 3339, section 5.6. |
* This table is not complete. *

String variable rule:
```json
"hello_morning": {
	"translations": [
		{
			"language": "en-US",
			"text": "Good morning, __first_name! Today is __date__."
		}
	],
	"variables": {
		"first_name": {
			"type": "string"
		},
		"date": {
			"type": "string",
			"format": "date-time"
		}
	}
}
```

##	Behaviors
###	Points and Strings
###	Variables
###	Contexts
###	Meta Data
## 	Examples
1. Basic language file definition
```json
{
	"encoding": "utf8",
	"strings": {
		"welcome": {
			"translations": [
				{
					"language": "en-US",
					"text": "Welcome home, __first_name__!"
				},
				{
					"language": "tr-TR",
					"text": "Tekrar hoşgeldin, __first_name__!"
				}
			],
			"variables": {
				"first_name": {
					"type": "string"
				}
			}
		},
		"success": {
			"translations": [
				{
					"language": "en-US",
					"text": "Congratulations, everything went well!"
				},
				{
					"language": "tr-TR",
					"text": "Tebrikler, her şey yolunda!"
				}
			]
		}
	},
	"points": {
		"header_login_success": {"$ref": "#/strings/welcome"},
		"login_welcome_subheader": [
			{
				"language": "en-US",
				"text": "Hello cats!"
			},
			{
				"language": "fr-FR",
				"text": "Bonjour la chat!",
				"contexts": {
					"plural": "Bonjour les chats!"
				}
			}
		],
		"registration": {
			"success": {"$ref": "#/strings/success"}
		}
	}
}
```
2. Advanced language file definition
```json
{
	"encoding": "utf8",
	"comments": [
		{
			"text": "This is my comment for the whole document.",
			"time": "2015-06-07T15:51:41.297Z",
			"author": {
				"$ref": "#/authors/0"
			}
		}
	],
	"variables": {
		"site_name": {
			"type": "string"
		},
		"current_year": {
			"type": "integer"
		}
	},
	"authors": [
		{
			"name": "Oytun Tez",
			"email": "oytun@motaword.com",
			"last_modification": "2015-06-07T15:51:41.297Z"
		}
	],
	"languages": {
		"source": "en-US",
		"targets": [
			"en-US", "fr-FR", "tr-TR"
		]
	},
	"strings": {
		"hello": {
			"translations": [
				{
					"language": "en-US",
					"text": "Hello people!",
					"alternatives": [
						{
							"text": "Hello guy!",
							"contexts": {
								"plural": {
									"text": "Hello guys!"
								}
							},
							"time": "2015-06-07T15:51:41.297Z"
						}
					],
					"author": {
						"$ref": "#/authors/0"
					}
				},
				{
					"language": "fr-FR",
					"text": "Bonjour l'homme!",
					"contexts": {
						"plural": {
							"text": "Bonjour les gens, en __current_year__!"
						}
					},
					"comments": [
						{
							"text": "This is my comment for this specific French translation.",
							"time": "2015-06-07T15:51:41.297Z",
							"author": {
								"$ref": "#/authors/0"
							}
						}
					],
					"time": "2015-06-07T15:51:41.297Z"
				},
				{
					"language": "tr-TR",
					"status": "final",
					"text": "Merhaba __first_name__!",
					"contexts": {
						"sincere": {
							"text": "Selam __first_name__"
						},
						"morning": {
							"$ref": "#/strings/hello_morning"
						}
					}
				}
			],
			"variables": {
				"first_name": {
					"type": "string"
				}
			},
			"comments": [
				{
					"text": "This is my comment for this specific string.",
					"author": {
						"$ref": "#/authors/0"
					},
					"time": "2015-06-07T15:51:41.297Z"
				}
			]
		},
		"hello_morning": [
			{
				"language": "tr-TR",
				"text": "Gunaydin insan!",
				"contexts": {
					"plural": {
						"text": "Gunaydin insanlar!"
					}
				}
			}
		],
		"success": [
			{
				"language": "en-US",
				"text": "Congratulations! Thanks for registering."
			}
		]
	},
	"points": {
		"login_welcome_header": {
			"$ref": { 
				"$ref": "#/strings/hello"
			}
		},
		"login_welcome_subheader": [
			{
				"language": "en-US",
				"text": "Hello cats!"
			},
			{
				"language": "fr-FR",
				"text": "Bonjour la chat!",
				"contexts": {
					"text": "Bonjour les chats!"
				}
			}
		],
		"registration": {
			"success": {
				"$ref": "#/strings/success"
			}
		}
	}
}
```
## 	Tools
## 	Support
##	Contributing
##	License