---
title: Azure Windows VM の更新プログラムとパッチの管理
description: この記事では、Azure Automation の更新の管理を使用して、Azure Windows VM の更新プログラムとパッチを管理する方法の概要について説明します。
services: automation
author: zjalexander
ms.service: automation
ms.component: update-management
ms.topic: tutorial
ms.date: 02/28/2018
ms.author: zachal
ms.custom: mvc
ms.openlocfilehash: 84ec2a5852e6aaeb4b9fe6ef11924209d03fb54b
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2018
ms.locfileid: "34054761"
---
# <a name="manage-windows-updates-with-azure-automation"></a>Azure Automation で Windows 更新プログラムを管理する

更新の管理を使用すると、仮想マシンの更新プログラムとパッチを管理できます。
このチュートリアルでは、利用可能な更新プログラムの状態の迅速な評価、必要な更新プログラムのインストールのスケジュール設定、展開の結果の確認、更新プログラムが正常に適用されたことを確認するアラートの作成を行う方法について説明します。

価格情報については、[Update Management の Automation の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 更新管理のために VM をオンボードする
> * 更新の評価を表示する
> * アラートを構成する
> * 更新プログラムのデプロイをスケジュールする
> * デプロイの結果を表示する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

* Azure サブスクリプション。 まだお持ちでない場合は、[Visual Studio サブスクライバー向けの月単位の Azure クレジットをアクティブにする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* 監視およびアクションの Runbook と監視タスクを保持する、[Automation アカウント](automation-offering-get-started.md)。
* オンボードする[仮想マシン](../virtual-machines/windows/quick-create-portal.md)。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure Portal (https://portal.azure.com) にログインします。

## <a name="enable-update-management"></a>Update Management の有効化

まず、このチュートリアル用に VM の更新管理を有効にする必要があります。

1. Azure portal の左側のメニューで **[仮想マシン]** を選択し､一覧から VM を選択します。
2. VM ページの **[操作]** セクションで、**[更新の管理]** をクリックします。 **[更新管理の有効化]** ページが開きます。

この VM で Update Management が有効になっているかを確認する検証が行われます。 この検証では、Log Analytics ワークスペースの確認、リンクされた Automation アカウントの確認、Update Management ソリューションがワークスペースにあるかどうかの確認が行われます。

[Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) ワークスペースは、Update Management のような機能およびサービスによって生成されるデータを収集するために使用されます。 ワークスペースには、複数のソースからのデータを確認および分析する場所が 1 つ用意されています。

また、検証プロセスでは、VM が Microsoft Monitoring Agent (MMA) と Automation ハイブリッド Runbook worker でプロビジョニングされているかどうかが確認されます。
このエージェントは、Azure Automation と通信し、更新の状態に関する情報を取得するために使用されます。 エージェントでは、Azure Automation サービスと通信したり、更新プログラムをダウンロードしたりするために、ポート 443 を開く必要があります。

オンボード中に次の前提条件のいずれかを満たしていないことがわかった場合は、自動的に追加されます。

* [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) ワークスペース
* [Automation アカウント](./automation-offering-get-started.md)
* [Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md) が VM で有効になっている

**[更新の管理]** 画面が開きます。 使用する場所、Log Analytics ワークスペース、Automation アカウントを構成し、**[有効にする]** をクリックします。 フィールドが淡色表示されている場合は、その VM で別の Automation ソリューションが有効になっているため、同じワークスペースと Automation アカウントを使用する必要があることを示します。

![[更新管理の有効化] ソリューション ウィンドウ](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

ソリューションの有効化には、最大数分かかることがあります。 この処理中はブラウザーのウィンドウは閉じないでください。
ソリューションが有効になると、VM 上の不足している更新プログラムの情報が Log Analytics に送られます。
データの分析に使用できるようになるまでに、30 分から 6 時間かかる場合があります。

## <a name="view-update-assessment"></a>更新の評価を確認する

**更新管理**が有効になると、**[更新管理]** 画面が表示されます。
不足している更新プログラムがある場合は、**[不足している更新プログラム]** タブに、デプロイされていない更新プログラムの一覧が表示されます。

更新プログラムの **[情報リンク]** を選択すると、新しいウィンドウに更新プログラムのサポート記事が表示されます。 この記事で、更新プログラムに関する重要情報を確認できます。

![更新ステータスを確認する](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

更新プログラムの別の場所をクリックすると、選択した更新プログラムの **[ログ検索]** ウィンドウが表示されます。 その更新プログラムについて、ログ検索のクエリが事前に定義されています。 このクエリを変更するか、独自のクエリを作成して、環境に展開されている更新プログラムまたは不足している更新プログラムに関する詳細情報を確認します。

![更新ステータスを確認する](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerting"></a>アラートを構成する

この手順では、更新プログラムが正常に展開されたときに通知するアラートを構成します。 作成するアラートは、Log Analytics クエリに基づいています。 さまざまなシナリオに対応するために、追加のアラート用のカスタム クエリを作成できます。 Azure portal 上で **[監視]** に移動し、**[アラートの作成]** をクリックします。 **[ルールの作成]** ページが開きます。

**[1. アラートの条件を定義します]** で、**[+ ターゲットの選択]** をクリックします。 **[Filter by resource type]\(リソースの種類でフィルター処理\)** で **[Log Analytics]** を選択します。 Log Analytics ワークスペースを選択し、**[完了]** をクリックします。

![アラートの作成](./media/automation-tutorial-update-management/create-alert.png)

**[+ Add criteria]\(+ 条件の追加\)** をクリックして、**[シグナル ロジックの構成]** ページを開きます。 表の **[Custom log search]\(カスタム ログ検索\)** を選択します。 **[検索クエリ]** ボックスに次のクエリを入力します。 このクエリでは、コンピューターと、指定した時間枠に完了した更新実行名が返されます。

```loganalytics
UpdateRunProgress
| where InstallationStatus == 'Succeeded'
| where TimeGenerated > now(-10m)
| summarize by UpdateRunName, Computer
```

アラート ロジックの **[しきい値]** として「**1**」を入力します。 終了したら、**[完了]** をクリックします。

![シグナル ロジックの構成](./media/automation-tutorial-update-management/signal-logic.png)

**[2. アラートの詳細を定義します]** で、アラートのフレンドリ名と説明を入力します。 このアラートは正常に実行されたことを通知するためのものであるため、**[重大度]** を **[Informational(Sev 2)]\(情報(重大度 2)\)** に設定します。

![シグナル ロジックの構成](./media/automation-tutorial-update-management/define-alert-details.png)

**[3. アクション グループを定義します]** で、**[+ 新しいアクション グループ]** をクリックします。 アクション グループとは、複数のアラートで使用できるアクションのグループです。 アクションには、電子メール通知、Runbook、webhook などがありますが、これらに限定されるわけではありません。 アクション グループの詳細については、[アクション グループの作成と管理](../monitoring-and-diagnostics/monitoring-action-groups.md)に関する記事をご覧ください。

**[アクション グループ名]** ボックスにフレンドリ名を入力し、短い名前を指定します。 短い名前は、通知がこのグループを使用して送信されるときに長い名前の代わりに使用されます。

**[アクション]** で、アクションに "**電子メール通知**" のようなフレンドリ名を付け、**[アクションの種類]** で **[電子メール/SMS/プッシュ/音声]** を選択します。 **[詳細]** で、**[詳細の編集]** を選択します。

**[電子メール/SMS/プッシュ/音声]** ページで、名前を指定します。 **[電子メール]** チェック ボックスをオンにし、使用する有効な電子メール アドレスを入力します。

![電子メール アクション グループの構成](./media/automation-tutorial-update-management/configure-email-action-group.png)

**[電子メール/SMS/プッシュ/音声]** ページで **[OK]** をクリックしてページを閉じ、**[OK]** をクリックして **[アクション グループの追加]** ページを閉じます。

送信する電子メールの件名をカスタマイズするには、**[ルールの作成]** ページの **[アクションをカスタマイズする]** で、**[電子メールの件名]** をクリックします。 完了したら、**[アラート ルールの作成]** をクリックします。 これにより、更新プログラムの展開が成功したことと、その更新プログラムの展開の実行対象となったコンピューターを通知するルールが作成されます。

## <a name="schedule-an-update-deployment"></a>更新プログラムのデプロイをスケジュールする

アラートが構成されたので、次に、リリース スケジュールとサービス時間帯に従って更新プログラムをインストールする展開をスケジュールします。
デプロイに含める更新の種類を選択できます。
たとえば、緊急更新プログラムやセキュリティ更新プログラムを追加し、更新プログラムのロールアップを除外できます。

> [!WARNING]
> 更新に再起動が必要な場合、VM は自動的に再起動されます。

**[更新の管理]** 画面に戻り、画面の上部にある **[更新プログラムの展開のスケジュール]** を選択して、VM の新しい更新プログラムの展開スケジュールを設定します。

**[新しい更新プログラムの展開]** 画面で、次の情報を指定します。

* **[名前]** - 更新プログラムの展開に一意の名前を入力します。

* **[オペレーティング システム]** - 更新プログラムの展開の対象となる OS を選択します。

* **更新プログラムの分類** - 更新プログラムのデプロイによってデプロイに追加されたソフトウェアの種類を選択します。 このチュートリアルでは、すべての種類を選択したままにします。

  分類の種類は次のとおりです。

   |OS  |type  |
   |---------|---------|
   |Windows     | 緊急更新プログラム</br>セキュリティ更新プログラム</br>更新プログラムのロールアップ</br>Feature Pack</br>Service Pack</br>定義の更新</br>ツール</br>更新プログラム        |
   |Linux     | 緊急更新プログラムとセキュリティ更新プログラム</br>他の更新プログラム       |

   分類の種類の詳細については、「[Update classifications (更新プログラムの分類)](automation-update-management.md#update-classifications)」をご覧ください。

* **[スケジュール設定]** - [スケジュール設定] ページを開きます。 既定の開始時刻は、現在の時刻の 30 分後です。 これは、10 分後以降の任意の時刻に設定できます。

   デプロイを 1 回行うか、定期的なスケジュールを設定するかを指定することもできます。
   **[繰り返し]** で **[1 回]** を選択します。 既定値の 1 日のままで **[OK]** をクリックします。 これで定期的なスケジュールが設定されます。

* **[メンテナンス期間 (分)]** - この値は既定値のままにします。 更新プログラムを展開する期間を指定できます。 この設定により、定義したサービス期間内は変更が確実に実行されます。

![更新プログラムのスケジュール設定画面](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

スケジュールの構成が完了したら、**[作成]** ボタンをクリックします。 状態ダッシュボードに戻ります。 **[スケジュールされた更新プログラムの展開]** を選択して、作成した展開スケジュールを表示します。

## <a name="view-results-of-an-update-deployment"></a>更新プログラムのデプロイの結果を表示する

スケジュールされた展開の開始後、**[更新管理]** 画面の **[更新プログラムの展開]** タブに、展開の状態が表示されます。
実行中の場合、状態は **[処理中]** と表示されます。
正常に完了すると、状態は **[成功]** に変わります。
展開時に 1 つ以上の更新プログラムでエラーが発生した場合、状態は **[部分的に失敗]** になります。
完了した更新プログラムのデプロイをクリックすると、その更新プログラムのデプロイ用のダッシュボードが表示されます。

![特定のデプロイに関する更新プログラムのデプロイ ステータスのダッシュボード](./media/automation-tutorial-update-management/manageupdates-view-results.png)

**[更新プログラムを実行した結果]** タイルの概要には、VM 上の更新プログラムの合計数と展開結果が表示されます。
右側の表には、各更新プログラムとインストールの結果の詳細が示されます。
使用できる値の一覧を次に示します。

* **試行されていません**: メンテナンス期間として定義した時間が十分ではなかったため、更新プログラムがインストールされませんでした。
* **成功**: 更新できました。
* **失敗**: 更新できませんでした。

**[すべてのログ]** をクリックし、デプロイで作成されたすべてのログ エントリを確認します。

**[出力]** タイルをクリックし、ターゲット VM での更新プログラムのデプロイを管理する runbook のジョブ ストリームを確認します。

デプロイで発生したエラーの詳細情報を確認するには、**[エラー]** をクリックします。

更新プログラムの展開が成功すると、次の画像のような電子メールが送信され、展開が成功したことが示されます。

![電子メール アクション グループの構成](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 更新管理のために VM をオンボードする
> * 更新の評価を表示する
> * アラートを構成する
> * 更新プログラムのデプロイをスケジュールする
> * デプロイの結果を表示する

更新の管理ソリューションの概要に進みます。

> [!div class="nextstepaction"]
> [更新の管理ソリューション](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json)
