---
title: Azure Active Directory で名前付きの場所を構成する | Microsoft Docs
description: 名前付きの場所を構成する方法を説明します。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 4c8aba8faf4ce91445209eb46abea17423f57a77
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589333"
---
# <a name="configure-named-locations-in-azure-active-directory"></a>Azure Active Directory で名前付きの場所を構成する

名前付きの場所を使うと、組織内の信頼できる IP アドレス範囲にラベルを付けることができます。 Azure Active Directory は次の場合に名前付きの場所を使います。

- 報告される偽陽性の数を減らすための[リスク イベント](active-directory-reporting-risk-events.md)の検出。  

- [場所ベースの条件付きアクセス](active-directory-conditional-access-locations.md)。


この記事では、環境内で名前付きの場所を構成する方法について説明します。


## <a name="entry-points"></a>エントリ ポイント

名前付きの場所の構成ページにアクセスするには、Azure Active Directory ページの **[セキュリティ]** セクションで以下をクリックします。

![エントリ ポイント](./media/active-directory-named-locations/34.png)

- **条件付きアクセス:**

    - **[管理]** セクションで、**[名前付きの場所]** をクリックします。
    
        ![[名前付きの場所] コマンド](./media/active-directory-named-locations/06.png)

- **危険なサインイン:**

    - 上部のツール バーで、**[既知の IP アドレス範囲の追加]** をクリックします。

       ![[名前付きの場所] コマンド](./media/active-directory-named-locations/35.png)



## <a name="configuration-example"></a>構成の例

**名前付きの場所は、次のように構成します。**

1. [Azure ポータル](https://portal.azure.com)にグローバル管理者としてサインインします。

2. 左側のウィンドウで、**[Azure Active Directory]** をクリックします。

    ![左側のウィンドウの [Azure Active Directory] リンク](./media/active-directory-named-locations/01.png)

3. **[Azure Active Directory]** ページの **[セキュリティ]** セクションで、**[条件付きアクセス]** をクリックします。

    ![[条件付きアクセス] コマンド](./media/active-directory-named-locations/05.png)


4. **[条件付きアクセス]** ページの **[管理]** セクションで、**[名前付きの場所]** をクリックします。

    ![[名前付きの場所] コマンド](./media/active-directory-named-locations/06.png)


5. **[名前付きの場所]** ページで、**[新しい場所]** をクリックします。

    ![[新しい場所] コマンド](./media/active-directory-named-locations/07.png)


6. **[新規]** ページで、次の操作を行います。

    ![[新規] ブレード](./media/active-directory-named-locations/61.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[名前]** ボックスに、名前付きの場所の名前を入力します。

    b. **[IP 範囲]** ボックスに、IP 範囲を入力します。 IP 範囲は *Classless Inter-Domain Routing* (CIDR) 形式である必要があります。  

    c. **Create** をクリックしてください。



## <a name="next-steps"></a>次の手順

詳細については、次を参照してください。

- [Azure Active Directory の条件付きアクセス](active-directory-conditional-access-azure-portal.md)

- [Azure Active Directory の条件付きアクセスの場所の条件](active-directory-conditional-access-locations.md)

- [Azure Active Directory リスク イベント](active-directory-reporting-risk-events.md)

- [Azure Active Directory ポータルのリスクの高いサインイン レポート](active-directory-reporting-security-risky-sign-ins.md)  
