# submodule_root


## サブモジュールの追加

サブモジュール（ディレクトリ）を追加する。

```
git submodule add git@github.com:sasano8/submodule_1.git submodule_1
```


管理しているサブモジュールを確認する。

```
it submodule status
```

管理しているサブモジュールの詳細を確認する。
（ブランチが指定されていない場合は、mainを指す）

```
cat .gitmodules
```


## コミット作業

- submodule 内での git 操作は、submoduleが独立したGitリポジトリとして動作します。

