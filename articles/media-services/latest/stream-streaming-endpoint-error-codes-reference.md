---
title: Paketleme ve kaynak hatalarını Azure Media Services
description: Bu konuda Azure Media Services akış uç noktası (Orgın) hizmetinden alabileceğiniz hatalar açıklanmaktadır.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: error-reference
ms.date: 05/07/2019
ms.author: inhenkel
ms.openlocfilehash: 8ba1dd5657e1163648983fcb172aa7b14aaa06cd
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282294"
---
# <a name="streaming-endpoint-origin-errors"></a>Akış Uç Noktası (Kaynak) hataları

Bu konuda Azure Media Services [akış uç noktası hizmetinden](stream-streaming-endpoint-concept.md)alabileceğiniz hatalar açıklanmaktadır.

## <a name="400-bad-request"></a>400 Hatalı İstek

İstek geçersiz bilgiler içeriyor ve bu hata kodlarıyla ve aşağıdaki nedenlerden biri nedeniyle reddedildi:

|Hata kodu|Onaltılık değer |Hata açıklaması|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|URL sözdizimi veya biçim hatası. Örnek olarak, geçersiz bir tür, geçersiz bir parça veya geçersiz bir izleme istekleri bulunur. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|İstekte URL 'de şifreleme etiketi yok. CMAF istekleri URL 'de bir şifreleme etiketi gerektirir. Birden fazla şifreleme türüyle yapılandırılan diğer protokoller de Kesinleştirme için şifreleme etiketi gerektirir. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|İsteği yerine getirmek için depolama isteği hatalı bir Istek hatasıyla başarısız oldu. |

## <a name="403-forbidden"></a>403 Yasak

Aşağıdaki nedenlerden biri nedeniyle isteğe izin verilmiyor:

|Hata kodu|Onaltılık değer |Hata açıklaması|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|İsteği yerine getirmek için depolama isteği bir kimlik doğrulama hatasıyla başarısız oldu. Bu durum, depolama anahtarları döndürülürse ve hizmet depolama anahtarlarını eşitlemişse meydana gelebilir. <br/><br/>Azure portal [Yardım + Destek](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) bölümüne giderek Azure desteği ile iletişim kurun.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |Depolama Işlemi hatası, yetersiz hesap Izinleri nedeniyle erişim başarısız oldu. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |Depolama hesabı devre dışı bırakıldığından, isteği yerine getirmek için depolama isteği başarısız oldu. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |Depolama Işlemi hatası, genel hatalar nedeniyle erişim başarısız oldu. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |Streammingpolicy içindeki yapılandırma nedeniyle çıkış biçimi engellendi. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |İçerik için şifreleme gereklidir, çıkış biçimi için teslim ilkesi gerekir. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |Dağıtım ilkesi ayarlarında şifreleme ayarlanmadı. |

## <a name="404-not-found"></a>404 Bulunamadı

İşlem, artık mevcut olmayan bir kaynak üzerinde işlem yapmaya çalışıyor. Örneğin, kaynak zaten silinmiş olabilir.

|Hata kodu|Onaltılık değer |Hata açıklaması|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |İstenen izleme bulunamadı. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |İstenen kaynak bulunamadı. |
|MPE_UNAUTHORIZED |0x80890244 |Erişim yetkisi yok. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |İstenen zaman damgası bulunamadı. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |İstenen dinamik bildirim filtresi bulunamadı. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |İstenen parça dizini geçerli aralığın ötesinde. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Moov arabelleğini almak için canlı medya girişleri bulunamıyor. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |Belirli bir izleme için istenen zamanda parça bulunamıyor.<br/><br/>Parçanın depolamada olmaması olabilir. Sununun parçalara sahip olabileceği farklı bir katmanını deneyin. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |Bildirimde istenen bit hızı için medya girişi bulunamıyor. <br/><br/>Player 'ın bildirimde olmayan belirli bir bit hızını video izlemesi istenebilir.|
|MPE_METADATA_NOT_FOUND |0x80890257 |Bildirimde belirli meta veriler bulunamadı veya depolama alanından yeniden temellendirme bulunamıyor. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |Depolama Işlemi hatası, kaynak bulunamadı. |

## <a name="409-conflict"></a>409 Çakışma

Veya işlemindeki bir kaynak için belirtilen KIMLIK, `PUT` `POST` var olan bir kaynak tarafından alındı. Bu sorunu çözmek için kaynak için başka bir KIMLIK kullanın.

|Hata kodu|Onaltılık değer |Hata açıklaması|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |Depolama Işlemi hatası, çakışma hatası.  |

## <a name="410"></a>410

|Hata kodu|Onaltılık değer |Hata açıklaması|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|Canlı akış için, forceEndTimestamp değeri true olarak ayarlandığında başlangıç veya bitiş zaman damgası geçerli DVR penceresinin dışındadır.|

## <a name="412-precondition-failure"></a>412 Önkoşul hatası

İşlem, sunucuda bulunan sürümden farklı bir eTag belirtti, diğer bir deyişle, iyimser eşzamanlılık hatası. Kaynağın en son sürümünü okuyup istekteki eTag’i güncelleştirdikten sonra isteği yeniden deneyin.

|Hata kodu|Onaltılık değer |Hata açıklaması|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |İstenen parça kullanılamıyor.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|Depolama işlemi hatası, bir önkoşul hatası.|

## <a name="415-unsupported-media-type"></a>415 desteklenmeyen medya türü

İstemci tarafından gönderilen yük biçimi desteklenmeyen bir biçimde.

|Hata kodu|Onaltılık değer |Hata açıklaması|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| Şifrelenmiş olan içerikte şifreleme uygulanmamalıdır.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |Giriş biçimi için şifreleme geçersizdir.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| Teslim ilkesi türü geçersiz.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |Özgün ayarlar birden çok çıktı biçimi tarafından paylaşılabilir.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|Medya biçimi veya türü desteklenmiyor. Örneğin Media Services, 64 üzerinde olan kalite düzeyi sayısını desteklemez. Media Services, FLV video etiketinde birden çok SPS ve birden çok PPS içeren bir video çerçevesini desteklemez.|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| İstenen varlığın giriş biçimi desteklenmiyor. Media Services düzgün (canlı), MP4 (VoD) ve aşamalı indirme biçimlerini destekler.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|İstenen çıkış biçimi desteklenmiyor. Media Services Düzgünleştir, TIRE (CSF, CMAF), HLS (v3, v4, CMAF) ve aşamalı indirme biçimlerini destekler.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|Desteklenmeyen şifreleme türüyle karşılaşıldı.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|İstenen medya türü, çıkış biçimi tarafından desteklenmiyor. Desteklenen türler video, ses veya "SUBT" alt yazı.|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|Kaynak varlık medyası, çıkış biçimiyle uyumlu olmayan bir medya biçimiyle kodlandı.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|Kaynak varlık, çıkış biçimiyle uyumlu olmayan bir video biçimiyle kodlandı. H. hev1, AVC, H. 265 (HEVC, veya hvc1) desteklenir.|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|Kaynak varlık, çıkış biçimiyle uyumlu olmayan bir ses biçimiyle kodlandı. Desteklenen ses biçimleri AAC, E-AC3 (gg +), Dolby DTS.|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|Kaynak korumalı varlık çıkış biçimine dönüştürülemez.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|Koruma biçimi çıkış biçimi tarafından desteklenmez.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|Koruma biçimi giriş biçimi tarafından desteklenmez.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|Video NAL birimi geçersiz; Örneğin, yalnızca örnekteki ilk NAL bir AUD olabilir.|
|MPE_INVALID_NALU_SIZE|0x80890260|Geçersiz NAL birim boyutu.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|Geçersiz NAL birim uzunluğu değeri.|
|MPE_FILTER_INVALID|0x80890236|Geçersiz dinamik bildirim filtreleri.|
|MPE_FILTER_VERSION_INVALID|0x80890237|Geçersiz veya desteklenmeyen filtre sürümleri.|
|MPE_FILTER_TYPE_INVALID|0x80890238|Geçersiz filtre türü.|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|Filtre tarafından geçersiz Aralık belirtildi.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|Filtre tarafından geçersiz izleme özniteliği belirtildi.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|Filtre tarafından geçersiz sunum penceresi uzunluğu belirtildi.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|Filtre tarafından geçersiz canlı geri dönme belirtildi.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|Eski filtrelerinde yalnızca bir Abstimeınhns öğesi desteklenir.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|Filtreler uygulandıktan sonra hiç akış yok.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|Canlı geri dönme, DVR penceresinin dışındadır.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|Canlı geri dönme, sunu penceresinden daha büyüktür.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|En fazla on (10) izin verilen varsayılan filtre aşıldı.|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|Birleşik istek filtrelerinde birden çok ilk video kalitesi işlecine izin verilmez.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|İlk kalite bit hızı özniteliklerinin sayısı bir (1) olmalıdır.|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|HLS segmenti, DVR penceresinin üçte birine ve HLS geri kapatmasına eşit olmalıdır.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|Parçaların süreleri yaklaşık 20 saniyeden az veya bu değere eşit olmalı ya da giriş kalitesi düzeyleri zaman hizalı değil.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|DTS 'e özgü hata, DTS kutusu ayrıştırma sırasında Dtsspeckurgu kutusunda bulunması gerektiğinde ReservedBox bulunamıyor.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|DTS 'e özgü hata, DTS kutusu ayrıştırma sırasında Dtsspeckurgu kutusunda kanal bulunamadı.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|DTS 'e özgü hata, Dtsspeckurgu kutusunda örnek tür uyuşmazlığı.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|DTS 'e özgü hata, çok varlık ayarlanmış ancak DTSH örnek türü uyuşmazlığı.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|DTS 'e özgü hata, çekirdek akış boyutu geçersiz.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|DTS 'e özgü hata, örnek çözümleme geçersizdir.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|DTS 'e özgü hata, alt akış uzantısı dizini geçersiz.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|DTS 'e özgü hata, alt akış blok numarası geçersiz.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|DTS 'e özgü hata, örnekleme sıklığı geçersiz.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|DTS 'e özgü hata, alt akış uzantısında başvuru saati kodu geçersiz.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|DTS 'e özgü hata, konuşmacı yeniden eşleme kümesi sayısı geçersiz.|

Şifreleme makaleleri ve örnekleri için bkz.:

- [Kavram: içerik koruma](drm-content-protection-concept.md)
- [Kavram: Içerik anahtarı Ilkeleri](drm-content-key-policy-concept.md)
- [Kavram: akış Ilkeleri](stream-streaming-policy-concept.md)
- [Örnek: AES şifrelemesi ile koruma](drm-playready-license-template-concept.md)
- [Örnek: DRM ile koruma](drm-protect-with-drm-tutorial.md)

Filtre Kılavuzu için bkz.:

- [Kavram: dinamik bildirimler](filters-dynamic-manifest-concept.md)
- [Kavram: filtreler](filters-concept.md)
- [Örnek: REST API 'Leri ile filtre oluşturma](filters-dynamic-manifest-rest-howto.md)
- [Örnek: .NET ile filtre oluşturma](filters-dynamic-manifest-dotnet-how-to.md)
- [Örnek: CLı ile filtre oluşturma](filters-dynamic-manifest-cli-how-to.md)

Canlı makaleler ve örnekler için bkz.:

- [Kavram: canlı akışa genel bakış](stream-live-streaming-concept.md)
- [Kavram: canlı olaylar ve canlı çıktılar](live-event-outputs-concept.md)
- [Örnek: canlı akış öğreticisi](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 Aralık Karşılanamıyor

|Hata kodu|Onaltılık değer |Hata açıklaması|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|Depolama Işlemi hatası, döndürülen http 416 hatası, geçersiz Aralık.|

## <a name="500-internal-server-error"></a>500 İç Sunucu Hatası

İsteğin işlenmesi sırasında, Media Services işlemin devam etmesini önleyen bir hata oluştu.  

|Hata kodu|Onaltılık değer |Hata açıklaması|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|ERROR_WINHTTP_TIMEOUT WinHTTP hata kodundan alındı ve çevrilir (0x00002ee2).|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|ERROR_WINHTTP_CONNECTION_ERROR WinHTTP hata kodundan alındı ve çevrilir (0x00002efe).|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|ERROR_WINHTTP_NAME_NOT_RESOLVED WinHTTP hata kodundan alındı ve çevrilir (0x00002ee7).|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|Depolama Işlemi hatası, HTTP 500 hatalarından birinde genel InternalError.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|Depolama Işlemi hatası, HTTP 500 hatalarından birindeki genel işlem zaman aşımına uğradı.|
|MPE_STORAGE_FAILURE|0x808900F2|Depolama Işlemi hatası, InternalError veya Operationtimeınterme 'den başka HTTP 500 hataları.|

## <a name="503-service-unavailable"></a>503 Hizmet Kullanılamıyor

Sunucu şu anda istekleri alamıyor. Bu hata hizmetin aşırı istek almasından kaynaklanıyor olabilir. Media Services azaltma mekanizması hizmete çok sayıda istek gönderen uygulamaların kaynak kullanımını kısıtlar.

> [!NOTE]
> 503 hatasını alma nedeniniz hakkında daha fazla bilgi edinmek için hata iletisini ve hata kodu dizesini kontrol edin. Bu hata her zaman azaltma anlamına gelmez.
> 

|Hata kodu|Onaltılık değer |Hata açıklaması|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|Depolama Işlemi hatası, alınan HTTP sunucu meşgul hatası 503.|

## <a name="ask-questions-give-feedback-get-updates"></a>Soru sorun, geri bildirimde bulunun, güncelleştirmeleri al

Soru sormak, geri bildirimde bulunmak ve Media Services hakkında güncelleştirmeler almak için [Azure Media Services Community](media-services-community.md) makalesine göz atın.

## <a name="see-also"></a>Ayrıca bkz.

- [Kodlama hata kodları](/rest/api/media/jobs/get#joberrorcode)
- [Azure Media Services kavramlar](concepts-overview.md)
- [Kotalar ve sınırlar](limits-quotas-constraints-reference.md)

## <a name="next-steps"></a>Sonraki adımlar

[Örnek: .NET ile ApiException 'a hata kodu ve Ileti erişimi](configure-connect-dotnet-howto.md#connect-to-the-net-client)
