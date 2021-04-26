---
title: Azure Cloud Services (klasik) def. NetworkTrafficRules şeması | Microsoft Docs
description: Bir rolün iç uç noktalarına erişebilen rolleri sınırlayan NetworkTrafficRules hakkında bilgi edinin. Bir hizmet tanım dosyasındaki rollerle birleştirir.
ms.topic: article
ms.service: cloud-services
ms.subservice: deployment-files
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 499b0f2b7e02dabbde906e0d347a17389b2e5753
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934113"
---
# <a name="azure-cloud-services-classic-definition-networktrafficrules-schema"></a>Azure Cloud Services (klasik) tanım NetworkTrafficRules şeması

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).

`NetworkTrafficRules`Düğüm, rollerinin birbirleriyle nasıl iletişim kuracağını belirten hizmet tanımı dosyasındaki isteğe bağlı bir öğedir. Bu, belirli rolün iç uç noktalarına hangi rollerin erişebileceğini sınırlandırır. `NetworkTrafficRules`Tek başına bir öğe değildir; bir hizmet tanımı dosyasında iki veya daha fazla rolle birleştirilir.

Hizmet tanım dosyası için varsayılan uzantı. csdef ' dir.

> [!NOTE]
>  `NetworkTrafficRules`Düğüm yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Ağ trafiği kuralları için temel hizmet tanımı şeması
Ağ trafiği tanımlarını içeren bir hizmet tanımı dosyasının temel biçimi aşağıdaki gibidir.

```xml
<ServiceDefinition …>
   <NetworkTrafficRules>
      <OnlyAllowTrafficTo>
         <Destinations>
            <RoleEndpoint endpointName="<name-of-the-endpoint>" roleName="<name-of-the-role-containing-the-endpoint>"/>
         </Destinations>
         <AllowAllTraffic/>
         <WhenSource matches="[AnyRule]">
            <FromRole roleName="<name-of-the-role-to-allow-traffic-from>"/>
         </WhenSource>
      </OnlyAllowTrafficTo>
   </NetworkTrafficRules>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Şema öğeleri
`NetworkTrafficRules`Hizmet tanımı dosyasının düğümü, bu konunun sonraki bölümlerinde ayrıntılı olarak açıklanan bu öğeleri içerir:

[NetworkTrafficRules öğesi](#NetworkTrafficRules)

[OnlyAllowTrafficTo öğesi](#OnlyAllowTrafficTo)

[Destinations öğesi](#Destinations)

[RoleEndpoint öğesi](#RoleEndpoint)

AllowAllTraffic öğesi

[WhenSource öğesi](#WhenSource)

[FromRole öğesi](#FromRole)

##  <a name="networktrafficrules-element"></a><a name="NetworkTrafficRules"></a> NetworkTrafficRules öğesi
Öğesi, hangi `NetworkTrafficRules` rollerin başka bir roldeki hangi uç noktayla iletişim kurabildiğini belirtir. Bir hizmet, bir `NetworkTrafficRules` tanım içerebilir.

##  <a name="onlyallowtrafficto-element"></a><a name="OnlyAllowTrafficTo"></a> OnlyAllowTrafficTo öğesi
`OnlyAllowTrafficTo`Öğesi, hedef uç noktaları ve bunlarla iletişim kurabilen roller koleksiyonunu açıklar. Birden çok düğüm belirtebilirsiniz `OnlyAllowTrafficTo` .

##  <a name="destinations-element"></a><a name="Destinations"></a> Destinations öğesi
`Destinations`Öğesi, ile iletilenenden bir RoleEndpoints koleksiyonu tanımlar.

##  <a name="roleendpoint-element"></a><a name="RoleEndpoint"></a> RoleEndpoint öğesi
`RoleEndpoint`Öğesi, ile iletişime izin vermek üzere bir rol üzerinde bir uç nokta tanımlar. `RoleEndpoint`Rol üzerinde birden fazla uç nokta varsa birden çok öğe belirtebilirsiniz.

| Öznitelik      | Tür     | Description |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Gereklidir. Trafiğe izin verilecek uç noktanın adı.|
| `roleName`     | `string` | Gereklidir. İletişime izin verilecek Web rolünün adı.|

## <a name="allowalltraffic-element"></a>AllowAllTraffic öğesi
`AllowAllTraffic`Öğesi, tüm rollerin düğümde tanımlanan uç noktalarla iletişim kurmasına izin veren bir kuraldır `Destinations` .

##  <a name="whensource-element"></a><a name="WhenSource"></a> WhenSource öğesi
`WhenSource`Öğesi, düğümünde tanımlanan uç noktalarla iletişim kurabildiğinden rol koleksiyonunu açıklar `Destinations` .

| Öznitelik | Tür     | Description |
| --------- | -------- | ----------- |
| `matches` | `string` | Gereklidir. İletişime izin verirken uygulanacak kuralı belirtir. Geçerli olan tek değer şu anda `AnyRule` .|
  
##  <a name="fromrole-element"></a><a name="FromRole"></a> FromRole öğesi
`FromRole`Öğesi, düğümünde tanımlanan uç noktalarla iletişim kurabilen rolleri belirtir `Destinations` . `FromRole`Uç noktalarla iletişim kurabilen birden fazla rol varsa birden çok öğe belirtebilirsiniz.

| Öznitelik  | Tür     | Description |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Gereklidir. İletişime izin verilecek rolün adı.|

## <a name="see-also"></a>Ayrıca Bkz.
[Bulut hizmeti (klasik) Tanım Şeması](schema-csdef-file.md)




