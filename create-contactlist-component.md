---
layout: module
title: モジュール 4&#58; ContactList コンポーネントの作成
---

このモジュールでは、取引先責任者のリストの表示を行うLightning コンポーネントを作成し、このContactList コンポーネントを QuickContacts へ追加します。

## 何を学ぶことができるか
- コンポーネント属性の使用方法
- イベントハンドラの使用方法
- Lightningコンポーネントの他のLightningコンポーネント内での利用方法


## ステップ 1: コンポーネントの作成

1. 開発者コンソールにて **File** > **New** > **Lightning Component** をクリックします。 **ContactList** をバンドル名に設定し **Submit** をクリックします。

2. コンポーネントを以下のように入力します:

    ```
    <aura:component controller="ContactListController">

        <aura:attribute name="contacts" type="Contact[]"/>
        <aura:handler name="init" value="{!this}" action="{!c.doInit}" />

        <ul>
            <aura:iteration items="{!v.contacts}" var="contact">
                <li>
                    <a href="{! '#/sObject/' + contact.Id + '/view'}">
                        <p>{!contact.Name}</p>
                        <p>{!contact.Phone}</p>
                    </a>
                </li>
            </aura:iteration>
        </ul>

    </aura:component>
    ```

    ### コードハイライト:
    - コントローラはコンポーネントにアサインされ (コードの一行目)、モジュール2で作成した **サーバサイドコントローラ** (ContactListController)　へ参照が行われます。
    - **contacts** 属性は、サーバから送られてくる取引先責任者のリストオブジェクトを保持するために定義されています。
    - **init** ハンドラはコンポーネントの初期化が行われた際にコードを実行するために定義されています。 (**doInit**) のコードは、コンポーネント内の　**クライアントサイドコントローラ** に定義されています。(次のステップでこのコントローラを実装します)
    - ```<aura:iteration>``` は取引先責任者のリストを繰り返し処理するために利用され、 ```<li>``` タグを取引先ごとに生成します。


1.  **File** > **Save** をクリックしファイルを保存します。


## ステップ 2: コントローラの実装

1. **CONTROLLER** をクリックします。

    ![](images/component-controller.jpg)

1. コントローラを以下の様に実装します:

    ```
    ({
        doInit : function(component, event) {
            var action = component.get("c.findAll");
            action.setCallback(this, function(a) {
                component.set("v.contacts", a.getReturnValue());
            });
            $A.enqueueAction(action);
        }
    })
    ```

    ### コードハイライト:
    - コントローラは**doInit** と定義される関数を１つ持っています。この関数はコンポーネントが初期化されるタイミングで、コンポーネントより呼び出されます。
    - まず最初に **findAll()** メソッドの参照をコンポーネントのサーバサイドコントローラ (ContactListController)より取得し、**action** 変数に保持しています。
    - サーバへのfindAll() メソッド呼び出しは非同期に行われるので、この場合実行結果が戻ってきた際のコールバック関数を定義します。コールバック関数内では、シンプルに取引先責任者のリストをコンポーネントの **contacts** 属性にアサインしています。
    - $A.enqueueAction(action) はサーバへリクエストを送信します。より正確には、非同期サーバコールをキューへ追加します。 このキューではLightningの最適化機能が働きます。

1. **File** > **Save** をクリックしてファイルを保存します。


## ステップ 3: ContactList をQuickContacts コンポーネントへ追加する

1. 開発者コンソールで、**QuickContacts**　コンポーネントへ戻ります。

    もしタブが開発者コンソール内で見つからない場合は、開発者コンソールメニューの **File** > **Open Lightning Resources** をクリックし、 **QuickContacts** > **COMPONENT** をダイアログ内で選択し、 **Open Selected** ボタンをクリックします。

    ![](images/lightning-resources.jpg)


1. ContactList のプレースホルダを実際のコンポーネントで上書きします:

    ```
    <aura:component implements="force:appHostable">

        <c:ContactList/>

    </aura:component>
    ```

    > **c:** はLightningコンポーネントの標準名前空間を指します。

1. **File** > **Save** をクリックしファイルを保存します。

1. Salesforce1 アプリへ戻り、メニューより **Quick Contacts**を再読み込みし、変更を確認します:

    ![](images/version2.jpg)


## ステップ 4: ContactList コンポーネントにスタイルを設定する

このステップでは、標準のモバイルでのリストに見えるようにいくつかのCSSスタイルをコンポーネントへ追加します。アイテムごとのバレットを消去し、アイテム毎に水平線を追加し、マージンを最適化します。


1. **STYLE** をクリックします

    ![](images/component-style.jpg)

1. 以下のようにスタイルを実装します:

    ```
    .THIS {
        list-style-type: none;
        padding: 0;
        margin: 0;
    }

    .THIS li {
        border-bottom: solid 1px #DDDDDD;
        padding: 8px;
    }

    .THIS p {
        margin: 4px;
    }
    ```

1. **File** > **Save** をクリックしファイルを保存します。

1. Salesforce1 アプリへ戻り、メニューより再読み込みして **Quick Contacts** の変更を確認します:

    ![](images/version3.jpg)



<div class="row" style="margin-top:40px;">
<div class="col-sm-12">
<a href="create-lightning-application.html" class="btn btn-default"><i class="glyphicon glyphicon-chevron-left"></i> 戻る</a>
<a href="create-searchbar-component.html" class="btn btn-default pull-right">次へ <i class="glyphicon glyphicon-chevron-right"></i></a>
</div>
</div>
