---
title: Azure sanal makine ölçek kümesi örnekleri için sonlandırma bildirimi | Microsoft Docs
description: Azure sanal makine ölçek kümesi örnekleri için sonlandırma bildirimini nasıl etkinleştireceğinizi öğrenin
services: virtual-machine-scale-sets
documentationcenter: ''
author: shandilvarun
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/27/2019
ms.author: vashan
ms.openlocfilehash: 7269c76236b7cbe60995d84e85857da596bec961
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264670"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances-preview"></a>Azure sanal makine ölçek kümesi örnekleri (Önizleme) için sonlandırma bildirimi
Ölçek kümesi örnekleri, örnek sonlandırma bildirimleri almak için kabul edebilir ve Sonlandırma işlemine önceden tanımlanmış bir gecikme zaman aşımı kümesi ayarlayabilir. Sonlandırma bildirimi, yeniden başlatmalar ve yeniden dağıtım gibi kesin işlemleri bildirimleri ve ertelerini sağlayan Azure Metadata Service – [zamanlanan olaylar](../virtual-machines/windows/scheduled-events.md)aracılığıyla gönderilir. Önizleme çözümü başka bir olay – Terminate – Zamanlanan Olaylar listesine ekler ve Terminate olayının ilişkili gecikmesi, kullanıcılar tarafından ölçek kümesi modeli yapılandırmalarında belirtilen gecikme sınırına bağlıdır.

Özelliğe kaydolduktan sonra, ölçek kümesi örneklerinin, örnek silinmeden önce belirtilen zaman aşımı süresinin dolmasını beklemeniz gerekmez. Sonlandırma bildirimi aldıktan sonra, sonlandırma zaman aşımı süresi dolmadan, örnek herhangi bir zamanda silinebilir.

> [!IMPORTANT]
> Ölçek kümesi örnekleri için sonlandırma bildirimi şu anda genel önizlemededir. Aşağıda açıklanan genel önizleme işlevlerini kullanmak için katılım prosedürü gerekmez.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-terminate-notifications"></a>Sonlandırma bildirimlerini etkinleştir
Aşağıdaki örneklerde açıklandığı şekilde ölçek kümesi örneklerinize sonlandırma bildirimleri etkinleştirmenin birden çok yolu vardır.

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

Yukarıdaki blok, ölçek kümesindeki tüm örneklerde herhangi bir sonlandırma işlemi için 5 dakikadan ( *PT5M*tarafından belirtildiği gibi) bir zaman aşımı gecikmesi belirtir. *Notbeforetimeout* alanı ISO 8601 biçiminde 5 ile 15 dakika arasında herhangi bir değer alabilir. Sonlandırma işlemi için varsayılan zaman aşımını, yukarıda açıklanan *Sonlandırmaenocertificate* altında bulunan *notbeforetimeout* özelliğini değiştirerek değiştirebilirsiniz.

Ölçek kümesi modelinde *scheduledEventsProfile* etkinleştirildikten ve *notbeforetimeout*ayarını yaptıktan sonra, değişiklikleri yansıtmak için tek tek örnekleri [en son modele](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) güncelleştirin.

> [!NOTE]
>Ölçek kümesi örneklerinde sonlandırma bildirimleri yalnızca API sürüm 2019-03-01 ve üstü ile etkinleştirilebilir

### <a name="azure-powershell"></a>Azure PowerShell
Yeni bir ölçek kümesi oluştururken, [New-AzVmssVM](/powershell/module/az.compute/new-azvmss) cmdlet 'ini kullanarak ölçek kümesinde sonlandırma bildirimlerini etkinleştirebilirsiniz.

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents
```

Yukarıdaki örnekte, 5 dakikalık varsayılan zaman aşımı ile sonlandırma bildirimleri etkin olan yeni bir ölçek kümesi oluşturulur. Yeni bir ölçek kümesi oluştururken, *Terminatescheduledevents* parametresi bir değer gerektirmez. Zaman aşımı değerini değiştirmek için, *TerminateScheduledEventNotBeforeTimeoutInMinutes* parametresi aracılığıyla istenen zaman aşımını belirtin.

Mevcut bir ölçek kümesinde sonlandırma bildirimlerini etkinleştirmek için [Update-AzVmssVM](/powershell/module/az.compute/update-azvmss) cmdlet 'ini kullanın.

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
Yukarıdaki örnek, mevcut bir ölçek kümesinde bildirimleri sonlandırmayı ve terminate olayı için 15 dakikalık bir zaman aşımını belirler.

Ölçek kümesi modelinde zamanlanmış olayları etkinleştirdikten ve zaman aşımını ayarlamadıktan sonra, değişiklikleri yansıtmak için tek tek örnekleri [en son modele](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) güncelleştirin.

## <a name="get-terminate-notifications"></a>Sonlandırma bildirimleri al

Sonlandırma bildirimleri, Azure Metadata Service olan [zamanlanan olaylar](../virtual-machines/windows/scheduled-events.md)aracılığıyla dağıtılır. Azure meta veri hizmeti, sanal makineleri VM içinden erişilebilen bir REST uç noktası kullanarak çalıştırmaya yönelik bilgiler sunar. Bilgiler, sanal makinenin dışında kullanıma sunulmaması için yönlendirilemeyen bir IP aracılığıyla kullanılabilir.

Zamanlanan Olaylar, ilk kez olay isteği yaptığınızda ölçek kümesi için etkinleştirilir. En fazla iki dakikalık ilk çağrındaki Gecikmeli yanıt alabilirsiniz. Yaklaşan bakım olaylarını ve devam eden bakım etkinliklerinin durumunu algılamak için uç noktayı düzenli aralıklarla sorgulayın.

Ölçek kümesi örnekleri 24 saat boyunca bir istek yapmıyorsanız ölçek kümesi için Zamanlanan Olaylar devre dışıdır.

### <a name="endpoint-discovery"></a>Uç nokta bulma
VNET etkin VM 'Ler için, Metadata Service statik bir yönlendirilemeyen IP, 169.254.169.254 tarafından kullanılabilir.

Bu önizleme için en son Zamanlanan Olaylar sürümü için tam uç nokta şunlardır:
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01 '

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
Documentınnation bir ETag ' dir ve olayların yükünün son sorgudan bu yana değişip değişmediğini denetlemek için kolay bir yol sağlar.

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

Ayrıca, [PowerShell](../virtual-machines/windows/scheduled-events.md#powershell-sample) ve [Python](../virtual-machines/linux/scheduled-events.md#python-sample)kullanarak olaylara sorgulama ve yanıt verme için örnekler betiklerine başvurabilirsiniz.

## <a name="tips-and-best-practices"></a>İpuçları ve en iyi uygulamalar
-   Yalnızca ' silme ' işlemlerinde bildirimleri Sonlandır – ölçek kümesinde *scheduledEventsProfile* etkinse, tüm silme işlemleri (el ile silme veya otomatik ölçeklendirme ile başlatılan ölçek), sonlandırma olayları oluşturur. Yeniden başlatma, yeniden görüntü, yeniden dağıtma ve durdurma/serbest bırakma gibi diğer işlemler sonlandırma olayları oluşturmaz. Düşük öncelikli VM 'Ler için sonlandırma bildirimleri etkinleştirilemez.
-   Zaman aşımı için zorunlu bekleme yok – sonlandırma işlemini, olay alındıktan sonra ve olayın *NotBefore* süre dolmadan önce herhangi bir zamanda başlatabilirsiniz.
-   Zaman aşımında zorunlu silme – önizleme, bir olay oluşturulduktan sonra zaman aşımı değerini genişletme yeteneği sağlamaz. Zaman aşımı süresi dolduğunda, bekleyen sonlandırma olayı işlenir ve VM silinir.
-   Değiştirilebilir zaman aşımı değeri – bir örnek silinmeden önce, ölçek kümesi modelinde *Notbeforetimeout* özelliğini değiştirerek ve sanal makine örneklerini en son modele güncelleştirerek zaman aşımı değerini değiştirebilirsiniz.
-   Tüm bekleyen silmeleri Onayla – VM_1 üzerinde bir bekleyen silme işlemi varsa ve VM_2 üzerinde başka bir Terminate olayını onayladıysanız, VM_1 için terminate olayı onaylanana veya zaman aşımı süresi geçene kadar VM_2 silinmez. VM_1 için sonlandırma olayını onayladıktan sonra, hem VM_1 hem de VM_2 silinir.
-   Tüm eşzamanlı silmeleri Onayla – yukarıdaki örneği genişleterek, VM_1 ve VM_2 aynı *NotBefore* saatine sahipseniz, her iki sonlandırma olayı da onaylanmalı veya zaman aşımı süresi dolmadan önce VM silinmemelidir.

## <a name="troubleshoot"></a>Sorun giderme
### <a name="failure-to-enable-scheduledeventsprofile"></a>ScheduledEventsProfile etkinleştirme hatası
"' VirtualMachineProfile ' türündeki nesnede ' scheduledEventsProfile ' üyesi bulunamadı" hatasını bildiren bir hata iletisiyle ' Rozrequest ' hatası alırsanız, ölçek kümesi işlemleri için kullanılan API sürümünü denetleyin. Bu önizleme için işlem API 'SI sürüm **2019-03-01** veya üzeri gereklidir.

### <a name="failure-to-get-terminate-events"></a>Sonlandırma olayları alınamadı
Zamanlanan Olaylar aracılığıyla herhangi bir **sonlandırma** olayı almıyorsanız olayları almak IÇIN kullanılan API sürümünü kontrol edin. Sonlandırma olayları için Metadata Service API sürümü **2019-01-01** veya üzeri gereklidir.
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01 '

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>Hatalı NotBefore ile sonlandırma olayı alma  
Ölçek kümesi modelinde *scheduledEventsProfile* etkinleştirildikten ve *notbeforetimeout*ayarını yaptıktan sonra, değişiklikleri yansıtmak için tek tek örnekleri [en son modele](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) güncelleştirin.

## <a name="next-steps"></a>Sonraki adımlar
Uygulamanızı sanal makine ölçek kümelerinde [dağıtmayı](virtual-machine-scale-sets-deploy-app.md) öğrenin.
