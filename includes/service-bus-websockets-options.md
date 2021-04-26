---
title: include dosyası
description: include dosyası
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 04/08/2021
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 08fccf366321b075542f36b86c9bf22d5d877167
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304432"
---
AMQP-Over-WebSockets Protocol seçeneği, HTTP/REST API gibi bağlantı noktası TCP 443 üzerinden çalışır, ancak Aksi halde düz AMQP ile aynı şekilde çalışır. Bu seçenek, ek el sıkışma ve HTTPS bağlantı noktasını paylaşmak için zorunluluğunu getirir kadar çok daha fazla ek yük nedeniyle ilk bağlantı gecikmesini daha yüksektir. Bu mod seçilirse, TCP bağlantı noktası 443 iletişim için yeterlidir. Aşağıdaki seçenekler AMQP WebSockets modunun seçilmesine izin verir. 

| Dil | Seçenek   |
| -------- | ----- |
| .NET (Azure.Messaging.ServiceBus)    | [Servicebusclientoptions](/dotnet/api/azure.messaging.servicebus.servicebusclientoptions) öğesini parametre olarak alan bir Oluşturucu kullanarak [servicebusclient](/dotnet/api/azure.messaging.servicebus.servicebusclient.-ctor) oluşturun. [Servicebusclientoptions. TransportType](/dotnet/api/azure.messaging.servicebus.servicebusclientoptions.transporttype) değerini [Servicebustransporttype. AmqpWebSockets](/dotnet/api/azure.messaging.servicebus.servicebustransporttype) olarak ayarlayın |
| .NET (Microsoft.Azure.ServiceBus)    | İstemci nesneleri oluştururken, [TransportType](/dotnet/api/microsoft.azure.servicebus.transporttype), [servicebusconnection](/dotnet/api/microsoft.azure.servicebus.servicebusconnection)veya [servicebusconnectionstringbuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder) 'ı parametre olarak alan oluşturucular kullanın. <p>Parametre olarak alan oluşturma için `transportType` , parametresini [TransportType. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype)olarak ayarlayın.</p> <p>Parametre olarak alan Oluşturucu için `ServiceBusConnection` [Servicebusconnection. TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) değerini [TransportType. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype)olarak ayarlayın.</p> <p>Kullanıyorsanız, `ServiceBusConnectionStringBuilder` size belirtme seçeneği sunan oluşturucular kullanın `transportType` .</p> |
| Java (com. Azure. Messaging. ServiceBus)     | İstemci oluştururken, [Servicebusclientbuilder. transportType](/java/api/com.azure.messaging.servicebus.servicebusclientbuilder.transporttype) öğesini [AmqpTransportType.AMQP.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype) olarak ayarlayın |
| Java (com. Microsoft. Azure. ServiceBus)    | İstemcileri oluştururken, `transportType` [com. Microsoft. Azure. ServiceBus. ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings#com_microsoft_azure_servicebus_ClientSettings_ClientSettings_com_microsoft_azure_servicebus_security_TokenProvider_com_microsoft_azure_servicebus_primitives_RetryPolicy_java_time_Duration_com_microsoft_azure_servicebus_primitives_TransportType_) içinde [com.Microsoft.Azure.ServiceBus.Primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) olarak ayarlayın |
| JavaScript  | Service Bus istemci nesneleri oluştururken `webSocketOptions` [Servicebusclientoptions](/javascript/api/@azure/service-bus/servicebusclientoptions)içindeki özelliğini kullanın. |
| Python | Service Bus istemcileri oluştururken, [ServiceBusClient.transport_type](/python/api/azure-servicebus/azure.servicebus.servicebusclient) [TransportType. AmqpOverWebSocket](/python/api/azure-servicebus/azure.servicebus.transporttype) olarak ayarlayın |

