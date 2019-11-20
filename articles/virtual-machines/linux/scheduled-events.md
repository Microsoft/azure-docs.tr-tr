---
title: Azure 'da Linux sanal makineleri için Zamanlanan Olaylar
description: Linux sanal makineleriniz için Azure Metadata Service kullanarak olayları zamanlayın.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: 1e348adc06a970fcd7222ce612c13f0ff3e01585
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035097"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Azure Metadata Service: Linux sanal makineleri için Zamanlanan Olaylar

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

- [Platform tarafından başlatılan bakım](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) (ÖRNEĞIN, VM yeniden başlatma, dinamik geçiş veya konak için güncelleştirmeleri koruma)
- Azaltılmış donanım
- Kullanıcı tarafından başlatılan bakım (örneğin, bir Kullanıcı bir VM 'yi yeniden başlatır veya yeniden dağıtır)
- Ölçek kümelerinde [düşük öncelıklı VM çıkarma](https://azure.microsoft.com/blog/low-priority-scale-sets)

## <a name="the-basics"></a>Temel bilgileri  

  Metadata Service VM 'nin içinden erişilebilen bir REST uç noktası kullanarak VM çalıştırma hakkında bilgi sunar. Bilgiler, sanal makinenin dışında kullanıma sunulmaması için yönlendirilemeyen bir IP aracılığıyla kullanılabilir.

### <a name="scope"></a>Kapsam
Zamanlanan olaylar şu şekilde dağıtılır:

- Tek başına sanal makineler.
- Bir bulut hizmetindeki tüm VM 'Ler.
- Bir kullanılabilirlik kümesindeki tüm VM 'Ler.
- Ölçek kümesi yerleştirme grubundaki tüm VM 'Ler. 

Sonuç olarak, hangi VM 'Lerin etkilendiğini belirlemek için olaydaki `Resources` alanını kontrol edin.

### <a name="endpoint-discovery"></a>Uç nokta bulma
VNET etkin VM 'Ler için, Metadata Service statik olarak yönlendirilemeyen bir IP `169.254.169.254`kullanılabilir. Zamanlanan Olaylar en son sürümü için tam uç nokta şunlardır: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01`

VM bir sanal ağ içinde oluşturulmadıysa, bulut hizmetleri ve klasik VM 'Ler için varsayılan durumlar olarak, kullanılacak IP adresini bulması için ek mantık gerekir. [Konak uç noktasını bulmayı](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)öğrenmek için bu örneğe bakın.

### <a name="version-and-region-availability"></a>Sürüm ve bölge kullanılabilirliği
Zamanlanan Olaylar hizmeti sürümlenmiş. Sürümler zorunludur; geçerli sürüm `2017-11-01`.

| Sürüm | Yayın türü | Bölgeler | Sürüm Notları | 
| - | - | - | - | 
| 2017-11-01 | Genel Erişilebilirlik | Tümü | <li> Düşük öncelikli VM çıkarma EventType ' preempt ' desteği eklendi<br> | 
| 2017-08-01 | Genel Erişilebilirlik | Tümü | <li> IaaS VM 'lerinin kaynak adlarından eklenmiş alt çizgi kaldırıldı<br><li>Tüm istekler için meta veri üst bilgisi gereksinimi zorlandı | 
| 2017-03-01 | Önizleme | Tümü | <li>İlk yayın


> [!NOTE] 
> API sürümü olarak desteklenen Zamanlanan Olaylar önceki önizleme sürümleri {Latest}. Bu biçim artık desteklenmemektedir ve gelecekte kullanım dışı bırakılacak.

### <a name="enabling-and-disabling-scheduled-events"></a>Zamanlanan Olaylar etkinleştirme ve devre dışı bırakma
Zamanlanan Olaylar, ilk kez olay isteği yaptığınızda hizmetiniz için etkinleştirilir. En fazla iki dakikalık ilk çağrındaki gecikmeli bir yanıt beklemelisiniz.

24 saat boyunca bir istek yapmadığından hizmetiniz için Zamanlanan Olaylar devre dışı bırakılır.

### <a name="user-initiated-maintenance"></a>Kullanıcı tarafından başlatılan bakım
Azure portal, API, CLı veya PowerShell aracılığıyla Kullanıcı tarafından başlatılan VM Bakımı zamanlanmış bir olayda sonuçlanır. Daha sonra uygulamanızdaki bakım hazırlama mantığını test edebilir ve uygulamanız kullanıcı tarafından başlatılan bakım için hazırlanabilirsiniz.

Bir VM 'yi yeniden başlatırsanız, `Reboot` türünde bir olay zamanlanır. Bir VM 'yi yeniden dağıtıyorsanız, `Redeploy` türünde bir olay zamanlanır.

## <a name="use-the-api"></a>API’yi kullanma

### <a name="headers"></a>Üst bilgiler
Metadata Service sorgulayıp, isteğin istem dışı olarak yeniden yönlendirilmemesini sağlamak için üst bilgi `Metadata:true` sağlamanız gerekir. `Metadata:true` üstbilgisi tüm zamanlanmış olaylar istekleri için gereklidir. Üst bilgiyi istek içine ekleme hatası, Metadata Service ' den gelen bir "Hatalı Istek" yanıtı ile sonuçlanır.

### <a name="query-for-events"></a>Olayları sorgula
Aşağıdaki çağrıyı yaparak Zamanlanmış olaylar için sorgulama yapabilirsiniz:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

Bir yanıt, Zamanlanmış olaylar dizisi içerir. Boş bir dizi, şu anda hiçbir olayın planlanmadığını gösterir.
Zamanlanan olayların olduğu durumlarda, yanıt bir olay dizisi içerir. 
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

### <a name="event-properties"></a>Olay özellikleri
|Özellik  |  Açıklama |
| - | - |
| EventID | Bu olay için genel benzersiz tanımlayıcı. <br><br> Örnek: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| Türü | Bu olay nedenlerini etkiler. <br><br> Değerler: <br><ul><li> `Freeze`: sanal makine birkaç saniye duraklamak üzere zamanlandı. CPU ve ağ bağlantısı askıya alınabilir, ancak bellekte veya açık dosyalarda bir etkisi yoktur.<li>`Reboot`: sanal makine yeniden başlatma için zamanlandı (kalıcı olmayan bellek kaybolur). <li>`Redeploy`: sanal makine başka bir düğüme ilerlemek üzere zamanlandı (kısa ömürlü diskler kaybedilir). <li>`Preempt`: düşük öncelikli sanal makine siliniyor (kısa ömürlü diskler kaybolur).|
| ResourceType | Bu olayın etkilediği kaynak türü. <br><br> Değerler: <ul><li>`VirtualMachine`|
| Kaynaklar| Bu olayın etkilediği kaynakların listesi. Listenin, en çok bir [güncelleştirme etki](manage-availability.md)alanından makineler içermesi garanti edilir, ancak bu, ud 'deki tüm makineleri içermeyebilir. <br><br> Örnek: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Bu olayın durumu. <br><br> Değerler: <ul><li>`Scheduled`: Bu olay, `NotBefore` özelliğinde belirtilen süreden sonra başlayacak şekilde zamanlandı.<li>`Started`: Bu olay başlatıldı.</ul> Hiç `Completed` veya benzer bir durum sağlanmaz. Olay tamamlandığında olay artık döndürülmez.
| NotBefore| Bu olayın başlayabileceği zaman. <br><br> Örnek: <br><ul><li> Mon, 19 Eyl 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Olay zamanlaması
Her olay, gelecekte olay türüne göre en az bir süre zamanlanır. Bu zaman, bir olayın `NotBefore` özelliğine yansıtılır. 

|Türü  | En düşük bildirim |
| - | - |
| Amazsınız| 15 dakika |
| Yeniden başlatma | 15 dakika |
| Yeniden dağıtım | 10 dakika |
| Preempt | 30 saniye |

### <a name="start-an-event"></a>Olay başlatma 

Yaklaşan bir olayı öğrendikten ve düzgün kapanma için mantığınızı tamamladıktan sonra, `EventId`ile Metadata Service için `POST` çağrısı yaparak bekleyen olayı onaylayabilirsiniz. Bu çağrı, Azure 'a, en düşük bildirim süresini (mümkün olduğunda) kısaltıp kısaltamayacağını gösterir. 

`POST` istek gövdesinde aşağıdaki JSON örneği bekleniyor. İstek bir `StartRequests`listesi içermelidir. Her `StartRequest`, hızlandırmak istediğiniz olay için `EventId` içerir:
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
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01
```

> [!NOTE] 
> Bir olayı ele almak, olayın yalnızca olayı kabul eden VM değil, olaydaki tüm `Resources` devam etmesine olanak tanır. Bu nedenle, onayı koordine etmek için bir öncü seçebilirsiniz ve bu, `Resources` alanındaki ilk makine kadar kolay olabilir.

## <a name="python-sample"></a>Python örneği 

Aşağıdaki örnek sorgular, Zamanlanmış olaylar için Metadata Service ve her bir bekleyen olayı onaylar:

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01"
headers = "{Metadata:true}"
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
        notbefore = evt['NotBefore'].replace(" ","_")
        if this_host in resources:
            print "+ Scheduled Event. This host " + this_host + " is scheduled for " + eventtype + " not before " + notbefore
            # Add logic for handling events here


def main():
   data = get_scheduled_events()
   handle_scheduled_events(data)

if __name__ == '__main__':
  main()
  sys.exit(0)
```

## <a name="next-steps"></a>Sonraki adımlar 
- Tanıtım görmek için [Azure Cuma 'daki zamanlanan olaylar](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) izleyin. 
- [GitHub deposunda zamanlanan olaylar Azure örnek meta verileri](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)zamanlanan olaylar kod örneklerini inceleyin.
- [Instance Metadata Service](instance-metadata-service.md)mevcut olan API 'ler hakkında daha fazla bilgi edinin.
- [Azure 'Da Linux sanal makineleri için planlı bakım](planned-maintenance.md)hakkında bilgi edinin.
