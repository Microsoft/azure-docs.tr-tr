---
title: Azure Data Factory zamanlama Tetikleyicileri oluşturma
description: Bir zamanlamaya göre işlem hattı çalıştıran Azure Data Factory tetikleyici oluşturmayı öğrenin.
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/30/2020
ms.custom: devx-track-python
ms.openlocfilehash: 4bf5e9e1e890b2f91377075c4c8b7c8ff6c50fa0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779860"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Zamanlamaya göre işlem hattı çalıştıran bir tetikleyici oluşturma

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede zamanlama tetikleyicisi ve bir zamanlama tetikleyicisi oluşturma, başlatma ve izleme adımları hakkında bilgi verilmektedir. Diğer tetikleyici türleri için bkz. işlem [hattı yürütme ve Tetikleyiciler](concepts-pipeline-execution-triggers.md).

Bir zamanlama tetikleyicisi oluştururken tetikleyici için bir zamanlama (başlangıç tarihi, yinelenme, bitiş tarihi vb.) belirtirsiniz ve bir işlem hattı ile ilişkilendirebilirsiniz. İşlem hatları ve tetikleyiciler çoka çok ilişkisine sahiptir. Birden çok tetikleyici tek bir işlem hattını başlatabilir. Tek bir tetikleyici birden fazla işlem hattını başlatabilir.

Aşağıdaki bölümlerde, farklı yollarla bir zamanlama tetikleyicisi oluşturma adımları sağlanmaktadır. 

## <a name="data-factory-ui"></a>Data Factory Kullanıcı Arabirimi (UI)

İşlem hattını düzenli aralıklarla (saatlik, günlük, vb.) çalışacak şekilde zamanlamak için bir **zamanlama tetikleyicisi** oluşturabilirsiniz. 

> [!NOTE]
> Bir işlem hattı ve bir zamanlama tetikleyicisi oluşturmaya yönelik, tetikleyiciyi işlem hattı ile ilişkilendiren ve işlem hattını yürüten ve izleyen bir adım adım yönergeler için bkz. [hızlı başlangıç: Data Factory Kullanıcı arabirimi kullanarak veri fabrikası oluşturma](quickstart-create-data-factory-portal.md).

1. Kalem simgesiyle gösterilen **Düzenle** sekmesine geçin. 

    ![Düzen sekmesine geçin](./media/how-to-create-schedule-trigger/switch-edit-tab.png)

1. Menüde **tetikleyici** ' yi seçin ve ardından **Yeni/Düzenle**' yi seçin. 

    ![Yeni tetikleyici menüsü](./media/how-to-create-schedule-trigger/new-trigger-menu.png)

1. **Tetikleyici Ekle** sayfasında **tetikleyici seç...** öğesini seçin ve **+ Yeni**' yi seçin. 

    ![Tetikleyici ekle - yeni tetikleyici](./media/how-to-create-schedule-trigger/add-trigger-new-button.png)

1. **Yeni tetikleyici** sayfasında, aşağıdaki adımları uygulayın: 

    1. **Tür** için **zamanlamanın** seçili olduğunu onaylayın.
    1. **Başlangıç tarihi** tetikleyicisinin başlangıç tarih/saati ' ni belirtin. Varsayılan olarak Eşgüdümlü Evrensel Saat (UTC) olarak geçerli tarih saat değerine ayarlanır.
    1. Tetikleyicinin oluşturulacağı saat dilimini belirtin. Saat dilimi ayarı, gelişmiş yinelenme seçeneklerinde **Başlangıç tarihi**, **bitiş tarihi** ve **zamanlama yürütme süreleri** için geçerlidir. Saat dilimi ayarını değiştirmek, başlangıç tarihini otomatik olarak değiştirmez. Başlangıç tarihinin belirtilen saat diliminde doğru olduğundan emin olun. Lütfen tetikleyicinin zamanlanan yürütme zamanının başlangıç tarihi olarak kabul edileceğini unutmayın (başlangıç tarihinin en az 1 dakika, yürütme zamanından daha küçük olduğundan emin olun, aksi takdirde bir sonraki tekrardaki işlem hattını tetikler). 

        > [!NOTE]
        > Gün ışığından yararlanma süresini gözlemleyecek saat dilimleri için, tetikleme süresi iki kez bir yıl değişikliği için otomatik olarak ayarlar. Günışığından yararlanma değişikliğini devre dışı bırakmak için lütfen gün ışığından yararlanma, örneğin UTC gibi bir saat dilimi seçin

    1. Tetikleyici için **yinelenme** belirtin. Açılan listeden (her dakika, saatlik, günlük, haftalık ve aylık) değerlerden birini seçin. Metin kutusuna çarpanı girin. Örneğin, tetikleyicinin her 15 dakikada bir kez çalışmasını istiyorsanız, **her dakikayı** seçer ve metin kutusuna **15** girersiniz. 
    1. Bir bitiş tarihi belirtmek için **bitiş tarihi belirt**' i seçin ve _bitişi_ belirtin ve ardından **Tamam**' ı seçin. Her işlem hattı çalıştırmasının bir maliyeti vardır. Test ediyorsanız, işlem hattının yalnızca birkaç kez tetiklendiğinden emin olmak isteyebilirsiniz. Öte yandan, yayımlama saatiyle bitiş saati arasında işlem hattının çalıştırılmasına yetecek kadar zaman olduğundan emin olun. Tetikleyici siz çözümü kullanıcı arabiriminde kaydettiğinizde değil ancak Data Factory'de yayımladığınızda devreye girer.

        ![Tetikleyici ayarları](./media/how-to-create-schedule-trigger/trigger-settings-01.png)

        ![Bitiş tarihi için tetikleyici ayarları](./media/how-to-create-schedule-trigger/trigger-settings-02.png)

1. **Yeni tetikleyici** penceresinde, **etkin** seçeneğinde **Evet** ' i seçin ve ardından **Tamam**' ı seçin. Bu onay kutusunu kullanarak tetikleyiciyi daha sonra devre dışı bırakabilirsiniz. 

    ![Tetikleyici ayarları - İleri düğmesi](./media/how-to-create-schedule-trigger/trigger-settings-next.png)

1. **Yeni tetikleyici** penceresinde, uyarı iletisini gözden geçirin ve **Tamam**' ı seçin.

    ![Tetikleyici ayarları - Son düğmesi](./media/how-to-create-schedule-trigger/new-trigger-finish.png)

1. Değişiklikleri Data Factory yayımlamak için **Tümünü Yayımla** ' yı seçin. Değişiklikleri Data Factory yayımlamazsanız, tetikleyici işlem hattı çalıştırmalarını tetiklemez. 

    ![Yayımla düğmesi](./media/how-to-create-schedule-trigger/publish-2.png)

1. Soldaki işlem **hattı çalıştırmaları** sekmesine geçin ve ardından Listeyi yenilemek için **Yenile** ' yi seçin. Zamanlanan tetikleyici tarafından tetiklenen işlem hattı çalıştırmalarını görürsünüz. **Tetikleyen** sütunundaki değerlere dikkat edin. **Şimdi Tetikle** seçeneğini kullanırsanız, el ile tetikleyici çalıştırmayı listede görürsünüz. 

    ![Tetiklenen çalıştırmaları izleme](./media/how-to-create-schedule-trigger/monitor-triggered-runs.png)

1. **Tetikleyici çalıştırma**  \  **zamanlaması** görünümüne geçin. 

    ![Tetikleme çalıştırmalarını izleme](./media/how-to-create-schedule-trigger/monitor-trigger-runs.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu bölümde, bir zamanlama tetikleyicisi oluşturmak, başlatmak ve izlemek için Azure PowerShell nasıl kullanılacağı gösterilmektedir. Bu örnek çalışmayı görmek için öncelikle [hızlı başlangıç: Azure PowerShell kullanarak veri fabrikası oluşturma](quickstart-create-data-factory-powershell.md)bölümüne gidin. Ardından, aşağıdaki kodu, her 15 dakikada bir çalışan bir zamanlama tetikleyicisi oluşturup Başlatan Main yöntemine ekleyin. Tetikleyici, hızlı başlangıç kapsamında oluşturduğunuz **Adfv2QuickStartPipeline** adlı bir işlem hattı ile ilişkilendirilir.

1. C:\ADFv2QuickStartPSH\ klasöründe aşağıdaki içeriğe sahip **MyTrigger.js** ADLı bir JSON dosyası oluşturun:

    > [!IMPORTANT]
    > JSON dosyasını kaydetmeden önce, **StartTime** öğesinin DEĞERINI geçerli UTC saatine ayarlayın. **BitişZamanı** öğesinin DEĞERINI geçerli UTC saatinden bir saat olarak ayarlayın.

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00Z",
                    "endTime": "2017-12-08T01:00:00Z",
                    "timeZone": "UTC"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```

    JSON kod parçacığında:
    - Tetikleyicinin **Type** öğesi "scheduletrigger" olarak ayarlanır.
    - **Sıklık** öğesi "Minute" olarak ayarlanır ve **Interval** öğesi 15 olarak ayarlanır. Bu nedenle tetikleyici, ardışık düzeni başlangıç ve bitiş zamanları arasında 15 dakikada bir çalıştırır.
    - **TimeZone** öğesi, tetikleyicinin oluşturulduğu saat dilimini belirtir. Bu ayar hem **StartTime** hem de **bitişsaati** etkiler.
    - **BitişZamanı** öğesi, **StartTime** öğesinin değerinden sonraki bir saattir. Bu nedenle tetikleyici, işlem hattını 15 dakika, 30 dakika ve başlangıç zamanından 45 dakika sonra çalıştırır. Başlangıç saatini geçerli UTC zamanına ve bitiş saatini başlangıç zamanından geçen bir saate güncelleştirmeyi unutmayın. 

        > [!IMPORTANT]
        > UTC saat dilimi için startTime ve bitişsaati ' yyyy-MM-ddTHH: mm: SS **Z**' biçimini izlemelidir, diğer Timezones için StartTime ve bitişsaati ' yyyy-mm-ddTHH: mm: ss ' izler. 
        > 
        > ISO 8601 standardı başına, zaman damgasına olan _Z_ son eki, tarih saat değerini UTC saat dilimine işaret edin ve saat dilimi alanını gereksiz şekilde işler UTC saat dilimi için _Z_ soneki eksik olsa da, _etkinleştirme_ tetiklenmesi sırasında hata oluşmasına neden olur.

    - Tetikleyici **Adfv2QuickStartPipeline** işlem hattı ile ilişkilendirilir. Birden çok işlem hattını bir tetikleyici ile ilişkilendirmek için daha fazla **pipelineReference** bölümü ekleyin.
    - Hızlı başlangıçtaki işlem hattı iki **parametre** değeri alır: **ınputpath** ve **OutputPath**. Ve bu parametrelerin değerlerini tetikleyiciden geçirirsiniz.

1. **Set-AzDataFactoryV2Trigger** cmdlet 'ini kullanarak bir tetikleyici oluşturun:

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```

1. Tetikleyici durumunun **Get-AzDataFactoryV2Trigger** cmdlet 'ı kullanılarak **durdurulduğunu** doğrulayın:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1. **Start-AzDataFactoryV2Trigger** cmdlet 'ini kullanarak tetikleyiciyi başlatın:

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1. Tetikleyici durumunun **Get-AzDataFactoryV2Trigger** cmdlet 'ı kullanılarak **başlatıldığını** onaylayın:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1.  **Get-AzDataFactoryV2TriggerRun** cmdlet 'ini kullanarak Azure PowerShell tetikleyici çalıştırmalarını alın. Tetikleyici çalıştırmaları hakkında bilgi almak için aşağıdaki komutu düzenli aralıklarla yürütün. **Triggerrunstartedadfter** ve **Triggerrunstartedbefore** değerlerini tetikleyici tanımınızdaki değerlerle eşleşecek şekilde güncelleştirin:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
    > [!NOTE]
    > Zamanlama Tetikleyicileri tetikleme süresi UTC zaman damgasında belirtilir. _Triggerrunstartedadfter_ ve _Triggerrunstartedbefore_ Ayrıca UTC zaman damgasını bekliyor

    Tetikleyici çalıştırmalarını ve Azure portal işlem hattı çalıştırmalarını izlemek için bkz. işlem [hattı çalıştırmalarını izleme](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="net-sdk"></a>.NET SDK

Bu bölümde, bir tetikleyiciyi oluşturmak, başlatmak ve izlemek için .NET SDK 'nın nasıl kullanılacağı gösterilmektedir. Bu örnek çalışmayı görmek için öncelikle [hızlı başlangıç: .NET SDK kullanarak veri fabrikası oluşturma](quickstart-create-data-factory-dot-net.md)bölümüne gidin. Ardından, aşağıdaki kodu, her 15 dakikada bir çalışan bir zamanlama tetikleyicisi oluşturup Başlatan Main yöntemine ekleyin. Tetikleyici, hızlı başlangıç kapsamında oluşturduğunuz **Adfv2QuickStartPipeline** adlı bir işlem hattı ile ilişkilendirilir.

Her 15 dakikada bir çalışan bir zamanlama tetikleyicisi oluşturmak ve başlatmak için aşağıdaki kodu Main yöntemine ekleyin:

```csharp
            // Create the trigger
            Console.WriteLine("Creating the trigger");

            // Set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // Specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // Create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // Associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // Set the start time to the current UTC time and the end time to one hour after the start time
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // Now, create the trigger by invoking the CreateOrUpdate method
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            // Start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);
```

Farklı bir saat diliminde (UTC dışında) Tetikleyiciler oluşturmak için aşağıdaki ayarlar gereklidir:
```csharp
<<ClientInstance>>.SerializationSettings.DateFormatHandling = Newtonsoft.Json.DateFormatHandling.IsoDateFormat;
<<ClientInstance>>.SerializationSettings.DateTimeZoneHandling = Newtonsoft.Json.DateTimeZoneHandling.Unspecified;
<<ClientInstance>>.SerializationSettings.DateParseHandling = DateParseHandling.None;
<<ClientInstance>>.DeserializationSettings.DateParseHandling = DateParseHandling.None;
<<ClientInstance>>.DeserializationSettings.DateFormatHandling = Newtonsoft.Json.DateFormatHandling.IsoDateFormat;
<<ClientInstance>>.DeserializationSettings.DateTimeZoneHandling = Newtonsoft.Json.DateTimeZoneHandling.Unspecified;
```

Bir tetikleyici çalıştırmasını izlemek için örnekteki son deyimin önüne aşağıdaki kodu ekleyin `Console.WriteLine` :

```csharp
            // Check that the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

Tetikleyici çalıştırmalarını ve Azure portal işlem hattı çalıştırmalarını izlemek için bkz. işlem [hattı çalıştırmalarını izleme](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="python-sdk"></a>Python SDK'sı

Bu bölümde, bir tetikleyiciyi oluşturmak, başlatmak ve izlemek için Python SDK 'nın nasıl kullanılacağı gösterilmektedir. Bu örnek çalışmayı görmek için öncelikle [hızlı başlangıç: Python SDK 'sını kullanarak veri fabrikası oluşturma](quickstart-create-data-factory-python.md)' ya gidin. Ardından, Python betiğinin "işlem hattı çalıştırmasını izleme" kod bloğunu sonra aşağıdaki kod bloğunu ekleyin. Bu kod, belirtilen başlangıç ve bitiş zamanları arasında her 15 dakikada bir çalışan bir zamanlama tetikleyicisi oluşturur. **Start_time** DEĞIŞKENINI geçerli UTC saatine ve **end_time** değişkenini, geçerli UTC saatinden bir saat sonra güncelleştirin.

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00Z', end_time='2017-12-12T05:00:00Z', time_zone='UTC')
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # Start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```

Tetikleyici çalıştırmalarını ve Azure portal işlem hattı çalıştırmalarını izlemek için bkz. işlem [hattı çalıştırmalarını izleme](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="azure-resource-manager-template"></a>Azure Resource Manager şablonu

Bir tetikleyici oluşturmak için Azure Resource Manager şablonu kullanabilirsiniz. Adım adım yönergeler için, bkz. [Kaynak Yöneticisi şablonu kullanarak Azure Veri Fabrikası oluşturma](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Tetikleyici başlangıç zamanını bir işlem hattına geçirme

Azure Data Factory sürüm 1, sistem değişkenlerini kullanarak bölümlenmiş verileri okumayı veya yazmayı destekler: LogFiles **Estart**, **daeend**, **windowstart** ve **windowend**. Geçerli Azure Data Factory sürümünde, işlem hattı parametresini kullanarak bu davranışı elde edebilirsiniz. Tetikleyici için başlangıç zamanı ve zamanlanan saat, işlem hattı parametresinin değeri olarak ayarlanır. Aşağıdaki örnekte, tetikleyicisinin zamanlanan saati, ardışık düzen **Scheduledruntime** parametresine bir değer olarak geçirilir:

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```

## <a name="json-schema"></a>JSON şeması

Aşağıdaki JSON tanımı, zamanlama ve yinelenme ile bir zamanlama tetikleyicisi oluşturmayı gösterir:

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // Optional, specifies how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // Optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurrences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ]
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  **parameters** özelliği, **pipelines** öğesinin zorunlu bir özelliğidir. İşlem hattınız herhangi bir parametre almasa bile, **parameters** özelliği için boş bir JSON tanımı eklemeniz gerekir.


### <a name="schema-overview"></a>Şemaya genel bakış

Aşağıdaki tabloda bir tetikleyicinin yinelenmesi ve zamanlanmasıyla ilgili ana şema öğelerinin genel bir özeti verilmiştir:

| JSON özelliği | Description |
|:--- |:--- |
| **startTime** | Bir Tarih-Saat değeri. Basit zamanlamalar için **startTime** özelliğinin değeri ilk oluşum için geçerli olur. Karmaşık zamanlamalar için tetikleyici belirtilen **startTime** değerinden önce başlamaz. <br> UTC saat dilimi için biçim, `'yyyy-MM-ddTHH:mm:ssZ'` diğer saat dilimi için biçimindedir `'yyyy-MM-ddTHH:mm:ss'` . |
| **endTime** | Tetikleyicinin bitiş tarihi ve saati. Tetikleyici belirtilen bitiş tarihi ve saatinden sonra yürütülmez. Bu özelliğin değeri geçmişte olamaz. Bu özellik isteğe bağlıdır.  <br> UTC saat dilimi için biçim, `'yyyy-MM-ddTHH:mm:ssZ'` diğer saat dilimi için biçimindedir `'yyyy-MM-ddTHH:mm:ss'` . |
| **TI** | Tetikleyicinin oluşturulduğu saat dilimi. Bu ayar **StartTime**, **bitişsaati** ve **zamanlamayı** etkiler. [Desteklenen saat dilimi listesini](#time-zone-option) görüntüleyin |
| **yinelemeyi** | Tetikleyici için yinelenme kurallarını belirten bir yinelenme nesnesi. recurrence nesnesi şu öğeleri destekler: **frequency**, **interval**, **endTime**, **count** ve **schedule**. Bir yinelenme nesnesi tanımlanırken **frequency** öğesi gereklidir. Yinelenme nesnesinin diğer öğeleri isteğe bağlıdır. |
| **frequency** | Tetikleyicinin yineleneceği sıklık birimi. “Minute”, “hour”, “day”, “week” ve “month” değerleri desteklenir. |
| **aralığında** | Tetikleyicinin çalışma sıklığını belirten **frequency** değerinin aralığını gösteren bir pozitif tamsayı. Örneğin **interval** değeri 3, **frequency** değeri de "week" ise tetikleyici 3 haftada bir yinelenir. |
| **çizelgesini** | Tetikleyicinin yinelenme zamanlaması. **frequency** değeri belirtilen bir tetikleyici, yinelenmesini bir yinelenme zamanlamasına göre değiştirir. **schedule** özelliği, yinelenme için dakika, saat, haftanın günü, ayın günü ve hafta numarası tabanlı değişiklikleri içerir.

> [!IMPORTANT]
> UTC saat dilimi için startTime ve bitişsaati ' yyyy-MM-ddTHH: mm: SS **Z**' biçimini izlemelidir, diğer Timezones için StartTime ve bitişsaati ' yyyy-mm-ddTHH: mm: ss ' izler. 
> 
> ISO 8601 standardı başına, zaman damgasına olan _Z_ son eki, tarih saat değerini UTC saat dilimine işaret edin ve saat dilimi alanını gereksiz şekilde işler UTC saat dilimi için _Z_ soneki eksik olsa da, _etkinleştirme_ tetiklenmesi sırasında hata oluşmasına neden olur.

### <a name="schema-defaults-limits-and-examples"></a>Şema varsayılanları, sınırlar ve örnekler

| JSON özelliği | Tür | Gerekli | Varsayılan değer | Geçerli değerler | Örnek |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | Dize | Yes | Yok | ISO-8601 Tarih-Saatleri | UTC saat dilimi için `"startTime" : "2013-01-09T09:30:00-08:00Z"` <br> diğer saat dilimi için `"2013-01-09T09:30:00-08:00"` |
| **TI** | Dize | Yes | Yok | [Saat dilimi değerleri](#time-zone-option)  | `"UTC"` |
| **yinelemeyi** | Nesne | Yes | Yok | Yinelenme nesnesi | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **aralığında** | Sayı | No | 1 | 1-1.000 | `"interval":10` |
| **endTime** | Dize | Yes | Yok | Gelecekteki bir zamanı temsil eden Tarih-Saat değeri. | UTC saat dilimi için `"endTime" : "2013-02-09T09:30:00-08:00Z"` <br> diğer saat dilimi için `"endTime" : "2013-02-09T09:30:00-08:00"`|
| **çizelgesini** | Nesne | No | Yok | Zamanlama nesnesi | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="time-zone-option"></a>Saat dilimi seçeneği

Zamanlama Tetikleyicileri için desteklenen bazı saat dilimleri aşağıda verilmiştir:

| Saat Dilimi | UTC kayması (yaz dışı kaydetme) | Saat dilimi değeri | Yaz 'ın kaydedilmesini gözlemleyin | Zaman damgası biçimi |
| :--- | :--- | :--- | :--- | :--- |
| Eşgüdümlü Evrensel Saat | 0 | `UTC` | No | `'yyyy-MM-ddTHH:mm:ssZ'`|
| Pasifik saati (PT) | -8 | `Pacific Standard Time` | Yes | `'yyyy-MM-ddTHH:mm:ss'` |
| Orta saat (CT) | -6 | `Central Standard Time` | Yes | `'yyyy-MM-ddTHH:mm:ss'` |
| Doğu saati (ET) | -5 | `Eastern Standard Time` | Yes | `'yyyy-MM-ddTHH:mm:ss'` |
| Greenwich saati (GMT) | 0 | `GMT Standard Time` | Yes | `'yyyy-MM-ddTHH:mm:ss'` |
| Orta Avrupa standart saati | +1 | `W. Europe Standard Time` | Yes | `'yyyy-MM-ddTHH:mm:ss'` |
| Hindistan Standart Saati (IST) | + 5:30 | `India Standard Time` | No | `'yyyy-MM-ddTHH:mm:ss'` |
| Çin standart saati | + 8 | `China Standard Time` | No | `'yyyy-MM-ddTHH:mm:ss'` |

Bu liste eksik. Saat dilimi seçeneklerinin tüm listesi için Data Factory Portal [tetikleyicisi oluşturma sayfasında](#data-factory-ui) araştırma yapın

### <a name="starttime-property"></a>startTime özelliği
Aşağıdaki tabloda **startTime** özelliğinin bir tetikleyici çalıştırmasını nasıl denetlediği gösterilmektedir:

| startTime değeri | Zamanlama olmadan yinelenme | Zamanlama ile yinelenme |
|:--- |:--- |:--- |
| Başlangıç zamanı geçmişte | Başlangıç zamanından sonraki ilk gelecek yürütme zamanını hesaplar ve bu zamanda çalıştırır.<br/><br/>Son yürütme zamanına göre yaptığı hesaplamalarla sonraki yürütmeleri çalıştırır.<br/><br/>Bu tablonun altındaki örneğe bakın. | Tetikleyici belirtilen başlangıç zamanından _önce_ başlamaz. İlk yinelenme, başlangıç zamanından hesaplanan zamanlamayı temel alır.<br/><br/>Sonraki yürütmeleri yinelenme zamanlamasına göre çalıştırır. |
| Başlangıç zamanı gelecekte veya güncel | Belirtilen başlangıç zamanında bir kez çalışır.<br/><br/>Son yürütme zamanına göre yaptığı hesaplamalarla sonraki yürütmeleri çalıştırır. | Tetikleyici belirtilen başlangıç zamanından _önce_ başlamaz. İlk yinelenme, başlangıç zamanından hesaplanan zamanlamayı temel alır.<br/><br/>Sonraki yürütmeleri yinelenme zamanlamasına göre çalıştırır. |

Başlangıç zamanı geçmişte olduğunda ve bir yinelenme olmasına rağmen zamanlama olmadığında neler olacağını gösteren bir örneğe bakalım. Geçerli zamanın `2017-04-08 13:00`, başlangıç zamanının `2017-04-07 14:00` ve yinelenmenin iki günde bir olduğunu varsayalım. ( **Yinelenme** değeri, **Sıklık** özelliği "Day" ve **Interval** özelliği 2 olarak ayarlanarak tanımlanır.) **StartTime** değerinin geçmişte olduğuna ve geçerli zamandan önce gerçekleşdiğine dikkat edin.

Bu koşullar altında ilk yürütme at ' dır `2017-04-09` `14:00` . Zamanlayıcı altyapısı çalıştırma yinelenmelerini başlangıç zamanından itibaren hesaplar. Geçmişteki örnekler dikkate alınmaz. Altyapı gelecekte gerçekleşen bir sonraki örneği kullanır. Bu senaryoda başlangıç saati ' dir, bu `2017-04-07` `2:00pm` nedenle sonraki örnek bu saatten iki gündür ve ' de olur `2017-04-09` `2:00pm` .

İlk yürütme zamanı **startTime** değeri `2017-04-05 14:00` veya `2017-04-01 14:00` olsa bile değişmez. İlk yürütme sonrasındaki yürütmeler zamanlama kullanılarak hesaplanır. Bu nedenle, sonraki yürütmeler `2017-04-11` konumunda, sonra, vb. ' de yer alır `2:00pm` `2017-04-13` `2:00pm` `2017-04-15` `2:00pm` .

Son olarak, bir tetikleyicinin zamanlamasında saatler veya dakikalar ayarlanmazsa varsayılan olarak ilk yürütmenin saat veya dakikaları kullanılır.

### <a name="schedule-property"></a>schedule özelliği

Diğer yandan, zamanlama kullanımı tetikleyici yürütme sayısını sınırlayabilir. Örneğin, aylık sıklığa sahip bir tetikleyici 31. günde çalışacak şekilde zamanlanırsa, tetikleyici yalnızca 31. günü olan aylarda çalışır.

Diğer yandan zamanlama, tetikleyici çalıştırma sayısını da genişletebilir. Örneğin, aylık sıklığa sahip olan ve ayın 1. ve 2. günlerinde çalışacak şekilde zamanlanan bir tetikleyici, ayda bir kez çalışmak yerine ayın 1. ve 2. günlerinde çalışır.

Birden fazla **schedule** öğesi belirtilmişse değerlendirme sırası en büyük zamanlama ayarından en küçüğe doğru olur. Değerlendirme hafta numarasıyla başlar ve arkasından ayın günü, haftanın günü, saat ve son olarak dakika gelir.

Aşağıdaki tabloda **schedule** öğeleri ayrıntılı bir şekilde açıklanmıştır:

| JSON öğesi | Description | Geçerli değerler |
|:--- |:--- |:--- |
| **dakika** | Tetikleyicinin çalıştığı dakika değeri. | <ul><li>Tamsayı</li><li>Tamsayı dizisi</li></ul>
| **saatlerinin** | Tetikleyicinin çalıştığı saat değeri. | <ul><li>Tamsayı</li><li>Tamsayı dizisi</li></ul> |
| **weekDays** | Tetikleyicinin çalıştığı hafta günleri. Bu değer yalnızca haftalık bir sıklık ile belirtilebilir. | <ul><li>Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday</li><li>Gün değerleri dizisi (en fazla dizi boyutu 7’dir)</li><li>Gün değerleri büyük/küçük harfe duyarlı değildir</li></ul> |
| **monthlyOccurrences** | Tetikleyicinin çalıştığı ay günleri. Bu değer yalnızca aylık bir sıklık ile belirtilebilir. | <ul><li>**Monthlyoccurrence** nesneleri dizisi: `{ "day": day,  "occurrence": occurrence }` .</li><li>**day** özniteliği, tetikleyicinin çalıştığı gündür. Örneğin, **day** değeri `{Sunday}` olan bir **monthlyOccurrences** özelliği, ayın her Pazar günü anlamına gelir. **day** özniteliği gereklidir.</li><li>**occurrence** özniteliği, ay içinde belirtilen **day** değerinin gerçekleşmesidir. Örneğin, **day** ve **occurrence** değerleri `{Sunday, -1}` olan bir **monthlyOccurrences** özelliği, ayın son Pazar günü anlamına gelir. **occurrence** özniteliği isteğe bağlıdır.</li></ul> |
| **monthDays** | Tetikleyicinin çalıştığı ay günü. Bu değer yalnızca aylık bir sıklık ile belirtilebilir. | <ul><li><= -1 ve >= -31 şartlarını karşılayan herhangi bir değer</li><li>>= 1 ve <= 31 şartlarını karşılayan herhangi bir değer</li><li>Değer dizisi</li></ul> |

## <a name="examples-of-trigger-recurrence-schedules"></a>Tetikleyici yineleme zamanlaması örnekleri

Bu bölümde yinelenme zamanlaması örnekleri sağlanmış ve **schedule** nesnesi ile bunun öğelerine odaklanılmıştır.

Örneklerde **interval** değerinin 1 olduğu ve zamanlama tanımına göre **frequency** değerinin doğru olduğu varsayılmıştır. Örneğin, **Sıklık** değeri "Day" ve ayrıca **Schedule** nesnesinde bir "monthdays" değişikliği olamaz. Bu gibi kısıtlamalar önceki bölümde yer alan tabloda belirtilmiştir.

| Örnek | Description |
|:--- |:--- |
| `{"hours":[5]}` | Her gün 05.00'te çalıştır. |
| `{"minutes":[15], "hours":[5]}` | Her gün 05.15'te çalıştır. |
| `{"minutes":[15], "hours":[5,17]}` | Her gün 05.15 ve 17.15’te çalıştır. |
| `{"minutes":[15,45], "hours":[5,17]}` | Her gün 05.15, 05.45, 17.15 ve 17.45’te çalıştır. |
| `{"minutes":[0,15,30,45]}` | 15 dakikada bir çalıştır. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Saatte bir çalıştır. Bu tetikleyici saatte bir çalışır. **startTime** değeri belirtilirse dakikalar bu değer tarafından denetlenir. Bir değer belirtilmezse dakikalar, oluşturma zamanı tarafından denetlenir. Örneğin başlangıç zamanı veya oluşturma zamanı (hangisi geçerliyse) 12:25 ise tetikleyici 00.25, 01.25, 02.25, ... ve 23.25 saatlerinde çalışır.<br/><br/>Bu zamanlama, **frequency** değeri “hour”, **interval** değeri 1 olan ve **schedule** olmayan bir tetikleyiciye sahip olmakla eşdeğerdir.  Bu zamanlama farklı **frequency** ve **interval** değerleriyle kullanılarak başka tetikleyiciler oluşturulabilir. Örneğin, **frequency** değeri “day” olduğunda her gün çalışan zamanlama, **frequency** değeri “month” olduğunda yalnızca ayda bir kere çalışır. |
| `{"minutes":[0]}` | Her saat başı çalıştır. Bu tetikleyici 12.00, 01.00, 02.00 gibi bir saatte başlayarak saat başı çalışır.<br/><br/>Bu zamanlama, **frequency** değeri “hour” olup **startTime** değeri sıfır dakika olan ya da **schedule** değeri olmayıp **frequency** değeri “day” olan bir tetikleyiciye eşdeğerdir. **Sıklık** değeri "Week" veya "month" ise, zamanlama yalnızca bir gün veya ayda bir gün boyunca çalışır. |
| `{"minutes":[15]}` | Her saat başını 15 dakika geçe çalıştır. Bu tetikleyici, 00.15, 01.15, 02.15, vb. bir saatte başlayıp 23.15’te bitecek şekilde her saat başını 15 dakika geçe çalışır. |
| `{"hours":[17], "weekDays":["saturday"]}` | Her hafta Cumartesi günleri saat 17.00'de çalıştır. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Her hafta Pazartesi, Çarşamba ve Cuma günleri saat 17.00'de çalıştır. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Her hafta Pazartesi, Çarşamba ve Cuma günleri saat 17.15 ve 17.45'te çalıştır. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Haftanın her günü 15 dakikada bir çalıştır. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Haftanın her günü 09.00 ile 16.45 arasında 15 dakikada bir çalıştır. |
| `{"weekDays":["tuesday", "thursday"]}` | Salı ve Perşembe günleri belirtilen başlangıç saatinde çalıştır. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Her ayın 28. gününde saat 06.00'da çalıştır (**frequency** değerinin “month” olduğu kabul edilir). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Ayın son günü saat 06.00'da çalıştır. Bir tetikleyiciyi ayın son gününde çalıştırmak için 28, 29, 30 veya 31 yerine -1 değerini kullanın. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Her ayın ilk ve son günü saat 06.00'da çalıştır. |
| `{monthDays":[1,14]}` | Her ayın birinci ve 14. gününde belirtilen başlangıç zamanında çalıştır. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Her ayın ilk Cuma günü saat 05.00'te çalıştır. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Her ayın ilk Cuma gününde belirtilen başlangıç zamanında çalıştır. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Her ay, ayın sondan üçüncü Cuma gününde belirtilen zamanda çalıştır. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Her ayın ilk ve son Cuma günü saat 05.15'te çalıştır. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Her ayın ilk ve son Cuma günü belirtilen başlangıç zamanında çalıştır. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Her ayın beşinci Cuma günü belirtilen başlangıç zamanında çalıştır. Bir ayda beşinci Cuma günü yoksa, işlem hattı yalnızca beşinci Cuma günlerinde çalışacak şekilde zamanlandığından çalışmaz. Tetikleyiciyi ayın son Cuma gününde çalıştırmak istiyorsanız **occurrence** değeri için 5 yerine -1 yazın. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Ayın son Cuma günü 15 dakikada bir çalıştır. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Her ayın üçüncü Çarşamba günü 05.15, 05.45, 17.15 ve 17.45’te çalıştır. |

## <a name="next-steps"></a>Sonraki adımlar

- Tetikleyiciler hakkında ayrıntılı bilgi için bkz. işlem [hattı yürütme ve Tetikleyiciler](concepts-pipeline-execution-triggers.md#trigger-execution).
- İşlem hattında tetikleyici meta verilerine nasıl başvurulacağını öğrenin, bkz. işlem [hattı çalıştırmalarıyla başvuru tetikleyici meta verileri](how-to-use-trigger-parameterization.md)
