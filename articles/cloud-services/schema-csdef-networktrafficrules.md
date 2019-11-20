---
title: Azure Cloud Services def. NetworkTrafficRules şeması | Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 351b369f-365e-46c1-82ce-03fc3655cc88
caps.latest.revision: 17
author: georgewallace
ms.author: gwallace
manager: gwallace
ms.openlocfilehash: e99b9f0f601841fe6ff32eba0a43bfafd652e941
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945943"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Azure Cloud Services Definition NetworkTrafficRules şeması
`NetworkTrafficRules` Düğüm, rollerinin birbirleriyle nasıl iletişim kuracağını belirten hizmet tanımı dosyasındaki isteğe bağlı bir öğedir. Bu, belirli rolün iç uç noktalarına hangi rollerin erişebileceğini sınırlandırır. `NetworkTrafficRules` Tek başına bir öğe değildir; bir hizmet tanımı dosyasında iki veya daha fazla rolle birleştirilir.

Hizmet tanım dosyası için varsayılan uzantı. csdef ' dir.

> [!NOTE]
>  `NetworkTrafficRules` Düğüm yalnızca Azure SDK 1,3 veya üzeri sürümler kullanılarak kullanılabilir.

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
Hizmet tanımı dosyasının düğümü, bu konunun sonraki bölümlerinde ayrıntılı olarak açıklanan bu öğeleri içerir: `NetworkTrafficRules`

[NetworkTrafficRules öğesi](#NetworkTrafficRules)

[OnlyAllowTrafficTo öğesi](#OnlyAllowTrafficTo)

[Destinations öğesi](#Destinations)

[RoleEndpoint öğesi](#RoleEndpoint)

AllowAllTraffic öğesi

[WhenSource öğesi](#WhenSource)

[FromRole öğesi](#FromRole)

##  <a name="NetworkTrafficRules"></a>NetworkTrafficRules öğesi
Öğesi `NetworkTrafficRules` , hangi rollerin başka bir roldeki hangi uç noktayla iletişim kurabildiğini belirtir. Bir hizmet, bir `NetworkTrafficRules` tanım içerebilir.

##  <a name="OnlyAllowTrafficTo"></a>OnlyAllowTrafficTo öğesi
Öğesi `OnlyAllowTrafficTo` , hedef uç noktaları ve bunlarla iletişim kurabilen roller koleksiyonunu açıklar. Birden çok `OnlyAllowTrafficTo` düğüm belirtebilirsiniz.

##  <a name="Destinations"></a>Destinations öğesi
Öğesi `Destinations` , ile iletilenenden bir roleendpoints koleksiyonu tanımlar.

##  <a name="RoleEndpoint"></a>RoleEndpoint öğesi
Öğesi `RoleEndpoint` , ile iletişime izin vermek üzere bir rol üzerinde bir uç nokta tanımlar. Rol üzerinde birden fazla `RoleEndpoint` uç nokta varsa birden çok öğe belirtebilirsiniz.

| Öznitelik      | Type     | Açıklama |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Gerekli. Trafiğe izin verilecek uç noktanın adı.|
| `roleName`     | `string` | Gerekli. İletişime izin verilecek Web rolünün adı.|

## <a name="allowalltraffic-element"></a>AllowAllTraffic öğesi
Öğesi, tüm rollerin `Destinations` düğümde tanımlanan uç noktalarla iletişim kurmasına izin veren bir kuraldır. `AllowAllTraffic`

##  <a name="WhenSource"></a>WhenSource öğesi
Öğesi, `Destinations` düğümünde tanımlanan uç noktalarla iletişim kurabildiğinden rol koleksiyonunu açıklar. `WhenSource`

| Öznitelik | Type     | Açıklama |
| --------- | -------- | ----------- |
| `matches` | `string` | Gerekli. İletişime izin verirken uygulanacak kuralı belirtir. Geçerli olan tek değer şu anda `AnyRule`.|
  
##  <a name="FromRole"></a>FromRole öğesi
Öğesi, `Destinations` düğümünde tanımlanan uç noktalarla iletişim kurabilen rolleri belirtir. `FromRole` Uç noktalarla iletişim kurabilen `FromRole` birden fazla rol varsa birden çok öğe belirtebilirsiniz.

| Öznitelik  | Type     | Açıklama |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Gerekli. İletişime izin verilecek rolün adı.|

## <a name="see-also"></a>Ayrıca Bkz.
[Bulut hizmeti (klasik) Tanım Şeması](schema-csdef-file.md)
