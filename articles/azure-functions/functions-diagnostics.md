---
title: Azure Işlevleri tanılamaları genel bakış
description: Azure Işlevleri Tanılama ile işlev uygulamanızla ilgili sorunları nasıl giderebileceğinizi öğrenin.
keywords: işlevler, Tanılamalar, destek, sunucusuz, sorun giderme, kendi kendine yardım
author: yunjchoi
manager: gwallace
ms.service: azure-functions
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: 96bfc133ef390f34712151733277ae6f9e9a8d70
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73722460"
---
# <a name="azure-functions-diagnostics-overview"></a>Azure Işlevleri tanılamaları genel bakış

Bir işlev uygulaması çalıştırırken, hataları tetikleyebilmesi için 4xx hatasından kaynaklanan tüm sorunlar için hazırlıklı olmak istersiniz. Azure Işlevleri tanılama, işlev uygulamanızda yapılandırma veya ek maliyet olmadan sorun gidermenize yardımcı olacak akıllı ve etkileşimli bir deneyimdir. İşlev uygulamanızla ilgili sorunlar yaşıyorsanız, Azure Işlevleri Tanılaması, sorunu daha kolay ve hızlı bir şekilde gidermeye ve çözmeye yönelik doğru bilgilere kılavuzluk etmek için neyin yanlış olduğunu gösterir. Bu makalede, yaygın işlev uygulaması sorunlarını daha hızlı tanılamak ve çözmek için Azure Işlevleri tanılamayı kullanma hakkında temel bilgiler verilmektedir.

## <a name="start-azure-functions-diagnostics"></a>Azure Işlevleri tanılamayı başlatma

Azure Işlevleri tanılamaları 'na erişmek için:

1. [Azure Portal](https://portal.azure.com)işlev uygulamanıza gidin.
2. **Platform özellikleri** sekmesini seçin.
3. Azure Işlevleri tanılamayı açan **kaynak yönetimi**altındaki **sorunları Tanıla ve çöz** ' ü seçin.
4. Giriş sayfası kutucuğunda anahtar sözcükleri kullanarak işlev uygulamanızın sorununu en iyi şekilde açıklayan bir kategori seçin. Ayrıca, arama çubuğunda sorununuzu en iyi açıklayan bir anahtar sözcük yazabilirsiniz. Örneğin, işlev uygulaması yürütmeyle ilgili tanılama raporlarının listesini görmek ve bunları doğrudan giriş sayfasından açmak için `execution` yazabilirsiniz.

![Değiştirir](./media/functions-diagnostics/homepage.png)

## <a name="use-the-interactive-interface"></a>Etkileşimli arabirimi kullanma

İşlev uygulamanızın sorunuyla en iyi şekilde hizalanan bir giriş sayfası kategorisi seçtiğinizde, Azure Işlevleri Tanılama ' etkileşimli arabirimi, Genie, uygulamanızın sorununu tanılamak ve çözmek için size yol gösterebilir. İlgilendiğiniz sorun kategorisinin tam Tanılama raporunu görüntülemek için Genie tarafından sunulan kutucuk kısayollarını kullanabilirsiniz. Kutucuk kısayolları, tanılama ölçümlerinize erişmenin doğrudan bir yolunu sağlar.

![Genie](./media/functions-diagnostics/genie.png)

Bir kutucuk seçtikten sonra kutucukta açıklanan sorunla ilgili konuların bir listesini görebilirsiniz. Bu konular, tüm rapordaki önemli bilgi parçacıklarını sağlar. Sorunları daha fazla araştırmak için bu konulardan herhangi birini seçebilirsiniz. Ayrıca, tek bir sayfadaki tüm konuları araştırmak için **tam raporu görüntüle** seçeneğini belirleyebilirsiniz.

![Tanılama raporunun önizlemesi](./media/functions-diagnostics/preview-of-diagnostic-report.png)

## <a name="view-a-diagnostic-report"></a>Tanılama raporunu görüntüle

Bir konu seçtikten sonra, işlev uygulamanıza özgü bir tanılama raporu görüntüleyebilirsiniz. Tanılama raporları, uygulamanız ile ilgili herhangi bir sorun olup olmadığını göstermek için durum simgeleri kullanır. Sorunun ayrıntılı açıklaması, Önerilen Eylemler, ilgili ölçümler ve yararlı belgeler görürsünüz. Özelleştirilmiş tanılama raporları, işlev uygulamanızda çalıştırılan bir denetim serisinden oluşturulur. Tanılama raporları, işlev uygulamanızda sorun giderme için yararlı bir araç olabilir ve sorunu çözmeye yönelik size rehberlik eder.

## <a name="find-the-problem-code"></a>Sorun kodunu bulun 

Betik tabanlı işlevler için, özel durumlara veya hatalara neden olan kod satırında daraltmak üzere **işlev uygulaması azaltma veya raporlama hataları altında** **Işlev yürütme** ve hataları kullanabilirsiniz. Bu özellik, temel nedeni almak ve belirli bir kod satırından sorunları düzeltmek için yararlı bir araç olabilir. Bu seçenek, önceden derlenmiş C# ve Java işlevleri için kullanılamaz.

![İşlev yürütme hatalarında tanılama raporu](./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png)

![İşlev özel durumu](./media/functions-diagnostics/function-exception.png)

## <a name="next-steps"></a>Sonraki adımlar

[UserVoice](https://feedback.azure.com/forums/355860-azure-functions)'Ta Azure işlevleri tanılamaları hakkında sorular sorabilir veya geri bildirim sağlayabilirsiniz. Lütfen geri bildirimlerinizin başlığına `[Diag]` ekleyin.

> [!div class="nextstepaction"]
> [İşlev uygulamalarınızı izleyin](functions-monitoring.md)
