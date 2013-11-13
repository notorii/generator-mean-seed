# File Structure, Key Files

### Notes		
1. The backend and frontend are separated. Frontend files are located in the `app/src` folder. Everything else is considered the backend, with the exception of:
	1. `Gruntfile.js`. This builds files for both the backend and frontend, since you only want to have to run it once.
	2. The `configs` folder. These files contain properties for both the backend and frontend.
	
	
## Backend / Node.js / root
- app	Holds backend AND frontend code.
	- src Frontend code. See below.
	- configs		Holds predefined configuration(s).
		- config.json	Example configuration(s) to be copied to the root directory and then used by the app.
		- config.test.json	Configuration for the automated tests
	- modules	Holds modularized code pieces. The bulk of the files are here.
		- controllers	Holds route / api controller files and their associated model files and tests. Most new files will go here.
			- [module_name]	Folder for this module (controller + model + tests)
				- [module_name].api.js		The controller/router.
				- [module_name].js		The model (where the bulk of the functions / code goes). This interacts with the database.
				- [module_name].test.js		The tests for this route/api call.
		- services	Holds libraries of common helper functions. These don't interact with api's or routes. This is only for app specific modules - ideally libraries should be built (and published) as public npm modules and referenced that way so ideally this folder should be pretty small.
	- routes
		- api	RPC api route setup
			- rpc	RPC specific files
				- api-help.html		The auto-generated, interactive help page for the api reference.
				- index.js
			- base.js
			- index.js		Main entry file and router for api. Used to set (a) new route endpoint(s).
		- index.js	This serves index.html, which in turn serves all frontend files.
	- test		The backend test core files.
		- all.spec.js		The main test file, which includes other modularized test files.
		- apiTestHelpers.js		Common functions for setup and running backend tests.
	- database.js
	- db_schema.json		Used as documentation for the db schema and also by database.js to form the collections interface, for use with db.[collection].[method].
	- depdendency.js	Holds paths to backend folders so modules can be require'd in each file as needed without hardcoding "../" in each file.
	- index.js
	- server.js
- config_environment.json	[optional] Holds the environment configuration that will be used to determine which `app/configs` config file to use. This file should by copied from `app/config_environment.json` if it is used. It is not tracked in version control.
- .gitignore
- Gruntfile.js	Grunt tasks. Build configuration for linting, testing, concating, minifying, generating documentation
- package.json	Used for npm install
- README.md
- run.js	This is the node.js entry script. Run `node run.js` from the command line in the root app directory to start the server.
- yuidoc-backend.json	Config for YUI auto docs for backend.
- yuidoc-frontend.json	Config for YUI auto docs for frontend.


## Frontend / AngularJS

### Summary

Based (almost exactly) off AngularUI. It does NOT follow the Angular-Seed folder structure.
Files are organized in a modular way. Each "component" is a folder containing all the necessary javascript, css/less, html/template/partial, test and any other resources (images, etc.) necessary for that component. Anything that's a shared resource that can't be fully modularized goes in the `common` folder.

### File / folder structure

- ie.html	HTML page for Internet Explorer not supported page/message/redirect
- ie.css	Styles for Internet Explorer not supported page
- index.html	Main entry point for ALL pages (Angular is a Single Page App architecture). This bootstraps the app (loads Angular and all other css and javascript resources) and sets up the main layouts (header, content - `ng-view`, footer).
- index*.html		Grunt template files and generated files for the main index.html entry point to the entire Angular app. ONLY edit `-grunt` suffix files as the rest are GENERATED by Grunt.
- build All grunt generated production (concatenated, minified) files. INCLUDES css and any other production generated files (again mimics AngularUI)
	- temp Temporary build files (could potentially auto delete them after the build process but you can just ignore these)
- common All common files - just like a module but for shared / global stuff
	- font		Holds all font files (including font-icons)
	- img	Image files
	- js
		- app.js Main/entry AngularJS file that loads modules and declares routes
	- less	LESS (CSS pre-processor) files. This is where all the COMMON/global classes are declared. Read through and memorize these as you'll use them often!
		- mixins		Custom defined LESS mixins that along with LessHat mixins can/should be used for writing LESS styles for other (modularized) pages/views/directives.
		- variables		Custom defined LESS variables that can/should be used for writing LESS styles for other (modularized) pages/views/directives.
			- colors.less		Holds the themes/colors for this app. You should almost always be using a pre-defined color variable; do NOT use a hardcoded color code (i.e. `#ff00aa` or `rgb(100,100,150)` or `blue`) if you can avoid it. Keep things referencing common/global variables and mixins so it's easy to change later (this applies to things outside of just colors as well).
- config
	- buildfilesModules.json	Holds all the javascript, less, and html files for use in the build process (i.e. to generate final files in the `build` folder). You will change this file often (though the yeoman generator will auto-update it for you!).
	- buildfilesModuleGroups.json	Pairs with buildfilesModules.json
	- karma.conf-grunt.js	Holds Karma testing configuration. This file generates karma.conf.js
	- karma.conf.js	NEVER edit this file directly - it's auto generated by grunt
	- protractor.conf-grunt.js	Holds Protractor E2E testing configuration. This file generates protractor.conf.js
	- protractor.conf.js	NEVER edit this file directly - it's auto generated by grunt
	- protractor	Put protractor E2E test configurations in here if you have more than one
- lib ALL 3rd party code and dependencies that are NOT Bower supported - including css, images, etc. NO 3rd party code should be anywhere but in this folder and the bower_components folder.
- modules
	- directives
		- appalert
		- login
		- signup
		- socialAuth
	- services		Angular services (models/data and libraries of common functions). The important ones are listed below (not an exhaustive list).
		- auth		Authentication (the `app.js` router calls this function before each page load to see if the user is logged in or not).
		- config
			- config-grunt.js		Template file for generating config.js. This holds common config files used througout the app.
			- config.js		Do NOT edit this file - it's generated by grunt. Edit config-grunt.js instead.
		- http		$http wrapper for AJAX (backend api) requests.
		- models	Where all data services go
			- user
				- UserModel.js		Holds the information/data on the currently logged in user.
		- nav		Sets the navigation (header and footer) for each page.
		- socialAuth	Service to interface with Facebook, Google, etc. login
		- storage	Persistent, client side (local)storage.
	- pages	Holds Angular javascript controllers and tests, html partials, less/css styles all in one folder as a modular component. The important/common folders are listed/detailed below (not an exhaustive list).
		- layout		The main layout file for the entire app (referenced in `index.html`). This is a parent controller to ALL page controllers so all page controllers inherit from LayoutCtrl. Thus it defines some common variables on the $scope such as `$scope.appPath` that can be used everywhere else. It also does some basic layout and authentication/login handling.
		- design
		- footer
		- header
		- home
		- login
		- logout
		- passwordReset
		- signup
		- userDelete
		- [page_name]
			- [page-name].less
			- [page-name].html
			- [PageName]Ctrl.js
			- [PageName]Ctrl.spec.js
- test	Some more Karma/Protractor setup / config
	- e2e	Holds all Protractor E2E tests

	
### Main files
- index-grunt.html This builds index.html. Index.html is the main, and only, entry file to the AngularJS application. It includes all the resources (css, script files), sets up the divs for the header, content, & footer, and has the ng-view div that is where all the content goes.

- index-prod-grunt.html and other such files are specific index files for other things like the production build, phonegap app, etc.

- ie.html & ie.css are Internet Explorer redirects for older versions. These just display a page that says "IE is not supported, try another browser" with links to other browsers.


### Other key files
- common/js/app.js is the Angular router and module loader. Each time a new module (directive or service) is created, it needs to be added/defined at the top of this file. And similarly, to create a new page / route, define it here. Though Yeoman ng-route subgenerator will do this for you!

- common/less fold has all the LESS CSS files.


### Common / Non-modularized / Site-specific files
You'll likely have to edit these files at some point.
NOTE: this does NOT include files in the `common` directory, which obviously are also site-specific.
- `modules/services/nav/nav.js`

- `common` directory files most likely/often to be updated
	- js
		- app.js
	- less [every file in the this main folder plus the following sub-folders/files below]
		- variables
			- colors.less
	- config
		buildfilesModules.json
	- modules
		-services
			nav/nav.js