---
title: include dosyası
description: include dosyası
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/17/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: cd118def8b9caf97f26aaa5ad5dc1f6fb015316d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469294"
---
1. Azure portal menüsünde **kaynak oluştur**' u seçin.

    ![Azure portal menüsünü kullanarak kaynak ekleme](./media/functions-create-function-app-portal/create-function-app-resource.png)

1. **Yeni** sayfasında, **işlem** > **işlev uygulaması**' ni seçin.

1. Görüntünün altındaki tabloda belirtilen işlev uygulaması ayarlarını kullanın.

    ![Temel Bilgiler](./media/functions-create-function-app-portal/function-app-create-basics.png)

    | Ayar      | Önerilen değer  | Açıklama |
    | ------------ | ---------------- | ----------- |
    | **Abonelik** | Aboneliğiniz | Bu yeni işlev uygulamasının oluşturulduğu abonelik. |
    | **[Kaynak Grubu](../articles/azure-resource-manager/resource-group-overview.md)** |  *myResourceGroup* | İşlev uygulamanızın oluşturulacağı yeni kaynak grubunun adı. |
    | **İşlev Uygulaması adı** | Genel olarak benzersiz bir ad | Yeni işlev uygulamanızı tanımlayan ad. Geçerli karakterler `a-z` (büyük/küçük harf duyarsız), `0-9`ve `-`.  |
    |**Yayımlama**| Kod | Kod dosyalarını veya Docker kapsayıcısını yayımlama seçeneği. |
    | **Çalışma zamanı yığını** | Tercih edilen dil | Tercih ettiğiniz işlev programlama dilini destekleyen bir çalışma zamanı seçin. C# ve F# için **.NET** işlevlerini seçin. |
    |**Bölge**| Tercih edilen bölge | Kendinize veya işlevinizin erişeceği diğer hizmetlere yakın bir [bölge](https://azure.microsoft.com/regions/) seçin. |

    **Sonraki: barındırma >** düğmesini seçin.

1. Barındırma için aşağıdaki ayarları girin.

    ![Barındırma](./media/functions-create-function-app-portal/function-app-create-hosting.png)

    | Ayar      | Önerilen değer  | Açıklama |
    | ------------ | ---------------- | ----------- |
    | **[Depolama hesabı](../articles/storage/common/storage-quickstart-create-account.md)** |  Genel olarak benzersiz bir ad |  İşlev uygulamanız tarafından kullanılan bir depolama hesabı oluşturun. Depolama hesabı adları 3 ile 24 karakter arasında olmalı ve yalnızca sayıyla küçük harf içermelidir. Ayrıca, [depolama hesabı gereksinimlerini](../articles/azure-functions/functions-scale.md#storage-account-requirements)karşılaması gereken mevcut bir hesabı da kullanabilirsiniz. |
    |**İşletim sistemi**| Tercih edilen işletim sistemi | Çalışma zamanı yığını seçiminize göre sizin için bir işletim sistemi önceden seçilmiştir, ancak gerekirse ayarı değiştirebilirsiniz. |
    | **[Planınızın](../articles/azure-functions/functions-scale.md)** | Tüketim planı | Kaynakların işlev uygulamanıza nasıl ayrılacağını tanımlayan barındırma planı. Varsayılan **Tüketim Planı**'nda kaynaklar işlevlerin taleplerine göre dinamik olarak eklenir. Bu [sunucusuz](https://azure.microsoft.com/overview/serverless-computing/) barındırmada, yalnızca işlevlerinizin çalıştığı süre için ödeme yaparsınız. Bir App Service planı içinde çalıştırdığınızda [işlev uygulamanızın ölçeklendirmesini](../articles/azure-functions/functions-scale.md) yönetmeniz gerekir.  |

    **Sonraki: izleme >** düğmesini seçin.

1. İzleme için aşağıdaki ayarları girin.

    ![İzleme](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

    | Ayar      | Önerilen değer  | Açıklama |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Varsayılan | En yakın desteklenen bölgede aynı *uygulama adının* Application Insights kaynağını oluşturur. Bu ayarı genişleterek, **Yeni kaynak adını** değiştirebilir veya verilerinizi depolamak istediğiniz [Azure Coğrafya](https://azure.microsoft.com/global-infrastructure/geographies/) 'da farklı bir **konum** seçebilirsiniz. |

    Uygulama yapılandırma seçimlerini gözden geçirmek için **gözden geçir + oluştur** ' u seçin.

1. İşlev uygulamasını sağlamak ve dağıtmak için **Oluştur**'u seçin.

1. Portalın sağ üst köşesindeki Bildirim simgesini seçin ve **Dağıtım başarılı** iletisini bekleyin.

    ![Dağıtım bildirimi](./media/functions-create-function-app-portal/function-app-create-notification2.png)

1. Yeni işlev uygulamanızı görüntülemek için **Kaynağa git**’i seçin. **Panoya sabitle ' yi**de seçebilirsiniz. Sabitleme, panonuzdan bu işlev uygulama kaynağına döndürülmesini kolaylaştırır.
