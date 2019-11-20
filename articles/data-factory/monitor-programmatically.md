---
title: Programlı olarak bir Azure Data Factory izleme
description: Farklı yazılım geliştirme setleri (SDK 'lar) kullanarak bir veri fabrikasında bir işlem hattını izlemeyi öğrenin.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
author: djpmsft
ms.author: daperlov
manager: craigg
ms.openlocfilehash: f9e85a2660ffe1088a9897e9936b6fd0360f87d5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684606"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Programlı olarak bir Azure Data Factory izleme
Bu makalede, farklı yazılım geliştirme setleri (SDK 'lar) kullanılarak bir veri fabrikasında bir işlem hattının nasıl izleneceği açıklanır. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>Veri aralığı

Data Factory yalnızca 45 gün boyunca işlem hattı çalıştırma verilerini depolar. Data Factory işlem hattı çalıştırmaları hakkında verileri programlama yoluyla sorgulayıp (örneğin, PowerShell komutuyla `Get-AzDataFactoryV2PipelineRun`, isteğe bağlı `LastUpdatedAfter` ve `LastUpdatedBefore` parametreleri için en fazla tarih yoktur. Ancak, bir önceki yıla ait veriler için sorgulama yaparsanız sorgu bir hata döndürmez, ancak yalnızca son 45 günden alınan işlem hattı çalıştırma verilerini döndürür.

İşlem hattı çalıştırma verilerini 45 günden fazla süreyle kalıcı hale getirmek istiyorsanız, [Azure izleyici](monitor-using-azure-monitor.md)ile kendi tanılama günlük kaydını ayarlayın.

## <a name="net"></a>.NET
.NET SDK kullanarak bir işlem hattı oluşturma ve izlemeye yönelik kapsamlı bir anlatım için bkz. [.NET kullanarak veri fabrikası ve işlem hattı oluşturma](quickstart-create-data-factory-dot-net.md).

1. Veri kopyalamayı bitirene kadar işlem hattı çalıştırmasının durumunu sürekli olarak denetlemek için aşağıdaki kodu ekleyin.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Kopyalama etkinliği çalıştırma ayrıntılarını (örneğin, okunan/yazılan verilerin boyutu) alan aşağıdaki kodu ekleyin.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

.NET SDK ile ilgili tüm belgeler için, [Data Factory .NET SDK başvurusu](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet)' na bakın.

## <a name="python"></a>Python
Python SDK kullanarak bir işlem hattı oluşturma ve izlemeye yönelik kapsamlı bir anlatım için bkz. [Python kullanarak veri fabrikası ve işlem hattı oluşturma](quickstart-create-data-factory-python.md).

İşlem hattı çalıştırmasını izlemek için aşağıdaki kodu ekleyin:

```python
# Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(
    rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(
    rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

Python SDK ile ilgili tüm belgeler için, [Data Factory Python SDK başvurusu](/python/api/overview/azure/datafactory?view=azure-python)' na bakın.

## <a name="rest-api"></a>REST API
REST API kullanarak bir işlem hattı oluşturma ve izlemeye yönelik kapsamlı bir anlatım için, bkz. [REST API kullanarak veri fabrikası oluşturma ve işlem hattı oluşturma](quickstart-create-data-factory-rest-api.md).
 
1. İşlem hattı çalıştırma durumunu, verileri kopyalama işlemi tamamlanıncaya kadar sürekli olarak denetlemek için aşağıdaki betiği çalıştırın.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ($response.Status -eq "InProgress") {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```
2. Kopyalama etkinliği çalıştırma ayrıntılarını (örneğin, okunan/yazılan verilerin boyutu) almak için aşağıdaki betiği çalıştırın.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

REST API hakkındaki tüm belgeler için bkz. [Data Factory REST API başvurusu](/rest/api/datafactory/).

## <a name="powershell"></a>PowerShell
PowerShell kullanarak bir işlem hattı oluşturma ve izlemeye yönelik kapsamlı bir anlatım için bkz. [PowerShell kullanarak veri fabrikası ve işlem hattı oluşturma](quickstart-create-data-factory-powershell.md).

1. İşlem hattı çalıştırma durumunu, verileri kopyalama işlemi tamamlanıncaya kadar sürekli olarak denetlemek için aşağıdaki betiği çalıştırın.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 30
    }
    ```
2. Kopyalama etkinliği çalıştırma ayrıntılarını (örneğin, okunan/yazılan verilerin boyutu) almak için aşağıdaki betiği çalıştırın.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

PowerShell cmdlet 'leri hakkında tüm belgeler için bkz. [PowerShell cmdlet başvurusu Data Factory](/powershell/module/az.datafactory).

## <a name="next-steps"></a>Sonraki adımlar
Data Factory işlem hatlarını izlemek üzere Azure Izleyici 'yi kullanmayı öğrenmek için bkz. Azure izleyici 'yi [kullanarak işlem hatlarını izleme](monitor-using-azure-monitor.md) . 

