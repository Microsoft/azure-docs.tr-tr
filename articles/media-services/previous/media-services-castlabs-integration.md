---
title: Azure Media Services için Wıdevine lisansları teslim etmek üzere Rolabs kullanma | Microsoft Docs
description: Bu makalede, AMS tarafından hem PlayReady hem de Widevine DRMs ile dinamik olarak şifrelenen bir akış teslim etmek için Azure Media Services (AMS) ' nin nasıl kullanılacağı açıklanır.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 2a9a408a-a995-49e1-8d8f-ac5b51e17d40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: willzhan
ms.openlocfilehash: 576ac636f166e2daebbb9919d6666fea913a17be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017061"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Azure Media Services’ta Widevine lisansları vermek için castLabs kullanma

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Genel Bakış

Bu makalede, AMS tarafından hem PlayReady hem de Widevine DRMs ile dinamik olarak şifrelenen bir akış teslim etmek için Azure Media Services (AMS) ' nin nasıl kullanılacağı açıklanır. PlayReady lisansı, Media Services PlayReady lisans sunucusundan gelir ve Widevine lisansı, **Rolabs** lisans sunucusu tarafından dağıtılır.

CENC (PlayReady ve/veya Widevine) tarafından korunan kayıttan akış içeriğini oynatmak için  [Azure Media Player](https://aka.ms/azuremediaplayer)kullanabilirsiniz. Ayrıntılar için bkz. [amp belgesi](https://amp.azure.net/libs/amp/latest/docs/) .

Aşağıdaki diyagramda, üst düzey bir Azure Media Services ve bir Rolabs tümleştirme mimarisi gösterilmektedir.

![tümleştirme](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Tipik sistem kurulumu

* Medya içeriği AMS 'de depolanır.
* İçerik anahtarlarının anahtar kimlikleri hem l Labs hem de AMS 'de depolanır.
* hem Rolabs hem de AMS 'de yerleşik belirteç kimlik doğrulaması bulunur. Aşağıdaki bölümlerde kimlik doğrulama belirteçleri ele alınmıştır. 
* Bir istemci videoyu akışını istediğinde, içerik **Common Encryption** (CENC) ile dinamik olarak şifrelenir ve AMS tarafından KESINTISIZ AKıŞ ve Dash ile dinamik olarak paketlenir. Ayrıca, HLS akış protokolü için PlayReady M2TS öğesel akış şifrelemesi sunuyoruz.
* PlayReady lisansı AMS lisans sunucusundan alınır ve Widevine lisansı, Rolabs lisans sunucusundan alınır. 
* Media Player, istemci platformu özelliğine göre hangi lisansın getirileceği otomatik olarak karar verir. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Lisans almak için kimlik doğrulama belirteci oluşturma

Hem Rolabs hem de AMS, bir lisansı yetkilendirmek için kullanılan JWT (JSON Web Token) belirteç biçimini destekler. 

### <a name="jwt-token-in-ams"></a>AMS 'de JWT belirteci

Aşağıdaki tabloda AMS 'de JWT belirteci açıklanmaktadır. 

| Veren | Seçilen güvenli belirteç hizmetinden veren dize (STS) |
| --- | --- |
| Hedef kitle |Kullanılan STS 'den alınan hedef kitle dizesi |
| Talepler |Bir talepler kümesi |
| NotBefore |Belirtecin geçerliliğini Başlat |
| Süre Sonu |Belirtecin bitiş geçerliliği |
| SigningCredentials |PlayReady lisans sunucusu, ROI Labs lisans sunucusu ve STS arasında paylaşılan anahtar, simetrik ya da asimetrik anahtar olabilir. |

### <a name="jwt-token-in-castlabs"></a>Rolabs 'de JWT belirteci

Aşağıdaki tabloda, Rolabs 'de JWT belirteci açıklanmaktadır. 

| Ad | Açıklama |
| --- | --- |
| optData |Sizin hakkında bilgi içeren bir JSON dizesi. |
| CRT |Varlık hakkındaki bilgileri, lisans bilgilerini ve kayıttan yürütme haklarını içeren bir JSON dizesi. |
| IAT |Dönem içinde geçerli tarih saat. |
| JTI dili |Bu belirteçle ilgili benzersiz bir tanımlayıcı (her belirteç yalnızca bir kez, bir tek |

## <a name="sample-solution-setup"></a>Örnek çözüm kurulumu

[Örnek çözüm](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) iki projeden oluşur:

* Hem PlayReady hem de Widevine için, zaten alınmış bir varlık üzerinde DRM kısıtlamalarını ayarlamak için kullanılabilen bir konsol uygulaması.
* Bir STS 'nin çok BASITLEŞTIRILMIŞ bir sürümü olarak görülemeyen belirteçleri kapsayan bir Web uygulaması.

Konsol uygulamasını kullanmak için:

1. AMS kimlik bilgilerini, ROI Labs kimlik bilgilerini, STS yapılandırmasını ve paylaşılan anahtarı ayarlamak için app.config değiştirin.
2. Bir varlığı AMS 'ye yükleyin.
3. Karşıya yüklenen varlığın UUID 'sini alın ve program. cs dosyasında 32 satırını değiştirin:
   
      var Objivarlık = _context. Varlıklar. burada (x => x.Id = = "NB: CID: UUID: dac53a5d-1500-80bd-b864-f1e4b62594cf"). FirstOrDefault ();
4. AssetId kullanarak, sabit kıymeti sisteminde varlığı (program. cs dosyasında satır 44) adlandırmak için kullanın.
   
   AssetId **için şunu** ayarlamanız gerekir; benzersiz bir alfasayısal dize olması gerekir.
5. Programı çalıştırın.

Web uygulamasını kullanmak için (STS):

1. web.config, ROI Laboratuvarı ticari KIMLIĞI, STS yapılandırması ve paylaşılan anahtarı ayarlamak için değiştirin.
2. Azure Web siteleri 'ne dağıtın.
3. Web sitesine gidin.

## <a name="playing-back-a-video"></a>Videoyu kayıttan yürütme

Ortak şifreleme (PlayReady ve/veya Widevine) ile şifrelenmiş bir videoyu kayıttan yürütmek için [Azure Media Player](https://aka.ms/azuremediaplayer)kullanabilirsiniz. Konsol uygulamasını çalıştırırken, Içerik anahtar KIMLIĞI ve bildirim URL 'SI yankılanır.

1. Yeni bir sekme açın ve STS 'nizi başlatın: http://[yourStsName]. azurewebsites. net/api/Token/assetid/[Yourserlabsassetıd]/contentkeyıd/[thecontentkeyıd].
2. [Azure Media Player](https://aka.ms/azuremediaplayer)gidin.
3. Akış URL 'sini yapıştırın.
4. **Gelişmiş Seçenekler** onay kutusuna tıklayın.
5. **Koruma** açılan menüsünde PlayReady ve/veya Widevine öğesini seçin.
6. STS 'nizden aldığınız belirteci belirteç metin kutusuna yapıştırın. 
   
   Rosılab lisans sunucusu, belirtecin önünde "taşıyıcı =" öneki gerektirmez. Bu nedenle, belirteci göndermeden önce lütfen bunu kaldırın.
7. Oynatıcıyı güncelleştirin.
8. Videonun yürütülmesi gerekir.

## <a name="additional-notes"></a>Ek notlar

* Widevine, Google Inc. tarafından sunulan bir hizmettir ve Google, Inc 'nin hizmet koşullarına ve gizlilik Ilkesine tabidir.

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

