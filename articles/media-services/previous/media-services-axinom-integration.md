---
title: Azure Media Services için Widevine lisansları teslim etmek üzere Axinom kullanma | Microsoft Docs
description: Bu makalede, AMS tarafından hem PlayReady hem de Widevine DRMs ile dinamik olarak şifrelenen bir akış teslim etmek için Azure Media Services (AMS) ' nin nasıl kullanılacağı açıklanır. PlayReady lisansı Media Services PlayReady lisans sunucusundan gelir ve Widevine lisansı, Axınom lisans sunucusu tarafından dağıtılır.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.assetid: 9c93fa4e-b4da-4774-ab6d-8b12b371631d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan
ms.reviewer: Mingfeiy;rajputam;Juliako
ms.openlocfilehash: 4d4823e8dcce0d1296ebe39a0b7a7c4bbc180317
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "69015423"
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Azure Media Services’ta Widevine lisansları vermek için Axinom kullanma 
> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Genel Bakış
Azure Media Services (AMS), Google Widevine dinamik koruma ekledi (Ayrıntılar için bkz. [Mingfeı blogumu](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) ). Ayrıca, Azure Media Player (AMP) Widevine desteğini de ekledi (Ayrıntılar için bkz. [amp belgesi](https://amp.azure.net/libs/amp/latest/docs/) ). Bu, hem MSE hem de EME ile donatılmış modern tarayıcılarda çok örnekli-DRM (PlayReady ve Widevine) ile CENC tarafından korunan akış DASH içeriği için önemli bir amadır.

Media Services .NET SDK sürümü Media Services 3.5.2 ile başlayarak Widevine lisans şablonunu yapılandırmanıza ve Widevine lisanslarını almanıza olanak sağlar. Widevine lisansları teslim etmenize yardımcı olması için aşağıdaki AMS iş ortaklarını da kullanabilirsiniz: [Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/), [ezdrm](https://ezdrm.com/), [ROI Labs](https://castlabs.com/company/partners/azure/).

Bu makalede, Axinom tarafından yönetilen Widevine lisans sunucusunu tümleştirme ve test etme işlemleri açıklanmaktadır. Özellikle şunları ele almaktadır:  

* Multi-DRM (PlayReady ve Widevine) ile dinamik Common Encryption, karşılık gelen lisans alımı URL 'Leri ile yapılandırma;
* Lisans sunucusu gereksinimlerini karşılamak için bir JWT belirteci oluşturma;
* JWT belirteci kimlik doğrulamasıyla lisans alımı işleyen Azure Media Player uygulama geliştirme;

Tüm sistem ve içerik anahtarı, anahtar KIMLIĞI, anahtar çekirdek, JTW belirteci ve talepleri, aşağıdaki diyagram tarafından en iyi şekilde açıklanabilir:

![DASH ve CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Content Protection
Dinamik koruma ve anahtar teslim ilkesini yapılandırmak için lütfen bkz. Mingfeı 'nin blogu: [Widevine paketlemeyi Azure Media Services ile yapılandırma](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Her ikisine de sahip olan DASH akışı için çoklu DRM ile dinamik CENC korumasını yapılandırabilirsiniz:

1. Bir belirteç yetkilendirme kısıtlamasına sahip olabilecek Microsoft Edge ve ıE11 için PlayReady koruması. Belirteç kısıtlı ilkesi, Azure Active Directory gibi bir güvenli belirteç hizmeti (STS) tarafından verilmiş bir belirteç ile birlikte kullanılmalıdır.
2. Chrome için Widevine koruması, başka bir STS tarafından verilen belirteç ile belirteç kimlik doğrulaması gerektirebilir. 

Neden Azure Active Directory Axinom 'ın Widevine lisans sunucusu için STS olarak kullanılamayacağını öğrenmek için [JWT belirteci oluşturma](media-services-axinom-integration.md#jwt-token-generation) bölümüne bakın.

### <a name="considerations"></a>Dikkat edilmesi gerekenler
1. Anahtar teslim hizmetini yapılandırmaya yönelik içerik anahtarını oluşturmak için, Axınom belirtilen anahtar kaynağı (8888000000000000000000000000000000000000) ve oluşturulan ya da seçtiğiniz anahtar KIMLIĞINI kullanmanız gerekir. Axinom lisans sunucusu, hem test hem de üretim için geçerli olan aynı anahtar Seed 'e dayalı içerik anahtarları içeren tüm lisansları yayınlar.
2. Test için Widevine lisans alma URL 'SI: [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense). Hem HTTP hem de ıSıTLARA izin verilir.

## <a name="azure-media-player-preparation"></a>Azure Media Player hazırlığı
AMP v 1.4.0, hem PlayReady hem de Widevine DRM ile dinamik olarak paketlenmiş AMS içeriğinin oynatılmasını destekler.
Widevine lisans sunucusu belirteç kimlik doğrulaması gerektirmiyorsa, Widevine tarafından korunan bir DASH içeriğini test etmek için yapmanız gereken ek bir şey yoktur. Örneğin, AMP ekibi, Widevine ile PlayReady ve Chrome ile Microsoft Edge ve ıE11 'de çalıştığını görebileceğiniz basit bir [örnek](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html)sağlar.
Axinom tarafından sunulan Widevine lisans sunucusu, JWT belirteci kimlik doğrulamasını gerektirir. JWT belirtecinin, "X-AxDRM-Message" HTTP üstbilgisiyle lisans isteğiyle gönderilmesi gerekir. Bu amaçla, kaynağı ayarlamadan önce AMP 'yı barındıran Web sayfasına aşağıdaki JavaScript eklemeniz gerekir:

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

AMP kodunun geri kalanı, AMP belgesinde olduğu gibi standart AMP API 'sidir [](https://amp.azure.net/libs/amp/latest/docs/).

Özel yetkilendirme üst bilgisini ayarlamaya yönelik yukarıdaki JavaScript, AMP 'daki uzun vadeli yaklaşımdan önce kısa vadeli bir yaklaşım olmaya devam etmektedir.

## <a name="jwt-token-generation"></a>JWT belirteci oluşturma
Test için axınom Widevine lisans sunucusu, JWT belirteci kimlik doğrulamasını gerektirir. Ayrıca, JWT belirtecindeki taleplerden biri basit veri türü yerine karmaşık nesne türüdür.

Ne yazık ki Azure AD yalnızca temel türler içeren JWT belirteçleri verebilir. Benzer şekilde, .NET Framework API (System. IdentityModel. Tokens. SecurityTokenHandler ve JwtPayload) yalnızca karmaşık nesne türünü talep olarak girmenize olanak sağlar. Ancak talepler yine de dize olarak serileştirilir. Bu nedenle, Widevine lisans isteği için JWT belirtecini oluşturmak üzere iki ' un hiçbirini kullanmıyoruz.

John Sheehan 'ın [JWT NuGet paketi](https://www.nuget.org/packages/JWT) , bu NuGet paketini kullanacağız.

Aşağıda, test için Axınom Widevine lisans sunucusu için gerekli talepler ile JWT belirteci oluşturma kodu verilmiştir:

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.IdentityModel.Tokens;
    using System.IdentityModel.Protocols.WSTrust;
    using System.Security.Claims;

    namespace OpenIdConnectWeb.Utils
    {
        public class JwtUtils
        {
            //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
            public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
            {
                byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.

                var payload = new Dictionary<string, object>()
                             {
                                 { "version", 1 },
                                 { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                                 { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
                             };

                string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);

                return token;
            }

            //convert hex string to byte[]
            public static byte[] ConvertHexStringToByteArray(string hexString)
            {
                if (hexString.Length % 2 != 0)
                {
                    throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
                }

                byte[] HexAsBytes = new byte[hexString.Length / 2];
                for (int index = 0; index < HexAsBytes.Length; index++)
                {
                    string byteValue = hexString.Substring(index * 2, 2);
                    HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
                }

                return HexAsBytes;
            }

        }  

    }  

Axınom Widevine lisans sunucusu

    <add key="ax:laurl" value="https://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

### <a name="considerations"></a>Dikkat edilmesi gerekenler
1. AMS PlayReady lisans teslimi hizmeti bir kimlik doğrulama belirtecinden önce "taşıyıcı =" gerektirse de, Axınom Widevine lisans sunucusu bunu kullanmaz.
2. Axınom iletişim anahtarı imzalama anahtarı olarak kullanılır. Anahtar onaltılık bir dizedir, ancak kodlama sırasında dize olmayan bir dizi bayt olarak değerlendirilmelidir. Bu, ConvertHexStringToByteArray yöntemi tarafından gerçekleştirilir.

## <a name="retrieving-key-id"></a>Anahtar KIMLIĞI alınıyor
Bir JWT belirteci oluşturma kodunda, anahtar KIMLIĞI ' nin gerekli olduğunu fark etmiş olabilirsiniz. JWT belirtecinin, AMP Player yüklenmeden önce hazır olması gerektiğinden, JWT belirteci oluşturmak için anahtar KIMLIĞI 'nin alınması gerekir.

Kuşkusuz, anahtar KIMLIĞINI tutan birçok yol vardır. Örneğin, biri bir veritabanında içerik meta verileriyle birlikte anahtar KIMLIĞINI de saklayabilir. Ya da DASH MPD (medya sunum açıklaması) dosyasından anahtar KIMLIĞI alabilirsiniz. Aşağıdaki kod ikincisi için verilmiştir.

    //get key_id from DASH MPD
    public static string GetKeyID(string dashUrl)
    {
        if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
        {
            dashUrl += "(format=mpd-time-csf)";
        }

        XPathDocument objXPathDocument = new XPathDocument(dashUrl);
        XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
        XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
        objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
        objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
        objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
        objXmlNamespaceManager.AddNamespace("xsi",  "https://www.w3.org/2001/XMLSchema-instance");
        objXmlNamespaceManager.PushScope();

        XPathNodeIterator objXPathNodeIterator;
        objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);

        string key_id = string.Empty;
        if (objXPathNodeIterator.MoveNext())
        {
            key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
        }

        return key_id;
    }

## <a name="summary"></a>Özet
Azure Media Services Content Protection ve Azure Media Player her ikisinde de Widevine desteğinin en son eklenmesiyle birlikte, AMS ve Widevine lisansında hem PlayReady lisans hizmeti hem de DASH + Multi-Native-DRM (PlayReady + Widevine) akışını uygulayabiliriz Aşağıdaki modern tarayıcılar için Axinom 'dan sunucu:

* Chrome
* Windows 10 ' da Microsoft Edge
* Windows 8.1 ve Windows 10 ' da IE 11
* Hem Firefox hem de Mac üzerinde Safari (iOS değil), Silverlight ve Azure Media Player ile aynı URL ile de desteklenir

Aşağıdaki parametreler, Axınom Widevine lisans sunucusu kullanan mini çözümde gereklidir. Anahtar KIMLIĞI dışında, parametrelerin geri kalanı Wıdevine sunucu kurulumuna göre Axınom tarafından sağlanır.

| Parametre | Nasıl kullanılır |
| --- | --- |
| İletişim anahtarı KIMLIĞI |JWT belirtecinde "com_key_id" talebinin değeri olarak eklenmelidir ( [Bu](media-services-axinom-integration.md#jwt-token-generation) bölüme bakın). |
| İletişim anahtarı |JWT belirtecinin imzalama anahtarı olarak kullanılması gerekir ( [Bu](media-services-axinom-integration.md#jwt-token-generation) bölüme bakın). |
| Anahtar tohum |Verilen içerik anahtarı KIMLIĞIYLE içerik anahtarı oluşturmak için kullanılmalıdır ( [Bu](media-services-axinom-integration.md#content-protection) bölüme bakın). |
| Widevine lisans alma URL 'SI |DASH akışı için varlık teslim ilkesini yapılandırırken kullanılması gerekir ( [Bu](media-services-axinom-integration.md#content-protection) bölüme bakın). |
| İçerik anahtarı KIMLIĞI |JWT belirtecinin yetkilendirme Iletisi talebinin değerinin bir parçası olarak eklenmelidir ( [Bu](media-services-axinom-integration.md#jwt-token-generation) bölüme bakın). |

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Bilgilendirme
Bu belgeyi oluşturmak için katkıda bulunan şu kişileri bildirmek istiyoruz: Kristjan Jõgi of Axinom, Mingfei yan ve Amit Esjput.

