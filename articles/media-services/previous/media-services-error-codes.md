---
title: Azure Media Services hata kodları | Microsoft Docs
description: Media Services ' de desteklenmeyen eylemlere yönelik kimlik doğrulama belirteçleri süresi dolan sorunlara bağlı olarak hizmetten HTTP hata kodları alabilirsiniz. Bu makale, Azure Media Services V2 API hata kodlarına genel bir bakış sunar.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: d3a62a64-7608-4b17-8667-479b26ba0d6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 10ade5c8f34d806ca5706ccaaf4b7ff2e05ba657
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013423"
---
# <a name="azure-media-services-error-codes"></a>Azure Media Services hata kodları

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Microsoft Azure Media Services kullanırken, hizmetten Media Services desteklenmeyen eylemlere yönelik kimlik doğrulama belirteçleri süresi dolan işlemlere bağlı olarak hizmetten HTTP hata kodları alabilirsiniz. Aşağıda, Media Services tarafından döndürülebilecek **http hata kodlarının** listesi ve olası nedenleri verilmiştir.  

## <a name="400-bad-request"></a>400 Hatalı İstek
İstek geçersiz bilgiler içeriyor ve aşağıdaki nedenlerden biri nedeniyle reddedildi:

* Desteklenmeyen bir API sürümü belirtildi. En güncel sürüm için bkz. [Media Services REST API Geliştirme Için kurulum](media-services-rest-how-to-use.md).
* Media Services API sürümü belirtilmedi. API sürümünü belirtme hakkında daha fazla bilgi için bkz. [Media Services Operations REST API Reference](/rest/api/media/operations/azure-media-services-rest-api-reference).
  
  > [!NOTE]
  > Media Services bağlanmak için .NET veya Java SDK 'larını kullanıyorsanız, Media Services bir işlem gerçekleştirmeye çalıştığınızda API sürümü sizin için belirtilir.
  > 
  > 
* Tanımlanmamış bir özellik belirtildi. Özellik adı hata iletisidir. Yalnızca belirli bir varlığın üyesi olan özellikler belirtilebilir. Varlıkların listesi ve özellikleri için bkz. [Azure Media Services REST API'si Başvurusu](/rest/api/media/operations/azure-media-services-rest-api-reference) .
* Geçersiz bir özellik değeri belirtildi. Özellik adı hata iletisidir. Geçerli özellik türleri ve değerleri için önceki bağlantıyı inceleyin.
* Özellik değeri eksik ve gereklidir.
* Belirtilen URL 'nin bir parçası bozuk bir değer içeriyor.
* Bir WriteOnce özelliği güncelleştirilmeye çalışıldı.
* Belirtilmemiş veya belirlenemediği birincil Assetdosyası olan bir giriş varlığına sahip bir Iş oluşturulmaya çalışıldı.
* SAS Konumlandırıcı 'yı güncelleştirme girişiminde bulunuldu. SAS Konumlandırıcı yalnızca oluşturulabilir veya silinebilir. Akış Konumlandırıcı güncelleştirilemeyebilir. Daha fazla bilgi için bkz. [Konum Belirleyicisi](/rest/api/media/operations/locator).
* Desteklenmeyen bir işlem veya sorgu gönderildi.

## <a name="401-unauthorized"></a>401 Yetkisiz
Aşağıdaki nedenlerden biri nedeniyle isteğin kimliği doğrulanamadı (yetkilendirilebilmesi için):

* Kimlik doğrulama üst bilgisi eksik.
* Hatalı kimlik doğrulama üst bilgisi değeri.
  * Belirtecin süresi doldu. 
  * Belirteç geçersiz bir imza içeriyor.

## <a name="403-forbidden"></a>403 Yasak
Aşağıdaki nedenlerden biri nedeniyle isteğe izin verilmiyor:

* Media Services hesabı bulunamadı veya silinmiş.
* Media Services hesabı devre dışı bırakıldı ve istek türü HTTP GET değil. Hizmet işlemleri de 403 yanıtını döndürür.
* Kimlik doğrulama belirteci kullanıcının kimlik bilgisi bilgilerini içermiyor: AccountName ve/veya SubscriptionID. Bu bilgileri Azure Yönetim Portalı Media Services hesabınız için Media Services kullanıcı arabirimi uzantısında bulabilirsiniz.
* Kaynağa erişilemiyor.
  
  * Media Services hesabınızda kullanılamayan bir MediaProcessor kullanılmaya çalışıldı.
  * Media Services tarafından tanımlanan bir JobTemplate güncelleştirme girişiminde bulunuldu.
  * Diğer Media Services hesabının Konumlandırıcı 'nın üzerine yazma girişiminde bulunuldu.
  * Başka bir Media Services hesabının ContentKey öğesinin üzerine yazılması denendi.
* Media Services hesabı için ulaşılan bir hizmet kotası nedeniyle kaynak oluşturulamadı. Hizmet kotaları hakkında daha fazla bilgi için bkz. [Kotalar ve sınırlamalar](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 Bulunamadı
Aşağıdaki nedenlerden biri nedeniyle bir kaynakta isteğe izin verilmiyor:

* Mevcut olmayan bir varlığı güncelleştirmek için bir girişimde bulunuldu.
* Mevcut olmayan bir varlığı silme girişiminde bulunuldu.
* Varolmayan bir varlığa bağlanan bir varlık oluşturmak için bir girişimde bulunuldu.
* Mevcut olmayan bir varlık ALıNMAYA çalışıldı.
* Media Services hesabıyla ilişkilendirilmemiş bir depolama hesabı belirtilmesi denendi.  

## <a name="409-conflict"></a>409 Çakışma
Aşağıdaki nedenlerden biri nedeniyle isteğe izin verilmiyor:

* Birden fazla AssetFile, varlık içinde belirtilen ada sahip.
* Varlık içinde ikinci bir birincil Assetdosyası oluşturma girişiminde bulunuldu.
* Belirtilen kimlikle zaten kullanılan bir ContentKey oluşturulmaya çalışıldı.
* Belirtilen kimliğe sahip bir bulucu oluşturulmaya çalışıldı, zaten kullanıldı.
* IngestManifest içinde, birden fazla IngestManifestFile belirtilen ada sahip.
* İkinci bir depolama şifrelemesi Içerik anahtarını depolama ile şifrelenen varlığa bağlama girişiminde bulunuldu.
* Aynı ContentKey 'i varlığa bağlamak için bir girişimde bulunuldu.
* Depolama kapsayıcısı eksik olan veya artık varlıkla ilişkili olmayan bir varlık için bulucu oluşturma girişiminde bulunuldu.
* Zaten 5 Konumlandırıcı kullanan bir varlık için bulucu oluşturulmaya çalışıldı. (Azure depolama, bir depolama kapsayıcısında beş paylaşılan erişim ilkesi sınırını uygular.)
* Bir varlığın depolama hesabını bir IngestManifestAsset 'ye bağlama, üst IngestManifest tarafından kullanılan depolama hesabı ile aynı değildir.  

## <a name="500-internal-server-error"></a>500 İç Sunucu Hatası
İsteğin işlenmesi sırasında, Media Services işlemin devam etmesini önleyen bir hata oluştu. Bunun nedeni, aşağıdakilerden biri olabilir:

* Media Services hesabının hizmet kotası bilgileri geçici olarak kullanılamadığından varlık veya Iş oluşturma başarısız olur.
* Hesabın depolama hesabı bilgileri geçici olarak kullanılamadığından, bir varlık veya IngestManifest BLOB depolama kapsayıcısı oluşturma başarısız olur.
* Diğer beklenmeyen hata.

## <a name="503-service-unavailable"></a>503 Hizmet Kullanılamıyor
Sunucu şu anda istekleri alamıyor. Bu hata hizmetin aşırı istek almasından kaynaklanıyor olabilir. Media Services azaltma mekanizması hizmete çok sayıda istek gönderen uygulamaların kaynak kullanımını kısıtlar.

> [!NOTE]
> 503 hatasını alma nedeniniz hakkında daha fazla bilgi edinmek için hata iletisini ve hata kodu dizesini kontrol edin. Bu hata her zaman azaltma anlamına gelmez.
> 
> 

Olası durum açıklamaları şunlardır:

* "Sunucu meşgul. Bu istek türünün önceki çalıştırmaları saniyeden uzun sürdü {0} . "
* "Sunucu meşgul. {0}Saniye başına istek sayısı daraltıldı. "
* "Sunucu meşgul. {0}Saniyeler içinde isteklerin daha fazlası {1} kısıtlanacaktır. "

Bu hatayı işlemek için üstel geri deneme mantığını kullanmanızı öneririz. Yani, ardışık hata yanıtları için yeniden denemeler arasında giderek daha uzun süre bekler.  Daha fazla bilgi için bkz. [geçici hata Işleme uygulama bloğu](/previous-versions/msp-n-p/hh680905(v=pandp.50)).

> [!NOTE]
> [.NET için Azure MEDIA SERVICES SDK](https://github.com/Azure/azure-sdk-for-media-services/tree/master)kullanıyorsanız, 503 hatası için yeniden deneme mantığı SDK tarafından uygulanmıştır.  
> 
> 

## <a name="see-also"></a>Ayrıca Bkz.
[Media Services yönetimi hata kodları](/rest/api/media/)

## <a name="next-steps"></a>Sonraki adımlar
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
