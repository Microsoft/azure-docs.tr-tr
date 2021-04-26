---
title: Media Services içerik anahtarı Ilkeleri-Azure
description: Bu makalede, Içerik anahtarı Ilkelerinin ne olduğu ve Azure Media Services tarafından nasıl kullanıldıkları hakkında bir açıklama sunulmaktadır.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 6e60292c817ccad8eb1dd6cb3c33b944ab1c18a0
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277700"
---
# <a name="content-key-policies"></a>İçerik Anahtar İlkeleri

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services ile canlı ve isteğe bağlı içeriğinizi Gelişmiş Şifreleme Standardı (AES-128) veya üç ana dijital hak yönetimi (DRM) sisteminden (Microsoft PlayReady, Google Widevine ve Apple FairPlay ile) dinamik olarak dağıtabilirsiniz. Media Services, yetkili istemcilere AES anahtarları ve DRM (PlayReady, Widevine ve FairPlay) lisanslarını sunmaya yönelik bir hizmet sağlar. 

Akışınızla ilgili şifreleme seçeneklerini belirtmek için bir [akış ilkesi](stream-streaming-policy-concept.md) oluşturmanız ve bunu [akış konumunuzla](stream-streaming-locators-concept.md)ilişkilendirmeniz gerekir. İçerik anahtarının ( [varlıklarınıza](assets-concept.md)güvenli erişim sağlayan) son istemcilere teslim edildiğini yapılandırmak Için [içerik anahtarı ilkesini](/rest/api/media/contentkeypolicies) oluşturursunuz. Belirtilen yapılandırmaya sahip anahtarların istemcilere teslim edilebilmesi için karşılanması gereken Içerik anahtarı Ilkesinde gereksinimleri (kısıtlamalar) ayarlamanız gerekir. İçerik anahtarı ilkesi, açık akış veya indirme için gerekli değildir. 

Genellikle, içerik anahtarı ilkenizi [akış konumunuzla](stream-streaming-locators-concept.md)ilişkilendirirsiniz. Alternatif olarak, içerik anahtarı ilkesini bir [akış ilkesi](stream-streaming-policy-concept.md) içinde belirtebilirsiniz (Gelişmiş senaryolar için özel bir akış ilkesi oluştururken). 

## <a name="best-practices-and-considerations"></a>En iyi uygulamalar ve noktalar

> [!IMPORTANT]
> Lütfen aşağıdaki önerileri gözden geçirin.

* Media Service hesabınız için sınırlı bir ilke kümesi tasarlamanızı ve aynı seçenek gerektiğinde bunları akış bulucular için yeniden kullanmanız gerekir. Daha fazla bilgi için bkz. [Kotalar ve sınırlar](limits-quotas-constraints-reference.md).
* İçerik anahtarı ilkeleri güncelleştirilebilir. Anahtar teslimi önbellekler güncelleştirilmiş ilkeyi güncelleştirmek ve seçmek 15 dakika sürebilir. 

   İlkeyi güncelleştirerek, var olan CDN önbelleğinizin üzerine yazılır, bu da önbelleğe alınmış içerik kullanan müşteriler için kayıttan yürütme sorununa neden olabilir.  
* Her varlık için yeni bir içerik anahtar ilkesi oluşturmanız önerilir. Aynı ilke seçeneklerine ihtiyacı olan varlıklar arasında aynı içerik anahtarı ilkesini paylaşmanın başlıca avantajları şunlardır:
   
   * Az sayıda ilkeyi yönetmek daha kolaydır.
   * İçerik anahtarı ilkesinde güncelleştirmeler yapmanız gerekiyorsa, değişiklikler neredeyse hemen tüm yeni lisans istekleri üzerinde devreye girer.
* Yeni bir ilke oluşturmanız gerekiyorsa, varlık için yeni bir akış Bulucu oluşturmanız gerekir.
* İçerik anahtarını Media Services yeniden AutoGenerate sağlamak önerilir. 

   Genellikle, uzun süreli bir anahtar kullanır ve [Get](/rest/api/media/contentkeypolicies/get)ile içerik anahtarı ilkesinin varolup olmadığını kontrol edersiniz. Anahtarı almak için, parolaları veya kimlik bilgilerini almak üzere ayrı bir eylem yöntemi çağırmanız gerekir, aşağıdaki örneğe bakın.

## <a name="example"></a>Örnek

Anahtarı almak için, `GetPolicyPropertiesWithSecretsAsync` [mevcut ilke örneğinde bir Imzalama anahtarı al](drm-get-content-key-policy-dotnet-how-to.md#get-contentkeypolicy-with-secrets) bölümünde gösterildiği gibi kullanın.

## <a name="filtering-ordering-paging"></a>Filtreleme, sıralama, sayfalama

Bkz. [Media Services varlıkların filtrelenmesi, sıralanması, sayfalama](filter-order-page-entitites-how-to.md).

## <a name="additional-notes"></a>Ek notlar

* Türündeki Içerik anahtarı Ilkelerinin özellikleri `Datetime` her zaman UTC biçimindedir.
* Widevine, Google Inc. tarafından sunulan bir hizmettir ve Google, Inc 'nin hizmet koşullarına ve gizlilik Ilkesine tabidir.

## <a name="next-steps"></a>Sonraki adımlar

* [AES-128 dinamik şifreleme ve anahtar dağıtım hizmetini kullanma](drm-playready-license-template-concept.md)
* [DRM dinamik şifreleme ve lisans teslim hizmetini kullanma](drm-protect-with-drm-tutorial.md)
* [Temel AES şifresiz anahtar şifreleme ve akış örnek kodu](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)
