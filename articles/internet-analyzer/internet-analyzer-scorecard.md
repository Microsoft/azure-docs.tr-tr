---
title: Karneyi yorumlama | Microsoft Docs
description: Azure Internet çözümleyici hakkında SSS.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a05c704f62d93bdc110d8a5943dd266ca22eb5f4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512892"
---
# <a name="interpreting-your-scorecard"></a>Karneyi yorumlama

Karne sekmesi, testlerinizin toplanan ve analiz edilen sonuçlarını içerir. Her testin kendi karneleri vardır. Karneler, ağ gereksinimleriniz için veri odaklı sonuçlar sağlamak üzere ölçüm sonuçlarının hızlı ve anlamlı özetlerini sağlar. Internet çözümleyici Analize odaklanarak karar vermenize olanak tanır.

Karne sekmesi Internet çözümleyici kaynak menüsünde bulunabilir. 


## <a name="filters"></a>Filtreler

* ***Test:*** Sonuçlarını görüntülemek istediğiniz testi seçin-her bir testin kendi karnesi vardır. Analizi tamamlamaya yetecek kadar veri olduğunda test verileri görüntülenir; çoğu durumda bu, 24 saat içinde olmalıdır. 
* ***Zaman aralığı & bitiş tarihi:*** Üç karne günlük olarak oluşturulur: her bir karne, bir önceki 24 saat (gün), önceki yedi gün (hafta) ve 30 gün önce (ay), farklı bir toplama dönemi yansıtır. Görmek istediğiniz dönemin son gününü seçmek için "bitiş tarihi" filtresini kullanın. 
* ***Ülke:*** Son kullanıcılarınız olan her ülke için bir karne oluşturulur. Genel filtre tüm son kullanıcıları içerir.

## <a name="measurement-count"></a>Ölçüm sayısı

Ölçüm sayısı analizin güvenini etkiler. Count arttıkça, sonuç daha doğru olur. En azından, testler gün başına en az 100 ölçüm için hedeflemelidir. Ölçüm sayıları çok düşükse, lütfen JavaScript istemcisini uygulamanızda daha sık yürütülecek şekilde yapılandırın. A ve B uç noktaları için ölçü sayıları çok benzer olmalıdır, ancak küçük farklılıklar beklenen ve sorunsuz olmalıdır. Büyük farklılıklar söz konusu olduğunda sonuçlara güvenilmemelidir.

## <a name="percentiles"></a>Yüzdebirlik değeri

Milisaniye cinsinden ölçülen gecikme süresi, Internet 'teki bir kaynak ve hedef arasındaki hız ölçmeye yönelik popüler bir ölçümdür. Gecikme süresi normal şekilde dağıtılır (yani, aritmetik ortalama gibi istatistikler kullanırken sonuçları çarpıtabilecek büyük gecikme süreleriyle ilgili bir "uzun kuyruk" olduğundan). Alternatif olarak, yüzdebirlik değeri, verileri çözümlemek için "dağıtım ücretsizdir" bir yol sağlar. Örneğin, ortanca veya 50. Yüzdeliğini yüzdebirlik değeri, dağıtımın ortasında, değerlerin yukarısında ve yarısını özetler. 75. yüzdebirlik değeri, dağıtımındaki tüm değerlerin %75 ' inden büyük olduğu anlamına gelir. Internet Çözümleyicisi yüzdebirlik değeri, P50, P75 ve P95 şeklinde toplu olarak ifade eder.

Internet Çözümleyicisi yüzdebirlik değeri, _örnek ölçümlerdir_. Bu, doğru _popülasyon ölçüsüne_karşılık gelen bir değer. Örneğin, Güney California ve Microsoft University 'teki öğrenciler arasındaki günlük gerçek popülasyon medyan gecikmesi, bu gün içindeki tüm isteklerin ortanca gecikme değeridir. Uygulamada, tüm isteklerin değerini ölçmek pratik olduğundan, makul bir büyük örneğin doğru popülasyon temsilcisi olduğunu varsaytık.

Analiz amaçları için, P50 (ortanca), bir gecikme süresi dağıtımı için beklenen değer olarak faydalıdır. P95 gibi daha yüksek yüzdebirlik değeri, en kötü gecikme süresinin en kötü durumda olduğunu belirlemek için faydalıdır. Müşteri gecikmesini genel olarak anlamak istiyorsanız, P50 doğru ölçüdür. En kötü performanslı müşterilere yönelik performansı anlamak istiyorsanız P95, odak olmalıdır. P75, aralarındaki bir dengedir.


## <a name="deltas"></a>Değişimleri

Bir Delta, A ve B uç noktaları için ölçüm değerlerinde farklılık gösterir. deltas, b 'nin avantajlarından daha iyi olduğunu gösteren bir. pozitif değerler, b 'nin performansının daha kötü olduğunu gösterir. Deltas mutlak (ör. 10 milisaniye) veya göreli (%5) olabilir.

## <a name="confidence-interval"></a>Güvenirlik aralığı 

Güvenirlik aralıkları (CI), ortanca, P75 veya ortalama gibi popülasyon ölçüsünü içeren bir değer aralığıdır. %95 CI kullanmanın genel istatistiksel kuralını izliyoruz.

Internet çözümleyici 'de, örnek ölçümün gerçek popülasyon ölçüsüne çok yakın olduğunu gösterdiği için dar bir güvenilirlik aralığı iyidir. Geniş bir güvenirlik aralığı, örnek ölçümünüzün gerçek popülasyon ölçümünü yansıttığımız daha az belirsizlik anlamına gelir. CI 'yı geliştirmenin en iyi yolu, ölçüm sayılarını artırmaktır.

## <a name="time-series"></a>Zaman serisi 

Zaman serisinde bir ölçümün zaman içinde nasıl değiştiği gösterilmektedir. Internet 'te, yoğun trafik dönemleri, hafta içi hafta sonu oluşturma farkları ve tatiller gibi performansı etkileyen çok sayıda zamana bağlı etken vardır.


## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi edinmek için bkz. [Internet Analyzer 'A genel bakış](internet-analyzer-overview.md).
