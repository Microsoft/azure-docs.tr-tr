---
title: Dağıtım sütunlarını seçin – Hyperscale (Citus)-PostgreSQL için Azure veritabanı
description: PostgreSQL için Azure veritabanı 'nın (Citus) ortak senaryolarında dağıtım sütunlarını nasıl seçeceğinizi öğrenin.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 129eff8c954c0c5469d3607e6ae16ce3202630ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91929345"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus"></a>PostgreSQL için Azure veritabanı 'nda dağıtım sütunları seçin – hiper ölçek (Citus)

Her tablonun dağıtım sütununu seçme, en önemli modelleme kararlarından biridir. PostgreSQL için Azure veritabanı – Hyperscale (Citus), satırların dağıtım sütununun değerine göre parçaları parçalar halinde depolar.

Doğru seçim, ilgili verileri aynı fiziksel düğümlerde gruplandırır, bu da sorguları hızlı bir şekilde yapar ve tüm SQL özellikleri için destek ekler. Yanlış bir seçim sistemin yavaş çalışmasını sağlar ve tüm SQL özelliklerini düğümler genelinde desteklemez.

Bu makale, en yaygın iki hiper ölçek (Citus) senaryosu için dağıtım sütunu ipuçları sağlar.

### <a name="multi-tenant-apps"></a>Çok kiracılı uygulamalar

Çok kiracılı mimari, sorguları sunucu grubundaki düğümlere dağıtmak için hiyerarşik bir veritabanı modelleme formu kullanır. Veri hiyerarşisinin en üstü *KIRACı kimliği* olarak bilinir ve her tablodaki bir sütunda depolanması gerekir.

Hiper ölçek (Citus), hangi kiracı KIMLIĞINI içerdikleri ve eşleşen tablo parçacısını bulan sorguları denetler. Sorguyu, parçayı içeren tek bir çalışan düğümüne yönlendirir. Aynı düğüme yerleştirilmiş tüm ilgili verilerle bir sorgu çalıştırmak birlikte bulundurma olarak adlandırılır.

Aşağıdaki diyagramda, çok kiracılı veri modelindeki birlikte bulundurma gösterilmektedir. Her biri tarafından dağıtılan iki tablo, hesap ve kampanya içerir `account_id` . Gölgeli kutular parçaları temsil eder. Yeşil parçalar bir çalışan düğümünde birlikte depolanır ve mavi parçalar başka bir çalışan düğümünde depolanır. Her iki tablo da aynı hesap kimliğiyle sınırlandırıldığı zaman, hesaplar ve kampanyalar arasındaki JOIN sorgusunun tüm gerekli verileri tek bir düğümde nasıl bir araya vereceğini fark edebilirsiniz \_ .

![Çok kiracılı birlikte bulundurma](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Bu tasarımı kendi şemanızda uygulamak için, uygulamanızda ne tür bir kiracı oluşturduğunu belirlemek için bu tasarımı yapın. Şirket, hesap, kuruluş veya müşteri ortak örnekleri içerir. Sütun adı veya gibi bir şey olacaktır `company_id` `customer_id` . Sorgularınızın her birini inceleyin ve sizinle aynı kiracı KIMLIĞINE sahip satırlara dahil olan tüm tabloları kısıtlamak için ek WHERE yan tümceleri varsa işe çalışırdı.
Çok kiracılı modeldeki sorgular bir kiracıya kapsamlıdır. Örneğin, satış veya envanterdeki sorgular belirli bir mağaza kapsamında kapsamlandırılır.

#### <a name="best-practices"></a>En iyi uygulamalar

-   **Dağıtılmış tabloları ortak Kiracı \_ kimliği sütunuyla bölümleme.** Örneğin, kiracıların şirketler olduğu bir SaaS uygulamasında, kiracı \_ kimliğinin şirket kimliği olması olasıdır \_ .
-   **Küçük bir çapraz kiracı tablolarını başvuru tablolarına dönüştürün.** Birden çok kiracı bir bilgi tablosunu paylaşıyorsa, onu başvuru tablosu olarak dağıtın.
-   **Tüm uygulama sorgularının kiracı kimliğine göre filtreleneceğini kısıtla \_ .** Her sorgu tek seferde bir kiracı için bilgi istemelidir.

Bu tür bir uygulamanın nasıl oluşturulacağını gösteren bir örnek için [çok kiracılı öğreticiyi](./tutorial-design-database-hyperscale-multi-tenant.md) okuyun.

### <a name="real-time-apps"></a>Gerçek zamanlı uygulamalar

Çok kiracılı mimari, hiyerarşik bir yapı sunar ve veri bulundurma kullanarak kiracı başına sorguları yönlendirebilir. Bunun aksine, gerçek zamanlı mimarilerin verilerin belirli dağıtım özelliklerine göre yüksek bir paralel işleme elde etmelerini sağlar.

Gerçek zamanlı modeldeki dağıtım sütunları için bir terim olarak "varlık KIMLIĞI" kullanırız. Tipik varlıklar, kullanıcılar, konaklar veya cihazlardır.

Gerçek zamanlı sorgular genellikle tarih veya kategoriye göre gruplanmış sayısal toplamalar ister. Hiper ölçek (Citus), bu sorguları her parçaya kısmi sonuçlar için gönderir ve düzenleyici düğümündeki son yanıtı ayrıştırır. Çok sayıda düğüm mümkün olduğunca en hızlı şekilde çalışır ve tek bir düğümün orantısız iş miktarı olması gerekir.

#### <a name="best-practices"></a>En iyi uygulamalar

-   **Dağıtım sütunu olarak yüksek kardinalite içeren bir sütun seçin.** Karşılaştırma için, sipariş tablosundaki yeni, ücretli ve sevk edilen değerlere sahip bir durum alanı, bir dağıtım sütunu yetersiz tercih edilir. Yalnızca, verileri tutan parça sayısını sınırlayan ve bunu işleyebilen düğümlerin sayısını sınırlayan yalnızca birkaç değeri kabul eder. Yüksek kardinalite içeren sütunlar arasında, genellikle Group by yan tümcelerinde veya JOIN anahtarları olarak kullanılan sütunları seçmek de iyi bir yoldur.
-   **Eşit bir dağıtım içeren bir sütun seçin.** Belirli ortak değerlere eğilmiş bir sütunda bir tablo dağıtırsanız, tablodaki veriler belirli parçaların birikmesine eğilimindedir. Bu parçaları tutan düğümler diğer düğümlerden daha fazla çalışma yapıyor.
-   **Olgu ve boyut tablolarını ortak sütunlarına dağıtın.**
    Olgu tablonuz yalnızca bir dağıtım anahtarına sahip olabilir. Başka bir anahtara eklenen tablolar olgu tablosuna katılamaz. Hangi sıklıkta birleştirildiğini ve birleştirme satırlarının boyutunu temel alarak bir boyut seçin.
-   **Bazı boyut tablolarını başvuru tablolarında değiştirin.** Bir boyut tablosu olgu tablosuyla birlikte bulundurulamıyor ise, boyut tablosunun kopyalarını bir başvuru tablosu formundaki tüm düğümlere dağıtarak sorgu performansını geliştirebilirsiniz.

Bu tür bir uygulamanın nasıl oluşturulacağını gösteren bir örnek için [gerçek zamanlı Pano öğreticisini](./tutorial-design-database-hyperscale-realtime.md) okuyun.

### <a name="time-series-data"></a>Zaman serisi verileri

Bir zaman serisi iş yükünde, uygulamalar eski bilgileri arşivlarken son bilgileri sorgular.

Hiper ölçekte (Citus) zaman serisi bilgilerini modelleyen en yaygın hata, zaman damgasının kendisini bir dağıtım sütunu olarak kullanmaktır. Zaman aralıklarını temel alan bir karma dağıtım, parçaları parçalar halinde birlikte tutmak yerine farklı parçalara rastgele olarak dağıtır. Zaman genellikle başvuru aralıklarını (örneğin, en son veriler) içeren sorgular. Bu tür bir karma dağıtım, ağ ek yüküne neden olur.

#### <a name="best-practices"></a>En iyi uygulamalar

-   **Dağıtım sütunu olarak zaman damgası seçmeyin.** Farklı bir dağıtım sütunu seçin. Çok kiracılı bir uygulamada, kiracı KIMLIĞINI kullanın veya gerçek zamanlı bir uygulamada varlık KIMLIĞINI kullanın.
-   **Bunun yerine PostgreSQL tablo bölümlemesini kullanın.** Zaman içinde oluşan büyük bir veriyi, farklı zaman aralıkları içeren her tablo ile birden fazla devralınmış tabloya bölmek için tablo bölümleme kullanın. Hiper ölçekte (Citus) bir Postgres bölümlenmiş tablo dağıtmak, devralınan tablolar için parçalar oluşturur.

## <a name="next-steps"></a>Sonraki adımlar
- Dağıtılmış veriler [arasındaki arada](concepts-hyperscale-colocation.md) bulunan sorguların hızla çalışmasına nasıl yardımcı olduğunu öğrenin.
