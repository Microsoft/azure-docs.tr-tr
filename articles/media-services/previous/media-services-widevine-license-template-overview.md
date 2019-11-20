---
title: Widevine lisans şablonuna genel bakış | Microsoft Docs
description: Bu konu, Widevine lisanslarını yapılandırmak için kullanılan bir Widevine lisans şablonuna genel bakış sunar.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 0e6f1f05-7ed6-4ed6-82a0-0cc2182b075a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: ab9725dd50487cf9df9d6fb967959b276f39979f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162465"
---
# <a name="widevine-license-template-overview"></a>Widevine lisans şablonuna genel bakış 
Google Widevine lisanslarını yapılandırmak ve istemek için Azure Media Services kullanabilirsiniz. Oynatıcı Widevine korumalı içeriğinizi yürütmeye çalıştığında, lisans almak için lisans teslim hizmetine bir istek gönderilir. Lisans hizmeti isteği onayladığında, hizmet lisansı yayınlar. İstemciye gönderilir ve belirtilen içeriğin şifresini çözmek ve yürütmek için kullanılır.

Widevine lisans isteği JSON iletisi olarak biçimlendirilir.  

>[!NOTE]
> Değer olmadan boş bir ileti oluşturabilirsiniz, yalnızca "{}." Ardından, varsayılan olarak bir lisans şablonu oluşturulur. Çoğu durumda varsayılan değer kullanılır. Microsoft tabanlı lisans-teslim senaryoları her zaman varsayılan değerleri kullanmalıdır. "Sağlayıcı" ve "content_id" değerlerini ayarlamanız gerekiyorsa, bir sağlayıcının Wıdevine kimlik bilgileriyle eşleşmesi gerekir.

    {  
       "payload": "<license challenge>",
       "content_id": "<content id>" 
       "provider": "<provider>"
       "allowed_track_types": "<types>",
       "content_key_specs": [  
          {  
             "track_type": "<track type 1>"
          },
          {  
             "track_type": "<track type 2>"
          },
          …
       ],
       "policy_overrides": {  
          "can_play": <can play>,
          "can persist": <can persist>,
          "can_renew": <can renew>,
          "rental_duration_seconds": <rental duration>,
          "playback_duration_seconds": <playback duration>,
          "license_duration_seconds": <license duration>,
          "renewal_recovery_duration_seconds": <renewal recovery duration>,
          "renewal_server_url": "<renewal server url>",
          "renewal_delay_seconds": <renewal delay>,
          "renewal_retry_interval_seconds": <renewal retry interval>,
          "renew_with_usage": <renew with usage>
       }
    }

## <a name="json-message"></a>JSON iletisi
| Adı | Değer | Açıklama |
| --- | --- | --- |
| Te |Base64 ile kodlanmış dize |İstemci tarafından gönderilen lisans isteği. |
| content_id |Base64 ile kodlanmış dize |Her content_key_specs. track_type için anahtar KIMLIĞINI ve içerik anahtarını türetmede kullanılan tanımlayıcı. |
| Sağlayıcısını |string |İçerik anahtarları ve ilkeleri aramak için kullanılır. Widevine lisans teslimi için Microsoft anahtar teslimi kullanılıyorsa, bu parametre yoksayılır. |
| policy_name |string |Daha önce kaydedilen bir ilkenin adı. İsteğe bağlı. |
| allowed_track_types |yardımının |SD_ONLY veya SD_HD. Bir lisansa hangi içerik anahtarlarının dahil edileceğini denetler. |
| content_key_specs |JSON yapıları dizisi, "Içerik anahtar özellikleri" bölümüne bakın.  |Hangi içerik anahtarlarının dönebileceği üzerinde daha ayrıntılı bir denetim. Daha fazla bilgi için "Içerik anahtarı özellikleri" bölümüne bakın. Allowed_track_types ve content_key_specs değerlerinden yalnızca biri belirtilebilir. |
| use_policy_overrides_exclusively |Boolean, true veya false |Policy_overrides tarafından belirtilen ilke özniteliklerini kullanın ve daha önce depolanan tüm ilkeleri atlayın. |
| policy_overrides |JSON yapısı, "Ilke geçersiz kılmaları" bölümüne bakın. |Bu lisansın ilke ayarları.  Bu varlığın önceden tanımlanmış bir ilkesi varsa, belirtilen değerler kullanılır. |
| session_init |JSON yapısı, "oturum başlatma" bölümüne bakın. |İsteğe bağlı veriler lisansa geçirilir. |
| parse_only |Boolean, true veya false |Lisans isteği ayrıştırılır, ancak lisans verilmez. Ancak, lisans isteğinden alınan değerler yanıtta döndürülür. |

## <a name="content-key-specs"></a>İçerik anahtarı özellikleri
Önceden var olan bir ilke varsa, içerik anahtarı belirtiminde bir değer belirtmeniz gerekmez. Bu içerikle ilişkili önceden varolan ilke, yüksek bant genişliği dijital Content Protection (HDCP) ve kopyalama genel yönetim sistemi (CGMS) gibi çıkış korumasını belirlemede kullanılır. Önceden var olan bir ilke Widevine lisans sunucusuna kayıtlı değilse, içerik sağlayıcısı değerleri lisans isteğine ekleyebilir.   

Use_policy_overrides_exclusively seçeneğinden bağımsız olarak tüm parçalar için her content_key_specs değeri belirtilmelidir. 

| Adı | Değer | Açıklama |
| --- | --- | --- |
| content_key_specs. track_type |string |Bir izleme türü adı. Lisans isteğinde content_key_specs belirtilmişse, tüm izleme türlerini açık olarak belirttiğinizden emin olun. Bunun yapılmaması, son 10 saniye içinde oynatılmasına neden olur. |
| content_key_specs  <br/> security_level |Int32 |Kayıttan yürütme için istemci sağlamlık gereksinimlerini tanımlar. <br/> -Yazılım tabanlı beyaz kutu şifrelemesi gereklidir. <br/> -Yazılım şifrelemesi ve karıştırılmış bir kod çözücü gereklidir. <br/> -Anahtar malzeme ve şifreleme işlemlerinin, donanım ile desteklenen bir güvenilir yürütme ortamında gerçekleştirilmesi gerekir. <br/> -İçerik şifrelemesi ve kodunun çözülmesi, donanım tarafından desteklenen bir güvenilir yürütme ortamında gerçekleştirilmelidir.  <br/> -Şifreleme, kod çözme ve medyanın tüm işlenmesi (sıkıştırılmış ve sıkıştırılmamış), donanım tarafından desteklenen bir güvenilir yürütme ortamında işlenmelidir. |
| content_key_specs <br/> required_output_protection. HDC |dize, birisi HDCP_NONE, HDCP_V1, HDCP_V2 |HDCP gerekip gerekmediğini gösterir. |
| content_key_specs <br/>anahtar |Biçiminde<br/>kodlanmış dize |Bu izleme için kullanılacak içerik anahtarı. Belirtilmişse, track_type veya key_id gereklidir. İçerik sağlayıcısı, Widevine lisans sunucusunun bir anahtar oluşturmasını veya arama yapmasına izin vermek yerine bu izleme için içerik anahtarını eklemek için bu seçeneği kullanabilir. |
| content_key_specs. Key _ID |Base64 kodlamalı dize ikili, 16 bayt |Anahtar için benzersiz tanımlayıcı. |

## <a name="policy-overrides"></a>İlke geçersiz kılmaları
| Adı | Değer | Açıklama |
| --- | --- | --- |
| policy_overrides. can_play |Boolean, true veya false |İçeriğin kayıttan yürütmeye izin verildiğini belirtir. Varsayılan değer false’tur. |
| policy_overrides. can_persist |Boolean, true veya false |Çevrimdışı kullanım için lisansın kalıcı depolamaya kalıcı olabileceğini belirtir. Varsayılan değer false’tur. |
| policy_overrides. can_renew |Boolean, true veya false |Bu lisansın yenilenmesini izin verildiğini belirtir. Doğru ise, Lisansın süresi sinyal ile genişletilebilir. Varsayılan değer false’tur. |
| policy_overrides. license_duration_seconds |Tutulamaz |Bu özel lisansın zaman penceresini gösterir. 0 değeri, Duration için bir sınır olmadığını gösterir. Varsayılan değer 0 ' dır. |
| policy_overrides. rental_duration_seconds |Tutulamaz |Kayıttan yürütmeye izin verildiğinde zaman penceresini gösterir. 0 değeri, Duration için bir sınır olmadığını gösterir. Varsayılan değer 0 ' dır. |
| policy_overrides. playback_duration_seconds |Tutulamaz |Kayıttan yürütme sonrasında zaman içindeki görüntüleme penceresi lisans süresi içinde başlar. 0 değeri, Duration için bir sınır olmadığını gösterir. Varsayılan değer 0 ' dır. |
| policy_overrides. renewal_server_url |string |Bu lisansın tüm sinyal (yenileme) istekleri belirtilen URL 'ye yönlendirilir. Bu alan yalnızca can_renew true ise kullanılır. |
| policy_overrides. renewal_delay_seconds |Tutulamaz |Yenileme işlemi ilk denendiğinde license_start_time sonra kaç saniye sonra. Bu alan yalnızca can_renew true ise kullanılır. Varsayılan değer 0 ' dır. |
| policy_overrides. renewal_retry_interval_seconds |Tutulamaz |Hata durumunda sonraki lisans yenileme istekleri arasındaki gecikme süresini saniye cinsinden belirtir. Bu alan yalnızca can_renew true ise kullanılır. |
| policy_overrides. renewal_recovery_duration_seconds |Tutulamaz |Yenileme denenirken kayıttan yürütmenin devam edebildiği zaman penceresi, ancak lisans sunucusu ile ilgili arka uç sorunları nedeniyle başarısız oldu. 0 değeri, Duration için bir sınır olmadığını gösterir. Bu alan yalnızca can_renew true ise kullanılır. |
| policy_overrides. renew_with_usage |Boolean, true veya false |Kullanım başladığında lisansın yenileme için gönderileceğini belirtir. Bu alan yalnızca can_renew true ise kullanılır. |

## <a name="session-initialization"></a>Oturum başlatma
| Adı | Değer | Açıklama |
| --- | --- | --- |
| provider_session_token |Base64 ile kodlanmış dize |Bu oturum belirteci lisansa geri geçirilir ve sonraki yenilemelerde bulunur. Oturum belirteci oturumlardan daha fazla kalıcı yapmaz. |
| provider_client_token |Base64 ile kodlanmış dize |Lisans yanıtına geri göndermek için istemci belirteci. Lisans isteği bir istemci belirteci içeriyorsa, bu değer yoksayılır. İstemci belirteci, lisans oturumlarının ötesinde devam ettirir. |
| override_provider_client_token |Boolean, true veya false |Yanlış ise ve lisans isteği bir istemci belirteci içeriyorsa, bu yapıda bir istemci belirteci belirtilmiş olsa bile istekten belirteci kullanın. True ise, her zaman bu yapıda belirtilen belirteci kullanın. |

## <a name="configure-your-widevine-licenses-by-using-net-types"></a>.NET türlerini kullanarak Widevine lisanslarınızı yapılandırma
Media Services, Widevine lisanslarınızı yapılandırmak için kullanabileceğiniz .NET API 'Leri sağlar. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>Media Services .NET SDK 'sında tanımlanan sınıflar
Aşağıdaki sınıflar bu türlerin tanımlarıdır:

    public class WidevineMessage
    {
        public WidevineMessage();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public AllowedTrackTypes? allowed_track_types { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public ContentKeySpecs[] content_key_specs { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public object policy_overrides { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum AllowedTrackTypes
    {
        SD_ONLY = 0,
        SD_HD = 1
    }
    public class ContentKeySpecs
    {
        public ContentKeySpecs();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string key_id { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public RequiredOutputProtection required_output_protection { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public int? security_level { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string track_type { get; set; }
    }

    public class RequiredOutputProtection
    {
        public RequiredOutputProtection();

        public Hdcp hdcp { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum Hdcp
    {
        HDCP_NONE = 0,
        HDCP_V1 = 1,
        HDCP_V2 = 2
    }

### <a name="example"></a>Örnek
Aşağıdaki örnek, basit bir Widevine lisansı yapılandırmak için .NET API 'Lerinin nasıl kullanılacağını gösterir:

    private static string ConfigureWidevineLicenseTemplate()
    {
        var template = new WidevineMessage
        {
            allowed_track_types = AllowedTrackTypes.SD_HD,
            content_key_specs = new[]
            {
                new ContentKeySpecs
                {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                }
            },
            policy_overrides = new
            {
                can_play = true,
                can_persist = true,
                can_renew = false
            }
        };

        string configuration = JsonConvert.SerializeObject(template);
        return configuration;
    }


## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirim sağlayın
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Ayrıca bkz.
[PlayReady ve/veya Widevine dinamik ortak şifreleme kullanma](media-services-protect-with-playready-widevine.md)

