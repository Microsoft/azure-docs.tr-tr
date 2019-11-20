---
title: Azure Batch havuzu oluşturma olayı | Microsoft Docs
description: Batch havuzu oluşturma olayı için başvuru.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 3f7b20cb915e0a969c6a400c07a094943f7f3a1b
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258437"
---
# <a name="pool-create-event"></a>Havuz oluşturma olayı

 Bu olay, bir havuz oluşturulduktan sonra yayınlanır. Günlüğün içeriği, havuz hakkındaki genel bilgileri açığa çıkarır. Havuzun hedef boyutu 0 işlem düğümünden fazlaysa, bir havuz yeniden boyutlandırma başlangıç olayı bu olaydan hemen sonra izlenecek şekilde değişir.

 Aşağıdaki örnek, `CloudServiceConfiguration` özelliği kullanılarak oluşturulan bir havuz için havuz oluşturma olayının gövdesini gösterir.

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|Öğe|Type|Notlar|
|-------------|----------|-----------|
|`id`|Dize|Havuzun KIMLIĞI.|
|`displayName`|Dize|Havuzun görünen adı.|
|`vmSize`|Dize|Havuzdaki sanal makinelerin boyutu. Bir havuzdaki tüm sanal makineler aynı boyutta. <br/><br/> Cloud Services havuzlara yönelik sanal makinelerin kullanılabilir boyutları (cloudServiceConfiguration ile oluşturulan havuzlar) hakkında daha fazla bilgi için bkz. [Cloud Services boyutları](https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/). Batch, hariç `ExtraSmall`tüm Cloud Services VM boyutlarını destekler.<br/><br/> Sanal makineler Market 'teki görüntüleri kullanan Havuzların kullanılabilir VM boyutları hakkında bilgi için (virtualMachineConfiguration ile oluşturulan havuzlar [) bkz. sanal makinelerin (](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) Linux) veya [boyutlarının](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) (Windows) boyutları. Batch `STANDARD_A0` ve premium depolama alanına sahip olanlar (`STANDARD_GS`, `STANDARD_DS` ve `STANDARD_DSV2` serisi) dışında tüm Azure sanal makinelerini destekler.|
|`imageType`|Dize|Görüntü için dağıtım yöntemi. Desteklenen değerler `virtualMachineConfiguration` veya`cloudServiceConfiguration`|
|[`cloudServiceConfiguration`](#bk_csconf)|Karmaşık Tür|Havuzun bulut hizmeti yapılandırması.|
|[`virtualMachineConfiguration`](#bk_vmconf)|Karmaşık Tür|Havuzun sanal makine yapılandırması.|
|[`networkConfiguration`](#bk_netconf)|Karmaşık Tür|Havuzun ağ yapılandırması.|
|`resizeTimeout`|Time|Havuzdaki son yeniden boyutlandırma işlemi için belirtilen havuza işlem düğümlerinin ayrılması için zaman aşımı.  (Havuzun oluşturulduğu ilk boyutlandırma, yeniden boyutlandırma olarak sayılır.)|
|`targetDedicatedNodes`|Int32|Havuz için istenen ayrılmış işlem düğümlerinin sayısı.|
|`targetLowPriorityNodes`|Int32|Havuz için istenen düşük öncelikli işlem düğümlerinin sayısı.|
|`enableAutoScale`|Bool|Havuz boyutunun zaman içinde otomatik olarak görüntülenip görüntülenmeyeceğini belirtir.|
|`enableInterNodeCommunication`|Bool|Havuzun düğümler arasında doğrudan iletişim için ayarlanmış olup olmadığını belirtir.|
|`isAutoPool`|Bool|Havuzun bir işin oto havuzu mekanizması aracılığıyla oluşturulup oluşturulmayacağını belirtir.|
|`maxTasksPerNode`|Int32|Havuzdaki tek bir işlem düğümünde eşzamanlı olarak çalışabilecek en fazla görev sayısı.|
|`vmFillType`|Dize|Batch hizmetinin görevleri havuzdaki işlem düğümleri arasında nasıl dağıttığını tanımlar. Geçerli değerler, yayma veya Paketdir.|

###  <a name="bk_csconf"></a>cloudServiceConfiguration

|Öğe adı|Type|Notlar|
|------------------|----------|-----------|
|`osFamily`|Dize|Havuzdaki sanal makinelere yüklenecek Azure Konuk işletim sistemi ailesi.<br /><br /> Olası değerler şunlardır:<br /><br /> **2** – Işletim sistemi ailesi 2, Windows Server 2008 R2 SP1 ile eşdeğerdir.<br /><br /> **3** – OS ailesi 3, Windows Server 2012 ile eşdeğerdir.<br /><br /> **4** – Işletim sistemi ailesi 4, Windows Server 2012 R2 ile eşdeğerdir.<br /><br /> Daha fazla bilgi için bkz. [Azure Konuk işletim sistemi sürümleri](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|
|`targetOSVersion`|Dize|Havuzdaki sanal makinelere yüklenecek Azure Konuk işletim sistemi sürümü.<br /><br /> Varsayılan değer **\*** , belirtilen aile için en son işletim sistemi sürümünü belirtir.<br /><br /> İzin verilen diğer değerler için bkz. [Azure Konuk işletim sistemi sürümleri](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|

###  <a name="bk_vmconf"></a>virtualMachineConfiguration

|Öğe adı|Type|Notlar|
|------------------|----------|-----------|
|[`imageReference`](#bk_imgref)|Karmaşık Tür|Kullanılacak platform veya Market görüntüsü hakkındaki bilgileri belirtir.|
|`nodeAgentId`|Dize|İşlem düğümünde sağlanan Batch düğüm aracısının SKU 'SU.|
|[`windowsConfiguration`](#bk_winconf)|Karmaşık Tür|Sanal makinede Windows işletim sistemi ayarlarını belirtir. ImageReference bir Linux işletim sistemi görüntüsüne başvuruyorsa bu özellik belirtilmemelidir.|

###  <a name="bk_imgref"></a>ImageReference

|Öğe adı|Type|Notlar|
|------------------|----------|-----------|
|`publisher`|Dize|Görüntünün yayımcısı.|
|`offer`|Dize|Görüntü teklifi.|
|`sku`|Dize|Görüntünün SKU 'SU.|
|`version`|Dize|Görüntünün sürümü.|

###  <a name="bk_winconf"></a>windowsConfiguration

|Öğe adı|Type|Notlar|
|------------------|----------|-----------|
|`enableAutomaticUpdates`|Boole değeri|Sanal makinenin otomatik güncelleştirmeler için etkinleştirilip etkinleştirilmediğini belirtir. Bu özellik belirtilmezse, varsayılan değer true 'dur.|

###  <a name="bk_netconf"></a>networkConfiguration

|Öğe adı|Type|Notlar|
|------------------|--------------|----------|
|`subnetId`|Dize|Havuzun işlem düğümlerinin oluşturulduğu alt ağın kaynak tanımlayıcısını belirtir.|
