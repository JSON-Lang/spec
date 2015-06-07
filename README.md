# JSON-Lang Specification
*For JSON Schema definition of the JSON-Lang specification, go to schemas/latest/schema.json file.*
*Quick introductory examples are below.*
**This document is to be completed.**
## 	Introduction
##	Definitions
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
