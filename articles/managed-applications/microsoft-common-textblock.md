---
title: Azure の TextBlock UI 要素 | Microsoft Docs
description: Azure Portal の Microsoft.Common.TextBlock UI 要素について説明します。
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/30/2018
ms.author: tomfitz
ms.openlocfilehash: 9026313b5022dd4d29bbe1d7e80cc1d51b89516a
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260825"
---
# <a name="microsoftcommontextblock-ui-element"></a>Microsoft.Common.TextBlock UI 要素
ポータルのインターフェイスにテキストを追加するために使用できるコントロールです。

## <a name="ui-sample"></a>UI サンプル
![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textblock.png)

## <a name="schema"></a>スキーマ
```json
{
  "name": "text1",
  "type": "Microsoft.Common.TextBlock",
  "visible": true,
  "options": {
    "text": "Look! Arbitrary text in templates!",
    "link": {
      "label": "Learn more",
      "uri": "https://www.microsoft.com"
    }
  }
}
```

## <a name="sample-output"></a>サンプル出力

```json
"Look! Arbitrary text in templates! Learn more"
```

## <a name="next-steps"></a>次の手順
* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
