###如何使用gulp给资源文件加上版本 [github](https://github.com/sofichael/gulp-rev)
###### １．package.json文件部分插件 [plugins](http://gulpjs.com/plugins/)
```json
  "dependencies": {
    "del": "^1.1.1",
    "gulp": "^3.8.11",
    "gulp-autoprefixer": "^2.1.0",
    "gulp-cache": "^0.2.8",
    "gulp-clean": "^0.3.1",
    "gulp-concat": "^2.5.2",
    "gulp-imagemin": "^2.2.1",
	...
  }
```
###### ２．安装所需的gulp插件
```shell
$ npm install
```
###### ３．gulpfile.js
```javascript
'use strict';
var gulp = require('gulp');
/*global -$ 加载需使用的gulp插件*/
var $ = require('gulp-load-plugins')();
```
- 配置选项(也可以直接在任务中写相对路径)
```javascript
/*
  *管理资源文件路径集合
  *config.static下
  *css scripts images 替换为自己的路径(可按照此配置管理)
 */
var config = {};
// 源资源文件路径
config['static'] = {
    styles: 'static/styles/*.*',
    scripts: 'static/scripts/*.*',
    images: 'static/images/*.*',
    html:'static/*.html'
};
...
```
- 图片任务流处理方法
```javascript
/*
 *images 任务流
 */
gulp.task('images', function () {
    return gulp.src(config['static'].images)
        .on('error', function (err) {
            console.error('Error!', err.message);
         })
        .pipe($.imagemin({
            distgressive: true,
            progressive: true,
            interlaced: true,
            svgoPlugins: [{removeViewBox: false}],
            use: [require('imagemin-pngquant')()]
        }))
        .pipe(gulp.dest(config['rev'].images))
        .pipe($.notify({ message: 'images task complete' }));
});
/*
 *img 添加版本任务流
 *use gulp-rev to version the rev files and generate the 'rev-manifest.json' file
 */
gulp.task('img', ['images'], function(){
    return gulp.src(config['rev'].images_file)
        .pipe($.rev())
        .pipe(gulp.dest(config['dist'].images))
        .pipe($.rev.manifest({
            base: 'dist',
            merge: true //如果存在 rev-manifest.json文件则合并
        }))
        .pipe(gulp.dest('dist'));
});
```
- css任务流处理方法
- js任务流处理方法
- 加入版本任务流处理方法
```javascript
gulp.task('rev',['img','css','js'], function () {
    gulp.src(['rev-manifest.json', config['static'].html])
        .pipe( $.revCollector({
            replaceReved: true,
            dirReplacements: {
                //路径替换
                // 'css': '/dist/css',
                // '/js/': '/dist/js/',
                // 'cdn/': function(manifest_value) {
                //     return '//cdn' + (Math.floor(Math.random() * 9) + 1) + '.' + 'exsample.dot' + '/img/' + manifest_value;
                // }
            }
        }) )
        .pipe($.minifyHtml({conditionals: true, loose: true}))
        .pipe(gulp.dest('dist'));
    gulp.task('del', require('del')('rev'));//最后删除过渡文件目录
});
```
- 开启任务
```javascript
gulp.task('test', ['clean'], function () {
  gulp.start('rev');
});
```

