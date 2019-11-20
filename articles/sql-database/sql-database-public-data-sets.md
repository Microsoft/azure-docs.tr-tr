---
title: Azure Analytics için genel veri kümeleri
description: Azure Analytics hizmetleri ve çözümlerini prototip ve test etmek için kullanabileceğiniz genel veri kümeleri hakkında bilgi edinin.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2018
ms.openlocfilehash: 763a45446771f7b13178e22b0f7a35bd68bfc8bd
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821274"
---
# <a name="public-data-sets-for-testing-and-prototyping"></a>Test ve prototipleme için ortak veri kümeleri

Depolama ve analiz hizmetleri ile çözümlerini prototip ve test etmek için kullanabileceğiniz veriler için genel veri kümeleri listesine gidin.

## <a name="us-government-and-agency-data"></a>ABD devlet ve kurumu verileri

| Veri kaynağı | Veriler hakkında | Dosyalar hakkında |
|---|---|---|
| [ABD devlet verileri](https://www.census.gov/data.html) | ABD 'de Agric, kele, tüketici, ekosistemleri, eğitim, enerji, finans, sağlık, yerel kamu, üretim, Maritime, okyanus, kamu güvenliği ve bilimçlerini kapsayan 190.000 'den fazla veri kümesi | HTML, XML, CSV, JSON, Excel ve diğer birçok biçimdeki çeşitli boyutlarda bulunan dosyalar. Kullanılabilir veri kümelerini dosya biçimine göre filtreleyebilirsiniz. |
| [ABD Census verileri](https://www.census.gov/data.html) | ABD popülasyonu hakkında istatistiksel veriler | Veri kümeleri çeşitli biçimlerde. |
| [NASA 'dan Dünya Bilimi verileri](https://earthdata.nasa.gov/) | Agricsal, Atmosphere, Biyoküre, Climate, cryosphere, insan boyutları, hydrosphere, Land Surface, Oceans, Sun-Earth etkileşimleri ve daha fazlasını kapsayan 32.000 ' den fazla veri koleksiyonu. | Veri kümeleri çeşitli biçimlerde. |
| [Airline uçuş gecikmeleri ve diğer taşıma verileri](https://www.transtats.bts.gov/OT_Delay/OT_DelayCause1.asp) | "Nakliklerdeki (nokta) nakliye için ABD Bakanlığı (BTS), büyük uçak taşıyıcılar tarafından çalıştırılan yurtiçi fışıkların zaman içindeki performansını izler. Zaman içinde, geciktirilen, iptal edilen ve ayrılan fışıklardan oluşan Özet bilgileri görüntülenir... Bu Web sitesine gönderilen Özet tablolarında. " | Dosyalar CSV biçimindedir. |
| [Trafik pertalimler-US Fatality Analysis Reporting System (FARS)](https://www.nhtsa.gov/FARS) | "Fars, motor araç trafiği Kilitlenmelerinde çok önemli sakatlama ile ilgili olarak NHTSA, Congress ve American kamu yıllık verileri sağlayan bir ülke genelinde görselleştirmenizdeki." | "Kendi Fatality verilerinizi oluşturma FARS sorgu sistemini kullanarak çevrimiçi olarak çalışır. Ya da tüm FARS verilerini FTP sitesinden sunmak için 1975 adresinden indirebilirsiniz. " |
| [Toxic kimyasal verileri-EPA Toxicity ForeCaster (ToxCast™) verileri](https://www.epa.gov/chemical-research/toxicity-forecaster-toxcasttm-data) | "EPA 'nın en güncel, herkese açık bir şekilde, binlerce kimlik üzerinde yüksek aktarım hızı verileri. Bu veriler EPA 'nin ToxCast araştırma çabasıyla oluşturulur. " | Veri kümeleri, elektronik tablolar, R paketleri ve MySQL veritabanı dosyaları gibi çeşitli biçimlerde kullanılabilir. |
| [Toxic kimyasal verileri-NIH Tox21 Data Challenge 2014](https://tripod.nih.gov/tox21/challenge/) | "2014 Tox21 veri sınaması, bilimcilerin, Toxic 'de Toxicoloji tarafından test edilmesine yardımcı olacak şekilde tasarlanmıştır. bu şekilde, kıza ve çözer 'ın, ı, | Veri kümeleri SMSETS ve SDF biçimlerinde kullanılabilir. Veriler ~ 10.000 çözer (Tox21 10.000) Tox21 koleksiyonunda "asdeyin etkinlik verileri ve kimyasal yapıları" sağlar. |
| [NCBI 'daki Biotechnology ve genom verileri](https://www.ncbi.nlm.nih.gov/guide/data-software/) | Genes, genomes ve protesto bileşenleri kapsayan birden çok veri kümesi. | Veri kümeleri metin, XML, BOMBVE diğer biçimlerdeki biçimlerdir. Bir uygulama için kullanılabilir. |

## <a name="other-statistical-and-scientific-data"></a>Diğer istatistiksel ve bilimsel veriler

| Veri kaynağı | Veriler hakkında | Dosyalar hakkında |
|---|---|---|
| [New York City TAXI verileri](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml) | "TAXI seyahat kayıtları, çekme ve geri alma tarihleri/saatleri, çekme ve Dropoff konumları, seyahat mesafeleri, listelenen fareler, ücret türleri, ödeme türleri ve sürücü tarafından bildirilen yolcular sayımlarını yakalayan alanları içerir." | Veri kümeleri, aya göre CSV dosyalarıdır. |
| [Microsoft Research veri kümeleri-"araştırma için veri bilimi"](https://www.microsoft.com/research/academic-program/data-science-microsoft-research/) | İnsan bilgisayar etkileşimini, ses/video, veri araştırma/bilgi alımı, Jeo-uzamsal/konum, doğal dil işleme ve Robotics/bilgisayar vizyonu kapsayan birden çok veri kümesi. | Veri kümeleri, indirme için daraltılmış çeşitli biçimlerde bulunur. |
| [Genel genom verileri](https://www.completegenomics.com/public-data/) | "Tüm insan genistanlarından oluşan çeşitli veri kümesi, herhangi bir genomik çalışmayı geliştirmek için herkese açık kullanıma sunuldu..." Sağlayıcı, tam Genomiks, özel bir for-kar şirketi. | Veri kümeleri, ayıkladıktan sonra UNIX metin biçiminde. Analiz araçları da kullanılabilir. |
| [Bilim verileri bulut verilerini aç](https://www.opensciencedatacloud.org/projects/) | "Açık bilim veri bulutu, terabayttan ve petab, bilimsel veri kümelerini depolama, paylaşma ve çözümleme için kaynaklarla bilimsel bir topluluk sağlar."| Veri kümeleri çeşitli biçimlerde. |
| [Küresel iklim verileri-Worldclid](https://worldclim.org/) | "Worldclid, yaklaşık 1 km2 üzerinde uzamsal bir çözünürlükte küresel bir klima katmanları (gridupsal veri) kümesidir. Bu veriler, eşleme ve uzamsal modelleme için kullanılabilir. " | Bu dosyalar Jeo uzamsal verileri içerir. Daha fazla bilgi için bkz. [veri biçimi](https://worldclim.org/formats1). |
| [İnsan topluluğu hakkındaki veriler-GDELT projesi](https://www.gdeltproject.org/data.html) | "GDELT projesi en büyük, en kapsamlı ve en yüksek çözünürlüklü açık insan topluluğu veritabanı veritabanıdır." | Ham veri dosyaları CSV biçimindedir. |
| [Criteo 'dan makine öğrenimi için tıklama tahmin verileri tanıtımı](https://labs.criteo.com/2013/12/download-terabyte-click-logs/) | "Genel olarak ML veri kümesi yayınlandı." Daha fazla bilgi için bkz. [Criteo 'ın 1 TB 'ı tahmin veri kümesi](https://blogs.technet.microsoft.com/machinelearning/20../../now-available-on-azure-ml-criteos-1tb-click-prediction-dataset/). | |
| [ClueWeb09, limon projesinden ayarlanan metin araştırma verileri](https://www.lemurproject.org/clueweb09.php/) | "ClueWeb09 veri kümesi, bilgi alımı ve ilgili insan dili teknolojileriyle araştırmayı desteklemek için oluşturulmuştur. Ocak ve 2009 ' de toplanan 10 dilde 1.000.000.000 Web sayfası hakkında bilgi içerir. " | Bkz. [veri kümesi bilgileri](https://www.lemurproject.org/clueweb09/datasetInformation.php).|

## <a name="online-service-data"></a>Çevrimiçi hizmet verileri

| Veri kaynağı | Veriler hakkında | Dosyalar hakkında |
|---|---|---|
| [GitHub Arşivi](https://www.githubarchive.org/) | "GitHub Arşivi, genel GitHub zaman çizelgesini [olay] kaydetmek, arşivlemek ve daha fazla analiz için kolayca erişilebilir hale getirmek için bir projem." | JSON kodlu olay arşivlerini bir web istemcisinden. gz (gzip) biçiminde indirin. |
| [Ghtorıo projeden GitHub etkinlik verileri](http://ghtorrent.org/) | "Gana projesi [,] GitHub REST API tarafından sunulan ölçeklenebilir, sorgulanabilir, çevrimdışı bir veri yansıtma oluşturma çabadır. Ghtorıo, GitHub genel olay zaman satırını izler. Her olay için, kendi içeriğini ve bağımlılıklarını alır. " | MySQL veritabanı dökümleri CSV biçimindedir. |
| [Stack Overflow veri dökümü](https://archive.org/details/stackexchange) | "Bu, yığın değişim ağı 'ndaki (Stack Overflow] dahil) tüm Kullanıcı tarafından katkıda bulunulan içeriklerin anonim olarak dökümünü alır." | "Her site [Örneğin Stack Overflow], bzip2 sıkıştırması kullanılarak 7-zip aracılığıyla daraltılmış XML dosyalarından oluşan ayrı bir arşiv olarak biçimlendirilir. Her site Arşivi gönderi, Kullanıcı, oy, yorum, PostHistory ve PostLinks içerir. " |
