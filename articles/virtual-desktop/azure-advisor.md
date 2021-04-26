---
title: Windows sanal masaüstünü Azure Danışmanı ile tümleştirme-Azure
description: Azure Advisor 'ı Windows sanal masaüstü dağıtımınızla kullanma.
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: b141d3829e9d98cb793d2038e93331804d2e6b8f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448296"
---
# <a name="use-azure-advisor-with-windows-virtual-desktop"></a>Azure Advisor 'ı Windows sanal masaüstü ile kullanma

Azure Danışmanı, kullanıcıların destek taleplerine dosya eklemek zorunda kalmadan ortak sorunları kendi kendine çözümlemesine yardımcı olabilir. Öneriler, yardım istekleri gönderme gereksinimini azaltarak zaman ve maliyet tasarrufu sağlar.

Bu makalede, Windows sanal masaüstü dağıtımınızda kullanıcılarınıza yardımcı olması için Azure Advisor 'ın nasıl ayarlanacağı açıklanır.

## <a name="what-is-azure-advisor"></a>Azure Danışmanı nedir?

Azure Advisor, sık karşılaşılan sorunları çözmeye yönelik kişiselleştirilmiş öneriler sunmak için yapılandırmalarınızı ve telemetrinizi analiz eder. Bu önerilerle, Azure kaynaklarınızı güvenilirlik, güvenlik, işlemsel üstün, performans ve maliyet açısından iyileştirebilirsiniz. [Azure Advisor Web sitesinden](https://azure.microsoft.com/services/advisor/)daha fazla bilgi edinin.

## <a name="how-to-start-using-azure-advisor"></a>Azure Advisor 'ı kullanmaya başlama

Kullanmaya başlamak için tek yapmanız gereken Azure portal bir Azure hesabıdır. İlk olarak, konumundaki Azure portal açın ve <https://portal.azure.com/#home> aşağıdaki görüntüde gösterildiği gibi **Azure hizmetleri** altında **danışman** ' yi seçin. Ayrıca, Azure portal arama çubuğuna "Azure Advisor" girebilirsiniz.

> [!div class="mx-imgBorder"]
> ![Azure portal ekran görüntüsü. Kullanıcı fare imlecini Azure Advisor bağlantısı üzerine düşerek açılan menünün görünmesine neden olur.](media/azure-advisor.png)

Azure Advisor 'ı açtığınızda beş kategori görürsünüz:

- Maliyet
- Güvenlik
- Güvenilirlik
- İşlem Mükemmelliği
- Performans

> [!div class="mx-imgBorder"]
> ![Her kategori için beş menü gösteren Azure Advisor 'ın ekran görüntüsü. Kendi kutularında görüntülenen beş öğe maliyet, güvenlik, güvenilirlik, Işlemsel üstün ve performanslarıdır.](media/advisor-categories.png)

Bir kategori seçtiğinizde, etkin öneriler sayfasına gidebilirsiniz. Bu sayfada, aşağıdaki görüntüde gösterildiği gibi, Azure Advisor 'ın sizin için hangi önerilere sahip olduğunu görüntüleyebilirsiniz.

> [!div class="mx-imgBorder"]
> ![Işlemsel üstün kullanım için etkin öneriler listesinin ekran görüntüsü. Listede, değişen öncelik düzeylerine sahip yedi öneri gösterilmektedir.](media/active-suggestions.png)

## <a name="additional-tips-for-azure-advisor"></a>Azure Danışmanı için ek ipuçları

- Önerileriniz en az bir haftada bir kez daha sık kontrol ettiğinizden emin olun. Azure Advisor, etkin önerilerini günde birden çok kez güncelleştirir. Yeni önerilere yönelik denetim, daha küçük olanları bir kez oluşturup çözmenize yardımcı olarak daha büyük sorunları önleyebilir.

- Her zaman Azure Advisor 'daki en yüksek öncelik düzeyiyle sorunları çözmeyi deneyin. Yüksek öncelikli sorunlar kırmızıyla işaretlenir. Yüksek öncelikli önerilerden ayrıldığınızda, bu, satırdaki sorunlara yol açabilir.

- Öneri daha az önemliyse, bunu kapatabilir veya erteleyebilirsiniz. Bir öneriyi kapatmak veya ertelemek için **eylem** sütununa gidin ve öğenin durumunu değiştirin.

- Neden görüneceğimizi ve sizin veya kullanıcılarınız üzerinde olumsuz bir etkiye sahip olmadığından emin olana kadar önerileri kapatmayın. Sorunun ne olduğunu görmek için her zaman **daha fazla bilgi edinin** ' i seçin. Azure Advisor 'daki yönergeleri izleyerek bir sorunu gideremezseniz, otomatik olarak listeden kaybolacaktır. Sorunları tekrar tekrar ertelemeden daha iyi bir şekilde çözeceğiz.

- Windows sanal masaüstündeki bir sorun boyunca her zaman ilk olarak Azure Advisor ' ı kontrol edin. Azure Danışmanı, sorunun nasıl çözüleceğini gösteren yönergeler verir veya en azından size yardımcı olabilecek bir kaynağa doğru bir yol gösterilir.

## <a name="next-steps"></a>Sonraki adımlar

Önerilerin nasıl çözümleneceğini öğrenmek için bkz. [Azure Advisor önerilerini çözümleme](azure-advisor-recommendations.md).
