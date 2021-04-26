---
title: İş akışı hatalarını giderme ve tanılama
description: Azure Logic Apps 'teki iş akışlarınızda sorunları, hataları ve hataları nasıl giderebileceğinizi ve tanılamanıza öğrenin
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95995055"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Azure Logic Apps’te iş akışı hatalarını giderme ve tanılama

Mantıksal uygulamanız uygulamanızdaki sorunları tanılamanıza ve hata ayıklamanıza yardımcı olabilecek bilgiler oluşturur. Azure portal aracılığıyla iş akışındaki her adımı inceleyerek bir mantıksal uygulamayı tanılayabilirsiniz. Ya da çalışma zamanı hata ayıklaması için bir iş akışına bazı adımlar ekleyebilirsiniz.

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>Tetikleyici geçmişini denetle

Her mantıksal uygulama çalıştırması bir tetikleyici deneyimiyle başlar, bu nedenle tetikleyici başlamazsa, şu adımları izleyin:

1. [Tetikleyici geçmişini denetleyerek](../logic-apps/monitor-logic-apps.md#review-trigger-history)tetikleyicinin durumunu kontrol edin. Tetikleyici girişimi hakkında daha fazla bilgi görüntülemek için, bu tetikleyici olayını seçin, örneğin:

   ![Tetikleyici durumunu ve geçmişini görüntüle](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. Beklendiği gibi göründüğünü onaylamak için tetikleyicinin girdilerini denetleyin. **Girişler bağlantısı** altında, **giriş** bölmesini gösteren bağlantıyı seçin.

   Tetikleyici girişleri, tetikleyicinin beklediği ve iş akışını başlatması gereken verileri içerir. Bu girişleri gözden geçirmek, tetikleyici girişlerinin doğru olup olmadığını ve koşulun karşılanıp karşılanmadığını belirlemenize yardımcı olabilir.

   Örneğin, `feedUrl` burada özelliği yanlış bır RSS Akış değerine sahiptir:

   ![Hata için tetikleyici girişlerini gözden geçirin](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. Varsa, bekledikleri gibi göründüğünü onaylamak için Tetikleyiciler çıkışları olup olmadığını kontrol edin. **Çıkışlar bağlantısı** altında, **çıktılar** bölmesini gösteren bağlantıyı seçin.

   Tetikleyici çıkışları, tetikleyicinin iş akışınızda sonraki adıma geçirdiği verileri içerir. Bu çıktıları gözden geçirmek, iş akışınızın sonraki adımına doğru veya beklenen değerlerin geçirilip geçirilmediğini belirlemenize yardımcı olabilir:

   ![Hata için tetikleyici çıkışlarını gözden geçirme](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > Tanımadığınız herhangi bir içerik bulursanız Azure Logic Apps [farklı içerik türleri](../logic-apps/logic-apps-content-type.md) hakkında daha fazla bilgi edinin.

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>Çalıştırma geçmişini denetle

Tetikleyici bir öğe veya olay için her tetiklendiğinde, Logic Apps altyapısı her öğe veya olay için ayrı bir iş akışı örneği oluşturur ve çalıştırır. Bir çalıştırma başarısız olursa, iş akışındaki her adımın durumu ve her adımın giriş ve çıkışları dahil olmak üzere bu çalıştırma sırasında ne olduğunu gözden geçirmek için aşağıdaki adımları izleyin.

1. Çalıştırma [geçmişini denetleyerek](../logic-apps/monitor-logic-apps.md#review-runs-history)iş akışının çalışma durumunu kontrol edin. Hatalı çalışma hakkında daha fazla bilgi görüntülemek için, söz konusu çalıştırdıkları tüm adımlar da dahil olmak üzere, başarısız olan çalıştırmayı seçin.

   ![Çalıştırma geçmişini görüntüle ve başarısız çalıştırma seçin](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. Çalıştırmanın tüm adımları görüntülendikten sonra, ilk başarısız adımı genişletin.

   ![İlk başarısız adımı Genişlet](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. Beklenen şekilde görünüp görünmediğini onaylamak için başarısız olan adımın girdilerini denetleyin.

1. Belirli bir çalıştırmada her adımın ayrıntılarını gözden geçirin. Çalışma **geçmişi** altında, incelemek istediğiniz çalıştırmayı seçin.

   ![Çalıştırma geçmişini gözden geçirme](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![Bir mantıksal uygulama çalıştırmasının ayrıntılarını görüntüleme](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. Belirli bir adımla ilgili girişleri, çıkışları ve hata iletilerini incelemek için, şeklin genişleyeceği ve ayrıntıları gösterdiği şekilde bu adımı seçin. Örnek:

   ![Adım ayrıntıları görüntüleme](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Çalışma zamanı hata ayıklamayı gerçekleştirme

Hata ayıklamaya yardımcı olmak için, bir Logic App iş akışına, tetikleme ve çalıştırma geçmişinin incelenmesi ile birlikte Tanılama adımları ekleyebilirsiniz. Örneğin, HTTP isteklerini incelemenize ve tam boyutunu, şeklini ve biçimini belirleyebilmeniz için [Web kancası Sınayıcısı](https://webhook.site/) hizmetini kullanan adımları ekleyebilirsiniz.

1. [Web kancası Sınayıcısı](https://webhook.site/) sitesine gidin ve oluşturulan benzersiz URL 'yi kopyalayın.

1. Mantıksal uygulamanızda bir HTTP POST eylemi ve test etmek istediğiniz gövde içeriğini ekleyin; örneğin, bir ifade veya başka bir adım çıktısı.

1. Web kancası test eden URL 'nizi HTTP POST eylemine yapıştırın.

1. Logic Apps altyapısından oluşturulduğunda bir isteğin nasıl biçimlendirildiğini gözden geçirmek için mantıksal uygulamayı çalıştırın ve daha fazla ayrıntı için Web kancası Sınayıcısı sitesini yeniden ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar

* [Mantıksal uygulamanızı izleme](../logic-apps/monitor-logic-apps.md)
