---
title: Harita araçları etkileşim türleri ve klavye kısayolları Microsoft Azure haritaları
description: Microsoft Azure Maps web SDK 'sında fare, dokunmatik ekran veya klavye kullanarak şekil çizme ve düzenleme
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: b609a21348222150faf5fb828f2cc38ca6658f5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895826"
---
# <a name="interaction-types-and-keyboard-shortcuts-in-the-drawing-tools-module"></a>Çizim araçları modülündeki etkileşim türleri ve klavye kısayolları

Bu makalede, fare, dokunmatik ekran veya klavye kısayollarını kullanarak şekil çizme ve düzenleme için kullanabileceğiniz farklı yollar özetlenmektedir.

Çizim Yöneticisi, şekil çizmek için harita ile etkileşimde bulunmanın üç farklı yolunu destekler.

* `click` -, Fare veya dokunma tıklandığında koordinatlar eklenir.
* `freehand ` -Koordinat, fare veya dokunma haritada sürüklendiğinde eklenir.
* `hybrid` -, Fare veya dokunma tıklandığında veya sürüklendiğinde koordinatlar eklenir.

## <a name="how-to-draw-shapes"></a>Şekil çizme

 Herhangi bir şekil çizilebilmeniz `drawingMode` için, çizim Yöneticisi seçeneğini desteklenen bir çizim ayarı olarak ayarlayın. Bu ayar, araç çubuğundaki çizim düğmelerinden birine basılarak programlanabilir veya çağrılabilir. Çizim modu, bir şekil çizildikten sonra bile etkin kalır, ancak aynı türde ek şekiller çizmeyi kolaylaştırır. Çizim modunu program aracılığıyla boşta durumuna ayarlayın. Ya da, araç çubuğundaki geçerli çizim modları düğmesine tıklayarak boşta durumuna geçiş yapın.

Sonraki bölümlerde şekillerin haritada çizileceği farklı yolları ana hatlarıyla listelenmektedir.

### <a name="how-to-draw-a-point"></a>Nokta çizme

Çizim Yöneticisi `draw-point` Çizim modundayken, haritada noktaları çizmek için aşağıdaki eylemler yapılabilir. Bu yöntemler tüm etkileşim modlarıyla çalışır.

**Çizimi Başlat**
 - Sol fare düğmesine tıklayın ya da haritaya bir nokta eklemek için haritaya dokunun. 
 - Fare haritanın üzerindeyken, `F` tuşa basın ve fare işaretçisinin koordinatına bir nokta eklenir. Bu yöntem, haritaya bir nokta eklemek için daha fazla doğruluk sağlar. Sol fare düğmesinin düğmesine basılması nedeniyle fare üzerinde daha az hareket olacaktır.
 - `F`Haritaya daha fazla işaret eklemek için tıklamaya, dokunmaya veya dokunarak çalışmaya devam edin.
 
**Çizimi tamamlama**
 - Çizim araç çubuğunda herhangi bir düğmeye tıklayın. 
 - Çizim modunu programlı olarak ayarlayın. 
 - Tuşuna basın `C` .

**Çizimi iptal et**
 - Tuşuna basın `Escape` .

### <a name="how-to-draw-a-line"></a>Çizgi çizme

Çizim Yöneticisi `draw-line` modundayken, etkileşim moduna bağlı olarak haritada noktaları çizmek için aşağıdaki eylemler yapılabilir.

**Çizimi Başlat**
 - Tıklama modu
   * Sol fare düğmesine tıklayın veya haritaya dokunarak bir çizginin her noktasını ekleyin. Her tıklama veya dokunma için çizgiye bir koordinat eklenir. 
   * Fare haritanın üzerindeyken, `F` tuşa basın ve fare işaretçisinin koordinatına bir nokta eklenir. Bu yöntem, haritaya bir nokta eklemek için daha fazla doğruluk sağlar. Sol fare düğmesinin düğmesine basılması nedeniyle fare üzerinde daha az hareket olacaktır.
   * İstenen tüm noktaların satıra eklenene kadar tıklamasına devam edin.
 - FreeHand modu
   * Sol fare düğmesine basın veya haritada dokunma yapın, fare veya dokunma noktasını etrafında sürükleyin. Fare veya dokunma noktası haritanın etrafında hareket ederken, koordinatlar çizgiye eklenir. Fare veya dokunma olayı tetiklendikten hemen sonra çizim tamamlanır. Koordinatların eklendiği sıklık, çizim yöneticileri seçeneği tarafından tanımlanır `freehandInterval` .
 - Karma mod
   * Tek bir çizgi çizerken, tıklama ve FreeHand yöntemleri arasında alternatif olarak. Örneğin, birkaç noktaya tıklayın, sonra da bir dizi noktayı eklemek için fareyi basılı tutup sürükleyin ve daha sonra birkaç kez tıklayın. 

**Çizimi tamamlama**
 - Karma/tıklama modu
   * Son noktada haritaya çift tıklayın. 
   * Çizim araç çubuğunda herhangi bir düğmeye tıklayın. 
   * Çizim modunu programlı olarak ayarlayın. 
 - FreeHand modu
   * Fare düğmesini veya dokunma noktasını serbest bırakın.
 - Tuşuna basın `C` .

**Çizimi iptal et**
 - Tuşuna basın `Escape` .

### <a name="how-to-draw-a-polygon"></a>Çokgen çizme

Çizim Yöneticisi `draw-polygon` modundayken, etkileşim moduna bağlı olarak haritada noktaları çizmek için aşağıdaki eylemler yapılabilir.

**Çizimi Başlat**
 - Tıklama modu
   * Sol fare düğmesine tıklayın veya haritaya dokunarak haritada her bir çokgen noktasını ekleyin. Her tıklama veya dokunma için çokgene bir koordinat eklenir. 
   * Fare haritanın üzerindeyken, `F` tuşa basın ve fare işaretçisinin koordinatına bir nokta eklenir. Bu yöntem, haritaya bir nokta eklemek için daha fazla doğruluk sağlar. Sol fare düğmesinin düğmesine basılması nedeniyle fare üzerinde daha az hareket olacaktır.
   * Tüm istenen noktaları Çokgen 'e eklenene kadar tıklamasına devam edin.
 - FreeHand modu
   * Sol fare düğmesine basın veya haritada dokunma yapın, fare veya dokunma noktasını etrafında sürükleyin. Fare veya dokunma noktası haritanın etrafında hareket ettirildiğinde, koordinatlar Çokgen 'e eklenir. Fare veya dokunma olayı tetiklendikten hemen sonra çizim tamamlanır. Koordinatların eklendiği sıklık, çizim yöneticileri seçeneği tarafından tanımlanır `freehandInterval` .
 - Karma mod
   * Tek bir çokgen çizerken, istediğiniz gibi tıklama ve FreeHand yöntemleri arasında alternatif. Örneğin, birkaç noktaya tıklayın, sonra da bir dizi noktayı eklemek için fareyi basılı tutup sürükleyin ve daha sonra birkaç kez tıklayın. 

**Çizimi tamamlama**
 - Karma/tıklama modu
   * Son noktada haritaya çift tıklayın. 
   * Çokgendeki ilk noktaya tıklayın.
   * Çizim araç çubuğunda herhangi bir düğmeye tıklayın. 
   * Çizim modunu programlı olarak ayarlayın. 
 - FreeHand modu
   * Fare düğmesini veya dokunma noktasını serbest bırakın.
 - Tuşuna basın `C` .

**Çizimi iptal et**
 - Tuşuna basın `Escape` .

### <a name="how-to-draw-a-rectangle"></a>Dikdörtgen çizme

Çizim Yöneticisi `draw-rectangle` modundayken, etkileşim moduna bağlı olarak haritada noktaları çizmek için aşağıdaki eylemler yapılabilir. Oluşturulan Şekil, [dikdörtgenler için genişletilmiş GeoJSON belirtimine](extend-geojson.md#rectangle)uyar.

**Çizimi Başlat**
 - Dikdörtgenin ilk köşesini eklemek ve dikdörtgeni oluşturmak için sürükleyin ve sürükleyerek sol fare düğmesine basın ya da haritada dokunma yapın. 

**Çizimi tamamlama**
 - Fare düğmesini veya dokunma noktasını serbest bırakın.
 - Çizim modunu programlı olarak ayarlayın. 
 - Tuşuna basın `C` .

**Çizimi iptal et**
 - Tuşuna basın `Escape` .

### <a name="how-to-draw-a-circle"></a>Daire çizme

Çizim Yöneticisi `draw-circle` modundayken, etkileşim moduna bağlı olarak haritada noktaları çizmek için aşağıdaki eylemler yapılabilir. Oluşturulan Şekil, [daireler için genişletilmiş GeoJSON belirtimine](extend-geojson.md#circle)uyar.

**Çizimi Başlat**
 - Dairenin merkezini eklemek için sol fare düğmesine basın veya haritada dokunma yapın ve ardından daire bir yarıçap sunun. 

**Çizimi tamamlama**
 - Fare düğmesini veya dokunma noktasını serbest bırakın.
 - Çizim modunu programlı olarak ayarlayın. 
 - Tuşuna basın `C` .

**Çizimi iptal et**
 - Tuşuna basın `Escape` .

## <a name="keyboard-shortcuts"></a>Klavye kısayolları

Çizim araçları klavye kısayollarını destekler. Bu klavye kısayolları, eşleme odağa sahip olduğunda işlevseldir.

| Anahtar      | Eylem                            |
|----------|-----------------------------------|
| `C` | Sürmekte olan tüm çizimleri tamamlar ve çizim modunu boşta olarak ayarlar. Odak, en üst düzey harita öğesine taşınacak.  |
| `Escape` | Sürmekte olan tüm çizimleri iptal eder ve çizim modunu boşta olarak ayarlar. Odak, en üst düzey harita öğesine taşınacak.  |
| `F` | Fare haritanın üzerindeyken bir noktaya, çizgiye veya poligbir koordinat ekler. Tıklama veya karma moddaki haritaya tıklanması için denk eylem. Bu kısayol daha kesin ve daha hızlı çizimler sağlar. Fareyi, düğmeyi basılı hareket etmeden hareket ettirmeden basılacak şekilde, fareyi ve diğerini konumlandırmak için tek bir el kullanabilirsiniz. |

## <a name="next-steps"></a>Sonraki adımlar

Çizim araçları modülündeki sınıflar hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Çizim Yöneticisi](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [Çizim araç çubuğu](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)