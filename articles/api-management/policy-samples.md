---
title: Azure API Management ilke örnekleri | Microsoft Docs
description: Azure API Management'ta kullanılabilecek ilkeler hakkında bilgi edinin.
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 10/31/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: f8bd768dc7c01310874cd484f59869fbafcf3fe7
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074205"
---
# <a name="api-management-policy-samples"></a>API Management ilke örnekleri

[İlkeler](api-management-howto-policies.md), yayımcının API’nin davranışını yapılandırma yoluyla değiştirmesini sağlayan güçlü sistem özellikleridir. İlkeler, bir API isteği veya yanıtı üzerinde sırayla yürütülen deyimlerin bir koleksiyonudur. Aşağıdaki tabloda örneklerin bağlantıları yer alır ve her örnek için kısa bir açıklama verilir.

|                                                                                                                                                                      |                                                                                                                                                                                                                             |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Gelen ilkeleri**                                                                                                                                                 |                                                                                                                                                                                                                             |
| [Arka uç API'sinin düzgün URL'ler oluşturmasını sağlamak için bir İletildi üst bilgisi ekleme](./policies/set-header-to-enable-backend-to-construct-urls.md?toc=api-management/toc.json) | Arka uç API'sinin düzgün URL'ler oluşturmasını sağlamak için gelen isteğe İletildi üst bilgisi ekleme işlemini gösterir.                                                                                                        |
| [Bağıntı kimliği içeren bir üst bilgi ekleme](./policies/add-correlation-id.md?toc=api-management/toc.json)                                                             | Gelen isteğe bağıntı kimliği içeren bir üst bilgi ekleme işlemini gösterir.                                                                                                                                        |
| [Arka uç hizmetine özellikler ekleme ve yanıtı önbelleğe alma](./policies/cache-response.md?toc=api-management/toc.json)                                             | Arka uç hizmetine özellikler ekleme işlemini gösterir. Örneğin, hava durumu tahmini API'sinde enlem ve boylam yerine bir yer adını kabul edin.                                                                    |
| [JWT talepleri temelinde erişim yetkisi verme](./policies/authorize-request-based-on-jwt-claims.md?toc=api-management/toc.json)                                              | JWT talepleri temelinde bir API'deki belirli HTTP yöntemlerine erişim yetkisi verme işlemini gösterir.                                                                                                                                       |
| [Dış yetkilendirici kullanarak istekleri yetkilendirme](./policies/authorize-request-using-external-authorizer.md)                                                   | API erişiminin güvenliğini sağlamak için dış yetkilendiricinin nasıl kullanılacağını gösterir.                                                                                                                                                               |
| [Google OAuth belirtecini kullanarak erişim yetkisi verme](./policies/use-google-as-oauth-token-provider.md?toc=api-management/toc.json)                                            | OAuth belirteç sağlayıcısı olarak Google'ı kullanıp uç noktalarınıza erişim yetkisi verme işlemini gösterir.                                                                                                                                    |
| [Paylaşılan Erişim İmzası oluşturma ve Azure depolamaya isteği iletme](./policies/generate-shared-access-signature.md?toc=api-management/toc.json)                  | İfadeleri kullanarak [Paylaşılan Erişim İmzası](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) oluşturma ve rewrite-uri ilkesiyle Azure depolamaya isteği iletme işlemlerini gösterir. |
| [AAD'den OAuth2 erişim belirtecini alma ve bunu arka uca iletme](./policies/use-oauth2-for-authorization.md?toc=api-management/toc.json)                             | Ağ geçidiyle arka uç arasındaki yetkilendirme için OAuth2 kullanma örneği sağlar. AAD'den erişim belirtecini alma ve bunu arka uca iletme işlemini de gösterir.                                                    |
| [İstek gönderme ilkesini kullanarak SAP ağ geçidinden X-CSRF belirtecini alma](./policies/get-x-csrf-token-from-sap-gateway.md?toc=api-management/toc.json)                           | Birçok API tarafından kullanılan X-CSRF deseninin nasıl uygulandığını gösterir. Bu örnek SAP Ağ Geçidi'ne özgüdür.                                                                                                                           |
| [İsteği, gövdesinin boyutu temelinde yönlendirme](./policies/route-requests-based-on-size.md?toc=api-management/toc.json)                                            | İstekleri gövdelerinin boyutu temelinde yönlendirme işlemini gösterir.                                                                                                                                                       |
| [Arka uç hizmetine istek bağlamı bilgilerini gönderme](./policies/send-request-context-info-to-backend-service.md?toc=api-management/toc.json)                    | Günlüğe kaydedilmesi veya işlenmesi için arka uç hizmetine bazı bağlam bilgilerinin nasıl gönderileceğini gösterir.                                                                                                                                |
| [Yanıt önbellek süresini ayarlama](./policies/set-cache-duration.md?toc=api-management/toc.json)                                                                          | Arka uç tarafından gönderilen Cache-Control üst bilgisindeki maxAge değerini kullanarak yanıt önbellek süresini ayarlama işlemini gösterir.                                                                                                             |
| **Giden ilkeleri**                                                                                                                                                |                                                                                                                                                                                                                             |
| [Yanıt içeriğini filtreleme](./policies/filter-response-content.md?toc=api-management/toc.json)                                                                         | İstekle ilişkilendirilmiş ürün temelinde yanıt yükünden veri öğelerinin nasıl filtreleneceğini gösterir.                                                                                                        |
| **Hata durum ilkeleri**                                                                                                                                                |                                                                                                                                                                                                                             |
| [Hataları Stackify'de günlüğe kaydetme](./policies/log-errors-to-stackify.md?toc=api-management/toc.json)                                                                           | Hataları günlüğe kaydedilmek üzere Stackify'ye göndermek üzere bir hata günlüğü ilkesi ekleme işlemini gösterir.                                                                                                                                            |
