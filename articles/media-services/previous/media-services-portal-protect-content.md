---
title: Azure portal kullanarak içerik koruma ilkelerini yapılandırma | Microsoft Docs
description: Bu makalede, içerik koruma ilkelerini yapılandırmak için Azure portal nasıl kullanılacağı gösterilmektedir. Makalede ayrıca varlıklarınız için dinamik şifrelemenin nasıl etkinleştirileceği gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: c6620158965271ba99f80c2fbd36504fdbea7ecf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103010788"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>Azure portal kullanarak içerik koruma ilkelerini yapılandırma

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Bu öğreticiyi tamamlamak için bir Azure hesabınızın olması gerekir. Ayrıntılı bilgi için bkz. [Azure Ücretsiz Deneme Sürümü](https://azure.microsoft.com/pricing/free-trial/).   Media Services V2 'ye yeni özellik veya işlevsellik eklenmiyor >. <br/>[V3 Media Services](../latest/index.yml)en son sürüme göz atın. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-v-2-v-3-migration-introduction.md)
>

 Azure Media Services, medyanızın depolama, işleme ve teslim aracılığıyla bilgisayarınızdan ayrıldığı zamandan güvenli hale getirebilirsiniz. Media Services, içeriğinizi, 128 bit şifreleme anahtarlarını kullanarak Gelişmiş Şifreleme Standardı (AES) ile şifreli olarak dağıtmak için kullanabilirsiniz. Ayrıca, PlayReady ve/veya Widevine dijital hak yönetimi (DRM) ve Apple FairPlay kullanarak ortak şifreleme (CENC) ile de kullanabilirsiniz. 

Media Services, yetkili istemcilere DRM lisansları ve AES şifresiz anahtarlar sunmaya yönelik bir hizmet sağlar. Tüm şifrelemeleri için bir anahtar/lisans yetkilendirme ilkesi oluşturmak üzere Azure portal kullanabilirsiniz.

Bu makalede, portalı kullanılarak bir içerik koruma ilkesinin nasıl yapılandırılacağı gösterilir. Makalede ayrıca varlıklarınıza dinamik şifrelemenin nasıl uygulanacağı gösterilmektedir.

## <a name="start-to-configure-content-protection"></a>İçerik korumayı yapılandırmaya başlayın
Portalı kullanarak genel içerik korumasını Media Services hesabınızı kullanarak yapılandırmak için aşağıdaki adımları uygulayın:

1. [Portalda](https://portal.azure.com/)Media Services hesabınızı seçin.

1. **Ayarlar**  >  **içerik koruması**' nı seçin.

    ![İçerik koruma](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>Anahtar/lisans yetkilendirme ilkesi
Media Services, anahtar veya lisans istekleri yapan kullanıcıların kimliğini doğrulamanın birden çok yolunu destekler. İçerik anahtarı yetkilendirme ilkesini yapılandırmanız gerekir. Anahtar/lisans kendisine teslim edilmeden önce istemciniz Bu ilkeyi karşılamalıdır. İçerik anahtarı yetkilendirme ilkesinin açık veya belirteç kısıtlaması şeklinde bir veya daha fazla yetkilendirme kısıtlaması olabilir.

Tüm şifrelemeleri için bir anahtar/lisans yetkilendirme ilkesi oluşturmak üzere portalını kullanabilirsiniz.

### <a name="open-authorization"></a>Yetkilendirmeyi aç
Açık kısıtlama, sistemin anahtarı bir anahtar isteği yapan herkese teslim eder anlamına gelir. Bu kısıtlama, test amacıyla yararlı olabilir. 

### <a name="token-authorization"></a>Belirteç yetkilendirme
Belirteç kısıtlamalı ilkenin beraberinde bir güvenlik belirteci hizmeti (STS) tarafından verilmiş bir belirteç bulunmalıdır. Media Services, basit web belirteci (SWT) ve JSON Web Token (JWT) biçimlerindeki belirteçleri destekler. Media Services STS sağlamıyor. Belirteçleri vermek için özel bir STS oluşturabilir veya Azure Access Control Service kullanabilirsiniz. STS, belirtilen anahtarla imzalanmış bir belirteç oluşturacak ve belirteç kısıtlama yapılandırmasında belirttiğiniz talepler verecek şekilde yapılandırılmalıdır. Belirteç geçerliyse ve belirteçteki talepler anahtar (veya lisans) için yapılandırılananlarla eşleşiyorsa, Media Services anahtar teslim hizmeti istemciye istenen anahtarı (veya lisansı) döndürür.

Belirteç kısıtlı ilkesini yapılandırırken, birincil doğrulama anahtarını, verenin ve hedef kitle parametrelerini belirtmeniz gerekir. Birincil doğrulama anahtarı, belirtecin imzalandığı anahtarı içerir. Veren, belirteci veren güvenli belirteç hizmetidir. Hedef kitle (bazen kapsam olarak adlandırılır) belirtecin amacını veya belirtecin erişim yetkisi aldığı kaynağı açıklar. Media Services anahtar teslim hizmeti, belirteçteki bu değerlerin şablondaki değerlerle eşleştiğini doğrular.

![Anahtar/lisans yetkilendirme ilkesi](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>PlayReady lisans şablonu
PlayReady lisans şablonu, PlayReady lisansınıza etkinleştirilen işlevselliği ayarlar. PlayReady lisans şablonu hakkında daha fazla bilgi için bkz. [PlayReady lisans şablonuna genel bakış Media Services](media-services-playready-license-template-overview.md).

### <a name="nonpersistent"></a>Kalıcı olmayan
Bir lisansı kalıcı olmayan olarak yapılandırırsanız, bu lisans yalnızca Player lisansı kullandığında bellekte tutulur.  

![Kalıcı olmayan içerik koruması](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Kalıcı
Bir lisansı kalıcı olarak yapılandırırsanız, istemci üzerindeki kalıcı depolamaya kaydedilir.

![Kalıcı içerik koruması](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Widevine lisans şablonu
Widevine lisans şablonu, Widevine lisanslarınız üzerinde etkinleştirilen işlevselliği ayarlar.

### <a name="basic"></a>Temel
**Temel**' i seçtiğinizde, şablon tüm varsayılan değerlerle oluşturulur.

### <a name="advanced"></a>Gelişmiş
Widevine Rights şablonu hakkında daha fazla bilgi için bkz. [Widevine lisans şablonuna genel bakış](media-services-widevine-license-template-overview.md).

![Gelişmiş içerik koruması](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>FairPlay yapılandırması
FairPlay şifrelemeyi etkinleştirmek için **Fairplay Configuration**' ı seçin. Ardından uygulama **sertifikası** ' nı seçin ve **uygulama gizli anahtarı**' nı girin. FairPlay yapılandırması ve gereksinimleri hakkında daha fazla bilgi için bkz. [Apple Fairplay veya Microsoft PlayReady Ile HLS Içeriğinizi koruma](media-services-protect-hls-with-FairPlay.md).

![FairPlay yapılandırması](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Varlığınıza dinamik şifrelemeyi uygulama
Dinamik şifrelemenin avantajlarından yararlanmak için, kaynak dosyanızı Uyarlamalı bit hızı olan bir MP4 dosyası kümesine kodlayın.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Şifrelemek istediğiniz bir varlık seçin
Tüm varlıklarınızı görmek için, **Ayarlar**  >  **varlıklar**' ı seçin.

![Varlıklar seçeneği](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>AES veya DRM ile şifreleme
Bir varlık için **şifrelemeyi** seçtiğinizde, iki seçenek görürsünüz: **AES** veya **DRM**. 

#### <a name="aes"></a>AES
AES şifresiz anahtar şifrelemesi tüm akış protokollerinde etkinleştirilmiştir: Kesintisiz Akış, HLS ve MPEG-DASH.

![Şifreleme yapılandırması](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
1. **DRM**' yi seçtikten sonra farklı içerik koruma ilkeleri (Bu nokta tarafından yapılandırılması gerekir) ve akış protokolleri kümesi görürsünüz:

    a. **MPEG-Dash Ile PlayReady ve Widevine** , PlayReady ve Widevine drms ile MPEG-Dash akışını dinamik olarak şifreler.

    b. , **HLS Ile PlayReady ve Widevine** , PlayReady ve Widevine drms ile MPEG-Dash akışınızı dinamik olarak şifreler. Bu seçenek ayrıca, HLS akışlarınızı FairPlay ile şifreler.

    c. **Yalnızca kesintisiz akış, HLS ve MPEG-Dash Ile PlayReady** , PlayReady DRM ile kesintisiz akış, HLS ve MPEG-Dash akışlarını dinamik olarak şifreler.

    d. **Yalnızca MPEG-Dash Ile Widevine,** WIDEVINE DRM ile MPEG-Dash ' i dinamik olarak şifreler.
    
    e. **Fairplay** , HLS Stream 'lerinizi dinamik olarak Fairplay ile şifreler.

1. FairPlay şifrelemeyi etkinleştirmek için **Content Protection genel ayarlar** dikey penceresinde **Fairplay yapılandırma**' yı seçin. Ardından uygulama **sertifikası**' nı seçin ve **uygulama gizli anahtarı**' nı girin.

    ![Şifreleme türü](./media/media-services-portal-content-protection/media-services-content-protection009.png)

1. Şifreleme seçimini yaptıktan sonra **Uygula**' yı seçin.

>[!NOTE] 
>Safari 'de AES şifreli bir HLS oynamasını planlıyorsanız, [Safari 'de ŞIFRELI HLS](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)bloguna bakın.

## <a name="additional-notes"></a>Ek notlar

* Widevine, Google Inc. tarafından sunulan bir hizmettir ve Google, Inc 'nin hizmet koşullarına ve gizlilik Ilkesine tabidir.

## <a name="next-steps"></a>Sonraki adımlar
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
