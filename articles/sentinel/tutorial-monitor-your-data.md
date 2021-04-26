---
title: Azure 'da Azure Izleyici çalışma kitaplarını kullanarak verilerinizi görselleştirin | Microsoft Docs
description: Azure Sentinel 'de çalışma kitaplarını kullanarak verilerinizi görselleştirmeyi öğrenmek için bu öğreticiyi kullanın.
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
ms.date: 04/04/2021
ms.author: yelevin
ms.openlocfilehash: c26d86c98c83d9762acb8a75bba8fe464cc2a58e
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491525"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>Öğretici: Verilerinizi görselleştirme ve izleme

Veri kaynaklarınızı Azure Sentinel 'e [bağladıktan](quickstart-onboard.md) sonra, özel panolar oluşturma konusunda çok yönlülük sağlayan Azure Izleyici çalışma kitaplarını benimseyerek Azure Sentinel benimseme kullanarak verileri görselleştirebilir ve izleyebilirsiniz. Çalışma kitapları Azure Sentinel 'de farklı şekilde görüntülenirken, [Azure Izleyici çalışma kitapları ile etkileşimli raporlar oluşturmayı](../azure-monitor/visualize/workbooks-overview.md)görmeniz yararlı olabilir. Azure Sentinel, verileriniz genelinde özel çalışma kitapları oluşturmanızı sağlar ve ayrıca, bir veri kaynağını bağladığınızda verilerinize hızlı bir şekilde Öngörüler elde etmenizi sağlayan yerleşik çalışma kitabı şablonlarıyla birlikte gelir.

Bu öğretici, verilerinizi Azure Sentinel 'de görselleştirmenize yardımcı olur.
> [!div class="checklist"]
> * Yerleşik çalışma kitaplarını kullanma
> * Yeni çalışma kitapları oluşturma

## <a name="prerequisites"></a>Önkoşullar

Azure Sentinel çalışma alanının kaynak grubunda en az **çalışma kitabı okuyucusu** veya **çalışma kitabı katılımcısı** izinlerinizin olması gerekir.

> [!NOTE]
> Azure Sentinel 'de görebileceğiniz çalışma kitapları, Azure Sentinel çalışma alanının kaynak grubu içinde kaydedilir ve oluşturuldukları çalışma alanına göre etiketlenebilir.

## <a name="use-built-in-workbooks"></a>Yerleşik çalışma kitaplarını kullanma

1. Azure Sentinel yerleşik çalışma kitaplarının tam listesini görmek için **çalışma kitaplarına** gidin ve **Şablonlar** ' ı seçin. 

    Bağladığınız veri türleriyle ilgili olan hangisi olduğunu görmek için, her çalışma kitabındaki **gerekli veri türleri** alanı, ilgili verileri zaten Azure Sentinel 'e akıdıysanız yeşil onay işaretinin yanındaki veri türünü listeleyecek.

    [![Çalışma kitaplarına gidin. ](media/tutorial-monitor-data/access-workbooks.png)](media/tutorial-monitor-data/access-workbooks.png#lightbox)

1. Şablonlarınızın verilerinize göre doldurulmuş olduğunu görmek için **şablonu görüntüle** ' yi seçin.

1. Çalışma kitabını düzenlemek için **Kaydet**' i seçin ve ardından şablon için JSON dosyasını kaydetmek istediğiniz konumu seçin.

   > [!NOTE]
   > Bu, ilgili şablonu temel alan bir Azure kaynağı oluşturur ve verilerin değil, çalışma kitabının JSON dosyasını kaydeder.


1. **Kaydedilmiş çalışma kitabını görüntüle**' yi seçin. 

    [![Çalışma kitaplarını görüntüleyin. ](media/tutorial-monitor-data/workbook-graph.png)](media/tutorial-monitor-data/workbook-graph.png#lightbox)

    Çalışma kitabını gereksinimlerinize göre özelleştirmek için çalışma kitabı araç çubuğundaki **Düzenle** düğmesini seçin. İşiniz bittiğinde, değişikliklerinizi kaydetmek için **Kaydet** ' i seçin.

    Daha fazla bilgi için bkz. [Azure Izleyici çalışma kitapları ile etkileşimli raporlar oluşturma](../azure-monitor/visualize/workbooks-overview.md).

> [!TIP]
> Çalışma kitabınızı kopyalamak için **Düzenle** ' yi seçin ve **farklı kaydet**' i seçip aynı abonelik ve kaynak grubu altında başka bir adla kaydettiğinizden emin olun.
> Kopyalanmış çalışma kitapları, **çalışma kitapları** sekmesinin altında görüntülenir.
>
## <a name="create-new-workbook"></a>Yeni çalışma kitabı oluştur

1. Çalışma **kitaplarına** gidip sıfırdan yeni bir çalışma kitabı oluşturmak için **çalışma kitabı Ekle** ' yi seçin.

    [![Yeni çalışma kitabı. ](media/tutorial-monitor-data/create-workbook.png)](media/tutorial-monitor-data/create-workbook.png#lightbox)

1. Çalışma kitabını düzenlemek için **Düzenle**' yi seçin ve ardından gerektiğinde metin, sorgular ve parametreler ekleyin. Çalışma kitabını özelleştirme hakkında daha fazla bilgi için bkz. [Azure Izleyici çalışma kitapları ile etkileşimli raporlar oluşturma](../azure-monitor/visualize/workbooks-overview.md). 

1. Bir sorgu oluştururken, **veri kaynağının** **Günlükler** olarak ayarlandığından emin olun ve **kaynak türü** **Log Analytics** olarak ayarlandığından ilgili çalışma alanlarını seçin. 

1. Çalışma kitabınızı oluşturduktan sonra, çalışma kitabını kaydederek Azure Sentinel çalışma alanınızın abonelik ve kaynak grubunun altına kaydettiğinizden emin olun.

1. Kuruluşunuzdaki başkalarının çalışma kitabını kullanmasına izin vermek istiyorsanız, **paylaşılan raporlar**' ı seçmek **için Kaydet** ' in altında. Bu çalışma kitabının yalnızca sizin için kullanılabilir olmasını istiyorsanız, **Raporlarım**' ı seçin.

1. Çalışma alanınızdaki çalışma kitapları arasında geçiş yapmak için, çalışma kitabını açmak üzere **Aç** ![ simgesini seçin.](./media/tutorial-monitor-data/switch.png) herhangi bir çalışma kitabının araç çubuğunda. Ekran, geçiş yapmak için kullanabileceğiniz diğer çalışma kitapları listesine geçer.

    Açmak istediğiniz çalışma kitabını seçin:

    [![Çalışma kitaplarını değiştirin. ](media/tutorial-monitor-data/switch-workbooks.png)](media/tutorial-monitor-data/switch-workbooks.png#lightbox)

## <a name="refresh-your-workbook-data"></a>Çalışma kitabı verilerinizi yenileme

Güncelleştirilmiş verileri göstermek için çalışma kitabınızı yenileyin. Araç çubuğunda, aşağıdaki seçeneklerden birini seçin:

- :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false":::Çalışma kitabı verilerinizi el ile yenilemek için **yenileyin**.

- :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false":::**Otomatik Yenile**, çalışma kitabınızı yapılandırılmış bir aralıkta otomatik olarak yenilenecek şekilde ayarlamak için.

    - Desteklenen otomatik yenileme aralıkları **5 dakikadan** **1 güne** kadar arasındadır.

    - Otomatik yenileme, bir çalışma kitabını düzenlerken duraklatılır ve düzenleme modundan görüntüleme moduna geçtiğinizde aralıklar yeniden başlatılır.

    - Verilerinizi el ile yenilediğinizde otomatik yenileme aralıkları da yeniden başlatılır.

    > [!TIP]
    > Varsayılan olarak, otomatik yenileme kapalıdır. Performansı iyileştirmek için, bir çalışma kitabını her kapattığınızda otomatik yenileme de kapatılır ve arka planda çalıştırılmaz. Çalışma kitabını bir sonraki açışınızda gerektiğinde otomatik yenilemeyi yeniden açın.
    >

## <a name="print-a-workbook-or-save-as-pdf"></a>Çalışma kitabını Yazdır veya PDF olarak kaydet

Bir çalışma kitabını yazdırmak veya bir PDF olarak kaydetmek için, çalışma kitabı başlığının sağındaki Seçenekler menüsünü kullanın.

1. :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **İçerik Yazdır**> Seçenekler ' i seçin. 
2. Yazdırma ekranında, yazdırma ayarlarınızı gerektiği gibi ayarlayın veya yerel olarak kaydetmek için **PDF olarak kaydet** ' i seçin.

Örnek:

[![Çalışma kitabınızı yazdırın veya PDF olarak kaydedin. ](media/whats-new/print-workbook.png)](media/whats-new/print-workbook.png#lightbox)

## <a name="how-to-delete-workbooks"></a>Çalışma kitaplarını silme

Kaydedilmiş bir çalışma kitabını (kaydedilmiş bir şablon ya da özelleştirilmiş çalışma kitabı) silmek için, çalışma kitapları sayfasında, silmek istediğiniz kaydedilmiş çalışma kitabını seçin ve **Sil**' i seçin. Bu, kaydedilen çalışma kitabını kaldırır.

> [!NOTE]
> Bu, çalışma kitabı kaynağını ve şablonda yaptığınız değişiklikleri kaldırır. Özgün şablon kullanılabilir olmaya devam edecektir.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure çalışma kitaplarını kullanarak verilerinizi Azure Sentinel 'de görselleştirmeyi öğrendiniz.

Tehditlere yönelik yanıtlarınızı otomatik hale getirmeyi öğrenmek için bkz. [Azure Sentinel 'de otomatik tehdit yanıtlarını ayarlama](tutorial-respond-threats-playbook.md).
