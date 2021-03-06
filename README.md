
How to compile SASS in [Sails](http://sailsjs.org) without Ruby
==================================================
> Build: Sails 0.10.x

*This tasks differs from [sails101/using-sass](https://github.com/sails101/using-sass) in that it uses libsass, a C/C++ port of the Sass engine. This removes the need to have Ruby installed to use Sass. This does, however, lack features like Compass support*

## Sass task

Run this task with the grunt sass command.

Sass is a preprocessor that adds nested rules, variables, mixins and functions, selector inheritance, and more to CSS. Sass files compile into well-formatted, standard CSS to use in your site or application.

NOTE: Above text taken from [grunt-contrib-sass](https://www.npmjs.org/package/grunt-contrib-sass)

libsass.. is a Sass compiler in C++. In contrast to the original Ruby compiler, this one is much faster, but is missing some features, though improving quickly. It also doesn't support Compass. Check out grunt-contrib-sass if you prefer something more stable, but slower.

NOTE: Above text taken from [grunt-sass](https://www.npmjs.org/package/grunt-sass)

## Set up and Configure

* Create a new Sails app: `sails new path_to_app`
* Install dependency: `npm install --save grunt-sass`
* Add `importer.scss` file to the `assets` > `styles`
```
/**
 * importer.scss
 *
 * By default, new Sails projects are configured to compile this file
 * from SASS to CSS.  Unlike CSS files, SASS files are not compiled and
 * included automatically unless they are imported below.
 *
 * The SASS files imported below are compiled and included in the order
 * they are listed.  Mixins, variables, etc. should be imported first
 * so that they can be accessed by subsequent SASS stylesheets.
 *
 * (Just like the rest of the asset pipeline bundled in Sails, you can
 * always omit, customize, or replace this behavior with SASS, SCSS,
 * or any other Grunt tasks you like.)
 */

@import 'styles.scss';

// For example:
//
// @import 'foundation.scss';
//
// etc.
```
* Add `sass.js` file to `tasks` > `config`, add config settings for SASS:
```javascript
module.exports = function(grunt) {

	grunt.config.set('sass', {
		dev: {
			files: [{
				expand: true,
				cwd: 'assets/styles/',
				src: ['importer.scss'],
				dest: '.tmp/public/styles/',
				ext: '.css'
			}]
		}
	});

	grunt.loadNpmTasks('grunt-sass');
};
```
* Edit `copy.js` in `tasks` > `config`, add file copy exclusions for SASS and SCSS:
```javascript
module.exports = function(grunt) {

	grunt.config.set('copy', {
		dev: {
			files: [{
				expand: true,
				cwd: './assets',
				// Before: src: ['**/*.!(coffee|less)'],
				src: ['**/*.!(coffee|less|scss|sass)'],
				dest: '.tmp/public'
			}]
		},
		build: {
			files: [{
				expand: true,
				cwd: '.tmp/public',
				src: ['**/*'],
				dest: 'www'
			}]
		}
	});

	grunt.loadNpmTasks('grunt-contrib-copy');
};

```
* Add line `sass:dev` to `register` > `compileAssets.js`:
```javascript
module.exports = function (grunt) {
	grunt.registerTask('compileAssets', [
		'clean:dev',
		'jst:dev',
		'less:dev',
		'sass:dev', <-- Add that
		'copy:dev',
		'coffee:dev'
	]);
};
```
* Add line 'sass:dev' to `register` > `syncAssets.js`:
```javascript
module.exports = function (grunt) {
	grunt.registerTask('syncAssets', [
		'jst:dev',
		'less:dev',
		'sass:dev', <-- Add that
		'sync:dev',
		'coffee:dev'
	]);
};
```

## Done!
That's it! On server start with `sails lift`, the SCSS imported to `importer.scss` will compile and create `importer.css`:

```
/* importer.scss
 *
 * By default, new Sails projects are configured to compile this file
 * from SASS to CSS.  Unlike CSS files, SASS files are not compiled and
 * included automatically unless they are imported below.
 *
 * The SASS files imported below are compiled and included in the order
 * they are listed.  Mixins, variables, etc. should be imported first
 * so that they can be accessed by subsequent SASS stylesheets.
 *
 * (Just like the rest of the asset pipeline bundled in Sails, you can
 * always omit, customize, or replace this behavior with SASS, SCSS,
 * or any other Grunt tasks you like.)
 */
body {
  font: 100% Helvetica, sans-serif;
  color: #333333; }
```
