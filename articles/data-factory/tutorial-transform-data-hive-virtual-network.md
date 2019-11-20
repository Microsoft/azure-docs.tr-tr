---
title: "Azure sanal ağ 'da Hive kullanarak verileri dönüştürme "
description: Bu öğretici, Azure Data Factory'de Hive etkinliğini kullanarak verileri dönüştürmeye ilişkin adım adım yönergeler sağlar.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 263eb243ea45963757c50aa031cc17e318d70d98
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683315"
---
# <a name="transform-data-in-azure-virtual-network-using-hive-activity-in-azure-data-factory"></a>Azure Data Factory’de Hive etkinliğini kullanarak Azure Sanal Ağ’daki verileri dönüştürme
Bu öğreticide, Azure PowerShell kullanarak Azure Sanal Ağ’daki bir HDInsight kümesinde Hive Etkinliği ile verileri dönüştüren bir Data Factory işlem hattı oluşturacaksınız. Bu öğreticide aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Veri fabrikası oluşturma. 
> * Şirket içinde barındırılan tümleştirme çalışma zamanı yazma ve kurma
> * Bağlantılı hizmetler yazma ve dağıtma.
> * Hive etkinliği içeren bir işlem hattı yazma ve dağıtma.
> * Bir işlem hattı çalıştırması başlatma.
> * İşlem hattı çalıştırmasını izleme 
> * çıktıyı doğrulama. 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure Depolama hesabı**. Bir hive betiği oluşturun ve Azure depolama alanına yükleyin. Hive betiğinin çıktısı bu depolama hesabında depolanır. Bu örnekte, HDInsight kümesi bu Azure Depolama hesabını birincil depolama alanı olarak kullanır. 
- **Azure Sanal Ağ.** Bir Azure sanal ağınız yoksa [bu yönergeleri](../virtual-network/quick-create-portal.md) izleyerek bir tane oluşturun. Bu örnekte HDInsight bir Azure Sanal Ağ içindedir. Azure Sanal Ağ’ın örnek yapılandırması aşağıda verilmiştir. 

    ![Sanal ağ oluşturma](media/tutorial-transform-data-using-hive-in-vnet/create-virtual-network.png)
- **HDInsight kümesi.** Bir HDInsight kümesi oluşturun ve önceki adımda oluşturduğunuz sanal ağa katmak için şu makaleyi izleyin: [Bir Azure Sanal Ağ kullanarak Azure HDInsight’ı genişletme](../hdinsight/hdinsight-extend-hadoop-virtual-network.md). Bir sanal ağda HDInsight’ın örnek yapılandırması aşağıda verilmiştir. 

    ![Sanal ağda HDInsight](media/tutorial-transform-data-using-hive-in-vnet/hdinsight-in-vnet-configuration.png)
- **Azure PowerShell**. [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/install-Az-ps) bölümündeki yönergeleri izleyin.

### <a name="upload-hive-script-to-your-blob-storage-account"></a>Hive betiğini Blob Depolama hesabınıza yükleme

1. Aşağıdaki içeriğe sahip **hivescript.hql** adlı bir Hive SQL dosyası oluşturun:

   ```sql
   DROP TABLE IF EXISTS HiveSampleOut; 
   CREATE EXTERNAL TABLE HiveSampleOut (clientid string, market string, devicemodel string, state string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
   STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

   INSERT OVERWRITE TABLE HiveSampleOut
   Select 
       clientid,
       market,
       devicemodel,
       state
   FROM hivesampletable
   ```
2. Azure Blob depolama alanınızda henüz yoksa **adftutorial** adlı bir kapsayıcı oluşturun.
3. **hivescripts** adlı bir klasör oluşturun.
4. **hivescript.hql** dosyasını **hivescripts** alt klasörüne yükleyin.

  

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma


1. Kaynak grubu adını ayarlayın. Bu öğreticinin bir parçası olarak bir kaynak grubu oluşturun. Ancak, isterseniz mevcut bir kaynak grubunu kullanabilirsiniz. 

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup" 
    ```
2. Veri fabrikası adını belirtin. Genel olarak benzersiz olması gerekir.

    ```powershell
    $dataFactoryName = "MyDataFactory09142017"
    ```
3. İş hattı için bir ad belirtin. 

    ```powershell
    $pipelineName = "MyHivePipeline" # 
    ```
4. Şirket içinde barındırılan tümleştirme çalışma zamanı adını belirtin. Data Factory’nin, bir sanal ağ içindeki kaynaklara (Azure SQL Veritabanı gibi) erişmesi gerektiğinde, şirket içinde barındırılan tümleştirme çalışma zamanına ihtiyaç duyacaksınız. 
    ```powershell
    $selfHostedIntegrationRuntimeName = "MySelfHostedIR09142017" 
    ```
2. **PowerShell**’i başlatın. Bu hızlı başlangıcın sonuna kadar Azure PowerShell’i açık tutun. Kapatıp yeniden açarsanız komutları yeniden çalıştırmanız gerekir. Data Factory'nin kullanılabileceği Azure bölgelerinin bir listesi için bir sonraki sayfada ilgilendiğiniz bölgeleri seçin ve **Analytics**'i genişleterek **Data Factory**: [Products available by region](https://azure.microsoft.com/global-infrastructure/services/) (Bölgeye göre kullanılabilir durumdaki ürünler) bölümünü bulun. Veri fabrikası tarafından kullanılan verileri depoları (Azure Depolama, Azure SQL Veritabanı vb.) ve işlemler (HDInsight vb.) başka bölgelerde olabilir.

    Aşağıdaki komutu çalıştırın ve Azure portalda oturum açmak için kullandığınız kullanıcı adı ve parolayı girin:
        
    ```powershell
    Connect-AzAccount
    ```        
    Bu hesapla ilgili tüm abonelikleri görmek için aşağıdaki komutu çalıştırın:

    ```powershell
    Get-AzSubscription
    ```
    Çalışmak isteğiniz aboneliği seçmek için aşağıdaki komutu çalıştırın. **SubscriptionId**’yi Azure aboneliğinizin kimliği ile değiştirin:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"    
    ```  
3. Aboneliğinizde henüz yoksa ADFTutorialResourceGroup adlı kaynak grubunu oluşturun. 

    ```powershell
    New-AzResourceGroup -Name $resourceGroupName -Location "East Us" 
    ```
4. Veri fabrikasını oluşturun. 

    ```powershell
     $df = Set-AzDataFactoryV2 -Location EastUS -Name $dataFactoryName -ResourceGroupName $resourceGroupName
    ```

    Çıktıyı görmek için aşağıdaki komutu yürütün: 

    ```powershell
    $df
    ```

## <a name="create-self-hosted-ir"></a>Şirket içinde barındırılan IR oluşturma
Bu bölümde, şirket içinde barındırılan bir tümleştirme çalışma zamanı oluşturacak ve HDInsight kümenizin bulunduğu Azure Sanal Ağdaki bir Azure VM ile ilişkilendireceksiniz.

1. Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturun. Aynı ada sahip başka bir tümleştirme çalışma zamanı varsa benzersiz bir ad kullanın.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted
   ```
    Bu komut, şirket içinde barındırılan tümleştirme çalışma zamanının mantıksal bir kaydını oluşturur. 
2. Şirket içinde barındırılan tümleştirme çalışma zamanını kaydetmek üzere kimlik doğrulama anahtarlarını almak için PowerShell’i kullanın. Şirket içinde barındırılan tümleştirme çalışma zamanını kaydetmek için anahtarlardan birini kopyalayın.

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName | ConvertTo-Json
   ```

   Örnek çıktı aşağıdaki gibidir: 

   ```powershell
   {
       "AuthKey1":  "IR@0000000000000000000000000000000000000=",
       "AuthKey2":  "IR@0000000000000000000000000000000000000="
   }
   ```
    **AuthKey1** değerini tırnak işareti olmadan not edin. 
3. Bir Azure VM oluşturun ve HDInsight kümenizi içeren aynı sanal ağa ekleyin. Ayrıntılar için bkz. [Sanal makine oluşturma](../virtual-network/quick-create-portal.md#create-virtual-machines). Sanal makineleri bir Azure Sanal Ağa ekleyin. 
4. Azure VM’ye [şirket içinde barındırılan tümleştirme çalışma zamanını](https://www.microsoft.com/download/details.aspx?id=39717) indirin. Şirket içinde barındırılan tümleştirme çalışma zamanını el ile kaydetmek için önceki adımda elde edilen Kimlik Doğrulama Anahtarını kullanın. 

   ![Tümleştirme çalışma zamanını kaydetme](media/tutorial-transform-data-using-hive-in-vnet/register-integration-runtime.png)

   Şirket içinde barındırılan tümleştirme çalışma zamanı başarıyla kaydedildiğinde aşağıdaki iletiyi görürsünüz: ![Başarıyla kaydedildi](media/tutorial-transform-data-using-hive-in-vnet/registered-successfully.png)

   Düğüm bulut hizmetine bağlandığında şu sayfayı görürsünüz: ![Düğüm bağlı](media/tutorial-transform-data-using-hive-in-vnet/node-is-connected.png)

## <a name="author-linked-services"></a>Bağlı hizmetler oluşturma

Bu bölümde iki Bağlı Hizmet oluşturup dağıtacaksınız:
- Bir Azure Depolama hesabını veri fabrikasına bağlayan Azure Depolama Bağlı Hizmeti. Bu depolama, HDInsight kümeniz tarafından kullanılan birincil depolamadır. Bu örnekte, bu Azure Depolama hesabını Hive betiğini ve komut dosyası çıktısını tutmak için de kullanırız.
- Bir HDInsight Bağlı Hizmeti. Azure Data Factory, Hive betiğini yürütmek üzere bu HDInsight kümesine gönderir.

### <a name="azure-storage-linked-service"></a>Azure Storage bağlı hizmeti

Tercih ettiğiniz düzenleyiciyi kullanarak bir JSON dosyası oluşturun, Azure Depolama bağlı hizmetinin aşağıdaki JSON tanımını kopyalayın ve ardından dosyayı **MyStorageLinkedService.json** olarak kaydedin.

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
          "type": "SecureString"
        }
      },
      "connectVia": {
        "referenceName": "MySelfhostedIR",
        "type": "IntegrationRuntimeReference"
      }  
    }
}
```

**accountname&lt;&gt; ve &lt;accountkey&gt;** sözcüklerini Azure Depolama hesabınızın adı ve anahtarıyla değiştirin.

### <a name="hdinsight-linked-service"></a>HDInsight bağlı hizmeti

Tercih ettiğiniz düzenleyiciyi kullanarak bir JSON dosyası oluşturun, Azure HDInsight bağlı hizmetinin aşağıdaki JSON tanımını kopyalayın ve dosyayı **MyHDInsightLinkedService.json** olarak kaydedin.

```
{
  "name": "MyHDInsightLinkedService",
  "properties": {     
      "type": "HDInsight",
      "typeProperties": {
          "clusterUri": "https://<clustername>.azurehdinsight.net",
          "userName": "<username>",
          "password": {
            "value": "<password>",
            "type": "SecureString"
          },
          "linkedServiceName": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
      },
      "connectVia": {
        "referenceName": "MySelfhostedIR",
        "type": "IntegrationRuntimeReference"
      }
  }
}
```

Bağlı hizmet tanımında aşağıdaki özelliklerin değerlerini güncelleştirin:

- **userName**. Kümeyi oluştururken belirttiğiniz küme oturum açma kullanıcı adı. 
- **password**. Kullanıcının parolası.
- **clusterUri**. HDInsight kümenizin URL 'sini şu biçimde belirtin: `https://<clustername>.azurehdinsight.net`.  Bu makalede, kümeye internet üzerinden erişebildiğiniz varsayılır. Örneğin, `https://clustername.azurehdinsight.net` konumundaki kümeye bağlanabilirsiniz. Bu adres, İnternet'ten erişimi kısıtlamak için ağ güvenlik grupları (NSG) veya kullanıcı tanımlı yollar (UDR) kullandıysanız kullanılabilir olmayan ortak ağ geçidi kullanır. Data Factory’nin işleri Azure Sanal Ağdaki HDInsight kümelerine gönderebilmesi için Azure Sanal Ağınızı URL’nin HDInsight tarafından kullanılan ağ geçidine ait özel IP adresine çözümlenebileceği şekilde yapılandırmanız gerekir.

  1. Azure portalından, HDInsight’ın içinde bulunduğu Sanal Ağı açın. Adı `nic-gateway-0` ile başlayan ağ arabirimini açın. Özel IP adresini not edin. Örneğin, 10.6.0.15. 
  2. Azure sanal ağınızda DNS sunucusu varsa, HDInsight kümesi `https://<clustername>.azurehdinsight.net` URL’sinin `10.6.0.15` hedefine çözümlenebilmesi için DNS kaydını güncelleştirin. Bu, önerilen yaklaşımdır. Azure Sanal Ağınızda bir DNS sunucusu yoksa, şunun gibi bir giriş ekleyerek şirket içinde barındırılan tümleştirme çalışma zamanı düğümleri olarak kaydedilmiş tüm VM’lerin ana bilgisayar dosyalarını (C:\Windows\System32\drivers\etc) düzenleyerek bu sorunu geçici olarak çözebilirsiniz: 
  
        `10.6.0.15 myHDIClusterName.azurehdinsight.net`

## <a name="create-linked-services"></a>Bağlı hizmetler oluşturma
PowerShell’de, JSON dosyalarını oluşturduğunuz klasöre geçin ve bağlı hizmetleri dağıtmak için şu komutu çalıştırın: 

1. PowerShell'de, JSON dosyalarını oluşturduğunuz klasöre geçin.
2. Azure Depolama bağlı hizmeti oluşturmak için şu komutu çalıştırın. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyStorageLinkedService" -File "MyStorageLinkedService.json"
    ```
3. Azure HDInsight bağlı hizmeti oluşturmak için şu komutu çalıştırın. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyHDInsightLinkedService" -File "MyHDInsightLinkedService.json"
    ```

## <a name="author-a-pipeline"></a>İşlem hattı oluşturma
Bu adımda, Hive etkinliği ile bir işlem hattı oluşturacaksınız. Etkinlik, bir örnek tablodan veri döndürmek ve tanımladığınız bir yola kaydetmek üzere Hive betiğini yürütür. Tercih ettiğiniz düzenleyicide bir JSON dosyası oluşturun, şu işlem hattı JSON tanımını kopyalayın ve **MyHivePipeline.json** olarak kaydedin.


```json
{
  "name": "MyHivePipeline",
  "properties": {
    "activities": [
      {
        "name": "MyHiveActivity",
        "type": "HDInsightHive",
        "linkedServiceName": {
            "referenceName": "MyHDILinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "scriptPath": "adftutorial\\hivescripts\\hivescript.hql",
          "getDebugInfo": "Failure",
          "defines": {           
            "Output": "wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/"
          },
          "scriptLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }
    ]
  }
}

```

Aşağıdaki noktalara dikkat edin:

- **scriptPath**, MyStorageLinkedService için kullandığınız Azure Depolama Hesabında Hive betiğinin yoluna işaret eder. Bu yol büyük/küçük harfe duyarlıdır.
- **Çıktı**, Hive betiğinde kullanılan bir değişkendir. Azure Depolama hesabınızda var olan bir klasörü işaret etmek için `wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` biçimini kullanın. Bu yol büyük/küçük harfe duyarlıdır. 

JSON dosyalarını oluşturduğunuz klasöre geçin ve işlem hattını dağıtmak için aşağıdaki komutu çalıştırın: 


```powershell
Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineName -File "MyHivePipeline.json"
```

## <a name="start-the-pipeline"></a>İşlem hattı başlatma 

1. Bir işlem hattı çalıştırması başlatma. Ayrıca, gelecekte izlemek üzere işlem hattı çalıştırma kimliğini yakalar.

    ```powershell
    $runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
   ```
2. İşlem hattı çalıştırma durumunu tamamlanıncaya kadar sürekli olarak denetlemek için aşağıdaki betiği çalıştırın.

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

   Örnek çalıştırmanın çıktısı aşağıdaki gibidir:

    ```json
    Pipeline run status: In Progress
    
    ResourceGroupName : ADFV2SampleRG2
    DataFactoryName   : SampleV2DataFactory2
    ActivityName      : MyHiveActivity
    PipelineRunId     : 000000000-0000-0000-000000000000000000
    PipelineName      : MyHivePipeline
    Input             : {getDebugInfo, scriptPath, scriptLinkedService, defines}
    Output            :
    LinkedServiceName :
    ActivityRunStart  : 9/18/2017 6:58:13 AM
    ActivityRunEnd    :
    DurationInMs      :
    Status            : InProgress
    Error             :
    
    Pipeline ' MyHivePipeline' run finished. Result:
    
    ResourceGroupName : ADFV2SampleRG2
    DataFactoryName   : SampleV2DataFactory2
    ActivityName      : MyHiveActivity
    PipelineRunId     : 0000000-0000-0000-0000-000000000000
    PipelineName      : MyHivePipeline
    Input             : {getDebugInfo, scriptPath, scriptLinkedService, defines}
    Output            : {logLocation, clusterInUse, jobId, ExecutionProgress...}
    LinkedServiceName :
    ActivityRunStart  : 9/18/2017 6:58:13 AM
    ActivityRunEnd    : 9/18/2017 6:59:16 AM
    DurationInMs      : 63636
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity Output section:
    "logLocation": "wasbs://adfjobs@adfv2samplestor.blob.core.windows.net/HiveQueryJobs/000000000-0000-47c3-9b28-1cdc7f3f2ba2/18_09_2017_06_58_18_023/Status"
    "clusterInUse": "https://adfv2HivePrivate.azurehdinsight.net"
    "jobId": "job_1505387997356_0024"
    "ExecutionProgress": "Succeeded"
    "effectiveIntegrationRuntime": "MySelfhostedIR"
    Activity Error section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "MyHiveActivity"
    ```
4. Hive sorgu sonucu olarak oluşturulan yeni dosya için `outputfolder` klasörünü denetleyin; aşağıdaki örnek çıktı gibi görünmelidir: 

   ```
   8 en-US SCH-i500 California
   23 en-US Incredible Pennsylvania
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   246 en-US SCH-i500 District Of Columbia
   246 en-US SCH-i500 District Of Columbia
   ```

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide aşağıdaki adımları gerçekleştirdiniz: 

> [!div class="checklist"]
> * Veri fabrikası oluşturma. 
> * Şirket içinde barındırılan tümleştirme çalışma zamanı yazma ve kurma
> * Bağlantılı hizmetler yazma ve dağıtma.
> * Hive etkinliği içeren bir işlem hattı yazma ve dağıtma.
> * Bir işlem hattı çalıştırması başlatma.
> * İşlem hattı çalıştırmasını izleme 
> * çıktıyı doğrulama. 

Azure üzerinde bir Spark kümesi kullanarak veri dönüştürme hakkında bilgi edinmek için aşağıdaki öğreticiye geçin:

> [!div class="nextstepaction"]
>[Data Factory denetim akışında dal oluşturma ve zincirleme](tutorial-control-flow.md)



