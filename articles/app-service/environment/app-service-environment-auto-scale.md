---
title: Otomatik ölçeklendirme ve App Service Ortamı v1-Azure
description: Otomatik ölçeklendirme ve App Service Ortamı
services: app-service
documentationcenter: ''
author: btardif
manager: erikre
editor: ''
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: f0c49e1835412b61817ff3571dd3ee1eaa29f21f
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070079"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>Otomatik ölçeklendirme ve App Service Ortamı v1

> [!NOTE]
> Bu makale App Service Ortamı v1 ile ilgilidir.  Daha güçlü altyapıda daha kolay ve çalışır App Service Ortamı daha yeni bir sürümü vardır. Yeni sürüm hakkında daha fazla bilgi edinmek için [App Service ortamı giriş](intro.md)ile başlayın.
> 

Azure App Service ortamlar *Otomatik ölçeklendirmeyi*destekler. İş havuzlarını ölçümlere veya zamanlamaya göre otomatik ölçeklendirme yapabilirsiniz.

![Bir çalışan havuzu için otomatik ölçeklendirme seçenekleri.][intro]

Otomatik ölçeklendirme, bir App Service ortamını bütçenize ve veya yükleme profilinize uyacak şekilde otomatik olarak büyüyerek ve küçülterek kaynak kullanımınızı iyileştirir.

## <a name="configure-worker-pool-autoscale"></a>Çalışan havuzu otomatik ölçeklendirmeyi yapılandırma
Otomatik ölçeklendirme işlevselliğine, çalışan havuzunun **Ayarlar** sekmesinden erişebilirsiniz.

![Çalışan havuzunun ayarlar sekmesi.][settings-scale]

Buradan, bir App Service planını ölçeklendirçalışırken gördüğünüz deneyim aynı olduğundan arabirim oldukça tanıdık olmalıdır. 

![El ile ölçeklendirme ayarları.][scale-manual]

Otomatik ölçeklendirme profili de yapılandırabilirsiniz.

![Otomatik ölçeklendirme ayarları.][scale-profile]

Ölçeklendirme profilleri, ölçeğinizdeki sınırları ayarlamak için faydalıdır. Bu şekilde, bir üst sınır (2) ayarlayarak, daha düşük bir sınır değeri (1) ve öngörülebilir bir harcama ucu ayarlayarak tutarlı bir performans deneyimine sahip olabilirsiniz.

![Profildeki ayarları ölçeklendirin.][scale-profile2]

Bir profil tanımladıktan sonra, profil tarafından tanımlanan sınırlar dahilinde çalışan havuzundaki örneklerin sayısını artırma veya azaltma için otomatik ölçeklendirme kuralları ekleyebilirsiniz. Otomatik ölçeklendirme kuralları ölçümlere göre yapılır.

![Kural ölçeklendirin.][scale-rule]

 Otomatik ölçeklendirme kurallarını tanımlamak için herhangi bir çalışan havuzu veya ön uç ölçümleri kullanılabilir. Bu ölçümler, kaynak dikey penceresinde izleyebilmeniz veya uyarılarını ayarlamanıza yönelik ölçümlerdir.

## <a name="autoscale-example"></a>Otomatik ölçeklendirme örneği
Bir App Service ortamının otomatik ölçeklendirilmesi, bir senaryo aracılığıyla yürüyerek en iyi şekilde gösterilmiştir.

Bu makalede, otomatik ölçeklendirmeyi ayarlarken gereken tüm noktalar açıklanmaktadır. Bu makalede, App Service Ortamı barındırılan ortamları App Service otomatik ölçeklendirmeyi gerçekleştirirken oyun oynamak için sunulan etkileşimler adım adım açıklanmaktadır.

### <a name="scenario-introduction"></a>Senaryo tanıtımı
Filiz, yönettikleri iş yüklerinin bir kısmını App Service ortamına geçirmiştir.

App Service ortamı aşağıdaki şekilde el ile ölçeklenebilen şekilde yapılandırılmıştır:

* **Ön uçlar:** 3
* **Çalışan havuzu 1**: 10
* **Çalışan Havuzu 2**: 5
* **Çalışan havuzu 3**: 5

Çalışan havuzu 1, üretim iş yükleri için, çalışan Havuzu 2 ve çalışan havuzu 3 kalite güvencesi (QA) ve geliştirme iş yükleri için kullanılır.

QA ve dev için App Service planları el ile ölçeğe göre yapılandırılmıştır. Üretim App Service planı, yük ve trafikte çeşitlerle başa çıkmak için otomatik ölçeklendirme olarak ayarlanır.

Filiz, uygulamaya çok tanıdık gelecektir. Bunlar, çalışanların ofis ofisinde oldukları sırada kullandıkları bir iş kolu (LOB) uygulaması olduğundan, yükün yoğun saatlerin 9:00 ile 6:00 PM arasında olduğunu bilir. Kullanım, kullanıcılar söz konusu gün için bittiğinde daha sonra bırakılanlar. Kullanıcılar mobil cihazlarını veya ev bilgisayarlarını kullanarak uygulamaya uzaktan erişebildiğinden, yoğun saatlerin dışında hala bazı yük vardır. Üretim App Service planı, aşağıdaki kurallarla CPU kullanımına göre otomatik olarak otomatik ölçeklendirme için yapılandırılmıştır:

![LOB uygulaması için özel ayarlar.][asp-scale]

| **Otomatik ölçeklendirme profili – iş günleri – App Service planı** | **Otomatik ölçeklendirme profili – hafta sonları – App Service plan** |
| --- | --- |
| **Ada** Hafta içi profil |**Ada** Hafta sonu profili |
| **Ölçek ölçütü:** Zamanlama ve performans kuralları |**Ölçek ölçütü:** Zamanlama ve performans kuralları |
| **Profilinizi** Ları |**Profilinizi** Hafta |
| **Türüyle** Yinelenme |**Türüyle** Yinelenme |
| **Hedef Aralık:** 5 ila 20 örnek |**Hedef Aralık:** 3 ila 10 örnek |
| **Miş** Pazartesi, Salı, Çarşamba, Perşembe, Cuma |**Miş** Cumartesi, Pazar |
| **Başlangıç Zamanı:** 9:00 |**Başlangıç Zamanı:** 9:00 |
| **Saat dilimi:** UTC-08 |**Saat dilimi:** UTC-08 |
|  | |
| **Otomatik ölçeklendirme kuralı (ölçek artırma)** |**Otomatik ölçeklendirme kuralı (ölçek artırma)** |
| **Kaynak** Üretim (App Service Ortamı) |**Kaynak** Üretim (App Service Ortamı) |
| **Ölçüt** 'SUNA |**Ölçüt** 'SUNA |
| **Çalışmasını** % 60 ' den büyük |**Çalışmasını** % 80 ' den büyük |
| **Sürenin** 5 Dakika |**Sürenin** 10 dakika |
| **Zaman toplama:** Average |**Zaman toplama:** Average |
| **Ön** Sayıyı 2 ' ye kadar artır |**Ön** Sayıyı 1 artır |
| **Seyrek Erişimli (dakika):** 15 |**Seyrek Erişimli (dakika):** 20 |
|  | |
| **Otomatik ölçeklendirme kuralı (ölçek azaltma)** |**Otomatik ölçeklendirme kuralı (ölçek azaltma)** |
| **Kaynak** Üretim (App Service Ortamı) |**Kaynak** Üretim (App Service Ortamı) |
| **Ölçüt** 'SUNA |**Ölçüt** 'SUNA |
| **Çalışmasını** % 30 ' dan az |**Çalışmasını** % 20 ' den az |
| **Sürenin** 10 dakika |**Sürenin** 15 dakika |
| **Zaman toplama:** Average |**Zaman toplama:** Average |
| **Ön** Sayıyı 1 azaltma |**Ön** Sayıyı 1 azaltma |
| **Seyrek Erişimli (dakika):** 20 |**Seyrek Erişimli (dakika):** 10 |

### <a name="app-service-plan-inflation-rate"></a>App Service planı enflasyon oranı
Otomatik ölçeklendirme için yapılandırılan App Service planlar, saat başına en fazla hızda yapılır. Bu fiyat, otomatik ölçeklendirme kuralında belirtilen değerlere göre hesaplanabilir.

*App Service planı enflasyon oranını* anlamak ve hesaplamak, bir çalışan havuzunda ölçek değişiklikleri anlık olmadığından App Service ortam otomatik ölçeklendirme için önemlidir.

App Service planı enflasyon oranı aşağıdaki şekilde hesaplanır:

![App Service planı enflasyon oranı hesaplaması.][ASP-Inflation]

Üretim App Service planının hafta Içi profili için otomatik ölçeklendirme – ölçek genişletme kuralına göre:

![Otomatik ölçeklendirme için ölçek artırma kuralına bağlı olarak, hafta içi haftanın enflasyon oranını App Service.][Equation1]

Otomatik ölçeklendirme, üretim App Service planının hafta sonu profili için ölçeği büyütme durumunda, formül şu şekilde çözümlenir:

![Otomatik ölçeklendirme için ölçek artırma kuralına göre hafta sonları için enflasyon oranı planı App Service.][Equation2]

Bu değer, ölçek azaltma işlemleri için de hesaplanabilir.

Üretim App Service planının hafta Içi profili için otomatik ölçeklendirme – ölçek azaltma kuralına bağlı olarak, bu şöyle görünür:

![Otomatik ölçeklendirme için ölçek azaltma kuralına bağlı olarak, hafta içi günlere yönelik enflasyon oranı planı App Service.][Equation3]

Otomatik ölçeklendirme için, üretim App Service planının hafta sonu profili için ölçek Azaltma kuralı için formül şu şekilde çözümlenir:  

![Otomatik ölçeklendirme – ölçek azaltma kuralına göre hafta sonları için App Service plan fiyatı.][Equation4]

Üretim App Service planı, hafta boyunca en fazla sekiz örnek/saat ve hafta sonu boyunca dört örnek/saat üzerinden büyüyebilir. Örnek, hafta boyunca en fazla dört örnek/saat ve hafta içinde altı örnek/saat hızında serbest bırakabilir.

Birden çok App Service planı bir çalışan havuzunda barındırılıyorsa, bu çalışan havuzunda barındırmakta olan tüm App Service planlarının enflasyon oranının toplamı olarak *Toplam enflasyon oranını* hesaplamanız gerekir.

![Bir çalışan havuzunda barındırılan birden çok App Service planı için toplam enflasyon oranı hesaplaması.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Çalışan havuzu otomatik ölçeklendirme kurallarını tanımlamak için App Service planı enflasyon oranını kullanın
Otomatik ölçeklendirme olarak yapılandırılmış App Service planlarını barındıran çalışan havuzlarının bir kapasite arabelleği ayrılması gerekir. Arabellek, otomatik ölçeklendirme işlemlerinin, App Service planını gerektiği şekilde büyütmesine ve küçültmesine izin verir. En düşük arabellek, hesaplanan toplam App Service planı enflasyon oranı olacaktır.

App Service ortamı ölçeklendirme işlemlerinin uygulanması biraz zaman aldığı için herhangi bir değişiklik, bir ölçeklendirme işlemi devam ederken oluşabilecek daha fazla talep değişikliği hesaba malıdır. Bu gecikmeyi karşılamak için, hesaplanan toplam App Service planı enflasyon oranını her bir otomatik ölçeklendirme işlemi için eklenen minimum örnek sayısı olarak kullanmanızı öneririz.

Bu bilgilerle, filiz aşağıdaki otomatik ölçeklendirme profilini ve kurallarını tanımlayabilir:

![LOB örneği için otomatik ölçeklendirme profili kuralları.][Worker-Pool-Scale]

| **Otomatik ölçeklendirme profili – hafta Içi** | **Otomatik ölçeklendirme profili – hafta sonları** |
| --- | --- |
| **Ada** Hafta içi profil |**Ada** Hafta sonu profili |
| **Ölçek ölçütü:** Zamanlama ve performans kuralları |**Ölçek ölçütü:** Zamanlama ve performans kuralları |
| **Profilinizi** Ları |**Profilinizi** Hafta |
| **Türüyle** Yinelenme |**Türüyle** Yinelenme |
| **Hedef Aralık:** 13-25 örnek |**Hedef Aralık:** 6-15 örnek |
| **Miş** Pazartesi, Salı, Çarşamba, Perşembe, Cuma |**Miş** Cumartesi, Pazar |
| **Başlangıç Zamanı:** 7:00 |**Başlangıç Zamanı:** 9:00 |
| **Saat dilimi:** UTC-08 |**Saat dilimi:** UTC-08 |
|  | |
| **Otomatik ölçeklendirme kuralı (ölçek artırma)** |**Otomatik ölçeklendirme kuralı (ölçek artırma)** |
| **Kaynak** Çalışan havuzu 1 |**Kaynak** Çalışan havuzu 1 |
| **Ölçüt** WorkersAvailable |**Ölçüt** WorkersAvailable |
| **Çalışmasını** 8 ' den az |**Çalışmasını** 3 ' ten az |
| **Sürenin** 20 dakika |**Sürenin** 30 dakika |
| **Zaman toplama:** Average |**Zaman toplama:** Average |
| **Ön** Sayıyı 8 olarak artır |**Ön** Sayıyı 3 olarak artır |
| **Seyrek Erişimli (dakika):** 180 |**Seyrek Erişimli (dakika):** 180 |
|  | |
| **Otomatik ölçeklendirme kuralı (ölçek azaltma)** |**Otomatik ölçeklendirme kuralı (ölçek azaltma)** |
| **Kaynak** Çalışan havuzu 1 |**Kaynak** Çalışan havuzu 1 |
| **Ölçüt** WorkersAvailable |**Ölçüt** WorkersAvailable |
| **Çalışmasını** 8 ' den büyük |**Çalışmasını** 3 ' ten büyük |
| **Sürenin** 20 dakika |**Sürenin** 15 dakika |
| **Zaman toplama:** Average |**Zaman toplama:** Average |
| **Ön** Sayıyı 2 ' ye kadar azalt |**Ön** Sayıyı 3 olarak azalt |
| **Seyrek Erişimli (dakika):** 120 |**Seyrek Erişimli (dakika):** 120 |

Profilde tanımlanan hedef aralığı, App Service planı + arabelleği için profilde tanımlanan minimum örneklerle hesaplanır.

En büyük Aralık, çalışan havuzunda barındırılan tüm App Service planlarına ait tüm maksimum aralıkların toplamıdır.

Ölçek Genişletme kuralları için artış sayısı en az 1X, ölçek artırma için App Service plan enflasyon oranına ayarlanmalıdır.

Azaltma sayısı, ölçek azaltma için App Service planı enflasyon oranını 1/2X veya 1X arasında bir şeye göre ayarlanabilir.

### <a name="autoscale-for-front-end-pool"></a>Ön uç havuzu için otomatik ölçeklendirme
Ön uç otomatik ölçeklendirme kuralları, çalışan havuzlarından daha basittir. Öncelikle,  
ölçüm süresinin ve coolazaltma zamanlayıcıları App Service planındaki Ölçek işlemlerinin anlık olmadığından emin olun.

Bu senaryoda, Filiz, ön uçlar% 80 CPU kullanımına ulaştığında hata oranının arttığı ve örnekleri aşağıdaki gibi artıracak şekilde otomatik ölçeklendirme kuralını ayarlıyor:

![Ön uç havuzu için otomatik ölçeklendirme ayarları.][Front-End-Scale]

| **Otomatik ölçeklendirme profili – ön uçlar** |
| --- |
| **Ada** Otomatik ölçeklendirme – ön uçlar |
| **Ölçek ölçütü:** Zamanlama ve performans kuralları |
| **Profilinizi** Her gün |
| **Türüyle** Yinelenme |
| **Hedef Aralık:** 3 ila 10 örnek |
| **Miş** Her gün |
| **Başlangıç Zamanı:** 9:00 |
| **Saat dilimi:** UTC-08 |
|  |
| **Otomatik ölçeklendirme kuralı (ölçek artırma)** |
| **Kaynak** Ön uç havuzu |
| **Ölçüt** 'SUNA |
| **Çalışmasını** % 60 ' den büyük |
| **Sürenin** 20 dakika |
| **Zaman toplama:** Average |
| **Ön** Sayıyı 3 olarak artır |
| **Seyrek Erişimli (dakika):** 120 |
|  |
| **Otomatik ölçeklendirme kuralı (ölçek azaltma)** |
| **Kaynak** Çalışan havuzu 1 |
| **Ölçüt** 'SUNA |
| **Çalışmasını** % 30 ' dan az |
| **Sürenin** 20 dakika |
| **Zaman toplama:** Average |
| **Ön** Sayıyı 3 olarak azalt |
| **Seyrek Erişimli (dakika):** 120 |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png
