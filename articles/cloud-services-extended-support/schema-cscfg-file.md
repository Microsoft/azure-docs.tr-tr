---
title: Azure Cloud Services (genişletilmiş destek) Tanım Şeması (. cscfg dosyası) | Microsoft Docs
description: Cloud Services için tanım şemasıyla ilgili bilgiler (genişletilmiş destek)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 3aac5ca8d146f0cd74f483846d22fdf0052f86f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98744697"
---
# <a name="azure-cloud-services-extended-support-config-schema-cscfg-file"></a>Azure Cloud Services (genişletilmiş destek) yapılandırma şeması (cscfg dosyası)

Hizmet yapılandırma dosyası, hizmette her bir rol için dağıtılacak rol örneği sayısını, herhangi bir yapılandırma ayarlarının değerlerini ve bir rolle ilişkili tüm sertifikalar için parmak izlerini belirtir. Hizmet bir sanal ağın parçasıysa, hizmet yapılandırma dosyasında ve sanal ağ yapılandırma dosyasında ağ için yapılandırma bilgilerinin sağlanması gerekir. Hizmet yapılandırma dosyası için varsayılan uzantı cscfg 'dir.

Hizmet modeli, [bulut hizmeti (genişletilmiş destek) Tanım Şeması](schema-csdef-file.md)tarafından açıklanmıştır.

Varsayılan olarak, Azure Tanılama yapılandırma şema dosyası `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` dizine yüklenir. `<version>` [Azure SDK](https://azure.microsoft.com/downloads/)'nın yüklü sürümüyle değiştirin.

Bir hizmette rolleri yapılandırma hakkında daha fazla bilgi için bkz. [bulut hizmeti modeli nedir?](../cloud-services/cloud-services-model-and-package.md).

## <a name="basic-service-configuration-schema"></a>Temel hizmet yapılandırma şeması
Hizmet yapılandırma dosyasının temel biçimi aşağıdaki gibidir.

```xml
<ServiceConfiguration serviceName="<service-name>" osFamily="<osfamily-number>" osVersion="<os-version>" schemaVersion="<schema-version>">

  <Role …>
    …
  </Role>

  <NetworkConfiguration>
    …
  </NetworkConfiguration>

</ServiceConfiguration>
```

## <a name="schema-definitions"></a>Şema tanımları
Aşağıdaki konular, öğesinin şemasını anlatmaktadır `ServiceConfiguration` :

- [Rol Şeması](schema-cscfg-role.md)
- [NetworkConfiguration Şeması](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Hizmet yapılandırması ad alanı
Hizmet yapılandırma dosyasının XML ad alanı: `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration` .

##  <a name="serviceconfiguration-element"></a><a name="ServiceConfiguration"></a> ServiceConfiguration öğesi
`ServiceConfiguration`Öğesi, hizmet yapılandırma dosyasının en üst düzey öğesidir.

Aşağıdaki tablo, öğesinin özniteliklerini açıklar `ServiceConfiguration` . Tüm öznitelik değerleri dize türleridir.

| Öznitelik | Açıklama |
| --------- | ----------- |
|HizmetAdı|Gereklidir. Bulut hizmetinin adı. Burada verilen ad, hizmet tanımı dosyasında belirtilen adla eşleşmelidir.|
|osFamily|İsteğe bağlı. Bulut hizmetindeki rol örneklerinde çalıştırılacak Konuk işletim sistemini belirtir. Desteklenen Konuk işletim sistemi sürümleri hakkında daha fazla bilgi için bkz. [Azure Konuk işletim sistemi sürümleri ve SDK uyumluluk matrisi](../cloud-services/cloud-services-guestos-update-matrix.md).<br /><br /> Bir `osFamily` değer eklemezseniz ve `osVersion` özniteliği belirli bir konuk işletim sistemi sürümüne ayarlamayın, varsayılan 1 değeri kullanılır.|
|osVersion|İsteğe bağlı. Bulut hizmetindeki rol örneklerinde çalıştırılacak Konuk işletim sisteminin sürümünü belirtir. Konuk işletim sistemi sürümleri hakkında daha fazla bilgi için bkz. [Azure Konuk işletim sistemi sürümleri ve SDK uyumluluk matrisi](../cloud-services/cloud-services-guestos-update-matrix.md).<br /><br /> Konuk işletim sisteminin en son sürüme otomatik olarak yükseltilmesi gerektiğini belirtebilirsiniz. Bunu yapmak için `osVersion` özniteliğinin değerini olarak ayarlayın `*` . Olarak ayarlandığında `*` , rol örnekleri belirtilen işletim sistemi ailesi Için Konuk işletim sisteminin en son sürümü kullanılarak dağıtılır ve konuk işletim sisteminin yeni sürümleri serbest bırakıldığında otomatik olarak yükseltilir.<br /><br /> Belirli bir sürümü el ile belirtmek için `Configuration String` [Azure Konuk Işletim sistemi SÜRÜMLERI ve SDK uyumluluk matrisi](../cloud-services/cloud-services-guestos-update-matrix.md)' nin **gelecek, güncel ve geçici Konuk işletim sistemi sürümleri** bölümündeki tablosundan öğesini kullanın.<br /><br /> Özniteliği için varsayılan değer `osVersion` `*` .|
|schemaVersion|İsteğe bağlı. Hizmet yapılandırma şemasının sürümünü belirtir. Şema sürümü, Visual Studio 'nun birden fazla SDK sürümü yan yana yüklenirse şema doğrulaması için kullanılacak doğru SDK araçlarını seçmesine olanak sağlar. Şema ve sürüm uyumluluğu hakkında daha fazla bilgi için bkz. [Azure Konuk işletim sistemi sürümleri ve SDK uyumluluk matrisi](../cloud-services/cloud-services-guestos-update-matrix.md)|

Hizmet yapılandırma dosyasında bir `ServiceConfiguration` öğe bulunmalıdır. `ServiceConfiguration`Öğesi herhangi bir sayıda `Role` öğe ve sıfır veya 1 `NetworkConfiguration` öğe içerebilir.

## <a name="see-also"></a>Ayrıca bkz.

[Azure Cloud Services (genişletilmiş destek) Tanım Şeması (csdef dosyası)](schema-csdef-file.md)