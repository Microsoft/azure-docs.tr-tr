---
title: Bir Azure Data Factory işlem hattında özel etkinlikler kullanma
description: Özel etkinlikler oluşturur ve bunları bir Azure Data Factory işlem hattında kullanma hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/26/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 8076ae81b111aa6b524b7e286ed15ca0661d748b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67062119"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Bir Azure Data Factory işlem hattında özel etkinlikler kullanma

> [!div class="op_single_selector" title1="Data Factory hizmetinin kullandığınız sürümü seçin:"]
> * [Sürüm 1](v1/data-factory-use-custom-activities.md)
> * [Geçerli sürüm](transform-data-using-dotnet-custom-activity.md)

Kullanabileceğiniz bir Azure Data Factory işlem hattı etkinlikleri iki tür vardır.

- [Veri taşıma etkinlikleri](copy-activity-overview.md) arasında veri taşımak için [kaynak ve havuz veri deposu desteklenen](copy-activity-overview.md#supported-data-stores-and-formats).
- [Veri dönüştürme etkinlikleri](transform-data.md) verileri dönüştürmek için kullanma gibi işlem hizmetlerini Azure HDInsight, Azure Batch ve Azure Machine Learning.

Taşımak için veri gönderip buralardan veri Data Factory desteklemiyor veya dönüştürebilen/Data Factory tarafından desteklenmeyen bir yolla veri için oluşturabileceğiniz depolamak bir **özel etkinlik** kendi veri hareketi veya dönüştürme mantığını ve kullanın işlem hattında etkinlik. Özel Etkinlik özelleştirilmiş kod mantığınızı üzerinde çalıştığı bir **Azure Batch** sanal makine havuzu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure Batch hizmetine yeni başladıysanız makalelerini takip bakın:

* [Azure Batch temel bilgileri](../batch/batch-technical-overview.md) için Azure Batch hizmetine genel bakış.
* [Yeni AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) Azure Batch hesabı oluşturmak için cmdlet'i (veya) [Azure portalında](../batch/batch-account-create-portal.md) Azure portalını kullanarak Azure Batch hesabı oluşturmak için. Bkz: [Azure Batch hesabını yönetmek için PowerShell kullanma](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) makale cmdlet kullanma hakkında ayrıntılı yönergeler için.
* [Yeni AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) bir Azure Batch havuzu oluşturmak için cmdlet'i.

## <a name="azure-batch-linked-service"></a>Azure Batch bağlı hizmeti

Aşağıdaki JSON örneği bağlı Azure Batch hizmeti tanımlar. Ayrıntılar için bkz [Azure Data Factory tarafından desteklenen ortam işlem](compute-linked-services.md)

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
}
```

 Bağlı Azure Batch hizmeti hakkında daha fazla bilgi için bkz: [işlem bağlı Hizmetleri](compute-linked-services.md) makalesi.

## <a name="custom-activity"></a>Özel etkinlik

Aşağıdaki JSON kod parçacığında, bir basit özel etkinliği ile işlem hattı tanımlar. Etkinlik tanımı bağlı Azure Batch hizmeti bir başvuru içeriyor.

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "helloworld.exe",
        "folderPath": "customactv2/helloworld",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }]
  }
}
```

Bu örnekte, helloworld.exe resourceLinkedService içinde kullanılan Azure depolama hesabı customactv2/helloworld klasöründe depolanan bir özel bir uygulamadır. Özel Etkinlik, Azure Batch'te yürütülecek özel bu uygulama gönderir. Azure Batch havuzu düğümlerin işletim sistemi hedefinde yürütülüp herhangi bir tercih edilen uygulama komutu değiştirebilirsiniz.

Aşağıdaki tabloda, adları ve açıklamaları bu etkinliğe özgü olan özellikleri açıklanmaktadır.

| Özellik              | Açıklama                              | Gerekli |
| :-------------------- | :--------------------------------------- | :------- |
| name                  | İşlem hattındaki etkinliğin adı     | Evet      |
| description           | Etkinliğin ne yaptığını açıklayan metin.  | Hayır       |
| type                  | Özel bir etkinlik için etkinlik türdür **özel**. | Evet      |
| linkedServiceName     | Azure Batch için bağlı hizmeti. Bu bağlı hizmeti hakkında bilgi edinmek için [işlem bağlı Hizmetleri](compute-linked-services.md) makalesi.  | Evet      |
| command               | Yürütülecek özel uygulama komutu. Uygulama zaten Azure Batch havuzu düğüm üzerinde kullanılabilir haldeyse, folderPath ve resourceLinkedService atlanabilir. Örneğin, komut olarak belirtebilirsiniz `cmd /c dir`, yerel olarak desteklendiği Windows Batch havuzu düğümü tarafından. | Evet      |
| resourceLinkedService | Özel uygulama depolandığı depolama hesabı için Azure depolama bağlı hizmeti | Yok&#42;       |
| folderPath            | Özel uygulama ve tüm bağımlılıklarını klasörünün yolu<br/><br/>Hiyerarşik klasör yapısı altında alt klasörlerinde - diğer bir deyişle, depolanan bağımlılıkları varsa *folderPath* -klasör yapısı şu anda Azure Batch'e dosyaları kopyalarken düzleştirilir. Diğer bir deyişle, tüm dosyaları hiçbir alt klasör tek bir klasöre kopyalanır. Bu davranışa geçici bir çözüm için dosyalar sıkıştırılıyor, sıkıştırılmış dosya kopyalamayı ve ardından, istenen konumu özel kodla sıkıştırması açılırken göz önünde bulundurun. | Yok&#42;       |
| referenceObjects      | Mevcut bağlı hizmetleri ve veri kümeleri dizisi. Data Factory kaynaklarını özel kodunuz başvurabilmeniz başvurulan bağlı hizmetleri ve veri kümeleri JSON biçimindeki özel uygulamaya geçirilir | Hayır       |
| extendedProperties    | Özel kodunuz ek özellikler başvurabilir, böylece JSON biçimindeki özel uygulamaya geçirilen kullanıcı tanımlı Özellikler | Hayır       |
| retentionTimeInDays | Özel Etkinlik için gönderilen dosyaları için elde tutma süresi. Varsayılan değer 30 gündür. | Hayır |

&#42;Özellikleri `resourceLinkedService` ve `folderPath` gerekir ya da her ikisi de belirtilmesi veya her ikisi de etmeyebilirsiniz.

> [!NOTE]
> Bağlı hizmetler olarak özel etkinlik referenceObjects geçiriyorsanız, bir Azure Key Vault geçirmek için en iyi güvenlik yöntemi (herhangi bir güvenli dize içermiyor olduğundan) bağlı hizmet ve getirme doğrudan anahtarından gizli dizi adı kullanarak kimlik bilgilerini devre dışı olduğu Koddan kasası. Bir örnek bulabilirsiniz [burada](https://github.com/nabhishek/customactivity_sample/tree/linkedservice) başvuruları AKV bağlı hizmeti, etkin kimlik bilgilerini Key Vault'tan alır ve ardından depolama kodda erişir.

## <a name="custom-activity-permissions"></a>Özel Etkinlik izinleri

Azure Batch otomatik kullanıcı hesabı olarak özel etkinlik ayarlar *görev kapsama sahip yönetici olmayan erişim* (varsayılan otomatik kullanıcı belirtimi). Otomatik kullanıcı hesabına izin düzeyini değiştiremezsiniz. Daha fazla bilgi için bkz. [görevleri kullanıcı hesapları altında Batch'de çalıştırma | Otomatik kullanıcı hesaplarını](../batch/batch-user-accounts.md#auto-user-accounts).

## <a name="executing-commands"></a>Komutları çalıştırma

Özel bir etkinlik kullanılarak bir komutu doğrudan çalıştırabilirsiniz. Aşağıdaki örnek, "Yankı hello world" komutu hedef Azure Batch havuzu düğümlerinde çalıştırır ve çıktısını Stdout'a yazdırır.

```json
{
  "name": "MyCustomActivity",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "cmd /c echo hello world"
      }
    }]
  }
}
```

## <a name="passing-objects-and-properties"></a>Nesneleri ve özellikleri geçirme

Bu örnek, Data Factory nesnelerle ve özelliklerle kullanıcı tanımlı özel uygulamanızı geçirmek için referenceObjects ve extendedProperties nasıl kullanabileceğinizi gösterir.

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {
          "connectionString": {
            "type": "SecureString",
            "value": "aSampleSecureString"
          },
          "PropertyBagPropertyName1": "PropertyBagValue1",
          "propertyBagPropertyName2": "PropertyBagValue2",
          "dateTime1": "2015-04-12T12:13:14Z"
        }
      }
    }]
  }
}
```

Etkinlik çalıştırıldığında referenceObjects ve extendedProperties SampleApp.exe aynı yürütme klasörüne dağıtılır aşağıdaki dosyaları depolanır:

- `activity.json`

  ExtendedProperties ve özel etkinlik özelliklerini depolar.

- `linkedServices.json`

  Depoları bağlı hizmetler dizisi referenceObjects özelliğinde tanımlanır.

- `datasets.json`

  Depoları, veri kümeleri bir dizi referenceObjects özelliğinde tanımlanır.

Örnek kod SampleApp.exe JSON dosyalarından gerekli bilgileri nasıl erişeceği gösterilmektedir:

```csharp
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.accountName);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>Yürütme çıktılarının alma

Aşağıdaki PowerShell komutunu kullanarak bir işlem hattı çalıştırması başlatabilirsiniz:

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
```

İşlem hattı çalışırken, aşağıdaki komutları kullanarak yürütme çıktısını denetleyebilirsiniz:

```powershell
while ($True) {
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

    if(!$result) {
        Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
    }
    elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
        Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
    }
    else {
        Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
        $result
        break
    }
    ($result | Format-List | Out-String)
    Start-Sleep -Seconds 15
}

Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
$result.Output -join "`r`n"

Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
$result.Error -join "`r`n"
```

**Stdout** ve **stderr** özel uygulamanızı kaydedilir **adfjobs** Azure depolama bağlı Azure Batch bağlantılı oluştururken tanımladığınız hizmet kapsayıcısında Hizmet görevinin GUID. Etkinlik çalıştırma çıktısı, aşağıdaki kod parçacığında gösterildiği gibi ayrıntılı yol alabilirsiniz:

```
Pipeline ' MyCustomActivity' run finished. Result:

ResourceGroupName : resourcegroupname
DataFactoryName   : datafactoryname
ActivityName      : MyCustomActivity
PipelineRunId     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
PipelineName      : MyCustomActivity
Input             : {command}
Output            : {exitcode, outputs, effectiveIntegrationRuntime}
LinkedServiceName :
ActivityRunStart  : 10/5/2017 3:33:06 PM
ActivityRunEnd    : 10/5/2017 3:33:28 PM
DurationInMs      : 21203
Status            : Succeeded
Error             : {errorCode, message, failureType, target}

Activity Output section:
"exitcode": 0
"outputs": [
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
]
"effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
Activity Error section:
"errorCode": ""
"message": ""
"failureType": ""
"target": "MyCustomActivity"
```

Aşağı Akış etkinliklerde stdout.txt içeriği istiyorsanız, ifadede stdout.txt dosyasının yolunu alabilirsiniz "\@activity('MyCustomActivity').output.outputs [0]".

> [!IMPORTANT]
> - Activity.json linkedServices.json ve datasets.json Batch görevin çalışma zamanı klasöründe depolanır. Bu örnekte, activity.json linkedServices.json ve datasets.json depolanır `"https://adfv2storage.blob.core.windows.net/adfjobs/\<GUID>/runtime/"` yolu. Gerekirse, ayrı ayrı temizlenmesi gerekir.
> - Şirket içinde barındırılan tümleştirme çalışma zamanı kullanan bağlı Hizmetleri için müşteri kimlik bilgisi kalır emin olmak için şirket içinde barındırılan Integration Runtime şifrelenir, anahtarlar veya parolalar gibi hassas bilgilerin özel ağ ortamında tanımlı. Bu şekilde, özel uygulama kodu tarafından başvurulduğunda hassas bazı alanlar eksik olabilir. Bağlı hizmet başvurusunu gerekirse kullanmak yerine extendedProperties SecureString kullanın.

## <a name="pass-outputs-to-another-activity"></a>Başka bir etkinlik çıktıları geçişi

Azure Data Factory için özel bir etkinlik kodunuzda özel değerleri geri gönderebilir. Bunları yazarak bunu yapabilirsiniz `outputs.json` uygulamanızdan. Veri Fabrikası içeriğini kopyalar `outputs.json` ve etkinlik çıkış değeri olarak ekler `customOutput` özelliği. (2 MB boyut sınırını içindir.) İçeriği kullanmak istiyorsanız `outputs.json` aşağı akış etkinlikleri değeri ifade kullanarak alabilirsiniz `@activity('<MyCustomActivity>').output.customOutput`.

## <a name="retrieve-securestring-outputs"></a>SecureString çıkışlar alınamıyor

Türü belirlenmiş önemli özellik değerlerini *SecureString*bazı bu makaledeki örneklerde gösterildiği gibi izleme sekmesinde Data Factory kullanıcı arabiriminde out maskelenir.  Ancak gerçek işlem hattı yürütme içinde bir *SecureString* özelliği içinde JSON olarak serileştirilmiş `activity.json` dosyasında düz metin olarak. Örneğin:

```json
"extendedProperties": {
  "connectionString": {
    "type": "SecureString",
    "value": "aSampleSecureString"
  }
}
```

Bu seri hale getirme tamamen güvenli değildir ve güvenli olacak şekilde tasarlanmamıştır. Amaç dışlamaktır izleme sekmesinde değeri maskelemek için Data factory'ye ipucu.

Türü özelliklerine erişmek için *SecureString* özel bir etkinlikten okuma `activity.json` dosyasını aynı klasöre yerleştirilir. EXE, JSON seri durumdan ve ardından JSON özelliği erişim (extendedProperties = > [propertyName] = > değer).

## <a name="compare-v2-v1"></a> V2 özel etkinlik ve sürüm 1 (özel) karşılaştırma DotNet etkinliği

Azure Data Factory sürüm 1, (özel) DotNet etkinliği uygulayan bir sınıf ile bir .NET Class Library projesi oluşturarak uygulamanız `Execute` yöntemi `IDotNetActivity` arabirimi. Bağlı hizmetler, veri kümeleri ve genişletilmiş özellikler (özel) DotNet etkinliği JSON yükündeki yürütme yöntemin türü kesin olarak belirtilmiş nesneler olarak geçirilir. Sürüm 1 davranışı hakkında daha fazla ayrıntı için bkz: [sürüm 1 (özel) DotNet](v1/data-factory-use-custom-activities.md). Bu uygulama nedeniyle, sürüm 1 DotNet etkinliği kodu .NET Framework 4.5.2'yi hedefleyen var. Sürüm 1 DotNet etkinliği de Windows tabanlı Azure Batch havuzu düğümlerinde yürütülmesi gerekir.

Azure Data Factory V2 özel etkinliğinde .NET arabirimi uygulamanız gerekmez. Artık doğrudan komutları ve komut yürütülmeye kendi özel kodunuzu çalıştırabilirsiniz. Bu uygulama yapılandırmak için belirttiğiniz `Command` özelliği ile birlikte `folderPath` özelliği. Yürütülebilir dosya ve bağımlılıkları için özel etkinlik yükler `folderpath` ve sizin için komutu çalıştırır.

Bağlı hizmetler, veri kümeleri (referenceObjects içinde tanımlanmıştır) ve JSON dosyaları olarak özel etkinlik, yürütülebilir dosya tarafından erişilebilen bir Data Factory v2 JSON yükü içinde tanımlanan özellikler genişletilmiş. Yukarıdaki SampleApp.exe kod örneğinde gösterildiği gibi bir JSON serileştirici kullanarak gerekli özelliklere erişebilirsiniz.

Data Factory V2 özel etkinliğinde sunulan değişikliklerle birlikte, tercih ettiğiniz dilde özel kod mantığınızı yazmanıza ve Windows ve Linux işletim sistemi Azure Batch tarafından desteklenen yürütün.

Data Factory V2 özel etkinliği ve Data Factory sürüm 1 arasındaki farklar (özel) aşağıdaki tabloda açıklanmıştır DotNet etkinliği:

|Farkları      | Özel Etkinlik      | Sürüm 1 (özel) DotNet etkinliği      |
| ---- | ---- | ---- |
|Nasıl özel mantığı tanımlanır      |Bir yürütülebilir dosya sağlayarak      |Bir .NET DLL uygulayarak      |
|Yürütme Ortamı özel mantığı      |Windows veya Linux      |Windows (.NET Framework 4.5.2)      |
|Betikleri çalıştırma      |Doğrudan komut dosyaları (örneğin "cmd /c Yankı hello world" Windows VM'de) yürütme destekler      |.NET DLL uygulamasını gerektirir      |
|Veri kümesi gerekiyor      |İsteğe bağlı      |Etkinliği zincirleyebilir, yani ve bilgi geçirmek için gerekli      |
|Özel mantığı etkinlikten geçiş bilgileri      |ReferenceObjects (LinkedServices ve veri kümeleri) ile ExtendedProperties (Özel Özellikler)      |ExtendedProperties (Özel Özellikler), giriş ve çıkış veri kümeleri      |
|Özel mantığı bilgilerini alma      |Activity.JSON linkedServices.json ve yürütülebilir dosya aynı klasörde depolanan datasets.json ayrıştırır.      |.NET SDK'sı (.NET çerçevesi 4.5.2)      |
|Günlüğe kaydetme      |Doğrudan STDOUT Yazar      |Günlükçü .NET DLL içinde uygulama      |

Mevcut .NET kodu için bir sürüm 1 (özel) DotNet etkinliği yazılan varsa, özel etkinliğin geçerli sürümüyle çalışabilmesi için kodunuzu değiştirmeniz gerekir. Bu üst düzey yönergeleri izleyerek kodunuzu güncelleştirin:

  - Proje bir konsol uygulaması için bir .NET sınıf kitaplığı'ndan değiştirin.
  - Uygulamanız ile başlayın `Main` yöntemi. `Execute` Yöntemi `IDotNetActivity` arabirimidir artık gerekli.
  - Okuma ve bağlı hizmetler, veri kümeleri ve etkinliği, JSON seri hale getirici ve kesin tür belirtilmiş nesneler olarak değil ayrıştırılamıyor. Ana özel kod mantığınızı gerekli özelliklerin değerlerini geçirirsiniz. Örneğin önceki SampleApp.exe koda bakın.
  - Günlükçü nesne artık desteklenmiyor. Konsola çıkışı, yürütülebilir dosya yazdırılabilir ve stdout.txt için kaydedilir.
  - Microsoft.Azure.Management.DataFactories NuGet paketi artık gerekli değildir.
  - Kodunuzu derlemek, Azure Depolama'ya yürütülebilir ve bağımlılıklarını yüklemek ve yolu tanımlama `folderPath` özelliği.

İçin nasıl uçtan uca DLL ve işlem hattı örnek Data Factory sürüm 1 makalede açıklanan tam bir örnek [bir Azure Data Factory işlem hattında özel etkinlikler kullanma](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities) Data Factory özel bir etkinlik yazılabilir, bakın[ Veri Fabrikası özel etkinliği örneği](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFv2CustomActivitySample).

## <a name="auto-scaling-of-azure-batch"></a>Azure batch otomatik olarak ölçeklendirme

Bir Azure Batch havuzu de oluşturabilirsiniz **otomatik ölçeklendirme** özelliği. Örneğin, 0 adanmış VM'ler ve Bekleyen Görevler sayısına bağlı olarak bir otomatik ölçeklendirme formülü ile bir azure batch havuzu oluşturabilirsiniz.

Burada örnek formülü aşağıdaki davranışı elde eder: Havuz başlangıçta oluşturulduğunda, 1 sanal makine ile başlar. $PendingTasks ölçüm çalışan + (kuyruğa alınmış) etkin içindeki görevlerin sayısını tanımlar durumu. Formül, Son 180 saniye cinsinden ortalama sayısı Bekleyen Görevler bulur ve TargetDedicated uygun şekilde ayarlar. TargetDedicated hiçbir zaman 25 VM'lerin ötesine geçen gider sağlar. Bu nedenle, yeni görevler gönderilen, havuzu otomatik olarak büyür ve görevler tamamlanınca ücretsiz tek tek sanal makineleri olur ve bu sanal makineler için otomatik ölçeklendirme küçültür. startingNumberOfVMs ve maxNumberofVMs ihtiyaçlarınıza göre ayarlanabilir.

Otomatik ölçeklendirme formülü:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Bkz: [işlem düğümleri Azure Batch havuzunda otomatik olarak](../batch/batch-automatic-scaling.md) Ayrıntılar için.

Varsayılan havuz kullanıyorsa [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), Batch hizmeti, sanal Makinenin özel etkinlik çalıştırmadan önce hazırlamak için 15-30 dakika sürebilir. Havuz farklı autoScaleEvaluationInterval kullanıyorsanız, Batch hizmeti autoScaleEvaluationInterval + 10 dakika sürebilir.

## <a name="next-steps"></a>Sonraki adımlar
Anlatan farklı yollarla verileri dönüştürmek aşağıdaki makalelere bakın:

* [U-SQL etkinliği](transform-data-using-data-lake-analytics.md)
* [Hive etkinliği](transform-data-using-hadoop-hive.md)
* [Pig etkinliği](transform-data-using-hadoop-pig.md)
* [MapReduce etkinliği](transform-data-using-hadoop-map-reduce.md)
* [Hadoop akış etkinliğinde](transform-data-using-hadoop-streaming.md)
* [Spark etkinliği](transform-data-using-spark.md)
* [Machine Learning Batch yürütme etkinliği](transform-data-using-machine-learning.md)
* [Saklı yordam etkinliği](transform-data-using-stored-procedure.md)
