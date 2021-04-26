---
title: Azure rol tabanlı erişim denetimi-Özel Görüntü İşleme
titleSuffix: Azure Cognitive Services
description: Bu makalede, Özel Görüntü İşleme projeleriniz için Azure rol tabanlı erişim denetimini nasıl yapılandıracağınız gösterilir.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: d265b6698a87c1c651dff1413cf48dd4984cd606
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94616901"
---
# <a name="azure-role-based-access-control"></a>Azure rol tabanlı erişim denetimi

Özel Görüntü İşleme, Azure kaynaklarına bireysel erişimi yönetmeye yönelik bir yetkilendirme sistemi olan Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) destekler. Azure RBAC kullanarak, farklı takım üyelerine Özel Görüntü İşleme projeleriniz için farklı düzeylerde izinler atarsınız. Azure RBAC hakkında daha fazla bilgi için bkz. [Azure RBAC belgeleri](../../role-based-access-control/index.yml).

## <a name="add-role-assignment-to-custom-vision-resource"></a>Özel Görüntü İşleme kaynağına rol ataması Ekle

Azure RBAC, bir Özel Görüntü İşleme kaynağına atanabilir. Bir Azure kaynağına erişim izni vermek için, bir rol ataması eklersiniz.
1. [Azure portalda](https://ms.portal.azure.com/)**Tüm hizmetler**’i seçin. 
1. Ardından bilişsel **Hizmetler**' i seçin ve özel özel görüntü işleme eğitim kaynağına gidin.
   > [!NOTE]
   > Azure RBAC 'yi tüm kaynak grupları, abonelikler veya yönetim grupları için de ayarlayabilirsiniz. İstenen kapsam düzeyini seçip istenen öğeye (örneğin, **kaynak grupları** ' nı seçip istediğiniz kaynak grubunuza tıklayarak) gidip bunu yapın.
1. Sol gezinti bölmesinde **erişim denetimi (IAM)** seçeneğini belirleyin.
1. Bu kapsamın rol atamalarını görüntülemek için **rol atamaları** sekmesini seçin.
1.   ->  **Rol Ekle ataması** Ekle ' yi seçin.
1. **Rol** açılan listesinde, eklemek istediğiniz bir rol seçin.
1. **Seç** listesinde, bir Kullanıcı, Grup, hizmet sorumlusu veya yönetilen kimlik seçin. Listede güvenlik sorumlusunu görmüyorsanız, görünen adlar, e-posta adresleri ve nesne tanımlayıcıları için dizinde arama yapmak üzere seçim kutusunu yazabilirsiniz.
1. Rolü atamak için **Kaydet**’i seçin.

Birkaç dakika içinde, hedefe seçili kapsamda seçili rol atanır.

## <a name="custom-vision-role-types"></a>Özel Görüntü İşleme rol türleri

Özel Görüntü İşleme kaynaklarınızın erişim ihtiyaçlarını öğrenmek için aşağıdaki tabloyu kullanın.

|Rol  |İzinler  |
|---------|---------|
|`Cognitive Services Custom Vision Contributor`     | Proje oluşturma, düzenleme veya silme özelliği de dahil olmak üzere projelere tam erişim.        |
|`Cognitive Services Custom Vision Trainer`     | Proje oluşturma veya silme imkanından hariç olmak üzere tam erişim. Traıners projeleri görüntüleyebilir ve düzenleyebilir, modelleri eğitebilir, yayımlayabilir, yayımdan kaldırabilir veya dışarı aktarabilir.        |
|`Cognitive Services Custom Vision Labeler`     | Eğitim görüntülerini karşıya yükleme, düzenleme veya silme, etiketleri oluşturma, ekleme, kaldırma veya silme özelliği. Etiketleyiciler projeleri görüntüleyebilir, ancak eğitim görüntüleri ve Etiketler dışında herhangi bir şeyi güncelleştiremez.         |
|`Cognitive Services Custom Vision Deployment`     | Modelleri yayımlama, yayımdan kaldırma veya dışa aktarma özelliği. Dağıtımcılar projeleri görüntüleyebilir, ancak bir projeyi, eğitim görüntülerini veya etiketleri güncelleştiremez.        |
|`Cognitive Services Custom Vision Reader`     | Projeleri görüntüleme özelliği. Okuyucular hiçbir değişiklik yapamaz.        |