---
title: Microsoft Azure Maps Hava durumu Hizmetleri sık sorulan sorular (SSS)
description: Azure haritalar hava hizmeti verileri ve özellikleri hakkında sık sorulan soruların yanıtını bulun.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 440ff94f4d5f3514d97d3e76299b60e462cebff3
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257560"
---
# <a name="azure-maps-weather-services-frequently-asked-questions-faq"></a>Azure haritalar Hava durumu Hizmetleri sık sorulan sorular (SSS)

Bu makalede, Azure Maps [Hava durumu Hizmetleri](/rest/api/maps/weather) veri ve özellikleriyle ilgili yaygın soruların yanıtları yer almamaktadır. Aşağıdaki konular ele alınmıştır:

* Veri kaynakları ve veri modelleri
* Hava durumu Hizmetleri kapsamı ve kullanılabilirliği
* Veri güncelleştirme sıklığı
* Azure Haritalar SDK 'Ları ile geliştirme
* Microsoft Power BI tümleştirme dahil Hava durumu verilerini görselleştirilecek seçenekler

## <a name="data-sources-and-data-models"></a>Veri kaynakları ve veri modelleri

**Azure Kaynak Hava durumu verilerini nasıl eşler?**

Azure haritalar, temel hava durumu verilerini sağlayan Accuhava durumu da dahil olmak üzere birinci sınıf taşınabilirlik ve konum teknoloji iş ortaklarının işbirliği ile oluşturulmuştur. Azure haritalar 'ın işbirliğiyle birlikte çalışan duyurusunu okumak için bkz. [yağmur veya görünü: Azure Maps Hava durumu Hizmetleri, kuruluşunuza Öngörüler](https://azure.microsoft.com/blog/rain-or-shine-azure-maps-weather-services-will-bring-insights-to-your-enterprise/)sağlar.

Accuhava durumu, dünyanın dört bir yanındaki, büyük ölçüde ulusal kamu Hava durumu kurumları ve diğer mülkiyet düzenlemelerinden kaynaklanan ortaklıklar nedeniyle dünyanın her yerinden sunulan gerçek zamanlı hava durumu ve çevresel bilgiler içerir. Bu temel bilgilerin bir listesi aşağıda verilmiştir.

* Kamu kurumlarından genel kullanıma açık genel yüzeysel gözlemler
* Hükümetler ve özel şirketlerden özel yüzey izleme veri kümeleri
* 40 ülkede/bölgede yüksek çözünürlüklü radar veriler
* En iyi sınıf gerçek zamanlı genel şimşek verileri
* 60 ülkeye/bölgelere ve bölgelere yönelik devlet tarafından verilen hava durumu uyarıları
* Tüm dünyayı kapsayan coğrafi olarak sabit hava uydularından uydu verileri
* 150 ' den fazla sayısal tahmin modeli, dahili, özel modelleme, ABD küresel tahmin sistemi (GFS) gibi kamu modelleri ve özel şirketler tarafından sunulan benzersiz aşağı ölçeklendirilmiş modeller dahil olmak üzere.
* Hava kalitesinde gözlemler
* Ulaşım departmanlarından oluşan gözlemler

Diğer verilerle birlikte on binlerce yüzey gözlemleri, kullanıcılara sunulan geçerli koşulları oluşturmak ve etkilemek için eklenmiştir. Bu, yalnızca ücretsiz standart veri kümelerini ve ayrıca Hindistan, Brezilya, Kanada ve diğer özel girişler dahil olmak üzere çok sayıda ülkede/bölgede ulusal türüdür hizmetlerinden alınan benzersiz gözlemleri içerir. Bu benzersiz veri kümeleri, kullanıcılarımız için geçerli durum verilerinin uzamsal ve zamana bağlı çözümlemesini artırır. 

Bu veri kümeleri, her zaman en son verileri dahil etmek ve bu sayede sürekli doğruluğunu en üst düzeye çıkarmak için, bir dijital tahmin sisteminin doğruluğu açısından gerçek zamanlı olarak gözden geçirilir.

**Hangi modeller Hava durumu tahmin verileri oluşturur?**

Genel tahminleri formülleştirmek için çok sayıda hava durumu tahmin Kılavuzu sistemi kullanılır. 150 ' den fazla sayısal tahmin modeli, her gün hem dış hem de iç veri kümelerinde kullanılır. Bu, Avrupa Centre ECMWF ve ABD küresel tahmin sistemi (GFS) gibi kamu modellerini içerir. Ayrıca, Accuhava durumu, tahminleri daha fazla doğrulukla tahmin etmek için belirli konumlara ve stratejik bölgesel etki alanlarına yönelik tahminleri ölçekleyen özel yüksek çözünürlüklü modeller içerir. Accuhava durumu 'nın benzersiz karıştırma ve ağırlık algoritmaları, son birkaç Decades üzerinde geliştirilmiştir. Bu algoritmalar, yüksek oranda doğru tahminler sağlamak için çok sayıda tahmin girişi en iyi şekilde yararlanır.

## <a name="weather-services-coverage-and-availability"></a>Hava durumu Hizmetleri kapsamı ve kullanılabilirliği

**Farklı ülkeler/bölgeler için ne tür bir kapsama bekleyebilir?**

Hava durumu hizmeti kapsamı ülkeye/bölgeye göre farklılık gösterir. Tüm özellikler her ülkede/bölgede kullanılamaz. Daha fazla bilgi için bkz. [kapsam belgeleri](./weather-coverage.md).

## <a name="data-update-frequency"></a>Veri güncelleştirme sıklığı

**Geçerli koşullar verileri ne sıklıkta güncelleştiriliyor?**

Geçerli koşullar verileri yaklaşık bir saatte en az bir kez güncelleştirilir, ancak büyük sıcaklık değişiklikleri, gök koşulları değişiklikleri, yağış değişiklikler vb. gibi hızla değişen koşullara göre daha sık güncelleştirilebilirler. Dünyanın dört bir yanındaki izleme istasyonları, koşullar değiştikçe saat başına birçok kez rapor. Ancak, birkaç alan zamanlanan aralıklarda yalnızca bir kez, iki kez veya dört kez güncelleştirilecek.  

Azure haritalar, verilerin gerçekleştiği andaki gerçek zamanlı güncelleştirme sıklığını yakalamaya yardımcı olmak için geçerli koşullar verisini 10 dakikaya kadar önbelleğe alır. Önbelleğe alınan yanıtın süresinin ne zaman dolacağını ve eski verilerin görüntülenmesini önlemenize engel olmak için, Azure Maps API yanıtının HTTP üstbilgisindeki süre sonu üst bilgisi bilgisinden yararlanabilirsiniz.

**Günlük ve saatlik tahmin verileri ne sıklıkla güncelleştirilir?**

Günlük ve saatlik tahmin verileri, güncelleştirilmiş gözlemler alındığından günde birden çok kez güncelleştirilir.  Örneğin, tahmini bir yüksek/düşük sıcaklık geçmişse, tahmin verilerimiz bir sonraki güncelleştirme döngüsüne göre ayarlanacaktır. Bu durum farklı aralıklarla gerçekleşebilir, ancak genellikle bir saat içinde olur. Birçok ani hava durumu koşulları, bir tahmin verilerinin değişmesine neden olabilir. Örneğin, bir sıcak yaz öğleden sonra, yalıtılmış bir büyük/küçük, büyük bulut kapsamı ve yağmur ortaya çıkar. Yalıtılmış fırtınası, sıcaklığın 10 derece kadar verimli bir şekilde düşürülebilmesi. Bu yeni sıcaklık değeri, günün geri kalanı için saatlik ve günlük tahminleri etkiler ve bu nedenle veri kümelerimizde güncelleştirilir.

Azure haritalar tahmin API 'Leri, 30 dakikaya kadar önbelleğe alınır. Önbelleğe alınan yanıtın süresinin ne zaman dolacağını ve eski verilerin görüntülenmesini önlemenize engel olmak için, Azure Maps API yanıtının HTTP üstbilgisindeki süre sonu üst bilgisi bilgisinden yararlanabilirsiniz. Belirli bir ürün kullanım örneğine ve Kullanıcı arabirimine (Kullanıcı arabirimi) göre gereken şekilde güncelleştirme yapmanız önerilir.

## <a name="developing-with-azure-maps-sdks"></a>Azure Haritalar SDK 'Ları ile geliştirme

**Azure Haritalar Web SDK 'Sı, hava durumu Hizmetleri tümleştirmesini yerel olarak destekliyor mu?**

Azure Haritalar Web SDK 'Sı bir hizmet modülü sağlar. Hizmetler modülü, Web veya Node.js uygulamalarında Azure Maps REST hizmetlerini kullanmayı kolaylaştıran bir yardımcı kitaplıktır. JavaScript veya TypeScript kullanarak. Başlamak için [belgelerimizi](./how-to-use-services-module.md)inceleyin.

**Azure haritalar, hava durumu Hizmetleri tümleştirmesini yerel olarak destekliyor Android SDK?**

Azure Haritalar Android SDK 'Ları, x/y/zoom gösterimi, dörtlü anahtar gösterimi veya EPSG 3857 sınırlayıcı kutusu gösterimine sahip olabilen Mercator kutucuk katmanlarını destekler.

Java/Android için Web SDK modülüne benzer bir hizmet modülü oluşturmayı planlıyoruz. Android Hizmetleri modülü, bir Java veya Android uygulamasındaki tüm Azure Maps hizmetlerine erişmeyi kolaylaştırır.  

## <a name="data-visualizations"></a>Veri görselleştirmeleri  

**Azure haritalar Power BI Visual desteği Azure Maps Hava durumu kutucuklarını ister misiniz?**

Evet. Microsoft Power BI görseline radar ve kızılötesi uydu kutucukları geçirmeyi öğrenmek için bkz. [görsel Power BI kutucuk katmanı ekleme](./power-bi-visual-add-tile-layer.md). 

**Radar ve uydu kutucukları için kullanılan renkleri Nasıl yaparım? yorumlayın mi?**

Azure haritalar [Hava durumu kavramı makalesi](./weather-services-concepts.md#radar-and-satellite-imagery-color-scale) , radar ve uydu kutucukları için kullanılan renklerin yorumlanmasına yardımcı olan bir kılavuz içerir. Makalede renk örnekleri ve ONALTıLıK renk kodları ele alınmaktadır.
 
**Radar ve uydu kutucuk animasyonları oluşturabilir miyim?**

Evet. Gerçek zamanlı radar ve uydu kutucuklarına ek olarak, Azure Maps müşterileri harita yer paylaşımları ile veri görselleştirmelerini geliştirmek için geçmiş ve gelecekteki kutucuklar isteyebilir. Bu, doğrudan [Get Map kutucuğu v2 API 'sini](/rest/api/maps/renderv2/getmaptilepreview) çağırarak veya Azure Maps web SDK 'sı aracılığıyla kutucuk isteyerek yapılabilir. Radar kutucukları geçmişte 1,5 saate kadar ve gelecekte 2 saate kadar sunulmaktadır. Kutucuklar ve 5 dakikalık aralıklarla kullanılabilir. Kızılötesi kutucuklar geçmişte 3 saate kadar sağlanır ve 10 dakikalık aralıklarla kullanılabilir. Daha fazla bilgi için bkz. açık kaynaklı Hava durumu kutucuğu animasyon [kodu örneği](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Animated%20tile%20layer).  

**Farklı hava durumu koşullarına yönelik simgeler sunuyoruz misiniz?**

Evet. Simgeleri ve ilgili kodlarını [burada](./weather-services-concepts.md#weather-icons)bulabilirsiniz. [Geçerli koşulları Al API 'si](/rest/api/maps/weather/getcurrentconditions)gibi bazı hava durumu hizmeti (Önizleme) API 'lerinin, yanıtta *ICODE* 'u döndürtiğine dikkat edin. Daha fazla bilgi için bkz. geçerli dalgalı koşullar açık kaynak [kodu örneği](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Get%20current%20weather%20at%20a%20location).

## <a name="next-steps"></a>Sonraki adımlar

Bu SSS sorunuzu yanıtmazsa, aşağıdaki kanallarda (yürüyen sırada) bizimle iletişim kurmanız gerekir:

* Bu makalenin açıklamalar bölümü.
* [MSFT Q Azure Maps Için bir sayfa&](/answers/topics/azure-maps.html).
* Microsoft Desteği. Yeni bir destek isteği oluşturmak için, [Azure Portal](https://portal.azure.com/)yardım sekmesinde **Yardım +** destek düğmesini seçin ve ardından **Yeni destek isteği**' ni seçin.
* Azure, özellik isteklerini gönderecek şekilde [UserVoice haritaları](https://feedback.azure.com/forums/909172-azure-maps) .

Azure haritalar Hava durumu hizmetlerini kullanarak gerçek zamanlı ve tahmini hava durumu verileri isteme hakkında bilgi edinin:
> [!div class="nextstepaction"]
> [Gerçek zamanlı hava durumu verileri iste ](how-to-request-weather-data.md)

Azure haritalar Hava durumu Hizmetleri kavramları makalesi:
> [!div class="nextstepaction"]
> [Hava durumu hizmetleriyle ilgili kavramlar](weather-coverage.md)

Azure haritalar Hava durumu Hizmetleri API 'SI belgelerini inceleyin:

> [!div class="nextstepaction"]
> [Azure Haritalar Hava durumu hizmetleri](/rest/api/maps/weather)