---
title: include dosyası
description: include dosyası
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2020
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: cdeba09c91bee72508632b8468126150017df7cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98187345"
---
1. Azure portalı menüsünde veya **Giriş** sayfasında **Kaynak oluştur**'u seçin.

1. **Yeni** sayfasında, **işlem**  >  **işlev uygulaması**' yi seçin.

1. **Temel bilgiler** sayfasında, aşağıdaki tabloda belirtilen işlev uygulaması ayarlarını kullanın:

    | Ayar      | Önerilen değer  | Açıklama |
    | ------------ | ---------------- | ----------- |
    | **Abonelik** | Aboneliğiniz | Bu yeni işlev uygulamasının oluşturulduğu abonelik. |
    | **[Kaynak grubu](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | İşlev uygulamanızın oluşturulacağı yeni kaynak grubunun adı. |
    | **İşlev Uygulamasının adı** | Genel olarak benzersiz bir ad | Yeni işlev uygulamanızı tanımlayan ad. Geçerli karakterler şunlardır: `a-z` (büyük/küçük harf duyarsız), `0-9` ve `-`.  |
    |**Yayımla**| Kod | Kod dosyalarını veya Docker kapsayıcısını yayımlama seçeneği. |
    | **Çalışma zamanı yığını** | Tercih edilen dil | Tercih ettiğiniz işlev programlama dilini destekleyen bir çalışma zamanı seçin. Portal içi düzenlemesi Şu anda [Python geliştirmesi](../articles/azure-functions/functions-reference-python.md)için desteklenmiyor.|
    |**Bölge**| Tercih edilen bölge | Size yakın bir bölge seçin ve işlevlerinizin erişebileceği diğer hizmetlere yakın bir [bölge](https://azure.microsoft.com/regions/) seçin. |

1. Ileri 'yi seçin **: barındırma**. **Barındırma** sayfasında, aşağıdaki ayarları girin:

    | Ayar      | Önerilen değer  | Açıklama |
    | ------------ | ---------------- | ----------- |
    | **[Depolama hesabı](../articles/storage/common/storage-account-create.md)** |  Genel olarak benzersiz bir ad |  İşlev uygulamanız tarafından kullanılan bir depolama hesabı oluşturun. Depolama hesabı adları 3 ile 24 karakter arasında olmalı ve yalnızca sayıyla küçük harf içermelidir. Ayrıca, [depolama hesabı gereksinimlerini](../articles/azure-functions/storage-considerations.md#storage-account-requirements)karşılaması gereken mevcut bir hesabı da kullanabilirsiniz. |
    |**İşletim sistemi**| Tercih edilen işletim sistemi | Çalışma zamanı yığını seçiminize göre sizin için bir işletim sistemi önceden seçilmiştir, ancak gerekirse ayarı değiştirebilirsiniz. Python yalnızca Linux üzerinde desteklenir. Portal içi düzenlemesi yalnızca Windows 'da desteklenir.|
    | **[Planlama](../articles/azure-functions/functions-scale.md)** | Premium | Kaynakların işlev uygulamanıza nasıl ayrılacağını tanımlayan barındırma planı. **Premium**' u seçin. Varsayılan olarak, yeni bir App Service planı oluşturulur. Varsayılan **SKU ve boyut** **EP1**, burada EP, _elastik Premium_ için temsil eder. Daha fazla bilgi edinmek için [Premium SKU 'ların listesine](../articles/azure-functions/functions-premium-plan.md#available-instance-skus)bakın.<br/>Premium bir planda JavaScript işlevlerini çalıştırırken, daha az vCPU içeren bir örnek seçmeniz gerekir. Daha fazla bilgi için bkz. [tek çekirdekli Premium planları seçme](../articles/azure-functions/functions-reference-node.md#considerations-for-javascript-functions).  |

1. **İleri: izleme** öğesini seçin. **İzleme** sayfasında, aşağıdaki ayarları girin:

    | Ayar      | Önerilen değer  | Açıklama |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Varsayılan | En yakın desteklenen bölgede aynı *uygulama adının* Application Insights kaynağını oluşturur. Bu ayarı genişleterek, verilerinizi depolamak için **Yeni kaynak adını** değiştirebilir veya [Azure Coğrafya](https://azure.microsoft.com/global-infrastructure/geographies/) 'da farklı bir **konum** seçebilirsiniz. |

1. Uygulama yapılandırma seçimlerini gözden geçirmek için **gözden geçir + oluştur** ' u seçin.

1. **Gözden geçir + oluştur** sayfasında ayarlarınızı gözden geçirin ve sonra işlev uygulamasını sağlamak ve dağıtmak için **Oluştur** ' u seçin.

1. Portalın sağ üst köşesindeki **Bildirimler** simgesini seçin ve **dağıtım başarılı** iletisini izleyin.

1. Yeni işlev uygulamanızı görüntülemek için **Kaynağa git**’i seçin. **Panoya sabitle ' yi** de seçebilirsiniz. Sabitleme, panonuzdan bu işlev uygulama kaynağına döndürülmesini kolaylaştırır.

    ![Dağıtım bildirimi](./media/functions-premium-create/function-app-create-notification2.png)
