---
title: Azure 'da Linux sanal makineleri için Zamanlanan Olaylar
description: Linux sanal makineleriniz için Azure Metadata Service kullanarak olayları zamanlayın.
author: EricRadzikowskiMSFT
ms.service: virtual-machines
ms.subservice: scheduled-events
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2020
ms.author: ericrad
ms.reviewer: mimckitt
ms.openlocfilehash: 8b4f8b064ab19a578ce5854697a1ed9bb0195759
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102505401"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Azure Metadata Service: Linux VM'ler için Zamanlanan Olaylar

Zamanlanan Olaylar, sanal makine (VM) bakımına hazırlanmak için uygulamanızın süresini sağlayan bir Azure Metadata Service. Uygulamanızın onlara hazırlanabilmesi ve kesintiye uğramasını önlemek için yaklaşan bakım olayları (örneğin, yeniden başlatma) hakkında bilgi sağlar. Hem Windows hem de Linux 'ta PaaS ve IaaS dahil tüm Azure sanal makine türlerinde kullanılabilir. 

Windows üzerinde Zamanlanan Olaylar hakkında bilgi için bkz. [Windows VM 'leri için zamanlanan olaylar](../windows/scheduled-events.md).

> [!Note] 
> Zamanlanan Olaylar, tüm Azure bölgelerinde genel kullanıma sunulmuştur. En son sürüm bilgileri için [Sürüm ve bölge kullanılabilirliğine](#version-and-region-availability) bakın.

## <a name="why-use-scheduled-events"></a>Neden Zamanlanan Olaylar kullanmalıyım?

Birçok uygulama, VM bakımı için hazırlanma zamanından faydalanabilir. Bu süre, kullanılabilirliği, güvenilirliği ve bakım çalışmalarını artıran uygulamaya özgü görevleri gerçekleştirmek için kullanılabilir: 

- Denetim noktası ve geri yükleme.
- Bağlantı boşaltma.
- Birincil çoğaltma yük devretmesi.
- Yük dengeleyici havuzundan kaldırma.
- Olay günlüğü.
- Düzgün kapanma.

Zamanlanan Olaylar, uygulamanız bakımın ne zaman yapılacağını bulabilir ve etkilerini sınırlamak için görevleri tetikleyebilirsiniz.  

Zamanlanan Olaylar aşağıdaki kullanım durumlarında Olaylar sağlar:

- [Platform tarafından başlatılan bakım](../maintenance-and-updates.md?bc=/azure/virtual-machines/linux/breadcrumb/toc.json&toc=/azure/virtual-machines/linux/toc.json) (ÖRNEĞIN, VM yeniden başlatma, dinamik geçiş veya konak için güncelleştirmeleri koruma)
- Sanal makine, yakında başarısız olarak tahmin edilen [düşürülmüş konak donanımında](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events) çalışıyor
- Kullanıcı tarafından başlatılan bakım (örneğin, bir Kullanıcı bir VM 'yi yeniden başlatır veya yeniden dağıtır)
- [Spot VM](../spot-vms.md) ve [spot ölçek kümesi](../../virtual-machine-scale-sets/use-spot.md) örnek çıkarmaları.

## <a name="the-basics"></a>Temeller  

  Metadata Service VM 'nin içinden erişilebilen bir REST uç noktası kullanarak VM çalıştırma hakkında bilgi sunar. Bilgiler, sanal makinenin dışında kullanıma sunulmaması için yönlendirilemeyen bir IP aracılığıyla kullanılabilir.

### <a name="scope"></a>Kapsam
Zamanlanan olaylar şu şekilde dağıtılır:

- Tek başına sanal makineler.
- Bir bulut hizmetindeki tüm VM 'Ler.
- Bir kullanılabilirlik kümesindeki tüm VM 'Ler.
- Bir kullanılabilirlik bölgesindeki tüm VM 'Ler. 
- Ölçek kümesi yerleştirme grubundaki tüm VM 'Ler. 

> [!NOTE]
> Bir kullanılabilirlik bölgesindeki VM 'lere özel olarak, zamanlanan olaylar bir bölgedeki tek VM 'lere gider.
> Örneğin, bir kullanılabilirlik kümesinde 100 VM varsa ve bunlardan birine yönelik bir güncelleştirme varsa, zamanlanan olay tüm 100 ' e gider, ancak bir bölgede 100 tek VM varsa, olay yalnızca etkilenen VM 'ye gider.

Sonuç olarak, `Resources` hangi VM 'lerin etkilendiğini belirlemek için olaydaki alanı kontrol edin.

### <a name="endpoint-discovery"></a>Uç nokta bulma
VNET etkin VM 'Ler için Metadata Service statik olmayan statik bir IP 'den kullanılabilir `169.254.169.254` . Zamanlanan Olaylar en son sürümü için tam uç nokta şunlardır: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01`

VM bir sanal ağ içinde oluşturulmadıysa, bulut hizmetleri ve klasik VM 'Ler için varsayılan durumlar olarak, kullanılacak IP adresini bulması için ek mantık gerekir. [Konak uç noktasını bulmayı](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)öğrenmek için bu örneğe bakın.

### <a name="version-and-region-availability"></a>Sürüm ve bölge kullanılabilirliği
Zamanlanan Olaylar hizmeti sürümlenmiş. Sürümler zorunludur; geçerli sürüm `2019-08-01` .

| Sürüm | Yayın türü | Bölgeler | Sürüm Notları | 
| - | - | - | - | 
| 2019-08-01 | Genel kullanılabilirlik | Tümü | <li> EventSource için destek eklendi |
| 2019-04-01 | Genel kullanılabilirlik | Tümü | <li> Olay açıklaması için destek eklendi |
| 2019-01-01 | Genel kullanılabilirlik | Tümü | <li> Sanal Makine Ölçek Kümeleri için destek eklendi EventType ' Terminate ' |
| 2017-11-01 | Genel kullanılabilirlik | Tümü | <li> Nokta VM çıkarma olay türü ' preempt ' için destek eklendi<br> | 
| 2017-08-01 | Genel kullanılabilirlik | Tümü | <li> IaaS VM 'lerinin kaynak adlarından eklenmiş alt çizgi kaldırıldı<br><li>Tüm istekler için meta veri üst bilgisi gereksinimi zorlandı | 
| 2017-03-01 | Önizleme | Tümü | <li>İlk yayın |


> [!NOTE] 
> API sürümü olarak desteklenen Zamanlanan Olaylar önceki önizleme sürümleri {Latest}. Bu biçim artık desteklenmemektedir ve gelecekte kullanım dışı bırakılacak.

### <a name="enabling-and-disabling-scheduled-events"></a>Zamanlanan Olayları Etkinleştirme ve Devre Dışı Bırakma
Zamanlanan Olaylar, ilk kez olay isteği yaptığınızda hizmetiniz için etkinleştirilir. En fazla iki dakikalık ilk çağrındaki gecikmeli bir yanıt beklemelisiniz.

24 saat boyunca bir istek yapmadığından hizmetiniz için Zamanlanan Olaylar devre dışı bırakılır.

### <a name="user-initiated-maintenance"></a>Kullanıcı tarafından başlatılan bakım
Azure portal, API, CLı veya PowerShell aracılığıyla Kullanıcı tarafından başlatılan VM Bakımı zamanlanmış bir olayda sonuçlanır. Daha sonra uygulamanızdaki bakım hazırlama mantığını test edebilir ve uygulamanız kullanıcı tarafından başlatılan bakım için hazırlanabilirsiniz.

Bir VM 'yi yeniden başlatırsanız, türüne sahip bir olay `Reboot` zamanlanır. Bir VM 'yi yeniden dağıtırsanız, türüne sahip bir olay `Redeploy` zamanlanır.

## <a name="use-the-api"></a>API’yi kullanma

### <a name="headers"></a>Üst Bilgiler
Metadata Service sorgulayıp, `Metadata:true` isteğin istem dışı olarak yeniden yönlendirilmemesini sağlamak için üst bilgiyi sağlamanız gerekir. `Metadata:true`Üst bilgi tüm zamanlanmış olaylar istekleri için gereklidir. Üst bilgiyi istek içine ekleme hatası, Metadata Service ' den gelen bir "Hatalı Istek" yanıtı ile sonuçlanır.

### <a name="query-for-events"></a>Olayları sorgula
Aşağıdaki çağrıyı yaparak Zamanlanmış olaylar için sorgulama yapabilirsiniz:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Bir yanıt, Zamanlanmış olaylar dizisi içerir. Boş bir dizi, şu anda hiçbir olayın planlanmadığını gösterir.
Zamanlanan olayların olduğu durumlarda, yanıt bir olay dizisi içerir. 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt" | "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},       
            "Description": {eventDescription},
            "EventSource" : "Platform" | "User",
        }
    ]
}
```

### <a name="event-properties"></a>Olay özellikleri
|Özellik  |  Açıklama |
| - | - |
| Even | Bu olay için genel benzersiz tanımlayıcı. <br><br> Örnek: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| Olay türü | Bu olay nedenlerini etkiler. <br><br> Değerler: <br><ul><li> `Freeze`: Sanal makine birkaç saniye duraklamak üzere zamanlandı. CPU ve ağ bağlantısı askıya alınabilir, ancak bellekte veya açık dosyalarda bir etkisi yoktur.<li>`Reboot`: Sanal makine yeniden başlatma için zamanlandı (kalıcı olmayan bellek kaybolur). <li>`Redeploy`: Sanal makine başka bir düğüme ilerlemek üzere zamanlandı (kısa ömürlü diskler kaybolur). <li>`Preempt`: Spot sanal makine siliniyor (kısa ömürlü diskler kaybolur). <li> `Terminate`: Sanal makine silinmek üzere zamanlandı. |
| ResourceType | Bu olayın etkilediği kaynak türü. <br><br> Değerler: <ul><li>`VirtualMachine`|
| Kaynaklar| Bu olayın etkilediği kaynakların listesi. Listenin, en çok bir [güncelleştirme etki](../availability.md)alanından makineler içermesi garanti edilir, ancak bu, ud 'deki tüm makineleri içermeyebilir. <br><br> Örnek: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Bu olayın durumu. <br><br> Değerler: <ul><li>`Scheduled`: Bu olay, özellikte belirtilen süreden sonra başlayacak şekilde zamanlandı `NotBefore` .<li>`Started`: Bu olay başlatıldı.</ul> Hiç `Completed` veya benzer bir durum sağlanmamıştır. Olay tamamlandığında olay artık döndürülmez.
| NotBefore| Bu olayın başlayabileceği zaman. <br><br> Örnek: <br><ul><li> Mon, 19 Eyl 2016 18:29:47 GMT  |
| Description | Bu olayın açıklaması. <br><br> Örnek: <br><ul><li> Ana bilgisayar sunucusu bakımda. |
| EventSource | Olayın Başlatıcısı. <br><br> Örnek: <br><ul><li> `Platform`: Bu olay platform tarafından başlatılır. <li>`User`: Bu olay kullanıcı tarafından başlatılır. |

### <a name="event-scheduling"></a>Olay zamanlaması
Her olay, gelecekte olay türüne göre en az bir süre zamanlanır. Bu zaman, bir olayın `NotBefore` özelliğinde yansıtılır. 

|Olay türü  | En düşük bildirim |
| - | - |
| Amazsınız| 15 dakika |
| Yeniden başlatma | 15 dakika |
| Yeniden dağıtım | 10 dakika |
| Preempt | 30 saniye |
| Terminate | [Kullanıcı yapılandırılabilir](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications): 5 ila 15 dakika |

> [!NOTE] 
> Azure, bazı durumlarda ana bilgisayar başarısızlığını düşürülmüş bir donanım nedeniyle tahmin edebilir ve bir geçiş zamanlayarak hizmetinize olan kesintiyi azaltmaya çalışacaktır. Etkilenen sanal makineler, `NotBefore` genellikle gelecekte birkaç gün olan zamanlanmış bir olay alır. Gerçek süre, tahmin edilen hata riski değerlendirmesine bağlı olarak farklılık gösterir. Azure, mümkün olduğunda 7 gün daha kısa bir bildirimde bulunmaya çalışır, ancak tahmin, donanım arızalanmaya yönelik yüksek bir şansınız olması durumunda gerçek zaman değişir ve daha küçük olabilir. Sistemin sistem tarafından başlatılan geçişten önce başarısız olması durumunda hizmetinize yönelik riski en aza indirmek için, sanal makinenizi mümkün olan en kısa sürede otomatik olarak yeniden dağıtmanız önerilir.

### <a name="polling-frequency"></a>Yoklama sıklığı

Güncelleştirme için uç noktayı istediğiniz sıklıkta veya daha seyrek olarak yoklayabilmeniz gerekir. Ancak, istekler arasındaki süre ne kadar uzun zaman, yaklaşan bir olaya tepki vermek için büyük olasılıkla daha fazla zaman kaybı olabilir. Çoğu olay yaklaşık olarak 5 ila 15 dakikalık bir bildirimde bulunabilir, ancak bazı durumlarda öncelikli bir uyarı 30 saniye olabilir. Azaltıcı işlemleri gerçekleştirmek için mümkün olduğunca çok zaman olduğundan emin olmak için, hizmeti saniyede bir kez yoklamenizi öneririz.

### <a name="start-an-event"></a>Olay başlatma 

Yaklaşan bir olayı öğrendikten ve düzgün kapanma için mantığınızı tamamladıktan sonra, ile Metadata Service çağrısı yaparak bekleyen olayı onaylayabilirsiniz `POST` `EventId` . Bu çağrı, Azure 'a, en düşük bildirim süresini (mümkün olduğunda) kısaltıp kısaltamayacağını gösterir. 

İstek gövdesinde aşağıdaki JSON örneği bekleniyor `POST` . İstek bir listesi içermelidir `StartRequests` . Her `StartRequest` biri `EventId` , hızlandırmak istediğiniz olay için içerir:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Bash örneği
```
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

> [!NOTE] 
> Bir olayı ele almak `Resources` , olayın yalnızca olayı kabul eden VM değil, olaydaki her şey için devam etmesine olanak tanır. Bu nedenle, onayı koordine etmek için bir öncü seçebilirsiniz ve bu, alandaki ilk makine kadar kolay olabilir `Resources` .

## <a name="python-sample"></a>Python örneği 

Aşağıdaki örnek sorgular, Zamanlanmış olaylar için Metadata Service ve her bir bekleyen olayı onaylar:

```python
#!/usr/bin/python

import json
import socket
import urllib2

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01"
this_host = socket.gethostname()


def get_scheduled_events():
    req = urllib2.Request(metadata_url)
    req.add_header('Metadata', 'true')
    resp = urllib2.urlopen(req)
    data = json.loads(resp.read())
    return data


def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ", "_")
    description = evt['Description']
    eventSource = evt['EventSource']
        if this_host in resources:
            print("+ Scheduled Event. This host " + this_host +
                " is scheduled for " + eventtype + 
        " by " + eventSource + 
        " with description " + description +
        " not before " + notbefore)
            # Add logic for handling events here


def main():
    data = get_scheduled_events()
    handle_scheduled_events(data)


if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>Sonraki adımlar 
- Tanıtım görmek için [Azure Cuma 'daki zamanlanan olaylar](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) izleyin. 
- [GitHub deposunda zamanlanan olaylar Azure örnek meta verileri](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)zamanlanan olaylar kod örneklerini inceleyin.
- [Instance Metadata Service](instance-metadata-service.md)mevcut olan API 'ler hakkında daha fazla bilgi edinin.
- [Azure 'Da Linux sanal makineleri için planlı bakım](../maintenance-and-updates.md?bc=/azure/virtual-machines/linux/breadcrumb/toc.json&toc=/azure/virtual-machines/linux/toc.json)hakkında bilgi edinin.
