---
title: Cocos Creator 3.x 開發規範 (Convention)
date: 2026-11-24 10:00:00
categories: 軟體開發
tags: Cocos
---

## 本文章可以幫你

1. 了解 Cocos Creator 3.x 團隊開發的基本規範
2. 統一命名、Log、Git commit 與開發流程
3. 作為 Code Review 的檢查依據

## Basic Convention

1. Cocos Creator 3.8.3
2. TypeScript only (Type & Object-Oriented)
3. SOLID / DRY / KISS
4. appropriate design patterns are encouraged
5. "coder-oriented" mode, most things will be done by code, Cocos Creator takes minimal job
6. using eslint and prettier
7. naming rule
    - `No Abbreviated / Ambiguous Naming`, `id`, `msg`, `init` are accepted
    - typescript variable, typescript method using camel case `testName`
    - class using pascal case `TestName`
    - interface using "I" as a prefix `ITestName`
    - enum members using UPPER_CASE
    - image file name using snake case `test_name`
    - folder name using kebab case `test-name`
8. line break using LF (`\n`), NOT CRLF (`\r\n`)
9. Log
    - log level
        - `log` : show information.
        - `warn` : errors that will `NOT` cause the system to break down.
        - `error` : errors that will cause the system to break down, user can not continue.
    - using `[className]` as title

    ```ts
    log(`[PlatformWeb] deviceId = ${this.deviceId}`);
    log(`[WebSocketWrapper] ${this.host}: new socket`);
    ```

10. group cocos lifecycle, private, protected, public methods (using `#region`)
11. order by `import` first, then `const`

## Cocos Convention

- ESM first, not `export default class` (CommonJS)，cocos 3.x 版推崇 ESM，或許之後會支援 tree shaking
- binding events in code (on/off)，綁定在 cocos creator 的事件太隱含，對於 trace code 時較麻煩
- clarify Event Type, `button.on(Button.EventType.CLICK, ...)`
- get child (dynamic render) or add reference (`@property`) for node
- `getComponent(ClassName)` not `getComponent('string')`，避免更名的 side effect
- component class script 在 inspector 中的順序放在最後
- typescript 使用 cocos template 建立
- creator 上數值盡量不要使用浮點數，一般浮點數效能較不好
- node 命名：一般使用小駝峰，掛有 class script 的使用大駝峰，以「元件 + 類型」命名，ex: `userAmountLabel`, `userAmountContainer`
- 避免濫用 Widget、Layout，過多位置計算有效能疑慮
- 避免過多的層級，Label 類元件可層級攤平，可以有效降低 drawcall，例如 `userNameLabel`、`userAmountLabel` 可直接放在同一層 Node
- 保持 prefab 修改最小單元化，可盡量避免 prefab 衝突
- 使用 table inject node 只封裝需要的元件，可減少遞迴次數
- 大圖或需要動態載入的圖檔，不需加入合圖內，不然一律會被載下來
- spine 圖檔或設定為不需合圖的圖檔，需要另外開啟「壓縮紋理」

## Code Review Key Point

- Bugs and Side Effect
- 可維護性、可讀性
- Coding Style
- 設計要點
    - 設計保持簡單，避免 over design
    - 元件需抽離業務邏輯
    - 清晰各類功能職責
    - 元件抽離 base

## Code Template

- 將團隊的 ts template 取代以下路徑的檔案，之後在 Creator 新增 TypeScript 即會套用：

```
/Applications/Cocos/Creator/3.8.3/CocosCreator.app/Contents/Resources/resources/3d/engine/editor/assets/default_file_content/typescript/ts
```

## TypeScript Style Guidelines

1. major reference to [Google TypeScript Style Guide](https://google.github.io/styleguide/tsguide.html)
2. minor reference to [Cocos 官方規範](https://docs.cocos.com/creator/3.8/manual/zh/scripting/reference/coding-standards.html)

## Commit Rule & Git Branch Grouping

- `feat`: new feature or change request
- `fix`: bug fixed
- `docs`: document or remark
- `style`: coding style (no side effect)
- `refactor`: refactoring or structure
- `perf`: performance or fine tune parameters
- `test`: test case or unit test
- `build`: build parameters, ex: path change etc…
- `ci`: CI/CD, ex: gitlab-ci
- `chore`: tools or delete files etc…

**Git branch grouping**

```
feat/xxx
fix/xxx
test/addLogForTracking
```

**Commit rule**

```
feat: new game rule UI
fix: game rule content
```

## Develop Flow

1. Create feature branch
2. Bug fix or feature
3. Run local client for QA testing
4. MR to pre-release done

```
feat/[feature] -> pre-release // MR
fix/[bug]      -> pre-release // MR
pre-release    -> master      // deploy
```

## TODO

1. pre-commit hook (husky / lint-staged)
2. commitlint / auto CHANGELOG
3. Gitlab CI
