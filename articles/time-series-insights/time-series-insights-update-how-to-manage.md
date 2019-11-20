---
title: Önizleme ortamı sağlama ve yönetme-Azure zaman serisi | Microsoft Docs
description: Azure Time Series Insights önizleme ortamı sağlamayı ve yönetmeyi öğrenin.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/23/2019
ms.custom: seodec18
ms.openlocfilehash: f66925c20f6767286eb98a7f5fab180845da4c33
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014791"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Azure Time Series Insights önizlemeyi sağlayın ve yönetin

Bu makalede, [Azure Portal](https://portal.azure.com/)kullanılarak Azure Time Series Insights önizleme ortamının nasıl oluşturulacağı ve yönetileceği açıklanmaktadır.

## <a name="overview"></a>Genel Bakış

Azure Time Series Insights Preview ortamları Kullandıkça Öde (PAYG) ortamlardır.

Azure Time Series Insights bir önizleme ortamı sağladığınızda şu Azure kaynaklarını oluşturursunuz:

* Azure Time Series Insights önizleme ortamı  
* Azure depolama genel amaçlı v1 hesabı
* Daha hızlı ve sınırsız sorgu için isteğe bağlı bir sıcak mağaza
  
[Ortamınızı nasıl planlayacağınızı](./time-series-insights-update-plan.md)öğrenin.

Her Azure Time Series Insights önizleme ortamını bir olay kaynağıyla ilişkilendirin. Daha fazla bilgi için, [Olay Hub 'ı kaynağı ekleme](./time-series-insights-how-to-add-an-event-source-eventhub.md) ve [IoT Hub kaynağı ekleme](./time-series-insights-how-to-add-an-event-source-iothub.md)makalesini okuyun. Bu adım sırasında zaman damgası KIMLIĞI özelliği ve benzersiz bir tüketici grubu sağlarsınız. Bunun yapılması, ortamın uygun olaylara erişmesini sağlar.

> [!NOTE]
> Önceki adım, sağlama iş akışında, zaman serisi önizleme ortamını oluştururken isteğe bağlıdır. Bununla birlikte, verilerin o ortamda akışa başlaması için ortama bir olay kaynağı eklemeniz gerekir.

Sağlama tamamlandıktan sonra, erişim ilkelerinizi ve diğer ortam özniteliklerini iş gereksinimlerinize uyacak şekilde değiştirebilirsiniz.

## <a name="create-the-environment"></a>Ortamı oluşturma

Azure Time Series Insights önizleme ortamı oluşturmak için:

1. **SKU** menüsünde, **PAYG** düğmesini seçin. Bir ortam adı sağlayın ve kullanılacak abonelik grubunu ve kaynak grubunu seçin. Ardından, ortamı barındırmak için desteklenen bir konum seçin.

   [![bir Azure Time Series Insights örneği oluşturun.](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

1. Bir zaman serisi KIMLIĞI girin.

    >[!NOTE]
    > * Zaman serisi KIMLIĞI, büyük/küçük harfe duyarlıdır ve sabittir. (Ayarlandıktan sonra değiştirilemez.)
    > * Zaman serisi kimlikleri en fazla üç anahtar olabilir.
    > * Zaman serisi KIMLIĞI seçme hakkında daha fazla bilgi için, okuma [zaman SERISI kimliği seçin](./time-series-insights-update-how-to-id.md).

1. Bir depolama hesabı adı seçerek ve bir çoğaltma seçeneği belirleyerek bir Azure depolama hesabı oluşturun. Bunun yapılması, otomatik olarak bir Azure depolama genel amaçlı v1 hesabı oluşturur. Hesap, daha önce seçtiğiniz Azure Time Series Insights önizleme ortamıyla aynı bölgede oluşturulur.

    [örneğiniz için bir Azure depolama hesabı oluşturun ![](media/v2-update-manage/manage-five.png)](media/v2-update-manage/manage-five.png#lightbox)

1. **(Isteğe bağlı)** Ortamınızdaki en son veriler üzerinde daha hızlı ve sınırsız sorgu istiyorsanız ortamınız için ısınma mağazasını etkinleştirin. Ayrıca, bir Time Series Insights önizleme ortamı oluşturduktan sonra sol gezinti bölmesindeki **depolama yapılandırması** seçeneği aracılığıyla bir sıcak mağaza oluşturabilir veya silebilirsiniz.

1. **(Isteğe bağlı)** Şimdi bir olay kaynağı ekleyebilirsiniz. Alternatif olarak, örnek sağlanana kadar bekleyebilirsiniz.

   * Time Series Insights, [azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) ve [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) 'yi olay kaynağı seçenekleri olarak destekler. Ortamı oluştururken yalnızca tek bir olay kaynağı ekleyebilseniz de daha sonra başka bir olay kaynağı ekleyebilirsiniz. Olay kaynağını eklediğinizde, mevcut bir tüketici grubunu seçebilir veya yeni bir tüketici grubu oluşturabilirsiniz. Tüm olayların Azure Time Series Insights önizleme ortamınızda göründüğünden emin olmak için benzersiz bir tüketici grubu oluşturmak en iyisidir.

   * Uygun zaman damgası özelliğini seçin. Varsayılan olarak, Azure Time Series Insights her olay kaynağı için ileti temelli sıraya alma süresini kullanır.

     > [!TIP]
     > İleti temelli sıraya alma saati, toplu olay senaryolarında veya geçmiş verileri karşıya yükleme senaryolarında kullanılacak en iyi yapılandırılmış ayar olmayabilir. Bu gibi durumlarda, bir zaman damgası özelliği kullanmayın veya kullanma kararını doğruladığınızdan emin olun.

     [![olay kaynağı sekmesi](media/v2-update-manage/manage-two.png)](media/v2-update-manage/manage-two.png#lightbox)

1. Ortamınızın istediğiniz ayarlarla sağlandığını doğrulayın.

    [![gözden geçir + Oluştur sekmesi](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

## <a name="manage-the-environment"></a>Ortamı yönetme

Azure Time Series Insights önizleme ortamınızı Azure portal kullanarak yönetebilirsiniz. Azure portal aracılığıyla yönetirken, Kullandıkça Öde Azure Time Series Insights önizleme ortamı ve genel olarak kullanılabilir S1 veya S2 ortamları arasında birkaç temel fark görürsünüz:

* Azure portal **genel bakış** dikey penceresi, aşağıdaki yollarla Azure Time Series Insights değiştirilmez:
  * Kapasite, Kullandıkça Öde ortamları için uygulanmadığından kaldırılır.
  * Zaman serisi KIMLIĞI özelliği eklendi. Verilerinizin bölümlenme şeklini belirler.
  * Başvuru veri kümeleri kaldırılır.
  * Görüntülenmiş URL sizi [Azure Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md)'a yönlendirir.
  * Azure depolama hesabınızın adı listelenir.

* Azure portal **yapılandırma** dikey penceresi Azure Time Series Insights önizlemede KALDıRıLARAK, PAYG ortamları yapılandırılamaz. Ancak, Yeni tanıtılan yarı depoyu yapılandırmak için **depolama yapılandırması** 'nı kullanabilirsiniz.

* Başvuru verileri Kullandıkça Öde ortamlarının parçası olmadığından, Azure portal **başvuru verileri** dikey penceresi Azure Time Series Insights önizlemede kaldırılır.

[Azure portal ![Time Series Insights önizleme ortamı](media/v2-update-manage/manage-four.png)](media/v2-update-manage/manage-four.png#lightbox)

## <a name="next-steps"></a>Sonraki adımlar

- [Ortamınızın planını](./time-series-insights-update-plan.md)okuyarak, genel olarak kullanılabilir ortamlar ve önizleme ortamları Time Series Insights hakkında daha fazla bilgi edinin.

- [Bir olay hub 'ı kaynağı eklemeyi](./time-series-insights-how-to-add-an-event-source-eventhub.md)öğrenin.

- [IoT Hub 'ı kaynağı](./time-series-insights-how-to-add-an-event-source-iothub.md)yapılandırın.
