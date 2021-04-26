---
title: Service Fabric bir kafes uygulamasında Azure dosya tabanlı birim kullanma
description: Azure CLı kullanarak bir hizmet içinde Azure dosya tabanlı bir birim bağlayarak Azure Service Fabric kafes uygulamasında durum depolamayı öğrenin.
author: georgewallace
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: gwallace
ms.custom: mvc, devcenter , devx-track-azurecli
ms.openlocfilehash: 40d10568e13ad455bc5178821da80e89f4132e93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625846"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Azure dosya tabanlı bir birimi Service Fabric bir kafes uygulamasına bağlama 

> [!IMPORTANT]
> Azure Service Fabric ağı önizlemesi devre dışı bırakıldı. Yeni dağıtımlar Service Fabric kafes API 'SI aracılığıyla artık izin verilmeyecektir. Mevcut dağıtımlar için destek 28 Nisan 2021 ' den devam edecektir.
> 
> Ayrıntılar için bkz. [Azure Service Fabric kafes önizleme kullanımdan](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)kaldırma.

Bu makalede, bir Azure dosya tabanlı birimin Service Fabric bir kafes uygulamasının bir hizmetine nasıl bağlayalacağı açıklanır.  Azure dosyaları birim sürücüsü, bir Azure dosya paylaşımının hizmet durumunu kalıcı hale getirmek için kullandığınız bir kapsayıcıya bağlanması için kullanılan bir Docker birimi sürücüsüdür. Birimler, genel amaçlı dosya depolaması sağlar ve normal disk g/ç dosya API 'Lerini kullanarak dosyaları okumanızı/yazmanızı sağlar.  Uygulama verilerini depolamaya yönelik birimler ve seçenekler hakkında daha fazla bilgi edinmek için [depolama durumunu](service-fabric-mesh-storing-state.md)okuyun.

Bir hizmete bir birimi bağlamak için, Service Fabric kafes uygulamanızda bir birim kaynağı oluşturun ve sonra bu birime hizmetinize başvurun.  Birim kaynağını bildirmek ve hizmet kaynağında buna başvurmak, [YAML tabanlı kaynak dosyalarında](#declare-a-volume-resource-and-update-the-service-resource-yaml) ya da [JSON tabanlı dağıtım şablonunda](#declare-a-volume-resource-and-update-the-service-resource-json)yapılabilir. Birimi bağlamadan önce Azure dosyalarında bir Azure depolama hesabı ve bir [dosya paylaşma](../storage/files/storage-how-to-create-file-share.md)oluşturun.

## <a name="prerequisites"></a>Önkoşullar
> [!NOTE]
> **WINDOWS RS5 geliştirme makinesinde dağıtım ile Ilgili bilinen sorun:** Azurefile birimlerinin bağlanmasını önleyen RS5 Windows makinelerde PowerShell cmdlet 'i New-SmbGlobalMapping açık hata vardır. AzureFile tabanlı birim yerel geliştirme makinesine bağlandığında karşılaşılan örnek hata aşağıda verilmiştir.
```
Error event: SourceId='System.Hosting', Property='CodePackageActivation:counterService:EntryPoint:131884291000691067'.
There was an error during CodePackage activation.System.Fabric.FabricException (-2147017731)
Failed to start Container. ContainerName=sf-2-63fc668f-362d-4220-873d-85abaaacc83e_6d6879cf-dd43-4092-887d-17d23ed9cc78, ApplicationId=SingleInstance_0_App2, ApplicationName=fabric:/counterApp. DockerRequest returned StatusCode=InternalServerError with ResponseBody={"message":"error while mounting volume '': mount failed"}
```
Sorunun geçici çözümü 1 ' dir) PowerShell Yöneticisi olarak aşağıdaki komutu çalıştırın ve 2) makineyi yeniden başlatın.
```powershell
PS C:\WINDOWS\system32> Mofcomp c:\windows\system32\wbem\smbwmiv2.mof
```

Bu makaleyi tamamlayabilmeniz için Azure Cloud Shell veya yerel bir Azure CLı yüklemesi kullanabilirsiniz. 

Azure CLı 'yi bu makaleyle yerel olarak kullanmak için, `az --version` en azından döndürdüğünden emin olun `azure-cli (2.0.43)` .  Bu [yönergeleri](service-fabric-mesh-howto-setup-cli.md)izleyerek Azure SERVICE fabrıc kafes CLI uzantısı modülünü yükler (veya güncelleştirir).

Azure 'da oturum açmak ve aboneliğinizi ayarlamak için:

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>Depolama hesabı ve dosya paylaşma oluşturma (isteğe bağlı)
Azure dosyaları birimi bağlamak için bir depolama hesabı ve dosya paylaşımının olması gerekir.  Mevcut bir Azure Depolama hesabını ve dosya payını kullanabilir veya kaynak oluşturabilirsiniz:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>Depolama hesabı adını ve anahtarını ve dosya paylaşımının adını alın
Depolama hesabı adı, depolama hesabı anahtarı ve dosya paylaşımının adı, `<storageAccountName>` `<storageAccountKey>` , ve olarak, `<fileShareName>` Aşağıdaki bölümlerde başvurulur. 

Depolama hesaplarınızı listeleyin ve kullanmak istediğiniz dosya paylaşımının bulunduğu depolama hesabının adını alın:
```azurecli-interactive
az storage account list
```

Dosya paylaşımının adını alın:
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

Depolama hesabı anahtarını al ("KEY1"):
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

Bu değerleri [Azure Portal](https://portal.azure.com)de bulabilirsiniz:
* `<storageAccountName>` - **Depolama hesapları** altında, dosya paylaşımının oluşturulması için kullanılan depolama hesabının adı.
* `<storageAccountKey>` - **Depolama hesapları** altında depolama hesabınızı seçin ve ardından **erişim anahtarları** ' nı seçin ve **KEY1** altındaki değeri kullanın.
* `<fileShareName>` -  **Depolama hesapları** altında depolama hesabınızı seçin ve ardından **dosyalar**' ı seçin. Kullanılacak ad, oluşturduğunuz dosya paylaşımının adıdır.

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>Bir birim kaynağı bildirme ve hizmet kaynağını güncelleştirme (JSON)

`<fileShareName>` `<storageAccountName>` `<storageAccountKey>` Önceki bir adımda bulduğunuz, ve değerleri için parametreler ekleyin. 

Uygulama kaynağının eşi olarak bir birim kaynağı oluşturun. Azure dosya tabanlı birimi kullanmak için bir ad ve sağlayıcı ("SFAzureFile") belirtin. İçinde, `azureFileParameters` `<fileShareName>` `<storageAccountName>` `<storageAccountKey>` önceki adımda bulduğunuz, ve değerleri için parametreleri belirtin.

Birimi hizmetinize bağlamak için, `volumeRefs` `codePackages` hizmetinin öğesine bir ekleyin.  `name` birim için kaynak KIMLIĞI (veya birim kaynağı için bir dağıtım şablonu parametresi) ve Volume. YAML kaynak dosyasında belirtilen birimin adı.  `destinationPath` , birimin bağlı olacağı yerel dizindir.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "defaultValue": "EastUS",
      "type": "String",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "fileShareName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure Files file share that provides the volume for the container."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure storage account that contains the file share."
      }
    },
    "storageAccountKey": {
      "type": "securestring",
      "metadata": {
        "description": "Access key for the Azure storage account that contains the file share."
      }
    },
    "stateFolderName": {
      "type": "string",
      "defaultValue": "TestVolumeData",
      "metadata": {
        "description": "Folder in which to store the state. Provide an empty value to create a unique folder for each container to store the state. A non-empty value will retain the state across deployments, however if more than one applications are using the same folder, the counter may update more frequently."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "VolumeTest",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/VolumeTestNetwork",
        "Microsoft.ServiceFabricMesh/volumes/testVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "VolumeTestService",
            "properties": {
              "description": "VolumeTestService description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "VolumeTestService",
                  "image": "volumetestservice:dev",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]",
                      "destinationPath": "C:\\app\\data"
                    }
                  ],
                  "environmentVariables": [
                    {
                      "name": "ASPNETCORE_URLS",
                      "value": "http://+:20003"
                    },
                    {
                      "name": "STATE_FOLDER_NAME",
                      "value": "[parameters('stateFolderName')]"
                    }
                  ],
                  ...
                }
              ],
              ...
            }
          }
        ],
        "description": "VolumeTest description."
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "testVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for the test application.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
    ...
  ]
}
```

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>Bir birim kaynağı bildirme ve hizmet kaynağını güncelleştirme (YAML)

Uygulamanız için *uygulama kaynakları* dizinine yeni bir *Volume. YAML* dosyası ekleyin.  Azure dosya tabanlı birimi kullanmak için bir ad ve sağlayıcı ("SFAzureFile") belirtin. `<fileShareName>`, `<storageAccountName>` ve `<storageAccountKey>` önceki bir adımda bulduğunuz değerlerdir.

```yaml
volume:
  schemaVersion: 1.0.0-preview2
  name: testVolume
  properties:
    description: Azure Files storage volume for counter App.
    provider: SFAzureFile
    azureFileParameters: 
        shareName: <fileShareName>
        accountName: <storageAccountName>
        accountKey: <storageAccountKey>
```

Hizmet *kaynakları* dizinindeki *Service. YAML* dosyasını, birimi hizmetinize bağlamak için güncelleştirin.  Öğesini `volumeRefs` `codePackages` öğesine ekleyin.  `name` birim için kaynak KIMLIĞI (veya birim kaynağı için bir dağıtım şablonu parametresi) ve Volume. YAML kaynak dosyasında belirtilen birimin adı.  `destinationPath` , birimin bağlı olacağı yerel dizindir.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: VolumeTest
  properties:
    services:
      - name: VolumeTestService
        properties:
          description: VolumeTestService description.
          osType: Windows
          codePackages:
            - name: VolumeTestService
              image: volumetestservice:dev
              volumeRefs:
                - name: "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]"
                  destinationPath: C:\app\data
              endpoints:
                - name: VolumeTestServiceListener
                  port: 20003
              environmentVariables:
                - name: ASPNETCORE_URLS
                  value: http://+:20003
                - name: STATE_FOLDER_NAME
                  value: TestVolumeData
              resources:
                requests:
                  cpu: 0.5
                  memoryInGB: 1
          replicaCount: 1
          networkRefs:
            - name: VolumeTestNetwork
```

## <a name="next-steps"></a>Sonraki adımlar

- [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter)'Da Azure dosyaları toplu örnek uygulamasını görüntüleyin.
- Service Fabric Kaynak Modeli hakkında daha fazla bilgi için bkz. [Service Fabric Mesh Kaynak Modeli](service-fabric-mesh-service-fabric-resources.md).
- Service Fabric Mesh hakkında daha fazla bilgi edinmek için [Service Fabric Mesh’e genel bakış](service-fabric-mesh-overview.md) makalesini okuyun.
