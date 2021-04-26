---
title: 'Hızlı başlangıç: Azure CLı kullanarak Azure Data Factory oluşturma'
description: Bu hızlı başlangıç, bağlı hizmet, veri kümeleri ve işlem hattı dahil bir Azure Data Factory oluşturur. Bir dosya kopyalama eylemi yapmak için işlem hattını çalıştırabilirsiniz.
author: linda33wj
ms.author: jingwang
ms.service: azure-cli
ms.topic: quickstart
ms.date: 03/24/2021
ms.custom:
- template-quickstart
- devx-track-azurecli
ms.openlocfilehash: b40407f4c4fb81bbf76bd0b552f3c9f2c827232a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871545"
---
# <a name="quickstart-create-an-azure-data-factory-using-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak Azure Data Factory oluşturma

Bu hızlı başlangıçta Azure Data Factory oluşturmak için Azure CLı 'nin nasıl kullanılacağı açıklanmaktadır. Bu veri fabrikasında oluşturduğunuz işlem hattı, verileri bir Azure Blob depolama alanındaki bir klasörden başka bir klasöre kopyalar. Azure Data Factory kullanarak verileri dönüştürme hakkında daha fazla bilgi için bkz. [Azure Data Factory verileri dönüştürme](transform-data.md).

Azure Data Factory hizmetine giriş bilgileri için bkz. [Azure Data Factory'ye giriş](introduction.md).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

[!INCLUDE [azure-cli-prepare-your-environment](../../includes/azure-cli-prepare-your-environment.md)]

> [!NOTE]
> Data Factory örnekleri oluşturmak için, Azure’da oturum açarken kullandığınız kullanıcı hesabı, katkıda bulunan, sahip veya yönetici rollerinin üyesi ya da bir Azure aboneliğinin yöneticisi olmalıdır. Daha fazla bilgi için bkz. [Azure rolleri](quickstart-create-data-factory-powershell.md#azure-roles).

## <a name="prepare-a-container-and-test-file"></a>Kapsayıcı ve test dosyası hazırlama

Bu hızlı başlangıç, bir dosya içeren bir kapsayıcı içeren bir Azure depolama hesabı kullanır.

1. Adlı bir kaynak grubu oluşturmak için `ADFQuickStartRG` [az Group Create](/cli/azure/group#az_group_create) komutunu kullanın:

   ```azurecli
   az group create --name ADFQuickStartRG --location eastus
   ```

1. [Az Storage Account Create](/cli/azure/storage/container#az_storage_container_create) komutunu kullanarak bir depolama hesabı oluşturun:

   ```azurecli
   az storage account create --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --location eastus
   ```

1. `adftutorial` [Az Storage Container Create](/cli/azure/storage/container#az_storage_container_create) komutunu kullanarak adlı bir kapsayıcı oluşturun:

   ```azurecli
   az storage container create --resource-group ADFQuickStartRG --name adftutorial \
       --account-name adfquickstartstorage --auth-mode key
   ```

1. Yerel dizinde, karşıya yüklemek için adlı bir dosya oluşturun `emp.txt` . Azure Cloud Shell ' de çalışıyorsanız, Bash komutunu kullanarak geçerli çalışma dizinini bulabilirsiniz `echo $PWD` . Bir dosya oluşturmak için, gibi standart Bash komutlarını kullanabilirsiniz `cat` :

   ```console
   cat > emp.txt
   This is text.
   ```

   Yeni dosyanızı kaydetmek için **CTRL + D** tuşlarını kullanın.

1. Yeni dosyayı Azure depolama kapsayıcısına yüklemek için [az Storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) komutunu kullanın:

   ```azurecli
   az storage blob upload --account-name adfquickstartstorage --name input/emp.txt \
       --container-name adftutorial --file emp.txt --auth-mode key
   ```

   Bu komut adlı yeni bir klasöre yükler `input` .

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

Bir Azure Data Factory oluşturmak için [az DataFactory Factory Create](/cli/azure/datafactory/factory#az_datafactory_factory_create) komutunu çalıştırın:

```azurecli
az datafactory factory create --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

> [!IMPORTANT]
> `ADFTutorialFactory`Genel olarak benzersiz bir veri fabrikası adıyla değiştirin, örneğin, ADFTutorialFactorySP1127.

[Az DataFactory Factory Show](/cli/azure/datafactory/factory#az_datafactory_factory_show) komutunu kullanarak oluşturduğunuz veri fabrikasını görebilirsiniz:

```azurecli
az datafactory factory show --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

## <a name="create-a-linked-service-and-datasets"></a>Bağlı hizmet ve veri kümeleri oluşturma

Ardından, bağlı bir hizmet ve iki veri kümesi oluşturun.

1. [Az Storage Account Show-Connection-String](/cli/azure/datafactory/factory#az_datafactory_factory_show) komutunu kullanarak depolama hesabınızın bağlantı dizesini alın:

   ```azurecli
   az storage account show-connection-string --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --key primary
   ```

1. Çalışma dizininizde, bu içeriğe sahip bir JSON dosyası oluşturun ve önceki adımdan kendi Bağlantı dizenizi dahil edin. Dosyayı adlandırın `AzureStorageLinkedService.json` :

   ```json
   {
       "type":"AzureStorage",
           "typeProperties":{
           "connectionString":{
           "type": "SecureString",
           "value":"DefaultEndpointsProtocol=https;AccountName=adfquickstartstorage;AccountKey=K9F4Xk/EhYrMBIR98rtgJ0HRSIDU4eWQILLh2iXo05Xnr145+syIKNczQfORkQ3QIOZAd/eSDsvED19dAwW/tw==;EndpointSuffix=core.windows.net"
           }
       }
   }
   ```

1. `AzureStorageLinkedService` [Az DataFactory Linked-Service Create](/cli/azure/datafactory/linked-service#az_datafactory_linked_service_create) komutunu kullanarak adlı bağlı bir hizmet oluşturun:

   ```azurecli
   az datafactory linked-service create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --linked-service-name AzureStorageLinkedService \
       --properties @AzureStorageLinkedService.json
   ```

1. Çalışma dizininizde, bu içerikle birlikte şu adlı bir JSON dosyası oluşturun `InputDataset.json` :

   ```json
   {
       "type": 
           "AzureBlob",
           "linkedServiceName": {
               "type":"LinkedServiceReference",
               "referenceName":"AzureStorageLinkedService"
               },
           "annotations": [],
           "type": "Binary",
           "typeProperties": {
               "location": {
                   "type": "AzureBlobStorageLocation",
                   "fileName": "emp.txt",
                   "folderPath": "input",
                   "container": "adftutorial"
           }
       }
   }
   ```

1. `InputDataset` [Az DataFactory DataSet Create](/cli/azure/datafactory/dataset#az_datafactory_dataset_create) komutunu kullanarak adlı bir giriş veri kümesi oluşturun:

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name InputDataset --factory-name ADFQuickStartFactory \
       --properties @InputDataset.json
   ```

1. Çalışma dizininizde, bu içerikle birlikte şu adlı bir JSON dosyası oluşturun `OutputDataset.json` :

   ```json
   {
       "type": 
           "AzureBlob",
           "linkedServiceName": {
               "type":"LinkedServiceReference",
               "referenceName":"AzureStorageLinkedService"
               },
           "annotations": [],
           "type": "Binary",
           "typeProperties": {
               "location": {
                   "type": "AzureBlobStorageLocation",
                   "fileName": "emp.txt",
                   "folderPath": "output",
                   "container": "adftutorial"
           }
       }
   }
   ```

1. `OutputDataset` [Az DataFactory DataSet Create](/cli/azure/datafactory/dataset#az_datafactory_dataset_create) komutunu kullanarak adlı bir çıktı veri kümesi oluşturun:

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name OutputDataset --factory-name ADFQuickStartFactory \
       --properties @OutputDataset.json
   ```

## <a name="create-and-run-the-pipeline"></a>İşlem hattını oluşturma ve çalıştırma

Son olarak, işlem hattını oluşturun ve çalıştırın.

1. Çalışma dizininizde, şu içerik adlı bir JSON dosyası oluşturun `Adfv2QuickStartPipeline.json` :

   ```json
   {
       "name": "Adfv2QuickStartPipeline",
       "properties": {
           "activities": [
               {
                   "name": "CopyFromBlobToBlob",
                   "type": "Copy",
                   "dependsOn": [],
                   "policy": {
                       "timeout": "7.00:00:00",
                       "retry": 0,
                       "retryIntervalInSeconds": 30,
                       "secureOutput": false,
                       "secureInput": false
                   },
                   "userProperties": [],
                   "typeProperties": {
                          "source": {
                           "type": "BinarySource",
                           "storeSettings": {
                               "type": "AzureBlobStorageReadSettings",
                               "recursive": true
                           }
                       },
                       "sink": {
                           "type": "BinarySink",
                           "storeSettings": {
                               "type": "AzureBlobStorageWriteSettings"
                           }
                       },
                       "enableStaging": false
                   },
                   "inputs": [
                       {
                           "referenceName": "InputDataset",
                           "type": "DatasetReference"
                       }
                   ],
                   "outputs": [
                       {
                           "referenceName": "OutputDataset",
                           "type": "DatasetReference"
                       }
                   ]
               }
           ],
           "annotations": []
       }
   }
   ```

1. `Adfv2QuickStartPipeline` [Az DataFactory ardışık düzen Create](/cli/azure/datafactory/pipeline#az_datafactory_pipeline_create) komutunu kullanarak adlı bir işlem hattı oluşturun:

   ```azurecli
   az datafactory pipeline create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --name Adfv2QuickStartPipeline \
       --pipeline @Adfv2QuickStartPipeline.json
   ```

1. [Az DataFactory ardışık düzen oluştur-Çalıştır](/cli/azure/datafactory/pipeline#az_datafactory_pipeline_create_run) komutunu kullanarak işlem hattını çalıştırın:

   ```azurecli
   az datafactory pipeline create-run --resource-group ADFQuickStartRG \
       --name Adfv2QuickStartPipeline --factory-name ADFTutorialFactory
   ```

   Bu komut bir çalıştırma KIMLIĞI döndürür. Sonraki komutta kullanmak üzere kopyalayın.

1. [Az DataFactory işlem hattı-Run Show](/cli/azure/datafactory/pipeline-run#az_datafactory_pipeline_run_show) komutunu kullanarak işlem hattının başarılı olduğunu doğrulayın:

   ```azurecli
   az datafactory pipeline-run show --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --run-id 00000000-0000-0000-0000-000000000000
   ```

[Azure Portal](https://portal.azure.com/)kullanarak, işlem hattınızı beklendiği gibi çalıştığını da doğrulayabilirsiniz. Daha fazla bilgi için bkz. [dağıtılan kaynakları İnceleme](quickstart-create-data-factory-powershell.md#review-deployed-resources).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıçtaki tüm kaynaklar aynı kaynak grubunun bir parçasıdır. Tümünü kaldırmak için [az Group Delete](/cli/azure/group#az_group_delete) komutunu kullanın:

```azurecli
az group delete --name ADFQuickStartRG
```

Bu kaynak grubunu başka bir şey için kullanıyorsanız, tek tek kaynakları silin. Örneğin, bağlı hizmeti kaldırmak için [az DataFactory Linked-Service Delete](/cli/azure/datafactory/linked-service#az_datafactory_linked_service_delete) komutunu kullanın.

Bu hızlı başlangıçta aşağıdaki JSON dosyalarını oluşturdunuz:

- Üzerinde AzureStorageLinkedService.js
- Üzerinde InputDataset.js
- Üzerinde OutputDataset.js
- Üzerinde Adfv2QuickStartPipeline.js

Standart Bash komutlarını kullanarak bunları silin.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Factory’de işlem hatları ve etkinlikler](concepts-pipelines-activities.md)
- [Azure Data Factory'de bağlı hizmetler](concepts-linked-services.md)
- [Azure Data Factory'de veri kümeleri](concepts-datasets-linked-services.md)
- [Azure Data Factory'de veri dönüştürme](transform-data.md)
