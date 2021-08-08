# osint
公開情報を利用した情報収集についての知見をまとめる。

# security exploit

## https://www.exploit-db.com/google-hacking-database
ハックに有益なgoogleクエリのデータベース

# tools
- [linkedIn](https://github.com/m8r0wn/CrossLinked)
- [twitter](https://kurozumi.github.io/tweepy/)

# pagefileから調査の手がかりを得る
```
# pagefile.sysの解析

# 参考
https://soji256.hatenablog.jp/entry/2019/06/25/065200

# ファイルパス
strings pagefile.sys | grep -i "^[a-z]:\\\\" | sort | uniq > list_file.txt


# URL
strings pagefile.sys | egrep "^https?://" | sort | uniq > list_url.txt

# メールアドレス
strings pagefile.sys | egrep '([[:alnum:]_.-]{1,64}+@[[:alnum:]_.-]{2,255}+?\.[[:alpha:].]{2,4})' > list_mail.txt


# 環境変数
strings pagefile.sys | grep -i "^[a-zA-Z09_]*=.*" | sort -u | uniq > list_envirnoment.txt
```

# OutlookのメールをExcelに出力
```
'UTF8だと動かない場合があるため、その場合はSJISで実行してみてください

' ここをトリプルクリックでスクリプト全体を選択できます。
Const IMPORT_FOLDER = "C:\Users\UserName\Documents" ' EML ファイルがあるフォルダーを指定

'実行の前提として、受信フォルダ配下にインポートフォルダを作成してください
Const TARGET_IMBOX = "インポート"


Const olFolderInbox = 6
Dim olkApp
Dim fldImport
Dim wshShell
Dim objFSO
Dim objFolder
Dim objFile
Set olkApp = CreateObject("Outlook.Application")
Set fldImport = olkApp.Session.GetDefaultFolder(olFolderInbox)
Set fldImport = fldImport.Folders("インポート")
fldImport.Display
Set wshShell = CreateObject("WScript.Shell")
Set objFSO = CreateObject("Scripting.FileSystemObject")
Set objFolder = objFSO.GetFolder(IMPORT_FOLDER)
' IMPORT_FOLDER で指定されたフォルダーのファイルを取得
For Each objFile In objFolder.Files
    If LCase(Right(objFile.Name,4)) = ".eml" Then
        ' 拡張子が .eml ならインポート処理
        OpenEml objFile.Name
    End If
Next
olkApp.ActiveExplorer.Close
MsgBox "インポートは終了しました。", vbOKOnly, "EML ファイル インポート"
' eml ファイルを開いてインポートするルーチン
Sub OpenEml( strFileName )
    On Error Resume Next
    Dim objCopied
    ' メールが開いていたら閉じる
    While Not olkApp.ActiveInspector Is Nothing
        olkApp.ActiveInspector.Close
        WScript.Sleep 1000
    Wend
    ' eml ファイルを Outlook で開くコマンドを実行
    wshShell.Run "outlook /eml """ & IMPORT_FOLDER & "\" & strFileName & """"
    ' 上記のコマンドで Outlook が起動するのを待つ
    While olkApp.ActiveInspector Is Nothing
        WScript.Sleep 1000
    Wend
    ' 開いたファイルを受信トレイに移動
    olkApp.ActiveInspector.CurrentItem.Move fldImport
    olkApp.ActiveInspector.Close
End Sub
```
