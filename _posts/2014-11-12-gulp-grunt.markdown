---
layout: post
title:  "Gulp & Grunt"
date:   2014-11-12 19:17:02
categories: Javascripts
---

Lorsqu'il s'agit d'optimiser ou de publier un site internet il y a une série de tâches qui revient inlassablement, grunt ou gulp nous permet de simplifier cette étape en automatisant ces opérations.

Sur le net on trouve une tâche pour quasiment tout:  minification, concaténation du js/css mais aussi des tâches qui compilent notre sass en css ou notre coffee script en javascript bref vous l'aurez compris c'est avant tout un outil de front-end

### Pourquoi Grunt ?

Grunt est vraiment très simple à prendre en main il suffit de créer un gruntFile.js et on est partie !!!

la plupart des gens comprendront rapidement le fichier .

petit exemple d'un gruntfile

{% highlight javascript %}
module.exports = function(grunt) {
  // Configuration de Grunt
    grunt.initConfig({
        mochaTest: {
            test: {
                options: {
                    reporter: 'spec',
                    captureFile: 'results.txt', // Optionally capture the reporter output to a file
                    quiet: false, // Optionally suppress output to standard out (defaults to false)
                    clearRequireCache: false // Optionally clear the require cache before running tests (defaults to false)
                },
                src: ['tests/**/*.js']
            }
        }
        copy: {
            main: {
                src: 'src/fonts',
                dest: 'build/fonts'
            },
        },
        inline: {
            dist: {
                src: ['src/index.html'],
                dest: ['build/']
            }
        }
    })

    // Import du package
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.registerTask('default', 'mochaTest');
    grunt.registerTask('dev', ['concat:dist', 'compass:dist', 'imagemin', 'inline:dist', 'copy:main', 'bower:install', 'coffee', 'concat:dist', 'uglify'])
{% endhighlight %}

pour enregistrer des tâches il suffit de les register à l'aide de "grunt.registerTask"

et dans votre console un petit grunt lancera vos tâches .

#### les défauts de Grunts

Les tâches de grunt sont lancés les une à la suite des autres ce qui peut être long surtout si vous utilisez un grunt watch pour lancer vos tâches à chaque fois que vous modifiez un fichier.

le gruntFile peut devenir super long à écrire et à lire (que des tableaux).

### Pourquoi Gulp ?

Gulp fait la même chose que grunt sauf qu'au lieu d'écrire de définir nos tâches dans des tableaux ont écrit du code !!!

l'avantage par rapport à grunt est sa rapidité d'exécution en effet il utilise nodeJs et peut donc lancer les tâches en parallèles les tâches peuvent aussi être écrient en coffee.
exemple: 

require 'coffee-script/register'
{% highlight javascript %}
gulp           = require('gulp')
pngquant       = require('imagemin-pngquant');
mainBowerFiles = require('main-bower-files');
$              = require('gulp-load-plugins')()
angularPath    = 'app/src/coffee/angular'
tasks          = ['coffee', 'concat', 'js-compress', 'bower', 'image', 'compass', 'css-concat', 'css-compress']

watch = ->
    gulp.watch angularFile, ['coffee', 'concat', 'ngAnnotate', 'js-compress', 'index-dev']
    gulp.watch 'app/src/scss/*.scss', ['compass', 'css-concat', 'css-compress', 'index-dev']

    gulp.watch 'app/src/images/*.png', ['image']
    gulp.watch 'app/src/images/*.jpg', ['image']
    gulp.watch 'app/src/images/*.gif', ['image']
    gulp.watch 'app/src/partials/*.html', ['index-dev']
    gulp.watch 'bower.json', ['bower', 'index-dev']

gulp.task 'coffee', ->
  gulp.src angularFile 
    .pipe $.coffee bare: true 
        .on 'error', $.util.log
    .pipe gulp.dest 'app/src/.compile/js'


#js concatenation
gulp.task 'concat', ['coffee'], ->
    gulp.src 'app/src/.compile/js/*.js'
    .pipe $.concat 'main.js' 
    .pipe gulp.dest 'public/javascripts'

#js compression
gulp.task 'js-compress', ['ngAnnotate'], ->
    gulp.src 'public/javascripts/main.js'
    .pipe $.uglify()
    .pipe gulp.dest 'public/javascripts'
    .pipe $.connect.reload()

#compass
gulp.task 'compass', ->
    gulp.src 'app/src/scss/*.scss'
        .pipe $.sass
            onError: console.error.bind(console, 'SASS error:')
        .pipe $.autoprefixer
            browsers: browsers 
        .pipe gulp.dest 'app/src/.compile/css'
        .pipe $.size()

gulp.task 'watch-dev', ['connect-dev'], ->
    gulp.watch 'app/src/*.html', ['html-dev']
    watch()

gulp.task 'default', tasks
{% endhighlight %}



