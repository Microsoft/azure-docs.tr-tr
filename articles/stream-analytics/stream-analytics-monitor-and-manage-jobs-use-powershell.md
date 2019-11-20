---
title: PowerShell ile Azure Stream Analytics işlerini izleme ve yönetme
description: Bu makalede Azure Stream Analytics işleri izlemek ve yönetmek için Azure PowerShell ve cmdlet 'lerinin nasıl kullanılacağı açıklanır.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 57dee438dca81d46d2bdfda0c7f255f32f203d60
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72925118"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Azure PowerShell cmdlet 'leriyle Stream Analytics işlerini izleme ve yönetme
Temel Stream Analytics görevlerini çalıştıran Azure PowerShell cmdlet 'leri ve PowerShell betiği ile Stream Analytics kaynakları izlemeyi ve yönetmeyi öğrenin.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Stream Analytics için Azure PowerShell cmdlet 'leri çalıştırmaya yönelik önkoşullar
* Aboneliğinizde bir Azure Kaynak grubu oluşturun. Aşağıda örnek bir Azure PowerShell betiği verilmiştir. Azure PowerShell bilgi için bkz. [Azure PowerShell 'Yi yükleyip yapılandırma](/powershell/azure/overview);  

Azure PowerShell 0.9.8:  

```powershell
# Log in to your Azure account
Add-AzureAccount
# Select the Azure subscription you want to use to create the resource group if you have more han one subscription on your account.
Select-AzureSubscription -SubscriptionName <subscription name>
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```

Azure PowerShell 1,0:

```powershell
# Log in to your Azure account
Connect-AzAccount
# Select the Azure subscription you want to use to create the resource group.
Get-AzSubscription –SubscriptionName "your sub" | Select-AzSubscription
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```


> [!NOTE]
> Program aracılığıyla oluşturulan Stream Analytics işlerin varsayılan olarak izleme özelliği yoktur.  Azure portalında izlemeyi, işin Izleyici sayfasına giderek ve Etkinleştir düğmesine tıklayarak veya [Azure Stream Analytics izleme Stream Analytics işlerinde bulunan adımları izleyerek programlı bir şekilde etkinleştirebilirsiniz. Programlı olarak](stream-analytics-monitor-jobs.md).
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Stream Analytics için Azure PowerShell cmdlet 'leri
Aşağıdaki Azure PowerShell cmdlet 'leri Azure Stream Analytics işleri izlemek ve yönetmek için kullanılabilir. Azure PowerShell farklı sürümlere sahip olduğunu unutmayın. 
**İlk komut listelenen örneklerde Azure PowerShell 0.9.8 için ikinci komut, Azure PowerShell 1,0 ' dir.** Azure PowerShell 1,0 komutlarının her zaman komutta "az" olması gerekir.

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzStreamAnalyticsJob
Azure aboneliğinde veya belirtilen kaynak grubunda tanımlanan tüm Stream Analytics işleri listeler veya bir kaynak grubu içindeki belirli bir işle ilgili iş bilgilerini alır.

**Örnek 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsJob
```

Bu PowerShell komutu, Azure aboneliğindeki tüm Stream Analytics işleri hakkında bilgi döndürür.

**Örnek 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Bu PowerShell komutu, kaynak grubundaki tüm Stream Analytics işlerle ilgili bilgileri döndürür StreamAnalytics-default-orta-US.

**Örnek 3**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Bu PowerShell komutu, StreamAnalytics-varsayılan-Orta-ABD kaynak grubundaki Stream Analytics Job StreamingJob hakkında bilgi döndürür.

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzStreamAnalyticsInput
Belirtilen bir Stream Analytics işinde tanımlanan tüm girişleri listeler veya belirli bir girdi hakkında bilgi alır.

**Örnek 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Bu PowerShell komutu, iş StreamingJob içinde tanımlanan tüm girişlerle ilgili bilgileri döndürür.

**Örnek 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream
```

Bu PowerShell komutu, iş StreamingJob içinde tanımlanan EntryStream adlı giriş hakkında bilgi döndürür.

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzStreamAnalyticsOutput
Belirtilen bir Stream Analytics işinde tanımlanan tüm çıktıları listeler veya belirli bir çıktı hakkında bilgi alır.

**Örnek 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Bu PowerShell komutu, iş StreamingJob içinde tanımlanan çıktılar hakkında bilgi döndürür.

**Örnek 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output
```

Bu PowerShell komutu, iş StreamingJob içinde tanımlanan output adlı çıktı hakkında bilgi döndürür.

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzStreamAnalyticsQuota
Belirtilen bölgedeki akış birimlerinin kotası hakkında bilgi alır.

**Örnek 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsQuota –Location "Central US" 
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsQuota –Location "Central US" 
```

Bu PowerShell komutu, Orta ABD bölgesindeki akış birimlerinin kotası ve kullanımı hakkındaki bilgileri döndürür.

### <a name="get-azurestreamanalyticstransformation--get-azstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | Get-AzStreamAnalyticsTransformation
Stream Analytics işinde tanımlanan belirli bir dönüşüm hakkında bilgi alır.

**Örnek 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob
```

Azure PowerShell 1,0:  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob
```

Bu PowerShell komutu, iş StreamingJob içinde StreamingJob adlı dönüştürme hakkındaki bilgileri döndürür.

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>New-AzureStreamAnalyticsInput | New-AzStreamAnalyticsInput
Stream Analytics işinde yeni bir giriş oluşturur veya belirtilen varolan bir girişi güncelleştirir.

Girişin adı. json dosyasında veya komut satırında belirtilebilir. Her ikisi de belirtilirse, komut satırındaki ad, dosyadaki bir ile aynı olmalıdır.

Zaten var olan ve – zorlama parametresini belirtmeyen bir giriş belirtirseniz cmdlet, var olan girişin değiştirilmesini isteyip istemediğinizi sorar.

– Zorla parametresini belirtirseniz ve var olan bir giriş adı belirtirseniz, giriş, onay olmadan değişir.

JSON dosya yapısı ve içerikleri hakkında ayrıntılı bilgi için [Stream Analytics yönetimi REST API başvuru kitaplığının][stream.analytics.rest.api.reference] [giriş (Azure Stream Analytics) oluşturma][msdn-rest-api-create-stream-analytics-input] bölümüne bakın.

**Örnek 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 
```

Bu PowerShell komutu, Input. json dosyasından yeni bir giriş oluşturur. Giriş tanımı dosyasında belirtilen ada sahip mevcut bir giriş zaten tanımlanmışsa, cmdlet 'in değiştirip etmeyeceğini sorar.

**Örnek 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream
```

Bu PowerShell komutu, iş içinde EntryStream adlı yeni bir giriş oluşturur. Bu ada sahip var olan bir giriş zaten tanımlanmışsa, cmdlet 'in değiştirip etmeyeceğini sorar.

**Örnek 3**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force
```

Bu PowerShell komutu, EntryStream adlı mevcut giriş kaynağının tanımını dosyadaki tanımıyla değiştirir.

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>New-AzureStreamAnalyticsJob | New-AzStreamAnalyticsJob
Microsoft Azure yeni bir Stream Analytics işi oluşturur veya var olan belirtilen bir işin tanımını güncelleştirir.

İş adı. json dosyasında veya komut satırında belirtilebilir. Her ikisi de belirtilirse, komut satırındaki ad, dosyadaki bir ile aynı olmalıdır.

Zaten var olan ve – zorlama parametresini belirtmeyen bir iş adı belirtirseniz cmdlet, var olan işin değiştirilmesini ister.

– Zorla parametresini belirtirseniz ve var olan bir iş adı belirtirseniz, iş tanımı onay olmadan değiştirilmez.

JSON dosya yapısı ve içerikleri hakkında ayrıntılı bilgi için [Stream Analytics yönetimi REST API başvuru kitaplığının][stream.analytics.rest.api.reference] [Stream Analytics iş oluşturma][msdn-rest-api-create-stream-analytics-job] bölümüne bakın.

**Örnek 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 
```

Bu PowerShell komutu, JobDefinition. JSON dosyasındaki tanımdan yeni bir iş oluşturur. İş tanımı dosyasında belirtilen ada sahip mevcut bir iş zaten tanımlanmışsa, cmdlet bunu değiştirip değiştiremeyeceğini sorar.

**Örnek 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force
```

Bu PowerShell komutu, StreamingJob iş tanımının yerini alır.

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>New-AzureStreamAnalyticsOutput | New-AzStreamAnalyticsOutput
Stream Analytics işinde yeni bir çıktı oluşturur veya var olan bir çıktıyı güncelleştirir.  

Çıkışın adı. json dosyasında veya komut satırında belirtilebilir. Her ikisi de belirtilirse, komut satırındaki ad, dosyadaki bir ile aynı olmalıdır.

Zaten var olan ve – zorlama parametresini belirtmeyen bir çıkış belirtirseniz cmdlet, var olan çıktının değiştirilmesini isteyip istemediğinizi sorar.

– Zorla parametresini belirtirseniz ve var olan bir çıkış adı belirtirseniz, çıkış onay olmadan değiştirilmez.

JSON dosya yapısı ve içerikleri hakkında ayrıntılı bilgi için [Stream Analytics yönetimi REST API başvuru kitaplığının][stream.analytics.rest.api.reference] [çıkış oluştur (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-output] bölümüne bakın.

**Örnek 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output
```

Bu PowerShell komutu, iş StreamingJob 'da "output" adlı yeni bir çıktı oluşturur. Bu ada sahip var olan bir çıkış zaten tanımlanmışsa, cmdlet 'in değiştirip etmeyeceğini sorar.

**Örnek 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force
```

Bu PowerShell komutu, iş StreamingJob 'daki "output" tanımının yerini alır.

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>New-AzureStreamAnalyticsTransformation | New-AzStreamAnalyticsTransformation
Stream Analytics işi içinde yeni bir dönüşüm oluşturur veya var olan dönüştürmeyi güncelleştirir.

Dönüşümün adı. json dosyasında veya komut satırında belirtilebilir. Her ikisi de belirtilirse, komut satırındaki ad, dosyadaki bir ile aynı olmalıdır.

Zaten var olan ve – zorlama parametresini belirtmeyen bir dönüşüm belirtirseniz cmdlet, var olan dönüşümün değiştirilmesini isteyip istemediğinizi sorar.

– Zorla parametresini belirtirseniz ve var olan bir dönüştürme adı belirtirseniz, dönüştürme onay olmadan değiştirilmez.

JSON dosya yapısı ve içerikleri hakkında ayrıntılı bilgi için, [Stream Analytics yönetimi REST API başvuru kitaplığı][stream.analytics.rest.api.reference]' nın [dönüştürme (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-transformation] bölümüne bakın.

**Örnek 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform
```

Bu PowerShell komutu, iş StreamingJob içinde StreamingJobTransform adlı yeni bir dönüşüm oluşturur. Var olan bir dönüşüm bu adla zaten tanımlanmışsa, cmdlet bunun değiştirilmesini ister.

**Örnek 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force
```

Azure PowerShell 1,0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force
```

 Bu PowerShell komutu, StreamingJob iş içindeki StreamingJobTransform tanımının yerini alır.

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>Remove-AzureStreamAnalyticsInput | Remove-AzStreamAnalyticsInput
Zaman uyumsuz olarak, Microsoft Azure bir Stream Analytics işinden belirli bir girişi siler.  
– Zorla parametresini belirtirseniz, giriş onaysız olarak silinir.

**Örnek 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream
```

Azure PowerShell 1,0:  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream
```

Bu PowerShell komutu, giriş EventStream 'i iş StreamingJob öğesinde kaldırır.  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>Remove-AzureStreamAnalyticsJob | Remove-AzStreamAnalyticsJob
Microsoft Azure içinde belirli bir Stream Analytics işi zaman uyumsuz olarak silinir.  
– Zorla parametresini belirtirseniz, iş onay olmadan silinir.

**Örnek 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
```

Azure PowerShell 1,0:  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
```

Bu PowerShell komutu, StreamingJob işini kaldırır.  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>Remove-AzureStreamAnalyticsOutput | Remove-AzStreamAnalyticsOutput
Zaman uyumsuz olarak, Microsoft Azure bir Stream Analytics işinden belirli bir çıktıyı siler.  
– Zorla parametresini belirtirseniz, çıkış onay olmadan silinir.

**Örnek 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
```

Azure PowerShell 1,0:  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
```

Bu PowerShell komutu, StreamingJob iş içindeki çıktı çıkışını kaldırır.  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzStreamAnalyticsJob
Zaman uyumsuz olarak Microsoft Azure Stream Analytics işi dağıtır ve başlatır.

**Örnek 1**

Azure PowerShell 0.9.8:  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Azure PowerShell 1,0:  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Bu PowerShell komutu, özel bir çıkış başlangıç saati 12 Aralık 2012, 12:12:12 UTC olarak ayarlanan iş StreamingJob 'u başlatır.

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>Durdur-AzureStreamAnalyticsJob | Stop-AzStreamAnalyticsJob
Zaman uyumsuz bir Stream Analytics işinin Microsoft Azure çalıştırılmasını ve kullanılmakta olan kaynakları serbest olarak ayırmasını engeller. İş tanımı ve meta veriler, hem Azure portal hem de yönetim API 'Leri aracılığıyla aboneliğiniz dahilinde kullanılabilir olmaya devam edecektir. Durdurulmuş durumdaki bir iş için ücretlendirilmeyecektir.

**Örnek 1**

Azure PowerShell 0.9.8:  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
```

Azure PowerShell 1,0:  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
```

Bu PowerShell komutu, StreamingJob işini durduruyor.  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzStreamAnalyticsInput
Stream Analytics, belirtilen girişe bağlanma olanağını sınar.

**Örnek 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream
```

Azure PowerShell 1,0:  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream
```

Bu PowerShell komutu, StreamingJob içindeki input EntryStream 'in bağlantı durumunu sınar.  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzStreamAnalyticsOutput
Stream Analytics, belirtilen çıktıya bağlanma olanağını sınar.

**Örnek 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
```

Azure PowerShell 1,0:  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
```

Bu PowerShell komutu, StreamingJob içindeki çıktı çıkışının bağlantı durumunu sınar.  

## <a name="get-support"></a>Destek alın
Daha fazla yardım için [Azure Stream Analytics Forumumuzu](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)deneyin. 

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Stream Analytics giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Akış Analizi Sorgu Dili Başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](https://msdn.microsoft.com/library/azure/dn835031.aspx)

[msdn-switch-azuremode]: https://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

