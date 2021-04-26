---
title: Media Services v3 Widevine lisans şablonuna genel bakış
description: Widevine lisans şablonuyla Azure Media Services ve Widevine lisanslarını yapılandırmak için nasıl kullanılacağını öğrenin.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 098421239549f1ab8418ced03de14c11dae84065
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106069156"
---
# <a name="media-services-v3-with-widevine-license-template-overview"></a>Wıdevine lisans şablonuna genel bakış ile Media Services v3

Azure Media Services **Google Widevine** ile içeriğinizi şifrelemenizi sağlar. Media Services Ayrıca Widevine lisansları teslim etmek için bir hizmet sağlar. Wıdevine lisanslarını yapılandırmak için Azure Media Services API 'Leri kullanabilirsiniz. Bir oyuncu Widevine korumalı içeriğinizi yürütmeye çalıştığında, lisans almak için lisans teslim hizmetine bir istek gönderilir. Lisans hizmeti isteği onayladığında, hizmet lisansı yayınlar. İstemciye gönderilir ve belirtilen içeriğin şifresini çözmek ve yürütmek için kullanılır.

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

Widevine lisans isteği JSON iletisi olarak biçimlendirilir.  



```json
{  
    "payload":"<license challenge>",
    "content_id": "<content id>"
    "provider": "<provider>"
    "allowed_track_types":"<types>",
    "content_key_specs":[  
        {  
            "track_type":"<track type 1>"
        },
        {  
            "track_type":"<track type 2>"
        },
        …
    ],
    "policy_overrides":{  
        "can_play":<can play>,
        "can persist":<can persist>,
        "can_renew":<can renew>,
        "rental_duration_seconds":<rental duration>,
        "playback_duration_seconds":<playback duration>,
        "license_duration_seconds":<license duration>,
        "renewal_recovery_duration_seconds":<renewal recovery duration>,
        "renewal_server_url":"<renewal server url>",
        "renewal_delay_seconds":<renewal delay>,
        "renewal_retry_interval_seconds":<renewal retry interval>,
        "renew_with_usage":<renew with usage>
    }
}
```

>[!NOTE]
> Değer olmadan boş bir ileti oluşturabilirsiniz, yalnızca " {} ." Ardından, varsayılan olarak bir lisans şablonu oluşturulur. Çoğu durumda varsayılan değer kullanılır. Microsoft tabanlı lisans-teslim senaryoları her zaman varsayılan değerleri kullanmalıdır. "Sağlayıcı" ve "content_id" değerlerini ayarlamanız gerekiyorsa, bir sağlayıcının Wıdevine kimlik bilgileriyle eşleşmesi gerekir.

## <a name="json-message"></a>JSON iletisi

| Name | Değer | Açıklama |
| --- | --- | --- |
| yük |Base64 ile kodlanmış dize |İstemci tarafından gönderilen lisans isteği. |
| content_id |Base64 ile kodlanmış dize |Her content_key_specs. track_type için anahtar KIMLIĞINI ve içerik anahtarını türetmede kullanılan tanımlayıcı. |
| sağlayıcısını |string |İçerik anahtarları ve ilkeleri aramak için kullanılır. Widevine lisans teslimi için Microsoft anahtar teslimi kullanılıyorsa, bu parametre yoksayılır. |
| policy_name |string |Daha önce kaydedilen bir ilkenin adı. İsteğe bağlı. |
| allowed_track_types |enum |SD_ONLY veya SD_HD. Bir lisansa hangi içerik anahtarlarının dahil edileceğini denetler. |
| content_key_specs |JSON yapıları dizisi, "Içerik anahtar özellikleri" bölümüne bakın.  |Hangi içerik anahtarlarının dönebileceği üzerinde daha ayrıntılı bir denetim. Daha fazla bilgi için "Içerik anahtarı özellikleri" bölümüne bakın. Allowed_track_types ve content_key_specs değerlerinden yalnızca biri belirtilebilir. |
| use_policy_overrides_exclusively |Boolean, true veya false |Policy_overrides tarafından belirtilen ilke özniteliklerini kullanın ve daha önce depolanan tüm ilkeleri atlayın. |
| policy_overrides |JSON yapısı, "Ilke geçersiz kılmaları" bölümüne bakın. |Bu lisansın ilke ayarları.  Bu varlığın önceden tanımlanmış bir ilkesi varsa, belirtilen değerler kullanılır. |
| session_init |JSON yapısı, "oturum başlatma" bölümüne bakın. |İsteğe bağlı veriler lisansa geçirilir. |
| parse_only |Boolean, true veya false |Lisans isteği ayrıştırılır, ancak lisans verilmez. Ancak, lisans isteğinden alınan değerler yanıtta döndürülür. |

## <a name="content-key-specs"></a>İçerik anahtarı özellikleri
Önceden var olan bir ilke varsa, içerik anahtarı belirtiminde bir değer belirtmeniz gerekmez. Bu içerikle ilişkili önceden varolan ilke, yüksek bant genişliği dijital Content Protection (HDCP) ve kopyalama genel yönetim sistemi (CGMS) gibi çıkış korumasını belirlemede kullanılır. Önceden var olan bir ilke Widevine lisans sunucusuna kayıtlı değilse, içerik sağlayıcısı değerleri lisans isteğine ekleyebilir.   

Her bir content_key_specs değeri, use_policy_overrides_exclusively seçeneğinden bağımsız olarak tüm parçalar için belirtilmelidir. 

| Name | Değer | Açıklama |
| --- | --- | --- |
| content_key_specs. track_type |string |Bir izleme türü adı. Lisans isteğinde content_key_specs belirtilmişse, tüm izleme türlerini açık olarak belirttiğinizden emin olun. Bunun yapılmaması, son 10 saniye içinde oynatılmasına neden olur. |
| content_key_specs  <br/> security_level |Int32 |Kayıttan yürütme için istemci sağlamlık gereksinimlerini tanımlar. <br/> -Yazılım tabanlı beyaz kutu şifrelemesi gereklidir. <br/> -Yazılım şifrelemesi ve karıştırılmış bir kod çözücü gereklidir. <br/> -Anahtar malzeme ve şifreleme işlemlerinin, donanım ile desteklenen bir güvenilir yürütme ortamında gerçekleştirilmesi gerekir. <br/> -İçerik şifrelemesi ve kodunun çözülmesi, donanım tarafından desteklenen bir güvenilir yürütme ortamında gerçekleştirilmelidir.  <br/> -Şifreleme, kod çözme ve medyanın tüm işlenmesi (sıkıştırılmış ve sıkıştırılmamış), donanım tarafından desteklenen bir güvenilir yürütme ortamında işlenmelidir. |
| content_key_specs <br/> required_output_protection. HDC |dize, biri HDCP_NONE, HDCP_V1 HDCP_V2 |HDCP gerekip gerekmediğini gösterir. |
| content_key_specs <br/>anahtar |Biçiminde<br/>kodlanmış dize |Bu izleme için kullanılacak içerik anahtarı. Belirtilmişse track_type veya key_id gereklidir. İçerik sağlayıcısı, Widevine lisans sunucusunun bir anahtar oluşturmasını veya arama yapmasına izin vermek yerine bu izleme için içerik anahtarını eklemek için bu seçeneği kullanabilir. |
| content_key_specs content_key_specs.key_id |Base64 kodlamalı dize ikili, 16 bayt |Anahtar için benzersiz tanımlayıcı. |

## <a name="policy-overrides"></a>İlke geçersiz kılmaları
| Name | Değer | Açıklama |
| --- | --- | --- |
| policy_overrides&#46;can_play |Boolean, true veya false |İçeriğin kayıttan yürütmeye izin verildiğini belirtir. Varsayılan değer false’tur. |
| policy_overrides&#46;can_persist |Boolean, true veya false |Çevrimdışı kullanım için lisansın kalıcı depolamaya kalıcı olabileceğini belirtir. Varsayılan değer false’tur. |
| policy_overrides&#46;can_renew |Boolean, true veya false |Bu lisansın yenilenmesini izin verildiğini belirtir. Doğru ise, Lisansın süresi sinyal ile genişletilebilir. Varsayılan değer false’tur. |
| policy_overrides&#46;license_duration_seconds |tutulamaz |Bu özel lisansın zaman penceresini gösterir. 0 değeri, Duration için bir sınır olmadığını gösterir. Varsayılan değer 0 ' dır. |
| policy_overrides&#46;rental_duration_seconds |tutulamaz |Kayıttan yürütmeye izin verildiğinde zaman penceresini gösterir. 0 değeri, Duration için bir sınır olmadığını gösterir. Varsayılan değer 0 ' dır. |
| policy_overrides&#46;playback_duration_seconds |tutulamaz |Kayıttan yürütme sonrasında zaman içindeki görüntüleme penceresi lisans süresi içinde başlar. 0 değeri, Duration için bir sınır olmadığını gösterir. Varsayılan değer 0 ' dır. |
| policy_overrides&#46;renewal_server_url |string |Bu lisans için tüm sinyal (yenileme) istekleri belirtilen URL 'ye yönlendirilir. Bu alan yalnızca can_renew true ise kullanılır. |
| policy_overrides&#46;renewal_delay_seconds |tutulamaz |Yenileme ilk denendikten sonra license_start_time kaç saniye sonra. Bu alan yalnızca can_renew true ise kullanılır. Varsayılan değer 0 ' dır. |
| policy_overrides&#46;renewal_retry_interval_seconds |tutulamaz |Hata durumunda sonraki lisans yenileme istekleri arasındaki gecikme süresini saniye cinsinden belirtir. Bu alan yalnızca can_renew true ise kullanılır. |
| policy_overrides&#46;renewal_recovery_duration_seconds |tutulamaz |Yenileme denenirken kayıttan yürütmenin devam edebildiği zaman penceresi, ancak lisans sunucusu ile ilgili arka uç sorunları nedeniyle başarısız oldu. 0 değeri, Duration için bir sınır olmadığını gösterir. Bu alan yalnızca can_renew true ise kullanılır. |
| policy_overrides&#46;renew_with_usage |Boolean, true veya false |Kullanım başladığında lisansın yenileme için gönderileceğini belirtir. Bu alan yalnızca can_renew true ise kullanılır. |

## <a name="session-initialization"></a>Oturum başlatma
| Name | Değer | Açıklama |
| --- | --- | --- |
| provider_session_token |Base64 ile kodlanmış dize |Bu oturum belirteci lisansa geri geçirilir ve sonraki yenilemelerde bulunur. Oturum belirteci oturumlardan daha fazla kalıcı yapmaz. |
| provider_client_token |Base64 ile kodlanmış dize |Lisans yanıtına geri göndermek için istemci belirteci. Lisans isteği bir istemci belirteci içeriyorsa, bu değer yoksayılır. İstemci belirteci, lisans oturumlarının ötesinde devam ettirir. |
| override_provider_client_token |Boolean, true veya false |Yanlış ise ve lisans isteği bir istemci belirteci içeriyorsa, bu yapıda bir istemci belirteci belirtilmiş olsa bile istekten belirteci kullanın. True ise, her zaman bu yapıda belirtilen belirteci kullanın. |

## <a name="configure-your-widevine-license-with-net"></a>.NET ile Widevine lisansınızı yapılandırma 

Media Services Widevine lisansı yapılandırmanıza imkan tanıyan bir sınıf sağlar. Lisansı oluşturmak için JSON 'u [Widevinetemplate](/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate)'e geçirin.

Şablonu yapılandırmak için şunları yapabilirsiniz:

### <a name="directly-construct-a-json-string"></a>Doğrudan bir JSON dizesi oluşturun

Bu yöntem hata durumunda olabilir. [Gerekli sınıfları tanımlama ve JSON için serileştirme](#classes)bölümünde açıklanan diğer yöntemi kullanmanız önerilir.

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a name="define-needed-classes-and-serialize-to-json"></a><a id="classes"></a> Gerekli sınıfları tanımlayın ve JSON 'a serileştirin

#### <a name="define-classes"></a>Sınıfları tanımlama

Aşağıdaki örnek, Widevine JSON şemasına eşlenen sınıfların tanımlarının bir örneğini gösterir. Sınıfları JSON dizesinde serileştirmek için bir örneği oluşturabilirsiniz.  

```csharp
/// <summary>
/// Widevine PolicyOverrides class.
/// </summary>
public class PolicyOverrides
{
    /// <summary>
    /// Gets or sets a value indicating whether playback of the content is allowed. Default is false.
    /// </summary>
    [JsonProperty("can_play")]
    public bool CanPlay { get; set; }

    /// <summary>
    /// Gets or sets a value indicating whether the license might be persisted to nonvolatile storage for offline use. Default is false.
    /// </summary>
    [JsonProperty("can_persist")]
    public bool CanPersist { get; set; }

    /// <summary>
    /// Gets or sets a value indicating whether renewal of this license is allowed. If true, the duration of the license can be extended by heartbeat. Default is false.
    /// </summary>
    [JsonProperty("can_renew")]
    public bool CanRenew { get; set; }

    /// <summary>
    /// Gets or sets the time window while playback is permitted. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("rental_duration_seconds")]
    public int RentalDurationSeconds { get; set; }

    /// <summary>
    /// Gets or sets the viewing window of time after playback starts within the license duration. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("playback_duration_seconds")]
    public int PlaybackDurationSeconds { get; set; }

    /// <summary>
    /// Gets or sets the time window for this specific license. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("license_duration_seconds")]
    public int LicenseDurationSeconds { get; set; }
}

/// <summary>
/// Widevine ContentKeySpec class.
/// </summary>
public class ContentKeySpec
{
    /// <summary>
    /// Gets or sets track type.
    /// If content_key_specs is specified in the license request, make sure to specify all track types explicitly.
    /// Failure to do so results in failure to play back past 10 seconds.
    /// </summary>
    [JsonProperty("track_type")]
    public string TrackType { get; set; }

    /// <summary>
    /// Gets or sets client robustness requirements for playback.
    /// Software-based white-box cryptography is required.
    /// Software cryptography and an obfuscated decoder are required.
    /// The key material and cryptography operations must be performed within a hardware-backed trusted execution environment.
    /// The cryptography and decoding of content must be performed within a hardware-backed trusted execution environment.
    /// The cryptography, decoding, and all handling of the media (compressed and uncompressed) must be handled within a hardware-backed trusted execution environment.
    /// </summary>
    [JsonProperty("security_level")]
    public int SecurityLevel { get; set; }

    /// <summary>
    /// Gets or sets the OutputProtection.
    /// </summary>
    [JsonProperty("required_output_protection")]
    public OutputProtection RequiredOutputProtection { get; set; }
}

/// <summary>
/// OutputProtection Widevine class.
/// </summary>
public class OutputProtection
{
    /// <summary>
    /// Gets or sets HDCP protection.
    /// Supported values : HDCP_NONE, HDCP_V1, HDCP_V2
    /// </summary>
    [JsonProperty("hdcp")]
    public string HDCP { get; set; }

    /// <summary>
    /// Gets or sets CGMS.
    /// </summary>
    [JsonProperty("cgms_flags")]
    public string CgmsFlags { get; set; }
}

/// <summary>
/// Widevine template.
/// </summary>
public class WidevineTemplate
{
    /// <summary>
    /// Gets or sets the allowed track types.
    /// SD_ONLY or SD_HD.
    /// Controls which content keys are included in a license.
    /// </summary>
    [JsonProperty("allowed_track_types")]
    public string AllowedTrackTypes { get; set; }

    /// <summary>
    /// Gets or sets a finer-grained control on which content keys to return.
    /// For more information, see the section "Content key specs."
    /// Only one of the allowed_track_types and content_key_specs values can be specified.
    /// </summary>
    [JsonProperty("content_key_specs")]
    public ContentKeySpec[] ContentKeySpecs { get; set; }

    /// <summary>
    /// Gets or sets policy settings for the license.
    /// In the event this asset has a predefined policy, these specified values are used.
    /// </summary>
    [JsonProperty("policy_overrides")]
    public PolicyOverrides PolicyOverrides { get; set; }
}
```

#### <a name="configure-the-license"></a>Lisansı yapılandırma

[Widevınetemplate](/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate)'i yapılandırmak IÇIN kullanılan JSON oluşturmak için önceki bölümde tanımlanan sınıfları kullanın:

```csharp
private static ContentKeyPolicyWidevineConfiguration ConfigureWidevineLicenseTempate()
{
    WidevineTemplate template = new WidevineTemplate()
    {
        AllowedTrackTypes = "SD_HD",
        ContentKeySpecs = new ContentKeySpec[]
        {
            new ContentKeySpec()
            {
                TrackType = "SD",
                SecurityLevel = 1,
                RequiredOutputProtection = new OutputProtection()
                {
                    HDCP = "HDCP_V2"
                }
            }
        },
        PolicyOverrides = new PolicyOverrides()
        {
            CanPlay = true,
            CanPersist = true,
            CanRenew = false,
            RentalDurationSeconds = 2592000,
            PlaybackDurationSeconds = 10800,
            LicenseDurationSeconds = 604800,
        }
    };

    ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
    {
        WidevineTemplate = Newtonsoft.Json.JsonConvert.SerializeObject(template)
    };
    return objContentKeyPolicyWidevineConfiguration;
}
```

## <a name="additional-notes"></a>Ek notlar

* Widevine, Google Inc. tarafından sunulan bir hizmettir ve Google, Inc 'nin hizmet koşullarına ve gizlilik Ilkesine tabidir.

## <a name="next-steps"></a>Sonraki adımlar

[DRM ile koruma](drm-protect-with-drm-tutorial.md)
