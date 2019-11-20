---
title: İçerik anahtarı ilkelerinde medya Hizmetleri - Azure | Microsoft Docs
description: Bu makalede, içerik anahtarı ilkeleri nelerdir ve Azure Media Services tarafından nasıl kullanıldıkları bir açıklama sağlar.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 8809bf25c3bcfb26fb0ad251a2b09dfdca2a3e04
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679199"
---
# <a name="content-key-policies"></a>İçerik Anahtar İlkeleri

Media Services ile, canlı ve isteğe bağlı içeriğinizi Gelişmiş Şifreleme Standardı (AES-128) veya üç ana dijital hak yönetimi (DRM) sisteminden dinamik olarak şifreli olarak dağıtabilirsiniz: Microsoft PlayReady, Google Widevine ve Apple FairPlay. Media Services de AES anahtarları ve DRM sunmaya yönelik bir hizmet sağlar (PlayReady, Widevine ve FairPlay) lisansları yetkili istemcilere. 

Akışınızla ilgili şifreleme seçeneklerini belirtmek için bir [akış ilkesi](streaming-policy-concept.md) oluşturmanız ve bunu [akış konumunuzla](streaming-locators-concept.md)ilişkilendirmeniz gerekir. İçerik anahtarının (varlıklarınıza güvenli erişim sağlayan) son istemcilere teslim edildiğini yapılandırmak için [Içerik anahtarı ilkesini](https://docs.microsoft.com/rest/api/media/contentkeypolicies) oluşturursunuz [](assets-concept.md). Belirtilen yapılandırmaya sahip anahtarların istemcilere teslim edilebilmesi için karşılanması gereken Içerik anahtarı Ilkesinde gereksinimleri (kısıtlamalar) ayarlamanız gerekir. İçerik anahtarı ilkesi, açık akış veya indirme için gerekli değildir. 

Genellikle, içerik anahtarı ilkenizi [akış konumunuzla](streaming-locators-concept.md)ilişkilendirirsiniz. Alternatif olarak, içerik anahtarı ilkesini bir [akış ilkesi](streaming-policy-concept.md) içinde belirtebilirsiniz (Gelişmiş senaryolar için özel bir akış ilkesi oluştururken). 

> [!NOTE]
> `Datetime` Türündeki içerik anahtarı ilkelerinin özellikleri her zaman UTC biçimindedir.

## <a name="best-practices-and-considerations"></a>En iyi uygulamalar ve noktalar

> [!IMPORTANT]
> Lütfen aşağıdaki önerileri gözden geçirin.

* Media Service hesabınız için sınırlı bir ilke kümesi tasarlamanızı ve aynı seçenek gerektiğinde bunları akış bulucular için yeniden kullanmanız gerekir. Daha fazla bilgi için bkz. [Kotalar ve sınırlamalar](limits-quotas-constraints.md).
* İçerik anahtarı ilkeleri güncelleştirilebilir. Anahtar teslimi önbellekler güncelleştirilmiş ilkeyi güncelleştirmek ve seçmek 15 dakika sürebilir. 

   İlkeyi güncelleştirerek, var olan CDN önbelleğinizin üzerine yazılır, bu da önbelleğe alınmış içerik kullanan müşteriler için kayıttan yürütme sorununa neden olabilir.  
* Her varlık için yeni bir içerik anahtar ilkesi oluşturmanız önerilir. Aynı ilke seçeneklerine ihtiyacı olan varlıklar arasında aynı içerik anahtarı ilkesini paylaşmanın başlıca avantajları şunlardır:
   
   * Az sayıda ilkeyi yönetmek daha kolaydır.
   * İçerik anahtarı ilkesinde güncelleştirmeler yapmanız gerekiyorsa, değişiklikler neredeyse hemen tüm yeni lisans istekleri üzerinde devreye girer.
* Yeni bir ilke oluşturmanız gerekiyorsa, varlık için yeni bir akış Bulucu oluşturmanız gerekir.
* İçerik anahtarını Media Services yeniden AutoGenerate sağlamak önerilir. 

   Genellikle, uzun süreli bir anahtar kullanır ve [Get](https://docs.microsoft.com/rest/api/media/contentkeypolicies/get)ile içerik anahtarı ilkesinin varolup olmadığını kontrol edersiniz. Anahtarı almak için, parolaları veya kimlik bilgilerini almak üzere ayrı bir eylem yöntemi çağırmanız gerekir, aşağıdaki örneğe bakın.

## <a name="example"></a>Örnek

Anahtarı almak için, `GetPolicyPropertiesWithSecretsAsync` [mevcut ilke örneğinde bir imzalama anahtarı al](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets) bölümünde gösterildiği gibi kullanın.

## <a name="filtering-ordering-paging"></a>Filtreleme, sıralama, sayfalama

Bkz. [Media Services varlıkların filtrelenmesi, sıralanması, sayfalama](entities-overview.md).

## <a name="next-steps"></a>Sonraki adımlar

* [AES-128 dinamik şifreleme ve anahtar teslim hizmetini kullanma](protect-with-aes128.md)
* [DRM dinamik şifreleme ve lisans teslimat hizmeti kullanın](protect-with-drm.md)
* [Encodehttpandpublishaesşifrelendi](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
