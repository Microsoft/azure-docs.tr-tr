---
title: Azure Service Bus abonelik filtrelerini ayarla | Microsoft Docs
description: Bu makalede, Azure Service Bus konu aboneliklerinde filtreleri ve eylemleri tanımlamaya yönelik örnekler sağlanmaktadır.
ms.topic: how-to
ms.date: 02/17/2021
ms.openlocfilehash: bcbb72901ed8e2dfe0932163ee18683e0011ce70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100654576"
---
# <a name="set-subscription-filters-azure-service-bus"></a>Abonelik filtrelerini ayarla (Azure Service Bus)
Bu makalede Service Bus konu aboneliklerinde filtre ayarlama konusunda birkaç örnek sunulmaktadır. Filtreler hakkında kavramsal bilgiler için bkz. [Filtreler](topic-filters.md).

## <a name="filter-on-system-properties"></a>Sistem özelliklerinde filtrele
Bir filtre içindeki bir sistem özelliğine başvurmak için şu biçimi kullanın: `sys.<system-property-name>` . 

```csharp
sys.label LIKE '%bus%'`
sys.messageid = 'xxxx'
sys.correlationid like 'abc-%'
```

## <a name="filter-on-message-properties"></a>İleti özelliklerini filtrele
İleti özelliklerini bir filtrede kullanma örnekleri aşağıda verilmiştir. Yalnızca veya kullanarak ileti özelliklerine erişebilirsiniz `user.property-name` `property-name` .

```csharp
MessageProperty = 'A'
SuperHero like 'SuperMan%'
```

## <a name="filter-on-message-properties-with-special-characters"></a>İleti özelliklerini özel karakterlerle filtrele
İleti özelliğinin adı özel karakterler içeriyorsa, `"` özellik adını çevrelemek için Double tırnakları () kullanın. Örneğin, özellik adı ise, `"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName"` filtrede aşağıdaki sözdizimini kullanın. 

```csharp
"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName" = 'account'
```

## <a name="filter-on-message-properties-with-numeric-values"></a>İleti özelliklerini sayısal değerlerle filtrele
Aşağıdaki örneklerde, filtrelerdeki sayısal değerlerle özellikleri nasıl kullanabileceğiniz gösterilmektedir. 

```csharp
MessageProperty = 1
MessageProperty > 1
MessageProperty > 2.08
MessageProperty = 1 AND MessageProperty2 = 3
MessageProperty = 1 OR MessageProperty2 = 3
```

## <a name="parameter-based-filters"></a>Parametre tabanlı filtreler
Parametre tabanlı filtreleri kullanmanın birkaç örneği aşağıda verilmiştir. Bu örneklerde, `DataTimeMp` türünde bir ileti özelliği `DateTime` ve bir `@dtParam` nesne olarak filtreye geçirilen parametredir `DateTime` .

```csharp
DateTimeMp < @dtParam
DateTimeMp > @dtParam

(DateTimeMp2-DateTimeMp1) <= @timespan //@timespan is a parameter of type TimeSpan
DateTimeMp2-DateTimeMp1 <= @timespan
```

## <a name="using-in-and-not-in"></a>İçinde DEĞIL, içinde kullanma

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

Bir C# örneği için bkz. [GitHub 'Da konu filtreleri örneği](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters).


## <a name="correlation-filter-using-correlationid"></a>CorrelationId kullanan bağıntı filtresi

```csharp
new CorrelationFilter("Contoso");
```

Olarak ayarlandığı iletileri filtreler `CorrelationID` `Contoso` . 

## <a name="correlation-filter-using-system-and-user-properties"></a>Sistem ve kullanıcı özelliklerini kullanarak bağıntı filtresi

```csharp
var filter = new CorrelationFilter();
filter.Label = "Important";
filter.ReplyTo = "johndoe@contoso.com";
filter.Properties["color"] = "Red";
```

Eşittir: `sys.ReplyTo = 'johndoe@contoso.com' AND sys.Label = 'Important' AND color = 'Red'`

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki örneklere bakın: 

- [Filtrelerle .NET-temel gönderme ve alma öğreticisi](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET-konu filtreleri](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Azure Resource Manager şablonu](/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)