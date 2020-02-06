# Using NPM Scripts as a build tool

* Download the repository.
* From the *src* folder open the *index.html* page in a browser and check it works (it is a very simply HTML page with some attached CSS and JavaScript).

> A note about 'slash' characters. When using the Command Prompt or terminal, most operating systems use a forward slash between filenames e.g. *node_modules/bin/lessc*. Windows use a backslash i.e. *node_modules\bin\lessc*. The backslash character is also the escape character so if we have a file path as part of a string we write "node_modules\\\\bin\\\\lessc". The following instructions are written for windows. If you are using a Mac, use single forward slashes instead.

> A note about local and global NPM packages. It is consider good practice to install packages locally. However, at the University, I have had sometimes had problems doing this.

## Setting up a Node.js project
* Using the Node.js Command Prompt navigate to the root of the project folder (where the README.md file is).

* Create a new Node.js project:

```
npm init -y
```
Open the *package.json*. This is the configuration file for the project.

You have now set up a Node.js project.

## Running SASS from the command line

Next we will install *node-sass* so that we can compile sass code in our project. Enter the following:

```
npm install node-sass -g

```

This installs the *node-sass* package.

Check this has worked by doing the following:

* In the Node.js command prompt, enter the following:

```
node-sass ./src/sass/style.scss ./src/css/style.css -w
```

* Open *src/sass/style.scss* in a text editor
* Make a simple change e.g. change the colour of the heading
* Look at the Command Prompt output, it should tell you it has generated a CSS file.
* Refresh the page in a browser to test this has worked.
* The -w flag specifies that the *style.scss* should be watched. If it changes, the sass file will be automatically compiled into CSS.
* Make another change to the sass file and make sure the changes are showing in the browser.

In the Command Prompt enter ctrl+c to cancel the watching.

## Using NPM scripts

* Now, instead of typing the *sass* command directly we are going to use an NPM script to run the command. Open the *package.json* file and change the script property so that looks like the following:

```json
  "scripts": {
    "sass":"node-sass ./src/sass/style.scss ./src/css/style.css -w"
  },
```

* Save *package.json*
* Back in the command prompt enter the following:

```
npm run sass
```

* You should get confirmation that your command has run.
* Make another change to your .scss file.
* Refresh the browser to check this has worked.


## Creating a build version
A build version is simply a version of your site that is ready to be used by end users. A build version of the site will differ from your developer version in a number of ways. For example, a build version will contain minified files and compressed images to make the pages load as fast as possible. We will do a simple example that minifies our CSS.

We are going to put our build version in a folder named *dist*.

Add the following *make-dist* script

```
"scripts": {
    "sass":"node-sass ./src/sass/style.scss ./src/css/style.css -w",
    "make-dist":"mkdir dist\\css"
  },
```

Open the command prompt and test it works:

```
npm run make-dist
```
You should find that a *dist* folder has been created in your project.
Next we will move the *index.html* page into the *dist* folder.
Add the following *move-html* script.

```json
  "scripts": {
    "sass":"node-sass ./src/sass/style.scss ./src/css/style.css -w",
    "make-dist":"mkdir dist\\css",
    "move-html":"copy src\\index.html dist"
  },
```

Run the script from the command line i.e.
```
npm run move-html
```

You should find that the *index.html* file has been moved into your *dist* folder. Next, we are going to put a minified copy of our CSS into the *dist* folder.

* Install the *clean-css* package to minify our CSS

```
npm install clean-css-cli -g
```

* Next, to see what it does, run it from the command prompt

```
cleancss  "./src/css/style.css" -o "./src/css/style.min.css"
```

* Open the file *style.min.css*, see how the CSS has been minified.

* Next, we'll add this as a script that will take the CSS file from the *src* folder, minify it and put it into the *dist* folder. Add the following *move-css* script to your *package.json* file.

```
  "scripts": {
    "sass":"node-sass ./src/sass/style.scss ./src/css/style.css -w",
    "make-dist":"mkdir dist\\css mkdir dist\\js",
    "move-html":"copy src\\index.html dist",
    "move-css": "cleancss  ./src/css/style.css -o dist/css/style.css"
  },
```

*move-css* is the new script that we have added. It runs *cleancss* to generate a minimised CSS file in the *dist* folder.

* Open up the command prompt run *move-css* i.e.
```
npm run move-css
```

* You should now have an optimised version of your site in the dist folder.

### Creating a clean script
When we create a new *dist* version of our site, we want to delete the existing one. We can do this with a *clean* script that will remove the *dist* folder.

Change the package.json file to add a clean script.
```
  "scripts": {
    "sass":"node-sass ./src/sass/style.scss ./src/css/style.css -w",
    "make-dist":"mkdir dist\\css",
    "move-html":"copy src\\index.html dist",
    "move-css": "cleancss  ./src/css/style.css -o dist/css/style.css",
    "clean":"rmdir dist\\css /s /q"
  },
```

* Open up the command prompt run the *clean* script.

* Finally we will add a *build* command that will run *make-dist*, *move-html* and then run *move-css*. The && symbol allows use to chain a series of commands together.

```
  "scripts": {
    "sass":"node-sass ./src/sass/style.scss ./src/css/style.css -w",
    "make-dist":"mkdir dist\\css",
    "move-html":"copy src\\index.html dist",
    "move-css": "cleancss  ./src/css/style.css -o dist/css/style.css",
    "clean":"rmdir dist /s /q",
    "build":"npm run make-dist && npm run move-html && npm run move-css"
  },

```
* Again, test this works e.g.
```
npm run build
```
* This single command should generate our user ready version of the site.

The above is a simple example, typically there would be lots more steps we would include in a build process e.g.
* Compressing image files e.g. image-min (https://github.com/imagemin/imagemin-cli)
* Linting, bundling and uglifying our JavaScript.

These are things you can explore in your assignment.

## Learning more
* https://css-tricks.com/why-npm-scripts/
* https://medium.freecodecamp.org/why-i-left-gulp-and-grunt-for-npm-scripts-3d6853dd22b8
* https://scotch.io/tutorials/using-npm-as-a-build-tool
* https://nervewax.com/npm-scripts-compiler/
