---
title: 建立 Linter Configuration Packages in Private NPM
date: 2021-11-23 17:12:57
categories: 軟體開發
tags: NPM
---

## 本文章可以幫你
1. 使用prettier rule定義eslint config 
2. 改寫default prettier rule
3. 設定auto formatter by prettier in VS code setting 
4. 把config package publish到private NPM
5. 下載使用config package

## 使用prettier rule定義eslint config 

### 基本規劃
![](capture01.png)
1. 建立一個使用prettier的linter base
   ![](capture02.png)
   - .eslintrc.js
   ``` javascript
    module.exports = {
        env: {
            browser: true,
            es6: true
        },
        extends: [
            "plugin:prettier/recommended"
        ],
        globals: {
            Atomics: "readonly",
            SharedArrayBuffer: "readonly"
        },
        parser: "",
        parserOptions: {
            ecmaVersion: 2018,
            sourceType: "module"
        },
        plugins: ["eslint-plugin-prettier"],
        rules: {
            "prettier/prettier": ["error"]
        }
    };
   ``` 
   ## 改寫default prettier rule
   - .prettierrc.js
   ``` javascript
    module.exports = {
        semi: true,
        singleQuote: false,
        printWidth: 120,
        tabWidth: 4
    };
   ``` 
2. 根據不同程式語言或框架去定義不同的linter，以typescript為例
   ![](capture03.png)
   - .eslintrc.js
   ``` javascript
    module.exports = {
        env: {},
        extends: [
            // inculing eslint base for javascript
            "../eslint-config-base/.eslintrc.js",
            "plugin:@typescript-eslint/recommended",
            "prettier/@typescript-eslint"
        ],
        globals: {},
        parser: "@typescript-eslint/parser",
        parserOptions: {},
        plugins: [],
        rules: {
            "@typescript-eslint/no-explicit-any": "off"
        }
    };
   ``` 
    設定好可以使用以下command去撈出目前所有載入的規則
    ``` bash
    npx eslint --print-config path::String
    ``` 
    ![](capture07.png)
3. 測試rules
   ![](capture04.png)
   - 在package.json建立測試script
   ``` json
   {
        "name": "eslint-config-leoru",
        "main": ".eslintrc.js",
        "version": "0.0.1",
        "license": "MIT",
        "scripts": {
            "eslint-test-base": "./node_modules/.bin/eslint test/*.* --config eslint-config-base/.eslintrc.js",
            "eslint-test-js": "./node_modules/.bin/eslint test/*.js --config eslint-config-javascript/.eslintrc.js",
            "eslint-test-ts": "./node_modules/.bin/eslint ./test/typescript_test.ts --config eslint-config-typescript/.eslintrc.js",
            "eslint-test-vue2.0": "./node_modules/.bin/eslint test/*.vue --config eslint-config-vue2.0/.eslintrc.js",
            "eslint-test-vue3.0": "./node_modules/.bin/eslint test/*.vue --config eslint-config-vue3.0/.eslintrc.js"
        },
        "dependencies": {},
        "devDependencies": {
            "@typescript-eslint/eslint-plugin": "^2.3.1",
            "@typescript-eslint/parser": "^2.3.1",
            "typescript": "2.5.2",
            "eslint": "^6.4.0",
            "eslint-config-prettier": "^6.3.0",
            "eslint-plugin-prettier": "^3.1.1",
            "eslint-plugin-vue": "^8.1.1",
            "prettier": "^1.18.2"
        }
    }
   ``` 
   執行測試
   ``` bash
   npm run eslint-test-ts
   ```
   ![](capture05.png)

## 設定auto formatter by prettier in VS code setting 
在.vscode/setting.json設定
![](capture06.png)
``` json
{
    "eslint.alwaysShowStatus": true,
    "eslint.options": {
        "extensions": [".ts", ".js", ".vue"]
    },
    "eslint.validate": ["typescript", "javascript", "vue"],

    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
        "source.fixAll.eslint": true
    },
    "[javascript]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode",
        "editor.formatOnSave": true,
        "editor.codeActionsOnSave": {
            "source.fixAll.eslint": true
        }
    },
    "[typescript]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode",
        "editor.formatOnSave": true,
        "editor.codeActionsOnSave": {
            "source.fixAll.eslint": true
        }
    },
    "[vue]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode",
        "editor.formatOnSave": true,
        "editor.codeActionsOnSave": {
            "source.fixAll.eslint": true
        }
    },
    "[html]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode",
        "editor.formatOnSave": true
    },
    "[json]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode",
        "editor.formatOnSave": true
    },
    "[css]": {
        "editor.defaultFormatter": "esbenp.prettier-vscode",
        "editor.formatOnSave": true
    }
}
``` 
## 把config package publish到private NPM
先設定project name跟預設執行的main
``` json
{
    "name": "@eslint-config-leo/eslint-config-base",
    "main": ".eslintrc.js",
    "version": "0.0.1",
    "license": "MIT",
    "dependencies": {},
    "devDependencies": {
        "eslint": "^6.4.0",
        "eslint-config-prettier": "^6.3.0",
        "eslint-plugin-prettier": "^3.1.1",
        "prettier": "^1.18.2"
    }
}
``` 

然後發布吧!!!
``` json
npm publish
``` 
## 下載使用config package
先安裝npm packages for eslint
``` json
npm install @eslint-config-leo/eslint-config-base --save-dev
``` 

接著設定project .eslintrc.js and  .prettierrc.js
- .eslintrc.js
``` javascript
module.exports = {
    extends: ["@eslint-config-leo/base"] //eslint-config-可以省略
};
``` 
- .prettierrc.js
``` javascript
module.exports = {
    ...require("./node_modules/@eslint-config-leo/eslint-config-base/.prettierrc.js")
};
```

## 接著大方地使用吧 ~~~~~
