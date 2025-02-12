# UI5ers Live `manifest.json`

## Q & A

- **Q: What does the [CIL](https://github.com/SAP/ui5-manifest/blob/d28f1df75b48b6ad65d41d0298649d738c78654a/schema_cil.json) stand for?**

	**A:** Component In Library

- **Q: `_version` of the `manifest.json` itself?**

	**A:** Referencing the schema version and used for validation and code completion (IntelliSense in IDE). 
	> "On the runtime side, [...] only one changed behavior based on the vesion of the manifest quite some time ago (since UI5 1.52, schema version 1.9.0)." - [UI5 Team on SO](https://stackoverflow.com/a/72123051/10323879)

- **Q: `_version` of `sap.app` (and other namespaces). Sometimes not part of scaffold: is default latest? is there a version specific structure for these namespaces as well?**

	**A:** The namespace dependent `_version` property is and was barely (if ever) used, mostly for SAP internal testing tools or as metadata. It is also not part of the current scaffolding generators anymore.

- **Q: Possible Bindings in Manifest? Like `{{appTitle}}` (`{{key in .properties file}}`) or `"${project.version}"`? See: [Descriptor Documentation](https://ui5.sap.com/sdk/#/topic/74038a52dcd7404e82b38be6d5fb1458) ([GH issue](https://github.com/SAP/ui5-tooling/issues/403) in UI5 Tooling with `${version}`)**

	**A:** Check out the corresponding [documentation](https://ui5.sap.com/#/topic/be0cf40f61184b358b5faedaec98b2da.html%23loiobe0cf40f61184b358b5faedaec98b2da/section_sap_app) and search for occurences of `{{`. This will help you find the places where binding is possible. As with the mentioned `${version}`, check out the UI5 Tooling and its [possibilities for replacing handlebars during build](https://sap.github.io/ui5-tooling/v3/api/builder_lib_tasks_replaceVersion.js.html)! See also the [builder documentation](https://sap.github.io/ui5-tooling/v3/pages/Builder/) and the mentioned `replaceCopyright` and `replaceVersion` tasks.

- **Q: What is the minimum content for a manifest to work per application/component?**

	**A:** Depends on the type of application you're working with (UI5 application/component, Cards, etc.). Best to rely on the corresponding generator. Absolute minimum: - sap.ui section only if dependencies
	<details>
	<summary>Theoretical Bare Minimal Manifest</summary>
		
	Properties like `sap.ui5` would then be required for [dependencies](https://ui5.sap.com/sdk/#/topic/8521ad1955f340f9a6207d615c88d7fd).

	Source: [TS Walkthrough](https://github.com/SAP-samples/ui5-typescript-walkthrough/blob/main/steps/01/webapp/manifest.json)
	
	```json
	{
	    "_version": "1.60.0",
	    "sap.app": {
	        "id": "ui5.walkthrough",
	        "type": "application",
	        "title": "OpenUI5 TypeScript Walkthrough",
	        "applicationVersion": {
	            "version": "1.0.0"
	        }
	    }
	}
	```	
	</details>

- **Q: `sourceTemplate` what is it used for and does it help me as developer in any way?**

	**A:** A rather technical/metadata key, answering what the source of the generation was. General information for tooling. Only exists when corresponding tooling was used (i.e. in BAS).

- **Q: `IAsyncContentCreation` tag interface vs manifest `async` keys what matters more/less? See: [Async Loading Documentation](https://ui5.sap.com/sdk/#/topic/676b636446c94eada183b1218a824717)**

	**A:** Short: The flags within the manifest (i.e. `RootView`) do not have the same effects as using the `IAsyncContentCreation` Interface. As the latter also effects other routing elements.
	<details>
	<summary>Details</summary>


	There are three main places where `async` is important & used:
	1.  The async setting of the component (within bootstrap)
 	2.  the RootView and
  	3.  within routing.
	<br> 
  	It is hard to compare these with another as they have different effects and the usage depends on what you want the result to be. Below is more of an Overview than a comparison.

	Effects of `IAsyncContentCreation` Interface
	- `createContent( )` can be async. This also results in the component factory being delayed.
 	- Nested Views are implicitly async. This is the case for all Views/Fragments within (that are part of) an async Owner-Component.
  	- Sync does not work anymore, as errors will be thrown.
  	- RootView & Routing configuration will be switched to async. This also results in changes to routes & targets!
  	- Strict Mode for View Processing. Meaning that errors will be thrown (promises will be rejected).

 	<br>
 
	There is also a [change in behavior](https://github.com/SAP/openui5/commit/5981ae4115c919f278572e2d2c500933c8c41efc#diff-57834d2526e8323161a07fca4c2b67a70b1843cb7e1e8d8ea32a68876c72b0c9L1677) between Pre- and Post UI5 version `1.125` when using the Interface.
	</details>

- **Q: Can I adjust the manifest on the fly via tooling or provide one (or its entries) dynamically (yes)? See: [SAP Community Question by Marcel Schork](https://community.sap.com/t5/technology-q-a/dynamic-odata-urls-in-fiori-elements-apps-running-in-cflp/qaq-p/12753637) or his [tweet](https://x.com/marcel_schork/status/1717287636929257815)**

	**A:** Generally discouraged. There should only be _one_ `manifest.json` and it should be the "source of truth". Decoupling something like the `dataSources` via a "general Data Source API" could be done (and was once tried by SAP? => couldn't find the sources anymore). These parts have rather "historically grown".

- **Q: Is one of these `minUI5Version` & `minVersion` my UI5 version? See: [SAP Fiori Tools User Guide](https://help.sap.com/docs/SAP_FIORI_tools/17d50220bcd848aa854c9c182d65b699/009f43e381234626b41e542dd7335672.html)**

	**A:** As the name suggests, the `minUI5Version` is the minimum UI5 version that is required at runtime to support the features used in the application. The same goes for the dependency section. They do not _force_ a version per se but may be used for feature detection (e.g. application modeler & guided development) and can influence things like tooling behavior (e.g. [GH Issue](https://github.com/SAP/ui5-tooling/issues/369)).

- **Q: Can I enhance the `manifest.json` with custom properties, like others do with the `package.json`? Maybe for my own tooling/scripts? Knowing about the `$schema`, could I create a custom schema describing those fields? Would it break UI5, is it strongly discouraged (also: ask peter about the part of last call: UI5 does not enforce or break due to things in manifest, something along those lines)?**

	**A:**
	- Custom sections are generally possible and not actively discouraged, as long as they do not interfere with SAPs namespaces. Or to put it different: as long as it is in your own namespace you may do as you please (e.g. `<company>.<xyz>`. There are no runtime checks from UI5 perspective that would hinder you to do so.
	  
	 - You can also merge schema definitions and by doing so, technically create your own, extending upon the UI5 manifest (or any other) schema! See this [explanation regarding schema inheritance](https://github.com/json-schema-org/json-schema-spec/issues/348#issuecomment-322940347). Also check out the demo example within this repository.
	  
	- Within `sap.ui5.config` you can also put any sort of custom configuration, as long as it adheres to the allowed types (see schema).

- **Q: Why can't I just generate what I need once, and be done with it?**

	A: While this is a rather jokingly asked question, it does have some merit as not everyone _wants_ to fiddle with the manifest file and not everyone does so in his day to day work. But you have to understand that the entire application itself, manifests in this file and therefore, as the application changes, so does the manifest. A living document so to say.

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
