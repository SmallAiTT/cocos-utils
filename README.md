cocos-utils
===========

Cocos utilities for Cocos2d-html5 NPM supporting.

A tool to help developers coding cocos2d-html5 easily.


## Installing
* Install `nodejs`...
* Install `ant`...
* Then type:

```bash
npm install cocos-utils -g
```

## Help
* type help command for help, and see all details of `cocos` command:

```bash
cocos help
```


## Install all modules of cocos2d-html5

```bash
cd your/workspace/
cocos install
```


## Create project of cocos2d-html5
`cd` `your/workspace/`(under your server path), e.g. a project named `helloworld`:

```bash
cd your/workspace/
cocos new helloworld
cd helloworld
cocos build
```

## Visit dev version
* Be sure that your project has been published in a webserver, then visit `serverhost:prot/index.html`

## Publishing
cd to the path of `helloworld`, then type:

```bash
cocos publish
```

## Visit release version
* Be sure that your project has been published in a webserver, then visit `serverhost:prot/release.html`

## cocos.json
This file has some information about `cocos` command.

* engineDir--->the path of cocos2d-html5 modules, default to be `../node_modules/`.
This is very important, for it tells us where the engine is.

* dependencies--->This is an array to tell us which modules of engine be required.

* genRes--->To generate config of path of resources.

```script
{
    "output" : "cfg/res.js",
    "fileTypes" : [
        "png", "jpg", "bmp", "jpeg", "gif", "mp3", "ogg", "wav", "mp4", "plist",
        "xml", "fnt", "tmx", "tsx", "ccbi", "font", "txt", "vsh", "fsh", "json"
    ],
    "dirCfgs" : ["res/Normal->res/Normal"]//Path to be searched.The string after `->` will be deleted, such as `res/Normal/a.png` to be `a.png`.
}
```

* genJsRes--->To generate config of path of js sources.
```script
{
    "output" : "cfg/jsRes.js",
    "fileTypes" : ["js"],
    "dirCfgs" : ["src", "test"]//Same as genRes
}
```

* publish--->Publish project to single file mode.
```script
{
    "output" : "projects/proj.html5/mini.js",
    "compilationLevel" : "advanced",
    "warning" : "quiet",
    "useSourceMap" : true,
    "sourceMapOutputFile" : "sourcemap",//works while useSourceMap is true
    "sourceMapFormat" : "V3",//works while useSourceMap is true
    "debug" : false,
    "delLog" : false
}
```

## Structure of project
```script
- node_modules (dir of engine modules)
    -cocos2d-html5 (core for engine)

- helloworld (project dir)
    - cfg
        - res.js (Config of path of resources, generated by cocos genRes)
        - jsRes.js (Config of path of js sources, generated by cocos genRes)
        - resCfg.js (Config of dependencies for all resources)

    - res (Path of resources)
        -Normal (Normal version)
        -HD (HD version)

    - src (Dir to put sources)
    - test  (Dir to put test sources)

    - node_modules (dir of third party modules)

    - cocos2d.js (Boot config of game)
    - main.js (Main for game)
    - baseCfg.js (Base config for game to load js and so on)
    - index.html (Url of dev version)

    - mini.js (Generated by cocos publish)
    - build.xml (Generated by cocos publish, used for closer compiler)
    - release.html (Url of release version)

    - cocos.json (Config for cocos command)

    - package.json
```

## resCfg
This is the main config for the dependencies of the project.
And the `resCfg["moduleName"]...` is the base config for the module. e.g. a module named `m1`:

```script
var resCfg = cc.resCfg || {};
var jsRes = js.m1;
resCfg["m1"] = {
    ref : [jsRes.code01_js, jsRes.code02_js],
    res : [jsRes.a_png, jsRes.b_png]
};
```

`ref` is short for `reference`, which contains the references for this part.

`res` is short for `resource`, whiche means the resources to be loaded for the part.

`resCfg["m1"]` will be loaded when the project boots by default,
which means config for `code01.js` and `code02.js` will will ben loaded, and so as `a.png` and `b.png`.

```script
resCfg[jsRes.code03_js] = {
    res : [res.c_png],
    sprite : "MySprite",
    args : {a : "AAA"}
};
```

You can see that, `code03.js` has no references, but on resource named `c.png`.

The `sprite` is used to test `code03.js`.
Be sure the there is a class name `MySprite` in `code03.js`,
and the `MySprite` has a function called `create`, such as `MySprite.create = function(args)...`.
`args` of the config will be passed to the `MySprite.create` function.

Then, test mode is opened while the `config.test` is configured in `main.js`.
Otherwise, the project will be ran as normal mode.

e.g. set `config.test = js.m1.code03_js`, visit index.html, then you will see the test case of `code03.js`.

Same as `layer`, `scene` and so on.

Custom interface of test unit will be provided in the future.

By this way, you can test your js file easily, without editing any code just for your test,
and see the effort immediately.
You do not need to boot the whole game, and do a lot of "click" actions to reach the page which will be tested.



```script
resCfg[jsRes.code04_js] = {
    ref : [jsRes.code03_js]
};
resCfg[jsRes.code05_js] = {
    ref : [jsRes.code04_js]
};
```

In the above, `code03.js` is referenced by `code04.js`.
Not matter what has been changed in `code03.js`, nothing will be changed, while the interface of `code03.js` is not changed

This will be good for team work, that everyone just care about the interfaces provided by others.


```script
resCfg.gameModules = [jsRes.code05_js, ...];
```

This is for modules of game, such as home page, fighting and so on.
The engine will load resources ans js for the modules by this config.

`resCfg.js` looks so complex, but it is easy to be used step by step to improve the efficiency of coding.

## package.json
Same as `package.json` of `npm`.

Third party modules will be configured in `dependencies`.
If you add or delete a module (dependencies in package.json or cocos.json),
you should run `cocos build` or `cocos genBaseCfg` once more.


## Develop
Do not forget run `cocos genRes` if you add or delete resources, rename the resources or change the path of the resources.

Do not forget run `cocos genJsRes` if you add or delete js, rename js or change the path of js.

Do not forget run `cocos genBaseCfg` if you install or uninstall modules of cocos2d-html5 which are configured in dependencies of cocos.json,
or of third party which are configured in dependencies of package.json, or modify the base part of resCfg of your project.

In fact, you can use `cocos build` which includes these three command above.