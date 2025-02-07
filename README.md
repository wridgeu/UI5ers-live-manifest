# com.myorg.myui5project

Q: CIL? => https://github.com/SAP/ui5-manifest/blob/d28f1df75b48b6ad65d41d0298649d738c78654a/schema_cil.json

Q: `_version` of `sap.app` and other namespaces, sometimes not part of scaffold, is default latest, is there a version specific structure for these namespaces as well?

Q: Binding: `{{appTitle}}` & `{{key in .properties file}}` or `"${project.version}"`? See: https://ui5.sap.com/sdk/#/topic/74038a52dcd7404e82b38be6d5fb1458

Q: Copy Paste error, what now?

Docs: Descriptor `manifest.json`:  https://ui5.sap.com/#/topic/74038a52dcd7404e82b38be6d5fb1458
Docs: Descriptor Details (`_version`): https://ui5.sap.com/#/topic/be0cf40f61184b358b5faedaec98b2da

Manifest Repository: https://github.com/SAP/ui5-manifest
- Version Mappings: https://github.com/SAP/ui5-manifest/blob/main/mapping.json
- Schema: https://github.com/SAP/ui5-manifest/blob/main/schema.json

## VSCode Settings - JSON schema

https://code.visualstudio.com/docs/languages/json#_json-schemas-and-settings
https://www.schemastore.org/json/

### Settings
> User Settings or Workspace Settings

`url` can also map to a schema within your workspace.

```json
"json.schemas": [
    {
        "fileMatch": [
            "/bower.json",
            "/.bower.json"
        ],
        "url": "http://json.schemastore.org/bower"
    },
],
```

### Inline

`$schema` - VS Code-specific and not part of the JSON Schema specification.

See also [UI5 Documentation](https://ui5.sap.com/#/topic/be0cf40f61184b358b5faedaec98b2da) for "No Namespace". Or [directly](https://github.com/SAP/ui5-manifest/blob/c00f35ab0e4dabe03c8c83f47421a9055a67ff4c/schema.json#L12-L16).

```json
{
  "$schema": "https://json.schemastore.org/coffeelint",
  "line_endings": "unix"
}
```

## Generated Manifest from BTP (BAS) 

Basic;

Check out the differences between the Targets, the first target is using the actual/new properties, and thus is incompatible when used with
```json
"viewPath": "myui5app.view", 
```

instead you need the `path` property here. What we can learn from the BAS kickstart/template is, that a mix & match for both, legacy and new 
is also possible & allowed!

```json
"TargetMainView": {
	"id": "MainView",
	"name": "MainView"
},
"TargetSecondView": {
	"viewType": "XML",
	"transition": "slide",
	"clearControlAggregation": false,
	"viewId": "SecondView",
	"viewName": "SecondView"
}
```
... and the config

```json
	"config": {
		"routerClass": "sap.m.routing.Router",
		"controlAggregation": "pages",
		"controlId": "app",
		"transition": "slide",
		"type": "View",    <<<<<<<< actualTargetAdditionStandard (View or Component) |$defs►actualTargetAdditionStandardWithoutRequiredProp►properties►type
		"viewType": "XML", <<<<<<<< Unchanged! Part of normal target definition      |$defs►target►properties►viewType
		"path": "project1.view",    <<<<<<<< actualTargetAdditionStandard            |$defs►actualTargetAdditionStandardWithoutRequiredProp►properties►path
		"async": true,
		"viewPath": "project1.view" <<<<<<<< legacyTargetAddition                    |$defs►legacyTargetAddition►properties►viewPath
	},
```

---

```json
{
    "_version": "1.65.0",
    "sap.app": {
        "id": "project1",
        "type": "application",
        "i18n": "i18n/i18n.properties",
        "applicationVersion": {
            "version": "0.0.1"
        },
        "title": "{{appTitle}}",
        "description": "{{appDescription}}",
        "resources": "resources.json",
        "sourceTemplate": {
            "id": "@sap/generator-fiori:basic",
            "version": "1.16.3",
            "toolsId": "5cd285ab-43e2-4982-94c7-fc91b4021e03"
        }
    },
    "sap.ui": {
        "technology": "UI5",
        "icons": {
            "icon": "",
            "favIcon": "",
            "phone": "",
            "phone@2": "",
            "tablet": "",
            "tablet@2": ""
        },
        "deviceTypes": {
            "desktop": true,
            "tablet": true,
            "phone": true
        }
    },
    "sap.ui5": {
        "flexEnabled": true,
        "dependencies": {
            "minUI5Version": "1.132.1",
            "libs": {
                "sap.m": {},
                "sap.ui.core": {}
            }
        },
        "contentDensities": {
            "compact": true,
            "cozy": true
        },
        "models": {
            "i18n": {
                "type": "sap.ui.model.resource.ResourceModel",
                "settings": {
                    "bundleName": "project1.i18n.i18n"
                }
            }
        },
        "resources": {
            "css": [
                {
                    "uri": "css/style.css"
                }
            ]
        },
        "routing": {
            "config": {
                "routerClass": "sap.m.routing.Router",
                "controlAggregation": "pages",
                "controlId": "app",
                "transition": "slide",
                "type": "View",
                "viewType": "XML",
                "path": "project1.view",
                "async": true,
                "viewPath": "project1.view"
            },
            "routes": [
                {
                    "name": "RouteView1",
                    "pattern": ":?query:",
                    "target": [
                        "TargetView1"
                    ]
                }
            ],
            "targets": {
                "TargetView1": {
                    "id": "View1",
                    "name": "View1"
                }
            }
        },
        "rootView": {
            "viewName": "project1.view.App",
            "type": "XML",
            "id": "App"
        }
    }
}
```
