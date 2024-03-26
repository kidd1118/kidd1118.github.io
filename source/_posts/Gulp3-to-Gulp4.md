---
title: 將 Gulp3 升級至 Gulp4 之路
date: 2022-08-24 15:48:38
categories: 軟體開發
tags: Gulp
---

## 本文章可以幫你
1. 使用NVM做nodeJS版本控管
2. 將Gulp3升級至Gulp4
3. 排除遇到的雷

## 安裝NVM與使用

1. 先安裝[nvm windows](https://github.com/coreybutler/nvm-windows/releases/tag/1.1.7)
2. 簡單的nvm指令
   ``` batch
    nvm install [node version]
    nvm list
    nvm use [node version]
   ``` 

## 安裝Gulp到NVM

NVM會將node_modules裝到該NVM/[node version]目錄下，不是原本預設的 C:\Users\[user name]\AppData\Roaming\npm，因此需要重新安裝npm install gulp -g 

![](capture01.png)

## 安裝Gulp4後會需要修改的寫法

1. 將 "run-sequence"改為gulp.series與gulp.parallel
   - gulp3
   ``` javascript
    gulp.task("my-task", function() {
        runSequence(
            "my-task1",
            "my-task2",
            [
                "my-task3",
                "my-task4"
            ],
            function() {
                console.log("run");
            }
        );
    });
   ```
   - gulp4
   ``` javascript
    gulp.task("my-task",
        gulp.series(
            "my-task1",
            "my-task2",
            gulp.parallel(
                "my-task3",
                "my-task4"
            ),
            function(cp) {
                console.log("run");
                cp();
            }
        )
    );
   ```
2. 將gulp.run改為tast() 
   - gulp3
   ``` javascript
    gulp.run("my-task", function() {});
   ```
   - gulp4
   ``` javascript
    gulp.task("my-task", function() {});
   ```
3. 回傳值需要加上return
   - gulp3
   ``` javascript
    gulp.task("my-task", function() {
        gulp.src(["./src"]).pipe(gulp.dest("build"));
    });
   ```
   - gulp4
   ``` javascript
    gulp.task("my-task", function() {
        return gulp.src(["./src"]).pipe(gulp.dest("build"));
    });
   ```
4. 呼叫task的時候需要預先定義好，因此需要把呼叫端方到後方
5. sass呼叫需要修改
   - gulp3
   ``` javascript
    const sass = require("gulp-sass");
    sass.compiler = require("sass");
   ```
   - gulp4
   ``` javascript
    const sass = require("gulp-sass")(require("sass"));
   ```