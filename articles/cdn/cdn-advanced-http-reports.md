---
title: Gelişmiş HTTP raporları, Azure CDN ile kullanım istatistikleri analiz | Microsoft Docs
description: Microsoft Azure CDN'de Gelişmiş HTTP raporları oluşturmayı öğrenin. Bu raporlar CDN etkinliği hakkında ayrıntılı bilgi sağlar.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ef90adc1-580e-4955-8ff1-bde3f3cafc5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 0b0eec2425f8a1663eb7a09c83a6bad037d1d79c
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594103"
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Gelişmiş HTTP raporları, Azure CDN ile kullanım istatistikleri analiz edin
## <a name="overview"></a>Genel Bakış
Bu belgede, Microsoft Azure CDN'de Gelişmiş HTTP raporlama açıklanmaktadır. Bu raporlar CDN etkinliği hakkında ayrıntılı bilgi sağlar.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Gelişmiş HTTP raporları erişme
1. CDN profili dikey penceresinden tıklayın **Yönet** düğmesi.
   
    ![CDN profili dikey penceresi Yönet düğmesi](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    CDN yönetim portalına açılır.
2. Üzerine **Analytics** sekmesine ve ardından üzerine **Gelişmiş HTTP raporları** açılır öğesi.  Tıklayarak **HTTP büyük Platform**.
   
    ![CDN yönetim portalına - Gelişmiş Raporlar menüsü](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    Rapor seçenekleri görüntülenir.

## <a name="geography-reports-map-based"></a>Coğrafya raporları (harita tabanlı)
Kendisinden içeriğinizi istenen bölgeleri göstermek için bir harita yararlanan beş raporu vardır. Bu, dünya haritası, Amerika Birleşik Devletleri harita, Kanada harita, Avrupa Harita ve Asya Pasifik harita raporlardır.

Her harita tabanlı bir raporu coğrafi varlıkları sıralar (yani, ülkeler/bölgeler, bir harita kendisinden içeriğinizi istenen konumları görselleştirmenize yardımcı olmak için sağlanır. İsteğe bağlı bu bölgede deneyimli sayısına göre her bir bölgeye renk kodlaması tarafından Bunu yapmak kullanabilirsiniz. Koyu bölgelerle içeriğiniz için isteğe bağlı üst düzey gösterirken, içerik için daha düşük talep daha hafif gölgeli bölgeleri gösterir.

Haritanın altında doğrudan her bölge için trafiği ve bant genişliği ayrıntılı bilgi sağlanmaktadır. Bu, isabetleri, isabetli okuma sayısının yüzdesi, toplam veri miktarı toplam sayısını görüntülemek için (gigabayt) aktardınız ve her bölge için verilerin yüzde aktarılan sağlar. Bir açıklama her bu ölçümleri görüntüleyin. Son olarak, ad ve bölgede gerçekleşen isabet yüzdesi (yani, ülke/bölge, eyalet veya bölge), bir bölgede geldiğinizde araç ipucu olarak görüntülenir.

Kısa bir açıklama her coğrafi harita tabanlı rapor türü için aşağıda verilmiştir.

| Rapor adı | Açıklama |
| --- | --- |
| Dünya Haritası |Bu rapor, CDN içeriğini dünya çapında talep görüntülemenize olanak sağlar. Her bir ülke/bölge, bölgesinden kaynaklanan isabet yüzdesi göstermek için dünya Haritası üzerinde renk kodludur. |
| Amerika Birleşik Devletleri eşleme |Bu rapor, Amerika Birleşik Devletleri'nde CDN içeriği için isteğe bağlı görüntülemek sağlar. Her bir durum söz konusu bölgesinden kaynaklanan isabet yüzdesi belirtmek için bu haritada renk kodlu. |
| Kanada eşleme |Bu rapor, CDN içeriğini talep Kanada'da görüntülemenize olanak sağlar. Her bölge, bölgesinden kaynaklanan isabet yüzdesi belirtmek için bu haritada renk kodlu. |
| Avrupa eşleme |Bu rapor, CDN içeriğini talep Avrupa'da görüntülemenize olanak sağlar. Her bir ülke/bölge söz konusu bölgesinden kaynaklanan isabet yüzdesi belirtmek için bu haritada renk kodlu. |
| Asya Pasifik eşleme |Bu rapor, CDN içeriğini talep Asya'da görüntülemenize olanak sağlar. Her bir ülke/bölge söz konusu bölgesinden kaynaklanan isabet yüzdesi belirtmek için bu haritada renk kodlu. |

## <a name="geography-reports-bar-charts"></a>Coğrafya raporları (çubuk grafikler)
Üst Şehir ve ülke üst olan Coğrafya göre istatistiksel bilgi sağlayan iki ek raporlar vardır. Bu raporlar Şehir ve ülkeler/bölgeler, sırasıyla bu ülkelerden/bölgelerden kaynaklanan isabet sayısına göre sırala. Bu tür bir rapor oluşturma sırasında bir çubuk grafik, ilk 10 şehir veya belirli bir platform içerik istenen ülkeler/bölgeler gösterir. Bu çubuk grafik, içerik istekleri en yüksek sayısını oluşturmak bölgeleri hızlı bir şekilde değerlendirmek sağlar.

Graf (y ekseni) sol tarafında belirtilen bölgede kaç oluştu gösterir. Doğrudan aşağıdaki grafikte (x ekseni), her biri en çok 10 bölgeler için bir etiket bulabilirsiniz.

### <a name="using-the-bar-charts"></a>Çubuk grafikleri kullanma
* Üzerine gelin, araç ipucu olarak adı ve bölgede gerçekleşen toplam sayısı görüntülenir.
* Araç İpucu üst şehirler rapor için bir şehir adı, bölge ve ülke/bölge kısaltması tanımlar.
* Şehir veya bölge (yani, eyalet/il) bir isteğin kaynaklandığı belirlenemedi, bilinmeyen olduğunu gösterir. Ülke/bölge bilinmeyen ve ardından iki soru işareti (yani??) olması durumunda görüntülenir.
* Bir rapor "Avrupa" veya "Asya/Pasifik bölgesi." için ölçüler içerebilir Bu öğeler bu bölgelerdeki tüm IP adresleri hakkında istatistiksel bilgiler sağlamak için tasarlanmamıştır. Bunun yerine, yalnızca Avrupa veya Asya/Pasifik yerine belirli bir şehir veya ülke/bölge için yayıldığını IP adresleri kaynaklanan istekler için geçerlidir.

Çubuk grafik oluşturmak için kullanılan verileri altında görüntülenebilir. Burada aktarılan toplam sayısı isabetleri, isabetli okuma sayısının yüzdesi, veri miktarı (gigabayt) ve üst 250 bölgeler için verilerin yüzde aktarılan bulacaksınız. Bir açıklama her bu ölçümleri görüntüleyin.

Aşağıdaki raporlar her iki tür için kısa bir açıklaması sağlanır.

| Rapor adı | Açıklama |
| --- | --- |
| Üst şehirler |Bu rapor, bu bölgede şehirlere göre kaynaklanan isabet sayısı sıralar. |
| En gelişmiş ülkelerden |Bu rapor, ülkelerde/bölgelerde bu ülkede/bölgede kaynaklanan isabet sayısı göre sıralar. |

## <a name="daily-summary"></a>Günlük Özet
Günlük özet raporu isabetler ve belirli bir platform günlük olarak aktarılan verilerin toplam sayısı görüntülemenize olanak sağlar. Bu bilgiler hızlı bir şekilde CDN etkinlik desenlerini ayrım için kullanılabilir. Örneğin, bu raporu hangi günlerinde daha deneyimli veya beklenen trafik küçüktür algılamanıza yardımcı olabilir.

Bu tür bir rapor oluşturma sırasında bir çubuk grafik görsel bir gösterge platforma özgü talep deneyimli miktarı için günlük olarak raporun kapsadığı süre boyunca sağlar. Bu raporda her gün için bir çubuk görüntüleyerek bunu yapar. Örneğin, zaman aralığını seçme "Geçen hafta" adlı yedi çubuklu bir çubuk grafik oluşturur. Her bir çubuğun Toplam İsabet deneyimli o gün sayısını gösterir.

Graf (y ekseni) sol tarafında belirtilen tarihte kaç oluştu gösterir. Doğrudan aşağıdaki grafikte (x ekseni), tarihi belirten bir etiket bulabilirsiniz (biçim: YYYY-MM-DD) rapora dahil her gün için.

> [!TIP]
> Bu tarihte oluşan toplam sayısı, üzerine gelin, araç ipucu olarak görüntülenir.
> 
> 

Çubuk grafik oluşturmak için kullanılan verileri altında görüntülenebilir. Burada raporun kapsadığı her gün için toplam isabet sayısı ve (gigabayt) aktarılan veri miktarını bulacaksınız.

## <a name="by-hour"></a>Saatlik olarak
Rapor tarafından saat isabetler ve belirli bir platform saatlik olarak aktarılan verilerin toplam sayısı görüntülemenize olanak sağlar. Bu bilgiler hızlı bir şekilde CDN etkinlik desenlerini ayrım için kullanılabilir. Örneğin, bu rapor daha yüksek veya beklenen trafiği düşük deneyimi, günlük zaman dilimlerinde algılamanıza yardımcı olabilir.

Bu tür bir rapor oluşturma sırasında bir çubuk grafik raporun kapsadığı süre boyunca saatlik olarak yaşadı platforma özgü talep miktarı için farklı görsel bir gösterge sağlar. Bu raporun kapsadığı her saat bir çubuk görüntüleyerek bunu yapar. Örneğin, 24 saatlik seçerek zaman aralığı yirmi dört çubuklu bir çubuk grafik oluşturur. Her bir çubuğun isabet deneyimli bir saat boyunca toplam sayısını gösterir.

Graf (y ekseni) sol tarafında belirtilen başı kaç oluştu gösterir. Doğrudan aşağıdaki grafikte (x ekseni), tarih/saat belirten bir etiket bulabilirsiniz (biçim: YYYY-MM-DD ss: dd) rapora dahil olduğu her saat. Saat, 24 saat biçimini kullanarak bildirilir ve UTC/GMT saat dilimi kullanılarak belirtilir.

> [!TIP]
> Üzerine gelin, araç ipucu olarak bu saat sırasında oluşan toplam sayısı görüntülenir.
> 
> 

Çubuk grafik oluşturmak için kullanılan verileri altında görüntülenebilir. Burada her saat raporun kapsadığı toplam isabet sayısı ve (gigabayt) aktarılan veri miktarını bulacaksınız.

## <a name="by-file"></a>Dosya
Dosya tarafından rapor isteğe bağlı ve belirli bir platform için en çok istenen varlıklar üzerinde sonucunda trafik miktarını görüntülemenize olanak sağlar. Bu tür bir rapor oluşturma sırasında bir çubuk grafik en çok 10 en çok istenen varlıklar üzerinde belirtilen süre boyunca oluşturulur.

> [!NOTE]
> Bu raporun amacı doğrultusunda, edge CNAME URL'leri, eşdeğer bir CDN URL'lerinin dönüştürülür. Bu CDN veya edge CNAME bu istek için kullanılan URL bakılmaksızın, bir varlığın ilişkili toplam sayısı için doğru bir tally sağlar.
> 
> 

Graf (y ekseni) sol tarafında, belirtilen süre boyunca her varlık için istekleri sayısını gösterir. Doğrudan aşağıdaki grafikte (x ekseni), her biri en iyi 10 istenilen varlıkların için dosya adını belirten bir etiket bulabilirsiniz.

Çubuk grafik oluşturmak için kullanılan verileri altında görüntülenebilir. Burada her istenen 250 varlıkları için aşağıdaki bilgileri bulacaksınız: göreli yol, isabetleri, isabetli okuma sayısının yüzdesi, veri miktarı toplam sayısı transfer (gigabayt) ve aktarılan veri yüzdesi.

## <a name="by-file-detail"></a>Dosya ayrıntısı
Tarafından dosya ayrıntısı raporu isteğe bağlı ve belirli bir varlık için belirli bir platform üzerinden sonucunda trafik miktarını görüntülemenize olanak sağlar. Bu rapor çok üst kısmında, Ayrıntılar, dosya seçenektir. Bu seçenek seçilen platform üzerinde en çok istenen varlıklarınızın listesini sağlar. Tarafından dosya ayrıntısı raporu oluşturmak için istenen varlık ayrıntıları için dosya seçeneğinden seçmeniz gerekir. Sonra bir çubuk grafik belirtilen süre boyunca oluşturulan günlük istek miktarını gösterir.

Graf (y ekseni) sol tarafında bir varlığın belirli bir günde yaşadı isteklerinin toplam sayısı gösterir. Doğrudan aşağıdaki grafikte (x ekseni), tarihi belirten bir etiket bulabilirsiniz (biçim: YYYY-MM-DD), CDN için varlık için isteğe bağlı olarak bildirildi.

Çubuk grafik oluşturmak için kullanılan verileri altında görüntülenebilir. Burada raporun kapsadığı her gün için toplam isabet sayısı ve (gigabayt) aktarılan veri miktarını bulacaksınız.

## <a name="by-file-type"></a>Dosya türüne göre
Dosya türüne göre raporu talep ve dosya türüne göre sonucunda trafik miktarını görüntülemenize olanak sağlar. Bu tür bir rapor oluşturma sırasında en iyi 10 dosya türleri tarafından oluşturulan isabet yüzdesi halka grafik gösterir.

> [!TIP]
> Halka grafikteki bir dilim üzerine gelin, Internet medya, dosya türü bir araç ipucu olarak görüntülenir türü.
> 
> 

Halka grafik oluşturmak için kullanılan verileri altında görüntülenebilir. Burada bulacaksınız dosya adı uzantısı/Internet medya türü, toplam isabet sayısı yüzdesi, isabetleri, aktarılan veri miktarı (gigabayt) ve veri yüzdesi üst 250 dosya türlerinin her biri için aktarılmaz.

## <a name="by-directory"></a>Dizin tarafından
Dizin tarafından rapor isteğe bağlı ve belirli bir dizinden içerik için belirli bir platform üzerinden sonucunda trafik miktarını görüntülemenize olanak sağlar. Bu tür bir rapor oluşturma sırasında bir çubuk grafik en çok 10 dizinlerde içerik tarafından oluşturulan toplam sayısı belirtin.

### <a name="using-the-bar-chart"></a>Çubuk grafik kullanma
* Karşılık gelen dizininin göreli yolu görüntülemek için bir çubuğu üzerine gelin.
* İsteğe bağlı dizin tarafından hesaplarken bir dizin, bir alt klasöründe depolanan içerikleri sayılmaz. Bu hesaplama yalnızca gerçek dizinde depolanan içerik için oluşturulan istek sayısını kullanır.
* Bu raporun amacı doğrultusunda, edge CNAME URL'leri, eşdeğer bir CDN URL'lerinin dönüştürülür. Bu CDN veya edge CNAME bu istek için kullanılan URL bakılmaksızın, bir varlığın ile ilişkili tüm istatistikler için doğru bir tally sağlar.

Graf (y ekseni) sol tarafında, ilk 10 dizinlerinde depolanan içerik isteklerini toplam sayısını gösterir. Her bir çubuk grafikte bir dizin temsil eder. Üst 250 tam dizinleri bölümünde listelenen bir dizin çubuğuna eşleştirilecek renk kodlaması düzeni kullanın.

Çubuk grafik oluşturmak için kullanılan verileri altında görüntülenebilir. Burada 250 üst dizinlerin her biri için aşağıdaki bilgileri bulacaksınız: göreli yol, isabetleri, isabetli okuma sayısının yüzdesi, veri miktarı toplam sayısı transfer (gigabayt) ve aktarılan veri yüzdesi.

## <a name="by-browser"></a>Tarayıcıya göre
Tarayıcı tarafından raporun hangi tarayıcılar içerik istemek için kullanılan görüntülemenize olanak sağlar. Bu tür bir rapor oluşturma sırasında bir pasta grafiğinin en çok 10 tarayıcılar tarafından işlenen isteklerin yüzdesini gösterir.

### <a name="using-the-pie-chart"></a>Pasta grafiği kullanma
* Bir dilim bir tarayıcının adını ve sürümünü görüntülemek için pasta grafiğinin üzerine gelin.
* Bu raporun amacı doğrultusunda, her bir benzersiz tarayıcı/sürüm bileşimi farklı bir tarayıcı olarak kabul edilir.
* "Diğer" adlı dilim diğer tüm tarayıcılar ve sürümleri tarafından işlenen isteklerin yüzdesini gösterir.

Pasta grafiği oluşturmak için kullanılan verileri altında görüntülenebilir. Burada, tarayıcı türü/sürümü sayısı, toplam isabet sayısı ve isabet yüzdesi her üst 250 tarayıcılar için bulacaksınız.

## <a name="by-referrer"></a>Göre başvuran
Tarafından başvuran rapor seçilen platformunda içeriği için üst başvuran görüntülemenize olanak sağlar. Bir başvuran bir istek oluşturulduğu ana bilgisayar adını belirtir. Bu tür bir rapor oluşturma sırasında bir çubuk grafik isteğe bağlı üst 10 Başvuranlar tarafından oluşturulan (yani, isabet) miktarını belirtir.

Graf (y ekseni) sol tarafında bir varlık için her başvuran yaşadı isteklerinin toplam sayısı gösterir. Her bir çubuk grafikte bir başvuran temsil eder. 250 başvuran üst bölümünde listelenen bir başvuran çubuğuna eşleştirilecek renk kodlaması düzeni kullanın.

Çubuk grafik oluşturmak için kullanılan verileri altında görüntülenebilir. Burada URL'si, toplam isabet sayısı ve oluşturulan her üst 250 başvuran isabet yüzdesi bulacaksınız.

## <a name="by-download"></a>İndirme ile
Raporu tarafından indir en çok istenen içerik için yükleme desenleri çözümlemenizi sağlar. Raporun üst karşılaştırır tamamlanmış indirmeleri en üst 10 istenilen varlıkların yüklemeleriyle çalıştığının bir çubuk grafiği içerir. Her bir çubuğun (mavi) denenen bir yükleme veya indirme tamamlandı (yeşil) olmasına göre renk kodludur.

> [!NOTE]
> Bu raporun amacı doğrultusunda, edge CNAME URL'leri, eşdeğer bir CDN URL'lerinin dönüştürülür. Bu CDN veya edge CNAME bu istek için kullanılan URL bakılmaksızın, bir varlığın ile ilişkili tüm istatistikler için doğru bir tally sağlar.
> 
> 

Graf (y ekseni) sol tarafında her istenen 10 varlıkları için dosya adını gösterir. Doğrudan aşağıdaki grafikte (x ekseni), toplam çalıştı ve tamamlanan indirme sayılarını gösteren etiketleri bulabilirsiniz.

Doğrudan çubuk grafiğin altına aşağıdaki bilgileri listelenir için üst 250 istenilen varlıkların: göreli yol (dosya adı dahil), tamamlanana kadar indirilen sayısı, isteğinde bulunuldu sayısı ve yüzdesi eksiksiz bir indirme sonuçlanan istek sayısı.

> [!TIP]
> Bizim CDN HTTP istemcisi (yani tarayıcısı) verilmediği için ne zaman bir varlık tamamen indirilir. Sonuç olarak, bir varlık tamamen durum kodları ve bayt aralığı göre indirildi olmadığını hesaplamak sahibiz istekleri. Biz bu hesaplama yaparken Ara ilk şey mi 200 Tamam durum koduna istek sonuçlarını ' dir. Bu durumda, ardından tüm varlık kapsadığından emin olmak için bayt aralığı isteklerinin bakacağız. Son olarak, istenen varlık boyutunu aktarılan veri miktarını karşılaştırın. Aktarılan veriler dosya boyutundan büyük veya eşit ise ve bayt aralığı isteklerini, bu varlık için uygundur, isabet eksiksiz bir indirme olarak sayılır.
> 
> Bu rapor yorumlayıcı yapısı nedeniyle, bu raporun doğruluğunu ve tutarlılık değiştirebildiğini aşağıdaki noktaları aklınızda tutmanız gerekir.
> 
> * Kullanıcı aracılarına farklı davranır olduğunda trafiği desenlerini doğru şekilde tahmin edilemez. Bu, % 100'den büyük olan tamamlanan indirme sonuçlara neden olabilir.
> * HTTP aşamalı indirme avantajlarından yararlanmak varlıklar doğru bir şekilde bu rapor tarafından temsil edilebilir değil. Bu, bir videoda farklı konumlara arayan kullanıcılar kaynaklanır.
> 
> 

## <a name="by-404-errors"></a>404 hataları
404 hataları tarafından rapor, en çok Nebyl Nalezen 404 durum kodu oluşturan bir içerik türünü belirlemenize izin verir. Raporun üst çubuk grafik bir 404 bulunamadı durum kodu en iyi duruma döndürüldü ilk 10 varlıkları içerir. Bu çubuk grafik, toplam sayısı, o varlıklar için 404 bulunamadı durum kodu ile sonuçlandı. istekte bulunan isteklerinin karşılaştırır. Her çubuk renk kodludur. Sarı çubuk, istek 404 bulunamadı durum kodu içinde kaynaklandığını göstermek için kullanılır. Bir kırmızı çubuk varlık istekleri toplam sayısını belirtmek için kullanılır.

> [!NOTE]
> Bu raporun amacı doğrultusunda, aşağıdakilere dikkat edin:
> 
> * İsabet bir varlığın durum koduna bakılmaksızın herhangi bir isteğini temsil eder.
> * Edge CNAME URL'leri, eşdeğer bir CDN URL'lerinin dönüştürülür. Bu CDN veya edge CNAME bu istek için kullanılan URL bakılmaksızın, bir varlığın ile ilişkili tüm istatistikler için doğru bir tally sağlar.
> 
> 

Graf (y ekseni) sol tarafında her bir 404 bulunamadı durum kodunda sonuçlanan ilk 10 istenilen varlıkların için dosya adını belirtir. Doğrudan aşağıdaki grafikte (x ekseni), toplam istek sayısı ve bir 404 bulunamadı durum kodunda sonuçlanan sayısını gösteren etiketleri bulabilirsiniz.

Doğrudan çubuk grafiğin altına aşağıdaki bilgileri listelenir için üst 250 istenilen varlıkların: 404 bulunamadı durum koduna, varlık istendi, toplam sayısı sonuçlanan istek sayısı için göreli yol (dahil olmak üzere dosya adı) ve bir 404 bulunamadı durum kodunda sonuçlanan istek yüzdesi.

## <a name="see-also"></a>Ayrıca bkz.
* [Azure CDN'ye genel bakış](cdn-overview.md)
* [Microsoft Azure cdn'de gerçek zamanlı İstatistikler](cdn-real-time-stats.md)
* [Kural altyapısı kullanarak varsayılan HTTP davranışı geçersiz kılma](cdn-rules-engine.md)
* [Uç performansını çözümleme](cdn-edge-performance.md)

