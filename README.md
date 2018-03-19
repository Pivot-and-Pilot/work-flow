# Pivot and Pilot development setup

## Setup Wordpress starter theme with underscore 
1. Download latest Wordpress version <a href="https://en-ca.wordpress.org/txt-download/" target="_blank">here</a>.
2. Download starting underscore theme <a href="https://underscores.me/" target="_blank">here</a>. Choose "_sassify!" box in advanced options to use Sass. Other information is optional.
3. Put the downloaded underscore them into theme folder of Wordpress.
4. Setup new database in phpmyadmin.
5. Install Wordpress and activate the theme.
6. In the root directory of the theme, make a folder named "src" with 2 sub folders named "scripts" and "styles". 
7. Make a folder named "css" in the "styles" folder above.
8. Put all the .js files in the "scripts" folder above.
9. Make a folder named "build" with 2 sub folders "scripts" and "styles", the concatted and minified .js and .css files will be found here.


## Setup workflow
- Install <a href="https://www.npmjs.com/get-npm" target="_blank">npm</a>.
- Install <a href="https://github.com/gulpjs/gulp/blob/v3.9.1/docs/getting-started.md" target="_blank">Gulp</a>.
- Make gulpfile.js in root directory of the theme folder. Copy the code below into gulpfile.js or refer to <a href="https://www.cssigniter.com/use-sass-gulp-wordpress-theme-plugin-development-workflow/" target="_blank">this link</a>.
- Change the name of the project in the proxy.
  ```
  const gulp = require('gulp');
  const plumber = require('gulp-plumber');
  const sass = require('gulp-sass');
  const postcss = require('gulp-postcss');
  const autoprefixer = require('autoprefixer');
  const groupmq = require('gulp-group-css-media-queries');
  const bs = require('browser-sync');
  const concat = require('gulp-concat');
  const uglify = require('gulp-uglify-es').default;
  const minify = require('gulp-minify-css');
  const imagemin = require('gulp-imagemin');

  /**
   * Compile Sass files
   */
  gulp.task('compile:sass', () =>
    gulp
      .src('sass/**/*.scss') // Grab all sass files in sass folder
      .pipe(plumber()) // Prevent termination on error
      .pipe(
        sass({
          indentType: 'tab',
          indentWidth: 1,
          outputStyle: 'expanded' // Expanded so that our CSS is readable
        })
      )
      .on('error', sass.logError)
      .pipe(
        postcss([
          autoprefixer({
            browsers: ['last 2 versions'],
            cascade: false
          })
        ])
      )
      .pipe(groupmq()) // Group media queries!
      .pipe(gulp.dest('./src/styles/css')) // Output compiled files in the CSS folder
      .pipe(bs.stream())
  ); // Stream to browserSync

  /**
   * Start up browserSync and Watch Sass files for changes
   */
  gulp.task('watch:sass', ['compile:sass'], () => {
    bs.init({
      proxy: 'http://localhost:8888/soberlife', //change the name of the project here
    });

    gulp.watch('sass/**/*.scss', ['compile:sass']);
  });
  
  gulp.task('js', function(){
   gulp.src('src/scripts/*.js')
   .pipe(concat('final-script.js'))
   .pipe(uglify())
   .pipe(gulp.dest('build/scripts/')); // the final minified and concatted .js file will be found here
  });

  gulp.task('css', function(){
     gulp.src('src/styles/css/*.css')
     .pipe(concat('final-styles.css'))
     .pipe(minify())
     .pipe(gulp.dest('build/styles/')); // the final minified and concatted .css file will be found here
  });
  
  gulp.task('img', function() {
  gulp.src('img/src/*.{png,jpg,gif}')
  .pipe(imagemin({
    optimizationLevel: 7,
    progressive: true
  }))
  .pipe(gulp.dest('img'))
  });

  /**
   * Default task executed by running `gulp`
   */
  gulp.task('default', ['watch:sass', 'css', 'img'], function () {
    // watch for CSS changes
    gulp.watch('src/styles/css/*.css', function() {
      // run css upon changes
      gulp.run('css');
    });
    // watch for images changes
    gulp.watch('img/src/*.{png,jpg,gif}', function() {
      gulp.run('img');
    });
  });
  gulp.task('build', ['js']);
  ```
- On Terminal(MacOS)/Command Line(Windows) run:
  ```
  npm init
  ```
  and go through the setup process. Then run the commands below to install the dependencies:
  ```
  npm i --save-dev gulp gulp-group-css-media-queries gulp-plumber gulp-postcss gulp-sass autoprefixer browser-sync gulp-uglify-es gulp-minify-css gulp-concat gulp-imagemin
  ```
- Run ```gulp``` to start BrowserSync and start developing.
- Enquere the final .css and .js files in function.php.
## Setup Git/Github Repo
1. Make .gitignore in the theme folder and include:
```
node_modules/
.npm-debug.log
tmp/
.sass-cache
*.css.map
DS_STORE
build/
img/src
```  
