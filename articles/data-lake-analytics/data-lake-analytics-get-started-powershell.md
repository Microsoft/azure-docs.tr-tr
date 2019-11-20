---
title: '& Sorgu oluşturma Azure Data Lake Analytics-PowerShell'
description: Azure PowerShell kullanarak Azure Data Lake Analytics hesabı oluşturun ve bir U-SQL işi gönderin.
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.topic: conceptual
ms.date: 05/04/2017
ms.openlocfilehash: 8b176434ce450382cc6463e7d21d341a74581ed8
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316604"
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Azure PowerShell'i kullanarak Azure Data Lake Analytics ile çalışmaya başlama

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure PowerShell kullanarak Azure Data Lake Analytics hesapları oluşturma ve sonra U-SQL işleri gönderip çalıştırma hakkında bilgi edinin. Data Lake Analytics hakkında daha fazla bilgi için bkz. [Azure Data Lake Analytics'e genel bakış](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu öğreticiye başlamadan önce aşağıdaki bilgilere sahip olmanız gerekir:

* **Azure Data Lake Analytics hesabı**. Bkz. [Data Lake Analytics ile çalışmaya başlama](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).
* **Azure PowerShell içeren bir iş istasyonu**. Bkz. [Azure PowerShell'i yükleme ve yapılandırma](/powershell/azure/overview).

## <a name="log-in-to-azure"></a>Azure'da oturum açma

Bu öğreticide, Azure PowerShell kullanımıyla ilgili bilgi sahibi olduğunuz varsayılır. Özellikle Azure'da oturum açmayı bilmeniz gerekir. Yardıma ihtiyacınız varsa bkz. [Azure PowerShell ile çalışmaya başlama](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Abonelik adı ile oturum açmak için:

```powershell
Connect-AzAccount -SubscriptionName "ContosoSubscription"
```

Oturum açmak için abonelik adı yerine abonelik kimliğini de kullanabilirsiniz:

```powershell
Connect-AzAccount -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

Başarılı olması halinde bu komutun çıkışı şu metin gibi görünür:

```
Environment           : AzureCloud
Account               : joe@contoso.com
TenantId              : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionId        : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionName      : ContosoSubscription
CurrentStorageAccount :
```

## <a name="preparing-for-the-tutorial"></a>Öğreticiye hazırlanma

Bu öğreticideki PowerShell kod parçacıkları bu bilgileri depolamak için aşağıdaki değişkenleri kullanır:

```powershell
$rg = "<ResourceGroupName>"
$adls = "<DataLakeStoreAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Bir Data Lake Analytics hesabı hakkında bilgi edinme

```powershell
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>U-SQL işi gönderme

U-SQL betiğini tutmak için bir PowerShell değişkeni oluşturun.

```powershell
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();

"@
```

Betik metnini `Submit-AdlJob` cmdlet'i ve `-Script` parametresi ile gönderin.

```powershell
$job = Submit-AdlJob -Account $adla -Name "My Job" -Script $script
```

Alternatif olarak, `-ScriptPath` parametresini kullanarak da bir betik dosyası gönderebilirsiniz:

```powershell
$filename = "d:\test.usql"
$script | out-File $filename
$job = Submit-AdlJob -Account $adla -Name "My Job" -ScriptPath $filename
```

`Get-AdlJob` ile bir işin durumunu alın. 

```powershell
$job = Get-AdlJob -Account $adla -JobId $job.JobId
```

Bir iş tamamlanana kadar Get-AdlJob yöntemini tekrar tekrar çağırmak yerine, `Wait-AdlJob` cmdlet’ini kullanabilirsiniz.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

`Export-AdlStoreItem` komutunu kullanarak çıkış dosyasını indirin.

```powershell
Export-AdlStoreItem -Account $adls -Path "/data.csv" -Destination "C:\data.csv"
```

## <a name="see-also"></a>Ayrıca bkz.

* Aynı öğreticiyi diğer araçları kullanarak görmek için sayfanın üst kısmındaki sekme seçicilerine tıklayın.
* U-SQL öğrenmek için bkz. [Azure Data Lake Analytics U-SQL dili ile çalışmaya başlama](data-lake-analytics-u-sql-get-started.md).
* Yönetim görevleri için bkz. [Azure portalı kullanarak Azure Data Lake Analytics'i yönetme](data-lake-analytics-manage-use-portal.md).
