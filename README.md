# api documentation for  [gulp-hash (v4.1.1)](https://github.com/Dragory/gulp-hash)  [![npm package](https://img.shields.io/npm/v/npmdoc-gulp-hash.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-gulp-hash) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-gulp-hash.svg)](https://travis-ci.org/npmdoc/node-npmdoc-gulp-hash)
#### Cachebust your assets by adding a hash to the filename

[![NPM](https://nodei.co/npm/gulp-hash.png?downloads=true)](https://www.npmjs.com/package/gulp-hash)

[![apidoc](https://npmdoc.github.io/node-npmdoc-gulp-hash/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-gulp-hash_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-gulp-hash/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-gulp-hash/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-gulp-hash/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Miikka Virtanen",
        "email": "contact@mivir.fi"
    },
    "bugs": {
        "url": "https://github.com/Dragory/gulp-hash/issues"
    },
    "contributors": [
        {
            "name": "https://github.com/coen-hyde"
        },
        {
            "name": "https://github.com/jorrit"
        },
        {
            "name": "https://github.com/ai"
        },
        {
            "name": "https://github.com/othree"
        },
        {
            "name": "https://github.com/outpunk"
        },
        {
            "name": "https://github.com/MEGApixel23"
        }
    ],
    "dependencies": {
        "es6-promise": "^2.1.1",
        "gulp-util": "^2.2.17",
        "lodash.assign": "^2.4.1",
        "lodash.template": "^2.4.1",
        "through2": "^2.0.0"
    },
    "description": "Cachebust your assets by adding a hash to the filename",
    "devDependencies": {
        "gulp": "^3.9.0",
        "mocha": "^1.20.1"
    },
    "directories": {},
    "dist": {
        "shasum": "7779910f6c378686b62f88abbb57ebeef20945d0",
        "tarball": "https://registry.npmjs.org/gulp-hash/-/gulp-hash-4.1.1.tgz"
    },
    "gitHead": "17e6ca0c39228df9dc3ce7eddb479b21ee8a753a",
    "homepage": "https://github.com/Dragory/gulp-hash",
    "keywords": [
        "cache",
        "cachebust",
        "cache bust",
        "gulpplugin"
    ],
    "license": "MIT",
    "main": "./index.js",
    "maintainers": [
        {
            "name": "mivir",
            "email": "contact@mivir.fi"
        }
    ],
    "name": "gulp-hash",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git+ssh://git@github.com/Dragory/gulp-hash.git"
    },
    "scripts": {
        "test": "mocha ./test"
    },
    "version": "4.1.1"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module gulp-hash](#apidoc.module.gulp-hash)
1.  [function <span class="apidocSignatureSpan">gulp-hash.</span>manifest (manifestPath, options)](#apidoc.element.gulp-hash.manifest)
1.  [function <span class="apidocSignatureSpan">gulp-hash.</span>resetManifestCache ()](#apidoc.element.gulp-hash.resetManifestCache)



# <a name="apidoc.module.gulp-hash"></a>[module gulp-hash](#apidoc.module.gulp-hash)

#### <a name="apidoc.element.gulp-hash.manifest"></a>[function <span class="apidocSignatureSpan">gulp-hash.</span>manifest (manifestPath, options)](#apidoc.element.gulp-hash.manifest)
- description and source-code
```javascript
manifest = function (manifestPath, options) {
	var space = null;
	var append = true;
	var sourceDir = __dirname;
	var deleteOld = false;

	if (arguments.length === 2 && typeof options === 'object') {
		// New signature
		if (options.append != null) append = options.append;
		if (options.space != null) space = options.space;
		if (options.sourceDir) sourceDir = options.sourceDir;
		deleteOld = !!options.deleteOld;
	} else {
		// Old signature
		if (arguments[1] != null) append = arguments[1];
		if (arguments[2] != null) space = arguments[2];
	}

	var newManifest = {};

	if (append && ! origManifestContents[manifestPath]) {
		try {
			var content = fs.readFileSync(manifestPath, {encoding: 'utf8'});
			origManifestContents[manifestPath] = JSON.parse(content);
		} catch (e) {
			origManifestContents[manifestPath] = {};
		}
	}

	function deleteOldFiles(oldFiles, newFiles, dirPath) {
		for (var prop in oldFiles) {
			if (newFiles.hasOwnProperty(prop) === false || oldFiles[prop] !== newFiles[prop]) {
				try {
					fs.unlinkSync(path.join(dirPath, oldFiles[prop]));
				} catch (e) {
					console.warn(e.message);
				}
			}
		}
	}

	return through2.obj(
		function(file, enc, cb) {
			if (typeof file.origPath !== 'undefined') {
				var manifestSrc = formatManifestPath(file.origPath);
				var manifestDest = formatManifestPath(file.relative);
				newManifest[manifestSrc] = manifestDest;
			}

			cb();
		},

		function(cb) {
			var finish = function (data) {
				if (deleteOld) {
					deleteOldFiles(origManifestContents[manifestPath], data, sourceDir);
				}

				origManifestContents[manifestPath] = data;

				this.push(new gutil.File({
					path: manifestPath,
					contents: new Buffer(JSON.stringify(origManifestContents[manifestPath], undefined, space))
				}));

				cb();
			}.bind(this);

			if (append) {
				appendQueue.then(new Promise(function(resolve) {
					finish(assign({}, origManifestContents[manifestPath], newManifest));
					resolve();
				}));
			} else {
				finish(newManifest);
			}
		}
	);
}
```
- example usage
```shell
...

'''javascript
var hash = require('gulp-hash');

gulp.src('./js/**/*.js')
	.pipe(hash()) // Add hashes to the files' names
	.pipe(gulp.dest('public/js')) // Write the renamed files
	.pipe(hash.manifest('assets.json', {
	  deleteOld: true,
	  sourceDir: __dirname + '/public/js'
	})) // Switch to the manifest file
	.pipe(gulp.dest('public')); // Write the manifest file
'''

The "manifest" is a JSON file that maps the original filenames to the renamed ones.
...
```

#### <a name="apidoc.element.gulp-hash.resetManifestCache"></a>[function <span class="apidocSignatureSpan">gulp-hash.</span>resetManifestCache ()](#apidoc.element.gulp-hash.resetManifestCache)
- description and source-code
```javascript
resetManifestCache = function () {
	origManifestContents = {};
}
```
- example usage
```shell
n/a
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
