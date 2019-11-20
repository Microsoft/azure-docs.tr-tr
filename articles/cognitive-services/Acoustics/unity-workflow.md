---
title: Project Acoustics Unity tasarım öğreticisi
titlesuffix: Azure Cognitive Services
description: Bu öğretici, Unity 'de Project Acoustics için tasarım iş akışını açıklar.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fd00e4105ce4edae9d014df2a83c5ae3aaf778da
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854272"
---
# <a name="project-acoustics-unity-design-tutorial"></a>Project Acoustics Unity tasarım öğreticisi
Bu öğreticide, Unity 'de Project Acoustics için tasarım araçları ve iş akışı açıklanmaktadır.

Önkoşullar:
* Windows için Unity 2018.2 +
* Bakmış Acoustics varlığı içeren bir Unity sahneyi

Bu öğreticide, bakmış Acoustics varlığına sahip bir Unity sahneyi iki şekilde edinebilirsiniz:
* [Unity projenize proje Acoustics ekleyin](unity-integration.md), ardından [Azure Batch bir hesap alın](create-azure-account.md)ve ardından [Unity sahnüslerinizi](unity-baking.md) ekleyin
* Veya [Project Acoustics Unity örnek içeriğini](unity-quickstart.md)kullanın.

## <a name="review-design-process-concepts"></a>Tasarım süreci kavramlarını gözden geçirin
Project Acoustics, kaynaklarınızı işlemek için ortak ses dijital sinyal işleme (DSP) yöntemlerini kullanır ve occlusiyon, Wet/kuru karışımı ve yankı kuyruğu uzunluğu (RT60) dahil tanıdık Acoustics özellikleri üzerinde denetim elde etmenizi sağlar. Ancak, temel [Proje Acoustics tasarım süreci kavramı](design-process.md) , bu özellikleri doğrudan ayarlamak yerine, bu özellikleri işlemek için benzetim sonuçlarının nasıl kullanıldığını kontrol edersiniz. Her denetim için varsayılan ayarlar fiziksel olarak doğru Acoustics temsil eder.

## <a name="design-acoustics-for-each-source"></a>Her kaynak için Acoustics Tasarla
Project Acoustics, bir dizi kaynağa özgü Acoustics tasarım denetimi sağlar. Bu, bazı kaynakları vurgulayarak ve diğerlerini kaldırarak sahnedeki karışımı denetlemenize olanak tanır.

### <a name="adjust-distance-based-attenuation"></a>Uzaklık tabanlı zayıflamayı ayarla
**Project Acoustics** Unity spatializer eklentisi tarafından sunulan ses DSP 'Si, Unity düzenleyicisinde yerleşik olarak bulunan ve kaynak başına uzaklık tabanlı zayıflatılaşmaya uyar. Uzaklık tabanlı zayıflalama denetimleri, **3B ses ayarları**altında, ses kaynaklarının **Inspector** panelinde bulunan **ses kaynağı** bileşenidir:

![Unity uzaklık zayıflalama seçenekleri panelinin ekran görüntüsü](media/distance-attenuation.png)

Acoustics, oynatıcı konum etrafında ortalanmış bir "simülasyon bölgesi" kutusunda hesaplama gerçekleştirir. Bir ses kaynağı, bu benzetim bölgesinin dışında bulunan Player 'dan uzak bir biçimde olursa, yalnızca kutudaki geometri, occluders oyuncunun oynatıcının yakın olduğu durumlarda makul bir şekilde çalışır. Ancak, oynatıcı açık alanda olduğunda ancak occlular, uzak ses kaynağına yakın olduğunda, ses, gerçekçi bir şekilde parçalanabilir. Önerilen geçici çözümümüzü, sesin, Player 'ın varsayılan yatay mesafesi olan 45. yaklaşık olarak 1 ' e kadar kapanmasına dikkat edin.

![Unity SpeakerMode seçenek bölmesinin ekran görüntüsü](media/speaker-mode.png)

### <a name="adjust-occlusion-and-transmission"></a>Occlusiyon ve iletimi ayarla
**AcousticsAdjust** betiğini bir kaynağa eklemek, bu kaynak için ayarlama parametrelerinin ayarlanmasını sağlar. Betiği eklemek için **Inspector** panelinin alt kısmındaki **Bileşen Ekle** ' ye tıklayın ve **> Acoustics ayarla**' ya gidin. Betik altı denetime sahiptir:

![Unity AcousticsAdjust betiğinin ekran görüntüsü](media/acoustics-adjust.png)

* **Enable Acoustics** -bu kaynağa Acoustics uygulanıp uygulanmadığını denetler. İşaretlenmediğinde, kaynak HRTFs veya kaydırma ile maça üzerinde kalır, ancak Acoustics olmaz. Bu, düşüşe, occlusiyon veya Level ve Decay gibi dinamik geri alma parametreleri anlamına gelir. Ters geri alma, hala sabit bir düzey ve Decay zaman ile uygulanmış.
* **Occlusiyon** -Acoustics sistemi tarafından hesaplanan Occlusiyon DB düzeyine bir çarpan uygular. Bu çarpan 1 ' den büyükse, occlusiyon, 1 ' den küçük değerler, occluson efektini daha hafif hale getirir ve 0 değeri occlusiyon 'ı devre dışı bırakır.
* **İletim (DB)** -geometri aracılığıyla aktarımdan kaynaklanan zayıflamayı (DB 'de) ayarlayın. İletimi devre dışı bırakmak için bu kaydırıcıyı en düşük düzeyine ayarlayın. Acoustics maça, sahne geometrisi (portaling) çevresinde ulaşan ilk kuru sesi gösterir. İletim, görüş satırı yönünde istenmeyen bir ek kurutma sağlar. Kaynak için olan uzaklık zayıflalama eğrisinin de uygulandığını unutmayın.

### <a name="adjust-reverberation"></a>Ters ayarlamayı ayarla
* **Wettik (DB)** -kaynaktaki mesafeye göre, DB 'de ters b gücünü ayarlar. Pozitif değerler daha ters bir ses yapar, negatif değerler ise ses daha fazla yapılır. Eğri düzenleyicisini açmak için eğri denetimine (yeşil çizgi) tıklayın. İstediğiniz işlevi oluşturmak için noktaları eklemek ve bu noktaları sürüklemek için sağ tıklayıp eğriyi değiştirin. X ekseni kaynağından uzaklığı ve y ekseni, dB 'de ters bir ayarlamadır. Eğrileri düzenlemeyle ilgili daha fazla bilgi için bkz. bu [Unity el kitabı](https://docs.unity3d.com/Manual/EditingCurves.html). Eğriyi varsayılana varsayılanına sıfırlamak için, **Wetlik** 'e sağ tıklayın ve **Sıfırla**' yı seçin.
* **Zaman ölçeğini** azaltma-zaman ölçeği için bir çarpanı ayarlar. Örneğin, hazırlama sonucu 750 milisaniyelik bir süre belirtiyorsa, ancak bu değer 1,5 olarak ayarlanırsa, kaynağa uygulanan Decay süresi 1.125 milisaniyedir.
* **Outdoorness** -Acoustics sisteminin bir kaynaktaki geri alma işlemlerinin ne kadar "dışarıda bırakma" tahminine yönelik tahmine sahip bir toplama ayarlaması. Bu değer 1 olarak ayarlandığında, kaynak her zaman tamamen dışarıda bırakılır ve-1 olarak ayarlandığında, kaynak ses tamamen kapılara göre yapılır.

**AcousticsAdjustExperimental** betiğini bir kaynağa eklemek, bu kaynak için ek deneysel ayarlama parametreleri sağlar. Betiği eklemek için **Inspector** panelinin alt kısmındaki **Bileşen Ekle** ' ye tıklayın ve **betikler > Acoustics ayarla**' ya gidin. Şu anda bir deneysel denetim var:

![Unity AcousticsAdjustExperimental betiğinin ekran görüntüsü](media/acoustics-adjust-experimental.png)

* **Algısal uzaklık warp** -kurutma oranını hesaplamak için kullanılan uzaklığa üstel bir çarpıtma uygular. Acoustics sistemi alanın tamamında, mesafede sorunsuzca farklılık gösteren ve algısal uzaklık ipuçları sağlayan ıslak düzeylerini hesaplar. Bu etkiyi, 1 ' den büyük olan değerlerin çarpılmasından sonra, mesafeden fazla geri alma düzeyini artırarak, "uzak" sesi yaparak bu etkiyi ortadan kaldırma. 1 ' den küçük değerleri çarpıtma, uzaklık tabanlı geri alma değişikliğini daha hafif hale getirerek sesi daha da "var" hale getirir.

## <a name="design-acoustics-for-all-sources"></a>Tüm kaynaklar için Acoustics Tasarla
Tüm kaynakların parametrelerini ayarlamak için Unity 'nin **Ses karıştırıcı**' da kanal şeridine tıklayın ve **Proje Acoustics karıştırıcı** efektinin parametrelerini ayarlayın.

![Project Acoustics Unity karıştırıcı özelleştirme bölmesinin ekran görüntüsü](media/mixer-parameters.png)

* **Wettik ayarı** -kaynak dinleyicisi uzaklığına bağlı olarak sahne içindeki tüm kaynaklarda, DB 'deki ters b gücünü ayarlar. Pozitif değerler daha ters bir ses yapar, negatif değerler ise ses daha fazla yapılır.
* **RT60 Scale** -Reverb süresi için ölçek skalar skaler.
* **Yatay kaydırma kullan** -sesin Binaural (0) veya çok kanallı kaydırma (1) olarak çıktı olup olmadığını denetler. 1 dışındaki tüm değerler Binaural gösterir. Binaural çıkışı, Htfs ile kullanım için ve çok kanallı bir surround konuşmacı sistemleriyle kullanım için VBAP ile birlikte istenmeyen çıkış ile birlikte kullanıma yönelik olarak tasarlanmıştır. Çok kanallı Panner kullanıyorsanız, **proje ayarları** > **sesi**altında bulunan cihaz ayarlarınızla eşleşen hoparlör modunu seçtiğinizden emin olun.

## <a name="check-proper-sound-source-placement"></a>Doğru ses kaynağı yerleştirmesini denetle
Dolu voxkine yerleştirilmiş ses kaynakları, akustik işleme almaz. VOX, görünür sahne geometrisini genişlettiğinden, Visual Geometry tarafından bir kaynağı Voxel içine yerleştirmek mümkündür. **Sahne** görünümü 'nin sağ üst köşesindeki **şeyler** menüsündeki Voxel Kılavuzu onay kutusunu değiştirerek Project Acoustics voxx 'i görüntüleyebilirsiniz.

![Unity şeyler menüsünün ekran görüntüsü](media/gizmos-menu.png)  

Voxel ekran, oyunun görsel bileşenlerinin bunlara uygulanmış bir dönüşüm olup olmadığını belirlemenize de yardımcı olabilir. Bu durumda, **Acoustics Manager**'ı barındıran gameobject 'e aynı dönüşümü uygulayın.

### <a name="bake-time-vs-run-time-voxels"></a>Bake saati ile çalışma zamanı voxki
Oyun tasarım zamanında ve çalışma zamanında oyun penceresinde, düzenleyici penceresinde voxkleri görüntülemek mümkündür. Bu görünümlerde voxboyutlarının boyutu farklıdır. Bunun nedeni, Acoustics çalışma zamanının, daha yumuşak ilişkilendirme sonuçları için daha hassas Voxel kılavuz kullanması nedeniyle oluşur. Ses kaynağı yerleşimi, çalışma zamanı voxiksel kullanılarak doğrulanmalıdır.

Tasarım zamanı voxpiksel:

![Tasarım zamanı sırasında projenin Acoustics voxpiksel ekran görüntüsü](media/voxels-design-time.png)

Çalışma zamanı voxpiksel:

![Çalışma zamanı sırasında Acoustics VOX, projenin ekran görüntüsü](media/voxels-runtime.png)

## <a name="next-steps"></a>Sonraki adımlar
* [Tasarım sürecinin](design-process.md) arkasındaki kavramları vurgulayan örnek olay incelemelerini keşfet

