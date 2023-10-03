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

`-` はコミットIDの参照情報を持っているが、実際のソースコードが checkout されていない状態

```
git submodule status
```

```
 11aab291062d666e60a3c61c393bcd3afdffb85c submodule_1 (heads/main)
-11aab291062d666e60a3c61c393bcd3afdffb85c submodule_2
```




```
git submodule status
```

次の状態になればいい。

```
 11aab291062d666e60a3c61c393bcd3afdffb85c submodule_4 (heads/main)
 11aab291062d666e60a3c61c393bcd3afdffb85c submodule_5 (heads/main)
```


```
+11aab291062d666e60a3c61c393bcd3afdffb85c submodule_1 (heads/main-2-g11aab29)
 11aab291062d666e60a3c61c393bcd3afdffb85c submodule_5 (heads/main)
```


- `+` : サブモジュールの現在のコミットは、親リポジトリが指定（参照）しているコミットと異なる。
- `-` : サブモジュールのコンテンツが設定されていない。
- `U` : サブモジュールのコンテンツがコンフリクトしている。
- 何もなし : サブモジュールが正しくチェックアウトされており、親リポジトリが指定しているコミットを指している。




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
git rm --cached $SUBMODULE_DIR  # ディレクトリが削除され、.gitmodules の更新とともにステージングされます
rm -rf .git/modules/$SUBMODULE_DIR
git commit -m"remove submodule_3"
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


## 動作検証


- submodule を追加後に、別のローカルリポジトリで pull を実行
    - submodule のディレクトリが作成される
    - pull は実行されないので、`git submodule update --init`が必要。
- submodule を削除後に、別のローカルリポジトリで pull を実行
    - submodule が削除される
    - submodule が削除されない（シチュエーションによる）
        - 単純にディレクトリを削除すればいいのだが、どのサブモジュールが削除されたか分かりにくい




- git submodule update の実行前にすべての変更をコミットすることを忘れないこと!変更は上書きされます。


## 状態の検証

```
git diff --submodule
git submodule status
git submodule foreach 'echo $sm_path `git rev-parse HEAD`'
```



## 親リポジトリ管理者用

サブモジュールでdevelopの最新コミットを参照させる。

```
git submodule foreach 'git checkout develop && git pull origin develop'
```

サブモジュールのパスを取得する

```
grep path .gitmodules | sed 's/.*= //'
```

サブモジュールのパスを取得し、それを `git add` する

```
grep path .gitmodules | sed 's/.*= //' | xargs git add
```
