# プロジェクト構造

このプロジェクトはUnityを使用しています。一般的なUnityプロジェクト構造に従い、以下のディレクトリ構造を持っています。

- PROJECT-ROOT/
  - Assets/ # 開発プロジェクトのコードとアセット
  - Documentation/ # 開発者向けドキュメント
  - Library/
    - PackageCache/ # 依存するUPMパッケージのキャッシュ
  - Packages/ # 埋め込みパッケージ

## 機能別アセンブリ構造

開発プロジェクトは1つ以上の機能フォルダに分割され、各機能フォルダは以下の構造を持ちます。
機能フォルダはAsssets/フォルダ配下に配置されます。

- PROJECT-ROOT/
  - Assets/
    - FEATURE-NAME/ # 機能フォルダ
      - Scenes/ # この機能に属するシーンファイル(.unity)
      - Scripts/
        - Editor/ # エディタ拡張コード
          - FEATURE-NAME.Editor.asmdef # エディタ拡張のアセンブリ定義ファイル
        - Runtime/ # ランタイムコード
          - FEATURE-NAME.asmdef # ランタイムのアセンブリ定義ファイル
      - Tests/
        - Editor/ # エディタ拡張コードのテストコード
          - FEATURE-NAME.Editor.Tests.asmdef # エディタ拡張コードに対するテストのアセンブリ定義ファイル
        - Runtime/ # ランタイムコードのテストコード
          - FEATURE-NAME.Tests.asmdef # ランタイムコードに対するテストのアセンブリ定義ファイル

アセンブリ定義ファイルは、アセンブリ間の依存関係を定義します。

## 埋め込みパッケージ構造

UPMパッケージを開発する場合、コードはPackages/フォルダ配下に配置されます。

- PROJECT-ROOT/
  - Packages/
    - PACKAGE-NAME/ # パッケージフォルダ
      - Editor/ # エディタ拡張コード
        - PACKAGE-NAME.Editor.asmdef # エディタ拡張のアセンブリ定義ファイル
      - Runtime/ # ランタイムコード
        - PACKAGE-NAME.asmdef # ランタイムのアセンブリ定義ファイル
      - Tests/
        - Editor/ # エディタ拡張コードのテストコード
          - PACKAGE-NAME.Editor.Tests.asmdef # エディタ拡張コードに対するテストのアセンブリ定義ファイル
        - Runtime/ # ランタイムコードのテストコード
          - PACKAGE-NAME.Tests.asmdef # ランタイムコードに対するテストのアセンブリ定義ファイル

## .metaファイル

プロジェクト内の各ファイルとフォルダには、対応する.metaファイルが存在します。このファイルには、アセットの一意な識別子(GUID)やインポート設定などのメタデータが含まれています。これらの.metaファイルは、アセットを作成または変更すると、Unityによって自動的に生成されます。
ファイルまたはフォルダを作成する際は、.metaファイルをVCSへのコミットに含めてください。

## 依存関係

### 依存するUPMパッケージ

参照のみされている依存パッケージは、./Library/PackageCache/フォルダ配下にキャッシュされます。
インストールされたパッケージは、./Packages/packages-lock.jsonファイルにリストされています。

### 依存するNuGetパッケージ

NuGetパッケージから使用されるDLLファイルは、抽出されて./Assets/Packages/フォルダに配置されます。
インストールされたパッケージは、./Assets/packages.configファイルにリストされています。
