---
title: Azure Media Services ' de Azure CDN önbelleğe alma ilkesi yönetme | Microsoft Docs
description: Bu makalede Azure Media Services Azure CDN önbelleğe alma ilkesinin nasıl yönetileceği açıklanmaktadır.
services: media-services,cdn
documentationcenter: .NET
author: juliako
manager: erikre
editor: ''
ms.assetid: be33aecc-6dbe-43d7-a056-10ba911e0e94
ms.service: media-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/04/2017
ms.author: juliako
ms.openlocfilehash: 5807d38e22d8cecf40b5ad4262f9e4662b77ec4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92779144"
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Azure Media Services Azure CDN önbelleğe alma ilkesini yönetme
Azure Media Services HTTP tabanlı uyarlamalı akış ve aşamalı indirme sağlar. HTTP tabanlı akış, proxy ve CDN katmanlarında önbelleğe almanın yanı sıra istemci tarafı önbelleğe alma avantajlarıyla yüksek düzeyde ölçeklenebilir. Akış uç noktaları, HTTP önbellek üstbilgileri için genel akış özellikleri ve ayrıca yapılandırma sağlar. Akış uç noktaları, HTTP Cache-Control: Max-Age ve Expires Headers ayarlar. [W3.org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html)adresinden http önbellek üstbilgileri hakkında daha fazla bilgi edinebilirsiniz.

## <a name="default-caching-headers"></a>Varsayılan önbelleğe alma üstbilgileri
Varsayılan olarak, akış uç noktaları isteğe bağlı akış verileri (gerçek medya parçaları/öbekleri) ve bildirim (çalma listesi) için 3 günlük önbellek üst bilgisi uygular. Canlı akış için, akış uç noktaları veriler için 3 günlük önbellek üst bilgisi (gerçek medya parçaları/öbekleri) ve bildirim (çalma listesi) istekleri için 2 saniyelik önbellek üstbilgisi uygular. Canlı program isteğe bağlı (Canlı Arşiv) ' i açtığında, isteğe bağlı akış önbelleği üstbilgileri uygulanır.

## <a name="azure-cdn-integration"></a>Azure CDN tümleştirme
Azure Media Services, akış uç noktaları için [TÜMLEŞIK CDN](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) sağlar. Cache-Control üst bilgileri, CDN etkin akış uç noktaları için akış uç noktalarıyla aynı şekilde geçerlidir. Azure CDN, iç önbelleğe alınmış nesnelerin yaşam süresini tanımlamak için akış uç noktası yapılandırılmış önbellek değerlerini kullanır ve ayrıca bu değeri, teslim önbelleği üst bilgilerini ayarlamak için kullanır. CDN etkin akış uç noktaları kullanılırken, küçük önbellek değerlerinin ayarlanması önerilmez. Küçük değerleri ayarlama performansı azaltır ve CDN 'nin avantajını azaltır. CDN etkin akış uç noktaları için 600 saniyeden daha küçük önbellek üstbilgileri ayarlama izni yoktur.

> [!IMPORTANT]
>Azure Media Services Azure CDN ile tamamen tümleştirme içerir. Tek bir tıklama ile, tüm kullanılabilir Azure CDN sağlayıcılarını standart ve Premium ürünleri dahil olmak üzere akış uç noktanızla tümleştirebilirsiniz. Daha fazla bilgi için bu [duyuruya](https://azure.microsoft.com/blog/standardstreamingendpoint/)bakın.
> 
> Akış uç noktasından CDN 'ye veri ücretleri yalnızca CDN, akış uç noktası API 'Leri üzerinden etkinleştirilmişse veya Azure portal akış uç noktası bölümü kullanılırken devre dışı bırakılır. CDN API 'Leri veya Portal bölümünü kullanarak el ile tümleştirme veya doğrudan CDN uç noktası oluşturma, veri ücretlerini devre dışı bırakır.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Azure Media Services ile önbellek üstbilgilerini yapılandırma
Önbellek üst bilgi değerlerini yapılandırmak için Azure portal veya Azure Media Services API 'Leri kullanabilirsiniz.

1. Azure portal kullanarak önbellek üstbilgilerini yapılandırmak için akış uç noktasını [yapılandırma bölümüne bakın](../media-services/previous/media-services-portal-manage-streaming-endpoints.md) .
2. Azure Media Services REST API'si, [Streamingendpoint](/rest/api/media/operations/streamingendpoint#StreamingEndpointCacheControl).
3. Azure Media Services .NET SDK, [Streamingendpointcachecontrol özellikleri](/dotnet/api/microsoft.windowsazure.mediaservices.client.streamingendpointcachecontrol).

## <a name="cache-configuration-precedence-order"></a>Önbellek yapılandırması öncelik sırası
1. Yapılandırılan Azure Media Services önbellek değeri varsayılan değeri geçersiz kılar.
2. El ile yapılandırma yoksa, varsayılan değerler geçerlidir.
3. Varsayılan olarak, 2 saniyelik önbellek üst bilgileri, Azure medyasından veya Azure depolama yapılandırması ne olursa olsun canlı akış bildirimi (çalma listesi) için geçerlidir ve bu değerin üzerine yazmak kullanılabilir değildir.