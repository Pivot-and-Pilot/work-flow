# Pivot and Pilot development setup

## Setup Wordpress starter theme with underscore 
1. Download latest Wordpress version <a href="https://en-ca.wordpress.org/txt-download/" target="_blank">here</a>.
2. Download starting underscore theme <a href="https://underscores.me/" target="_blank">here</a>. Choose "_sassify!" box in advanced options to use Sass. Other information is optional.
3. Put the downloaded underscore them into theme folder of Wordpress.
4. Setup new database in phpmyadmin.
5. Install Wordpress and activate the theme.

## Setup workflow
- Install <a href="https://www.npmjs.com/get-npm" target="_blank">npm</a>.
- Install <a href="https://github.com/gulpjs/gulp/blob/v3.9.1/docs/getting-started.md" target="_blank">Gulp</a>.
- Make gulpfile.js in root directory of the theme folder. Copy the code below into gulpfile.js or refer to <a href="https://www.cssigniter.com/use-sass-gulp-wordpress-theme-plugin-development-workflow/" target="_blank">this link</a>.
  ```
  const gulp = require('gulp');
  const plumber = require('gulp-plumber');
  const sass = require('gulp-sass');
  const postcss = require('gulp-postcss');
  const autoprefixer = require('autoprefixer');
  // const sourcemaps = require('gulp-sourcemaps');
  const groupmq = require('gulp-group-css-media-queries');
  const bs = require('browser-sync');

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
      .pipe(gulp.dest('./css')) // Output compiled files in the CSS folder
      .pipe(bs.stream())
  ); // Stream to browserSync

  /**
   * Start up browserSync and Watch Sass files for changes
   */
  gulp.task('watch:sass', ['compile:sass'], () => {
    bs.init({
      proxy: 'http://localhost:8888/soberlife',
      // proxy: {
      //   target: 'http://localhost:8888/mariadecotiis',
      //   middleware: [
      //     function (req, proxyRes, next) {
      //       proxyRes.setHeader('Access-Control-Allow-Origin', 'Origin, X-Requested-With, Content-Type, Accept');
      //       proxyRes.setHeader('connection', 'open');
      //       next();
      //   }]
      // }
    });

    gulp.watch('sass/**/*.scss', ['compile:sass']);
  });

  /**
   * Default task executed by running `gulp`
   */
  gulp.task('default', ['watch:sass']);

  ```
- On Terminal(MacOS)/Command Line(Windows) run:
  ```
  npm init
  ```
  and go through the setup process. Then run the commands below to install the dependencies:
  ```
  npm i --save-dev gulp
  npm i --save-dev gulp-group-css-media-queries 
  npm i --save-dev gulp-plumber
  npm i --save-dev gulp-postcss
  npm i --save-dev gulp-sass
  npm i --save-dev autoprefixer
  npm i --save-dev browser-sync
  ```
  
## Setup Git/Github Repo
1. Make .gitignore in the theme folder and include:
  - node_modules
  - .npm-debug.log
  - tmp
  - .sass-cache
  - *.css.map
  - DS_STORE
