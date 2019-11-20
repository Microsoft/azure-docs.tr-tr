---
title: Şablon işlevleri-kaynaklar
description: Kaynaklarla ilgili değerleri almak için bir Azure Resource Manager şablonunda kullanmak için işlevleri açıklar.
ms.topic: conceptual
ms.date: 10/26/2019
ms.openlocfilehash: 6457bafeeb0b241171311dc3dcea30b7b6993791
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150672"
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager şablonları için kaynak işlevleri

Resource Manager kaynak değerlerini almak için aşağıdaki işlevleri sunar:

* [Extensionresourceıd](#extensionresourceid)
* [Liste *](#list)
* [sağlayıcıları](#providers)
* [Başvuru](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [aboneliği](#subscription)
* [Subscriptionresourceıd](#subscriptionresourceid)
* [Tenantresourceıd](#tenantresourceid)

Parametreleri, değişkenleri veya geçerli dağıtım değerlerini almak için bkz. [dağıtım değeri işlevlerini](resource-group-template-functions-deployment.md).

## <a name="extensionresourceid"></a>Extensionresourceıd

```json
extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)
```

Özelliklerine eklemek için başka bir kaynağa uygulanan bir kaynak türü olan [uzantı kaynağı](extension-resource-types.md)IÇIN kaynak kimliğini döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| resourceId |Yes |string |Uzantı kaynağının uygulandığı kaynağın kaynak KIMLIĞI. |
| resourceType |Yes |string |Kaynak sağlayıcısı ad alanı dahil olmak üzere kaynak türü. |
| resourceName1 |Yes |string |Kaynağın adı. |
| resourceName2 |Hayır |string |Gerekirse, sonraki kaynak adı segmenti. |

Kaynak türü daha fazla kesim içerdiğinde kaynak adlarını parametre olarak eklemeye devam edin.

### <a name="return-value"></a>Dönüş değeri

Bu işlev tarafından döndürülen kaynak KIMLIĞININ temel biçimi:

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Kapsam segmenti, genişletilmekte olan kaynağa göre değişir.

Uzantı kaynağı bir **kaynağa**uygulandığında, kaynak kimliği aşağıdaki biçimde döndürülür:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Uzantı kaynağı bir **kaynak grubuna**uygulandığında, biçim şu şekilde olur:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Uzantı kaynağı bir **aboneliğe**uygulandığında, biçim şu şekilde olur:

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Uzantı kaynağı bir **yönetim grubuna**uygulandığında, biçim şu şekilde olur:

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>Extensionresourceıd örneği

Aşağıdaki örnek, bir kaynak grubu kilidinin kaynak KIMLIĞINI döndürür.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "lockName":{
            "type": "string"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "lockResourceId": {
            "type": "string",
            "value": "[extensionResourceId(resourceGroup().Id , 'Microsoft.Authorization/locks', parameters('lockName'))]"
        }
    }
}
```

<a id="listkeys" />
<a id="list" />

## <a name="list"></a>Listele

```json
list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)
```

Bu işlevin sözdizimi, liste işlemlerinin adına göre değişir. Her uygulama, bir liste işlemini destekleyen kaynak türünün değerlerini döndürür. İşlem adı `list`başlamalıdır. Bazı yaygın kullanımlar `listKeys` ve `listSecrets`. 

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| resourceName veya Resourceıdentifier |Yes |string |Kaynağın benzersiz tanımlayıcısı. |
| apiVersion |Yes |string |Kaynak çalışma zamanı durumu API sürümü. Genellikle, biçiminde **yyyy-aa-gg**. |
| functionValues |Hayır |object | İşlev için değerleri içeren nesne. Yalnızca bu nesne için parametre değerlerini içeren bir nesne gibi almayı destekleyen işlevleri sağlar **listAccountSas** bir depolama hesabı üzerinde. Bu makalede işlev değerlerini geçirme örneği gösterilmektedir. | 

### <a name="valid-uses"></a>Geçerli kullanımlar

Liste işlevleri yalnızca bir kaynak tanımı ve bir şablon ya da dağıtımın çıktılar bölümünün özelliklerinde kullanılabilir. [Özellik yinelemesi](resource-group-create-multiple.md#property-iteration)ile kullanıldığında, ifade kaynak özelliğine atandığından `input` için liste işlevlerini kullanabilirsiniz. Count, liste işlevi çözümlenmeden önce belirlenmesi gerektiğinden, bunları `count` kullanamazsınız.

### <a name="implementations"></a>Uygulamalar

Listenin olası kullanımları aşağıdaki tabloda gösterilmiştir.

| Kaynak türü | İşlev adı |
| ------------- | ------------- |
| Microsoft. AnalysisServices/sunucuları | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft. AppConfiguration/Configurationmağazaları | ListKeys 'i al |
| Microsoft. Automation/automationAccounts | [Listkeys'i](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [ListKeys 'i al](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft. Batchaı/Workspaces/denemeleri/Jobs | [lıutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft. Blockzincirine/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft. Blockzincirine/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft. BotService/botServices/Channels | listChannelWithKeys |
| Microsoft. Cache/redsıs | [Listkeys'i](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [Listkeys'i](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft. ContainerRegistry/kayıt defterleri | [listBuildSourceUploadUrl 'Si](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft. ContainerRegistry/kayıt defterleri | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft. ContainerRegistry/kayıt defterleri | [Listkullanımlar](/rest/api/containerregistry/registries/listusages) |
| Microsoft. ContainerRegistry/kayıt defterleri/Web kancaları | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft. ContainerRegistry/kayıt defterleri/çalıştırmaları | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft. ContainerRegistry/kayıt defterleri/görevler | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft. ContainerService/Managedkümeler | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft. ContainerService/Managedkümeler | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft. ContainerService/Managedkümeler/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft. DataBox/Jobs | listCredentials |
| Microsoft. DataFactory/DataFactory/ağ geçitleri | listauthkeys |
| Microsoft. DataFactory/Factory/ıntegrationçalışma zamanları | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft. DataLakeAnalytics/hesaplar/storageAccounts/kapsayıcılar | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft. Devices/iotHubs | [ListKeys 'i al](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft. Devices/provisioningServices/anahtarlar | [ListKeys 'i al](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft. Devices/provisioningServices | [ListKeys 'i al](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft. DevTestLab/Labs | [Listvhd 'ler](/rest/api/dtl/labs/listvhds) |
| Microsoft. DevTestLab/Labs/zamanlamaları | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft. DevTestLab/Labs/Users/Serviceyapılar | [Listapperepblezamanlamalar](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft. DevTestLab/Labs/virtualMachines | [Listapperepblezamanlamalar](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft. DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft. DocumentDB/databaseAccounts | [Listkeys'i](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [Listdomainönerilere](/rest/api/appservice/domains/listrecommendations) |
| Microsoft. DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft. EventGrid/Domains | [Listkeys'i](/rest/api/eventgrid/domains/listsharedaccesskeys) |
| Microsoft. EventGrid/konuları | [Listkeys'i](/rest/api/eventgrid/topics/listsharedaccesskeys) |
| Microsoft. EventHub/ad alanları/authorizationRules | [ListKeys 'i al](/rest/api/eventhub/namespaces/listkeys) |
| Microsoft. EventHub/namespaces/Disyıldız Recoveryconfigs/authorizationRules | [ListKeys 'i al](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Microsoft. EventHub/namespaces/eventhubs/authorizationRules | [ListKeys 'i al](/rest/api/eventhub/eventhubs/listkeys) |
| Microsoft. ımportexport/işler | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft. LabServices/kullanıcılar | [Listortamortamları](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft. LabServices/kullanıcılar | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft. Logic/ıntegrationaccounts/sözleşmeleri | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft. Logic/ıntegrationaccounts/derlemeler | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft. Logic/ıntegrationaccounts/Maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft. Logic/ıntegrationaccounts/iş ortakları | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft. Logic/ıntegrationaccounts/schemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft. Logic/iş akışları/Tetikleyiciler | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft. Logic/iş akışları/sürümler/Tetikleyiciler | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft. Machinöğrenim/webServices | [ListKeys 'i al](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft. Machinöğrenim/çalışma alanları | listworkspacekeys |
| Microsoft. MachineLearningServices/çalışma alanları/hesaplar | ListKeys 'i al |
| Microsoft. MachineLearningServices/çalışma alanları | ListKeys 'i al |
| Microsoft. Maps/hesaplar | [Listkeys'i](/rest/api/maps-management/accounts/listkeys) |
| Microsoft. Media/mediaservices/varlıkları | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft. Media/mediaservices/varlıkları | [Liststreamingkonumlandırıcı](/rest/api/media/assets/liststreaminglocators) |
| Microsoft. Media/mediaservices/Streamingkonumlandırıcı | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft. Media/mediaservices/Streamingkonumlandırıcı | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft. Network/applicationSecurityGroups | Liztipi yapılandırması |
| Microsoft. Notificationhub 'Lar/ad alanları/authorizationRules | [ListKeys 'i al](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft. Notificationhub 'Lar/ad alanları/Notificationhub/authorizationRules | [ListKeys 'i al](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft. Operationalınsights/çalışma alanları | [Listkeys'i](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Microsoft. Relay/Namespace/authorizationRules | [ListKeys 'i al](/rest/api/relay/namespaces/listkeys) |
| Microsoft. Relay/namespaces/Disyıldız Recoveryconfigs/authorizationRules | ListKeys 'i al |
| Microsoft. Relay/Namespace/HybridConnections/authorizationRules | [ListKeys 'i al](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft. Relay/namespaces/Wcfreyerleştir/authorizationRules | [ListKeys 'i al](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft. ServiceBus/namespaces/authorizationRules | [ListKeys 'i al](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft. ServiceBus/namespaces/Diskalrecoveryconfigs/authorizationRules | [ListKeys 'i al](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft. ServiceBus/namespaces/kuyruklar/authorizationRules | [ListKeys 'i al](/rest/api/servicebus/queues/listkeys) |
| Microsoft. ServiceBus/namespaces/konular/authorizationRules | [ListKeys 'i al](/rest/api/servicebus/topics/listkeys) |
| Microsoft. SignalRService/SignalR | [ListKeys 'i al](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [ListKeys 'i al](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft. StorSimple/yöneticileri/cihazları | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft. StorSimple/yöneticileri/cihazları | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft. StorSimple/yöneticileri | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft. StorSimple/yöneticileri | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft. Web/Connectiongateway 'ler | ListStatus |
| Microsoft. Web/Connections | listconsentlinks |
| Microsoft. Web/Customapsıs | Listwsdlınterfaces |
| Microsoft. Web/konumlar | listwsdlınterfaces |
| Microsoft. Web/apimanagementaccounts/API/bağlantı | listconnectionkeys |
| Microsoft. Web/apimanagementaccounts/API/bağlantı | listgizlilikler |
| Microsoft. Web/Sites/işlevleri | [listgizlilikler](/rest/api/appservice/webapps/listfunctionsecrets) |
| Microsoft. Web/Sites/hybridconnectionnamespaces/geçişler | [ListKeys 'i al](/rest/api/appservice/webapps/listhybridconnectionkeys) |
| Microsoft. Web/siteler | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| Microsoft. Web/Sites/yuvaları/işlevleri | [listgizlilikler](/rest/api/appservice/webapps/listfunctionsecretsslot) |

Hangi kaynak türlerinin bir listesini işlemi olduğunu belirlemek için aşağıdaki seçenekleriniz vardır:

* Görünüm [REST API işlemleri](/rest/api/) bir kaynak sağlayıcısı ve liste işlemleri arayın. Örneğin, depolama hesaplarının [Listkeys'i işlemi](/rest/api/storagerp/storageaccounts).
* [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) PowerShell cmdlet 'ini kullanın. Aşağıdaki örnek, depolama hesapları için tüm listeleme işlemleri alır:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Listeleme işlemleri filtrelemek için aşağıdaki Azure CLI komutunu kullanın:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Dönüş değeri

Döndürülen nesne, kullandığınız liste işlevine göre değişir. Örneğin, bir depolama hesabı için listKeys aşağıdaki biçimi döndürür:

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

Diğer liste işlevleri farklı dönüş biçimlerine sahip. Bir işlev biçimini görmek için örnek şablonda görüldüğü gibi çıktıların bölüme ekleyin.

### <a name="remarks"></a>Açıklamalar

Kaynak adı kullanarak bir kaynak belirtin veya [ResourceId işlevi](#resourceid). Başvurulan kaynağı dağıtan şablonda bir liste işlevi kullanırken, kaynak adını kullanın.

Koşullu olarak dağıtılan bir kaynakta bir **liste** işlevi kullanıyorsanız, işlev, kaynak dağıtılmasa bile değerlendirilir. **List** işlevi mevcut olmayan bir kaynağa başvuruyorsa bir hata alırsınız. İşlevin yalnızca kaynak dağıtıldığında değerlendirildiğinden emin olmak için **IF** işlevini kullanın. Koşullu olarak dağıtılan bir kaynakla IF ve List kullanan bir örnek şablon için [IF işlevine](resource-group-template-functions-logical.md#if) bakın.

### <a name="list-example"></a>Liste örneği

Aşağıdaki [örnek şablonu](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) nasıl çıktılar bölümünü bir depolama hesabı birincil ve ikincil anahtarları döndürüleceğini gösterir. Ayrıca, depolama hesabı için bir SAS belirtecini döndürür. 

SAS belirtecini almak için bir nesneyi süre sonu zamanına geçirin. Süre sonu zamanı gelecekte olmalıdır. Bu örnekte liste işlevlerini nasıl kullanacağınız göstermek için tasarlanmıştır. Genellikle, bir kaynak değerinde bir SAS belirteci kullanabilir yerine, bir çıkış değeri döndürür. Çıkış değerleri dağıtım geçmişini depolanır ve güvenli değildir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagename": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "southcentralus"
        },
        "accountSasProperties": {
            "type": "object",
            "defaultValue": {
                "signedServices": "b",
                "signedPermission": "r",
                "signedExpiry": "2018-08-20T11:00:00Z",
                "signedResourceTypes": "s"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2018-02-01",
            "name": "[parameters('storagename')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {
                "supportsHttpsTrafficOnly": false,
                "accessTier": "Hot",
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "keys": {
            "type": "object",
            "value": "[listKeys(parameters('storagename'), '2018-02-01')]"
        },
        "accountSAS": {
            "type": "object",
            "value": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties'))]"
        }
    }
}
```

## <a name="providers"></a>sağlayıcıları

```json
providers(providerNamespace, [resourceType])
```

Bir kaynak sağlayıcısı ve desteklenen kaynak türleri hakkında bilgi döndürür. Bir kaynak türü sağlamazsanız, işlev kaynak sağlayıcısı için desteklenen tüm türleri döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| providerNamespace |Yes |string |Namespace sağlayıcısı |
| resourceType |Hayır |string |Belirtilen ad alanı içinde kaynak türü. |

### <a name="return-value"></a>Dönüş değeri

Desteklenen her türü, şu biçimde döndürülür: 

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

Döndürülen değerleri dizi sıralama garantisi yoktur.

### <a name="providers-example"></a>Sağlayıcılar örneği

Aşağıdaki [örnek şablonu](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) sağlayıcısı işlevinin nasıl kullanılacağını gösterir:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "providerNamespace": {
            "type": "string"
        },
        "resourceType": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]",
            "type" : "object"
        }
    }
}
```

İçin **Microsoft.Web** kaynak sağlayıcısı ve **siteleri** kaynak türü, önceki örnekte nesne döndürür şu biçimde:

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

## <a name="reference"></a>Başvuru

```json
reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])
```

Bir kaynağın çalışma zamanı durumunu temsil eden bir nesne döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| resourceName veya Resourceıdentifier |Yes |string |Adı veya bir kaynağın benzersiz tanımlayıcısı. Geçerli şablon kaynağında başvuran bir parametre olarak yalnızca kaynak adı belirtin. Daha önce dağıtılan bir kaynağa başvururken kaynak KIMLIĞI sağlayın. |
| apiVersion |Hayır |string |Belirtilen kaynak API sürümü. Kaynak, aynı şablonu içinde sağlanan değil, bu parametreyi dahil edin. Genellikle, biçiminde **yyyy-aa-gg**. Kaynağınız için geçerli API sürümleri için bkz. [şablon başvurusu](/azure/templates/). |
| 'Full' |Hayır |string |Tam kaynak nesnesini döndürülüp döndürülmeyeceğini belirleyen değer. Belirtmezseniz `'Full'`, yalnızca kaynak özellikleri nesne döndürülür. Tam nesne, konum ve kaynak kimliği gibi değerlerini içerir. |

### <a name="return-value"></a>Dönüş değeri

Her kaynak türü için başvuru işlevi farklı özellikleri döndürür. İşlevi, önceden tanımlanmış tek bir biçim döndürmüyor. Ayrıca, döndürülen değer tam nesne belirtilip göre farklılık gösterir. Bir kaynak türü için özellikleri görmek için örnekte gösterildiği gibi çıkış bölümü nesneyi döndürür.

### <a name="remarks"></a>Açıklamalar

Başvuru işlevi, daha önceden dağıtılan bir kaynak ya da geçerli şablon dağıtılan bir kaynak çalışma zamanı durumunu alır. Bu makalede her iki senaryo için örnekler gösterilmektedir.

Genellikle, kullandığınız **başvuru** blob uç noktası URI'si veya tam etki alanı adı gibi bir nesne belirli bir değer döndürüleceğini işlevi.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')), '2016-03-30').dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

Kullanım `'Full'` özellikleri şemanın parçası olmayan kaynak değerleri gerektiğinde. Örneğin, anahtar kasası erişim ilkeleri ayarlamak için bir sanal makine için kimlik özelliklerini alır.

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
        "objectId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

### <a name="valid-uses"></a>Geçerli kullanımlar

Başvuru işlevi yalnızca bir kaynak tanımı özelliklerini ve bir şablonu veya dağıtım çıktılar bölümünü kullanılabilir. [Özellik yinelemesi](resource-group-create-multiple.md#property-iteration)ile kullanıldığında, ifade kaynak özelliğine atandığından `input` için başvuru işlevini kullanabilirsiniz. Count, başvuru işlevi çözümlenmeden önce belirlenmesi gerektiğinden, bunu `count` kullanamazsınız.

İç içe yerleştirilmiş şablonda dağıttığınız bir kaynağı döndürmek için, [iç içe geçmiş bir şablonun](resource-group-linked-templates.md#nested-template) çıktılarında başvuru işlevini kullanamazsınız. Bunun yerine, [bağlantılı bir şablon](resource-group-linked-templates.md#external-template)kullanın.

Koşullu olarak dağıtılan bir kaynakta **başvuru** işlevini kullanırsanız, işlev, kaynak dağıtılmasa bile değerlendirilir.  **Başvuru** işlevi mevcut olmayan bir kaynağa başvuruyorsa bir hata alırsınız. İşlevin yalnızca kaynak dağıtıldığında değerlendirildiğinden emin olmak için **IF** işlevini kullanın. Koşullu olarak dağıtılan bir kaynakla IF ve Reference kullanan bir örnek şablon için [IF işlevine](resource-group-template-functions-logical.md#if) bakın.

### <a name="implicit-dependency"></a>Örtük bağımlılık

Başvuru işlevini kullanarak, aynı şablonu içinde başvurulan kaynak sağlandıktan ve kaynağa adıyla (kaynak kimliği değil) başvurun bir kaynak başka bir kaynaktaki bağlıdır örtük olarak bildirdiğiniz. Ayrıca dependsOn özelliği kullanmanız gerekmez. Başvurulan kaynak dağıtımı tamamlanana kadar işlevi değerlendirilmez.

### <a name="resource-name-or-identifier"></a>Kaynak adı veya tanımlayıcı

Aynı şablonda dağıtılan bir kaynağa başvururken kaynağın adını belirtin.

```json
"value": "[reference(parameters('storageAccountName'))]"
```

Aynı şablonda dağıtılan bir kaynağa başvururken kaynak KIMLIĞI ' ni belirtin.

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

Hangi kaynağı başvurduğunuz hakkında belirsizliğe engel olmak için tam bir kaynak adı sağlayabilirsiniz.

```json
"value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')))]"
```

Bir kaynağa tam nitelikli bir başvuru oluştururken, kesimleri tür ve ad ile birleştirme sırası yalnızca iki birleştirme değildir. Bunun yerine, ad alanından sonra en az belirli bir *türe/ad* çiftinden en belirgin bir dizi kullanın:

**{Resource-Provider-Namespace}/{Parent-Resource-Type}/{Parent-Resource-Name} [/{Child-Resource-Type}/{Child-Resource-Name}]**

Örneğin:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` doğru `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` doğru değil

### <a name="reference-example"></a>Başvuru örneği

Aşağıdaki [örnek şablonu](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) kaynak dağıtır ve o kaynağa başvuruyor.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": { 
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
``` 

Yukarıdaki örnekte, iki nesne döndürür. Özellikler nesnesinin aşağıdaki biçimdedir:

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

Tam nesne aşağıdaki biçimdedir:

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

Aşağıdaki [örnek şablonu](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) bu şablonda dağıtılan olmayan bir depolama hesabına başvuruyor. Depolama hesabı aynı abonelik içinde zaten var.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageResourceGroup": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]",
            "type": "object"
        }
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

```json
resourceGroup()
```

Geçerli kaynak grubunu temsil eden bir nesne döndürür. 

### <a name="return-value"></a>Dönüş değeri

Döndürülen nesne aşağıdaki biçimdedir:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

**ManagedBy** özelliği yalnızca başka bir hizmet tarafından yönetilen kaynakları içeren kaynak grupları için döndürülür. Yönetilen uygulamalar, Databricks ve AKS için, özelliğinin değeri, yöneten kaynağın kaynak KIMLIĞIDIR.

### <a name="remarks"></a>Açıklamalar

`resourceGroup()` işlevi, [abonelik düzeyinde dağıtılan](deploy-to-subscription.md)bir şablonda kullanılamaz. Yalnızca bir kaynak grubuna dağıtılan şablonlarda kullanılabilir.

Bir ortak resourceGroup işlevin kaynak grubu ile aynı konumda kaynakları oluşturmak için kullanılır. Aşağıdaki örnek, bir web sitesi için konum atamak için kaynak grubu konumu kullanır.

```json
"resources": [
   {
      "apiVersion": "2016-08-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

Kaynak grubundaki etiketleri bir kaynağa uygulamak için resourceGroup işlevini de kullanabilirsiniz. Daha fazla bilgi için bkz. [kaynak grubundan etiket uygulama](resource-group-using-tags.md#apply-tags-from-resource-group).

### <a name="resource-group-example"></a>Kaynak grubu örneği

Aşağıdaki [örnek şablonu](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) kaynak grubunun özelliklerini döndürür.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceGroupOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

Yukarıdaki örnekte, aşağıdaki biçimde bir nesne döndürür:

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="resourceid"></a>resourceId

```json
resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)
```

Bir kaynağın benzersiz tanımlayıcısını döndürür. Kaynak adı belirsiz veya aynı şablon içinde sağlanan olduğunda bu işlevi kullanın. 

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| subscriptionId |Hayır |dize (içinde GUID biçimi) |Varsayılan değer geçerli bir aboneliktir. Başka bir Abonelikteki kaynak almak, ihtiyacınız olduğunda bu değeri belirtin. |
| resourceGroupName |Hayır |string |Geçerli kaynak grubu varsayılan değerdir. Başka bir kaynak grubunda kaynak almak, ihtiyacınız olduğunda bu değeri belirtin. |
| resourceType |Yes |string |Kaynak sağlayıcısı ad alanı dahil olmak üzere kaynak türü. |
| resourceName1 |Yes |string |Kaynağın adı. |
| resourceName2 |Hayır |string |Gerekirse, sonraki kaynak adı segmenti. |

Kaynak türü daha fazla kesim içerdiğinde kaynak adlarını parametre olarak eklemeye devam edin.

### <a name="return-value"></a>Dönüş değeri

Kaynak KIMLIĞI aşağıdaki biçimde döndürülür:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

[Abonelik düzeyinde bir dağıtımda](deploy-to-subscription.md)kullanıldığında, kaynak kimliği aşağıdaki biçimde döndürülür:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Diğer biçimlerdeki KIMLIĞI almak için bkz.:

* [Extensionresourceıd](#extensionresourceid)
* [Subscriptionresourceıd](#subscriptionresourceid)
* [Tenantresourceıd](#tenantresourceid)

### <a name="remarks"></a>Açıklamalar

Sağladığınız parametrelerin sayısı, kaynağın bir üst veya alt kaynak olduğunu ve kaynağın aynı abonelikte veya kaynak grubunda olup olmamasına göre farklılık gösterir.

Aynı abonelik ve kaynak grubundaki bir üst kaynağın kaynak KIMLIĞINI almak için kaynağın türünü ve adını belirtin.

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

Bir alt kaynağın kaynak KIMLIĞINI almak için, kaynak türündeki parçaların sayısına dikkat edin. Kaynak türünün her segmenti için bir kaynak adı belirtin. Segmentin adı, hiyerarşinin o parçası için var olan kaynağa karşılık gelir.

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

Aynı abonelikte ancak farklı kaynak grubunda bulunan bir kaynağın kaynak KIMLIĞINI almak için, kaynak grubu adını sağlayın.

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

Farklı bir abonelik ve kaynak grubundaki bir kaynağın kaynak KIMLIĞINI almak için abonelik KIMLIĞI ve kaynak grubu adını sağlayın.

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Genellikle, bu işlev bir alternatif bir kaynak grubuna bir depolama hesabı veya sanal ağ kullanılırken kullanmanız gerekir. Aşağıdaki örnek, bir dış kaynak grubundan kaynak kolayca nasıl kullanılabileceğini gösterir:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "subnet1Ref": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="resource-id-example"></a>Kaynak KIMLIĞI örneği

Aşağıdaki [örnek şablonu](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) kaynak grubunda bir depolama hesabı kaynak kimliği döndürür:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "sameRGOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentRGOutput": {
            "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentSubOutput": {
            "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "nestedResourceOutput": {
            "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]",
            "type" : "string"
        }
    }
}
```

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| sameRGOutput | Dize | /Subscriptions/{Current-Sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Dize | /Subscriptions/{Current-Sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Dize | /Subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Dize | /Subscriptions/{Current-Sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/Servers/ServerName/Databases/databaseName |

## <a name="subscription"></a>subscription

```json
subscription()
```

Geçerli dağıtım için abonelik ayrıntılarını döndürür. 

### <a name="return-value"></a>Dönüş değeri

İşlev aşağıdaki biçimde veri döndürür:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="subscription-example"></a>Abonelik örneği

Aşağıdaki [örnek şablonu](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) abonelik işlevini çağırdı çıkışları bölümünde gösterir. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

## <a name="subscriptionresourceid"></a>Subscriptionresourceıd

```json
subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)
```

Abonelik düzeyinde dağıtılan bir kaynak için benzersiz tanımlayıcıyı döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| subscriptionId |Hayır |dize (GUID biçiminde) |Varsayılan değer geçerli bir aboneliktir. Başka bir Abonelikteki kaynak almak, ihtiyacınız olduğunda bu değeri belirtin. |
| resourceType |Yes |string |Kaynak sağlayıcısı ad alanı dahil olmak üzere kaynak türü. |
| resourceName1 |Yes |string |Kaynağın adı. |
| resourceName2 |Hayır |string |Gerekirse, sonraki kaynak adı segmenti. |

Kaynak türü daha fazla kesim içerdiğinde kaynak adlarını parametre olarak eklemeye devam edin.

### <a name="return-value"></a>Dönüş değeri

Tanımlayıcısı şu biçimde döndürülür:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Açıklamalar

Bu işlevi, bir kaynak grubu yerine [aboneliğe dağıtılan](deploy-to-subscription.md) KAYNAKLARıN kaynak kimliğini almak için kullanırsınız. Döndürülen KIMLIK, bir kaynak grubu değeri dahil değil [RESOURCEID](#resourceid) işlevi tarafından döndürülen değerden farklı.

### <a name="subscriptionresourceid-example"></a>Subscriptionresourceıd örneği

Aşağıdaki şablon yerleşik bir rol atar. Bunu bir kaynak grubuna veya aboneliğe dağıtabilirsiniz. Yerleşik rollerin kaynak KIMLIĞINI almak için Subscriptionresourceıd işlevini kullanır.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="tenantresourceid"></a>Tenantresourceıd

```json
tenantResourceId(resourceType, resourceName1, [resourceName2], ...)
```

Kiracı düzeyinde dağıtılan bir kaynak için benzersiz tanımlayıcıyı döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| resourceType |Yes |string |Kaynak sağlayıcısı ad alanı dahil olmak üzere kaynak türü. |
| resourceName1 |Yes |string |Kaynağın adı. |
| resourceName2 |Hayır |string |Gerekirse, sonraki kaynak adı segmenti. |

Kaynak türü daha fazla kesim içerdiğinde kaynak adlarını parametre olarak eklemeye devam edin.

### <a name="return-value"></a>Dönüş değeri

Tanımlayıcısı şu biçimde döndürülür:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Açıklamalar

Kiracıya dağıtılan bir kaynağın kaynak KIMLIĞINI almak için bu işlevi kullanın. Döndürülen KIMLIK, kaynak grubu veya abonelik değerleri dahil değil, diğer kaynak KIMLIĞI işlevleri tarafından döndürülen değerlerden farklıdır.

## <a name="next-steps"></a>Sonraki adımlar

* Bir Azure Resource Manager şablonu olarak bölümlerde açıklaması için bkz: [Azure Resource Manager şablonları yazma](resource-group-authoring-templates.md).
* Birden fazla şablon birleştirmek için bkz: [Azure Resource Manager ile bağlı şablonları kullanma](resource-group-linked-templates.md).
* Belirtilen sayıda yineleme için bir kaynak türünü oluştururken bkz [Azure Resource Manager'da kaynakları birden çok örneğini oluşturma](resource-group-create-multiple.md).
* Oluşturduğunuz bir şablonu dağıtmayı öğrenmek için bkz [Azure Resource Manager şablonu ile uygulama dağıtma](resource-group-template-deploy.md).

