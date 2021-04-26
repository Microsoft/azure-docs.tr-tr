---
title: Azure Relay bağlantı noktası ayarları | Microsoft Docs
description: Bu makale, Azure Relay için bağlantı noktası değerleri için gereken yapılandırmayı açıklayan bir tablo içerir.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 81551055d967babaac6f12c3a23ce6b1e78afbd5
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314177"
---
# <a name="azure-relay-port-settings"></a>Azure Relay bağlantı noktası ayarları

Aşağıdaki tabloda Azure Relay için bağlantı noktası değerleri için gereken yapılandırma açıklanmaktadır.

## <a name="hybrid-connections"></a>Karma Bağlantılar

Karma Bağlantılar, bağlantı noktası 443 üzerinde WebSockets kullanarak, yalnızca **https** kullanan temel taşıma MEKANIZMASı olarak TLS kullanır. 

## <a name="wcf-relays"></a>WCF Geçişleri
  
|Bağlama|Taşıma Güvenliği|Bağlantı noktası|  
|-------------|------------------------|----------|  
|[Basichttprelaybinding sınıfı](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (istemci)|Yes|HTTPS| 
|" |Hayır|HTTP|  
|[Basichttprelaybinding sınıfı](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (hizmet)|Herhangi biri|9351/HTTP|  
|[Neteventrelaybinding sınıfı](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (istemci)|Yes|9351/HTTPS|  
|" |Hayır|9350/HTTP|  
|[Neteventrelaybinding sınıfı](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (hizmet)|Herhangi biri|9351/HTTP|  
|[Nettcprelaybinding sınıfı](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (istemci/hizmet)|Herhangi biri|5671/9352/HTTP (karma kullanılıyorsa 9352/9353)|  
|[NetOnewayRelayBinding sınıfı](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (istemci)|Yes|9351/HTTPS|  
|" |Hayır|9350/HTTP|  
|[NetOnewayRelayBinding sınıfı](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (hizmet)|Herhangi biri|9351/HTTP|  
|[WebHttpRelayBinding sınıfı](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (istemci)|Yes|HTTPS|  
|" |Hayır|HTTP|  
|[WebHttpRelayBinding sınıfı](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (hizmet)|Herhangi biri|9351/HTTP|  
|[WS2007HttpRelayBinding sınıfı](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (istemci)|Yes|HTTPS|  
|" |Hayır|HTTP|  
|[WS2007HttpRelayBinding sınıfı](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (hizmet)|Herhangi biri|9351/HTTP|

## <a name="next-steps"></a>Sonraki adımlar
Azure Relay hakkında daha fazla bilgi edinmek için şu bağlantıları ziyaret edin:
* [Azure Relay nedir?](relay-what-is-it.md)
* [Geçiş hakkında SSS](relay-faq.yml)