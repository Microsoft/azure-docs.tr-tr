---
title: Azure Sentinel 'de yerleşik analiz kuralları ile tehditleri algılama | Microsoft Docs
description: Şüpheli bir sorun olduğunda size bildirimde bulunan yerleşik şablonları temel alan yerleşik tehdit algılama kurallarını nasıl kullanacağınızı öğrenin.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2021
ms.author: yelevin
ms.openlocfilehash: ba757474ab24006b7b8b514bda085522bd353ea8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312680"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Öğretici: Hemen tehditleri algılayabilme

Veri kaynaklarınızı Azure Sentinel 'e [bağladıktan](quickstart-onboard.md) sonra, şüpheli bir sorun oluştuğunda bildirim almak isteyeceksiniz. Azure Sentinel, tehdit algılama kuralları oluşturmanıza yardımcı olacak yerleşik, yerleşik şablonlar sağlar. Bu şablonlar, Microsoft 'un, bilinen tehditler, yaygın saldırı vektörleri ve şüpheli etkinlik yükseltme zincirlerine göre güvenlik uzmanı ve analistlerinin ekibi tarafından tasarlanmıştır. Bu şablonlardan oluşturulan kurallar, şüpheli görünen herhangi bir etkinlik için ortamınızda otomatik olarak arama yapar. Şablonların birçoğu, etkinlikleri aramak veya gereksinimlerinize göre onları filtrelemek için özelleştirilebilir. Bu kurallar tarafından oluşturulan uyarılar, ortamınızda atayabileceğiniz ve araştırabilmeniz için olaylar oluşturur.

Bu öğretici, Azure Sentinel ile tehditleri algılamanıza yardımcı olur:

> [!div class="checklist"]
> * Kullanıma hazır tehdit algılamalarını kullanma
> * Tehdit yanıtlarını otomatikleştirin

## <a name="about-out-of-the-box-detections"></a>Hazır gelen algılamalar hakkında

Kullanıma hazır tüm algılamaları görüntülemek için analizler **' e ve ardından** **kural şablonları**' na gidin. Bu sekme, tüm Azure Sentinel yerleşik kurallarını içerir.

   :::image type="content" source="media/tutorial-detect-built-in/view-oob-detections.png" alt-text="Azure Sentinel ile tehditleri bulmak için yerleşik algılamaları kullanın":::

Aşağıdaki bölümlerde kullanılabilen kullanıma hazır şablonların türleri açıklanır:

### <a name="microsoft-security"></a>Microsoft güvenliği

Microsoft güvenlik şablonları, diğer Microsoft Güvenlik çözümlerinde oluşturulan uyarılardan gerçek zamanlı olarak Azure Sentinel olaylarını otomatik olarak oluşturur. Benzer mantığa sahip yeni kurallar oluşturmak için Microsoft güvenlik kurallarını şablon olarak kullanabilirsiniz.

Güvenlik kuralları hakkında daha fazla bilgi için bkz. [Microsoft Güvenlik uyarılarından olayları otomatik olarak oluşturma](create-incidents-from-alerts.md).

### <a name="fusion"></a>Fusion

Fusion teknolojisine bağlı olarak, Azure Sentinel 'de gelişmiş çok aşamalı saldırı algılama, birden fazla ürün genelinde çok sayıda düşük kaliteli uyarı ve olayı Yüksek uygunluğa ve eyleme dönüştürülebilir olaylara ilişkilendirebilen ölçeklenebilir makine öğrenimi algoritmaları kullanır. Fusion varsayılan olarak etkindir. Logic Hidden ve bu nedenle özelleştirilebilir olmadığından, bu şablonla yalnızca bir kural oluşturabilirsiniz.

> [!IMPORTANT]
> Fusion kural şablonundaki algılamalardan bazıları şu anda **Önizleme** aşamasındadır. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.
>
> Hangi algılamaların önizlemede olduğunu görmek için bkz. [Azure Sentinel 'de gelişmiş çok aşamalı saldırı algılama](fusion.md).

### <a name="machine-learning-behavioral-analytics"></a>Makine öğrenmesi davranış analizi

Bu şablonlar, Microsoft makine öğrenimi algoritmalarını temel alır. bu nedenle, nasıl çalıştıkları ve ne zaman çalıştıkları hakkında iç mantığı göremezsiniz. Logic Hidden ve bu nedenle özelleştirilebilir olmadığından, bu türden her şablonla yalnızca bir kural oluşturabilirsiniz.

> [!IMPORTANT]
> - Machine Learning davranış analizi kural şablonları Şu anda **Önizleme** aşamasındadır. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.
>
> - ML davranış analizi şablonlarına dayalı herhangi bir kural oluşturup etkinleştirerek, Microsoft 'un, makine öğrenimi motorları ve modelleri tarafından işlenmek üzere **Azure Sentinel çalışma alanınızın Coğrafya dışında alınan verileri kopyalaması için izin verirsiniz** .

### <a name="scheduled"></a>Zamanlanan

Zamanlanan analiz kuralları, Microsoft güvenlik uzmanları tarafından yazılan yerleşik sorguları temel alır. Sorgu mantığını görebilir ve üzerinde değişiklik yapabilirsiniz. Zamanlanan kurallar şablonunu kullanabilir ve yeni kurallar oluşturmak için Sorgu mantığı ve zamanlama ayarlarını özelleştirebilirsiniz.

> [!TIP]
> Kural zamanlama seçenekleri, kuralı etkinleştirdiğinizde başlangıç saati ile her bir belirtilen dakika, saat veya gün boyunca çalıştırılacak kuralı yapılandırmayı içerir.
>
> Kuralların zaman içinde yeni olay yığınını aldığından emin olmak için yeni veya düzenlenmiş bir analiz kuralını etkinleştirdiğinizde, en az bir şekilde değişiklik yapmanızı öneririz. Örneğin, SOC analistlerinizin Workday 'i başlayacağı zaman eşitlenmiş bir kural çalıştırmak ve kuralları etkinleştirmek isteyebilirsiniz.
>

## <a name="use-out-of-the-box-detections"></a>Kullanıma hazır algılamaları kullanma

1. Yerleşik bir şablon kullanmak için şablon adına tıklayın ve ardından Ayrıntılar bölmesindeki **kural oluştur** düğmesine tıklayarak bu şablonu temel alan yeni bir etkin kural oluşturun. Her şablonda gerekli veri kaynakları listesi bulunur. Şablonu açtığınızda, veri kaynakları otomatik olarak kullanılabilirlik için denetlenir. Bir kullanılabilirlik sorunu varsa, **kural oluştur** düğmesi devre dışı bırakılabilir veya bu etkiye bir uyarı görebilirsiniz.

    :::image type="content" source="media/tutorial-detect-built-in/use-built-in-template.png" alt-text="Algılama kuralı Önizleme bölmesi":::

1. **Kural oluştur** düğmesine tıklamak, seçilen şablonu temel alan kural oluşturma Sihirbazı ' nı açar. Tüm ayrıntılar, yeniden doldurulur ve **zamanlanan** veya **Microsoft Güvenlik** şablonlarıyla, mantığın ve diğer kural ayarlarının özel gereksinimlerinize daha uygun olması için özelleştirebilirsiniz. Bu işlemi, yerleşik şablonu temel alan ek kurallar oluşturmak için yineleyebilirsiniz. Kural oluşturma sihirbazındaki adımları sona erdikten sonra, şablonu temel alan bir kural oluşturmayı bitirirsiniz. Yeni kurallar, **etkin kurallar** sekmesinde görünür.

    Kural oluşturma Sihirbazı 'nda kurallarınızı özelleştirme hakkında daha fazla bilgi için bkz. [öğretici: tehditleri algılamak için özel analiz kuralları oluşturma](tutorial-detect-threats-custom.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure Sentinel kullanarak tehditleri algılamaya nasıl başlacağınız hakkında daha fazla öğrenirsiniz.

Tehditlere yönelik yanıtlarınızı otomatik hale getirmeyi öğrenmek için [Azure Sentinel 'de otomatik tehdit yanıtları ayarlayın](tutorial-respond-threats-playbook.md).
