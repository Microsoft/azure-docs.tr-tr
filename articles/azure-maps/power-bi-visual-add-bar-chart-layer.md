---
title: Görsel Power BI bir çubuk grafik katmanını Azure Maps 'e ekleme | Microsoft Azure haritaları
description: Bu makalede, Power BI için Microsoft Azure haritaları görselindeki çubuk grafik katmanını nasıl kullanacağınızı öğreneceksiniz.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 136676b46df6e32f98dca99fccba19d4aa369dbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896285"
---
# <a name="add-a-bar-chart-layer"></a>Çubuk grafiği katmanı ekleme

**Çubuk grafik katmanı** , harita üzerinde 3B çubuklar veya silindir olarak konum verilerinin görselleştirmesine izin vererek bir sonraki boyuta veri almak için faydalıdır. Kabarcık katmanına benzer şekilde, çubuk grafik daha sonra renk ve göreli yükseklik kullanılarak aynı anda iki ölçümü kolayca görselleştirebilir. Çubukların yüksekliği olması için **alanlar** bölmesinin **Boyut** demetine bir ölçünün eklenmesi gerekir. Bir ölçü sağlanmazsa, yüksekliği olmayan çubuklar, **çubuk şekli** seçeneğine bağlı olarak düz kareler veya daireler olarak gösterilir.

> [!div class="mx-imgBorder"]
> ![Çubuk grafik katmanını kullanarak nokta verileri görüntüleyen bir harita](media/power-bi-visual/bar-chart-layer-styled.png)

Kullanıcılar farklı perspektiflerden verilerinizi görüntülemek için Haritayı eğleyebilir ve döndürebilir. Eşleme, aşağıdaki yöntemlerden biri kullanılarak eğimli veya eklenebilir.

-   **Biçim** bölmesinin **harita ayarlarındaki** **Gezinti denetimleri** seçeneğini açın. Bu, Haritayı eğmek için bir düğme ekler.
-   Sağ fare düğmesine basın ve fareyi yukarı veya aşağı sürükleyin.
-   Dokunmatik ekran kullanarak haritada iki parmağınızla dokunarak dokunun ve bunları birlikte yukarı veya aşağı sürükleyin.
-   Eşleme odaklı olarak, **SHIFT** tuşunu basılı tutun ve **yukarı** veya **aşağı ok** tuşlarına basın.

Eşleme, aşağıdaki yöntemlerden biri kullanılarak döndürülebilir.

-   **Biçim** bölmesinin **harita ayarlarındaki** **Gezinti denetimleri** seçeneğini açın. Bu, Haritayı döndürmek için bir düğme ekler.
-   Sağ fare düğmesine basın ve fareyi sola veya sağa sürükleyin.
-   Dokunmatik ekran kullanarak Haritayı iki parmağınızla dokunarak ve döndürün.
-   Eşleme odaklı olacak şekilde **SHIFT** tuşunu basılı tutun ve **sol** veya **sağ ok** tuşlarına basın.

Aşağıda, **çubuk grafik katmanı** bölümünde bulunan **Biçim** bölmesinde bulunan tüm ayarlar verilmiştir.

| Ayar              | Açıklama      |
|----------------------|------------------|
| Çubuk şekli            | 3B çubuğun şekli.<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Box: dikdörtgen kutular olarak işlenen çubuklar.<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Silindir – Silindir olarak işlenen çubuklar. |
| Height               | Her çubuğun yüksekliği. **Alanlar** bölmesinin **Boyut** demetine bir alan geçirilirse, çubuklar bu yükseklik değerine göre ölçeklendirilir. |
| Yakınlaştırma yüksekliğini ölçeklendirin | Çubukların yüksekliğinin yakınlaştırma düzeyine göre ölçeklendirilmesi gerekip gerekmediğini belirtir. |
| Width                | Her çubuğun genişliği.  |
| Yakınlaştırmadan genişliği Ölçeklendir  | Çubukların genişliğinin yakınlaştırma düzeyine göre ölçeklendirilmesi gerekip gerekmediğini belirtir.  |
| Dolgu rengi           | Her çubuğun rengi. **Alanlar** bölmesinin **gösterge** demetine bir alan geçirildiğinde Bu seçenek gizlenir ve **Biçim** bölmesinde ayrı bir **veri renkleri** bölümü görüntülenir. |
| Şeffaflık         | Her çubuğun saydamlığı. |
| En az yakınlaştırma             | En düşük yakınlaştırma düzeyi kutucukları var. |
| En fazla yakınlaştırma             | En yüksek yakınlaştırma düzeyi kutucukları var. |
| Katman konumu       | Katmanın diğer harita katmanlarına göre konumunu belirtir. |

> [!NOTE]
> Çubukların bir küçük genişlik değeri varsa ve **Yakınlaştırma genişliği ölçeği Yakınlaştır** seçeneği devre dışıysa, işlenen genişliği boyutunun bir pikselden küçük olması halinde uzaklaştırılmış, bu durum ortadan kalkar. Ancak, **yakınlaştırmadan genişliği Ölçekle** seçeneği etkinken, yakınlaştırma düzeyi değiştiğinde, büyük veri kümelerinin performansını etkileyebilecek ek hesaplamalar yapılır.

## <a name="next-steps"></a>Sonraki adımlar

Haritaya daha fazla bağlam ekleyin:

> [!div class="nextstepaction"]
> [Başvuru katmanı ekleme](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Kutucuk katmanı ekleme](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Gerçek zamanlı trafiği gösterme](power-bi-visual-show-real-time-traffic.md)

Görseli özelleştirin:

> [!div class="nextstepaction"]
> [Power BI'da renk biçimlendirmeye ilişkin ipuçları ve püf noktaları](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [Görselleştirme başlıklarını, arka planlarını ve göstergelerini özelleştirme](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)