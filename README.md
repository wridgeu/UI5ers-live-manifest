# UI5ers Live `manifest.json`

## Q & A

- **Q: What does the [CIL](https://github.com/SAP/ui5-manifest/blob/d28f1df75b48b6ad65d41d0298649d738c78654a/schema_cil.json) stand for?**

	**A:** Component In Library

- **Q: `_version` of the `manifest.json` itself?**

	**A:** Referencing the schema version and used for validation and code completion (IntelliSense in IDE). 
	> "On the runtime side, [...] only one changed behavior based on the vesion of the manifest quite some time ago (since UI5 1.52, schema version 1.9.0)." - [UI5 Team on SO](https://stackoverflow.com/a/72123051/10323879)

- **Q: `_version` of `sap.app` (and other namespaces). Sometimes not part of scaffold: is default latest? is there a version specific structure for these namespaces as well?**

	**A:** The namespace dependent `_version` property is and was barely used. It is also not part of the current scaffolding generators anymore.

- **Q: Possible Bindings in Manifest? Like `{{appTitle}}` (`{{key in .properties file}}`) or `"${project.version}"`? See: [Descriptor Documentation](https://ui5.sap.com/sdk/#/topic/74038a52dcd7404e82b38be6d5fb1458) ( [GH issue](https://github.com/SAP/ui5-tooling/issues/403) in UI5 Tooling with `${version}`)**

	**A:**

- **Q: What is the minimum content for a manifest to work per application/component?**

	**A:** Depends on the type of application you're working with (UI5 application/component, Cards, etc.). Best to rely on the corresponding generator.

- **Q: `sourceTemplate` what is it used for and does it help me as developer in any way?**

	**A:** A rather technical key, answering what the source of the generation was. General information for tooling. Only exists when corresponding tooling was used (i.e. in BAS).

- **Q: `IAsyncContentCreation` tag interface vs manifest `async` keys what matters more/less? See: [Async Loading Documentation](https://ui5.sap.com/sdk/#/topic/676b636446c94eada183b1218a824717)**

	**A:**

- **Q: Can I adjust the manifest on the fly via tooling or provide one (or its entries) dynamically (yes)? See: [SAP Community Question by Marcel Schork](https://community.sap.com/t5/technology-q-a/dynamic-odata-urls-in-fiori-elements-apps-running-in-cflp/qaq-p/12753637) or his [tweet](https://x.com/marcel_schork/status/1717287636929257815)**

	**A:** Generally discouraged. There should only be _one_ `manifest.json` and it should be the "source of truth". Decoupling something like the `dataSources` via a general Data Source API could be done (and was once tried by SAP? => couldn't find the sources anymore). These parts have rather "historically grown".

- **Q: Is one of these `minUI5Version` & `minVersion` my UI5 version? See: [SAP Fiori Tools User Guide](https://help.sap.com/docs/SAP_FIORI_tools/17d50220bcd848aa854c9c182d65b699/009f43e381234626b41e542dd7335672.html)**

	**A:** As the name suggests, the `minUI5Version` is the minimum UI5 version that is required at runtime to support the features used in the application. The same goes for the dependency section. They do not _force_ a version per se but may be used for feature detection (e.g. application modeler & guided development) and can influence things like tooling behavior (e.g. [GH Issue](https://github.com/SAP/ui5-tooling/issues/369)).

- **Q: Can I enhance the `manifest.json` with custom properties, like others do with the `package.json`? Maybe for my own tooling/scripts? Knowing about the `$schema`, could I create a custom schema describing those fields? Would it break UI5, is it strongly discouraged (also: ask peter about the part of last call: UI5 does not enforce or break due to things in manifest, something along those lines)?**

	**A:** 2nd question: You can merge schema definitions and by doing so, technically create your own! See this [explanation regarding schema inheritance](https://github.com/json-schema-org/json-schema-spec/issues/348#issuecomment-322940347).

- **Q: Why can't I just generate what I need once, and be done with it?**

	A: While this is a rather jokingly asked question, it does have some merit as not everyone _wants_ to fiddle with it and not everyone does so in his day to day. But you have to understand that the entire application itself, manifests in this file and therefore, as the application changes, so does the manifest. A living document so to say.

> [!TIP] 
>
> The `manifest.json` is not the only UI5 configuration file with a schema! The [ui5-workspace.yaml](https://sap.github.io/ui5-tooling/schema/ui5-workspace.yaml.json) and [ui5.yaml](https://sap.github.io/ui5-tooling/schema/ui5.yaml.json) have one too!

## General Information

### Docs
- `manifest.json` the descriptor file, see [docs](https://ui5.sap.com/#/topic/74038a52dcd7404e82b38be6d5fb1458)
- descriptor details (`_version`), see [docs](https://ui5.sap.com/#/topic/be0cf40f61184b358b5faedaec98b2da)

### Manifest Repository 
- [Repository](https://github.com/SAP/ui5-manifest)
- [Version Mappings](https://github.com/SAP/ui5-manifest/blob/main/mapping.json)
- [Schema](https://github.com/SAP/ui5-manifest/blob/main/schema.json)

## VSCode & JSON Schema

- [VSCode Documentation](https://code.visualstudio.com/docs/languages/json#_json-schemas-and-settings)
- [Schemastore](https://www.schemastore.org/json/)

### Settings

#### Workspace / User Specific

The `url` can also map to a schema within your workspace.

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

#### Inline

The `$schema` property is VSCode specific and not part of the JSON Schema specification!

UI5 supports the `$schema` property, see also the relevant section in the [documentation](https://ui5.sap.com/#/topic/be0cf40f61184b358b5faedaec98b2da) under "No Namespace". Or [directly](https://github.com/SAP/ui5-manifest/blob/c00f35ab0e4dabe03c8c83f47421a9055a67ff4c/schema.json#L12-L16) in the manifest schema itself!

Alternatively: `https://github.com/SAP/ui5-manifest/blob/main/schema.json` or local in your workspace `../manifest_schema.json`

```json
{
  "$schema": "https://raw.githubusercontent.com/SAP/ui5-manifest/master/schema.json"
}
```

> [!NOTE] 
> Check `{}` - Schema Validation Status Bar.
>
> It can happen (especially in BASP due to reset of configs) that you do not have any validation. This is due to no default formatter being configured.
> 
> If the validation isn't updated once it was set: reload schema cache (command palette), navigate to different file & back, reload window or restart editor.

## Generated Manifest from BTP (BAS) 

Basic;

Check out the differences between the Targets, the first target is using the actual/new properties, and thus is incompatible when used with the `viewPath`.

```json
"viewPath": "myui5app.view", 
```

Instead you need the `path` property here. What we can learn from the BAS kickstart/template is, that a mix & match for both, legacy and new 
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
