---
title: Azure 'da Windows VM 'Leri için Zamanlanan Olaylar
description: Windows sanal makinelerinizdeki için Azure meta veri hizmetini kullanan Zamanlanmış olaylar.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: gwallace
editor: ''
tags: ''
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: 7889ee66ec80ee0b77b92efc5755e1a84a5cbf04
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073277"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Azure Metadata Service: Windows VM 'Leri için Zamanlanan Olaylar

Zamanlanan Olaylar, sanal makine bakımına hazırlanmak için uygulamanızın süresini sağlayan bir Azure Metadata Service. Bu, yaklaşan bakım olayları (örneğin, yeniden başlatma) hakkında bilgi sağlar ve böylece uygulamanızın onlara hazırlanabilmesi ve kesintiye uğramasını sınırlayabilirsiniz. Hem Windows hem de Linux 'ta PaaS ve IaaS dahil tüm Azure sanal makine türlerinde kullanılabilir. 

Linux üzerinde Zamanlanan Olaylar hakkında daha fazla bilgi için bkz. [Linux VM 'leri için zamanlanan olaylar](../linux/scheduled-events.md).

> [!Note] 
> Zamanlanan Olaylar, tüm Azure bölgelerinde genel kullanıma sunulmuştur. En son sürüm bilgileri için [Sürüm ve bölge kullanılabilirliğine](#version-and-region-availability) bakın.

## <a name="why-scheduled-events"></a>Neden Zamanlanan Olaylar?

Birçok uygulama, sanal makine bakımı için hazırlanma zamanından faydalanabilir. Bu süre, kullanılabilirliği, güvenilirliği ve bakım için aşağıdakiler dahil olmak üzere uygulama özel görevleri gerçekleştirmek için kullanılabilir: 

- Denetim noktası ve geri yükleme
- Bağlantı boşaltma
- Birincil çoğaltma yük devretmesi 
- Yük dengeleyici havuzundan kaldırma
- Olay günlüğü
- Düzgün kapanma 

Uygulamanızın Zamanlanan Olaylar kullanımı, bakımın ne zaman gerçekleşeceğini bulabilir ve etkilerini sınırlamak için görevleri tetikler. Zamanlanan olayları etkinleştirmek, bakım etkinliği gerçekleştirilmeden önce sanal makinenize en az bir süre sağlar. Ayrıntılar için aşağıdaki olay zamanlaması bölümüne bakın.

Zamanlanan Olaylar aşağıdaki kullanım durumlarında Olaylar sağlar:
- [Platform tarafından başlatılan bakım](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates) (ÖRNEĞIN, VM yeniden başlatma, dinamik geçiş veya konak için güncelleştirmeleri koruma)
- Azaltılmış donanım
- Kullanıcı tarafından başlatılan bakım (ör. Kullanıcı bir VM 'yi yeniden başlatır veya yeniden dağıtır)
- Ölçek kümelerinde [düşük öncelıklı VM çıkarma](https://azure.microsoft.com/blog/low-priority-scale-sets)

## <a name="the-basics"></a>Temel bilgileri  

Azure meta veri hizmeti, sanal makineleri VM içinden erişilebilen bir REST uç noktası kullanarak çalıştırmaya yönelik bilgiler sunar. Bilgiler, sanal makinenin dışında kullanıma sunulmaması için yönlendirilemeyen bir IP aracılığıyla kullanılabilir.

### <a name="endpoint-discovery"></a>Uç nokta bulma
VNET etkin VM 'Ler için, meta veri hizmeti statik olmayan statik bir IP `169.254.169.254`kullanılabilir. Zamanlanan Olaylar en son sürümü için tam uç nokta şunlardır: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01`

Sanal makine bir sanal ağ içinde oluşturulmadıysa, bulut hizmetleri ve klasik VM 'Ler için varsayılan durumlar olarak, kullanılacak IP adresini bulması için ek mantık gerekir. [Konak uç noktasını bulmayı](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)öğrenmek için bu örneğe bakın.

### <a name="version-and-region-availability"></a>Sürüm ve bölge kullanılabilirliği
Zamanlanan Olaylar hizmeti sürümlenmiş. Sürümler zorunludur ve geçerli sürüm `2017-11-01`.

| Sürüm | Yayın türü | Bölgeler | Sürüm Notları | 
| - | - | - | - |
| 2017-11-01 | Genel Erişilebilirlik | Tümü | <li> Düşük öncelikli VM çıkarma EventType ' preempt ' desteği eklendi<br> | 
| 2017-08-01 | Genel Erişilebilirlik | Tümü | <li> IaaS VM 'lerinin kaynak adlarından eklenmiş alt çizgi kaldırıldı<br><li>Tüm istekler için meta veri üst bilgisi gereksinimi zorlandı | 
| 2017-03-01 | Önizleme | Tümü |<li>İlk yayın

> [!NOTE] 
> Zamanlanan olayların önceki önizleme sürümleri, api sürümü olarak {latest} desteklenir. Bu biçim artık desteklenmemektedir ve gelecekte kullanım dışı bırakılacak.

### <a name="enabling-and-disabling-scheduled-events"></a>Zamanlanan Olaylar etkinleştirme ve devre dışı bırakma
Zamanlanan Olaylar, ilk kez olay isteği yaptığınızda hizmetiniz için etkinleştirilir. En fazla iki dakikalık ilk çağrındaki gecikmeli bir yanıt beklemelisiniz. Yaklaşan bakım olaylarını ve gerçekleştirilen bakım etkinliklerinin durumunu tespit etmek için uç noktayı düzenli aralıklarla sorgulayın.

24 saat boyunca bir istek yapmadığından hizmetiniz için Zamanlanan Olaylar devre dışı bırakılır.

### <a name="user-initiated-maintenance"></a>Kullanıcı tarafından başlatılan bakım
Azure portal, API, CLı veya PowerShell aracılığıyla Kullanıcı tarafından başlatılan sanal makine bakımı zamanlanmış bir olayda sonuçlanıyor. Bu, uygulamanızda bakım hazırlama mantığını test etmenizi ve uygulamanızın kullanıcı tarafından başlatılan bakım için hazırlanmasına olanak tanır.

Bir sanal makineyi yeniden başlatmak `Reboot`türünde bir olay zamanlar. Bir sanal makineyi yeniden dağıtmak, `Redeploy`türünde bir olay zamanlar.

## <a name="using-the-api"></a>API 'YI kullanma

### <a name="headers"></a>Üst bilgiler
Metadata Service sorgulayıp, isteğin istem dışı yönlendirilmediğinden emin olmak için üst bilgi `Metadata:true` sağlamanız gerekir. `Metadata:true` üstbilgisi tüm zamanlanmış olaylar istekleri için gereklidir. Üst bilgiyi istek içine dahil etme hatası, Metadata Service hatalı Istek yanıtı oluşmasına neden olur.

### <a name="query-for-events"></a>Olayları sorgula
Aşağıdaki çağrıyı yaparak Zamanlanan Olaylar için sorgulama yapabilirsiniz:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01 -H @{"Metadata"="true"}
```

Bir yanıt, Zamanlanmış olaylar dizisi içerir. Boş bir dizi, şu anda zamanlanan hiçbir olay olmadığı anlamına gelir.
Zamanlanan olayların bulunduğu durumda, yanıt bir olay dizisi içerir: 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
Documentınnation bir ETag ' dir ve olayların yükünün son sorgudan bu yana değişip değişmediğini denetlemek için kolay bir yol sağlar.

### <a name="event-properties"></a>Olay özellikleri
|Özellik  |  Açıklama |
| - | - |
| EventID | Bu olay için genel benzersiz tanımlayıcı. <br><br> Örnek: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| Türü | Bu olay nedenlerini etkiler. <br><br> Değerler: <br><ul><li> `Freeze`: sanal makine birkaç saniye duraklamak üzere zamanlandı. CPU ve ağ bağlantısı askıya alınabilir, ancak bellekte veya açık dosyalarda bir etkisi yoktur. <li>`Reboot`: sanal makine yeniden başlatma için zamanlandı (kalıcı olmayan bellek kaybolur). <li>`Redeploy`: sanal makine başka bir düğüme ilerlemek üzere zamanlandı (kısa ömürlü diskler kaybedilir). <li>`Preempt`: düşük öncelikli sanal makine siliniyor (kısa ömürlü diskler kaybolur).|
| ResourceType | Bu olayın etkilediği kaynak türü. <br><br> Değerler: <ul><li>`VirtualMachine`|
| Kaynaklar| Bu olayın etkilediği kaynakların listesi. Bu, en çok bir [güncelleştirme etki alanındaki](manage-availability.md)makineler bulundur, ancak ud 'deki tüm makineleri içermeyebilir. <br><br> Örnek: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Olay durumu | Bu olayın durumu. <br><br> Değerler: <ul><li>`Scheduled`: Bu olay, `NotBefore` özelliğinde belirtilen süreden sonra başlayacak şekilde zamanlandı.<li>`Started`: Bu olay başlatıldı.</ul> Hiç `Completed` veya benzer bir durum sağlanmaz; olay tamamlandığında olay artık döndürülmeyecektir.
| NotBefore| Bu olayın başlayabileceği zaman. <br><br> Örnek: <br><ul><li> Mon, 19 Eyl 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Olay zamanlaması
Her olay, gelecekte olay türüne göre en az bir süre zamanlanır. Bu zaman, bir olayın `NotBefore` özelliğine yansıtılır. 

|Türü  | En düşük bildirim |
| - | - |
| Amazsınız| 15 dakika |
| Yeniden başlatma | 15 dakika |
| Yeniden dağıtım | 10 dakika |
| Preempt | 30 saniye |

### <a name="event-scope"></a>Olay kapsamı     
Zamanlanan olaylar şu şekilde dağıtılır:
 - Tek başına sanal makineler
 - Bir bulut hizmetindeki tüm sanal makineler      
 - Bir kullanılabilirlik kümesindeki tüm sanal makineler      
 - Ölçek kümesi yerleştirme grubundaki tüm sanal makineler.         

Sonuç olarak, hangi VM 'Lerin etkilenecek olduğunu belirlemek için olaydaki `Resources` alanını denetlemeniz gerekir. 

### <a name="starting-an-event"></a>Olay başlatma 

Yaklaşan bir olayı öğrendikten ve düzgün kapanma için mantığınızı tamamladıktan sonra, `EventId`meta veri hizmetine `POST` çağrısı yaparak bekleyen olayı onaylayabilirsiniz. Bu, Azure 'a, en düşük bildirim süresini (mümkün olduğunda) kısaltıp kısaltamayacağını gösterir. 

`POST` istek gövdesinde beklenen JSON aşağıda verilmiştir. İstek bir `StartRequests`listesi içermelidir. Her `StartRequest`, hızlandırmak istediğiniz olay için `EventId` içerir:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="powershell"></a>PowerShell
```
curl -H @{"Metadata"="true"} -Method POST -Body '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01
```

> [!NOTE] 
> Bir olayı ele almak, olayın yalnızca olayı ele alan sanal makineyi değil, olaydaki tüm `Resources` devam etmesine izin verir. Bu nedenle, onayı koordine etmek için `Resources` alanındaki ilk makine kadar basit olabilecek bir öncü seçin.


## <a name="powershell-sample"></a>PowerShell örneği 

Aşağıdaki örnek, Zamanlanmış olaylar için meta veri hizmetini sorgular ve her bir bekleyen olayı onaylar.

```powershell
# How to get scheduled events 
function Get-ScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function Approve-ScheduledEvent($eventId, $uri)
{
    # Create the Scheduled Events Approval Document
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests} 
    
    # Convert to JSON string
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

function Handle-ScheduledEvents($scheduledEvents)
{
    # Add logic for handling events here
}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events URI for a VNET-enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-11-01' -f $localHostIP 

# Get events
$scheduledEvents = Get-ScheduledEvents $scheduledEventURI

# Handle events however is best for your service
Handle-ScheduledEvents $scheduledEvents

# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
        Approve-ScheduledEvent $event.EventId $scheduledEventURI 
    }
}
``` 

## <a name="next-steps"></a>Sonraki adımlar 

- Azure Cuma 'da [zamanlanan olaylar tanıtım](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) izleyin. 
- [GitHub deposunda zamanlanan olaylar Azure örnek meta verilerinde](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm) zamanlanan olaylar kod örneklerini gözden geçirin
- [Örnek meta veri hizmetinde](instance-metadata-service.md)bulunan API 'ler hakkında daha fazla bilgi edinin.
- [Azure 'Da Windows sanal makineleri için planlı bakım](planned-maintenance.md)hakkında bilgi edinin.
