# コメントの指針

差分をみたときに元のコードをなるべく変更しないようにする。
具体的には、コードの後ろにコメントをつけない。

・SQLite3 のライブラリを利用し、直接DB を参照している
・

# 関数ごとのメモ

run():
  コマンド毎に関数への参照を格納した「リスト」 commands を用意。
  argparse でコマンドライン引数をパースし、
  コマンド毎に処理を振り分ける。

  処理毎に、sqlite3.connect で Anki の DB に直接つなぎ、
  コマンドごとの関数へ渡す。

  処理がうまくいきトランザクション中なら、
  DB に反映させる(コミット)する

# DB の カラム名

* notes
    * mid: model_id
    * flds: フィールド
    * tags: タグ一覧
    * todo: usn: ?
    * cards: 
* col: コレクション？
    * tags: 全タグ？
* 

# コマンドごとのメモ

* dump_notes_fields():      print_notes_fields の JSON 版
* dump_notes_tags():        print_notes_tags の JSON 版
* dump_notes():             print_notes の JSON 版
* list_decks():             decks 用 list_models_decks
* list_models():            models 用 list_models_decks
* rename_tags():            tags, remove

    ```python
    # 全タグを取得
    row = conn.execute("select * from col where id=1").fetchone()
    row["tags"]

    #
    for target in srcs:
        for tag in keys:

            rename_tag_in_notes(conn, tag, dst)

    # 全タグを置き換え
    tagstr = json.dumps(tagsdict)
    conn.execute('update col set tags=?,mod=? where id=?',
                   (tagstr, int(time.time()*1000), row['id']))
    ```

* print_notes():            ids に応じた ノートを表示する

    JSON オプションが指定された場合は、 list 型の notes へ、
    そうでない場合は、 print_note を使い出力する。

    ```python
    for _id in ids:
        :
        row = conn.execute('select mid,flds,tags from notes where id=?',
                           (_id,)).fetchone()
    ```
    

* print_notes_fields():     ids で指定した ノートのフィールドを表示すすｒ。

    JSON オプションが指定された場合は、 dict 型の notes へ、
    そうでない場合は、 print_fields を使い出力する。

    ```python
    for _id in ids:
        :
        row = conn.execute('select mid,flds from notes where id=?',
                           (_id,)).fetchone()
    ```

* print_notes_tags():       ids で指定した ノートのタグを表示する。
    
    JSON オプションが指定された場合は、 dict 型の notes へ、
    そうでない場合は、 print_tags を使い出力する。


    ```python
    for _id in ids:
        :
        row = conn.execute('select tags from notes where id=?',
                           (_id,)).fetchone()
    ```

* replace_fields():         フィールドを置換。 json_strings で指定。


    ```python
    conn.execute('update notes set flds=?,mod=?,usn=? where id=?',
                   (fieldsstr, int(time.time()), -1, _id))
    ```

* replace_tags():           タグのまるごと置き換え。json_strings で指定。 replace_fields とほぼ同じ。

    dump_tags した JSON を 変更して、 replace_tags に入れるとよいようだ。( README.md )

    ```python
    conn.execute(
        'update notes set tags=?,mod=?,usn=? where id=?',
        (tagsstr, int(time.time()), -1, _id))
    ```

* remove_tags():            rename_tags を削除用につかう
* search_notes():           ノートを 検索

    regexps, only_field=None, only_tags=False, cards=False):

    ```python
    for row in conn.execute('select id,mid,flds,tags,sfld from notes'):
    ```

* search_notes_field():     フィールド 用 search_notes
* search_notes_fields():    全フィールド 用 search_notes
    only_field オプションは 実は正規表現をうけつける('.' が指定されている)

* search_notes_tags():      タグ 用 search_notes
* search_cards():           カード 用 search_notes
* print_cards():            ids のカードを出力

    ```python
    row = conn.execute("select id,nid,ord,due,ivl,factor,reps,lapses from cards where id = ?", (_id,)).fetchone()
    ```

* dump_cards():             print_cards の JSON 版

# 

※ github上 のエディタにて、変換キーを押すと
一つ前の文字が消えるのはどうにかならないものか

