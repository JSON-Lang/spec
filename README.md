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
