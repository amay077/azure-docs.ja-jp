---
title: Azure Key Vault への資格情報の格納 | Microsoft Docs
description: Azure Data Factory で実行時に自動的に取得できる、Azure Key Vault で使用されたデータ ストアの資格情報を格納する方法を説明します。
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: jingwang
ms.openlocfilehash: f12c3a2a1193adc08c408292a4e0eca2e2458085
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620668"
---
# <a name="store-credential-in-azure-key-vault"></a>Azure Key Vault への資格情報の格納

データ ストアや計算のための資格情報を [Azure Key Vault](../key-vault/key-vault-whatis.md) に格納することができます。 Azure Data Factory は、データ ストア/計算を使うアクティビティの実行時に、資格情報を取得します。

現時点では、カスタム アクティビティを除くすべてのアクティビティの種類が、この機能をサポートしています。 具体的なコネクタの構成について詳しくは、[各コネクタ トピック](copy-activity-overview.md#supported-data-stores-and-formats)の「リンクされたサービスのプロパティ」セクションをご覧ください。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 のドキュメント](v1/data-factory-introduction.md)を参照してください。

## <a name="prerequisites"></a>前提条件

この機能は、データ ファクトリのサービス ID に依存しています。 [データ ファクトリのサービス ID](data-factory-service-identity.md) からの使用方法と、データ ファクトリに関連付けられていることを確認する方法について説明します。

>[!TIP]
>Azure Key Vault でシークレットを作成するときには、**ADF のリンクされたサービスで要求されるシークレット プロパティの値全体 (例: 接続文字列/パスワード/サービス プリンシパル キー/その他)** を指定します。 たとえば、Azure Storage のリンクされたサービスの場合、`DefaultEndpointsProtocol=http;AccountName=myAccount;AccountKey=myKey;` を AKV シークレットとして指定し、次に ADF からの "connectionString" フィールドで参照します。Dynamics のリンクされたサービスの場合は、AKV シークレットとして `myPassword` を指定し、次に ADF からの "paassword" フィールドで参照します。 サポートされているプロパティの詳細については、各コネクタとコンピューティングの記事を参照してください。

## <a name="steps"></a>手順

Azure Key Vault に格納されている資格情報を参照するには、次の手順に従う必要があります。

1. ファクトリと共に生成された "サービス ID アプリケーション ID" の値をコピーして、**データ ファクトリのサービス ID を取得**します。 ADF オーサリング UI を使用する場合、サービス ID が Azure Key Vault のリンクされたサービスの作成ウィンドウに表示されます。Azure portal から取得することもできます。「[Retrieve data factory service identity](data-factory-service-identity.md#retrieve-service-identity)」(データ ファクトリのサービス ID の取得) を参照してください。
2. **サービス ID に、Azure Key Vault へのアクセス権を付与します。** キー コンテナーで、[アクセス ポリシー] -> [新規追加] を選択し、このサービス ID アプリケーション ID を検索して、[シークレットのアクセス許可] ドロップダウンで **Get** アクセス許可を付与します。 この指定されたファクトリで、キー コンテナー内のシークレットにアクセスできます。
3. **Azure Key Vault をポイントするリンクされたサービスを作成します。** 「[Azure Key Vault のリンクされたサービス](#azure-key-vault-linked-service)」をご覧ください。
4. **データ ストアのリンクされたサービスを作成します。その内部で、キー コンテナーに格納されている対応するシークレットを参照します。** 「[キー コンテナーに格納されたシークレットの参照](#reference-secret-stored-in-key-vault)」をご覧ください。

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault のリンクされたサービス

Azure Key Vault のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| 型 | type プロパティは **AzureKeyVault** に設定する必要があります。 | [はい] |
| baseUrl | Azure Key Vault の URL を指定します。 | [はい] |

**オーサリング UI の使用:**

**[接続]** -> **[リンクされたサービス]** -> **[+ 新規]** の順にクリックし、"Azure Key Vault" を検索します。

![AKV の検索](media/store-credentials-in-key-vault/search-akv.png)

資格情報が格納されるプロビジョニングされた Azure Key Vault を選択します。 **テスト接続**を実行し、AKV 接続が有効なことを確認します。 

![AKV の構成](media/store-credentials-in-key-vault/configure-akv.png)

**JSON の例:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
            "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-secret-stored-in-key-vault"></a>キー コンテナーに格納されたシークレットの参照

キー コンテナーのシークレットを参照するリンクされたサービスのフィールドを構成する場合は、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| 型 | フィールドの type プロパティは **AzureKeyVaultSecret** に設定する必要があります。 | [はい] |
| secretName | Azure Key Vault のシークレットの名前。 | [はい] |
| secretVersion | Azure Key Vault のシークレットのバージョン。<br/>指定しない場合は、常に最新バージョンのシークレットが使用されます。<br/>指定した場合は、その特定のバージョンに固定されます。| いいえ  |
| store | 資格情報の格納に使用する Azure Key Vault のリンクされたサービスを表します。 | [はい] |

**オーサリング UI の使用:**

データストア/コンピューティングへの接続を作成するときに、シークレット フィールドに対して **Azure Key Vault** を選択します。 プロビジョニングされた Azure Key Vault のリンクされたサービスを選択し、**シークレット名**を指定します。 シークレット バージョンも必要に応じて指定できます。 

![AKV シークレットの構成](media/store-credentials-in-key-vault/configure-akv-secret.png)

**JSON の例: ("password" セクションをご覧ください)**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
