---
title: gulp 前端自動化
date: 2019-02-18 16:11:04
tags: gulp、browser-sync、gulp-rename、SASS/SCSS、gulp-autoprefixer
---

### 相關工具、套件、技術
*gulp、browser-sync、gulp-rename、SASS/SCSS、gulp-autoprefixer*

### 目的
> 為求開發時，簡化重複的流程、建立一個良好的本機開發環境

### 步驟

1. 安裝 node js
   > [https://nodejs.org/en/](https://nodejs.org/en/)
2. 安裝全域 gulp
   ``` bash
    npm install -g gulp
   ```
3. 初始化專案，產生 package.json
   ``` bash
    npm init
   ```
4. 安裝本地所需套件
   ``` bash
    // 安裝本地 gulp
    npm install gulp --save

    // 安裝 sass 及 自動補前綴樣式的套件
    npm install gulp-sass gulp-autoprefixer --save

    // 安裝重新命名的套件
    npm install gulp-rename --save

    // 安裝建立本機伺服器及自動重整瀏覽器用的套件
    npm install browser-sync --save
   ```
5. 在根目錄新增 gulpfile.js
   ``` javascript
    var gulp = require('gulp');
    var sass = require('gulp-sass');
    var cleanCss = require('gulp-clean-css');
    var prefix = require('gulp-autoprefixer');
    var browserSync = require('browser-sync');
    var rename = require('gulp-rename');

    var scss = {
      output: {
        outputStyle: 'expanded'
        // outputStyle: 'compressed'
      },
      prefix: {
        browsers: ['last 2 versions']
      }
    }

    var minify = {
      compatibility: 'ie8'
    }

    gulp.task('scss', function() {
      gulp.src('scss/style.scss') // 來源檔案
        .pipe(sass(scss.output)) // 將scss編譯成css
        .pipe(prefix(scss.prefix)) // prefix()加入樣式前綴，()內不加屬性則使用autoprefix的預設值
        .pipe(gulp.dest('./style')) // 輸出位置
        .pipe(rename('style.min.css')) // 重新命名css
        .pipe(cleanCss(minify)) // 壓縮css
        .pipe(gulp.dest('./style')) // 輸出位置
    });

    gulp.task('browser-sync', function() { // 建立localhost伺服器
      browserSync.init(["style/*.css", "js/*.js"], {
        server: {
          baseDir: "./"
        }
      });
    });

    gulp.task('watch', function () { // 存檔就刷新瀏覽器
      gulp.watch("scss/*.scss", ['scss']).on('change', browserSync.reload);
      gulp.watch("./*.html").on('change', browserSync.reload);
    });

    gulp.task('default', ['watch', 'scss', 'browser-sync']);

   ```
6. 在根目錄新增 index.html 及 scss 資料夾內新增 style.scss
    >
      gulp-demo(根目錄)
      |--scss
      &nbsp;&nbsp;|-- style.scss
      |-- gulpfile.js
      |-- index.html
      |-- package-lock.json
      |-- package.json
    >
7. 執行 gulp，啟動伺服器
   ``` bash
    gulp
   ```
8. 開發網頁GO~
   > 當在 index.html 或是 style.scss 內有變更時(存檔)，瀏覽器會自動刷新
   > 如果要停止伺服器，則按下 ctrl + c (mac 的 ctrl 改為 cmd)
