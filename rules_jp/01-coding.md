# コーディングガイドライン

## 基本原則

- シンプルであることを心がけてください。
- SOLID原則を遵守してください。特に単一責任原則、インターフェース分離原則、依存性逆転原則が重要です。
- コードを書く前に、.editorconfig ファイルを読んでください。

## 構造

- エディタ拡張コードは `Editor/` ディレクトリ配下に配置してください。
- ランタイムコードは `Runtime/` ディレクトリ配下に配置してください。
- 1つのファイルには、1つのpublicクラスまたはインターフェースのみを含めてください。

## 命名規則

### フィールドと変数

- 変数名には名詞を使用してください。変数名は特定の物や状態を表すため、明確で説明的である必要があります。bool型の変数を除き、命名時には名詞を使用してください。
- Boolean型には動詞の接頭辞を付けてください。これらの変数はtrueまたはfalseの値を示します。多くの場合、「プレイヤーは走っているか」「ゲームは終了したか」といった質問への答えになります。
    - 意味をより明確にするために動詞の接頭辞を付けてください。多くの場合、説明や条件とペアになります。例えば、isDead、isWalking、hasDamageMultiplier などです。
- 意味のある名前を使用してください。省略形は避けてください(数学的表現を除く)。変数名はその意図を明らかにします。発音しやすく、検索しやすい名前を選択してください。
    - ループや数式では1文字の変数でも問題ありませんが、それ以外の状況では省略形を使用しないでください。数文字を省略することで節約できる時間よりも、明確さの方が重要です。
    - 素早いプロトタイピングでは、一時的に短い「仮」の名前を使用し、後でより意味のある名前にリファクタリングしてください。
- publicフィールドにはPascalCaseを使用し、private変数にはcamelCaseに接頭辞「_」を付けてください。publicフィールドの代替として、publicな「getter」を持つプロパティを使用することができます。
- staticフィールドにはcamelCaseを使用し、「s_」で始めてください。

### プロパティ

- プロパティアクセサに追加のロジックが不要な場合は、自動実装プロパティを使用してください。
- Unityのシリアライズされたプロパティについては、Inspectorで表示できるように次のパターンを使用してください。
    ```csharp
    [field: SerializeField]
    public int SerializedProperty { get; set; } = defaultValue;
    ```
    このパターンは、プロパティをpublicのまま保ちながら、バッキングフィールドにSerializeField属性を適用します。
- プロパティのドキュメントコメントは、属性の上ではなく、プロパティの上に配置してください。

### 列挙型(Enum)

- 列挙型の名前と値にはPascalCaseを使用してください。publicな列挙型はクラスの外に配置して、グローバルにすることができます。列挙型の名前には単数形の名詞を使用してください。
- 注意: System.FlagsAttribute でマークされたビット単位の列挙型は、このルールの例外です。これらは複数の型を表すため、通常は複数形にしてください。

### クラスとインターフェース

- クラス名にはPascalCaseの名詞を使用してください。これによりクラスが整理された状態を保てます。
- ファイルにMonoBehaviourがある場合、ソースファイル名は一致する必要があります。ファイル内に他の内部クラスがあっても構いませんが、1つのファイルにつき1つのMonoBehaviourのみが存在すべきです。
- インターフェース名には大文字の「I」を接頭辞として付けてください。その後に機能を説明する形容詞を続けます。
- 抽象クラス名には接頭辞や接尾辞は付けませんが、実装クラスには抽象クラス名を接尾辞として付けてください。

### メソッド

- 名前を動詞で始めてください。必要に応じてコンテキストを追加してください(例: GetDirection、FindTarget など)。
- パラメータにはcamelCaseを使用してください。メソッドに渡されるパラメータは、ローカル変数と同じようにフォーマットしてください。
- boolを返すメソッドは質問形式にしてください。Boolean変数自体と同様に、true-falseの条件を返す場合はメソッドに動詞の接頭辞を付けてください。これにより質問の形式になります(例: IsGameOver、HasStartedTurn)。

### イベントとイベントハンドラー

- イベントには動詞句で名前を付けてください。状態変化を正確に伝えるものを選択してください。
- 現在分詞または過去分詞を使用して、イベントの状態が前か後かを示してください。例えば、ドアを開ける前のイベントには「OpeningDoor」を、その後のイベントには「DoorOpened」を指定してください。
- System.Actionを使用してください。
    - イベントにはSystem.Actionデリゲートを使用してください。ほとんどの場合、Action<T>デリゲートでゲームプレイに必要なイベントを処理できます。
    - void戻り値型で、最大16個の異なる型の入力パラメータを渡すことができます。事前定義されたデリゲートを使用することでコードを節約できます。
    - 注意: EventHandlerまたはEventHandler<TEventArgs>デリゲートを使用することもできます。チームとしてイベントの実装方法について合意してください。
- イベント発生メソッド(サブジェクト側)には「On」を接頭辞として付けてください。イベントを呼び出すサブジェクトは、通常「On」で始まるメソッドから行います(例: 「OnOpeningDoor」または「OnDoorOpened」)。
- イベント処理メソッド(オブザーバー側)には、サブジェクトの名前とアンダースコア(_)を接頭辞として付けてください。サブジェクトが「GameEvents」という名前の場合、オブザーバーは「GameEvents_OpeningDoor」または「GameEvents_DoorOpened」というメソッドを持つことができます。
- カスタムEventArgsは必要な場合にのみ作成してください。カスタムデータをイベントに渡す必要がある場合は、System.EventArgsから継承するか、カスタム構造体から新しいタイプのEventArgsを作成してください。

### 名前空間

- 特殊記号やアンダースコアを使用せず、PascalCaseを使用してください。
- ファイルの先頭にusing ディレクティブを追加して、名前空間の接頭辞の繰り返し入力を避けてください。
- サブ名前空間も作成してください。ドット(.)演算子を使用して名前のレベルを区切ることで、スクリプトを階層的なカテゴリに整理できます。例えば、「MyApplication.GameFlow」、「MyApplication.AI」、「MyApplication.UI」などを作成して、ゲームの異なる論理コンポーネントを保持することができます。

## 設計

- 条件分岐では、可能な限り早期リターンパターンを使用してください。ネストされたif文よりも、条件が満たされない場合の早期リターンを優先してください。
    ```csharp
    public void ProcessData(Data data)
    {
        if (data == null || !data.IsValid)
        {
            return;
        }

        // process...
    }
    ```

## コメント

コメントは英語で記述してください。

### XMLドキュメントコメント

- すべてのpublicクラスとメソッドにはXMLドキュメントコメントが必要です。利用可能なタグについては https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/xmldoc/recommended-tags を参照してください。
    ```csharp
    /// <summary>
    /// A class that manages the player's state.
    /// </summary>
    public class CharacterController : MonoBehaviour
    {
        /// <summary>
        /// Take damage to the character.
        /// </summary>
        /// <param name="amount">Damage amount</param>
        /// <returns>True: Character is alive</returns>
        public bool TakeDamage(float amount) { ... }
    }
    ```
- インターフェースまたは抽象クラスを実装する場合は、継承コメントのみを記述してください。
    ```csharp
    /// <inheritdoc/>
    public class MyClass : AbstractClass {}
    ```

### コード内のコメント

- コメントでは、「なぜそうしなかったか」の理由を提供してください。他の実装方法が可能であるように見える場合は、なぜその方法が選択されなかったかを説明してください。
    ```csharp
    private List<Player> _activePlayers = new List<Player>();
    // Reasons for using List instead of Dictionary<int, Player>:
    // Since the number of players is small and there are no frequent lookup operations,
    //We prioritize memory efficiency and iteration speed.
    ```

### コメントの更新

- コードを変更する場合は、対応するコメントも必ず更新してください。
- 古いコメントは誤解を招く可能性があるため、現在のコードを反映するように更新してください。
- 不要と判断したコメントは積極的に削除してください。
