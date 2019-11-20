---
title: RBAC-Azure depolama ile blob ve kuyruk verilerinde Azure AD erişim haklarını yönetmek için Azure portal kullanın | Microsoft Docs
description: Güvenlik sorumlularına kapsayıcılara ve kuyruklara erişim atamak için Azure portal rol tabanlı erişim denetimi (RBAC) kullanın. Azure depolama, Azure AD aracılığıyla kimlik doğrulaması için yerleşik ve özel RBAC rollerini destekler.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ad88066ebf19bdcc9bcdb77309ce76828c09ce47
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671133"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-in-the-azure-portal"></a>Azure portal RBAC ile Azure Blob ve kuyruk verilerine erişim verme

Azure Active Directory (Azure AD), [rol tabanlı erişim denetimi (RBAC)](../../role-based-access-control/overview.md)aracılığıyla güvenli kaynaklara erişim haklarını yetkilendirir. Azure depolama, blob veya kuyruk verilerine erişmek için kullanılan ortak izin kümelerini çevreleyen yerleşik RBAC rollerinin bir kümesini tanımlar. 

Azure AD güvenlik sorumlusuna bir RBAC rolü atandığında Azure, bu güvenlik sorumlusu için bu kaynaklara erişim izni verir. Erişim, aboneliğin düzeyi, kaynak grubu, depolama hesabı veya tek bir kapsayıcı veya kuyruk kapsamına eklenebilir. Azure AD güvenlik sorumlusu, bir Kullanıcı, Grup, uygulama hizmeti sorumlusu veya [Azure kaynakları için yönetilen bir kimlik](../../active-directory/managed-identities-azure-resources/overview.md)olabilir.

Bu makalede, RBAC rolleri atamak için Azure portal nasıl kullanılacağı açıklanır. Azure portal RBAC rolleri atamak ve depolama kaynaklarınıza erişimi yönetmek için basit bir arabirim sağlar. Ayrıca, Azure komut satırı araçlarını veya Azure depolama yönetimi API 'Lerini kullanarak blob ve kuyruk kaynakları için RBAC rolleri atayabilirsiniz. Depolama kaynakları için RBAC rolleri hakkında daha fazla bilgi için bkz. [Azure Active Directory kullanarak Azure bloblarına ve kuyruklara erişim kimlik doğrulaması](storage-auth-aad.md). 

## <a name="rbac-roles-for-blobs-and-queues"></a>Blob 'lar ve kuyruklar için RBAC rolleri

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Kaynak kapsamını belirleme 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Azure portal kullanarak RBAC rolleri atama

Bir rol ataması için uygun kapsamı belirledikten sonra, Azure portal bu kaynağa gidin. Kaynak için **Access Control (IAM)** ayarlarını görüntüleyin ve rol atamalarını yönetmek için aşağıdaki yönergeleri izleyin:

1. Azure AD güvenlik sorumlusuna erişim sağlamak için uygun Azure Storage RBAC rolünü atayın.

1. Azure Resource Manager [okuyucusu](../../role-based-access-control/built-in-roles.md#reader) rolünü, Azure AD kimlik bilgilerini kullanarak Azure Portal kapsayıcı veya kuyruklara erişmesi gereken kullanıcılara atayın. 

Aşağıdaki bölümlerde bu adımların her biri daha ayrıntılı olarak açıklanır.

> [!NOTE]
> Bir Azure depolama hesabınızın sahibi olarak, otomatik olarak veri erişim izni atanmaz. Siz açıkça kendiniz bir RBAC rolü için Azure depolama atamanız gerekir. Aboneliğinizi aboneliğiniz, kaynak grubunuz, depolama hesabınız veya bir kapsayıcı veya kuyruk düzeyinde atayabilirsiniz.
> 
> Depolama hesabınızda hiyerarşik bir ad alanı etkinse bir kapsayıcıya veya kuyruğa kapsamlı bir rol atayamazsınız.

### <a name="assign-a-built-in-rbac-role"></a>Yerleşik RBAC rolü atama

Bir güvenlik sorumlusuna rol atamadan önce, verdiğiniz izinlerin kapsamını göz önünde bulundurduğunuzdan emin olun. Uygun kapsama karar vermek için [kaynak kapsamını belirleme](#determine-resource-scope) bölümünü gözden geçirin.

Burada gösterilen yordam, bir kapsayıcıya kapsamlı bir rol atar, ancak bir sıraya kapsamlı bir rol atamak için aynı adımları izleyebilirsiniz: 

1. [Azure Portal](https://portal.azure.com)depolama hesabınıza gidin ve hesabın **genel bakışını** görüntüleyin.
1. Hizmetler altında **Bloblar**' ı seçin. 
1. Rolü atamak istediğiniz kapsayıcıyı bulun ve kapsayıcının ayarlarını görüntüleyin. 
1. Kapsayıcının erişim denetimi ayarlarını göstermek için **erişim denetimi (IAM)** seçeneğini belirleyin. Rol atamalarının listesini görmek için **rol atamaları** sekmesini seçin.

    ![Kapsayıcı erişim denetimi ayarlarını gösteren ekran görüntüsü](media/storage-auth-aad-rbac-portal/portal-access-control-container.png)

1. Yeni bir rol eklemek için **rol ataması Ekle** düğmesine tıklayın.
1. **Rol ataması Ekle** penceresinde, atamak Istediğiniz Azure depolama rolünü seçin. Ardından, bu rolü atamak istediğiniz güvenlik sorumlusunu bulmak için arama yapın.

    ![RBAC rolü atamayı gösteren ekran görüntüsü](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. **Kaydet**’e tıklayın. Rolü atadığınız kimlik söz konusu rol altında listelenir. Örneğin, aşağıdaki görüntüde Kullanıcı tarafından eklenen kullanıcının, *örnek kapsayıcı*adlı kapsayıcıda bulunan veriler için okuma izinleri olduğunu gösterir.

    ![Bir role atanan kullanıcı listesini gösteren ekran görüntüsü](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

Depolama hesabı, kaynak grubu veya aboneliğe kapsamlı bir rol atamak için benzer adımları izleyebilirsiniz.

### <a name="assign-the-reader-role-for-portal-access"></a>Portal erişimi için okuyucu rolünü atama

Azure depolama için bir güvenlik sorumlusuna yerleşik veya özel bir rol atadığınızda, Depolama hesabınızdaki veriler üzerinde işlem gerçekleştirmek üzere bu güvenlik sorumlusuna izinler vermiş olursunuz. Yerleşik **veri okuyucu** rolleri bir kapsayıcı veya kuyruktaki veriler için okuma izinleri sağlar, ancak yerleşik **veri katılımcısı** rolleri bir kapsayıcı veya sıraya yönelik okuma, yazma ve silme izinleri sağlar. İzinler belirtilen kaynak kapsamına alınır.  

Örneğin, **Depolama Blobu veri katılımcısı** rolünü **örnek kapsayıcısı**adlı bir kapsayıcı düzeyinde Kullanıcı Mary 'ye atarsanız, Mary, bu kapsayıcıdaki tüm bloblara okuma, yazma ve silme erişimi verilir.

Ancak, Mary Azure portal bir blobu görüntülemek isterse, **Depolama Blobu veri katılımcısı** rolü kendisini görüntülemek için, portalda blob 'a gezinmek için yeterli izinleri sağlamaz. Portalda gezinmek ve görünen diğer kaynakları görüntülemek için ek Azure AD izinleri gereklidir.

Kullanıcılarınızın Azure portal bloblara erişebilmeleri gerekiyorsa, bu kullanıcılara, depolama hesabı düzeyinde veya daha yüksek olan başka bir RBAC rolü, [okuyucu](../../role-based-access-control/built-in-roles.md#reader) rolü atayın. **Okuyucu** rolü, kullanıcıların depolama hesabı kaynaklarını görüntülemesine izin veren, ancak değiştirmelerini sağlayan bir Azure Resource Manager roldür. Azure depolama 'daki verilere yönelik okuma izinleri sağlamaz, ancak yalnızca hesap yönetimi kaynakları için geçerlidir.

Kullanıcının Azure portal bloblara erişebilmeleri için **okuyucu** rolünü atamak için aşağıdaki adımları izleyin. Bu örnekte, atama depolama hesabı kapsamıdır:

1. [Azure Portal](https://portal.azure.com)depolama hesabınıza gidin.
1. Depolama hesabının erişim denetimi ayarlarını göstermek için **erişim denetimi (IAM)** seçeneğini belirleyin. Rol atamalarının listesini görmek için **rol atamaları** sekmesini seçin.
1. **Rol ataması Ekle** penceresinde **okuyucu** rolünü seçin. 
1. **Erişim ata** ALANıNDAN **Azure AD Kullanıcı, Grup veya hizmet sorumlusu**' nı seçin.
1. Rolü atamak istediğiniz güvenlik sorumlusunu bulmak için arama yapın.
1. Rol atamasını kaydedin.

> [!NOTE]
> Okuyucu rolünü atamak yalnızca Azure portal kullanarak blob 'lara veya kuyruklara erişmesi gereken kullanıcılar için gereklidir. 

## <a name="next-steps"></a>Sonraki adımlar

- Depolama kaynakları için RBAC rolleri hakkında daha fazla bilgi için bkz. [Azure Active Directory kullanarak Azure bloblarına ve kuyruklara erişim kimlik doğrulaması](storage-auth-aad.md). 
- RBAC hakkında daha fazla bilgi edinmek için bkz. [rol tabanlı erişim denetimi (RBAC) nedir?](../../role-based-access-control/overview.md).
- Azure PowerShell, Azure CLı veya REST API ile RBAC rol atamalarını atamayı ve yönetmeyi öğrenmek için şu makalelere bakın:
    - [Rol tabanlı erişim denetimi 'ni (RBAC) Azure PowerShell ile yönetme](../../role-based-access-control/role-assignments-powershell.md)
    - [Rol tabanlı erişim denetimi 'ni (RBAC) Azure CLı ile yönetme](../../role-based-access-control/role-assignments-cli.md)
    - [Rol tabanlı erişim denetimi 'ni (RBAC) REST API ile yönetme](../../role-based-access-control/role-assignments-rest.md)
- Depolama uygulamalarınızın içinden kapsayıcılara ve kuyruklara erişim yetkisi verme hakkında bilgi edinmek için bkz. Azure [Storage uygulamalarıyla Azure ad kullanma](storage-auth-aad-app.md).
