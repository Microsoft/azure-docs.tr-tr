---
title: Bağlantı Izleyicisi oluşturma-ARM şablonu
titleSuffix: Azure Network Watcher
description: ARMClient kullanarak bağlantı Izleyicisi oluşturmayı öğrenin.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 46bdaf932d4224bf97b46e7713d49d815ca1bcdd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99833006"
---
# <a name="create-a-connection-monitor-using-the-arm-template"></a>ARM şablonunu kullanarak bağlantı Izleyicisi oluşturma

> [!IMPORTANT]
> 1 Temmuz 2021 ' den itibaren, mevcut bir çalışma alanına yeni testler ekleyemez veya Ağ Performansı İzleyicisi yeni bir çalışma alanı etkinleştiremeyeceksiniz. Ayrıca, bağlantı Izleyicisinde (klasik) yeni bağlantı izleyicileri ekleyemeyeceksiniz. 1 Temmuz 2021 ' den önce oluşturulan testleri ve bağlantı izleyicilerini kullanmaya devam edebilirsiniz. Geçerli iş yüklerinizde hizmet kesintisini en aza indirmek için, [testlerinizi ağ performansı İzleyicisi ](migrate-to-connection-monitor-from-network-performance-monitor.md) veya  [bağlantı izleyicisinden (klasik)](migrate-to-connection-monitor-from-connection-monitor-classic.md) Azure ağ Izleyicisi 'ndeki yeni bağlantı Izleyicisinden, 29 Şubat 2024 tarihinden önce geçirin.

ARMClient kullanarak kaynaklarınız arasındaki iletişimi izlemek için bağlantı Izleyicisi oluşturmayı öğrenin. Karma ve Azure bulut dağıtımlarını destekler.


## <a name="before-you-begin"></a>Başlamadan önce 

Bağlantı Izleyicisinde oluşturduğunuz bağlantı izleyicilerinde, hem şirket içi makineleri hem de Azure VM 'lerini kaynak olarak ekleyebilirsiniz. Bu bağlantı izleyicileri, uç noktalara bağlantıyı da izleyebilir. Uç noktalar Azure veya başka bir URL veya IP üzerinde olabilir.

Bağlantı Izleyicisi aşağıdaki varlıkları içerir:

* **Bağlantı İzleyicisi kaynağı** : bölgeye özgü bir Azure kaynağı. Aşağıdaki varlıkların hepsi bir bağlantı İzleyicisi kaynağının özellikleridir.
* **Uç nokta** : bağlantı denetimlerine katılan kaynak veya hedef. Uç noktalara örnek olarak Azure VM 'Leri, şirket içi aracılar, URL 'Ler ve IP 'Ler verilebilir.
* **Test yapılandırması** – bir test için protokole özgü bir yapılandırma. Seçtiğiniz protokole bağlı olarak, bağlantı noktası, eşikler, sınama sıklığı ve diğer parametreleri tanımlayabilirsiniz.
* **Test grubu** : kaynak uç noktaları, hedef uç noktaları ve test yapılandırmalarının bulunduğu grup. Bir bağlantı İzleyicisi birden fazla test grubu içerebilir.
* **Test** : kaynak uç noktası, hedef uç noktası ve test yapılandırmasının birleşimi. Test, izleme verilerinin kullanılabildiği en ayrıntılı düzeydir. İzleme verileri, başarısız olan denetim yüzdesini ve gidiş dönüş süresini (RTT) içerir.

    ![Test grupları ve testler arasındaki ilişkiyi tanımlayan bir bağlantı izleyicisini gösteren diyagram](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-sample-arm-template"></a>Örnek ARM şablonuyla oluşturma adımları

ARMClient kullanarak bir bağlantı İzleyicisi oluşturmak için aşağıdaki kodu kullanın.

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "https://management.azure.com"

$SUB = "subscriptions/<subscription id 1>;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_<region>"

$body =

"{

location: '<region>',

properties: {

endpoints: [{

name: 'endpoint_workspace_machine',

type: 'MMAWorkspaceMachine',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

//Example 1: Choose a machine

address : '<non-Azure machine FQDN>'
}

//Example 2: Select IP from chosen machines

address : '<non-Azure machine FQDN>

"scope": {
      "include": [
            {
                  "address": "<IP belonging to machine chosen above>"  
        }
       ]
      }
   }    
   
name: 'endpoint_workspace_network',

type: 'MMAWorkspaceNetwork',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

 coverage level : 'high', //Optional
 
 //Include subnets. You can also exclude IPs from subnet to exclude from monitoring
 
 scope: {
      "include": [
            {
                  "address": "<subnet 1 mask>" // Eg: 10.10.1.0/28
            },
            {
                  "address": "<subnet 2 mask>" 
            }
      ],
      "exclude": [
            { 
            "address" : "<ip-from-included-subnets-that-should-be-excluded>"
        }
      ]
     }
},

//Use a Azure VM as an endpoint
{

name: 'endpoint_virtualmachine',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

//Use an Azure VNET or Subnet as an endpoint

 {

name: 'endpoint_vnet_subnet',

resourceId: '<resource id of VNET or subnet'
coverage level: 'high' //Optional

//Scope is optional.

  "scope": {
      "include": [
            {
                  "address": "<subnet 1 mask>" // Eg: 10.10.1.0/28 .This subnet should match with any existing subnet in vnet
            }
      ],
    "exclude": [
            {
                  "address": "<ip-from-included-subnets-that-should-be-excluded>" // If used with include, IP should be part of the subnet defined above. Without include, this could be any address within vnet range or any specific subnet range as a whole.
            }
      ]
  }
   },

//Endpoint as a URL
{

name: 'azure portal'

address: '<URL>'

   },

//Endpoint as an IP 
 {

    name: 'ip',

     address: '<IP>'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

   // Choose your protocol
   
    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: <threshold for checks failed %>,

     roundTripTimeMs: <threshold for RTT>

    }

   }, {

    name: 'https',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    httpConfiguration: {
    
     port: '<port of choice>'
  
    preferHTTPS: true // If port chosen is not 80 or 443
    
    method: 'GET', //Choose GET or POST
    
    path: '/', //Specify path for request
         
    requestHeaders: [
            {
              "name": "Content-Type",
              "value": "appication/json"
            }
          ],
          
    validStatusCodeRanges: [ "102", "200-202", "3xx" ], //Samples
          
    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, 
   {

    name: 'tcpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }

  ]

 }

} "
```

Dağıtım komutu aşağıda verilmiştir:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

## <a name="description-of-properties"></a>Özelliklerin açıklaması

* connectionMonitorName-Bağlantı İzleyicisi kaynağının adı

* Bağlantı İzleyicisi oluşturmak istediğiniz aboneliğin alt abonelik KIMLIĞI

* NW-ağ Izleyicisi kaynak KIMLIĞI, CM 'nin oluşturulacağı 

* Bağlantı İzleyicisi 'nin oluşturulacağı konum bölgesi

* Uç Noktalar
    * ad – her uç nokta için benzersiz ad
    * RESOURCEID – Azure uç noktaları Için kaynak KIMLIĞI, sanal makineler için Azure Resource Manager kaynak KIMLIĞINE başvurur. Azure dışı uç noktalar için kaynak KIMLIĞI, Azure olmayan aracılara bağlı Log Analytics çalışma alanının Azure Resource Manager kaynak KIMLIĞINE başvurur.
    * Adres: yalnızca kaynak KIMLIĞI belirtilmediğinde veya kaynak KIMLIĞI Log Analytics çalışma alanı ise geçerlidir. Log Analytics kaynak KIMLIĞIYLE birlikte kullanılırsa, bu, izleme için kullanılabilen aracının FQDN 'sine başvurur. Kaynak KIMLIĞI olmadan kullanılırsa bu, herhangi bir genel bitiş noktasının URL veya IP 'SI olabilir.
    * Filtre: Azure dışı uç noktalar Için, Bağlantı İzleyicisi kaynağında izlemek üzere kullanılacak Log Analytics çalışma alanından aracıları seçmek için filtre kullanın. Filtreler ayarlanmamışsa, Log Analytics çalışma alanına ait olan tüm aracılar izleme için kullanılabilir
        * tür – türü "aracı adresi" olarak ayarla
        * Adres: adresi şirket içi aracınızın FQDN 'SI olarak ayarlayın

* Test grupları
    * ad-test grubunuzu adlandırın.
    * testConfigurations-kaynak uç noktaların hedef uç noktalara bağlandığı test yapılandırması
    * kaynaklar-yukarıda oluşturulan uç noktalardan seçin. Azure tabanlı kaynak uç noktalarında Azure ağ Izleyicisi uzantısının yüklü olması ve Azure tabanlı kaynak uç noktalarında Azure Log Analytics aracısının yüklü olması gerekir. Kaynağınıza yönelik bir aracı yüklemek için bkz. [izleme aracılarını yüklemek](./connection-monitor-overview.md#install-monitoring-agents).
    * hedefler-yukarıda oluşturulan uç noktalardan seçin. Azure VM 'lerine veya herhangi bir uç noktaya (genel IP, URL veya FQDN) yönelik bağlantıyı, bunları hedefler olarak belirterek izleyebilirsiniz. Tek bir test grubunda Azure VM 'leri, Office 365 URL 'Leri, Dynamics 365 URL 'Leri ve özel uç noktaları ekleyebilirsiniz.
    * devre dışı bırak-bu alanı, test grubunun belirttiği tüm kaynaklar ve hedefler için izlemeyi devre dışı bırakmak için kullanın.

* Test yapılandırması
    * ad-test yapılandırmasının adı.
    * testFrequencySec-kaynakların, belirttiğiniz protokol ve bağlantı noktasındaki hedeflere ne sıklıkta ping olarak alınacağını belirtin. 30 saniye, 1 dakika, 5 dakika, 15 dakika veya 30 dakika seçebilirsiniz. Kaynaklar, seçtiğiniz değere göre hedeflere bağlantıyı test edecektir. Örneğin, 30 saniye seçerseniz, kaynaklar en az 30 saniyelik bir dönemde hedefe bağlantıyı kontrol eder.
    * protokol-TCP, ıCMP, HTTP veya HTTPS seçeneklerini belirleyebilirsiniz. Protokolüne bağlı olarak, bazı protokole özgü yapılandırmaları yapabilirsiniz
    
        * preferHTTPS-kullanılan bağlantı noktası ne 80 ne de 443 değil, HTTP üzerinden HTTPS kullanılıp kullanılmayacağını belirtin
        * bağlantı noktası-tercih ettiğiniz hedef bağlantı noktasını belirtin.
        * Disableizleme Oute-bu, protokolü TCP veya ıCMP olan test yapılandırmalarına yöneliktir. Topoloji ve atlama-atlama RTT bulma kaynaklarını durdurur.
        * Yöntem-bu, Protokolü HTTP olan test yapılandırmalarına uygulandı. HTTP istek yöntemini seçin--Al veya postala
        * yol-URL 'ye eklenecek yol parametrelerini belirtin
        * validStatusCodes-geçerli durum kodlarını seçin. Yanıt kodu bu listeyle eşleşmiyorsa, bir tanılama iletisi alacaksınız
        * requestHeaders-hedefe geçirilecek özel istek üst bilgisi dizelerini belirtin
        
    * Başarılı eşik-aşağıdaki ağ parametrelerinde eşikler ayarlayabilirsiniz:
        * checksFailedPercent-belirttiğiniz ölçütlere göre kaynaklar hedeflere bağlantı denetlediğinde başarısız olan denetim yüzdesini ayarlayın. TCP veya ıCMP protokolü için, başarısız denetimlerin yüzdesi, paket kaybı yüzdesine eşit hale getirilmiş olabilir. HTTP protokolü için, bu alan yanıt olmadan alınan HTTP isteklerinin yüzdesini temsil eder.
        * Roundroundtimems-kaynakların test yapılandırması üzerinden hedefe bağlanması için geçen süreyi milisaniye olarak ayarlayın.

## <a name="scale-limits"></a>Ölçek sınırları

Bağlantı izleyicileri aşağıdaki ölçek sınırlarına sahiptir:

* Her bölge için abonelik başına en fazla bağlantı İzleyicisi: 100
* Bağlantı İzleyicisi başına en fazla test grubu: 20
* Bağlantı İzleyicisi başına en fazla kaynak ve hedef sayısı: 100
* Bağlantı İzleyicisi başına en fazla test yapılandırması: ARMClient aracılığıyla 20

## <a name="next-steps"></a>Sonraki adımlar

* [İzleme verilerini çözümlemeyi ve uyarıları ayarlamayı](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts) öğrenin
* [Ağınızdaki sorunları tanılamayı](./connection-monitor-overview.md#diagnose-issues-in-your-network) öğrenin
