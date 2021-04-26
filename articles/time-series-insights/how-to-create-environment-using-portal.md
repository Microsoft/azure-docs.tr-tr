---
title: Azure portal-Azure Time Series Insights Gen2 kullanarak bir Gen2 ortamı ayarlama | Microsoft Docs
description: Azure portal kullanarak Azure Time Series Insights Gen2 'de ortam ayarlamayı öğrenin.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: 09068d966df871d4b6804978a543db50bccbee37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952856"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-portal"></a>Azure portal kullanarak Azure Time Series Insights Gen2 ortamı oluşturma

Bu makalede [Azure Portal](https://portal.azure.com/)kullanılarak Azure Time Series Insights Gen2 ortamının nasıl oluşturulacağı açıklanmaktadır.

Ortam sağlama öğreticisi, süreç boyunca size yol gösterecektir. Doğru zaman serisi KIMLIĞINI seçme ve iki JSON yükten örnekleri görüntüleme hakkında bilgi edineceksiniz.</br>

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWzk3P]

## <a name="overview"></a>Genel Bakış

Bir Azure Time Series Insights Gen2 ortamı sağladığınızda şu Azure kaynaklarını oluşturursunuz:

* Kullandıkça Öde fiyatlandırma modelini izleyen bir Azure Time Series Insights Gen2 ortamı
* Bir Azure depolama hesabı
* Daha hızlı ve sınırsız sorgu için isteğe bağlı bir sıcak mağaza

> [!TIP]
>
> * [Ortamınızı nasıl planlayacağınızı](./how-to-plan-your-environment.md)öğrenin.
> * [Bir olay hub 'ı kaynağı ekleme](./how-to-ingest-data-event-hub.md) veya [IoT Hub kaynağı ekleme](./how-to-ingest-data-iot-hub.md)hakkında bilgi edinin.

Şunları öğrenirsiniz:

1. Her Azure Time Series Insights Gen 2 ortamını bir olay kaynağıyla ilişkilendirin. Ayrıca, ortamın uygun olaylara erişimi olduğundan emin olmak için bir zaman damgası KIMLIĞI özelliği ve benzersiz bir tüketici grubu da sağlarsınız.

1. Sağlama tamamlandıktan sonra, erişim ilkelerinizi ve diğer ortam özniteliklerini, iş gereksinimlerinize uyacak şekilde değiştirebilirsiniz.

   > [!NOTE]
   > Bir ortam sağlanırken ilk adım isteğe bağlıdır. Bu adımı atlarsanız, verilerin ortamınıza akmasını ve sorgu aracılığıyla erişilebilir olması için, daha sonra ortama bir olay kaynağı eklemeniz gerekir.

## <a name="create-the-environment"></a>Ortamı oluşturma

Azure Time Series Insights Gen 2 ortamı oluşturmak için:

1. [Azure Portal](https://portal.azure.com/)üzerinde *Nesnelerin İnterneti* altında bir Azure Time Series Insights kaynağı oluşturun.

1. **Katman** olarak **Gen2 (L1)** seçeneğini belirleyin. Bir ortam adı sağlayın ve kullanılacak abonelik grubunu ve kaynak grubunu seçin. Ardından, ortamı barındırmak için desteklenen bir konum seçin.

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration.png" alt-text="Azure Time Series Insights bir örnek oluşturun." lightbox="media/how-to-create-environment-using-portal/environment-configuration.png":::

1. Bir zaman serisi KIMLIĞI girin.

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration-2.png" alt-text="Devam eden bir Azure Time Series Insights ortam yapılandırması oluşturun." lightbox="media/how-to-create-environment-using-portal/environment-configuration-2.png":::

   > [!NOTE]
   >
   > * Zaman serisi KIMLIĞI, *büyük/küçük harfe duyarlıdır* ve *sabittir*. (Ayarlandıktan sonra değiştirilemez.)
   > * Zaman serisi kimlikleri en fazla *üç* anahtar olabilir. Bunu, ortamınızdaki verileri gönderen her bir cihaz algılayıcısını benzersiz bir şekilde temsil eden bir veritabanında birincil anahtar olarak düşünün. Bu, bir özellik veya en fazla üç özelliğin bir birleşimi olabilir.
   > * [Zaman SERISI kimliğini seçme](./how-to-select-tsid.md) hakkında daha fazla bilgi edinin

1. Bir depolama hesabı adı, hesap türü seçerek ve bir [çoğaltma](../storage/common/redundancy-migration.md?tabs=portal) seçeneği belirleyerek bir Azure depolama hesabı oluşturun. Bunun yapılması, otomatik olarak bir Azure depolama hesabı oluşturur. Varsayılan olarak, [genel amaçlı v2](../storage/common/storage-account-overview.md) hesabı oluşturulur. Hesap, daha önce seçtiğiniz Azure Time Series Insights Gen2 ortamı ile aynı bölgede oluşturulur.
Alternatif olarak, yeni bir Azure zaman serisi Gen2 ortamı oluştururken kendi depolama alanınızı (BYOS) [Azure Resource Manager şablonu](./time-series-insights-manage-resources-using-azure-resource-manager-template.md) aracılığıyla da getirebilirsiniz.

1. **(Isteğe bağlı)** Ortamınızdaki en son veriler üzerinde daha hızlı ve sınırsız sorgu istiyorsanız ortamınız için ısınma mağazasını etkinleştirin. Ayrıca, bir Azure Time Series Insights Gen2 ortamı oluşturduktan sonra sol gezinti bölmesindeki **depolama yapılandırması** seçeneği aracılığıyla bir sıcak mağaza oluşturabilir veya silebilirsiniz.

1. **(Isteğe bağlı)** Şimdi bir olay kaynağı ekleyebilirsiniz. Örnek sağlandıktan sonra da bekleyebilirsiniz.

   * Azure Time Series Insights, [azure IoT Hub](./how-to-ingest-data-iot-hub.md) ve [Azure Event Hubs](./how-to-ingest-data-event-hub.md) 'yi olay kaynağı seçenekleri olarak destekler. Ortamı oluştururken yalnızca tek bir olay kaynağı ekleyebilseniz de daha sonra başka bir olay kaynağı ekleyebilirsiniz.

     Olay kaynağını eklediğinizde, mevcut bir tüketici grubunu seçebilir veya yeni bir tüketici grubu oluşturabilirsiniz. Olay kaynağının, veri okumak için ortamınızda benzersiz bir tüketici grubu kullandığından emin olun.

   * Uygun zaman damgası özelliğini seçin. Varsayılan olarak, Azure Time Series Insights her olay kaynağı için ileti temelli sıraya alma süresini kullanır.

     > [!TIP]
     > İleti temelli sıraya alma saati, toplu olay senaryolarında veya geçmiş verileri karşıya yükleme senaryolarında kullanılacak en iyi yapılandırılmış ayar olmayabilir. Bu gibi durumlarda, bir zaman damgası özelliği kullanmayın veya kullanma kararını doğruladığınızdan emin olun.

   :::image type="content" source="media/how-to-create-environment-using-portal/configure-event-source.png" alt-text="Olay kaynağı Yapılandırma sekmesi" lightbox="media/how-to-create-environment-using-portal/configure-event-source.png":::

1. Ortamınızın sağlandığını ve istediğiniz şekilde yapılandırıldığını onaylamak için **gözden geçir + oluştur** ' u seçin.

    :::image type="content" source="media/how-to-create-environment-using-portal/environment-confirmation.png" alt-text="Gözden geçir + Oluştur sekmesi" lightbox="media/how-to-create-environment-using-portal/environment-confirmation.png":::

## <a name="next-steps"></a>Sonraki adımlar

* [Ortamınızın planını](./how-to-plan-your-environment.md)okuyarak, genel olarak kullanılabilir ortamlar ve Gen2 ortamları Azure Time Series Insights hakkında daha fazla bilgi edinin.
* Azure Time Series Insights Gen2 ortamınız için [akış alma olay kaynakları](./concepts-streaming-ingestion-event-sources.md) hakkında bilgi edinin.
* [Ortamınızı yönetme](./how-to-provision-manage.md)hakkında daha fazla bilgi edinin.
