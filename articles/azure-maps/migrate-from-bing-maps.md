---
title: "Öğretici: Bing Haritalar 'dan Azure Maps 'e geçiş | Microsoft Azure haritaları"
description: Bing Haritalar 'dan Microsoft Azure haritalarını geçirmeye yönelik bir öğretici. Yönergeler, Azure Maps API 'Lerine ve SDK 'larına nasıl geçlenebileceğine kılavuzluk eder.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 9bd0516889733a666bf15668cffd124dcc468f3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100388966"
---
# <a name="tutorial-migrate-from-bing-maps-to-azure-maps"></a>Öğretici: Bing Haritalar 'dan Azure Maps 'e geçiş

Bu kılavuzda, Web, mobil ve sunucu tabanlı uygulamaların Bing Haritalar 'dan Azure haritalar platformuna nasıl geçirileceğiyle ilgili Öngörüler sunulmaktadır. Bu kılavuzda, Azure Maps 'e geçiş için karşılaştırılma kodu örnekleri, geçiş önerileri ve en iyi uygulamalar yer almaktadır. 

Bu öğreticide şunları öğreneceksiniz:

> [!div class="checklist"]
> * Azure haritalar 'da bulunan eşdeğer Bing Haritalar özellikleri için üst düzey karşılaştırma.
> * Hangi lisans farklılıkları göz önünde bulundurulmalıdır.
> * Geçişinizi planlayın.
> * Teknik kaynakların ve desteğin nerede bulunacağı.

## <a name="prerequisites"></a>Önkoşullar

1. [Azure portalında](https://portal.azure.com) oturum açın. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
2. [Azure haritalar hesabı oluşturma](quick-demo-map-app.md#create-an-azure-maps-account)
3. Birincil anahtar veya abonelik anahtarı olarak da bilinen [birincil bir abonelik anahtarı alın](quick-demo-map-app.md#get-the-primary-key-for-your-account). Azure haritalar 'da kimlik doğrulaması hakkında daha fazla bilgi için bkz. [Azure haritalar 'da kimlik doğrulamasını yönetme](how-to-manage-authentication.md).

## <a name="azure-maps-platform-overview"></a>Azure haritalar platformuna genel bakış

Azure haritalar, geliştiricilerin Web ve mobil uygulamalar için coğrafi bağlam sağlamak üzere sunulan en güncel eşleme verileriyle paketlenmiş, tüm sektörlerin güçlü Jeo-uzamsal yeteneklerini sunmaktadır. Azure haritalar; haritalar, arama, yönlendirme, trafik, saat dilimleri, bölge sınırlaması, harita verileri, hava durumu verileri ve birden çok platformda daha kolay, esnek ve taşınabilir hale getirmek için hem Web hem de Android SDK 'Lar tarafından birlikte bir Azure tek API uyumlu REST API kümesidir. [Azure haritalar da Power BI de mevcuttur](power-bi-visual-getting-started.md).

## <a name="high-level-platform-comparison"></a>Üst düzey platform karşılaştırması

Aşağıdaki tablo, Bing Haritalar özelliklerinin üst düzey bir listesini ve Azure Maps 'taki bu özellikler için göreli desteği sağlar. Bu liste erişilebilirlik, bölge sınırlaması API 'Leri, Trafik Hizmetleri, uzamsal işlemler, doğrudan harita kutucuğu erişimi ve Batch hizmetleri gibi ek Azure Maps özellikleri içermez.

| Bing Haritalar özelliği                     | Azure haritalar desteği |
|---------------------------------------|:------------------:|
| Web SDK’sı                               | ✓                  |
| Android SDK                           | ✓                  |
| iOS SDK                               | Planlandı            |
| UWP SDK 'Sı                               | Yok                 |
| WPF SDK 'Sı                               | Yok                 |
| REST hizmeti API 'Leri                     | ✓                  |
| Otomatik öneri                           | ✓                  |
| Yönergeler (kamyon dahil)          | ✓                  |
| Uzaklık matrisi                       | ✓                  |
| Yükseltmeleri                            | ✓ (Önizleme)        |
| Imagery – statik eşleme                  | ✓                  |
| Imagery meta verileri                      | ✓                  |
| İzokron                            | ✓                  |
| Yerel Öngörüler                        | ✓                  |
| Yerel arama                          | ✓                  |
| Konum tanıma                  | ✓                  |
| Konumlar (ileri/ters coğrafi kodlama) | ✓                  |
| İyileştirilmiş yolculuk yolları            | Planlandı            |
| Yollara yapış                         | ✓                  |
| Uzamsal veri Hizmetleri (SDS)           | Kısmi            |
| Saat Dilimi                             | ✓                  |
| Trafik olayları                     | ✓                  |
| Yapılandırma temelli haritalar             | Yok                |

Bing Haritalar temel anahtar tabanlı kimlik doğrulaması sağlar. Azure haritalar hem temel anahtar tabanlı kimlik doğrulaması hem de yüksek düzeyde güvenli Azure Active Directory kimlik doğrulaması sağlar.

## <a name="licensing-considerations"></a>Lisanslama konuları

Bing Haritalar 'dan Azure Maps 'a geçiş yaparken, lisanslamayla ilgili olarak aşağıdaki bilgiler göz önünde bulundurulmalıdır.

* Azure Maps, yüklenen harita kutucuklarının sayısına göre etkileşimli haritalar kullanımı için ücretlendirirken, Bing Haritalar harita denetimi 'nin (oturumlar) yüklemesi için ücretlendirir. Azure haritalar, geliştiricilerin maliyetlerini azaltmak için harita kutucuklarını otomatik olarak önbelleğe alır. Yüklenen her 15 harita kutucuğu için bir Azure haritalar işlemi oluşturulur. Etkileşimli Azure Haritalar SDK 'Ları 512 piksellik kutucukları kullanır ve ortalama üzerinde sayfa görünümü başına bir veya daha az işlem oluşturur.

* Azure haritalar, platformdaki verilerin Azure 'da depolanmasını sağlar. Ayrıca [kullanım koşullarına](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)göre altı aya kadar bir yerde önbelleğe alınabilir.

Azure haritalar için lisanslama ile ilgili bazı kaynaklar aşağıda verilmiştir:

-   [Azure haritalar fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/azure-maps/)
-   [Azure fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
-   [Azure Haritalar kullanım koşulları](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (Microsoft Online Services koşulları 'nda bulunur)
-   [Azure haritalar 'da doğru fiyatlandırma katmanını seçin](./choose-pricing-tier.md)

## <a name="suggested-migration-plan"></a>Önerilen geçiş planı

Aşağıda, üst düzey bir geçiş planına örnek verilmiştir.

1.  Uygulamanızın kullandığı Bing Haritalar SDK 'Ları ve hizmetleri hakkında daha fazla işlem yapın ve Azure Maps 'e geçiş yapmanız için alternatif SDK 'lar ve hizmetler sağladığını doğrulayın.
2.  Bir Azure aboneliği oluşturun (henüz bir tane yoksa) <https://azure.com> .
3.  Azure haritalar hesabı ([Belgeler](./how-to-manage-account-keys.md)) ve kimlik doğrulama anahtarı veya Azure Active Directory ([Belgeler](./how-to-manage-authentication.md)) oluşturun.
4.  Uygulama kodunuzu geçirin.
5.  Geçirilen uygulamanızı test edin.
6.  Geçirilen uygulamanızı üretime dağıtın.

## <a name="create-an-azure-maps-account"></a>Azure Haritalar hesabı oluşturma

Bir Azure Maps hesabı oluşturmak ve Azure haritalar platformuna erişmek için şu adımları izleyin:

1. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
2. [Azure portalında](https://portal.azure.com/) oturum açın.
3. [Azure haritalar hesabı](./how-to-manage-account-keys.md)oluşturun.
4. Gelişmiş güvenlik için [Azure Maps abonelik anahtarınızı](./how-to-manage-authentication.md#view-authentication-details) veya kurulum Azure Active Directory kimlik doğrulamasını alın.

## <a name="azure-maps-technical-resources"></a>Azure haritalar teknik kaynakları

Azure haritalar için yararlı teknik kaynakların listesi aşağıda verilmiştir.

* Genel Bakış: <https://azure.com/maps>
* Belgelerle <https://aka.ms/AzureMapsDocs>
* Web SDK kodu örnekleri: <https://aka.ms/AzureMapsSamples>
* Geliştirici forumları: <https://aka.ms/AzureMapsForums>
* Larınız <https://aka.ms/AzureMapsVideos>
* Lenemeyen <https://aka.ms/AzureMapsBlog>
* Azure haritalar geri bildirimi (UserVoice): <https://aka.ms/AzureMapsFeedback>

## <a name="migration-support"></a>Geçiş desteği

Geliştiriciler, [Forum](/answers/topics/azure-maps.html) aracılığıyla veya birçok Azure destek seçeneğinden biri aracılığıyla geçiş desteği arayabilir: <https://azure.microsoft.com/support/options/>

## <a name="new-terminology"></a>Yeni terminoloji

Aşağıdaki listede, yaygın Bing Haritalar terimleri ve bunlara karşılık gelen Azure haritalar terimleri yer almaktadır.

| Bing Haritalar terimi                    | Azure haritalar terimi                                                |
|-----------------------------------|----------------------------------------------------------------|
| Havadan                            | Uydu veya havadan                                            |
| Yönergeler                        | Yönlendirme olarak da adlandırılabilir                             |
| Varlıklar                          | Geometriler veya Özellikler                                         |
| `EntityCollection`                | Veri kaynağı veya katmanı                                           |
| `Geopoint`                        | Konum                                                       |
| `GeoXML`                          | Uzamsal GÇ modülündeki XML dosyaları                             |
| Zemin kaplama                    | Görüntü katmanı                                                    |
| Karma (eşleme türüne başvuru olarak) | Yollar ile uydu                                           |
| Kapat                           | Açılan Pencere                                                          |
| Konum                          | Konum                                                       |
| `LocationRect`                    | Sınırlayıcı kutusu                                                   |
| Eşleme Türü                          | Harita stili                                                      |
| Gezinti çubuğu                    | Harita stil Seçicisi, yakınlaştırma denetimi, aralıklı denetim, pusula denetimi |
| Raptiye                           | Kabarcık katmanı, simge katmanı veya HTML Işaretçisi                      |

## <a name="clean-up-resources"></a>Kaynakları temizleme

Temizleme gerektiren kaynak yok.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelerle Bing Haritalar uygulamanızı nasıl geçirileceğiyle ilgili ayrıntıları öğrenin:

> [!div class="nextstepaction"]
> [Web uygulamasını geçirme](migrate-from-bing-maps-web-app.md)
