---
title: Azure Cloud Services (klasik) Tanım Şeması (. csdef dosyası) | Microsoft Docs
description: Hizmet tanımı (. csdef) dosyası, hizmet için kullanılabilir rolleri, uç noktaları ve yapılandırma değerlerini içeren bir uygulama için bir hizmet modeli tanımlar.
ms.topic: article
ms.service: cloud-services
ms.subservice: deployment-files
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: f201bc05795fa6aece256f3d3b4bd650385fef48
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934147"
---
# <a name="azure-cloud-services-classic-definition-schema-csdef-file"></a>Azure Cloud Services (klasik) Tanım Şeması (. csdef dosyası)

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).

Hizmet tanımı dosyası bir uygulama için hizmet modelini tanımlar. Dosya, bir bulut hizmeti için kullanılabilir olan roller için tanımları içerir, hizmet uç noktalarını belirtir ve hizmet için yapılandırma ayarlarını oluşturur. Yapılandırma ayarı değerleri, [bulut hizmeti (klasik) yapılandırma şemasında](/previous-versions/azure/reference/ee758710(v=azure.100))açıklandığı gibi hizmet yapılandırma dosyasında ayarlanır.

Varsayılan olarak, Azure Tanılama yapılandırma şema dosyası `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` dizine yüklenir. `<version>` [Azure SDK](https://www.windowsazure.com/develop/downloads/)'nın yüklü sürümüyle değiştirin.

Hizmet tanım dosyası için varsayılan uzantı. csdef ' dir.

## <a name="basic-service-definition-schema"></a>Temel hizmet tanımı şeması
Hizmet tanımı dosyası bir `ServiceDefinition` öğe içermelidir. Hizmet tanımında en az bir rol ( `WebRole` veya `WorkerRole` ) öğesi bulunmalıdır. Tek bir tanımda tanımlanmış en fazla 25 rol içerebilir ve rol türlerini karıştırabilirsiniz. Hizmet tanımı Ayrıca, `NetworkTrafficRules` belirtilen iç uç noktalarla hangi rollerin iletişim kurabildiğini kısıtlayan isteğe bağlı öğeyi de içerir. Hizmet tanımı, `LoadBalancerProbes` uç noktaların müşteri tanımlı sistem durumu araştırmalarını içeren isteğe bağlı öğeyi de içerir.

Hizmet tanım dosyasının temel biçimi aşağıdaki gibidir.

```xml
<ServiceDefinition name="<service-name>" topologyChangeDiscovery="<change-type>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" upgradeDomainCount="<number-of-upgrade-domains>" schemaVersion="<version>">
  
  <LoadBalancerProbes>
         …
  </LoadBalancerProbes>
  
  <WebRole …>
         …
  </WebRole>
  
  <WorkerRole …>
         …
  </WorkerRole>
  
  <NetworkTrafficRules>
         …
  </NetworkTrafficRules>

</ServiceDefinition>
```

## <a name="schema-definitions"></a>Şema tanımları
Aşağıdaki konular şemayı anlatmaktadır:

- [LoadBalancerProbe Şeması](schema-csdef-loadbalancerprobe.md)
- [WebRole Şeması](schema-csdef-webrole.md)
- [WorkerRole Şeması](schema-csdef-workerrole.md)
- [NetworkTrafficRules Şeması](schema-csdef-networktrafficrules.md)

##  <a name="servicedefinition-element"></a><a name="ServiceDefinition"></a> ServiceDefinition öğesi
`ServiceDefinition`Öğesi, hizmet tanım dosyasının en üst düzey öğesidir.

Aşağıdaki tablo, öğesinin özniteliklerini açıklar `ServiceDefinition` .

| Öznitelik               | Açıklama |
| ----------------------- | ----------- |
| name                    |Gereklidir. Hizmetin adı. Ad, hizmet hesabı içinde benzersiz olmalıdır.|
| Topologyıchangediscovery | İsteğe bağlı. Topoloji değişiklik bildiriminin türünü belirtir. Olası değerler şunlardır:<br /><br /> -   `Blast` -Güncelleştirmeyi tüm rol örneklerine en kısa sürede gönderir. Seçeneğini belirlerseniz, rolün yeniden başlatılmadan önce rolün topoloji güncelleştirmesini işleyebilmesi gerekir.<br />-   `UpgradeDomainWalk` – Güncelleştirmeyi, önceki örnek güncelleştirmeyi başarıyla kabul ettikten sonra her bir rol örneğine sıralı bir şekilde gönderir.|
| schemaVersion           | İsteğe bağlı. Hizmet tanımı şemasının sürümünü belirtir. Şema sürümü, Visual Studio 'nun birden fazla SDK sürümü yan yana yüklenirse şema doğrulaması için kullanılacak doğru SDK araçlarını seçmesine olanak sağlar.|
| upgradeDomainCount      | İsteğe bağlı. Bu hizmetteki rollerin ayrıldığı yükseltme etki alanı sayısını belirtir. Rol örnekleri, hizmet dağıtıldığında bir yükseltme etki alanına ayrılır. Daha fazla bilgi için bkz. [bulut hizmeti rolü veya dağıtımı güncelleştirme](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment), [sanal makinelerin kullanılabilirliğini yönetme](../virtual-machines/availability.md) ve [bulut hizmeti modeli nedir](./cloud-services-model-and-package.md).<br /><br /> En fazla 20 yükseltme etki alanı belirtebilirsiniz. Belirtilmemişse, varsayılan yükseltme etki alanı sayısı 5 ' tir.|