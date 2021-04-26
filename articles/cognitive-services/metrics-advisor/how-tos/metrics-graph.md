---
title: Ölçüm Danışmanı ölçüm grafiği
titleSuffix: Azure Cognitive Services
description: Ölçüm grafınızı yapılandırma ve verilerinizde ilgili bozukluklar görselleştirme.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbullwin
ms.openlocfilehash: bcedef4a1339dacaff3fe841e97f985c42320819
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "92043162"
---
# <a name="how-to-build-a-metrics-graph-to-analyze-related-metrics"></a>Nasıl yapılır: ilgili ölçümleri çözümlemek için ölçüm grafiği oluşturma

Ölçüm Danışmanı 'ndaki her ölçüm, gelecekteki eğilimleri tahmin etmek için geçmiş verilerden öğrenerek bir model tarafından ayrı olarak izlenir. Her ölçümün kendisine uygulanan ayrı bir modeli vardır. Ancak bazı durumlarda, birkaç ölçüm birbirleriyle ilişkilendirilecektir ve anormalilerin birden çok ölçüm genelinde çözümlenmesi gerekir. **Ölçüm grafiği** buna yardımcı olur. 

Örnek olarak, ayrı ölçümler için farklı telemetri akışlarınız varsa, ölçüm Danışmanı bunları ayrı olarak izler. Bir ölçümde bir ölçümde bozukluklar başkalarının bozukluklar oluşmasına neden olursa, bu ilişkileri bulma ve verilerinize ilişkin temel neden, olayları adresleyen faydalı olabilir. Ölçüm grafiği, bulunan anormalilerin görsel topoloji grafiğini oluşturmanıza olanak sağlar. 

## <a name="select-a-metric-to-put-the-first-node-to-the-graph"></a>İlk düğümü grafiğe yerleştirmek için bir ölçüm seçin

Gezinti çubuğundaki **ölçümler grafik** sekmesine tıklayın. Ölçüm grafiği oluşturmanın ilk adımı grafiğe bir düğüm koyulamıyor. Sayfanın üst kısmında bir veri akışı ve ölçüm seçin. Alt panelde bir düğüm görünür. 

:::image type="content" source="../media/graph/metrics-graph-select.png" alt-text="Ölçüm seçin":::

## <a name="add-a-noderelation-on-existing-node"></a>Mevcut düğüme bir düğüm/ilişki ekleme

Daha sonra başka bir düğüm eklemeniz ve var olan düğüm (ler) e bir ilişki belirtmeniz gerekir. Var olan bir düğümü seçin ve sağ tıklayın. Bir bağlam menüsü, çeşitli seçeneklerle birlikte görüntülenir. 

**Ilişki Ekle**' ye tıklayın, başka bir ölçüm seçebilir ve iki düğüm arasındaki ilişki türünü belirtebilirsiniz. Ayrıca, belirli boyut filtrelerini de uygulayabilirsiniz. 

:::image type="content" source="../media/graph/metrics-graph-node-action.png" alt-text="Düğüm ve ilişki ekleme":::

Yukarıdaki adımları tekrarladıktan sonra, tüm ilgili ölçümler arasındaki ilişkileri açıklayan bir ölçüm grafiğine sahip olursunuz.
**Düğüm renkleriyle ilgili ipucu**
> [!TIP]
> - Bir ölçüm ve boyut filtresi seçtiğinizde, Grafikteki aynı ölçüm ve boyut filtresi bulunan tüm düğümler **<font color=blue>mavi</font>** olarak renklendirilir.
> - Grafikteki bir ölçümü temsil eden seçilmemiş düğümler **<font color=green>yeşil</font>** renkte görünür.
> - Geçerli ölçümde gözlemlenen bir anomali varsa, düğüm **<font color=red>kırmızı</font>** renkte görünür.

## <a name="view-related-metrics-anomaly-status-in-incident-hub"></a>Olay Hub 'ında ilgili ölçümleri anomali durumunu görüntüleme

Ölçüm grafiği oluşturulduğunda, grafik içindeki ölçümler üzerinde her bir anomali algılandığında ilgili anomali durumlarını görüntüleyebilir ve olayın üst düzey bir görünümünü edinebilirsiniz. 

Grafik içindeki bir olaya tıklayın ve daha sonra, tanılama bilgilerinin altındaki **çapraz ölçüm analizine** kaydırın.

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="İlgili ölçümleri ve anormallikleri görüntüleme":::

## <a name="next-steps"></a>Sonraki adımlar

- [Geri bildirimi kullanarak anomali algılamayı ayarlama](anomaly-feedback.md)
- [Bir olayı tanılayın](diagnose-incident.md).
- [Ölçümleri yapılandırma ve ince ayar algılama yapılandırması](configure-metrics.md)
