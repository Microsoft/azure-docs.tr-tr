---
title: Veri erişimi için bir Azure rolü atamak üzere Azure portal kullanma
titleSuffix: Azure Storage
description: Azure rol tabanlı erişim denetimi (Azure RBAC) ile Azure Active Directory güvenlik sorumlusuna izin atamak için Azure portal nasıl kullanacağınızı öğrenin. Azure depolama, Azure AD aracılığıyla kimlik doğrulaması için yerleşik ve Azure özel rollerini destekler.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: d63a31eae57d09f1658f5f19c1518cb0648fa4e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373767"
---
# <a name="use-the-azure-portal-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Azure portalı kullanarak blob ve kuyruk verilerine erişim için Azure rolü atama

Azure Active Directory (Azure AD), [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md)aracılığıyla güvenli kaynaklara erişim hakları verir. Azure depolama, blob veya kuyruk verilerine erişmek için kullanılan ortak izin kümelerini çevreleyen bir dizi Azure yerleşik rol tanımlar.

Azure AD güvenlik sorumlusuna bir Azure rolü atandığında Azure, bu güvenlik sorumlusu için bu kaynaklara erişim izni verir. Erişim, aboneliğin düzeyi, kaynak grubu, depolama hesabı veya tek bir kapsayıcı veya kuyruk kapsamına eklenebilir. Azure AD güvenlik sorumlusu, bir Kullanıcı, Grup, uygulama hizmeti sorumlusu veya [Azure kaynakları için yönetilen bir kimlik](../../active-directory/managed-identities-azure-resources/overview.md)olabilir.

Bu makalede, Azure rolleri atamak için Azure portal nasıl kullanılacağı açıklanır. Azure portal, Azure rolleri atamak ve depolama kaynaklarınıza erişimi yönetmek için basit bir arabirim sağlar. Azure komut satırı araçlarını veya Azure depolama yönetimi API 'Lerini kullanarak blob ve kuyruk kaynakları için Azure rolleri de atayabilirsiniz. Depolama kaynakları için Azure rolleri hakkında daha fazla bilgi için bkz. [Azure Active Directory kullanarak Azure bloblarına ve kuyruklara erişim kimlik doğrulaması](storage-auth-aad.md).

## <a name="azure-roles-for-blobs-and-queues"></a>Blob 'lar ve kuyruklar için Azure rolleri

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Kaynak kapsamını belirleme

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-azure-roles-using-the-azure-portal"></a>Azure portal kullanarak Azure rolleri atama

Bir rol ataması için uygun kapsamı belirledikten sonra, Azure portal bu kaynağa gidin. Kaynak için **Access Control (IAM)** ayarlarını görüntüleyin ve rol atamalarını yönetmek için aşağıdaki yönergeleri izleyin:

1. Azure AD güvenlik sorumlusuna erişim vermek için uygun Azure depolama Azure rolünü atayın.

1. Azure Resource Manager [okuyucusu](../../role-based-access-control/built-in-roles.md#reader) rolünü, Azure AD kimlik bilgilerini kullanarak Azure Portal kapsayıcı veya kuyruklara erişmesi gereken kullanıcılara atayın.

Aşağıdaki bölümlerde bu adımların her biri daha ayrıntılı olarak açıklanır.

> [!IMPORTANT]
> Bir Azure depolama hesabı oluşturduğunuzda, Azure AD aracılığıyla verilere erişim için otomatik olarak izinler atanmamıştır. Azure depolama için kendinize açık bir Azure rolü atamanız gerekir. Aboneliğiniz, kaynak grubunuz, depolama hesabınız veya Kapsayıcınız ya da kuyruğunuzun düzeyinde atayabilirsiniz.
>
> Veri erişimi için kendinize bir rol atamadan önce, Azure portal Azure portal aracılığıyla Depolama hesabınızdaki verilere erişebilirsiniz. bu nedenle, veri erişimi için hesap anahtarını da kullanabilir. Daha fazla bilgi için bkz. [Azure Portal blob verilerine erişimi yetkilendirmeyi seçme](../blobs/authorize-data-operations-portal.md).
>
> Depolama hesabı bir Azure Resource Manager salt okuma kilidi ile kilitliyse kilit, depolama hesabı kapsamındaki Azure RBAC rollerinin veya bir veri kapsayıcısının (blob kapsayıcısı veya kuyruğu) atanmasını engeller.

### <a name="assign-an-azure-built-in-role"></a>Azure yerleşik rolü atama

Bir güvenlik sorumlusuna rol atamadan önce, verdiğiniz izinlerin kapsamını göz önünde bulundurduğunuzdan emin olun. Uygun kapsama karar vermek için [kaynak kapsamını belirleme](#determine-resource-scope) bölümünü gözden geçirin.

Burada gösterilen yordam, bir kapsayıcıya kapsamlı bir rol atar, ancak bir sıraya kapsamlı bir rol atamak için aynı adımları izleyebilirsiniz:

1. [Azure Portal](https://portal.azure.com)depolama hesabınıza gidin ve hesabın **genel bakışını** görüntüleyin.
1. Hizmetler altında **Bloblar**' ı seçin.
1. Rolü atamak istediğiniz kapsayıcıyı bulun ve kapsayıcının ayarlarını görüntüleyin.
1. Kapsayıcının erişim denetimi ayarlarını göstermek için **erişim denetimi (IAM)** seçeneğini belirleyin. Rol atamalarının listesini görmek için **rol atamaları** sekmesini seçin.

    :::image type="content" source="media/storage-auth-aad-rbac-portal/portal-access-control-container.png" alt-text="Kapsayıcı erişim denetimi ayarlarını gösteren ekran görüntüsü":::

1. Yeni bir rol eklemek için **rol ataması Ekle** düğmesine tıklayın.
1. **Rol ataması Ekle** penceresinde, atamak Istediğiniz Azure depolama rolünü seçin. Ardından, bu rolü atamak istediğiniz güvenlik sorumlusunu bulmak için arama yapın.

    :::image type="content" source="media/storage-auth-aad-rbac-portal/add-rbac-role.png" alt-text="Azure rolü atamayı gösteren ekran görüntüsü":::

1. **Kaydet**’e tıklayın. Rolü atadığınız kimlik söz konusu rol altında listelenir. Örneğin, aşağıdaki görüntüde Kullanıcı tarafından eklenen kullanıcının, *örnek kapsayıcı* adlı kapsayıcıda bulunan veriler için okuma izinleri olduğunu gösterir.

    :::image type="content" source="media/storage-auth-aad-rbac-portal/container-scoped-role.png" alt-text="Bir role atanan kullanıcı listesini gösteren ekran görüntüsü":::

Depolama hesabı, kaynak grubu veya aboneliğe kapsamlı bir rol atamak için benzer adımları izleyebilirsiniz.

### <a name="assign-the-reader-role-for-portal-access"></a>Portal erişimi için okuyucu rolünü atama

Azure depolama için bir güvenlik sorumlusuna yerleşik veya özel bir rol atadığınızda, Depolama hesabınızdaki veriler üzerinde işlem gerçekleştirmek üzere bu güvenlik sorumlusuna izinler vermiş olursunuz. Yerleşik **veri okuyucu** rolleri bir kapsayıcı veya kuyruktaki veriler için okuma izinleri sağlar, ancak yerleşik **veri katılımcısı** rolleri bir kapsayıcı veya sıraya yönelik okuma, yazma ve silme izinleri sağlar. İzinler belirtilen kaynak kapsamına alınır.  
Örneğin, **Depolama Blobu veri katılımcısı** rolünü **örnek kapsayıcısı** adlı bir kapsayıcı düzeyinde Kullanıcı Mary 'ye atarsanız, Mary, bu kapsayıcıdaki tüm bloblara okuma, yazma ve silme erişimi verilir.

Ancak, Mary Azure portal bir blobu görüntülemek isterse, **Depolama Blobu veri katılımcısı** rolü kendisini görüntülemek için, portalda blob 'a gezinmek için yeterli izinleri sağlamaz. Portalda gezinmek ve görünen diğer kaynakları görüntülemek için ek Azure AD izinleri gereklidir.

Kullanıcılarınızın Azure portal bloblara erişebilmeleri gerekiyorsa, bu kullanıcılara depolama hesabı düzeyinde veya yukarıdaki bir ek Azure rolü, [okuyucu](../../role-based-access-control/built-in-roles.md#reader) rolü atayın. **Okuyucu** rolü, kullanıcıların depolama hesabı kaynaklarını görüntülemesine izin veren, ancak değiştirmelerini sağlayan bir Azure Resource Manager roldür. Azure depolama 'daki verilere yönelik okuma izinleri sağlamaz, ancak yalnızca hesap yönetimi kaynakları için geçerlidir.

Kullanıcının Azure portal bloblara erişebilmeleri için **okuyucu** rolünü atamak için aşağıdaki adımları izleyin. Bu örnekte, atama depolama hesabı kapsamıdır:

1. [Azure portalında](https://portal.azure.com) depolama hesabınıza gidin.
1. Depolama hesabının erişim denetimi ayarlarını göstermek için **erişim denetimi (IAM)** seçeneğini belirleyin. Rol atamalarının listesini görmek için **rol atamaları** sekmesini seçin.
1. **Rol ataması Ekle** penceresinde **okuyucu** rolünü seçin. 
1. **Erişim ata** ALANıNDAN **Azure AD Kullanıcı, Grup veya hizmet sorumlusu**' nı seçin.
1. Rolü atamak istediğiniz güvenlik sorumlusunu bulmak için arama yapın.
1. Rol atamasını kaydedin.

**Okuyucu** rolünü atamak yalnızca Azure Portal kullanarak blob 'lara veya kuyruklara erişmesi gereken kullanıcılar için gereklidir.

> [!IMPORTANT]
> Azure portal Depolama Gezgini önizleme sürümü, blob veya kuyruk verilerini görüntülemek ve değiştirmek için Azure AD kimlik bilgilerini kullanmayı desteklemez. Azure portal Depolama Gezgini, her zaman verilere erişmek için hesap anahtarlarını kullanır. Azure portal Depolama Gezgini kullanmak için **Microsoft. Storage/storageAccounts/ListKeys/Action** içeren bir rol atanmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

- Depolama kaynakları için Azure rolleri hakkında daha fazla bilgi için bkz. [Azure Active Directory kullanarak Azure bloblarına ve kuyruklara erişim kimlik doğrulaması](storage-auth-aad.md). 
- Azure RBAC hakkında daha fazla bilgi edinmek için bkz. [Azure rol tabanlı erişim denetimi (Azure RBAC) nedir?](../../role-based-access-control/overview.md).
- Azure rol atamalarını Azure PowerShell, Azure CLı veya REST API ile atamayı ve yönetmeyi öğrenmek için şu makalelere bakın:
    - [Azure PowerShell modülünü kullanarak Azure rol atamaları ekleme veya kaldırma](../../role-based-access-control/role-assignments-powershell.md)
    - [Azure CLı kullanarak Azure rol atamaları ekleme veya kaldırma](../../role-based-access-control/role-assignments-cli.md)
    - [REST API kullanarak Azure rol atamalarını ekleme veya kaldırma](../../role-based-access-control/role-assignments-rest.md)
- Depolama uygulamalarınızın içinden kapsayıcılara ve kuyruklara erişim yetkisi verme hakkında bilgi edinmek için bkz. Azure [Storage uygulamalarıyla Azure ad kullanma](storage-auth-aad-app.md).
