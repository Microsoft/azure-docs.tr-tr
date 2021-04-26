---
title: Bu Azure Sentinel çalışma kitabıyla veri bağlayıcılarınızın sistem durumunu izleyin | Microsoft Docs
description: Veri bağlayıcılarınızın bağlantısının ve performansının izlenmesini sağlamak için sistem durumu Izleme çalışma kitabını kullanın.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2020
ms.author: yelevin
ms.openlocfilehash: 7d52b76601a617f62ae5b10fa38841ef2608bf49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94656999"
---
# <a name="monitor-the-health-of-your-data-connectors-with-this-azure-sentinel-workbook"></a>Bu Azure Sentinel çalışma kitabıyla veri bağlayıcılarınızın sistem durumunu izleyin

**Veri bağlayıcıları sistem durumu izleme çalışma kitabı** , veri bağlayıcılarınızın sistem durumunu, bağlantısını ve performansını Azure Sentinel içinden izlemenize olanak tanır. Çalışma kitabı ek izleyiciler sağlar, anormallikleri algılar ve çalışma alanının veri alma durumu hakkında öngörü sağlar. Alınan verilerin genel durumunu izlemek ve özel görünümler ve kural tabanlı uyarılar oluşturmak için çalışma kitabının mantığını kullanabilirsiniz.

## <a name="use-the-health-monitoring-workbook"></a>Sistem durumu izleme çalışma kitabını kullanma

1. Azure Sentinel portalından, **tehdit yönetimi** menüsünden **çalışma kitapları** ' nı seçin.

1. **Çalışma kitapları** galerisinde, arama çubuğuna *sistem durumu* girin ve sonuçlar arasından **veri toplama sistem durumu izleme** ' yi seçin.

1. Çalışma kitabını olduğu gibi kullanmak için **şablonu görüntüle** ' yi seçin veya çalışma kitabının düzenlenebilir bir kopyasını oluşturmak için **Kaydet** ' i seçin. Kopya oluşturulduğunda, **kaydedilmiş çalışma kitabını görüntüle**' yi seçin.

1. Çalışma kitabında bir kez daha önce görüntülemek istediğiniz **aboneliği** ve **çalışma alanını** seçin, ardından verileri gereksinimlerinize göre filtrelemek için **timerange** tanımlayın. Çalışma kitabının yerinde açıklamasını görüntülemek için **Yardım göster** ' i kullanın.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-1.png" alt-text="Veri Bağlayıcısı sistem durumu izleme çalışma kitabı giriş sayfası" lightbox="media/monitor-data-connector-health/data-health-workbook-1.png":::

Bu çalışma kitabında üç sekmeli bölüm vardır:

1. **Genel bakış** sekmesi, seçilen çalışma alanındaki verilerin genel durumunu gösterir: birim ÖLÇÜMLERI, EPS ücretleri ve son günlük alma süresi.

1. **Veri toplama bozukluileri** sekmesi, veri toplama işlemindeki anormallikleri tablo ve veri kaynağına göre tespit etmenize yardımcı olur. Her sekme belirli bir tabloya yönelik bozukluklar sunar ( **genel** sekmesi bir tablo koleksiyonu içerir). **Anomali puanı** döndüren **series_decompose_anomalies ()** işlevi kullanılarak hesaplanır. [Bu işlev hakkında daha fazla bilgi edinin](/azure/data-explorer/kusto/query/series-decompose-anomaliesfunction?WT.mc_id=Portal-fx). Değerlendirmek için işlevin aşağıdaki parametrelerini ayarlayın:

    - **AnomaliesTimeRange**: Bu zaman seçicisi yalnızca veri toplam anomalileri görünümde geçerlidir.
    - **SampleInterval**: Verilerin örneklendiği zaman aralığı. Anomali puanı yalnızca son aralıktaki veriler üzerinde hesaplanır.
    - **PositiveAlertThreshold**: Bu değer, pozitif anomali puanı eşiğini tanımlar. Ondalık değerleri kabul eder.
    - **NegativeAlertThreshold**: Bu değer, negatif anomali puanı eşiğini tanımlar. Ondalık değerleri kabul eder.

        :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-2.png" alt-text="Veri Bağlayıcısı sistem durumu izleme çalışma kitabı bozuklukları sayfası" lightbox="media/monitor-data-connector-health/data-health-workbook-2.png":::

1. **Aracı bilgisi** sekmesi, farklı makinelerinizde yüklü olan Log Analytics aracılarının sistem durumu hakkında, Azure VM, DIĞER bulut VM, ŞIRKET içi VM veya fiziksel olarak ilgili bilgileri gösterir. Aşağıdakileri izleyebilirsiniz:

   - Sistem konumu

   - Sinyal durumu ve gecikme süresi

   - Kullanılabilir bellek ve disk alanı

   - Aracı işlemleri

    Bu bölümde, makinelerinizi destekleyen sekmeyi seçmeniz gerekir: yalnızca Azure Arc ile yönetilen makineleri görüntülemek istiyorsanız, **Azure tarafından yönetilen makineler** sekmesini seçin; Log Analytics Aracısı yüklü olan hem yönetilen hem de Azure dışı makineleri görüntülemek için **tüm makineler** sekmesini seçin.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-3.png" alt-text="Veri Bağlayıcısı sistem durumu izleme çalışma kitabı Aracısı bilgi sayfası" lightbox="media/monitor-data-connector-health/data-health-workbook-3.png":::

## <a name="next-steps"></a>Sonraki adımlar
Verilerinizi [Azure Sentinel 'e](quickstart-onboard.md)ekleme, [veri kaynaklarını bağlama](connect-data-sources.md)ve [verilerinizin görünürlüğünü ve olası tehditleri nasıl sağlayacağınızı](quickstart-get-visibility.md)öğrenin.