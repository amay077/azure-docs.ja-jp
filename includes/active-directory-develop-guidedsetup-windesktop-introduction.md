---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 79b6b8e5d81d3885f9c125f971f3e32e695cf2b1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
ms.locfileid: "32200617"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Windows デスクトップ アプリから Microsoft Graph API を呼び出す

このガイドでは、ネイティブの Windows デスクトップ .NET (XAML) アプリケーションでアクセス トークンを取得し、Microsoft Graph API またはアクセス トークンを必要とする他の API を、Azure Active Directory v2 エンドポイントから呼び出す方法について説明します。

このガイドを完了すると、アプリケーションで個人アカウント (outlook.com、live.com など) を使用する保護された API を呼び出すことができるようになります。 このアプリケーションでは、Azure Active Directory を使用する会社または組織の職場および学校のアカウントも使用します。  

> [!NOTE] 
> このガイドでは、Visual Studio 2015 Update 3 または Visual Studio 2017 が必要です。  どちらのバージョンもお持ちでない場合は、 [Visual Studio 2017 を無料でダウンロードできます](https://www.visualstudio.com/downloads/)。

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>このガイドで生成されたサンプル アプリの動作

![このガイドの利用法](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.png)

このガイドで作成するサンプル アプリケーションにより、Windows デスクトップ アプリケーションで、Microsoft Graph API または Azure Active Directory v2 エンドポイントからトークンを受け取る Web API に対してクエリを実行できるようになります。 このシナリオでは、Authorization ヘッダーを使用して HTTP 要求にトークンを追加します。 トークンの取得と更新は、Microsoft Authentication Library (MSAL) で処理されます。

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>保護された Web API にアクセスするためのトークン取得処理

ユーザーが認証されると、サンプル アプリケーションは、Microsoft Graph API または Azure Active Directory v2 で保護された Web API でのクエリ実行に使用できるトークンを受け取ります。

Microsoft Graph などの API では、特定のリソースへのアクセスを許可するためにトークンが必要になります。 たとえば、トークンは、ユーザーのプロファイルの読み取り、ユーザーの予定表へのアクセス、メールの送信などに必要です。 アプリケーションでは、MSAL を使用してアクセス トークンを要求し、API スコープを指定することによってこれらのリソースにアクセスできます。 このアクセス トークンは、保護されたリソースに対するすべての呼び出しで HTTP Authorization ヘッダーに追加されます。 

アクセス トークンのキャッシュと更新は MSAL が管理するため、アプリケーションが管理する必要はありません。

## <a name="nuget-packages"></a>NuGet パッケージ

このガイドでは、次の NuGet パッケージを使用します。

|ライブラリ|[説明]|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL)|

