######################################################
エクスペリエンスエディターのリボンをカスタマイズする
######################################################

エクスペリエンス エディターのリボンをカスタマイズするには、新しいボタンアイテムを作成して、そのボタンにコマンドを割り当てます。PageCodeScriptFileName プロパティを使用して、リボンのタブが読み込まれたときに実行したJavaScriptファイルのパスを指定します。

以下のリボン コントロールを作成することができます：

* QuickbarButton
* LargeDropDownButton
* LargeButton
* SmallButton
* SmallCheckButton

.. image:: images/blank.png
   :align: center
   :width: 400px
   :alt: 空白

*メモ* 上記のコントロールはSPEAKコントロールをサポートするために CommandRibbonComponent テンプレート (ID: “{CE11D144-D6A8-4B33-8E98-07DED610E952}”)を継承しています。これは /sitecore/client/Applications/ExperienceEditor/Common/Templates/Controls に配置されています。

この章では、以下について説明します：

* 新しいボタンアイテムを作成する
* 既存のリボンコントロールを非表示にする]

********************************
新しいボタン アイテムを作成する
********************************

エクスペリエンス エディター リボンに新しいボタン アイテムを作成するには、次の手順を実施します：

1. Core データベースに切り替えてコンテンツ エディターを開き、/sitecore/content/Applications/WebEdit/Ribbons/WebEdit/Page Editor/Edit に移動します。
2. 適切なリボンのコントロール テンプレートに基づく新しいアイテムを作成します。この例では、Small Button テンプレートを使用します。テンプレートは /sitecore/templates/System/Ribbon/ にあります。
3. 新しいアイテムに以下の情報を追加します。

  * [ヘッダー] フィールドにボタンの表示名を入力します。
  * [ID] フィールドにアイテムの一意識別子を入力します。例えば、ID にリボンのグループ名を含むことができます。
  * [アイコン] フィールドに適切なアイコンのパスを入力します。作成するボタンに応じてアイコンのサイズを調整します。

.. image:: images/blank.png
   :align: center
   :width: 400px
   :alt: 空白

4. Sitecore Rocks を開き、適切なコントロール レンダリングを追加します。例えば、作成したボタン アイテムのレイアウトに SmallButton を追加します。

5. レンダリングの一意識別子を入力します。
6. 他の SPEAK コントロールでは、[Data Source] フィールドで別のアイテムを指定して、他のアイテムで設定を指定することができます。

.. note:: 

  このレンダリングにはプレースホルダーは指定しないでください。


ボタンにコマンドを割り当てる
============================

新しいボタンのコマンドを作成して割り当てるには、以下の手順に従います：

1. Sitecore のフォルダー ストラクチャーで、\Website\sitecore\shell\client\Sitecore\ExperienceEditor\Commands に移動し、新しい JavaScript ファイルを作成します。この例では、helloworld.js を作成します。
2. ファイルを開いて、関連するコードを記述します。例：

.. code-block:: csharp

    define(["sitecore"], function (Sitecore) {

      Sitecore.Commands.HelloWorld =

      {

        canExecute: function (context) {

        // Determines whether command is disabled or enabled.

        return true;

        },

        execute: function (context) {

          alert("Hello world!");

        }

      };

    });

3. Sitecore Rocks で新しいボタン アイテムを右クリックし、[タスク] 、[Design Layout] の順にクリックします。
4. SmallButton レンダリングをダブル クリックし、[Edit Rendering Properties] ダイアログ ボックスで以下の値を変更します：

  * Click プロパティでは、値を [trigger:button:click] に設定します。
  * Command プロパティでは、 Sitecore.Commands 名前空間で定義されたコマンド オブジェクトの名前を入力します。この例では「HelloWorld」と入力します。
  * [PageCodeScriptFileName] フィールドに、実行が必要な JavaScript ファイルのパスを入力します。

5. [閉じる] をクリックしてエクスペリエンス エディターを開き、リボンにボタンが表示されることを確認します。

*****************************************
既存のリボン コントロールを非表示にする
*****************************************

エクスペリエンス エディターのリボンで、既存のボタンまたはタブを非表示にすることができます。

* 既存のボタンを非表示にするには、セキュリティ設定を使用します。
* 既存のタブを非表示にするには、まず JavaScript が実行された時にタブを非表示にする JavaScript ファイルを作成し、そのファイルを非表示にしたいタブに割り当てます。

既存のタブを非表示にするには、以下の手順に従います：

1. Sitecore のフォルダー ストラクチャーで \Website\sitecore\shell\client\Sitecore\ExperienceEditor\Commands に移動し、JavaScript が実行された時にタブを非表示にする JavaScript ファイルを作成します。
2. Sitecore Rocks で、非表示にしたいタブに移動して [タスク] をクリックし、[Design Layout] をクリックします。
3. 非表示にしたいタブのデザイン レイアウトで、[Renderings and Place Holders] セクションの [Strip rendering] をダブル クリックします。

.. image:: images/blank.png
   :align: center
   :width: 400px
   :alt: 空白

4. [Edit Rendering Properties] ダイアログ ボックスで、[PageCodeScriptFileName] フィールドに JavaScript が実行された時にタブを非表示にするカスタムの JavaScript ファイルのパスを入力します。

.. image:: images/blank.png
   :align: center
   :width: 400px
   :alt: 空白

