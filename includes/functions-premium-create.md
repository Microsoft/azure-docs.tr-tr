---
title: include dosyası
description: include dosyası
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 10/25/2019
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: 911db285d1ca885142e8a80345926ce76379e3bc
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72958819"
---
1. Azure portal [https://portal.azure.com](https://portal.azure.com) açın

1. **Kaynak oluştur** düğmesini seçin

    ![Kaynak oluştur](./media/functions-create-function-app-portal/function-app-create-resource.png)

1. **İşlem** > **işlev uygulaması**seçin.

    ![Azure portalında işlev uygulaması oluşturma](./media/functions-premium-create/function-app-create-start.png)

1. Görüntünün altındaki tabloda belirtilen işlev uygulaması ayarlarını kullanın.

    ![Temel Bilgiler](./media/functions-premium-create/function-app-create-basics.png)

    | Ayar      | Önerilen değer  | Açıklama |
    | ------------ | ---------------- | ----------- |
    | **Abonelik** | Aboneliğiniz | Bu yeni işlev uygulamasının oluşturulduğu abonelik. |
    | **[Kaynak Grubu](../articles/azure-resource-manager/resource-group-overview.md)** |  *myResourceGroup* | İşlev uygulamanızın oluşturulacağı yeni kaynak grubunun adı. |
    | **İşlev Uygulaması adı** | Genel olarak benzersiz bir ad | Yeni işlev uygulamanızı tanımlayan ad. Geçerli karakterler `a-z` (büyük/küçük harf duyarsız), `0-9`ve `-`.  |
    |**Yayımlama**| Kodlayın | Kod dosyalarını veya Docker kapsayıcısını yayımlama seçeneği. |
    | **Çalışma zamanı yığını** | Tercih edilen dil | Tercih ettiğiniz işlev programlama dilini destekleyen bir çalışma zamanı seçin. C# ve F# için **.NET** işlevlerini seçin. |
    |**Bölge**| Tercih edilen bölge | Kendinize veya işlevinizin erişeceği diğer hizmetlere yakın bir [bölge](https://azure.microsoft.com/regions/) seçin. |

    **Sonraki: barındırma >** düğmesini seçin.

1. Aşağıdaki barındırma ayarlarını girin.

    ![Barındırma](./media/functions-premium-create/function-app-premium-create-hosting.png)

    | Ayar      | Önerilen değer  | Açıklama |
    | ------------ | ---------------- | ----------- |
    | **[Depolama hesabı](../articles/storage/common/storage-quickstart-create-account.md)** |  Genel olarak benzersiz bir ad |  İşlev uygulamanız tarafından kullanılan bir depolama hesabı oluşturun. Depolama hesabı adları 3 ile 24 karakter arasında olmalı ve yalnızca sayıyla küçük harf içermelidir. Ayrıca, [depolama hesabı gereksinimlerini](../articles/azure-functions/functions-scale.md#storage-account-requirements)karşılaması gereken mevcut bir hesabı da kullanabilirsiniz. |
    |**İşletim sistemi**| Tercih edilen işletim sistemi | Çalışma zamanı yığını seçiminize göre sizin için bir işletim sistemi önceden seçilmiştir, ancak gerekirse ayarı değiştirebilirsiniz. |
    | **[Planınızın](../articles/azure-functions/functions-scale.md)** | Premium | Plan türü için **Premium (Önizleme)** öğesini seçin ve *Windows planı* ve *SKU ve boyut* seçimleri için Varsayılanlar ' ı seçin. |

    **Sonraki: izleme >** düğmesini seçin.

1. Aşağıdaki izleme ayarlarını girin.

    ![İzleme](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

    | Ayar      | Önerilen değer  | Açıklama |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Varsayılan | En yakın desteklenen bölgede aynı *uygulama adının* Application Insights kaynağını oluşturur. Bu ayarı genişleterek, **Yeni kaynak adını** değiştirebilir veya verilerinizi depolamak istediğiniz [Azure Coğrafya](https://azure.microsoft.com/global-infrastructure/geographies/) 'da farklı bir **konum** seçebilirsiniz. |

    Uygulama yapılandırma seçimlerini gözden geçirmek için **gözden geçir + oluştur** ' u seçin.

1. İşlev uygulamasını sağlamak ve dağıtmak için **Oluştur**'u seçin.