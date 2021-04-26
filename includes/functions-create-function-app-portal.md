---
title: include dosyası
description: include dosyası
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/04/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 9ec5920295117b1726a684bcd08534cd6917f7e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100569783"
---
1. Azure portalı menüsünde veya **Giriş** sayfasında **Kaynak oluştur**'u seçin.

1. **Yeni** sayfasında, **işlem**  >  **işlev uygulaması**' yi seçin.

1. **Temel bilgiler** sayfasında, aşağıdaki tabloda belirtilen işlev uygulaması ayarlarını kullanın.

    | Ayar      | Önerilen değer  | Açıklama |
    | ------------ | ---------------- | ----------- |
    | **Abonelik** | Aboneliğiniz | Bu yeni işlev uygulamasının oluşturulduğu abonelik. |
    | **[Kaynak grubu](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | İşlev uygulamanızın oluşturulacağı yeni kaynak grubunun adı. |
    | **İşlev Uygulamasının adı** | Genel olarak benzersiz bir ad | Yeni işlev uygulamanızı tanımlayan ad. Geçerli karakterler şunlardır: `a-z` (büyük/küçük harf duyarsız), `0-9` ve `-`.  |
    |**Yayımla**| Kod | Kod dosyalarını veya Docker kapsayıcısını yayımlama seçeneği. |
    | **Çalışma zamanı yığını** | Tercih edilen dil | Tercih ettiğiniz işlev programlama dilini destekleyen bir çalışma zamanı seçin. Portal içi düzenlemesi yalnızca JavaScript, PowerShell, TypeScript ve C# betiği için kullanılabilir. C# sınıf kitaplığı, Java ve Python işlevleri [yerel olarak geliştirilmelidir](../articles/azure-functions/functions-develop-local.md#local-development-environments).  |
    |**Sürüm**| Sürüm numarası | Yüklü çalışma zamanının sürümünü seçin. |
    |**Bölge**| Tercih edilen bölge | Size yakın bir bölge seçin ve işlevlerinizin erişebileceği diğer hizmetlere yakın bir [bölge](https://azure.microsoft.com/regions/) seçin. |

1. **Sonraki: Barındırma**’ya tıklayın. **Barındırma** sayfasında, aşağıdaki ayarları girin.

    | Ayar      | Önerilen değer  | Açıklama |
    | ------------ | ---------------- | ----------- |
    | **[Depolama hesabı](../articles/storage/common/storage-account-create.md)** |  Genel olarak benzersiz bir ad |  İşlev uygulamanız tarafından kullanılan bir depolama hesabı oluşturun. Depolama hesabı adları 3 ila 24 karakter uzunluğunda olmalı ve yalnızca rakamlar ve küçük harfler içerebilir. Ayrıca, [depolama hesabı gereksinimlerini](../articles/azure-functions/storage-considerations.md#storage-account-requirements)karşılaması gereken mevcut bir hesabı da kullanabilirsiniz. |
    |**İşletim sistemi**| Windows | Çalışma zamanı yığını seçiminize göre sizin için bir işletim sistemi önceden seçilmiştir, ancak gerekirse ayarı değiştirebilirsiniz. Portal içi düzenlemesi yalnızca Windows 'da desteklenir. |
    | **[Planlama](../articles/azure-functions/functions-scale.md)** | **Kullanım (Sunucusuz)** | Kaynakların işlev uygulamanıza nasıl ayrılacağını tanımlayan barındırma planı. Varsayılan **Tüketim** planında kaynaklar işlevlerin taleplerine göre dinamik olarak eklenir. Bu [sunucusuz](https://azure.microsoft.com/overview/serverless-computing/) barındırmada yalnızca işlevlerinizin çalıştığı süre için ödeme yaparsınız. Bir App Service planı içinde çalıştırdığınızda [işlev uygulamanızın ölçeklendirmesini](../articles/azure-functions/functions-scale.md) yönetmeniz gerekir.  |

1. **İleri: izleme** öğesini seçin. **İzleme** sayfasında, aşağıdaki ayarları girin.

    | Ayar      | Önerilen değer  | Açıklama |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Varsayılan | En yakın desteklenen bölgede aynı *uygulama adının* Application Insights kaynağını oluşturur. Bu ayarı genişleterek veya **Yeni oluştur**' u seçerek, Application Insights adını değiştirebilir veya verilerinizi depolamak istediğiniz [Azure Coğrafya](https://azure.microsoft.com/global-infrastructure/geographies/) 'da farklı bir bölge seçebilirsiniz. |

1. Uygulama yapılandırma seçimlerini gözden geçirmek için **gözden geçir + oluştur** ' u seçin.

1. **Gözden geçir + oluştur** sayfasında ayarlarınızı gözden geçirin ve sonra işlev uygulamasını sağlamak ve dağıtmak için **Oluştur** ' u seçin.

1. Portalın sağ üst köşesindeki **Bildirimler** simgesini seçin ve **dağıtım başarılı** iletisini izleyin.

1. Yeni işlev uygulamanızı görüntülemek için **Kaynağa git**’i seçin. **Panoya sabitle ' yi** de seçebilirsiniz. Sabitleme, panonuzdan bu işlev uygulama kaynağına döndürülmesini kolaylaştırır.

    ![Dağıtım bildirimi](./media/functions-create-function-app-portal/function-app-create-notification2.png)
