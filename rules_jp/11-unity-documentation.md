# 公式ドキュメントを参照する

## Unity ドキュメント

Unity EngineとEditor APIを使用する際は、以下の公式ドキュメントを参照して事実を確認してください。
URLの「6000.0」はUnityのバージョンを示しています。このプロジェクトと同じバージョンのドキュメントを参照する場合は、./ProjectSettings/ProjectVersion.txtファイルに記載されているバージョンに置き換えてください。

- https://docs.unity3d.com/6000.0/Documentation/Manual/UnityManual.html
- https://docs.unity3d.com/6000.0/Documentation/ScriptReference/index.html

`UnityEngine.UI`、`UnityEngine.Event`、`TMPro`名前空間のAPIドキュメントについては、以下のURLを参照してください:

- https://docs.unity3d.com/Packages/com.unity.ugui@latest

## 依存パッケージ

### UPM パッケージ

依存するUPMパッケージは、./Library/PackageCache/ディレクトリ配下にキャッシュされています。
主にREADME.mdを参照し、必要に応じてソースファイルを参照してください。
また、package.jsonの「documentationUrl」に記載されているURLも参照できます。

### NuGet パッケージ

依存するNuGetパッケージは、./Assets/packages.configファイルにリストされています。
READMEは、https://www.nuget.org/packages/ + パッケージ名 から取得できます。
ソースコードを参照する場合は、GitHubリポジトリから./.claude/sandbox/ディレクトリにクローンすることもできます。
