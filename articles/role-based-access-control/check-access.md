---
title: Hızlı başlangıç-Azure kaynakları için bir kullanıcının erişimini denetleme-Azure RBAC
description: Bu hızlı başlangıçta, Azure portal ve Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak kendiniz veya başka bir kullanıcı için Azure kaynaklarına erişimi nasıl denetleyeceğinizi öğreneceksiniz.
services: role-based-access-control
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: quickstart
ms.workload: identity
ms.date: 12/09/2020
ms.author: rolyon
ms.custom: contperf-fy21q2
ms.openlocfilehash: 5e4f3314ba580dddbd995855bc0f0512b7597107
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98115759"
---
# <a name="quickstart-check-access-for-a-user-to-azure-resources"></a>Hızlı başlangıç: bir kullanıcı için Azure kaynaklarına erişimi denetleme

Bazen bir kullanıcının Azure kaynakları kümesine hangi erişimi olduğunu denetlemeniz gerekir. Atamalarını listeleyerek erişimini kontrol edersiniz. Tek bir kullanıcıya erişimi denetlemeye yönelik hızlı bir yol, **erişim denetimi (IAM)** sayfasındaki **erişimi denetle** özelliğini kullanmaktır.

## <a name="step-1-open-the-azure-resources"></a>1. Adım: Azure kaynaklarını açma

Bir kullanıcının erişimini denetlemek için öncelikle erişimi denetlemek istediğiniz Azure kaynaklarını açmanız gerekir. Azure kaynakları, genellikle *kapsam* olarak adlandırılan düzeyler halinde düzenlenir. Azure 'da, geniş ve dar: yönetim grubu, abonelik, kaynak grubu ve kaynak olmak üzere dört düzeyde bir kapsam belirtebilirsiniz.

![Azure RBAC için kapsam düzeyleri](../../includes/role-based-access-control/media/scope-levels.png)

Erişimini denetlemek istediğiniz Azure kaynakları kümesini açmak için aşağıdaki adımları izleyin.

1. [Azure portalını](https://portal.azure.com) açın.

1. **Yönetim grupları**, **abonelikler**, **kaynak grupları** veya belirli bir kaynak gibi Azure kaynakları kümesini açın.

1. Bu kapsamdaki belirli bir kaynağa tıklayın.

    Aşağıda örnek bir kaynak grubu gösterilmektedir.

    ![Kaynak grubuna genel bakış](./media/shared/rg-overview.png)

## <a name="step-2-check-access-for-a-user"></a>2. Adım: Kullanıcı için erişimi denetleme

Daha önce seçilen Azure kaynaklarına tek bir Kullanıcı, Grup, hizmet sorumlusu veya yönetilen kimlik erişimini denetlemek için bu adımları izleyin.

1. **Erişim denetimi (IAM)** öğesine tıklayın.

    Aşağıda, bir kaynak grubu için erişim denetimi (ıAM) sayfasının bir örneği gösterilmektedir.

    ![Kaynak grubu erişim denetimi-erişim sekmesine bakın](./media/shared/rg-access-control.png)

1. **Erişim denetimi** sekmesinde, **bul** listesinde, erişimi denetlemek istediğiniz kullanıcı, Grup, hizmet sorumlusu veya yönetilen kimlik ' i seçin.

1. Arama kutusuna, görünen adlar, e-posta adresleri veya nesne tanımlayıcıları için dizinde arama yapmak üzere bir dize girin.

    ![Erişim denetimi seçim listesi](./media/shared/rg-check-access-select.png)

1. **Atamalar** bölmesini açmak için güvenlik sorumlusuna tıklayın.

    Bu bölmede, bu kapsamda seçili güvenlik sorumlusu için erişimi görebilir ve bu kapsama devralınmış olursunuz. Alt kapsamların atamaları listelenmez. Aşağıdaki atamaları görürsünüz:

    - Azure RBAC ile rol atamaları eklendi.
    - Azure şemaları veya Azure yönetilen uygulamaları kullanılarak eklenen atamaları engelleyin.
    - Klasik dağıtımlar için klasik hizmet yöneticisi veya Co-Administrator atamaları. 

    ![Bir kullanıcı için rol ve reddetme atamaları bölmesi](./media/shared/rg-check-access-assignments-user.png)

## <a name="step-3-check-your-access"></a>3. Adım: erişiminizi denetleyin

Daha önce seçilen Azure kaynaklarına erişiminizi denetlemek için aşağıdaki adımları izleyin.

1. **Erişim denetimi (IAM)** öğesine tıklayın.

1. **Erişim denetimi** sekmesinde, **erişimimi görüntüle** düğmesine tıklayın.

    Bu kapsamdaki erişimi listeleyen ve bu kapsama devralınmış bir atamalar bölmesi görüntülenir. Alt kapsamların atamaları listelenmez.

    ![Rol ve reddetme atamaları bölmesi](./media/check-access/rg-check-access-assignments.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure portal kullanarak Azure rol atamalarını listeleyin](role-assignments-list-portal.md)
