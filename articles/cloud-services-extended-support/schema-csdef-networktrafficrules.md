---
title: Azure Cloud Services (genişletilmiş destek) def. NetworkTrafficRules şeması | Microsoft Docs
description: Cloud Services ilişkili ağ trafiği kurallarıyla ilgili bilgiler (genişletilmiş destek)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 0064794701e87419da086c458673f7ccee4f37dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98744797"
---
# <a name="azure-cloud-services-extended-support-definition-networktrafficrules-schema"></a>Azure Cloud Services (genişletilmiş destek) tanım NetworkTrafficRules şeması

`NetworkTrafficRules`Düğüm, rollerinin birbirleriyle nasıl iletişim kuracağını belirten hizmet tanımı dosyasındaki isteğe bağlı bir öğedir. Bu, belirli rolün iç uç noktalarına hangi rollerin erişebileceğini sınırlandırır. `NetworkTrafficRules`Tek başına bir öğe değildir; bir hizmet tanımı dosyasında iki veya daha fazla rolle birleştirilir.

Hizmet tanım dosyası için varsayılan uzantı csdef ' dir.

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

[AllowAllTraffic öğesi](#AllowAllTraffic)

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

## <a name="allowalltraffic-element"></a><a name="AllowAllTraffic"></a> AllowAllTraffic öğesi
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

## <a name="see-also"></a>Ayrıca bkz.
[Bulut hizmeti (genişletilmiş destek) Tanım Şeması](schema-csdef-file.md).




