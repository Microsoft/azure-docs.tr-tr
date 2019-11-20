---
title: REST API kullanarak Azure VM 'lerini yedekleme
description: Bu makalede, REST API kullanarak Azure VM yedeklemesi 'nin yedekleme işlemlerini yapılandırma, başlatma ve yönetme hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: c929f820862f5d041b4a63a1ca9c083abf1a1e4c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173454"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>REST API aracılığıyla Azure Backup kullanarak bir Azure VM 'yi yedekleme

Bu makalede, REST API aracılığıyla Azure Backup kullanarak bir Azure VM için yedeklemelerin nasıl yönetileceği açıklanır. Daha önce korumasız bir Azure VM için korumayı ilk kez yapılandırmak, korumalı bir Azure VM için isteğe bağlı bir yedekleme tetikleyip, yedeklenen bir sanal makinenin yedekleme özelliklerini burada açıklandığı gibi REST API aracılığıyla değiştirin.

Yeni kasa ve ilke oluşturmaya yönelik REST API [kasa oluşturma](backup-azure-arm-userestapi-createorupdatevault.md) ve [ilke oluşturma](backup-azure-arm-userestapi-createorupdatepolicy.md) öğreticilerine bakın.

"TestRG" kaynak grubu altındaki "testVM" sanal makinesini "testVaultRG" kaynak grubunda ("DefaultPolicy" adlı) içeren "Testkasag" adlı bir kurtarma hizmetleri kasasıyla korumak istediğinizi varsayalım.

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>REST API kullanarak korumasız bir Azure VM için yedeklemeyi yapılandırma

### <a name="discover-unprotected-azure-vms"></a>Korumasız Azure VM 'lerini bulma

İlk olarak, kasanın Azure VM 'yi tanımlayabilmesi gerekir. Bu, [yenileme işlemi](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh)kullanılarak tetiklenir. Bu, kasasının geçerli abonelikteki tüm korumasız VM 'nin en son listesini aldığından ve bunlara ' önbelleğe aldığınızdan emin olan zaman uyumsuz bir *Post* işlemidir. VM ' önbelleğe alındıktan sonra, Kurtarma Hizmetleri sanal makineye erişebilir ve bunu koruyabilir.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

POST URI 'sinin `{subscriptionId}`, `{vaultName}`, `{vaultresourceGroupName}``{fabricName}` parametreleri vardır. `{fabricName}`, "Azure" dır. Örneğimize göre, `{vaultName}` "Testkasası" ve `{vaultresourceGroupName}` "testVaultRG" dir. URI 'de tüm gerekli parametreler verildiğinden, ayrı bir istek gövdesi gerekmez.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses"></a>Responses

' Refresh ' işlemi [zaman uyumsuz bir işlemdir](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Bu işlemin Ayrıca izlenmesi gereken başka bir işlem oluşturduğu anlamına gelir.

Başka bir işlem oluşturulduğunda 202 (kabul edildi) ve bu işlem tamamlandığında 200 (Tamam) iki yanıt döndürür.

|Ad  |Tür  |Açıklama  |
|---------|---------|---------|
|204 Içerik yok     |         |  Hiçbir içerik döndürülmeden Tamam      |
|202 kabul edildi     |         |     Eden    |

##### <a name="example-responses"></a>Örnek yanıtlar

*Post* isteği gönderildikten sonra, 202 (kabul edildi) yanıtı döndürülür.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
X-Content-Type-Options: nosniff
x-ms-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-client-request-id: 4910609f-bb9b-4c23-8527-eb6fa2d3253f; 4910609f-bb9b-4c23-8527-eb6fa2d3253f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-routing-request-id: SOUTHINDIA:20180521T105701Z:43cf550d-e463-421c-8922-37e4766db27d
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:57:00 GMT
Location: https://management.azure.com/subscriptions//00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Basit bir *Get* komutuyla "konum" üst bilgisini kullanarak ortaya çıkan işlemi izleyin

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
```

Tüm Azure VM 'Leri keşfedildiğinde GET komutu bir 204 (Içerik yok) yanıtı döndürür. Kasa artık abonelik içindeki herhangi bir VM 'yi bulabilir.

```http
HTTP/1.1 204 NoContent
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-client-request-id: 25bb6345-f9fc-4406-be1a-dc6db0eefafe; 25bb6345-f9fc-4406-be1a-dc6db0eefafe
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14997
x-ms-correlation-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-routing-request-id: SOUTHINDIA:20180521T105825Z:cf6cd73b-9189-4942-a61d-878fcf76b1c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:58:25 GMT
X-Powered-By: ASP.NET
```

### <a name="selecting-the-relevant-azure-vm"></a>İlgili Azure VM 'yi seçme

 Abonelik kapsamındaki [Tüm korunabilir öğeleri listeleyerek](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list) "önbelleğe alma" işleminin yapıldığını ve yanıttaki istenen VM 'yi bulabileceğinizi doğrulayabilirsiniz. [Bu işlemin yanıtı,](#example-responses-1) kurtarma hizmetleri 'nin bir VM 'yi nasıl tanımladığına ilişkin bilgiler de sağlar.  Modele alışdıktan sonra, bu adımı atlayabilir ve [Korumayı etkinleştirme](#enabling-protection-for-the-azure-vm)işlemine doğrudan devam edebilirsiniz.

Bu işlem bir *Get* işlemidir.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

*Get* URI 'sinin tüm gerekli parametreleri vardır. Ek istek gövdesi gerekli değildir.

#### <a name="responses-1"></a>Lerinde

|Ad  |Tür  |Açıklama  |
|---------|---------|---------|
|200 TAMAM     | [Workloadkorunabilir Tableıtemresourcelist](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       Tamam |

#### <a name="example-responses-1"></a>Örnek yanıtlar

*Get* isteği gönderildikten sonra bir 200 (Tamam) yanıtı döndürülür.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-client-request-id: 40c8601a-c217-4c68-87da-01db8dac93dd; 40c8601a-c217-4c68-87da-01db8dac93dd
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14979
x-ms-correlation-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-routing-request-id: SOUTHINDIA:20180521T071408Z:7c2cf56a-e6be-4345-96df-c27ed849fe36
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:14:08 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG;testVM/protectableItems/vm;iaasvmcontainerv2;testRG;testVM",
      "name": "iaasvmcontainerv2;testRG;testVM",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
      "properties": {
        "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "virtualMachineVersion": "Compute",
        "resourceGroup": "testRG",
        "backupManagementType": "AzureIaasVM",
        "protectableItemType": "Microsoft.Compute/virtualMachines",
        "friendlyName": "testVM",
        "protectionState": "NotProtected"
      }
    },……………..

```

> [!TIP]
> Bir *Get* yanıtında değer sayısı, bir ' Page ' için 200 ile sınırlıdır. Sonraki yanıt kümesinin URL 'sini almak için ' nextLink ' alanını kullanın.

Yanıt, tüm korumasız Azure VM 'lerinin listesini içerir ve her `{value}`, Azure kurtarma hizmeti 'nin yedeklemeyi yapılandırmak için gereken tüm bilgileri içerir. Yedeklemeyi yapılandırmak için, `{properties}` bölümündeki `{name}` alanını ve `{virtualMachineId}` alanı ' na göz önünde edin. Aşağıda belirtildiği gibi bu alan değerlerinden iki değişken oluşturun.

- containerName = "ıaasvmcontainer;" +`{name}`
- Korunabilir = "VM;" + `{name}`
- `{virtualMachineId}`, [istek gövdesinde](#example-request-body) daha sonra kullanılır

Örnekte yukarıdaki değerler şu şekilde çeviri yapar:

- containerName = "ıaasvmcontainer; iaasvmcontainerv2; testRG; testVM"
- protectedItemName = "vm;iaasvmcontainerv2;testRG;testVM"

### <a name="enabling-protection-for-the-azure-vm"></a>Azure VM için korumayı etkinleştirme

İlgili VM "Önbelleğe alındı" ve "tanımlı" olduktan sonra, korunacak ilkeyi seçin. Kasadaki mevcut ilkeler hakkında daha fazla bilgi edinmek için [liste ILKESI API](https://docs.microsoft.com/rest/api/backup/backuppolicies/list)'sine bakın. Ardından ilke adına başvurarak [ilgili ilkeyi](https://docs.microsoft.com/rest/api/backup/protectionpolicies(2019-05-13)/get) seçin. İlke oluşturmak için [ilke oluşturma öğreticisi](backup-azure-arm-userestapi-createorupdatepolicy.md)' ne bakın. Aşağıdaki örnekte "DefaultPolicy" seçilidir.

Korumayı etkinleştirme, ' Protected Item ' oluşturan zaman uyumsuz bir *PUT* işlemidir.

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

`{containerName}` ve `{protectedItemName}` yukarıda oluşturulur. `{fabricName}`, "Azure" dır. Örneğimiz için şu şekilde çeviri yapar:

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>İstek gövdesini oluşturma

Korumalı bir öğe oluşturmak için, istek gövdesinin bileşenleri aşağıda verilmiştir.

|Ad  |Tür  |Açıklama  |
|---------|---------|---------|
|properties     | AzureIaaSVMProtectedItem        |Korunabilir kaynak özellikleri         |

İstek gövdesinin ve diğer ayrıntıların tanımlarının tüm listesi için, [korumalı öğe oluştur REST API belge](https://docs.microsoft.com/rest/api/backup/protecteditems/createorupdate#request-body)' ye başvurun.

##### <a name="example-request-body"></a>Örnek istek gövdesi

Aşağıdaki istek gövdesi, korumalı bir öğe oluşturmak için gereken özellikleri tanımlar.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

`{sourceResourceId}` [korunabilir öğelerin listesinin yanıtından](#example-responses-1)yukarıda bahsedilen `{virtualMachineId}`.

#### <a name="responses"></a>Responses

Korumalı bir öğenin oluşturulması [zaman uyumsuz bir işlemdir](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Bu işlemin Ayrıca izlenmesi gereken başka bir işlem oluşturduğu anlamına gelir.

Başka bir işlem oluşturulduğunda 202 (kabul edildi) ve bu işlem tamamlandığında 200 (Tamam) iki yanıt döndürür.

|Ad  |Tür  |Açıklama  |
|---------|---------|---------|
|200 TAMAM     |    [Korunabilir kaynak](https://docs.microsoft.com/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  Tamam       |
|202 kabul edildi     |         |     Eden    |

##### <a name="example-responses"></a>Örnek yanıtlar

Korumalı öğe oluşturma veya güncelleştirme için *PUT* isteğini gönderdikten sonra, ilk yanıt bir konum üst bilgisi veya Azure-Async-header ile 202 (kabul edilir) olur.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Ardından, bir basit *Get* komutuyla konum üstbilgisini veya Azure-AsyncOperation üst bilgisini kullanarak elde edilen işlemi izleyin.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

İşlem tamamlandıktan sonra, yanıt gövdesinde korunan öğe içeriğiyle 200 (Tamam) döndürür.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM",
  "name": "VM;testRG;testVM",
  "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems",
  "properties": {
    "friendlyName": "testVM",
    "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "protectionStatus": "Healthy",
    "protectionState": "IRPending",
    "healthStatus": "Passed",
    "lastBackupStatus": "",
    "lastBackupTime": "2001-01-01T00:00:00Z",
    "protectedItemDataId": "17592691116891",
    "extendedInfo": {
      "recoveryPointCount": 0,
      "policyInconsistent": false
    },
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "backupManagementType": "AzureIaasVM",
    "workloadType": "VM",
    "containerName": "iaasvmcontainerv2;testRG;testVM",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "policyName": "DefaultPolicy"
  }
}
```

Bu, korumanın VM için etkinleştirildiğini ve ilk yedeklemenin ilke zamanlaması uyarınca tetikleneceğini onaylar.

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>Korumalı bir Azure VM için isteğe bağlı yedekleme tetikleyin

Yedekleme için bir Azure VM yapılandırıldıktan sonra yedeklemeler, ilke zamanlaması uyarınca gerçekleşir. Zamanlanan ilk yedeklemeyi bekleyebilir veya dilediğiniz zaman bir isteğe bağlı yedekleme tetikleyebilirsiniz. İsteğe bağlı yedeklemeler için bekletme, yedekleme ilkesinin saklama alanından ayrıdır ve belirli bir tarih-saat için belirtilebilir. Belirtilmemişse, isteğe bağlı yedekleme tetikleyicisinin günüyle 30 gün olduğu varsayılır.

İsteğe bağlı yedekleme tetiklenmesi bir *Post* işlemidir.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

`{containerName}` ve `{protectedItemName}` [yukarıda](#responses-1)oluşturulur. `{fabricName}`, "Azure" dır. Örneğimiz için şu şekilde çeviri yapar:

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body"></a>İstek gövdesini oluşturma

İsteğe bağlı bir yedeklemeyi tetiklemek için, istek gövdesinin bileşenleri aşağıda verilmiştir.

|Ad  |Tür  |Açıklama  |
|---------|---------|---------|
|properties     | [Iaasvmbackuprequest](https://docs.microsoft.com/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |BackupRequestResource özellikleri         |

İstek gövdesinin ve diğer ayrıntıların tanımlarının tamamı listesi için bkz. [korumalı öğeler için tetikleyici yedeklemeleri REST API belgesi](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body).

#### <a name="example-request-body"></a>Örnek istek gövdesi

Aşağıdaki istek gövdesi, korumalı bir öğe için bir yedeklemeyi tetiklemek için gereken özellikleri tanımlar. Bekletme belirtilmemişse, yedekleme işinin tetiklenme zamanından itibaren 30 gün boyunca tutulacaktır.

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses"></a>Responses

İsteğe bağlı yedekleme tetiklenmesi [zaman uyumsuz bir işlemdir](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Bu işlemin Ayrıca izlenmesi gereken başka bir işlem oluşturduğu anlamına gelir.

Başka bir işlem oluşturulduğunda 202 (kabul edildi) ve bu işlem tamamlandığında 200 (Tamam) iki yanıt döndürür.

|Ad  |Tür  |Açıklama  |
|---------|---------|---------|
|202 kabul edildi     |         |     Eden    |

#### <a name="example-responses-3"></a>Örnek yanıtlar

İstek üzerine yedekleme için *Post* isteğini gönderdikten sonra, ilk yanıt bir konum üst bilgisi veya Azure-Async-header ile 202 (kabul edilir) olur.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-client-request-id: 7df8e874-1d66-4f81-8e91-da2fe054811d; 7df8e874-1d66-4f81-8e91-da2fe054811d
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-routing-request-id: SOUTHINDIA:20180521T083541Z:7885ca75-c7c6-43fb-a38c-c0cc437d8810
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:35:41 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationResults/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Ardından, bir basit *Get* komutuyla konum üstbilgisini veya Azure-AsyncOperation üst bilgisini kullanarak elde edilen işlemi izleyin.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

İşlem tamamlandıktan sonra, yanıt gövdesinde elde edilen yedekleme işinin KIMLIĞI ile 200 (Tamam) döndürür.

```json
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-client-request-id: 5a63209d-3708-4e69-a75f-9499f4c8977c; 5a63209d-3708-4e69-a75f-9499f4c8977c
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14995
x-ms-correlation-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-routing-request-id: SOUTHINDIA:20180521T083723Z:a8b13524-2c95-445f-b107-920806f385c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:37:22 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "00000000-0000-0000-0000-000000000000",
  "status": "Succeeded",
  "startTime": "2018-05-21T08:35:40.9488967Z",
  "endTime": "2018-05-21T08:35:40.9488967Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "7ddead57-bcb9-4269-ac31-6a1b57588700"
  }
}
```

Yedekleme işi uzun süredir çalışan bir işlem olduğundan, [REST API belge kullanan izleme işlerinde](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)açıklandığı şekilde izlenmesi gerekir.

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>Korumalı bir Azure VM için yedekleme yapılandırmasını değiştirme

### <a name="changing-the-policy-of-protection"></a>Koruma ilkesini değiştirme

VM 'nin koruduğu ilkeyi değiştirmek için, [Korumayı etkinleştirme](#enabling-protection-for-the-azure-vm)ile aynı biçimi kullanabilirsiniz. [İstek gövdesinde](#example-request-body) yenı ilke kimliğini sağlamanız ve isteği göndermesi yeterlidir. Örneğin: testVM ilkesini ' DefaultPolicy ' iken ' ProdPolicy ' olarak değiştirmek Için, istek gövdesinde ' ProdPolicy ' KIMLIĞINI sağlayın.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

Yanıt, [korumayı etkinleştirmek için](#responses-2) belirtilen biçimde olacaktır

### <a name="stop-protection-but-retain-existing-data"></a>Korumayı durdurun, ancak mevcut verileri koruyun

Korunan bir sanal makinede korumayı kaldırmak, ancak zaten yedeklenen verileri korumak için, istek gövdesinde ilkeyi kaldırın ve isteği iletin. İlkeyle ilişkilendirme kaldırıldıktan sonra yedeklemeler artık tetiklenmez ve yeni kurtarma noktası oluşturulmaz.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

Yanıt, [isteğe bağlı bir yedeklemenin tetiklenmesi için](#example-responses-3)belirtilen biçimde olacaktır. Sonuç iş, [REST API belge kullanan izleme işlerinde](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)açıklandığı şekilde izlenmelidir.

### <a name="stop-protection-and-delete-data"></a>Korumayı durdurma ve verileri silme

Korunan bir sanal makinenin korumasını kaldırmak ve yedekleme verilerini silmek için, [burada](https://docs.microsoft.com/rest/api/backup/protecteditems/delete)ayrıntılı olarak bir silme işlemi gerçekleştirin.

Korumayı durdurma ve verileri silme işlemi *silme* işlemidir.

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

`{containerName}` ve `{protectedItemName}` [yukarıda](#responses-1)oluşturulur. `{fabricName}` "Azure". Örneğimiz için şu şekilde çeviri yapar:

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

### <a name="responses-2"></a>Lerinde

Korumayı *silme* [işlemi zaman uyumsuz bir işlemdir](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Bu işlemin Ayrıca izlenmesi gereken başka bir işlem oluşturduğu anlamına gelir.

Bu, başka bir işlem oluşturulduğunda 202 (kabul edildi) ve bu işlem tamamlandığında 204 (NoContent) olarak iki yanıt döndürür.

|Ad  |Tür  |Açıklama  |
|---------|---------|---------|
|204 NoContent     |         |  NoContent       |
|202 kabul edildi     |         |     Eden    |

## <a name="next-steps"></a>Sonraki adımlar

[Verileri bir Azure sanal makine yedeğinden geri yükleyin](backup-azure-arm-userestapi-restoreazurevms.md).

Azure Backup REST API 'Leri hakkında daha fazla bilgi için aşağıdaki belgelere bakın:

- [Azure kurtarma hizmetleri sağlayıcısı REST API](/rest/api/recoveryservices/)
- [Azure REST API’yi kullanmaya başlayın](/rest/api/azure/)
