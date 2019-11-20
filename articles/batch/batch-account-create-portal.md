---
title: Azure portal bir hesap oluşturun-Azure Batch | Microsoft Docs
description: Büyük ölçekli paralel iş yükleri bulutta çalıştırmak için Azure portalda bir Azure Batch hesabı oluşturmayı öğrenin
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5cceb7cc179f78d6b6d7350e7c4f6c31bb9cbfed
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095723"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Azure portalıyla Batch hesabı oluşturma

[Azure portal][azure_portal]Azure Batch hesap oluşturmayı ve işlem senaryonuza uyan hesap özelliklerini kullanmayı öğrenin. Erişim anahtarları ve hesap URL’leri gibi önemli hesap özelliklerini nerede bulabileceğinizi öğrenin.

Batch hesapları ve senaryoları hakkında arka plan bilgileri için bkz. [özelliğe genel bakış](batch-api-basics.md).

## <a name="create-a-batch-account"></a>Batch hesabı oluşturma

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. [Azure Portal][azure_portal] oturum açın.

1. **Kaynak oluştur** > **İşlem** > **Batch Hizmeti**'ni seçin.

    ![Market’te Batch][marketplace_portal]

1. **Yeni Batch hesabı** ayarlarını girin. Aşağıdaki ayrıntılara bakın.

    ![Batch hesabı oluşturma][account_portal]

    a. **Abonelik**: Batch hesabının oluşturulacağı abonelik. Yalnızca bir aboneliğiniz varsa, varsayılan olarak seçilidir.

    b. **Kaynak grubu**: Yeni Batch hesabınız için mevcut bir kaynak grubu seçin ya da isteğe bağlı olarak yeni bir tane oluşturun.

    c. **Hesap adı**: Seçtiğiniz ad, hesabın oluşturulduğu Azure bölgesi içinde benzersiz olmalıdır (aşağıdaki **konuma** bakın). Hesap adı yalnızca küçük harfler, sayılar içerebilir ve 3-24 karakter uzunluğunda olmalıdır.

    d. **Konum**: Batch hesabının oluşturulacağı Azure bölgesi. Yalnızca aboneliğiniz ve kaynak grubunuz tarafından desteklenen bölgeler seçenek olarak görüntülenir.

    e. **Depolama hesabı**: Batch hesabınızla ilişkilendirdiğiniz isteğe bağlı bir Azure depolama hesabı. En iyi performans için genel amaçlı v2 depolama hesabı önerilir. Toplu Işteki tüm depolama hesabı seçenekleri için bkz. [Batch özelliğine genel bakış](batch-api-basics.md#azure-storage-account). Portalda, var olan bir depolama hesabını seçin veya yeni bir tane oluşturun.

      ![Depolama hesabı oluşturma][storage_account]

    f. **Havuz ayırma modu**: **Gelişmiş** ayarlar sekmesinde, havuz ayırma modunu **Batch hizmeti** veya **Kullanıcı aboneliği**olarak belirtebilirsiniz. Çoğu senaryo için varsayılan **Batch hizmetini**kabul edin.

      ![Toplu işlem havuzu ayırma modu][pool_allocation]

1. Hesabı oluşturmak için **Oluştur**'u seçin.

## <a name="view-batch-account-properties"></a>Batch hesabı özelliklerini görüntüleme

Hesap oluşturulduktan sonra seçerek ayarlarına ve özelliklerine erişebilirsiniz. Sol menüyü kullanarak tüm hesap ayarlarına ve özelliklerine erişebilirsiniz.

![Azure portalında Batch hesabı sayfası][account_blade]

* **Batch hesabı adı, URL ve anahtarlar**: [Batch API 'leriyle](batch-apis-tools.md#azure-accounts-for-batch-development)bir uygulama geliştirirken, Batch kaynaklarınıza erişmeniz için bir hesap URL 'si ve anahtar gerekir. (Batch, Azure Active Directory kimlik doğrulamasını da destekler.)

    Batch hesabı erişim bilgilerini görüntülemek için **Anahtarlar**'ı seçin.

    ![Azure portalında Batch hesabı anahtarları][account_keys]

* Batch hesabınızla ilişkili depolama hesabının adını ve anahtarlarını görüntülemek için **Depolama hesabı**'nı seçin.

* Batch hesabı için geçerli olan kaynak kotalarını görüntülemek için **Kotalar**'ı seçin. Ayrıntılar için bkz. [Batch hizmeti kotaları ve limitleri](batch-quota-limit.md).

## <a name="additional-configuration-for-user-subscription-mode"></a>Kullanıcı aboneliği modu için ek yapılandırma

Kullanıcı aboneliği modunda bir Batch hesabı oluşturmayı seçerseniz, hesabı oluşturmadan önce aşağıdaki ek adımları gerçekleştirin.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Azure Batch hizmetinin aboneliğe erişmesine izin verme (tek seferlik işlem)

Kullanıcı aboneliği modunda ilk Batch hesabınızı oluştururken, aboneliğinizi Batch hizmetine kaydetmeniz gerekir. (Bunu daha önce yaptıysanız sonraki bölüme atlayın.)

1. [Azure Portal][azure_portal] oturum açın.

1. **Tüm hizmetler** > **Abonelikler**'i ve ardından Batch hesabı için kullanmak istediğiniz aboneliği seçin.

1. **Abonelik** sayfasında **Kaynak sağlayıcıları**'nı seçip **Microsoft.Batch**'i arayın. **Microsoft.Batch** kaynak sağlayıcısının aboneliğe kayıtlı olup olmadığını kontrol edin. Kayıtlı değilse **Kaydet** bağlantısını seçin.

    ![Microsoft.Batch sağlayıcısını kaydetme][register_provider]

1. **Abonelik** sayfasında, **erişim denetimi (IAM)**  > **rol atamaları** > **rol ataması Ekle**' yi seçin.

    ![Abonelik erişim denetimi][subscription_access]

1. **Rol ataması Ekle** sayfasında, **katkıda bulunan** rolünü seçin ve Batch API 'sini arayın. API'yi bulana kadar aşağıdaki dizelerden her birini arayın:
    1. **MicrosoftAzureBatch**.
    1. **Microsoft Azure Batch**. Daha yeni Azure AD kiracıları bu adı kullanıyor olabilir.
    1. Batch API'sinin kimliği: **ddbf3205-c6bd-46ae-8127-60eb93363864**.

1. Batch API'sini bulduktan sonra seçin ve **Kaydet**'i seçin.

    ![Batch izinleri ekleme][add_permission]

### <a name="create-a-key-vault"></a>Bir anahtar kasası oluşturma

Kullanıcı aboneliği modunda, oluşturulacak Batch hesabı ile aynı kaynak grubuna ait olan bir Azure key vault gereklidir. Kaynak grubunun, Batch hizmetinin [mevcut](https://azure.microsoft.com/regions/services/) olduğu ve aboneliğinizin desteklediği bir bölgede olduğundan emin olun.

1. [Azure Portal][azure_portal] **Yeni** > güvenlikKeyVault > ' nı seçin.

1. **Key Vault Oluştur** sayfasında anahtar kasası için bir ad girin ve Batch hesabınız için istediğiniz bölgede bir kaynak grubu oluşturun. Kalan ayarları varsayılan değerlerinde bırakın ve ardından **Oluştur**'u seçin.

Kullanıcı aboneliği modunda Batch hesabını oluştururken, Anahtar Kasası için kaynak grubunu kullanın. Havuz ayırma modu olarak **Kullanıcı aboneliğini** belirtin, anahtar kasasını seçin ve anahtar kasasına Azure Batch erişim izni vermek için kutuyu işaretleyin. 

Anahtar kasasına el ile erişim izni vermeyi tercih ediyorsanız, anahtar kasasının **erişim ilkeleri** bölümüne gidin ve **erişim ilkesi Ekle** ' yi seçin ve **Microsoft Azure Batch**arayın. Seçildikten sonra, açılan menüyü kullanarak **gizli izinleri** yapılandırmanız gerekecektir. Azure Batch, en az **Get**, **list**, **set**ve **Delete** izinleri olarak verilmelidir.

![Azure Batch için gizli izinler](./media/batch-account-create-portal/secret-permissions.png)

### <a name="configure-subscription-quotas"></a>Abonelik kotalarını yapılandırma

Çekirdek kotalar, kullanıcı aboneliği toplu Iş hesaplarında varsayılan olarak ayarlanır. Standart Batch çekirdek kotaları Kullanıcı aboneliği modundaki hesaplara uygulanmadığından, çekirdek kotalar el ile ayarlanmalıdır.

1. [Azure Portal][azure_portal], ayarlarını ve özelliklerini göstermek için Kullanıcı abonelik modu Batch hesabınızı seçin.

1. Sol taraftaki menüden **Kotalar** ' ı seçerek Batch hesabınızla ilişkili çekirdek kotalarını görüntüleyin ve yapılandırın.

Kullanıcı aboneliği modu çekirdek kotaları hakkında daha fazla bilgi için [Batch hizmeti kotaları ve limitlerini](batch-quota-limit.md) inceleyin.

## <a name="other-batch-account-management-options"></a>Diğer Batch hesabı yönetim seçenekleri

Azure portalını kullanmaya ek olarak Batch hesaplarını aşağıdaki gibi araçlarla oluşturup yönetebilirsiniz:

* [Batch PowerShell cmdlet’leri](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](batch-cli-get-started.md)
* [Batch Yönetimi .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Sonraki adımlar

* Batch hizmeti kavramları ve özellikler hakkında daha fazla bilgi edinmek için bkz. [Batch özelliklerine genel bakışı](batch-api-basics.md). Makale havuzlar, işlem düğümleri, işler ve görevler gibi birincil Batch kaynaklarını ele alır ve büyük ölçekli işlem iş yüklerine olanak tanıyan hizmetin özelliklerine genel bir bakış sağlar.
* [Batch .NET istemci kitaplığı](quick-run-dotnet.md) veya [Python](quick-run-python.md) kullanarak Batch özellikli bir uygulama geliştirmenin temellerini öğrenin. Bu hızlı başlangıçlar, bir iş yükünü birden fazla işlem düğümünde yürütmek üzere Batch hizmetini kullanan örnek uygulamalar konusunda size rehberlik sağlamanın yanı sıra, iş yükü dosyası hazırlama ve alma işlemleri için Azure Depolama kullanma ile ilgili bilgiler de içerir.

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[marketplace_portal]: ./media/batch-account-create-portal/marketplace-batch.png
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[pool_allocation]: ./media/batch-account-create-portal/batch-pool-allocation.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[register_provider]: ./media/batch-account-create-portal/register_provider.png
