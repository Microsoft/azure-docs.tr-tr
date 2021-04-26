---
title: Azure Cloud Services (genişletilmiş destek) rol şeması | Microsoft Docs
description: Cloud Services için rol şemasıyla ilgili bilgiler (genişletilmiş destek)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2567f5bb817a34f6274d5e265a266d67a9c81413
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98744705"
---
# <a name="azure-cloud-services-extended-support-config-role-schema"></a>Azure Cloud Services (genişletilmiş destek) yapılandırma rolü şeması

`Role`Yapılandırma dosyasının öğesi, hizmette her bir rol için dağıtılacak rol örneği sayısını, herhangi bir yapılandırma ayarlarının değerlerini ve bir rolle ilişkili tüm sertifikalar için parmak izlerini belirtir.

Azure hizmet yapılandırma şeması hakkında daha fazla bilgi için bkz. [Cloud Service (genişletilmiş destek) yapılandırma şeması](schema-cscfg-file.md). Azure hizmet tanımı şeması hakkında daha fazla bilgi için bkz. [Cloud Service (genişletilmiş destek) Tanım Şeması](schema-csdef-file.md).

##  <a name="role-element"></a><a name="Role"></a> rol öğesi
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

Aşağıdaki tabloda öğesi için öznitelikler açıklanmaktadır `Role` .

| Öznitelik | Açıklama |
| --------- | ----------- |
| name   | Gereklidir. Rolün adını belirtir. Ad, hizmet tanımı dosyasındaki rol için girilen adla eşleşmelidir.|
| vmName | İsteğe bağlı. Bir sanal makinenin DNS adını belirtir. Ad 10 karakter veya daha az olmalıdır.|

Aşağıdaki tablo, öğesinin alt öğelerini açıklar `Role` .

| Öğe | Açıklama |
| ------- | ----------- |
| Örnekler | Gereklidir. Rol için dağıtılacak örneklerin sayısını belirtir. Örnek sayısı öznitelik için bir tamsayı ile tanımlanır `count` .|
| Ayar   | İsteğe bağlı. Bir rolün ayarlar koleksiyonundaki bir ayar adı ve değeri belirtir. Ayar adı, özniteliği için bir dize tarafından tanımlanır `name` ve ayar değeri öznitelik için bir dize tarafından tanımlanır `value` .|
| Sertifika | İsteğe bağlı. Rolüyle ilişkilendirilecek bir hizmet sertifikasının adını, parmak izini ve algoritmasını belirtir. Sertifika adı, öznitelik için bir dize tarafından tanımlanır `name` . Sertifika parmak izi, öznitelik için boşluk içermeyen bir onaltılık sayı dizesi tarafından tanımlanır `thumbprint` . Onaltılık sayılar, rakamlar ve büyük Alfa karakterleri kullanılarak temsil etmelidir. Sertifika algoritması, özniteliği için bir dize tarafından tanımlanır `thumbprintAlgorithm` .|

## <a name="see-also"></a>Ayrıca bkz.
[Bulut hizmeti (genişletilmiş destek) yapılandırma şeması](schema-cscfg-file.md).