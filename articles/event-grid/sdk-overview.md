---
title: Azure Event Grid SDK 'lar
description: Azure Event Grid için SDK 'Ları açıklar. Bu SDK 'lar yönetim, yayımlama ve tüketim sağlar.
ms.topic: reference
ms.date: 07/07/2019
ms.openlocfilehash: aa53aaa89a703bb88df6611cdb0dd38341248a55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955933"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Yönetim ve yayımlama için SDK 'Ları Event Grid

Event Grid kaynaklarınızın ve post olaylarınızın programlı bir şekilde yönetilmesini sağlayan SDK 'lar sağlar.

## <a name="management-sdks"></a>Yönetim SDK 'Ları

Yönetim SDK 'Ları, olay Kılavuzu konuları ve abonelikleri oluşturmanızı, güncelleştirmenizi ve silmenizi sağlar. Şu anda aşağıdaki SDK 'lar kullanılabilir:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Git](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/@azure/arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>Veri düzlemi SDK 'Ları

Veri düzlemi SDK 'Ları, kimlik doğrulama, olay yapma ve belirtilen uç noktaya zaman uyumsuz olarak gönderme işlemini gerçekleştirerek olayları konuya göndermenizi sağlar. Bunlar, ilk taraf olaylarını kullanmanıza da olanak tanır. Şu anda aşağıdaki SDK 'lar kullanılabilir:

| Programlama dili | SDK | 
| -------------------- | ---------- | 
| .NET | Kararlı SDK: [Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)<p>SDK önizlemesi: [Azure. Messaging. EventGrid](https://www.nuget.org/packages/Azure.Messaging.EventGrid/) |
| Java | Kararlı SDK: [Azure-eventgrid](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)<p>SDK önizlemesi: [Azure-mesajlaşma-eventgrid](https://search.maven.org/artifact/com.azure/azure-messaging-eventgrid/)</p> |  
| Python | [Azure-eventgrid](https://pypi.org/project/azure-eventgrid/#history) ( **yayın geçmişi** sayfasında en son kararlı ve yayın öncesi sürümlerine bakın) |
| JavaScript | [@azure/eventgrid](https://www.npmjs.com/package/@azure/eventgrid/) (en son kararlı ve beta sürüm paketlerini görmek için **sürümler** sekmesine geçin). | 
| Go | [Go için Azure SDK](https://github.com/Azure/azure-sdk-for-go) |
| Ruby | [azure_event_grid](https://rubygems.org/gems/azure_event_grid) |


## <a name="next-steps"></a>Sonraki adımlar

* Örneğin uygulamalar için bkz. [Event Grid kodu örnekleri](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* Event Grid giriş için bkz. [Event Grid nedir?](overview.md)
* Azure CLı 'deki Event Grid komutları için bkz. [Azure CLI](/cli/azure/eventgrid).
* PowerShell 'deki Event Grid komutları için bkz. [PowerShell](/powershell/module/az.eventgrid).
