---
title: include dosyası
description: include dosyası
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/30/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5b537b88052ce4042e346732f3dc63aaec6621cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100529740"
---
1. **Çözüm Gezgini**, projeye sağ tıklayın ve **Yayımla** ' yı seçin ve **hedefte** **Azure** ' ı seçin ve ardından **İleri**' yi seçin.

1. Belirli bir **hedef** Için, Windows üzerinde çalışan bir işlev uygulaması oluşturan **Azure işlev uygulaması (Windows)** öğesini seçin.

1. **Işlev örneği**' nde **Yeni bir Azure işlevi oluştur ' u seçin...** 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-new-resource.png" alt-text="Yeni bir işlev uygulaması örneği oluştur":::

1. Aşağıdaki tabloda belirtilen değerleri kullanarak yeni bir örnek oluşturun:

    | Ayar      | Değer  | Açıklama                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Ad** | Genel olarak benzersiz bir ad | Yeni işlev uygulamanızı benzersiz şekilde tanımlayan ad. Bu adı kabul edin veya yeni bir ad girin. Geçerli karakterler şunlardır: `a-z` , `0-9` , ve `-` . |
    | **Abonelik** | Aboneliğiniz | Kullanılacak Azure aboneliği. Bu aboneliği kabul edin veya açılan listeden yeni bir tane seçin. |
    | **[Kaynak grubu](../articles/azure-resource-manager/management/overview.md)** | Kaynak grubunuzun adı |  İşlev uygulamanızın oluşturulacağı kaynak grubu. Açılan listeden var olan bir kaynak grubunu seçin veya yeni bir kaynak grubu oluşturmak için **Yeni** ' yi seçin.|
    | **[Plan türü](../articles/azure-functions/functions-scale.md)** | Tüketim | Projenizi bir [Tüketim planında](../articles/azure-functions/consumption-plan.md)çalışan bir işlev uygulamasında yayımladığınızda yalnızca işlevler uygulamanızın yürütmeleri için ödeme yaparsınız. Diğer barındırma planları daha yüksek maliyetler doğurur. |
    | **Konum** | App Service 'in konumu | Size yakın bir [bölgede](https://azure.microsoft.com/regions/) veya işlevlerinizin erişebileceği diğer hizmetlerden bir **konum** seçin. |
    | **[Azure Depolama](../articles/azure-functions/storage-considerations.md)** | Genel amaçlı depolama hesabı | Işlevler çalışma zamanı için bir Azure depolama hesabı gereklidir. Genel amaçlı bir depolama hesabı yapılandırmak için **Yeni** ' yi seçin. [Depolama hesabı gereksinimlerini](../articles/azure-functions/storage-considerations.md#storage-account-requirements)karşılayan mevcut bir hesabı da seçebilirsiniz.  |

    ![App Service Oluştur iletişim kutusu](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Azure 'da bir işlev uygulaması ve ilgili kaynakları oluşturmak için **Oluştur** ' u seçin. Kaynak oluşturma durumu pencerenin sol alt kısmında gösterilir. 

1. **İşlevler örneğine** geri döndüğünüzde, **paket dosyasından Çalıştır** ' ın işaretli olduğundan emin olun. İşlev uygulamanız, [paket Çalıştır](../articles/azure-functions/run-functions-from-deployment-package.md) modu etkin olan [ZIP dağıtımı](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) kullanılarak dağıtılır. Bu, daha iyi performans ile sonuçlandığından, işlevler projeniz için önerilen dağıtım yöntemidir. 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-4.png" alt-text="Profil oluşturmayı tamamlama":::

1. **Son**' u seçin ve Yayımla sayfasında, proje dosyalarınızı Içeren paketi Azure 'daki yeni işlev uygulamanıza dağıtmak için **Yayımla** ' yı seçin. 

    Dağıtım tamamlandıktan sonra, Azure 'da işlev uygulamasının kök URL 'SI **Yayımla** sekmesinde gösterilir. 
    
1.  Yayımla sekmesinde, **Cloud Explorer 'Da Yönet**' i seçin. Bu, Cloud Explorer 'da yeni işlev uygulaması Azure kaynağı ' nı açar. 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Başarı iletisi yayımlama":::
    
    Cloud Explorer, Visual Studio 'Yu kullanarak sitenin içeriğini görüntüleyebilir, işlev uygulamasını başlatıp durdurabilir ve doğrudan Azure 'daki ve Azure portal uygulama kaynaklarına gidebilir. 
