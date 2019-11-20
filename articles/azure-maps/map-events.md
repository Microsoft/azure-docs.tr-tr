---
title: Azure haritalar ile olayları işleme | Microsoft Docs
description: Harita olayları ile etkileşimli Web SDK eşlemesi yapma
author: jingjing-z
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c5b4ed73d7bc4d89a67280a0bb183f374ae093d8
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899439"
---
# <a name="interact-with-the-map"></a>Harita ile etkileşim kurma

Bu makalede haritadaki olayları ve haritanın farklı katmanlarını vurgulamak için [harita sınıfı olayları](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events) özelliğinin nasıl kullanılacağı gösterilir. Ayrıca, bir HTML işaretleyicisi ile etkileşim kurarken olayları vurgulamak için harita sınıfı olayları özelliğini nasıl kullanacağınızı gösterir.

## <a name="interact-with-the-map"></a>Harita ile etkileşim kurma

Aşağıdaki haritada yürütün ve sağ tarafta vurgulanan ilgili fare olaylarına bakın. JavaScript kodunu görüntülemek ve düzenlemek için **js sekmesine** tıklayabilirsiniz. Ayrıca, **birlikte Düzenle** ' yi tıklatın ve kodu CodePen ' da düzenleyebilirsiniz.

<br/>

<iframe height='600' scrolling='no' title='Harita ile etkileşim kurma – fare olayları' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bkz. kalemin <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>harita – fare olayları ile</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile <a href='https://codepen.io'>codepen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Harita katmanları ile etkileşim kurma

Aşağıdaki kod, sembol katmanıyla etkileşime geçerek harekete geçen olayların adını vurgular. Sembol, kabarcık, çizgi ve çokgen katmanı, hepsi aynı olay kümesini destekler. Isı haritası ve döşeme katmanları bu olayların hiçbirini desteklemez.

<br/>

<iframe height='600' scrolling='no' title='Harita-katman olayları ile etkileşim kurma' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>'da <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>harita-katman olayları</a> (Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile etkileşim kurma kalemine bakın.
</iframe>

## <a name="interact-with-html-marker"></a>HTML Işaretleyicisi ile etkileşim kurma

Aşağıdaki kod, bir HTML işaretine JavaScript harita olayları ekler. Ayrıca, HTML işaretleyicisi ile etkileşimde bulunana kadar harekete geçen olayların adını vurgular.

<br/>

<iframe height='500' scrolling='no' title='Map-HTML Işaretleyici olayları ile etkileşim kurma' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>üzerinde Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>harita-HTML işaretçisi olayları ile etkileşime</a> geçen kaleme bakın.
</iframe>

Aşağıdaki tabloda desteklenen tüm harita sınıfı olayları listelenmektedir.

| Olay             | Açıklama |
|-------------------|-------------|
| boxzoomend        | Bir "Box Zoom" etkileşimi sona erdiğinde tetiklenir.|
| boxzoomstart      | Bir "Box Zoom" etkileşimi başladığında tetiklenir.|
| Tıklat             | Bir işaret cihazına basıldığında ve haritadaki aynı noktada serbest bırakıldığında harekete geçirilir.|
| kapat             | Popup el ile veya program aracılığıyla kapatıldığında harekete geçirilir.|
| olayını       | Farenin sağ düğmesine tıklandığında harekete geçirilir.|
| veri eklendi         | Veri kaynağına şekiller eklendiğinde harekete geçirildi.|
| dataretaşındı       | Veri kaynağından şekiller kaldırıldığında harekete geçirildi.|
| datasourceupdated | DataSource nesnesi güncelleştirildikten sonra harekete geçirilir.|
| DblClick          | Bir işaret aygıtı, haritada aynı noktada iki kez tıklandığında harekete geçirilir.|
| Sürükleyin              | Harita, açılan menü veya HTML işaretçisi üzerinde "kaydırmak için sürükleyin" etkileşimi sırasında sürekli olarak harekete geçirildi.|
| DragEnd           | Harita, açılan menü veya HTML işaretçisi üzerinde "kaydırmak için sürükle" etkileşimi sona erdiğinde tetiklenir.|
| dragstart         | Harita, açılan menü veya HTML işaretçisi üzerinde "Pan 'a sürükleyin" etkileşimi başladığında tetiklenir.|
| hata             | Bir hata oluştuğunda harekete geçirildi.|
| KeyDown           | Bir tuşa basıldığında harekete geçirilir.|
| KeyPress          | Bir tyrilebilir karakter (ANSI anahtarı) üreten bir anahtar basıldığında tetiklenir.|
| KeyUp             | Bir anahtar serbest bırakıldığında harekete geçirilir.|
| layeradded        | Haritaya bir katman eklendiğinde harekete geçirildi.|
| load              | Tüm gerekli kaynaklar indirildikten ve haritanın ilk görsel olarak işlenmesi tamamlandıktan hemen sonra harekete geçirilir.|
| MouseDown         | Harita içinde bir işaret cihazına basıldığında harekete geçirilir.|
| olayına         | Harita içinde bir işaret aygıtı taşındığında tetiklenir.|
| mouseOut          | Bir nokta aygıtı haritanın tuvalinden ayrıldığında harekete geçirilir.|
| gelme olayından         | Harita içinde bir işaret aygıtı taşındığında tetiklenir.|
| dan           | Harita içinde bir işaret aygıtı bırakıldığında harekete geçirilir.|
| Geçiş              | Bir görünümden diğerine animasyon eklenmiş bir geçiş sırasında, Kullanıcı etkileşimi ya da yöntemlerinin sonucu olarak tekrar tekrar tetiklenir.|
| MoveEnd           | Eşleme, Kullanıcı etkileşimi ya da yöntemlerinin sonucu olarak bir görünümden diğerine geçişi tamamladıktan hemen sonra harekete geçirilir.|
| MoveStart         | Eşleme, Kullanıcı etkileşimi ya da yöntemlerinin sonucu olarak bir görünümden diğerine geçiş yapmaya başlamadan hemen önce tetiklenir.|
| açık              | Açılan pencere el ile veya program aracılığıyla açıldığında harekete geçirilir.|
| perde             | Her iki kullanıcı etkileşimi ya da yönteminin sonucu olarak haritanın sıklığı (eğimi) değiştiğinde harekete geçirilir.|
| süchend          | Haritanın sıklığı (eğimi), Kullanıcı etkileşimi ya da yöntemlerinin sonucu olarak değiştirmeyi tamamladıktan hemen sonra harekete geçirilir.|
| akchstart        | Haritanın sıklığı (eğim) her iki kullanıcı etkileşimi veya yöntemlerinin sonucu olarak bir değişikliğe başladığında tetiklenir.|
| hazır             | Eşleme, bir program aracılığıyla ile etkileşim için hazırlanmadan önce en düşük gerekli eşleme kaynakları yüklendiğinde tetiklenir.|
| işlenecek            | <p> Şu sonucu olarak harita ekrana çizildiğinde tetiklenir:<ul><li>Haritanın konumunu, yakınlaştırmasını, perdesini veya yataya bir değişikliği.</li><li>Haritanın stilinde değişiklik.</li><li>Veri kaynağı kaynağı değişikliği.</li><li>Vektör kutucuğu, GeoJSON dosyası, glif veya sprite yüklemesi.</li></ul></p>|
| yeniden boyutlandırma            | Eşleme yeniden boyutlandırılırken hemen sonra harekete geçirildi.|
| Boyut            | "Döndürmek için sürükleyin" etkileşimi sırasında sürekli olarak harekete geçirildi.|
| rotateend         | "Döndürmek için sürükle" etkileşim uçlarını harekete geçirilir.|
| rotatestart       | "Döndürmek için sürükle" etkileşimi başladığında tetiklenir.|
| shapechanged      | Şekil nesnesi özelliği değiştirildiğinde harekete geçirilir.|
| sourceadded       | Haritaya bir DataSource veya VectorTileSource eklendiğinde harekete geçirilir.|
| sourceretaşındı     | Bir DataSource veya VectorTileSource, eşlemden kaldırıldığında tetiklenir.|
| styledata         | Haritanın stili yüklenirken veya değiştiğinde harekete geçirilir.|
| belirteç alındı     | AAD erişim belirteci edinildiği zaman tetiklenir.|
| touchcancel       | Eşleme içinde bir touchcancel olayı oluştuğunda harekete geçirildi.|
| touchend          | Eşleme içinde bir touchEnd olayı oluştuğunda harekete geçirildi.|
| touchmove         | Eşleme içinde bir TouchMove olayı oluştuğunda harekete geçirildi.|
| touchstart        | Eşleme içinde bir touchstart olayı oluştuğunda harekete geçirildi.|
| tekerleği             | Eşleme içinde fare tekerleği olayı oluştuğunda harekete geçirildi.|
| Yakınlaştırma              | Bir yakınlaştırma düzeyinden diğerine animasyon eklenmiş bir geçiş sırasında, Kullanıcı etkileşimi ya da yöntemlerinin sonucu olarak tekrar tekrar tetiklenir.|
| zoomend           | Eşleme, Kullanıcı etkileşimi ya da yöntemlerinin sonucu olarak, bir yakınlaştırma düzeyinden diğerine geçişi tamamladıktan hemen sonra harekete geçirilir.|
| zoomstart         | Eşleme, Kullanıcı etkileşimi ya da yöntemlerinin sonucu olarak bir yakınlaştırma düzeyinden diğerine geçiş yapmaya başlamadan hemen önce tetiklenir.|


## <a name="next-steps"></a>Sonraki adımlar

Tam kod örnekleri için aşağıdaki makalelere bakın:

> [!div class="nextstepaction"]
> [Azure haritalar Hizmetleri modülünü kullanma](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Kod örnekleri](https://docs.microsoft.com/samples/browse/?products=azure-maps)
