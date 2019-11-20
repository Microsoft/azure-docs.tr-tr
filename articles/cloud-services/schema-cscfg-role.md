---
title: Azure Cloud Services rol şeması | Microsoft Docs
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 12
author: georgewallace
ms.author: gwallace
ms.openlocfilehash: 481301333ada39297bf2813bbea5f096c2abd3ad
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360664"
---
# <a name="azure-cloud-services-config-role-schema"></a>Azure Cloud Services yapılandırma rolü şeması

Yapılandırma `Role` dosyasının öğesi, hizmette her bir rol için dağıtılacak rol örneği sayısını, herhangi bir yapılandırma ayarlarının değerlerini ve bir rolle ilişkili tüm sertifikalar için parmak izlerini belirtir.

Azure hizmet yapılandırma şeması hakkında daha fazla bilgi için bkz. [Cloud Service (klasik) yapılandırma şeması](schema-cscfg-file.md). Azure hizmet tanımı şeması hakkında daha fazla bilgi için bkz. [bulut hizmeti (klasik) Tanım Şeması](schema-csdef-file.md).

##  <a name="Role"></a>Rol öğesi
Aşağıdaki örnek `Role` öğesi ve onun alt öğelerini gösterir.

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Aşağıdaki tabloda `Role` öğesi için öznitelikler açıklanmaktadır.

| Öznitelik | Açıklama |
| --------- | ----------- |
| name   | Gerekli. Rolün adını belirtir. Ad, hizmet tanımı dosyasındaki rol için girilen adla eşleşmelidir.|
| vmName | İsteğe bağlı. Bir sanal makinenin DNS adını belirtir. Ad 10 karakter veya daha az olmalıdır.|

Aşağıdaki tablo, `Role` öğesinin alt öğelerini açıklar.

| Öğe | Açıklama |
| ------- | ----------- |
| Örnekler | Gerekli. Rol için dağıtılacak örneklerin sayısını belirtir. Örnek sayısı `count` öznitelik için bir tamsayı ile tanımlanır.|
| Ayar   | İsteğe bağlı. Bir rolün ayarlar koleksiyonundaki bir ayar adı ve değeri belirtir. Ayar adı, `name` özniteliği için bir dize tarafından tanımlanır ve ayar değeri `value` öznitelik için bir dize tarafından tanımlanır.|
| Sertifika | İsteğe bağlı. Rolüyle ilişkilendirilecek bir hizmet sertifikasının adını, parmak izini ve algoritmasını belirtir. Sertifika adı, `name` öznitelik için bir dize tarafından tanımlanır. Sertifika parmak izi, `thumbprint` öznitelik için boşluk içermeyen bir onaltılık sayı dizesi tarafından tanımlanır. Onaltılık sayılar, rakamlar ve büyük Alfa karakterleri kullanılarak temsil etmelidir. Sertifika algoritması, `thumbprintAlgorithm` özniteliği için bir dize tarafından tanımlanır.|

## <a name="see-also"></a>Ayrıca Bkz.
[Bulut hizmeti (klasik) yapılandırma şeması](schema-cscfg-file.md)