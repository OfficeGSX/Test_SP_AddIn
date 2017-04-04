---
title: アドイン Web の Web ページに Web パーツを含める
ms.prod: SHAREPOINT
ms.assetid: dd525e64-2472-4bc7-91be-86950f638ce4
---


# アドイン Web の Web ページに Web パーツを含める
SharePoint アドインのページに Web パーツを含める方法について説明します。
SharePoint アドインのアドイン Web のページにすぐに利用できる Web パーツを含めることができますが、アドインの更新が必要になった場合でも問題が生じない方法で含めることが重要です。
  
    
    

このトピックのガイダンスを示すコード サンプルの場所:  [OfficeDev/Core.WebPartOnAppWebPage](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.WebPartOnAppWebPage)
## 前提条件

前提条件については、「 [SharePoint ホスト型の SharePoint アドインの作成を始める](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)」をご覧ください。
  
    
    

## ページに Web パーツを追加する


  
    
    

1. SharePoint ホスト型 SharePoint アドインのプロジェクトを Visual Studio で作成します。詳しくは、「 [SharePoint ホスト型の SharePoint アドインの作成を始める](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)」をご覧ください。
    
  
2. Web パーツの追加先 aspx ファイルを開きます。このトピックでは、Default.aspx を例として使用します。 
    
  
3. **WebPartZone** を、以下のようなマークアップが含まれる Web パーツとする **<asp:Content>** 要素に追加します。通常は、 **ContentPlaceHolderId** が `PlaceHolderMain` である **<asp:Content>** に追加します。以下に例を示します。
    
 ```XML
  
<asp:Content ContentPlaceHolderId="PlaceHolderMain" runat="server">
  <WebPartPages:WebPartZone runat="server" FrameType="TitleBarOnly" 
      ID="HomePage1" Title="loc:full" />
</asp:Content>

 ```


    > **注意**
      > **<WebPartPages:XsltListViewWebPart>** などの Web パーツ要素を、 **WebPartZone** の子として追加することが可能です。ただし、SharePoint アドインにおいて、通常これは優れた方法とは言えません。アドインを更新する必要が生じると、aspx ファイルに挿入されている Web パーツ要素によって更新が失敗することがあり、「この ID の Web パーツは、既にこのページに追加されています。」というメッセージが表示される原因となります。この手順の残りの部分で説明されているように、Web パーツをページの要素マニフェストに追加することをお勧めします。
4. ページの要素マニフェスト ファイルを開きます。通常これは elements.xml という名前で、aspx ファイルと同じプロジェクト フォルダー内にあります。
    
  
5. 以下の例に示されているように、ページの **File** 要素に、子 **AllUsersWebPart** 要素を追加し、その **WebPartZoneID** をページ上で作成した Web パーツ領域の値に設定します。
    
 ```
  
<Elements xmlns="http://schemas.microsoft.com/sharepoint/">
  <Module Name="Pages">
    <File Path="Pages\\Default.aspx" Url="Pages/Default.aspx" ReplaceContent="TRUE" >
      <AllUsersWebPart WebPartZoneID="HomePage1" WebPartOrder="1">

      </AllUsersWebPart>
    </File>
  </Module>
</Elements>

 ```

6. 以下の例に示されているように、 **CDATA** 要素を **AllUsersWebPart** の子として追加し、 **webParts** 要素を **CDATA** の子として追加します。
    
 ```
  
<AllUsersWebPart WebPartZoneID="HomePage1" WebPartOrder="1">
  <![CDATA[
    <webParts>

    </webParts>
  ]]>
</AllUsersWebPart>
 ```

7. **webPart** マークアップを、 **webParts** 要素の子として追加します。 **XsltListViewWebPart** を追加する例を以下に示します。この例では、カスタム リスト "Test List" は同じアドイン プロジェクトの一部であると想定します。カスタム リストをアドイン Web に追加する方法については、「 [SharePoint のカスタムのリストおよびコンテンツ タイプを含む、プロバイダー向けのホスト型アドインを作成する](create-a-provider-hosted-add-in-that-includes-a-custom-sharepoint-list-and-conte.md)」をご覧ください。 
    
    > **メモ**
      >  Web パーツに ID プロパティがないことに注意してください。ベスト プラクティスとして、本当に必要な以下の 2 つの場合にのみ明示的な ID を Web パーツに含めます。>  Web パーツが SharePoint wiki ページに追加されている場合。>  Web パーツが、接続される 2 つ以上の Web パーツのうちの 1 つの場合。

 ```
  
<webParts>
  <webPart xmlns="http://schemas.microsoft.com/WebPart/v3">
    <metaData>
      <type name="Microsoft.SharePoint.WebPartPages.XsltListViewWebPart, 
                   Microsoft.SharePoint, Version=15.0.0.0, Culture=neutral, 
                   PublicKeyToken=71e9bce111e9429c" />
    </metaData>
    <data>
      <properties>
        <property name="ListUrl">Lists/{TestList}</property>
        <property name="IsIncluded">True</property>
        <property name="NoDefaultStyle">True</property>
        <property name="Title">{Test List}</property>
        <property name="PageType">PAGE_NORMALVIEW</property>
        <property name="Default">False</property>
        <property name="ViewContentTypeId">0x</property>
      </properties>
    </data>
  </webPart>
</webParts>
 ```

8. F5 を押してアドインをデバッグします。ページ上に対象の Web パーツが表示されるはずです。
    
  

