---
title: Azure Stream Analytics işinizi Temizleme
description: Bu makalede Azure Stream Analytics işlerinizi silmeye yönelik farklı yöntemler gösterilmektedir.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 31812ac805bd3465b1b735842b45adb372286d66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016074"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Azure Stream Analytics işinizi durdurma veya silme

Azure Stream Analytics işleri Azure portal, Azure PowerShell, .NET için Azure SDK veya REST API aracılığıyla kolayca durdurulabilir veya silinebilir. Bir Stream Analytics işi silindikten sonra kurtarılamaz.

>[!NOTE] 
>Stream Analytics işinizi durdurduğunuzda, veriler yalnızca giriş ve çıkış depolamada (örneğin, Event Hubs veya Azure SQL veritabanı) devam ediyor. Verileri Azure 'dan kaldırmanız gerekiyorsa, Stream Analytics işinizin giriş ve çıkış kaynakları için kaldırma işlemini izlediğinizden emin olun.

## <a name="stop-a-job-in-azure-portal"></a>Azure portal bir işi durdurma

Bir işi durdurduğunuzda, kaynakların sağlanması kaldırılır ve olayları işlemeyi durdurur. Bu işle ilgili ücretler de durdurulur. Ancak tüm yapılandırmanız korunur ve işi daha sonra yeniden başlatabilirsiniz 

1. [Azure portalında](https://portal.azure.com) oturum açın. 

2. Çalışan Stream Analytics işinizi bulun ve seçin.

3. İşi durdurmak için Stream Analytics işi sayfasında **Durdur** ' u seçin. 

   ![Azure Stream Analytics işini durdur](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Azure portal bir işi silme

>[!WARNING] 
>Bir Stream Analytics işi silindikten sonra kurtarılamaz.

1. Azure portalında oturum açın. 

2. Mevcut Stream Analytics işinizi bulun ve seçin.

3. İşi silmek için Stream Analytics işi sayfasında **Sil** ' i seçin. 

   ![Azure Stream Analytics Işini Sil](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>PowerShell 'i kullanarak bir işi durdurma veya silme

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell 'i kullanarak bir işi durdurmak için [stop-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) cmdlet 'ini kullanın. PowerShell 'i kullanarak bir işi silmek için [Remove-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) cmdlet 'ini kullanın.

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>.NET için Azure SDK 'Yı kullanarak bir işi durdurma veya silme

.NET için Azure SDK 'yı kullanarak bir işi durdurmak için [Streamingjobsoperationsextensions. BeginStop](/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop) metodunu kullanın. .NET için Azure SDK, [Streamingjobsoperationsextensions. BeginDelete](/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete) yöntemi kullanarak bir işi silmek için.

## <a name="stop-or-delete-a-job-using-rest-api"></a>REST API kullanarak bir işi durdurma veya silme

REST API kullanarak bir işi durdurmak için [stop](/rest/api/streamanalytics/2016-03-01/streamingjobs/stop) yöntemine bakın. REST API kullanarak bir işi silmek için, [Delete](/rest/api/streamanalytics/2016-03-01/streamingjobs/delete) yöntemine bakın.