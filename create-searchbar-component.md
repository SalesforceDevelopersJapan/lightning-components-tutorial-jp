---
layout: module
title: モジュール 5&#58; SearchBar コンポーネントの作成
---

このモジュールでは、名前から取引先責任者を検索することの出来る SearchBar コンポーネントを作成します。ContactListコンポーネントに検索バーを追加することも可能ですが、コンポーネントの再利用性に制限をかけてしまう恐れがあります: 例えば特定のUI要求で検索バーをリストのトップに置いておきたい、ヘッダーと統合したい、またはどこか別の場所に設置したいなどや、通常の入力フィールドと検索ボタン以外にも検索自動補完等を付けたい場合など、その要件は様々です。
このような場合 ContactList コンポーネントは検索バーのようなものとは独立して取引先責任者をリスト表示できるようにすべきでしょう。そのため、今回は ContactList 及び SearchBar の2種類のコンポーネントを作成します。

## 何を学ぶことができるか

- Lightning イベントの作成
- イベントを使ったコンポーネント間のやりとり

## ステップ 1: SearchKeyChange イベントの作成:

ここでSearchBar 及び ContactList という２つの別々のコンポーネントを作成することが決まった所で、ContactList は検索文字が変わった際にはそれを知り、該当する取引先責任者を表示しなおす必要があります。Lightning イベントを使うとこのようなコンポーネント間のやりとりを可能にします。
このステップでは、SearchBar コンポーネントが、検索条件が代わった際に他のコンポーネントに通知するためのLightning Eventを作成します。

1. 開発者コンソールにて **File** > **New** > **Lightning Event** をクリックし **SearchKeyChange** をバンドル名に入力し **Submit** をクリックします。

1. イベントを以下の様に実装します:

    ```
    <aura:event type="APPLICATION">
        <aura:attribute name="searchKey" type="String"/>
    </aura:event>
    ```
    ### Code Highlights:
    - The event holds one argument: the new **searchKey**

1. **File** > **Save** をクリックしファイルを保存します。

## ステップ 2: SearchBar コンポーネントの作成

1. 開発者コンソールにて **File** > **New** > **Lightning Component**　をクリックし、 **SearchBar** をバンドル名に設定して **Submit** をクリックします

2. コンポーネントは以下のように実装します:

    ```
    <aura:component>

        <div>
            <input type="text" class="form-control"
                    placeholder="Search" onkeyup="{!c.searchKeyChange}"/>
        </div>

    </aura:component>
    ```
    ### コードハイライト:
    -これは単一のインプット項目を持つサンプルコンポーネントです
    - ユーザが何か文字を入力した際 (**onkeyup**) 、 **searchKeyChange()** 関数がコンポーネントのクライアントサイドコントローラで実行されます (次のステップでこの関数をコーディングします)。 このアプローチでは、ユーザがキーボードを入力するたびに検索が再実行されます。


1. **File** > **Save** をクリックしてファイルを保存します。

## Step 3: コントローラの実装

1. **CONTROLLER** をクリックします(コードエディタ内の右上隅)

1. 以下の様にコントローラを実装します:

    ```
    ({
        searchKeyChange: function(component, event, helper) {
            var myEvent = $A.get("e.c:SearchKeyChange");
            myEvent.setParams({"searchKey": event.target.value});
            myEvent.fire();
        }
    })
    ```

    ### コードハイライト:
    - 関数はまず **SearchKeyChange** イベントのインスタンスを取得します
    - その際、イベントのsearchKeyパラメータに入力フィールドの新しい値を取得し、設定します
    - 最後にイベントを発火し、登録済みのリスナーはそれを受け取ることができます。

1. **File** > **Save** をクリックしてファイルを保存します


## Step 4: コンポーネントにスタイルを設定する

1. **STYLE** をクリックします

    ![](images/searchbar-style.jpg)

1. 以下のようにスタイルを実装します:

    ```
    .THIS {
        width: 100%;
        padding: 8px;
    }

    .THIS input {
        width: 100%;
        padding: 8px;
        -webkit-appearance: none;
        border: solid 1px #dddddd;
    }
    ```

1. **File** > **Save** をクリックしファイルを保存します

## ステップ 5: SearchKeyChange イベントを ContactList で購読する

1. 開発者コンソールにて、**ContactList** コンポーネントへ戻ります

1. **SearchKeyChange** イベントのためのイベントハンドラを追加します (initハンドラのすぐ後に):

    ```
    <aura:handler event="c:SearchKeyChange" action="{!c.searchKeyChange}"/>
    ```

    ### コードハイライト:
    - ハンドラはコントローラ内の **searchKeyChange()** 関数を呼ぶように設定されています。


1. **CONTROLLER** をクリックします(コードエディタ内の右上隅)

1. **searchKeyChange()** 関数を以下の様に実装します:

    ```
    searchKeyChange: function(component, event) {
        var searchKey = event.getParam("searchKey");
        var action = component.get("c.findByName");
        action.setParams({
          "searchKey": searchKey
        });
        action.setCallback(this, function(a) {
        	component.set("v.contacts", a.getReturnValue());
        });
        $A.enqueueAction(action);
    }
    ```

    > doInit() 及び searchKeyChange() 関数がカンマによって区切られていることを確認して下さい

    ### コードハイライト:
    - まず最初にイベントオブジェクトより有効なsearchKeyの値を取得します。
    - その際にモジュール3で作成したApexコントローラ内の **findByName()** メソッドを呼び出します。
    - 非同期コールが帰ってきた際に、コンポーネントの **contacts** 属性に対して、findByName()から返された取引先責任者のリストを設定します。


1. **File** > **Save** をクリックしてファイルを保存します。

## ステップ 6: SearchBar を QuickContacts コンポーネントに追加する

1. 開発者コンソールにて、 **QuickContacts** コンポーネントに戻ります

1. SearchBar コンポーネントを ContactList コンポーネントの前に追加します:

    ```
    <aura:component implements="force:appHostable">

        <c:SearchBar/>
        <c:ContactList/>

    </aura:component>
    ```

1. **File** > **Save** をクリックしてファイルを保存します。

1. Salesforce1アプリに戻り**クイック取引先責任者** をメニューから再読み込みして変更を確認します。取引先責任者を検索するために、検索バーに文字を入力します。

    ![](images/version4.jpg)

<div class="row" style="margin-top:40px;">
<div class="col-sm-12">
<a href="create-contactlist-component.html" class="btn btn-default"><i class="glyphicon glyphicon-chevron-left"></i> 戻る</a>
<a href="next.html" class="btn btn-default pull-right">次へ <i class="glyphicon glyphicon-chevron-right"></i></a>
</div>
</div>
