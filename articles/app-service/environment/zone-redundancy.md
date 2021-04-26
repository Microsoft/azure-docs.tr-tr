---
title: App Service ortamları için kullanılabilirlik alanı desteği
description: Uygulamalarınızın bölge yedekli olması için App Service ortamlarınızı dağıtmayı öğrenin.
author: ccompy
ms.assetid: 24e3e7eb-c160-49ff-8d46-e947818ef186
ms.topic: article
ms.date: 07/15/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 383b5bb5c7295fe54efda883e47b9b2338286de5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624734"
---
# <a name="availability-zone-support-for-app-service-environments"></a>App Service ortamları için kullanılabilirlik alanı desteği

App Service ortamları (Ao), Kullanılabilirlik Alanları (AZ) olarak dağıtılabilir.  Müşteriler bir Azure bölgesindeki bir alt yük dengeleyici (ıLB) ASE 'yi belirli bir şekilde dağıtabilir. ILB AX 'inizi belirli bir yere sabitleyerek, bir ıLB ay tarafından kullanılan kaynaklar, belirtilen AZ olarak sabitlenir veya bir bölge yedekli şekilde dağıtılır.  

ILB Ao, belirli bir bölgeye sabitlendiği için, AZ bir olarak AZ olarak dağıtılan bir ıLB Ao kaynak olarak kabul edilir. Aşağıdaki ıLB Ao bağımlılıkları belirtilen bölgeye sabitlenecektir:

- Ao 'nun iç yük dengeleyici IP adresi
- Web uygulamalarını yönetmek ve çalıştırmak için as tarafından kullanılan işlem kaynakları

Bir bölgesel İlb Ao 'da dağıtılan Web uygulamalarına yönelik uzak dosya depolama alanı, bölgesel olarak yedekli depolama (ZRS) kullanır.

Bu makalede açıklanan adımlar izlenmediği takdirde ıLB ASEs, bir şekilde otomatik olarak dağıtılmamaktadır. Genel bir IP adresi ile bir dış ATıCı 'yi belirli bir kullanılabilirlik bölgesine sabitleyemez. 

Aşağıdaki bölgelerde zonal ıLB ASEs oluşturulabilir:

- Doğu Avustralya
- Orta Kanada
- Central US
- Doğu ABD
- Doğu ABD 2
- Doğu ABD 2 (EUAP)
- Orta Fransa 
- Doğu Japonya
- Kuzey Avrupa
- West Europe
- Güneydoğu Asya
- Güney Birleşik Krallık
- Batı ABD 2

Bir bölgesel ILB ASE 'de dağıtılan uygulamalar, aynı bölgedeki diğer bölgeler kesintiye neden olsa bile bu ASE üzerinde trafik çalıştırmaya ve hizmet vermeye devam edecektir.  Aşağıdakiler dahil, çalışma zamanı olmayan davranışları olabilir; uygulama hizmeti planı ölçekleme, uygulama oluşturma, uygulama yapılandırma ve uygulama yayımlama, diğer kullanılabilirlik bölgelerinde bir kesinti yaşınızdan etkilenebilir. Bir bölgesel İlb Ao 'nun bölge sabitlenmiş dağıtımı, zaten dağıtılan uygulamalar için devam eden çalışma süresini sağlar.

## <a name="how-to-deploy-an-app-service-environment-in-an-availability-zone"></a>Bir kullanılabilirlik bölgesinde App Service Ortamı dağıtma ##

ARM şablonları kullanılarak zonal ıLB ASEs oluşturulması gerekir. Bir bölgesel ILB akolu bir ARM şablonu aracılığıyla oluşturulduktan sonra, Azure Portal ve CLI aracılığıyla görüntülenebilir ve bunlarla etkileşim yapılabilir.  ARM şablonu yalnızca bir bölgesel ILB Ao 'nun ilk oluşturulması için gereklidir.

Bir ARM şablonunda gereken tek değişiklik yeni ***Zones** _ özelliğidir. ILB asa 'nın sabitlendiği mantıksal kullanılabilirlik bölgesine bağlı olarak _ *_Zones_** özelliği, "1", "2" veya "3" değerine ayarlanmalıdır.

Aşağıdaki örnek ARM şablon parçacığı, ıLB ATıCı 'in bölge 2 ' ye sabitlenebilmelidir. 

```
   "resources": [
      {
         "type": "Microsoft.Web/hostingEnvironments",
         "kind": "ASEV2",
         "name": "yourASENameHere",
         "apiVersion": "2015-08-01",
         "location": "your location here",
         "zones": [
            "2"
         ],
         "properties": {
         "name": "yourASENameHere",
         "location": "your location here",
         "ipSslAddressCount": 0,
         "internalLoadBalancingMode": "3",
         "dnsSuffix": "contoso-internal.com",
         "virtualNetwork": {
             "Id": "/subscriptions/your-subscription-id-here/resourceGroups/your-resource-group-here/providers/Microsoft.Network/virtualNetworks/your-vnet-name-here",
             "Subnet": "yourSubnetNameHere"
          }
         }
      }
    ]
```

Uygulamalar diliminizi gereksiz hale getirmek için iki bölgesel ILB ases dağıtmanız gerekir. İki bölgesel ILB ases, ayrı kullanılabilirlik bölgelerinde olmalıdır. Daha sonra uygulamalarınızı her bir ıLB ASE 'ye dağıtmanız gerekir. Uygulamalarınız oluşturulduktan sonra bir yük dengeleme çözümü yapılandırmanız gerekir. Önerilen çözüm, bölgesel ILB ASE 'lerin yukarı akış [Application Gateway yedekli bir bölge](../../application-gateway/application-gateway-autoscaling-zone-redundant.md) dağıtmaktır. 

## <a name="in-region-data-residency"></a>Bölge veri yerleşimi bölümünde ##

Bir kullanılabilirlik bölgesinde dağıtılan ILB 'ler, müşteri verilerini yalnızca bölgesel ILB ASE 'nin dağıtıldığı bölge içinde depolar. Her iki Web sitesi dosyası içeriğinin yanı sıra App Service depolanan müşteri tarafından sağlanan ayarlar ve gizlilikler, bölgesel ILB asa 'nın dağıtıldığı bölge içinde kalır.

Müşteriler, "kullanılabilirlik bölgesinde bir App Service Ortamı dağıtma" bölümünde daha önce özetlenen adımları izleyerek tek bölge verilerinin yeniden kullanılabilmesini sağlar. Bu adımlara göre bir App Service Ortamı yapılandırarak, bir kullanılabilirlik alanında dağıtılan bir App Service Ortamı, [Azure Güven Merkezi](https://azuredatacentermap.azurewebsites.net/)belirtilen dahil olmak üzere bölge veri yerleşimi gereksinimlerini karşılar.

Müşteriler, aşağıdaki adımları izleyerek bir App Service Ortamı verileri tek bir bölgede depolamak üzere düzgün bir şekilde yapılandırıldığını doğrulayabilir: 

1. [Kaynak Gezgini](https://resources.azure.com)kullanarak App Service ortamı ARM kaynağına gidin.  ASEs *sağlayıcılar/Microsoft. Web/hostingEnvironments* altında listelenmiştir.
2. ARM JSON sözdiziminin görünümünde bir *bölgeler* özelliği varsa ve bu, "1", "2" veya "3" değerine sahip tek DEĞERLI bir JSON dizisi içeriyorsa, ASE bölge tarafından dağıtılır ve müşteri verileri aynı bölgede kalır.
2. Bir *bölgeler* özelliği yoksa veya özelliğin daha önce belirtildiği gibi geçerli bir bölge değeri yoksa, Ao, bölge dışı olarak dağıtılır ve müşteri verileri, aynı bölgede özel olarak depolanmaz.
