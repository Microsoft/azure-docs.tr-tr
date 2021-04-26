---
title: Azure sanal makine ölçek kümesi örnekleri için bildirimi sonlandırma
description: Azure sanal makine ölçek kümesi örnekleri için sonlandırma bildirimini nasıl etkinleştireceğinizi öğrenin
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: terminate-notification
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: ed042afbcbb67a88e304c92302b14af56b26c8e1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933415"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances"></a>Azure sanal makine ölçek kümesi örnekleri için bildirimi sonlandırma
Ölçek kümesi örnekleri, örnek sonlandırma bildirimleri almak için kabul edebilir ve Sonlandırma işlemine önceden tanımlanmış bir gecikme zaman aşımı kümesi ayarlayabilir. Sonlandırma bildirimi, yeniden başlatmalar ve yeniden dağıtım gibi kesin işlemleri bildirimleri ve ertelerini sağlayan Azure Metadata Service – [zamanlanan olaylar](../virtual-machines/windows/scheduled-events.md)aracılığıyla gönderilir. Çözüm başka bir olay – Terminate – Zamanlanan Olaylar listesine ekler ve Terminate olayının ilişkili gecikmesi, kullanıcılar tarafından ölçek kümesi modeli yapılandırmalarında belirtilen gecikme sınırına bağlıdır.

Özelliğe kaydolduktan sonra, ölçek kümesi örneklerinin, örnek silinmeden önce belirtilen zaman aşımı süresinin dolmasını beklemeniz gerekmez. Sonlandırma bildirimi aldıktan sonra, sonlandırma zaman aşımı süresi dolmadan, örnek herhangi bir zamanda silinebilir.

## <a name="enable-terminate-notifications"></a>Sonlandırma bildirimlerini etkinleştir
Aşağıdaki örneklerde açıklandığı şekilde ölçek kümesi örneklerinize sonlandırma bildirimleri etkinleştirmenin birden çok yolu vardır.

### <a name="azure-portal"></a>Azure portalı

Aşağıdaki adımlar yeni bir ölçek kümesi oluştururken sonlandırma bildirimini etkinleştirir. 

1. **Sanal makine ölçek kümelerine** gidin.
1. Yeni bir ölçek kümesi oluşturmak için **+ Ekle** ' yi seçin.
1. **Yönetim** sekmesine gidin. 
1. **Örnek sonlandırma** bölümünü bulun.
1. **Örnek sonlandırma bildirimi** için **Açık**' ı seçin.
1. **Sonlandırma gecikmesi (dakika)** için, istenen varsayılan zaman aşımını ayarlayın.
1. Yeni ölçek kümesini oluşturmayı tamamladığınızda, **gözden geçir + oluştur** düğmesini seçin. 

> [!NOTE]
> Azure portal var olan ölçek kümelerinde sonlandırma bildirimleri ayarlayamayacak

### <a name="rest-api"></a>REST API

Aşağıdaki örnek, ölçek kümesi modelinde sonlandırma bildirimini sunar.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "virtualMachineProfile": {
            "scheduledEventsProfile": {
                "terminateNotificationProfile": {
                    "notBeforeTimeout":"PT5M",
                    "enable":true
                }
            }
        }
    }        
}

```

Yukarıdaki blok, ölçek kümesindeki tüm örneklerde herhangi bir sonlandırma işlemi için 5 dakikadan ( *PT5M* tarafından belirtildiği gibi) bir zaman aşımı gecikmesi belirtir. *Notbeforetimeout* alanı ISO 8601 biçiminde 5 ile 15 dakika arasında herhangi bir değer alabilir. Sonlandırma işlemi için varsayılan zaman aşımını, yukarıda açıklanan *Sonlandırmaenocertificate* altında bulunan *notbeforetimeout* özelliğini değiştirerek değiştirebilirsiniz.

Ölçek kümesi modelinde *scheduledEventsProfile* etkinleştirildikten ve *notbeforetimeout* ayarını yaptıktan sonra, değişiklikleri yansıtmak için tek tek örnekleri [en son modele](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) güncelleştirin.

> [!NOTE]
>Ölçek kümesi örneklerinde sonlandırma bildirimleri yalnızca API sürüm 2019-03-01 ve üstü ile etkinleştirilebilir

### <a name="azure-powershell"></a>Azure PowerShell
Yeni bir ölçek kümesi oluştururken, [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) cmdlet 'ini kullanarak ölçek kümesinde sonlandırma bildirimlerini etkinleştirebilirsiniz.

Bu örnek betik, yapılandırma dosyasını kullanarak bir ölçek kümesinin ve ilişkili kaynakların oluşturulmasını adım adım göstermektedir: [tamamen sanal makine ölçek kümesi oluşturma](./scripts/powershell-sample-create-complete-scale-set.md). Ölçek kümesi oluşturmak için *Terminatescheduledevents* ve *TerminateScheduledEventNotBeforeTimeoutInMinutes* parametrelerini yapılandırma nesnesine ekleyerek sonlandırma bildirimini yapılandırma sağlayabilirsiniz. Aşağıdaki örnek, özelliği 10 dakikalık bir gecikme zaman aşımı ile birlikte sunar.

```azurepowershell-interactive
New-AzVmssConfig `
  -Location "VMSSLocation" `
  -SkuCapacity 2 `
  -SkuName "Standard_DS2" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 10
```

Var olan bir ölçek kümesinde sonlandırma bildirimlerini etkinleştirmek için [Update-AzVmss](/powershell/module/az.compute/update-azvmss) cmdlet 'ini kullanın.

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
Yukarıdaki örnek, mevcut bir ölçek kümesinde bildirimleri sonlandırmayı ve terminate olayı için 15 dakikalık bir zaman aşımını belirler.

Ölçek kümesi modelinde zamanlanmış olayları etkinleştirdikten ve zaman aşımını ayarlamadıktan sonra, değişiklikleri yansıtmak için tek tek örnekleri [en son modele](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) güncelleştirin.

### <a name="azure-cli-20"></a>Azure CLI 2.0

Aşağıdaki örnek, yeni bir ölçek kümesi oluştururken sonlandırma bildirimini etkinleştirmek içindir.

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --terminate-notification-time 10
```

Yukarıdaki örnek ilk olarak bir kaynak grubu oluşturur ve 10 dakikalık varsayılan zaman aşımı için sonlandırma bildirimleri etkin olan yeni bir ölçek kümesi oluşturur.

Aşağıdaki örnek, var olan bir ölçek kümesinde sonlandırma bildirimini etkinleştirmek içindir.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-terminate-notification true \
  --terminate-notification-time 10
```

## <a name="get-terminate-notifications"></a>Sonlandırma bildirimleri al

Sonlandırma bildirimleri, Azure Metadata Service olan [zamanlanan olaylar](../virtual-machines/windows/scheduled-events.md)aracılığıyla dağıtılır. Azure meta veri hizmeti, sanal makineleri VM içinden erişilebilen bir REST uç noktası kullanarak çalıştırmaya yönelik bilgiler sunar. Bilgiler, sanal makinenin dışında kullanıma sunulmaması için yönlendirilemeyen bir IP aracılığıyla kullanılabilir.

Zamanlanan Olaylar, ilk kez olay isteği yaptığınızda ölçek kümesi için etkinleştirilir. En fazla iki dakikalık ilk çağrındaki Gecikmeli yanıt alabilirsiniz. Yaklaşan bakım olaylarını ve devam eden bakım etkinliklerinin durumunu algılamak için uç noktayı düzenli aralıklarla sorgulayın.

Ölçek kümesi örnekleri 24 saat boyunca bir istek yapmıyorsanız ölçek kümesi için Zamanlanan Olaylar devre dışıdır.

### <a name="endpoint-discovery"></a>Uç nokta bulma
VNET etkin VM 'Ler için, Metadata Service statik bir yönlendirilemeyen IP, 169.254.169.254 tarafından kullanılabilir.

Zamanlanan Olaylar en son sürümü için tam uç nokta şunlardır:
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="query-response"></a>Sorgu yanıtı
Bir yanıt, Zamanlanmış olaylar dizisi içerir. Boş bir dizi, şu anda zamanlanan hiçbir olay olmadığı anlamına gelir.

Zamanlanan olayların olduğu durumlarda, yanıt bir olay dizisi içerir. "Sonlandır" olayı için yanıt şöyle görünür:
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
*Documentınnation* bir ETag ' dir ve olayların yükünün son sorgudan bu yana değişip değişmediğini denetlemek için kolay bir yol sağlar.

Yukarıdaki alanların her biri hakkında daha fazla bilgi için bkz. [Windows](../virtual-machines/windows/scheduled-events.md#event-properties) ve [Linux](../virtual-machines/linux/scheduled-events.md#event-properties)için zamanlanan olaylar belgeleri.

### <a name="respond-to-events"></a>Olaylara yanıt verme
Yaklaşan bir olayın öğrenmesini ve daha iyi kapanma için mantığınızı tamamladıktan sonra, EventID ile meta veri hizmetine bir POST çağrısı yaparak bekleyen olayı onaylayabilirsiniz. POST çağrısı, Azure 'a VM silme işlemine devam edebileceğini belirtir.

POST isteği gövdesinde beklenen JSON aşağıdadır. İstek bir StartRequests listesi içermelidir. Her StartRequest, hızlandırmak istediğiniz olay için EventID içerir:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

Ölçek kümesindeki her VM 'nin yalnızca o VM için ilgili EventID 'yi onaylamasından emin olun. VM, [örnek meta verileri aracılığıyla](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name)kendi VM adını alabilir. Bu ad "{Scale-set-name} _ {Instance-id}" formunu alır ve yukarıda açıklanan sorgu yanıtının ' Resources ' bölümünde görüntülenir.

Ayrıca, [Python](../virtual-machines/linux/scheduled-events.md#python-sample)olaylarını sorgulamak ve yanıtlamak için örnekler betiklerine başvurabilirsiniz.

## <a name="tips-and-best-practices"></a>İpuçları ve en iyi yöntemler
-   Yalnızca ' silme ' işlemlerinde bildirimleri Sonlandır – ölçek kümesinde *scheduledEventsProfile* etkinse, tüm silme işlemleri (el ile silme veya otomatik ölçeklendirme ile başlatılan ölçek), sonlandırma olayları oluşturur. Yeniden başlatma, yeniden görüntü, yeniden dağıtma ve durdurma/serbest bırakma gibi diğer işlemler sonlandırma olayları oluşturmaz. Düşük öncelikli VM 'Ler için sonlandırma bildirimleri etkinleştirilemez.
-   Zaman aşımı için zorunlu bekleme yok – sonlandırma işlemini, olay alındıktan sonra ve olayın *NotBefore* süre dolmadan önce herhangi bir zamanda başlatabilirsiniz.
-   Zaman aşımında zorunlu silme: bir olay oluşturulduktan sonra zaman aşımı değerini genişletme yeteneği yoktur. Zaman aşımı süresi dolduğunda, bekleyen sonlandırma olayı işlenir ve VM silinir.
-   Değiştirilebilir zaman aşımı değeri – bir örnek silinmeden önce, ölçek kümesi modelinde *Notbeforetimeout* özelliğini değiştirerek ve sanal makine örneklerini en son modele güncelleştirerek zaman aşımı değerini değiştirebilirsiniz.
-   Tüm bekleyen silmeleri Onayla – onaylanmamış VM_1 üzerinde bekleyen bir silme işlemi varsa ve VM_2 başka bir sonlandırma olayını onayladıysanız, VM_1 için sonlandırma olayı onaylanana veya zaman aşımı süresi geçene kadar VM_2 silinmez. VM_1 için sonlandırma olayını onayladıktan sonra, VM_1 ve VM_2 her ikisi de silinir.
-   Tüm eşzamanlı silmeleri Onayla – yukarıdaki örneği genişleterek VM_1 ve VM_2 aynı *NotBefore* zamanına sahip olursa, sonlandırma olaylarının her ikisi de onaylanmalı veya zaman aşımı süresi dolmadan önce VM silinmemelidir.

## <a name="troubleshoot"></a>Sorun giderme
### <a name="failure-to-enable-scheduledeventsprofile"></a>ScheduledEventsProfile etkinleştirme hatası
"' VirtualMachineProfile ' türündeki nesnede ' scheduledEventsProfile ' üyesi bulunamadı" hatasını bildiren bir hata iletisiyle ' Rozrequest ' hatası alırsanız, ölçek kümesi işlemleri için kullanılan API sürümünü denetleyin. İşlem API 'SI sürüm **2019-03-01** veya üzeri gereklidir. 

### <a name="failure-to-get-terminate-events"></a>Sonlandırma olayları alınamadı
Zamanlanan Olaylar aracılığıyla herhangi bir **sonlandırma** olayı almıyorsanız olayları almak IÇIN kullanılan API sürümünü kontrol edin. Sonlandırma olayları için Metadata Service API sürümü **2019-01-01** veya üzeri gereklidir.
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>Hatalı NotBefore ile sonlandırma olayı alma  
Ölçek kümesi modelinde *scheduledEventsProfile* etkinleştirildikten ve *notbeforetimeout* ayarını yaptıktan sonra, değişiklikleri yansıtmak için tek tek örnekleri [en son modele](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) güncelleştirin.

## <a name="next-steps"></a>Sonraki adımlar
Uygulamanızı sanal makine ölçek kümelerinde [dağıtmayı](virtual-machine-scale-sets-deploy-app.md) öğrenin.
