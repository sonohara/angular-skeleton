# Skeleton Angular

![](https://github.com/sonohara/skeleton-angular/actions/workflows/ci.yml/badge.svg)

Angular 使うときの雛形

## License

MIT

## 構築メモ

### 1. Angular プロジェクトのセットアップ

https://angular.io/guide/setup-local

### 2. Angular ESLint を入れる

https://github.com/angular-eslint/angular-eslint

### 3. Prettier を入れる

https://prettier.io/docs/en/install.html

公式 Docs に従って、ESLint との競合を避けるために eslint-config-prettier も入れる
https://github.com/prettier/eslint-config-prettier

公式 Docs に従って、husky と lint-staged も入れる

- husky
  - https://github.com/typicode/husky
  - [husky 側のドキュメント](https://typicode.github.io/husky/#/?id=usage) を見ると Prettier のドキュメントに記載のインストール方法は Manual インストールらしく、
    そうではない Automatic インストールが recommended なので、そっちの方法でインストールする（どっちでやっても最終的な状態は変わらないが）
- lint-staged
  - https://github.com/okonet/lint-staged

#### 備忘

npm-run-all で eslint と prettier --check を直列で lint する npm script を登録しておいて、
lint-staged で実行しようとしたが、エラーが出て実行できなかった。

原因:

lint-staged の仕組みとして、staging ステータスのファイルを引数として指定のコマンドを呼び出す。
そのため、正しく動作させるには eslint と prettier のそれぞれに引数が指定されるかたちで呼び出す必要があったが、その形式になっていなかった。
一応、[run-s にも引数を渡す方法](https://github.com/mysticatea/npm-run-all/blob/master/docs/run-s.md#argument-placeholders) があったが、設定が複雑になりそうなので諦めて、素直に lint-staged 側でそれぞれのコマンドを実行することにした。

package.json (抜粋)

```json
    "lint": "run-s eslint format:check",
    "eslint": "eslint",
    "format:check": "prettier --check --ignore-unknown src",
```

.lintstagedrc

```json
{
  "src/**/*": ["npm run lint]
}
```

↑ を ↓ に修正して対応

.lintstagedrc`

```json
{
  "src/**/*": ["eslint", "prettier --check --ignore-unknown"]
}
```

### 5. test を CI 上で動くようにする

ヘッドレス Chrome で動く設定を追加する。
watch を false にしないとテストが自動終了しないので設定を追加しておく。

package.json (抜粋)

```json
    "test:ci": "ng test --browsers ChromeHeadless --watch false --code-coverage true",
```

### 6. GitHub Actions の設定を入れる

CI 用の workflow を作る
