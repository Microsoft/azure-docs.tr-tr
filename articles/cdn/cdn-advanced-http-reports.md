---
title: Gelişmiş HTTP raporları Azure CDN kullanım istatistiklerini çözümleme | Microsoft Docs
description: Microsoft Azure CDN 'de gelişmiş HTTP raporları oluşturmayı öğrenin. Bu raporlar CDN etkinliği hakkında ayrıntılı bilgi sağlar.
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
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: e2ef1367cbbb1fb71159c4520c3d1481904c1769
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92779331"
---
# <a name="analyze-usage-statistics-with-azure-cdn-advanced-http-reports"></a>Azure CDN gelişmiş HTTP raporları ile kullanım istatistiklerini analiz etme
## <a name="overview"></a>Genel Bakış
Bu belgede Microsoft Azure CDN 'de gelişmiş HTTP raporlaması açıklanmaktadır. Bu raporlar CDN etkinliği hakkında ayrıntılı bilgi sağlar.

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Gelişmiş HTTP raporlarına erişme
1. CDN profili dikey penceresinde **Yönet** düğmesine tıklayın.
   
    ![CDN profili dikey penceresi Yönet düğmesi](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
   
    CDN yönetim portalı açılır.
2. **Analiz** sekmesinin üzerine gelin ve ardından **Gelişmiş http raporları** açılır öğesi üzerine gelin.  **Http büyük platformu**' na tıklayın.
   
    ![CDN yönetim portalı-gelişmiş raporlar menüsü](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)
   
    Rapor seçenekleri görüntülenir.

## <a name="geography-reports-map-based"></a>Coğrafya raporları (eşleme tabanlı)
İçeriğinizi istediğiniz bölgeleri göstermek için bir haritanın avantajlarından yararlanan beş rapor vardır. Bu raporlar Dünya Haritası, Birleşik Devletler Haritası, Kanada haritası, Avrupa Haritası ve Asya Pasifik haritalarıdır.

Her harita tabanlı rapor, coğrafi varlıkları (yani, ülkeler/bölgeler) derecelendirerek, içeriğinizin istediği konumları görselleştirmenize yardımcı olmak için bir eşlem sağlanır. Bu bölge, bu bölgede karşılaşılan talep miktarına göre her bölge için renk kodlayarak bunu yapabilir. Daha açık gölgeli bölgeler içeriğiniz için daha düşük talebi gösterir, daha koyu bölgeler içeriğiniz için daha yüksek talepler gösterir.

Her bölge için ayrıntılı trafik ve bant genişliği bilgileri, doğrudan haritanın altında sağlanır. Bu, toplam isabet sayısını, isabetlerin yüzdesini, aktarılan toplam veri miktarını (gigabayt cinsinden) ve her bölge için aktarılan verilerin yüzdesini görüntülemenize olanak sağlar. Bu ölçümlerin her biri için bir açıklama görüntüleyin. Son olarak, bir bölgenin üzerine geldiğinizde (örn. ülke/bölge, eyalet veya bölge), bölgede oluşan isabetlerin adı ve yüzdesi bir araç ipucu olarak görüntülenir.

Her harita tabanlı Coğrafya raporu türü için aşağıda kısaca bir açıklama verilmiştir.

| Rapor Adı | Description |
| --- | --- |
| Dünya Haritası |Bu rapor, CDN içeriğiniz için dünya çapında talebi görüntülemenizi sağlar. Her ülke/bölge, bu bölgeden kaynaklanan isabetlerin yüzdesini belirtmek için dünya haritasında renk kodludur. |
| Birleşik Devletler eşleme |Bu rapor, Birleşik Devletler CDN içeriğiniz için talebi görüntülemenize olanak sağlar. Her durum bu haritada, bu bölgeden kaynaklı isabetlerin yüzdesini göstermek için renk kodludur. |
| Kanada haritası |Bu rapor, Kanada 'daki CDN içeriğiniz için talebi görüntülemenize olanak sağlar. Her eyalet bu haritada, bu bölgeden kaynaklı isabetlerin yüzdesini göstermek için renk kodludur. |
| Avrupa Haritası |Bu rapor, Avrupa 'daki CDN içeriğiniz için talebi görüntülemenize olanak sağlar. Her ülke/bölge, söz konusu bölgeden kaynaklanan isabetlerin yüzdesini belirtmek için bu haritada renk kodludur. |
| Asya Pasifik eşleme |Bu rapor, Asya 'daki CDN içeriğiniz için talebi görüntülemenize olanak sağlar. Her ülke/bölge, söz konusu bölgeden kaynaklanan isabetlerin yüzdesini belirtmek için bu haritada renk kodludur. |

## <a name="geography-reports-bar-charts"></a>Coğrafya raporları (çubuk grafikler)
Coğrafi şehir ve en üst ülkeler olan coğrafya 'ya göre istatistiksel bilgiler sağlayan iki ek rapor vardır. Bu raporlar, bu ülkelerden/bölgelerden kaynaklanan isabetlerin sayısına göre, şehir ve ülkeleri/bölgeleri sırasıyla derecelendirmez. Bu tür bir rapor oluşturduktan sonra bir çubuk grafik, belirli bir platform üzerinde içerik isteyen ilk 10 şehir veya ülke/bölge olduğunu gösterir. Bu çubuk grafik, içeriğiniz için en yüksek istek sayısını üreten bölgeleri hızlı bir şekilde değerlendirmenize olanak tanır.

Grafiğin sol tarafı (y ekseni), belirtilen bölgede kaç isabetlerin oluştuğunu gösterir. Grafiğin hemen altında (x ekseni), ilk 10 bölgenin her biri için bir etiket bulacaksınız.

### <a name="using-the-bar-charts"></a>Çubuk grafiklerini kullanma
* Bir çubuğun üzerine geldiğinizde, bölgede gerçekleşen isabetlerin adı ve toplam isabet sayısı bir araç ipucu olarak görüntülenir.
* En üstteki şehirler raporunun araç ipucu adı, eyalet/il ve ülke/bölge kısaltması tarafından bir şehri tanımlar.
* Bir isteğin kaynaklandığı şehir veya bölge (örneğin, Eyalet/bölge) belirlenmiyorsa, bunların bilinmediği anlamına gelir. Ülke/bölge bilinmiyorsa, iki soru işareti (ör.,??) görüntülenir.
* Bir rapor "Avrupa" veya "Asya/Pasifik bölgesi" ölçümlerini içerebilir. Bu öğeler, bu bölgelerdeki tüm IP adresleri hakkında istatistiksel bilgiler sağlamaya yöneliktir. Bunun yerine, yalnızca belirli bir şehir veya ülke/bölge yerine Avrupa veya Asya/Pasifik üzerinden yayılan IP adreslerinden kaynaklanan istekler için geçerlidir.

Çubuk grafiği oluşturmak için kullanılan veriler bunun altında görüntülenebilir. Toplam isabet sayısı, isabet yüzdesi, aktarılan veri miktarı (gigabayt cinsinden) ve en üstteki 250 bölgeleri için aktarılan verilerin yüzdesi bulacaksınız. Bu ölçümlerin her biri için bir açıklama görüntüleyin.

Aşağıdaki tür raporların her ikisi için de kısa bir açıklama verilmiştir.

| Rapor Adı | Description |
| --- | --- |
| En çok şehir |Bu rapor, şehri ilgili bölgeden kaynaklanan isabet sayısına göre derecelendirir. |
| Popüler ülkeler |Bu rapor, ülkeleri/bölgeleri, bu ülke/bölgeden kaynaklanan isabetlerin sayısına göre derecelendirir. |

## <a name="daily-summary"></a>Günlük Özet
Günlük Özet raporu, belirli bir platform üzerinden günlük olarak aktarılan toplam isabet sayısını ve verileri görüntülemenize olanak sağlar. Bu bilgiler, CDN etkinlik desenlerini hızlı bir şekilde ayırt etmek için kullanılabilir. Örneğin, bu rapor, beklenen trafikten daha yüksek veya daha düşük olan günleri tespit etmenize yardımcı olabilir.

Bu tür bir rapor oluşturduktan sonra, bir çubuk grafik, raporun kapsadığı zaman dilimi boyunca günlük olarak karşılaşılan platforma özgü talep miktarına göre görsel bir gösterge sağlar. Bu işlem, rapordaki her gün için bir çubuk görüntüleyerek yapılır. Örneğin, "Last Week" olarak adlandırılan zaman döneminin seçilmesi yedi çubuk içeren bir çubuk grafik oluşturur. Her çubuk, o gün üzerinde karşılaşılan isabetlerin toplam sayısını gösterir.

Grafiğin sol tarafı (y ekseni), belirtilen tarihte kaç isabetlerin oluştuğunu gösterir. Grafiğin hemen altında (x ekseni), rapora dahil edilen her bir gün için tarihi (Biçim: YYYY-AA-GG) belirten bir etiket bulacaksınız.

> [!TIP]
> Bir çubuğun üzerine geldiğinizde, bu tarihte gerçekleşen toplam isabet sayısı bir araç ipucu olarak görüntülenir.
> 
> 

Çubuk grafiği oluşturmak için kullanılan veriler bunun altında görüntülenebilir. Toplam isabet sayısını ve rapor kapsamındaki her bir gün için aktarılan veri miktarını (gigabayt cinsinden) bulacaksınız.

## <a name="by-hour"></a>Saate göre
Saate göre raporu, belirli bir platform üzerinden saatlik olarak aktarılan toplam isabet sayısını ve verileri görüntülemenize olanak sağlar. Bu bilgiler, CDN etkinlik desenlerini hızlı bir şekilde ayırt etmek için kullanılabilir. Örneğin, bu rapor, gün içinde beklenen trafikten daha yüksek veya daha düşük olan zaman aralıklarını tespit etmenize yardımcı olabilir.

Bu tür bir rapor oluşturduktan sonra, bir çubuk grafik, raporun kapsadığı zaman dilimi boyunca saatlik olarak karşılaşılan platforma özgü talep miktarına göre görsel bir gösterge sağlar. Bu işlem, raporun kapsadığı her saat için bir çubuk görüntüleyerek yapılır. Örneğin, 24 saatlik bir zaman dilimi seçilmesi yirmi dört çubuk içeren bir çubuk grafik oluşturacaktır. Her çubuk, bu saat içinde karşılaşılan toplam isabet sayısını belirtir.

Grafiğin sol tarafı (y ekseni), belirtilen saatte kaç isabetlerin oluştuğunu gösterir. Grafiğin hemen altında (x ekseni), rapora dahil edilen her saat için tarih/saat (Biçim: YYYY-AA-GG SS: DD) belirten bir etiket bulacaksınız. Saat, 24 saat biçimi kullanılarak bildirilir ve UTC/GMT saat dilimi kullanılarak belirtilir.

> [!TIP]
> Bir çubuğun üzerine geldiğinizde, bu saat boyunca gerçekleşen isabetlerin toplam sayısı bir araç ipucu olarak görüntülenir.
> 
> 

Çubuk grafiği oluşturmak için kullanılan veriler bunun altında görüntülenebilir. Toplam isabet sayısını ve rapor kapsamındaki her saat için aktarılan veri miktarını (gigabayt cinsinden) bulacaksınız.

## <a name="by-file"></a>Dosyaya göre
Dosya tarafından yapılan rapor, istenen varlıklar için belirli bir platformda uygulanan talep miktarını ve trafiği görüntülemenize olanak sağlar. Bu tür bir rapor oluşturduktan sonra, belirtilen dönemde en çok istenen 10 varlık üzerinde bir çubuk grafik oluşturulacaktır.

> [!NOTE]
> Bu raporun amaçları doğrultusunda, Edge CNAME URL 'Leri eşdeğer CDN URL 'lerine dönüştürülür. Bu, istekte bulunan CDN veya Edge CNAME URL 'sinden bağımsız olarak bir varlıkla ilişkili isabetlerin toplam sayısının doğru bir şekilde oluşturulmasına izin verir.
> 
> 

Grafiğin sol tarafı (y ekseni), belirtilen dönem içindeki her bir varlık için istek sayısını gösterir. Grafiğin hemen altında (x ekseni), istenen ilk 10 varlık için dosya adını belirten bir etiket bulacaksınız.

Çubuk grafiği oluşturmak için kullanılan veriler bunun altında görüntülenebilir. En üstteki 250 istenen varlıkların her biri için aşağıdaki bilgileri bulabilirsiniz: göreli yol, toplam isabet sayısı, isabetlerin yüzdesi, aktarılan veri miktarı (gigabayt cinsinden) ve aktarılan verilerin yüzdesi.

## <a name="by-file-detail"></a>Dosya ayrıntısına göre
Dosya ayrıntısı raporu, belirli bir varlık için belirli bir platformda uygulanan talep miktarını ve trafiği görüntülemenize olanak sağlar. Bu raporun en üst kısmında, seçeneğinin dosya ayrıntıları vardır. Bu seçenek, seçilen platformda en çok istenen varlıkların bir listesini sağlar. Bir dosya ayrıntısı raporu oluşturmak için, dosya ayrıntıları seçeneğinin yanındaki istenen varlığı seçmeniz gerekir. Bundan sonra çubuk grafik, belirtilen dönem boyunca oluşturduğu günlük talep miktarını gösterir.

Grafiğin sol tarafı (y ekseni), bir varlığın belirli bir günde yaşadığı toplam istek sayısını gösterir. Grafiğin (x ekseni) doğrudan altında, varlık için CDN talebinin bildirildiği tarihi (Biçim: YYYY-AA-GG) belirten bir etiket görürsünüz.

Çubuk grafiği oluşturmak için kullanılan veriler bunun altında görüntülenebilir. Toplam isabet sayısını ve rapor kapsamındaki her bir gün için aktarılan veri miktarını (gigabayt cinsinden) bulacaksınız.

## <a name="by-file-type"></a>Dosya türüne göre
Dosya türüne göre raporu, istek miktarını ve dosya türüne göre tahakkuk eden trafiği görüntülemenize olanak sağlar. Bu tür bir rapor oluşturduktan sonra, halka grafik ilk 10 dosya türü tarafından oluşturulan isabetlerin yüzdesini gösterir.

> [!TIP]
> Halka grafiğindeki bir dilimin üzerine geldiğinizde, bu dosya türünün Internet medya türü bir araç ipucu olarak görüntülenir.
> 
> 

Halka grafik oluşturmak için kullanılan veriler bunun altında görüntülenebilir. Dosya adı uzantısı/Internet medya türü, toplam isabet sayısı, isabetlerin yüzdesi, aktarılan veri miktarı (gigabayt cinsinden) ve her bir üst 250 dosya türü için aktarılan verilerin yüzdesi bulunur.

## <a name="by-directory"></a>Dizine göre
Dizin oluşturma raporu, belirli bir dizinden içerik için belirli bir platformda oluşan istek miktarını ve trafiği görüntülemenize olanak sağlar. Bu tür bir rapor oluşturduktan sonra, bir çubuk grafik, ilk 10 dizindeki içerik tarafından oluşturulan isabetlerin toplam sayısını gösterir.

### <a name="using-the-bar-chart"></a>Çubuk grafiği kullanma
* İlgili dizinin göreli yolunu görüntülemek için bir çubuğun üzerine gelin.
* Dizinin bir alt klasöründe depolanan içerik, talebe göre hesaplama sırasında sayılmaz. Bu hesaplama yalnızca gerçek dizinde depolanan içerik için oluşturulan istek sayısına dayanır.
* Bu raporun amaçları doğrultusunda, Edge CNAME URL 'Leri eşdeğer CDN URL 'lerine dönüştürülür. Bu, bir varlıkla ilişkili tüm istatistiklerin, bunu istemek için kullanılan CDN veya Edge CNAME URL 'sinden bağımsız olarak doğru bir şekilde oluşturulmasına olanak sağlar.

Grafiğin sol tarafı (y ekseni), ilk 10 dizininizde depolanan içerik için toplam istek sayısını gösterir. Grafikteki her çubuk bir dizini temsil eder. En üstteki 250 tam dizinler bölümünde listelenen bir dizine bir çubuk ile eşleştirmek için renk kodlama şemasını kullanın.

Çubuk grafiği oluşturmak için kullanılan veriler bunun altında görüntülenebilir. En üstteki 250 dizinlerin her biri için aşağıdaki bilgileri bulabilirsiniz: göreli yol, toplam isabet sayısı, isabetlerin yüzdesi, aktarılan veri miktarı (gigabayt cinsinden) ve aktarılan verilerin yüzdesi.

## <a name="by-browser"></a>Tarayıcıya göre
Tarayıcı tarafından yapılan rapor, içerik istemek için hangi tarayıcıların kullanıldığını görüntülemenize olanak sağlar. Bu tür bir rapor oluşturduktan sonra, bir pasta grafik, ilk 10 tarayıcı tarafından işlenen isteklerin yüzdesini gösterir.

### <a name="using-the-pie-chart"></a>Pasta grafiğini kullanma
* Bir tarayıcının adını ve sürümünü görüntülemek için pasta grafiğindeki bir dilimin üzerine gelin.
* Bu raporun amaçları doğrultusunda, her benzersiz tarayıcı/sürüm kombinasyonu farklı bir tarayıcı olarak değerlendirilir.
* "Diğer" adlı dilim, diğer tüm tarayıcılar ve sürümler tarafından işlenen isteklerin yüzdesini gösterir.

Pasta grafiğini oluşturmak için kullanılan veriler bunun altında görüntülenebilir. Tarayıcı türü/sürüm numarasını, toplam isabet sayısını ve ilk 250 tarayıcıların her biri için isabet yüzdesini bulacaksınız.

## <a name="by-referrer"></a>Başvuran tarafından
Başvuran bu rapor, seçilen platformda içeriğe en üstteki başvuranları görüntülemenize olanak sağlar. Başvuran, bir isteğin oluşturulduğu ana bilgisayar adını gösterir. Bu tür bir rapor oluşturduktan sonra, bir çubuk grafik, ilk 10 başvura göre oluşturulan istek miktarını (örn. isabetler) gösterecektir.

Grafiğin sol tarafı (y ekseni), bir varlığın her bir başvuran için yaşadığı isteklerin toplam sayısını gösterir. Grafikteki her çubuk bir başvuran temsil eder. En üstteki 250 başvuran bölümünde listelenen başvuran için bir çubuk ile eşleştirmek için renk kodlama şemasını kullanın.

Çubuk grafiği oluşturmak için kullanılan veriler bunun altında görüntülenebilir. URL 'yi, toplam isabetlerin sayısını ve ilk 250 başvurağından oluşturulan isabetlerin yüzdesini bulacaksınız.

## <a name="by-download"></a>Indirme yoluyla
Indirme ölçütü raporu, en son içerikleriniz için karşıdan yükleme düzenlerini analiz etmenizi sağlar. Raporun en üstünde, tamamlanan indirmeleri, en çok istenen 10 varlık için tamamlanan indirmelerle karşılaştıran bir çubuk grafik bulunur. Her çubuk, bir indirme girişimi (mavi) veya tamamlanmış bir indirme (yeşil) olup olmadığına göre renk kodludur.

> [!NOTE]
> Bu raporun amaçları doğrultusunda, Edge CNAME URL 'Leri eşdeğer CDN URL 'lerine dönüştürülür. Bu, bir varlıkla ilişkili tüm istatistiklerin, bunu istemek için kullanılan CDN veya Edge CNAME URL 'sinden bağımsız olarak doğru bir şekilde oluşturulmasına olanak sağlar.
> 
> 

Grafiğin sol tarafı (y ekseni), istenen ilk 10 varlık için dosya adını gösterir. Grafiğin hemen altında (x ekseni), denenen/tamamlanan indirmelerin toplam sayısını belirten Etiketler bulacaksınız.

Çubuk grafiğinin doğrudan altında, aşağıdaki bilgiler, istenen ilk 250 varlık: göreli yol (dosya adı dahil), tamamlanmasına kaç kez indirildiğini, istek yaptığı sürelerin sayısını ve tamamen İndirilme ile sonuçlanan isteklerin yüzdesini içerecek şekilde listelenecektir.

> [!TIP]
> Bir varlık tamamen indirildiyse CDN, bir HTTP istemcisi (tarayıcı) tarafından bilgilendirilmez. Sonuç olarak, bir varlığın durum kodlarına ve bayt aralığı isteklerine göre tamamen indirilip indirilmediğini hesapladık. Bu hesaplamayı yaparken ilk şey, isteğin 200 OK bir durum kodu ile sonuçlanmasının gerekip gerekmediğini belirtir. Bu durumda, tüm varlığı kapsadıklarından emin olmak için, bayt aralığı isteklerine bakacağız. Son olarak, aktarılan veri miktarını istenen varlığın boyutuna göre karşılaştırıyoruz. Aktarılan veriler dosya boyutundan büyük veya bu değere eşitse ve bu varlık için bayt aralığı istekleri uygunsa, isabet tam indirme olarak sayılır.
> 
> Bu raporun yorumlık doğası nedeniyle, bu raporun tutarlılığını ve doğruluğunu değiştirebilecek aşağıdaki noktaları göz önünde bulundurmanız gerekir.
> 
> * Kullanıcı aracıları farklı davrandığınızda trafik desenleri doğru şekilde tahmin edilemez. Bu, %100 ' den büyük olan tamamlanmış indirme sonuçları üretebilir.
> * HTTP aşamalı Indirmenin avantajlarından yararlanan varlıklar bu rapor tarafından doğru şekilde gösterilmeyebilir. Bunun nedeni, bir videoda farklı konumlara arayan kullanıcılardan kaynaklanır.
> 
> 

## <a name="by-404-errors"></a>404 ile hata
404 ' den fazla hata raporu, en fazla 404 bulunamayan durum kodunu üreten içerik türünü tanımlamanızı sağlar. Raporun en üstünde, 404 olmayan bir durum kodunun döndürdüğü ilk 10 varlık için bir çubuk grafik bulunur. Bu çubuk grafik, bu varlıklar için 404 bulunamadı durum kodu ile sonuçlanan isteklerin toplam istek sayısını karşılaştırır. Her çubuk renk kodludur. İsteğin 404 olmayan bir durum kodu ile sonuçlandığını göstermek için sarı bir çubuk kullanılır. Varlık için toplam istek sayısını göstermek için kırmızı bir çubuk kullanılır.

> [!NOTE]
> Bu raporun amaçları doğrultusunda aşağıdakilere göz önünde yer verilmiştir:
> 
> * İsabet, durum kodundan bağımsız olarak bir varlık için istekleri temsil eder.
> * Edge CNAME URL 'Leri eşdeğer CDN URL 'lerine dönüştürülür. Bu, bir varlıkla ilişkili tüm istatistiklerin, bunu istemek için kullanılan CDN veya Edge CNAME URL 'sinden bağımsız olarak doğru bir şekilde oluşturulmasına olanak sağlar.
> 
> 

Grafiğin sol tarafı (y ekseni), 404 olmayan bir durum kodu ile sonuçlanan ilk 10, istenen varlıkların her biri için dosya adını gösterir. Grafiğin hemen altında (x ekseni), toplam istek sayısını ve 404 bulunamayan bir durum kodu ile sonuçlanan istek sayısını belirten Etiketler bulacaksınız.

Çubuk grafiğinin doğrudan altında, aşağıdaki bilgiler, istenen ilk 250 varlık için listelenir: göreli yol (dosya adı dahil), 404 ile sonuçlanan istek sayısı durum kodu, varlığın istenen toplam sayısı ve bir 404 ile sonuçlanan isteklerin yüzdesi durum kodu.

## <a name="see-also"></a>Ayrıca bkz.
* [Azure CDN'ye Genel Bakış](cdn-overview.md)
* [Microsoft Azure CDN 'de gerçek zamanlı istatistikler](cdn-real-time-stats.md)
* [Kurallar altyapısını kullanarak varsayılan HTTP davranışını geçersiz kılma](./cdn-verizon-premium-rules-engine.md)
* [Kenar performansını çözümleme](cdn-edge-performance.md)