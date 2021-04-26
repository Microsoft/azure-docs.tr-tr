---
title: Sık sorulan sorular
description: Azure nesne bağlayıcıları hizmeti hakkında SSS.
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 04/01/2020
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: aebc1013dcead6c32dab55512ce915e25f60f94a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047584"
---
# <a name="frequently-asked-questions-about-azure-object-anchors"></a>Azure nesne bağlantıları hakkında sık sorulan sorular

Azure nesne bağlantıları, bir uygulamanın bir 3B model kullanarak fiziksel dünyadaki bir nesneyi algılamasını ve 6 DoF pozlarını tahmin etmesini sağlar.

Daha fazla bilgi için bkz. [Azure nesne tutturucularının genel bakış](overview.md).

## <a name="product-faq"></a>Ürün SSS
**S: kullanılması gereken nesneler için hangi önerileri kullanıyorsunuz?**

Y **:** Nesneler için aşağıdaki özellikleri öneririz:

* her boyut için 1-10 ölçü
* Geometri içinde yeterli çeşitliliğe sahip simetrik olmayan
* Parlak renk ile düşük reflectivity (mat yüzeyler)
* Sabit nesneler
* No veya az miktarda ifade
* Veya en az dağınıklığı olan arka planları temizle
* Taranan nesne, eğitilen modelle ilgili 1:1 ile eşleşmelidir

**S: model alma işlemi için işlenebilecekleri maksimum nesne boyutları nelerdir?**

Y **:** CAD modelinin her boyutu 10 metreden az olmalıdır.

**S: alma işlemi için işlenebilecekleri maksimum CAD model boyutu nedir?**

Y **:** Model dosyası boyutu 150 MB 'tan az olmalıdır.

**S: desteklenen CAD biçimleri nelerdir?**

Y **:** Şu anda `fbx` ,, `ply` , `obj` `glb` ve `gltf` dosya türlerini destekliyoruz.

**S: model alma hizmeti için gereken yerçekimi yönü ve birimi nedir? Bunları nasıl anlayabilir?**

Y **:** Yerçekimi yönü, dünyaya işaret eden aşağı vektörüdür. CAD modelleri için, graçekimi yönü genellikle bir yukarı yönünün tersidir. Örneğin, çoğu durumda + Z, bu durumda-Z veya `Vector3(0.0, 0.0, -1.0)` graçekimi yönünü temsil eder. Yerçekimi belirlerken, yalnızca modeli düşünmemelidir, ancak çalışma zamanı sırasında modelin görüleceği yönü de göz önünde bulundurmanız gerekir. Gerçek dünyada bir sandalyesi basit bir yüzey üzerinde algılamaya çalışıyorsanız, yerçekimi olabilir `Vector3(0.0, 0.0, -1.0)` . Ancak sandalyesi 45 derecelik bir eğim içeriyorsa, yerçekimi olabilir `Vector3(0.0, -Sqrt(2)/2, -Sqrt(2)/2)` .

Yerçekimi yönü, [MeshLab](http://www.meshlab.net/)gibi bir 3B işleme aracıyla karşı bir sorun olabilir.

Birim, modelin ölçüm birimini temsil eder. Desteklenen birimler **Microsoft. Azure. ObjectAnchors. ınestion. Unit** numaralandırması kullanılarak bulunabilir.

**S: bir CAD modelinin alınması ne kadar sürer?**

Y **:** Bir `ply` model için genellikle 3-15 dakikadır. Modelleri başka biçimlerde gönderiyorsanız, dosya boyutuna bağlı olarak 15-60 dakika beklemeniz beklenir.

**S: nesne tutturucular hangi cihazları destekler?**

Y **:** HoloLens 2 

**S: HoloLens hangi işletim sistemi derlemesini çalıştırmalıyım?**

Y **:** 12 Mart 2020 ' den sonra yayınlanan OS derlemesi 18363,720 veya daha yeni bir sürüm.

  [Windows 10 ' da 12 mart 2020 güncelleştirmesi](https://support.microsoft.com/help/4551762)hakkında daha fazla bilgi.

**S: HoloLens üzerinde bir nesneyi algılamak ne kadar sürer?**

Y **:** Nesne boyutuna ve tarama işlemine bağlıdır. Daha hızlı algılamayı sağlamak için, tam tarama için en iyi yöntemleri takip edin. Her boyuttaki 2 Ölçüm içindeki daha küçük nesneler için algılama birkaç saniye içinde gerçekleşebilir. Bir otomobil gibi daha büyük nesneler için, kullanıcı güvenilir bir algılama sağlamak üzere nesnenin etrafında tam bir döngüye yol açar, bu da algılama işleminin onlarca saniye sürebileceği anlamına gelir.

**S: bir HoloLens uygulamasında nesne bağlayıcıları kullanırken en iyi uygulamalar nelerdir?**

**A**

 1. Doğru işleme almak için göz ayarlama işlemi gerçekleştirin.
 2. Odanın zengin görsel dokusunu ve iyi aydınlatmaya sahip olduğundan emin olun.
 3. Mümkünse nesneyi sabit tutun.
 4. İsteğe bağlı olarak, HoloLens cihazınızda [uzamsal eşleme](/windows/mixed-reality/spatial-mapping) önbelleğini temizleyin.
 5. Nesneyi inceleyerek nesneyi tarayın. Nesnenin çoğunun gözlemlendiği emin olun.
 6. Bir arama alanını nesneyi kapsayacak yeterince büyük olarak ayarlayın.
 7. Nesne, algılama sırasında sabit kalmalıdır.
 8. Nesne algılamayı başlatın ve tahmini poz temelinde işlemeyi görselleştirin.
 9. İzleme kararlı olduktan ve pil ömrünü korumak için doğru olduğunda, algılanan nesneyi kilitle veya izlemeyi durdur.

**S: tahmini bir poz ne kadar doğru?**

Y **:** Nesne boyutuna, malzemeye, ortama vb. göre değişir. Küçük nesneler için tahmini poz 2 cm hata içinde olabilir. Bir otomobil gibi büyük nesneler için hata 2-8 cm 'ye kadar olabilir.

**S: nesne bağlantıları nesneleri taşımayı mi işleyebilir?**

Y **:** **Sürekli olarak taşınan** veya **dinamik** nesneleri desteklemiyoruz.

**S: Nesne tutturucuları veya ifade işleme mi?**

Y **:** Kısmen, ne kadar nesne şekli veya geometri değişikliğine göre değişiklik veya ifade nedeniyle değişir. Nesnenin geometrisi bir lotu değiştirirse, Kullanıcı bu yapılandırma için başka bir model oluşturabilir ve bunu algılama için kullanabilir.

**S: aynı anda kaç farklı nesne, nesne tutturucularını tespit edebilir?**

Y **:** Aynı anda tek bir nesne modelini algılamayı destekliyoruz. 

**S: nesne bağlantıları aynı nesne modelinin birden çok örneğini tespit edebilir mi?**

Y **:** Evet, aynı model türünde en fazla 3 nesne tespit edebilirsiniz. Uygulama, `ObjectObserver.DetectAsync` aynı modelin birden çok örneğini algılamak için farklı sorgularla birden çok kez çağrı yapabilir.

**S: nesne tutturma çalışma zamanı nesnemi algılayamazsa ne yapmam gerekir?**

**A**

* Birkaç posterleri ekleyerek odanın yeterli dokulara sahip olduğundan emin olun.
* Nesneyi daha tamamen tarayın.
* Model parametrelerini yukarıda açıklanan şekilde ayarlayın.
* Nesnenin tamamını veya çoğunu içeren arama alanı olarak sıkı bir sınırlayıcı kutusu sağlayın.
* Uzamsal eşleme önbelleğini temizleyin ve nesneyi yeniden tarayın.
* Tanılamayı yakalayın ve verileri bize gönderin.

**S: nesne sorgu parametrelerini seçme**

**A**

* Algılama hızını ve doğruluğu artırmak için tam nesneyi ideal olarak kapsayan sıkı arama alanı sağlar.
* `ObjectQuery.MinSurfaceCoverage`Nesne modelinden varsayılan değer genellikle iyidir, aksi takdirde daha hızlı bir algılama sağlamak için daha küçük bir değer kullanın.
* `ObjectQuery.ExpectedMaxVerticalOrientationInDegrees`Nesnenin sağ tarafında olması bekleniyorsa küçük bir değer kullanın.
* Bir uygulama, algılama için her zaman bir `1:1` nesne modeli kullanmalıdır. Tahmini ölçek, 1% hata dahilinde ideal olmalıdır. Bir uygulama `ObjectQuery.MaxScaleChange` `0` , ölçek tahmine izin vermek veya `0.1` devre dışı bırakmak veya etkinleştirmek için veya örnek pozı değerlendirmek için qualitatively olarak ayarlanabilir.

**S: nesne al Nasıl yaparım?, HoloLens 'ten tanılama verilerini tutturur mi?**

Y **:** Uygulama, tanılama arşivleri 'nin konumunu belirtebilir. Nesne tutturucular örnek uygulaması, tanılamayı **Tempstate** klasörüne yazar.

**S: nesne bağlantıları Unity SDK 'Sı tarafından döndürülen poz kullanılırken kaynak modeli neden fiziksel nesneyle hizalanmıyor?**

Y **:** Unity, nesne modeli içeri aktarırken koordinat sistemi değiştirebilir. Örneğin, nesne tutturucular Unity SDK 'Sı, sağ taraftan sol taraf koordinat sistemine dönüştürürken Z eksenini tersine çevirir, ancak Unity, X veya Y ekseni hakkında ek bir döndürme uygulayabilir. Geliştirici, koordinat sistemlerini görselleştirerek ve karşılaştırarak bu ek döndürmeyi tespit edebilir.

**S: 2B destekliyor musunuz?**

Y **:** Geometri tabanlı olduğundan, yalnızca 3B ' i destekliyoruz.

**S: aynı modeli farklı renklerde ayırt edebilir misiniz?**

Y **:** Algoritmalarımız geometri tabanlıdır olduğundan, aynı modelin farklı renkleri algılama sırasında farklı davranmayacaktır.

**S: internet bağlantısı olmadan nesne tutturucularını kullanabilir miyim?**

**A** 
* Model alımı ve eğitimi için, bulutta gerçekleştiği için bağlantı gerekir.
* Çalışma zamanı oturumları tamamen cihazlardır ve HoloLens 2 ' de tüm hesaplamalar gerçekleşirken bağlantı gerektirmez.

## <a name="privacy-faq"></a>Gizlilik SSS
**S: Azure nesnesi verileri nasıl depolar?**

Y **:** Yalnızca Microsoft tarafından yönetilen bir veri şifreleme anahtarıyla, bekleyen olarak şifrelenen sistem meta verilerini depolarız.