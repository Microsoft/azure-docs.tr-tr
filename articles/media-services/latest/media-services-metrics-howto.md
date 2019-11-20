---
title: Azure İzleyici ile ölçümlerini görüntüleme
description: Bu makalede, Azure portal grafikleri ve Azure CLI ile ölçümleri izlemek gösterilmektedir.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: d331dc4eb0c6668426e1ab1a01a1dd1dcebe0c85
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795817"
---
# <a name="monitor-media-services-metrics"></a>Media Services ölçümlerini izleyin 

[Azure İzleyici](../../azure-monitor/overview.md) ölçümlerini izleme ve yardımcı olacak tanılama günlüklerini anlama, uygulamalarınızın performansını sağlar. Bu ayrıntılı açıklaması için özellik ve neden Azure Media Services ölçümleri ve tanılama günlüklerini kullanmak istiyorsunuz görmek için bkz: [İzleyici Media Services ölçümleri ve tanılama günlüklerini](media-services-metrics-diagnostic-logs.md).

Azure İzleyici, bunları portalda grafik, REST API aracılığıyla erişmesini veya bunları sorgulama gibi ölçümleri ile etkileşim kurmak için çeşitli yollar sağlar CLI kullanarak. Bu makalede, Azure portal grafikleri ve Azure CLI ile ölçümleri izlemek gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

- [Bir Media Services hesabı oluşturma](create-account-cli-how-to.md)
- Gözden geçirme [İzleyici Media Services ölçümleri ve tanılama günlükleri](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Azure portalında ölçümlerini görüntüleme

1. [https://portal.azure.com](https://portal.azure.com ) adresinden Azure portalında oturum açın.
1. Azure Media Services hesabınıza gidin ve seçin **ölçümleri**.
1. Tıklayın **kaynak** kutusuna ve ölçümleri izlemek istediğiniz kaynağı seçin. 

    **Bir kaynak seçin** size mevcut olan kaynakların listesini ile sağdaki penceresi görüntülenir. Bu durumda görürsünüz 

    * &lt;Media Services hesap adı&gt;
    * &lt;Media Services hesap adına&gt;/&lt;akış uç noktası adı&gt;
    * &lt;Depolama hesabı adı&gt;

    Kaynak seçip ENTER tuşuna **Uygula**. Desteklenen kaynakları ve ölçümler hakkında daha fazla ayrıntı için bkz: [İzleyici Media Services ölçüm](media-services-metrics-diagnostic-logs.md).
 
    ![Ölçümler](media/media-services-metrics/metrics02.png)
    
    > [!NOTE]
    > Ölçümleri izlemek istediğiniz kaynakları arasında geçiş yapmak için tıklatın **kaynak** yeniden kutusuna ve bu adımı yineleyin.
1. (İsteğe bağlı olarak), grafik, bir ad (düzenleme en üstünde kalemin tuşuna basarak adı) verin.
1. Görüntülemek istediğiniz ölçümleri ekleyin.

    ![Ölçümler](media/media-services-metrics/metrics03.png)
1. Grafiği panonuza sabitleyebilirsiniz.

## <a name="view-metrics-with-azure-cli"></a>Azure CLI ile ölçümlerini görüntüleme

CLI ile "Çıkış" ölçümleri almak için aşağıdaki çalıştıracağınız `az monitor metrics` CLI komutunu:

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Diğer ölçümleri almak için "Çıkış" ilgilendiğiniz ölçüm adı yerine.

## <a name="see-also"></a>Ayrıca bkz.

* [Azure İzleyici ölçümleri](../../azure-monitor/platform/data-platform.md)
* [Oluşturun, görüntüleyin ve Azure İzleyicisi'ni kullanarak ölçüm Uyarıları yönetme](../../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Sonraki adımlar

[Tanılama günlükleri](media-services-diagnostic-logs-howto.md)
