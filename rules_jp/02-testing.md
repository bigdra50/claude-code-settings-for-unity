# テストガイドライン

## 基本原則

- ユニットテストは小さく、高速で、独立していること
  - 各テストは1つの機能や動作のみをテストします
  - テスト間の依存関係を避けます
- テストはドキュメントとしての役割も果たします
  - テスト名は「何をテストするか」を明確に示します
  - テストコードは実装の使用例としても機能します
- 依存オブジェクトがあるケースでも、可能な限り本物のプロダクトコードを使ってテストします。やむを得ないときのみテストダブルを使用します

## 構造

- テスト対象が Editor/ ディレクトリ下にある場合（エディタ拡張の場合）、テストコードは `Tests/Editor/` 下に置きます（Edit Mode tests）
- テスト対象が Runtime/ ディレクトリ下にある場合、テストコードは `Tests/Runtime/` 下に置きます（Play Mode tests）
- テスト対象クラスと対となるディレクトリ構造およびテストクラスを作成します
- テストコード内で使用するTest double (Stub, Spy, Dummy, Fake, Mock) クラスを作る場合は、`Tests/Editor/TestDoubles/` もしくは `Tests/Runtime/TestDoubles/` ディレクトリに置きます
- テストコード内で使用するSceneファイルを作る場合は、`Tests/Scenes/` ディレクトリに置きます

## 命名規則

- テストアセンブリ名は、テスト対象アセンブリ名 + ".Tests" とします
- テストコードの名前空間は、テスト対象と同一とします
- テストクラス名は テスト対象クラス名 + "Test" とします。e.g., `public class CharacterControllerTest`
- テストメソッド名は「テスト対象メソッド名」「条件」「期待される結果」をアンダースコアで連結した形式を使用します。e.g., `public void TakeDamage_WhenHealthIsZero_CharacterDies()`
- テスト対象オブジェクトには`sut`、実測値には`actual`、期待値には`expected`と命名し、役割を明示すること
- テストダブルを使用する場合、xUnit Test Patterns (xUTP) の定義に従って `stub`, `spy`, `dummy`, `fake`, `mock` のいずれかの接頭辞を使用します

## 設計

- テストにはNUnit3ベースのUnity Test Frameworkを使用します。See: https://docs.unity3d.com/Packages/com.unity.test-framework@1.4/manual/index.html
- テストクラスには `TestFixture` 属性をつけます
- テストメソッドの構造
  - Arrange, Act, Assert のパターンに従います
  - 各セクションの間を空行で区切ります。コメントは不要です
- Assertは1つのテストメソッドにつき、1つのみとします
- Assertには制約モデル（`Assert.That`）を使用します。ドキュメントを参照して、最適な制約を使用します。See: https://docs.nunit.org/api/NUnit.Framework.Constraints.html
- `Assert.That` の 引数 `message` は指定しません。テスト名と制約で十分に意図が伝わるようにします
- テストコードはシンプルなシングルパスであること
  - テストコード内では `if`, `switch`, `for`, `foreach`, 三項演算子を使用しないでください
- Parameterized tests を積極的に使用します
  - Arrangeが異なりActとAssertが同じテストは、`TestCase`, `TestCaseSource`, `Values`, `ValueSource` 属性を使用してパラメータ化できます
  - `ParametrizedIgnore` 属性で組み合わせの除外もできます
- テストで使用するオブジェクトの生成は、creation method pattern を積極的に使用します。e.g., `private GameObject CreateSystemUnderTestObject()`
  - `TearDown` でリソースを開放するために private field に保持する場合でも、テストメソッドでは常に creation method の戻り値を使用します
- 各テストは独立して実行できること。他のテストの実行結果に依存しません
- テスト中に `GameObject` を生成する場合、テストメソッドに `CreateScene` 属性をつけます。もしすでに `LoadScene` 属性がついていれば不要です
- 安易に`LogAssert`によるログメッセージの検証は避け、必要ならばSpyを作成して使用します
- 非同期テストでは、むやみに `Delay`, `Wait` による指定時間待機を使用しないこと。1フレーム待つだけなら `yield return null` を使用できます
- 非同期メソッドで例外がスローされることを検証する場合、`Throws` 制約ではなく、try-catchブロックを使用して、例外が発生することを確認します（Unity Test Frameworkの制限事項）
    ```csharp
    try
    {
        await Foo.Bar(-1);
        Assert.Fail("例外が出ることを期待しているのでテスト失敗とする");
    }
    catch (ArgumentException expectedException)
    {
        Assert.That(expectedException.Message, Is.EqualTo("Semper Paratus!"));
    }
    ```

## コメント

- テストコードには XML Documentation Comments は不要です

## テスト実行

- テスト実行前に、Unity editorでコンパイルエラーが出ていないことを確認します
- テストの実行は、MCP経由でUnity editor上のTest Runnerで行ないます
- フィルタを積極的に指定して、実行するテスト数を最小限にします。命名規則セクションで定めたネーミングルールを利用できます

## テスト実行結果の解釈

テスト実行結果は次の基準で解釈します

- Passed: テスト成功
- Failed: テスト失敗。原因調査と修正が必要です
- Inconclusive: テストの前提条件が満たせません。失敗と同様に扱います
- Skipped: スキップ指定されているテスト。無視してよいです

失敗したテストには次の手順で対処します

1. エラーメッセージを確認して原因を特定します
2. 期待値と実際の値の差異を分析します
3. 修正後、同じテストを再実行して確認します
4. 連続して失敗する場合は、テストコードと実装の両方を見直します
5. 2連続で失敗する場合、状況を整理してユーザーに指示を仰ぎます
