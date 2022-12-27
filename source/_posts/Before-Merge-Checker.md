---
title: 使用GitLab CI/CD在Merge前做預先檢查
date: 2022-08-24 17:54:59
categories:
tags: GitLab, CI/CD, ESLint, StyleLint
---

## 本文章可以幫你
1. GitLab Merge when pipleline succeeds設定
2. 使用GitLab CI/CD
3. 使用ESLint/StyleLint檢查程式碼

## 設定GitLab的Merge when pipeline succeeds/啟動runners

1. 官方文件可以參考 [site1](https://docs.gitlab.com/ee/user/project/merge_requests/merge_when_pipeline_succeeds.html)
   [site2](https://gitlab.ict888.net/help/ci/merge_request_pipelines/index.md#pipelines-for-merge-requests)
2. GitLab設定的位置[Repo] => Settings => General => Merge requests
    ![](capture01.png)
3. 記得啟動runners[Repo] => Settings => CI/CD => Runners
    ![](capture02.png)

## 在專案裡面新增GitLab CI comfig
1. 新增.gitlab-ci.yml並設定其內容
2. 設定workflow讓pipeline只在Merge Request Contenx建立的時候才執行
``` yml
workflow:
    rules:
        - if: $CI_MERGE_REQUEST_ID # Execute jobs in merge request context
        - if: $CI_COMMIT_BRANCH == 'dev' # Execute jobs when a new commit is pushed to dev branch

stages:
  - prepare
  - check
  - build
  - deploy

cache:
  paths:
    - node_modules/

install:
  stage: prepare
#   only: => going to be deprecated，根據gitloab 11.x doc，即將被拋棄
#     - dev
  script:
    - npm install

eslint:
  stage: check
#   only: => going to be deprecated，根據gitloab 11.x doc，即將被拋棄
#     - dev
  dependencies:
    - prepare
  script:
    - eslint src/*.ts

stylelint:
  stage: check
#   only: => going to be deprecated，根據gitloab 11.x doc，即將被拋棄
#     - dev
  dependencies:
    - prepare
  script:
    - stylelint src/css/**/*.scss

build:
  stage: build
#   only: => going to be deprecated，根據gitloab 11.x doc，即將被拋棄
#     - dev
  dependencies:
    - prepare
  script:
    - npm run build

deploy:
  stage: deploy
  script: ./deploy
  rules:
    - if: $CI_COMMIT_BRANCH == 'dev' # Execute jobs when a new commit is pushed to dev branch
```

## 設定eslint/stylelint

1. 安裝eslint與設定
   - install packages
   ``` batch
    npm install eslint eslint-config-prettier eslint-plugin-prettier @typescript-eslint/eslint-plugin @typescript-eslint/parser
   ```
   - .eslintrc.js
   ``` javascript
    module.exports = {
        env: {
            browser: true,
            es6: true
        },
        extends: [
            // Uses the recommended rules from the @typescript-eslint/eslint-plugin
            "plugin:@typescript-eslint/recommended",
            // Uses eslint-config-prettier to disable ESLint rules from @typescript-eslint/eslint-plugin that would conflict with prettier
            "prettier/@typescript-eslint",
            // Enables eslint-plugin-prettier and eslint-config-prettier. This will display prettier errors as ESLint errors.
            // Make sure this is always the last configuration in the extends array.
            "plugin:prettier/recommended"
        ],
        globals: {
            Atomics: "readonly",
            SharedArrayBuffer: "readonly"
        },
        parser: "@typescript-eslint/parser",
        parserOptions: {
            ecmaVersion: 2018,
            sourceType: "module"
        },
        plugins: ["@typescript-eslint"],
        rules: {
            "prettier/prettier": ["error"],
            "@typescript-eslint/no-explicit-any": "off",
            "@typescript-eslint/interface-name-prefix": "off"
        }
    };
   ```
   - 使用
   ``` batch
    eslint src/js/**/*.ts --fix
   ```
2. 安裝stylelint與設定
   - install packages
   ``` batch
    npm install stylelint stylelint-config-prettier stylelint-config-sass-guidelines stylelint-config-standard stylelint-order stylelint-scss
   ```
   - .stylelintrc.js
   ``` javascript
    module.exports = {
        extends: ["stylelint-config-standard", "stylelint-config-sass-guidelines"],
        rules: {
            indentation: 4,
            "selector-max-id": null,
            "max-nesting-depth": null,
            "function-url-quotes": null,
            "selector-class-pattern": null,
            "selector-max-compound-selectors": null,
            "selector-max-compound-selectors": null,
            "selector-max-compound-selectors": null,
            "selector-no-qualifying-type": null,
            "no-descending-specificity": null,
            "declaration-block-no-duplicate-properties": null,
            "function-linear-gradient-no-nonstandard-direction": null,
            "selector-id-pattern": null,
            "font-family-no-missing-generic-family-keyword": null,
            "function-no-unknown": null,
            "max-line-length": null
        }
    };
   ```
   - 使用
   ``` batch
    stylelint src/css/**/*.scss --fix
   ```

## 前置作業設定好了，試試看吧!

1. 執行後，可以從[Repo] => CI/CD => Piplelines/Jobs去看結果
    ![](capture03.png) 
    ![](capture04.png) 
2. 設定eslint error level去測試錯誤結果
   ![](capture05.png) 
   commit後pipeline因為eslint error跑失敗
   ![](capture06.png) 
   開了merge request會無法merge，需要先修正pipeline的錯誤
   ![](capture07.png) 

## [Enhancement]修改為當workflow成立時才可以Merge
1. 將.gitlab ci config加上workflow rules
   ``` yml
    workflow:
        rules:
            - if: $CI_MERGE_REQUEST_ID # Execute jobs in merge request context
            - if: $CI_COMMIT_BRANCH == 'dev' # Execute jobs when a new commit is pushed to dev branch
    ```
2. 如此一來當在gitlab上開Merge request時，會自動跑CI/CD，成功後才可以去Merge
![](capture08.png)
![](capture09.png)

