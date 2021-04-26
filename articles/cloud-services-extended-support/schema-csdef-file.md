---
title: Azure Cloud Services (genişletilmiş destek) Tanım Şeması (csdef dosyası) | Microsoft Docs
description: Cloud Services için Tanım Şeması (csdef) ile ilgili bilgiler (genişletilmiş destek)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: d9bf1b54f1bfeebacbb406a50c8496817857204c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102507577"
---
# <a name="azure-cloud-services-extended-support-definition-schema-csdef-file"></a>Azure Cloud Services (genişletilmiş destek) Tanım Şeması (csdef dosyası)

Hizmet tanımı dosyası bir uygulama için hizmet modelini tanımlar. Dosya, bir bulut hizmeti için kullanılabilir olan roller için tanımları içerir, hizmet uç noktalarını belirtir ve hizmet için yapılandırma ayarlarını oluşturur. Yapılandırma ayarı değerleri, [bulut hizmeti (genişletilmiş destek) yapılandırma şeması](schema-cscfg-file.md)bölümünde açıklandığı gibi hizmet yapılandırma dosyasında ayarlanır.

Varsayılan olarak, Azure Tanılama yapılandırma şema dosyası `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` dizine yüklenir. `<version>` [Azure SDK](https://www.windowsazure.com/develop/downloads/)'nın yüklü sürümüyle değiştirin.

Hizmet tanım dosyası için varsayılan uzantı csdef ' dir.

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
| upgradeDomainCount      | İsteğe bağlı. Bu hizmetteki rollerin ayrıldığı yükseltme etki alanı sayısını belirtir. Rol örnekleri, hizmet dağıtıldığında bir yükseltme etki alanına ayrılır. Daha fazla bilgi için bkz. [bulut hizmeti rolü veya dağıtımı güncelleştirme](sample-update-cloud-service.md) ve [sanal makinelerin kullanılabilirliğini yönetme](../virtual-machines/availability.md) hakkında, en fazla 20 yükseltme etki alanı belirtebilirsiniz. Belirtilmemişse, varsayılan yükseltme etki alanı sayısı 5 ' tir.|

## <a name="see-also"></a>Ayrıca bkz.

[Azure Cloud Services (genişletilmiş destek) yapılandırma şeması (cscfg dosyası)](schema-cscfg-file.md).