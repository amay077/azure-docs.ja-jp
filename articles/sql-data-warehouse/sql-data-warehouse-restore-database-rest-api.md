---
title: Azure SQL Data Warehouse の復元 - REST API | Microsoft Docs
description: REST API を使用して Azure SQL Data Warehouse を復元します。
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 2e1874fdf7c11d98d369072739c5937caffe6e96
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2018
ms.locfileid: "31524426"
---
# <a name="restore-an-azure-sql-data-warehouse-with-rest-apis"></a>REST API を使用して Azure SQL Data Warehouse を復元する
REST API を使用して Azure SQL Data Warehouse を復元します。

## <a name="before-you-begin"></a>開始する前に
**DTU 容量を確認します。** 各 SQL Data Warehouse は、既定の [DTU クォータ](../sql-database/sql-database-what-is-a-dtu.md)が割り当てられている論理 SQL サーバー (例: myserver.database.windows.net) でホストされます。  SQL Data Warehouse を復元する前に、データベースの復元に必要な量の DTU クォータがSQL server に残っていることを確認してください。 より多くの DTU を要求するために、[サポート チケットを作成](sql-data-warehouse-get-started-create-support-ticket.md)できます。

## <a name="restore-an-active-or-paused-data-warehouse"></a>アクティブまたは一時停止中のデータ ウェアハウスを復元する
データ ウェアハウスを復元するには、次の手順に従います。

1. 「データベース復元ポイントの取得」操作を使用して、データベースの復元ポイントの一覧を取得します。
2. 「 [データベースの復元要求の作成](https://msdn.microsoft.com/library/azure/dn509571.aspx) 」の操作を使用して、復元を開始します。
3. 「 [データベース操作の状態](https://msdn.microsoft.com/library/azure/dn720371.aspx) 」の操作を使用して、復元の状態を追跡します。

> [!NOTE]
> 復元が完了したら、「[復旧後のデータベースの構成](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery)」の手順に従って、復旧されたデータ ウェアハウスを構成できます。
> 
> 

## <a name="restore-a-deleted-data-warehouse"></a>削除されたデータ ウェアハウスを復元する
削除されたデータ ウェアハウスを復元するには、次の手順に従います。

1. 「[削除された復元可能なデータベースの一覧表示](https://msdn.microsoft.com/library/azure/dn509562.aspx)」の操作を使用して、復元可能なすべての削除済みデータ ウェアハウスの一覧を表示します。
2. [削除された復元可能なデータベースを取得する][削除された復元可能なデータベースを取得する]操作を使用して、復元する必要のある削除済みデータ ウェアハウスの詳細を取得します。
3. 「 [データベースの復元要求の作成](https://msdn.microsoft.com/library/azure/dn509571.aspx) 」の操作を使用して、復元を開始します。
4. 「 [データベース操作の状態](https://msdn.microsoft.com/library/azure/dn720371.aspx) 」の操作を使用して、復元の状態を追跡します。

> [!NOTE]
> 復元が完了した後にデータ ウェアハウスを構成する方法については、「[復旧後のデータベースの構成](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery)」を参照してください。
> 
> 

## <a name="next-steps"></a>次の手順
Azure SQL Database の各エディションのビジネス継続性機能については、 [Azure SQL Database のビジネス継続性の概要](../sql-database/sql-database-business-continuity.md)に関するページをご覧ください。
