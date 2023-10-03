# submodule_root

- submodule を管理するリポジトリを親リポジトリと呼びます
- submodule を子リポジトリと呼びます

- submodule の実体は ``.gitmodules` である。
- submodule として追加したディレクトリは、ただの外部リポジトリへ参照を記録した箱である
- submodule の外側は、その箱のどのコミットを参照しているのかを記録している
- submodule の内側は、基本的には、自分が操作したときにしか更新されない

- submodule update の動作
    - `.gitmodules` を参照し、 submodule を更新します
    - submodule はチェックアウトされます
    - `.git/modules/<submodule_path>/HEAD` に submodule への参照コミットを保持・更新します？？？


- `.gitmodules` にコミットハッシュが記録される（ブランチではない）

- サブモジュール内で未コミットの変更が存在する場合
    - 未コミットを解消するか stash で一時退避する


## ローカルリポジトリの初期化・更新

リポジトリをクローンします。

```
git clone xxxx
```

クローン直後、submodule は初期化されていません。
直後にローカルリポジトリの初期化・更新を怒ってください。



submodule の管理リポジトリで変更を取得します。

- submodule の管理リポジトリで pull を実行すると、管理リポジトリに関する変更のみ取得します。
- submodule 下で pull を実行すると、その submodule に関する変更のみ取得します。

```
git pull
```


## ローカルリポジトリの初期化・更新



サブモジュールがあるコミットの状態になります。

```
git submodule update --init
```

- init: 未初期化の submodule を初期化した上で、submodule を更新します。
- recursive: submodule の中に submodule がある場合にも再帰的に初期化します。


特定のサブモジュールのみ更新をする。

```
git submodule update --init -- path/to/your/submodule
```








## サブモジュールの追加

サブモジュール（ディレクトリ）を追加する。

```
git submodule add git@github.com:sasano8/submodule_1.git submodule_1
```

サブモジュールを追加すると、次のファイル・サブモジュールが stagging される。

- .gitmodules
- submodule_1

```
git commit -m"add submodule_1"
```





管理しているサブモジュールを確認する。

```
git submodule status
```

管理しているサブモジュールの詳細を確認する。
（ブランチが指定されていない場合は、mainを指す）

```
cat .gitmodules
```


## サブモジュールの削除

```
SUBMODULE_NAME=submodule_1
SUBMODULE_DIR=submodule_1
git submodule deinit $SUBMODULE_NAME
git rm $SUBMODULE_DIR  # ディレクトリが削除され、.gitmodules の更新とともにステージングされます
rm -rf .git/modules/$SUBMODULE_DIR
git commit -m"remove submodule_1"
git push
```



## サブモジュールの変更

submodule で通常の git 操作でコミットを行った上で、サブモジュールをステージングし、コミットします。

```
git add path/to/submodule
git commit -m "Update submodule"
```




## コミット作業

- submodule 内での git 操作は、submoduleが独立したGitリポジトリとして動作します。



## 運用

- 親リポジトリの管理者は一人とする
    - 更に管理者は子リポジトリを修正する場合は、親リポジトリ編集用のローカルブランチを分ける（親リポジトリの編集のみに注力できるようにするべき）
        - それを強制する方法は xxx
    - submodule checkout 時に、参照コミットが代わり、push するとそのコミットがリポジトリに反映される


- 親リポジトリ開発者
    - 親ローカルリポジトリ
    - 子ローカルリポジトリ
- 子リポジトリ開発者
    - 子ローカルリポジトリ
