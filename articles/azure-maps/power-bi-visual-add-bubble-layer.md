---
title: Görsel Power BI Azure Maps 'a bir kabarcık katmanı ekleyin | Microsoft Azure haritaları
description: Bu makalede, Power BI için Microsoft Azure haritaları görselindeki balon katmanını nasıl kullanacağınızı öğreneceksiniz.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: ff0d2c8e90995222af79d5be63ca3e9da24b3c45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896234"
---
# <a name="add-a-bubble-layer"></a>Baloncuk katmanı ekleme

**Kabarcık katmanı** , konum verilerini haritada ölçeklendirilen daireler olarak işler.

> [!div class="mx-imgBorder"]
> ![Balon katmanını kullanarak nokta verileri görüntüleyen bir harita](media/power-bi-visual/bubble-layer-with-legend-color.png)

Başlangıçta tüm kabarcıklar aynı Fill rengine sahiptir. **Alanlar** bölmesinin **gösterge** demetine bir alan geçirilirse, kabarcıklar kategoriye göre renklendirilir. Kabarcıkların ana hattı varsayılan olarak beyazdır, ancak yeni bir renge veya yüksek karşıtlıklı ana hat seçeneği etkinleştirilerek değiştirilebilir. **Yüksek karşıtlıklı ana hat** seçeneği, bir ana hat rengini dinamik olarak, renkli bir şekilde, bir yüksek karşıtlıklı varyant olarak atar. Bu, haritanın stili ne olursa olsun kabarcıkların açıkça görünür olmasını sağlamaya yardımcı olur. Aşağıda, **kabarcık katmanı** bölümünde bulunan **Biçim** bölmesindeki birincil ayarlar verilmiştir.

| Ayar               | Açıklama    |
|-----------------------|----------------|
| Boyut                  | Her kabarcığın boyutu. **Alanlar** bölmesinin **Boyut** demetine bir alan geçirildiğinde Bu seçenek gizlenir. Ek seçenekler, bu makalede daha sonra [kabarcık boyutu ölçeklendirme](#bubble-size-scaling) konusunda özetlenen şekilde görünür. |
| Dolgu rengi            | Her kabarcığun rengi. **Alanlar** bölmesinin **gösterge** demetine bir alan geçirildiğinde Bu seçenek gizlenir ve **Biçim** bölmesinde ayrı bir **veri renkleri** bölümü görüntülenir. |
| Saydamlığı doldur     | Her kabarcığun saydamlığı. |
| Yüksek karşıtlıklı ana hat | , Bir daha iyi erişilebilirlik için, daha iyi bir sonuç olarak, bir, Fill renginin büyük bir türevini kullanarak, ana hat rengi ile kontrast sağlar. |
| Ana hat rengi         | Balonu özetleyen renk. **Yüksek karşıtlıklı ana hat** seçeneği etkin olduğunda bu seçenek gizlenir. |
| Ana hat saydamlığı  | Ana hat saydamlığı. |
| Ana hat genişliği         | Ana hattın piksel cinsinden genişliği. |
| Bulanıklaştırma                  | Ana hat için uygulanan Bulanıklaştırma miktarı. 1 değeri, kabarcıkları yalnızca merkez noktasının saydamlığın olmadığı şekilde bulanıklaştırır. 0 değeri, tüm bulanıklaştırma efektlerini uygular. |
| Sıklık hizalaması       | Haritanın bir üst sırada nasıl görüneceğini belirtir. <br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Görünüm penceresi-görünüm, Görünüm penceresi ile ilişkili harita üzerinde görünür. (varsayılan)<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Map-kabarcıklar haritanın yüzeyinde düz olarak işlenir. |
| Yakınlaştırma ölçeği            | Kabarcıkların yakınlaştırma düzeyine göre ölçeklendirilmesi gereken miktar. Bir yakınlaştırma ölçeği, ölçeklendirmesiz bir anlamına gelir. Büyük değerler yakınlaştırıldığında ölçeği daha küçük hale getirir ve yakınlaştırıldığında daha büyük olur. Bu, yakınlaştırıldığında haritadaki dağınıklığı azaltmaya yardımcı olur, ancak iade edildiğinde noktaların daha fazla zaman almasını sağlar. 1 değeri herhangi bir ölçeklendirmeyi uygulamaz. |
| En az yakınlaştırma              | En düşük yakınlaştırma düzeyi kutucukları var. |
| En fazla yakınlaştırma              | En yüksek yakınlaştırma düzeyi kutucukları var. |
| Katman konumu        | Katmanın diğer harita katmanlarına göre konumunu belirtir. |

## <a name="bubble-size-scaling"></a>Kabarcık boyutu ölçeklendirme

**Alanlar** bölmesinin **Boyut** demetine bir alan geçirilirse, kabarcıklar görece her bir veri noktasının ölçü değerine göre ölçeklendirilir. **Biçim** bölmesindeki **kabarcık katmanı** bölümündeki **Boyut** seçeneği, bir alan **Boyut** demetine geçirildiğinde kaybolur, çünkü kabarcıklar en düşük ve en büyük değer arasında ölçeklendirildiğinde olur. Bir **Boyut** demeti belirtilen alana sahip olduğunda, **Biçim** bölmesinin **kabarcık katmanı** bölümünde aşağıdaki seçenekler görüntülenir.

| Ayar             | Açıklama  |
|---------------------|--------------|
| En küçük boyut            | Verileri ölçeklendirirken en küçük kabarcık boyutu.|
| En büyük boyut            | Veriler ölçeklendirilirken en büyük kabarcık boyutu.|
| Boyut ölçeklendirme yöntemi | Göreli kabarcık boyutunu belirlemede kullanılan ölçekleme algoritması.<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Doğrusal-daha erken ve en büyük boyuta eşlenen giriş verisi aralığı. (varsayılan)<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Günlük veri aralığı, en düşük ve en büyük boyutla eşleştirilmiş olan giriş verisi logaritarak.<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Cubic-Bezier-özel ölçeklendirme yöntemi oluşturmak için Cubic-Bezier eğrisinin x1, y1, X2, Y2 değerlerini belirtin. |

**Boyut ölçekleme yöntemi** **günlüğe yazılır** olarak ayarlandığında, aşağıdaki seçenekler kullanılabilir hale getirilir.

| Ayar   | Açıklama      |
|-----------|------------------|
| Günlük ölçeği | Kabarcıkların boyutu hesaplanırken uygulanacak Logaritmik ölçek. |

**Boyut ölçekleme yöntemi** **üçüncü dereceden Bezier** olarak ayarlandığında, ölçek eğrisini özelleştirmek için aşağıdaki seçenekler kullanılabilir hale getirilir.

| Ayar | Açıklama                           |
|---------|---------------------------------------|
| X1      | Üçüncü dereceden Bezier eğrisinin x1 parametresi. |
| Y1      | Üçüncü dereceden Bezier eğrisinin x2 parametresi. |
| X2      | Üçüncü dereceden Bezier eğrisinin Y1 parametresi. |
| Y2      | Üçüncü dereceden Bezier eğrisinin Y2 parametresi. |

> [!TIP]
> [https://cubic-bezier.com/](https://cubic-bezier.com/) Cubic-Bezier eğrilerine yönelik parametreler oluşturmak için kullanışlı bir araçtır.

## <a name="next-steps"></a>Sonraki adımlar

Haritada verinin nasıl görüntülendiğini değiştirin:

> [!div class="nextstepaction"]
> [Çubuk grafiği katmanı ekleme](power-bi-visual-add-bar-chart-layer.md)

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