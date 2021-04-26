---
title: Uzamsal analiz kamera yerleşimi
titleSuffix: Azure Cognitive Services
description: Bir kamerayı uzamsal analiz ile kullanmak üzere ayarlamayı öğrenin
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: d58e4303078733eb1014171271d27907ff7a95b0
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286275"
---
# <a name="camera-placement-guide"></a>Kamera Yerleştirme Kılavuzu

Bu makale, uzamsal analiz (Genel Önizleme) için kamera yerleştirme önerileri sağlar. Bu, genel yönergelerin yanı sıra, dahil edilen tüm işlemler için yükseklik, açı ve kameraya odakla noktaya-uzaklık için özel öneriler içerir. 

> [!NOTE]
> Bu kılavuz, eksen M3045-V kamerası için tasarlanmıştır. Bu kamera, çözüm 1920x1080, 106 derece yatay alan görüntüleme, 59 derece dikey alanın görünümü ve bir sabit 2,8 mm odak uzunluğu kullanacaktır. Aşağıdaki ilkeler tüm kameralar için geçerlidir, ancak kamera yüksekliğinin ve kameraya odaklılığın bulunduğu belirli yönergelerin diğer kameralar ile kullanılmak üzere ayarlanması gerekir. 

## <a name="general-guidelines"></a>Genel yönergeler

Uzamsal analizler için kameraları konumlandırırken aşağıdaki genel yönergeleri göz önünde bulundurun:

* **Aydınlatma yüksekliği.** Kameraları, kameraları engellememeleri için aydınlatma armatürleri altına yerleştirin.
* **Engel.** Kamera görünümlerinin engellenmesini önlemek için, Poles, tifge, Rafa kaldırma, duvarlar ve mevcut LP kameralar gibi engelleri göz önünde atın.
* **Çevresel geri aydınlatma.** Dış mekan geri aydınlatma, kamera görüntü kalitesini etkiler. Ciddi geri aydınlatma koşullarından kaçınmak için, kameraları dışarıdan yönelik Windows ve cam kapılara yönlendirmaktan kaçının.
* **Yerel gizlilik kuralları ve düzenlemeleri.** Yerel düzenlemeler, kameraların yakalayabileceğini kısıtlayabilir. Kameraları yerleştirmeden önce yerel kuralları ve düzenlemeleri anladığınızdan emin olun.
* **Yapı yapısı.** HVAC, sprınkler ve mevcut kablolama, kameraların sabit bağlamasını sınırlayabilir.
* **Kablo yönetimi.** Planlı kamera bağlama konumlarından bir Ethernet kablosunu Internet üzerinden güç (PoE) anahtarına yönlendirdiğinizden emin olun.

## <a name="height-focal-point-distance-and-angle"></a>Yükseklik, odak noktası uzaklığı ve açı

Uzamsal analize yönelik bir kamerayı nasıl yükleyeceğinize karar verirken üç şeyi göz önünde bulundurmanız gerekir:
- Kamera yüksekliği
- Kamera-odak noktası uzaklığı
- Kameranın taban düzlemi ile ilgili açısı

Mümkün olduğunda, insanların büyük bölümünün (kişi tarafından yürüme yönü) bir kamera alanıyla bağlantılı olarak ele aldığı yönü bilmemiz de önemlidir. Bu yön, sistem performansı için önemlidir.

![Bir yönde yürüyen bir kişinin görüntüsü](./media/spatial-analysis/person-walking-direction.png)

Aşağıdaki çizimde, kişi yürüme yönü için yükseltme görünümü gösterilmektedir.

![Yükseltme ve plan görünümü](./media/spatial-analysis/person-walking-direction-diagram.png)

## <a name="camera-height"></a>Kamera yüksekliği

Genellikle, kameraların temelden 12-14 fit 'e bağlanması gerekir. Yüz maskesini algılama için, kameraların temelden 8-12 metreye bağlanmasını öneririz. Kameranın bu aralıkta bağlanmasını planlarken, kamera görünümünü etkileyebilecek engelleri (örneğin, Rafa kaldırma, asılı ışıklar, asılı imza ve ekran) göz önünde bulundurun ve sonra yüksekliği gerektiği gibi ayarlayabilirsiniz.

## <a name="camera-to-focal-point-distance"></a>Kamera-odak noktası uzaklığı

_Kamera-odak noktası uzaklığı_ , odak noktasından (veya kamera resminin merkezinden), zemin üzerinde ölçülen kameraya doğrusal uzaklıktan oluşur.

![Kamera-odak noktası uzaklığı](./media/spatial-analysis/camera-focal-point.png)

Bu uzaklık, taban düzlemi üzerinde ölçülür.

![Kameranın odak noktası uzaklığı tabandan nasıl ölçülür](./media/spatial-analysis/camera-focal-point-floor-plane.png)

Yukarıdaki, şu şekilde görünür:

![Kameradan odak noktası uzaklığı, yukarıdakilerden nasıl ölçülür](./media/spatial-analysis/camera-focal-point-above.png)

Kameranın, belirli bağlama yükseklikleri temelinde odak noktasından uzaklığını öğrenmek için aşağıdaki tabloyu kullanın. Bu uzaklıklar en iyi yerleştirme içindir. Tablo, bazı ceiller yüksekliği sınırlayabileceğinizden bu yana 12 '-14 ' önerinin altında rehberlik sağladığını unutmayın. Yüz maskesi algılama için önerilen kamera-odak noktası uzaklığı (min/max), 8 ' ila 12 ' arasında kamera yüksekliği için 4 '-10 ' ' dur.

| Kamera yüksekliği | Kamera-odak noktası uzaklığı (min/max) |  
| ------------- | ---------------------------------------- |  
| 240            | 4.6 '-8 '                                  |  
| (           | 5.8 '-10 '                                 |  
| +           | 7 '-12 '                                   |  
| May           | 8 '-14 ' '                                  |  
| k           | 9.2 '-16 '                                 |  
| 2.0           | 11,5 lik '-20 '                                |  

Aşağıdaki çizim, kamera görünümlerinin en yakın ve en uzak kameradan odak noktası uzaklıklarından benzetimini yapar.

| Renkle                                      | Soldan                                      |  
| -------------------------------------------- | --------------------------------------------- |  
| ![En yakın kameradan odak noktası uzaklığı](./media/spatial-analysis/focal-point-closest.png) | ![En uzak kamera-odak noktası uzaklığı](./media/spatial-analysis/focal-point-farthest.png) |  

## <a name="camera-angle-mounting-ranges"></a>Kamera açısı bağlama aralıkları

Bu bölümde, kabul edilebilir kamera açısı bağlama aralıkları açıklanmaktadır. Bu bağlama aralıkları, en iyi yerleştirme için kabul edilebilir aralığı gösterir.

### <a name="line-configuration"></a>Satır yapılandırma

**Biliveservices. Vision. spatialanalysis-personcrossingline** işlemi için +/-5 °, doğruluğu en üst düzeye çıkarmak için en uygun kamera bağlama açıdır.

Yüz maskesi algılama için +/-30 derece, kamera yüksekliği için 8 ' ila 12 ' arasındaki en iyi kamera bağlama açısıdır.

Aşağıdaki çizim, bir kapı için giriş sayımını yapmak üzere **biliveservices. Vision. spatialanalysis-personcrossingline** 'ı kullanmak için en soldaki (-) ve en sağdaki (+) bağlama açısı önerilerini kullanarak kamera görünümlerinin benzetimini yapar.

| En soldaki görünüm                | En sağdaki görünüm                |  
| ---------------------------- | ----------------------------- |  
| ![En soldaki kamera açısı](./media/spatial-analysis/camera-angle-left.png) | ![En sağdaki kamera açısı](./media/spatial-analysis/camera-angle-right.png) |  

Aşağıdaki çizimde, bir kuşbakışı görünümünden kamera yerleştirme ve bağlama açıları gösterilmektedir.

![Kuş bakışı görünüm](./media/spatial-analysis/camera-angle-top.png)

### <a name="zone-configuration"></a>Bölge yapılandırması

Kapsanan alanın yeterince büyük olduğunu güvence altına almak için kameraları 10 metreye veya daha fazla yere yerleştirmenizi öneririz. 

Bölge, bir duvar veya raf gibi bir engelin yanında olduğunda, kameraları aşağıdaki çizimde gösterildiği gibi kabul edilebilir 120 derece açılı Aralık içindeki hedeften belirtilen mesafede bağlayın.

![Kabul edilebilir kamera açısı](./media/spatial-analysis/camera-angle-acceptable.png)

Aşağıdaki çizimde, bir rafa ait bir alanın sol ve sağ kamera görünümlerine yönelik benzetimler sağlanmıştır.

| Sol görünüm        | Sağ görünüm        |  
| ---------------- | ----------------- |  
| ![Sol görünüm](./media/spatial-analysis/end-cap-left.png) | ![Sağ görünüm](./media/spatial-analysis/end-cap-right.png) |  

#### <a name="queues"></a>Kuyruklar

Biliveservices. **Vision. spatialanalysis-PersonCount**, **biliveservices. Vision. spatialanalysis-persondistance** ve **biliveservices. Vision. spatialanalysis-personcrossingçokgen** becerileri, kuyrukları izlemek için kullanılabilir. En iyi kuyruk veri kalitesi için, sıradaki kişilerin kullanımını en aza indirmek ve kuyruklar konumunun zaman içinde tutarlı olduğundan emin olmak için Retractable bant engelleri tercih edilir.

![Retractable bant kuyruğu](./media/spatial-analysis/retractable-belt-queue.png)

Bu tür bir engel, sistemin öngörülerinin doğruluğunu en üst düzeye çıkarmak için sıra için donuk engelleri üzerinde tercih edilir.

İki tür kuyruk vardır: doğrusal ve Zig-Zak.

Aşağıdaki çizim, doğrusal kuyrukların önerilerini gösterir:

![Doğrusal sıra önerisi](./media/spatial-analysis/camera-angle-linear-queue.png)

Aşağıdaki çizimde, doğrusal kuyrukların sol ve sağ kamera görünümlerine yönelik benzetimler sağlanmıştır. Kamerayı sıranın karşısındaki tarafına bağlayabileceğinizi unutmayın.

| Sol görünüm                          | Sağ görünüm                          |  
| ---------------------------------- | ----------------------------------- |  
| ![Doğrusal sıra için sol açı](./media/spatial-analysis/camera-angle-linear-left.png) | ![Doğrusal sıra için sağ açı](./media/spatial-analysis/camera-angle-linear-right.png) |  

Zig-Zak kuyruklar için, aşağıdaki çizimde gösterildiği gibi, kamerayı doğrudan sıra çizgisi yönüne doğru bir şekilde yerleştirmekten kaçınmak en iyisidir. Çizimdeki dört örnek kamera konumlarının her birinde, her yönde +/-15 derecenin kabul edilebilir bir sapmasıyla ideal bir görünüm sağlar.

Aşağıdaki çizimler, bir Zig-Zak kuyruğu için ideal konumlara yerleştirilmiş bir kameradan görünümün benzetimini yapar.

| 1. görünüm        | Görünüm 2        |  
| ------------- | ------------- |  
| ![1. görünüm](./media/spatial-analysis/camera-angle-ideal-location-right.png) | ![Görünüm 2](./media/spatial-analysis/camera-angle-ideal-location-left.png) |  

| 3. görünüm |  4. görünüm |  
| ---- | ----  |
| ![3. görünüm](./media/spatial-analysis/camera-angle-ideal-location-back.png) |  ![4. görünüm](./media/spatial-analysis/camera-angle-ideal-location-back-left.png) | 

##### <a name="organic-queues"></a>Organik kuyruklar

Organik kuyruk satırları formu genel olarak. Bu sıra stili, kuyruklar 2-3 kişiden fazla ve bölge tanımı içindeki satır formlarından daha fazla oluşturmuyor. Sıra uzunluğu genellikle 2-3 kişiden daha büyükse, sıra yönüne kılavuzluk etmek ve bölge tanımı içinde satır formlarınızı sağlamak için bir Retractable bandı kullanmanızı öneririz.

## <a name="next-steps"></a>Sonraki adımlar

* [Web uygulaması sayımını yapan bir kişi dağıtın](spatial-analysis-web-app.md)
* [Uzamsal analiz işlemlerini yapılandırma](./spatial-analysis-operations.md)
* [Günlüğe kaydetme ve sorun giderme](spatial-analysis-logging.md)
* [Bölge ve satır yerleştirme Kılavuzu](spatial-analysis-zone-line-placement.md)
