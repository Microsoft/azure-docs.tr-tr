---
title: Bing Web Araması API'si analiz ekleyin
titleSuffix: Azure Cognitive Services
description: Bing Istatistikleri Bing Resim Arama API'si analiz sağlar. Analiz, çağrı hacmi, en çok sorgu dizeleri, coğrafi dağıtım ve daha fazlasını içerir.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 02fea02cca5950ef8467377a866e9a765af9e2e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96349563"
---
# <a name="add-analytics-to-the-bing-search-apis"></a>Bing Arama API'leri analiz ekleyin

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Bing Istatistikleri Bing Arama API'leri için analiz sağlar. Bu analizler çağrı hacmi, en çok sorgu dizeleri, coğrafi dağıtım ve daha fazlasını içerir. [Azure Portal](https://ms.portal.azure.com) Bing istatistiklerini Azure kaynağına giderek ve **Bing istatistiklerini etkinleştir**' e tıklayarak etkinleştirebilirsiniz.

> [!IMPORTANT]
> * Bing Istatistikleri ücretsiz `F0` fiyatlandırma katmanındaki kaynaklarla kullanılamaz.
> * Üçüncü taraflara dağıtmak üzere uygulamalar oluşturmak için Bing Istatistik panosu aracılığıyla mevcut herhangi bir veriyi kullanamazsınız.
> * Bing Istatistiklerini etkinleştirmek, aboneliğinizin hızını biraz artırır. Ayrıntılar için bkz. [fiyatlandırma](https://aka.ms/bingstatisticspricing) .


Aşağıdaki görüntüde Bing Arama her bir API uç noktası için kullanılabilir analiz gösterilmektedir.

![Uç nokta desteği matrisine göre dağıtım](./media/bing-statistics/bing-statistics-matrix.png)

## <a name="access-your-analytics"></a>Analizize erişin

Bing verileri her 24 saatte bir analiz eder ve [analiz panosundan](https://bingapistatistics.com)erişebileceğiniz en fazla 13 aya kadar geçmiş tutar. Bing STATISTICS 'e kaydolmak için kullandığınız Microsoft hesabı (MSA) ile oturum açtığınızdan emin olun.

> [!NOTE]  
> * Ölçümlerin panoda yüzeyi 24 saate kadar sürebilir. Panoda verilerin son güncelleştirilme tarihi ve saati gösterilir.  
> * Ölçümler, Bing Istatistik eklentisini etkinleştirdiğiniz zamandan itibaren kullanılabilir.

## <a name="filter-the-data"></a>Verileri filtreleme

Varsayılan olarak, grafikler ve grafikler, erişiminiz olan tüm ölçümleri ve verileri görüntüler. İlgilendiğiniz kaynakları, pazarlar, uç noktaları ve raporlama dönemini seçerek grafiklerde ve grafiklerde gösterilen verilere filtre uygulayabilirsiniz. Aşağıdaki filtreleri değiştirebilirsiniz:

- **Kaynak kimliği**: Azure aboneliğinizi tanımlayan BENZERSIZ kaynak kimliği. Birden fazla Bing Arama API katmanına abone olduğunuzda liste birden fazla kimlik içerir. Varsayılan olarak tüm kaynaklar seçilidir.  
  
- **Pazarlar**: sonuçların geldiği pazarlar. Örneğin, en-US (Ingilizce, Birleşik Devletler). Varsayılan olarak tüm pazarlar seçilidir. `en-WW`Bu Pazar, çağrı bir pazar belirlememe ve Bing kullanıcının pazarını belirleyemezse Bing 'in kullandığı pazardır.  
  
- **Uç noktalar**: Bing arama API uç noktaları. Liste, ücretli aboneliğinizin bulunduğu tüm uç noktaları içerir. Varsayılan olarak, tüm uç noktalar seçilidir.  

- **Zaman dilimi**: raporlama dönemi. Şunları belirtebilirsiniz:
  - **Tümü**: en fazla 13 ay veri içerir  
  - Son **24 saat**: son 24 saat içindeki analizi içerir  
  - **Son hafta**: önceki 7 güne ait analizi içerir  
  - **Geçen ay**: önceki 30 günden analizler içerir  
  - **Özel bir tarih aralığı**: varsa, belirtilen tarih aralığından analiz içerir  

## <a name="charts-and-graphs"></a>Grafikler ve grafikler

Panoda seçili uç nokta için kullanılabilir ölçümlerin grafikleri ve grafikleri gösterilmektedir. Tüm ölçümler tüm uç noktalar için kullanılamaz. Her uç noktanın grafikleri ve grafikleri statiktir (görüntülenecek grafikleri ve grafikleri seçemezsiniz). Pano yalnızca verilerin bulunduğu grafikleri ve grafikleri gösterir.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

Aşağıdakiler olası ölçümler ve uç nokta kısıtlamalardır.

- **Arama birimi**: raporlama döneminde yapılan çağrıların sayısını gösterir. Raporlama dönemi bir gün için ise grafik, saat başına yapılan çağrı sayısını gösterir. Aksi halde grafik, raporlama döneminin günlük yaptığı çağrı sayısını gösterir.  
  
  > [!NOTE]
  > Çağrı hacmi, genellikle yalnızca başarılı çağrıları içeren faturalandırma raporlarından farklı olabilir.

- **Popüler sorgular**: raporlama dönemi boyunca her bir sorgunun en iyi sorgularını ve oluşum sayısını gösterir. Gösterilen sorguların sayısını yapılandırabilirsiniz. Örneğin, en üstteki 25, 50 veya 75 sorgularını gösterebilirsiniz. Üst sorgular aşağıdaki uç noktalar için kullanılamaz:  

  - /images/trbitiriliyor
  - /images/details
  - /images/VisualSearch
  - /Videos/trbitiriliyor
  - /Videos/details
  - /News
  - /News/trendingkonular
  - /öneriler  
  
  > [!NOTE]  
  > E-posta, telefon numarası, SSN vb. gibi gizli bilgileri kaldırmak için bazı sorgu terimleri bastırılabilir.

- **Coğrafi dağıtım**: arama sonuçlarının gerçekleştiği pazarlar. Örneğin, `en-us` (İngilizce, Birleşik Devletler). Bing, `mkt` belirtilmişse pazaryı belirlemede sorgu parametresini kullanır. Aksi halde, Bing, pazarı belirlemede arayanın IP adresi gibi sinyalleri kullanır.

- **Yanıt kodu dağıtımı**: raporlama dönemi boyunca tüm çağrıların http durum kodları.

- **Çağrı kaynağı dağıtımı**: kullanıcılar tarafından kullanılan tarayıcı türleri. Örneğin, Microsoft Edge, Chrome, Safari ve FireFox. Tarayıcı dışından yapılan çağrılar (botlar, Postman veya bir konsol uygulamasından kıvrımlı kullanılması) Kitaplıklar altında gruplandırılır. Kaynak, isteğin User-Agent üst bilgi değeri kullanılarak belirlenir. İstek User-Agent üst bilgisini içermiyorsa, Bing kaynağı diğer sinyallerden türemeye çalışır.  

- **Güvenli arama dağıtımı**: Güvenli Arama değerlerinin dağılımı. Örneğin kapalı, orta veya katı. `safeSearch`Belirtilmişse, sorgu parametresi değerini içerir. Aksi halde, Bing değeri orta olarak belirler.  

- **Istenen dağıtım yanıtları**: sorgu parametresinde ISTEDIĞINIZ Web araması API yanıtı `responseFilter` .  

- **Döndürülen yanıt dağıtımı**: YANıTTA Web araması API 'nin döndürdüğü yanıtlar.

- **Yanıt sunucusu dağıtımı**: API isteklerinizi sunan uygulama sunucusu. Olası değerler Bing.com (masaüstü ve dizüstü cihazlarından sunulan trafik için) ve Bing.com-Mobile (mobil cihazlardan sunulan trafik için). Sunucu, isteğin User-Agent üst bilgi değeri kullanılarak belirlenir. İstek User-Agent üst bilgisini içermiyorsa, Bing sunucuyu diğer sinyallerden türemeye çalışır.

## <a name="next-steps"></a>Sonraki adımlar

* [Bing Arama API'leri nedir?](bing-api-comparison.md)
* [Bing Arama API’si kullanım ve görüntüleme gereksinimleri](use-display-requirements.md)