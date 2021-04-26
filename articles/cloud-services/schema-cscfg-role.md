---
title: Azure Cloud Services (klasik) rol şeması | Microsoft Docs
description: Bir hizmet yapılandırma dosyasının rol öğesi, her rol, yapılandırma değeri ve sertifika parmak izleri için kaç tane rol örneğinin dağıtılacağını belirtir.
ms.topic: article
ms.service: cloud-services
ms.subservice: deployment-files
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: dd46a37ada1fb96797faee6c8491359561e065d7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934164"
---
# <a name="azure-cloud-services-classic-config-role-schema"></a>Azure Cloud Services (klasik) yapılandırma rolü şeması

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).

`Role`Yapılandırma dosyasının öğesi, hizmette her bir rol için dağıtılacak rol örneği sayısını, herhangi bir yapılandırma ayarlarının değerlerini ve bir rolle ilişkili tüm sertifikalar için parmak izlerini belirtir.

Azure hizmet yapılandırma şeması hakkında daha fazla bilgi için bkz. [Cloud Service (klasik) yapılandırma şeması](schema-cscfg-file.md). Azure hizmet tanımı şeması hakkında daha fazla bilgi için bkz. [bulut hizmeti (klasik) Tanım Şeması](schema-csdef-file.md).

##  <a name="role-element"></a><a name="Role"></a> Rol öğesi
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

## <a name="see-also"></a>Ayrıca Bkz.
[Bulut hizmeti (klasik) yapılandırma şeması](schema-cscfg-file.md)