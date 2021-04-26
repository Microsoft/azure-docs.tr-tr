---
title: Azure portal hesap oluşturma
description: Büyük ölçekli paralel iş yükleri bulutta çalıştırmak için Azure portalda bir Azure Batch hesabı oluşturmayı öğrenin
ms.topic: how-to
ms.date: 02/23/2021
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 36759a0caef41af9307bf621a1b6b634ddf586cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101703673"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Azure portalıyla Batch hesabı oluşturma

Bu konuda, işlem senaryonuza uyan hesap özelliklerini seçerek [Azure portal](https://portal.azure.com) [Azure Batch hesabının](accounts.md) nasıl oluşturulacağı gösterilmektedir. Ayrıca erişim anahtarları ve hesap URL 'Leri gibi önemli hesap özelliklerini nerede bulacağınızı öğreneceksiniz.

Batch hesapları ve senaryoları hakkında arka plan için bkz. [Batch hizmeti iş akışı ve kaynakları](batch-service-workflow-features.md).

## <a name="create-a-batch-account"></a>Batch hesabı oluşturma

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Giriş sayfasından **kaynak oluştur**' u seçin.

1. Arama kutusuna **Batch hizmeti** girin. Sonuçlardan **Batch hizmeti** ' ni seçin ve ardından **Oluştur**' u seçin.

1. Aşağıdaki ayrıntıları girin.

    :::image type="content" source="media/batch-account-create-portal/batch-account-portal.png" alt-text="Yeni Batch hesabı ekranının ekran görüntüsü.":::

    a. **Abonelik**: Batch hesabının oluşturulacağı bir abonelik. Yalnızca bir aboneliğiniz varsa, varsayılan olarak seçilidir.

    b. **Kaynak grubu**: Yeni Batch hesabınız için mevcut bir kaynak grubu seçebilir ya da isterseniz yeni bir tane oluşturabilirsiniz.

    c. **Hesap adı**: Seçtiğiniz ad, hesabın oluşturulduğu Azure bölgesinde benzersiz olmalıdır (aşağıdaki **Konum** bölümüne bakın). Hesap adı yalnızca küçük harfler, sayılar içerebilir ve 3-24 karakter uzunluğunda olmalıdır.

    d. **Konum**: Batch hesabının oluşturulacağı bir Azure bölgesi. Yalnızca aboneliğiniz ve kaynak grubunuz tarafından desteklenen bölgeler seçenek olarak görüntülenir.

    e. **Depolama hesabı**: Batch hesabınızla ilişkilendirdiğiniz isteğe bağlı bir [Azure depolama hesabı](accounts.md#azure-storage-accounts) . Var olan bir depolama hesabını seçebilir veya yeni bir tane oluşturabilirsiniz. En iyi performans için genel amaçlı v2 depolama hesabı önerilir.

    :::image type="content" source="media/batch-account-create-portal/storage_account.png" alt-text="Bir depolama hesabı oluştururken seçeneklerin ekran görüntüsü.":::

1. İsterseniz, **kimlik türünü**, **genel ağ erişimini** veya **havuz ayırma modunu** belirtmek için **Gelişmiş** ' i seçin. Çoğu senaryo için varsayılan seçenekler iyidir.

1. **Gözden geçir + oluştur**' u seçin ve sonra hesabı oluşturmak için **Oluştur** ' u seçin.

## <a name="view-batch-account-properties"></a>Batch hesabı özelliklerini görüntüleme

Hesap oluşturulduktan sonra seçerek ayarlarına ve özelliklerine erişebilirsiniz. Sol menüyü kullanarak tüm hesap ayarlarına ve özelliklerine erişebilirsiniz.

> [!NOTE]
> Batch hesabının adı kendi KIMLIĞIDIR ve değiştirilemez. Bir Batch hesabının adını değiştirmeniz gerekiyorsa, hesabı silmeniz ve hedeflenen ada sahip yeni bir tane oluşturmanız gerekir.

:::image type="content" source="media/batch-account-create-portal/batch_blade.png" alt-text="Azure portal Batch hesabı sayfasının ekran görüntüsü.":::

[Batch API 'leriyle](batch-apis-tools.md#azure-accounts-for-batch-development)bir uygulama geliştirirken, Batch kaynaklarınıza erişmeniz için bir hesap URL 'si ve anahtar gerekir. (Batch Azure Active Directory kimlik doğrulamasını da destekler.) Batch hesabı erişim bilgilerini görüntülemek için **anahtarlar**' ı seçin.

:::image type="content" source="media/batch-account-create-portal/batch-account-keys.png" alt-text="Azure portal Batch hesabı anahtarlarının ekran görüntüsü.":::

Batch hesabınızla ilişkili depolama hesabının adını ve anahtarlarını görüntülemek için **Depolama hesabı**'nı seçin.

Batch hesabına uygulanan [kaynak kotalarını](batch-quota-limit.md) görüntülemek için **Kotalar**' ı seçin.

## <a name="additional-configuration-for-user-subscription-mode"></a>Kullanıcı aboneliği modu için ek yapılandırma

Kullanıcı aboneliği modunda bir Batch hesabı oluşturmayı seçerseniz, hesabı oluşturmadan önce aşağıdaki ek adımları gerçekleştirin.

> [!IMPORTANT]
> Batch hesabını Kullanıcı abonelik modunda oluşturan kullanıcının, Batch hesabının oluşturulacağı abonelik için katkıda bulunan veya sahip rolü ataması olması gerekir.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Azure Batch hizmetinin aboneliğe erişmesine izin verme (tek seferlik işlem)

Kullanıcı aboneliği modunda ilk Batch hesabınızı oluştururken, aboneliğinizi Batch hizmetine kaydetmeniz gerekir. (Zaten bunu yaptıysanız sonraki bölüme atlayın.)

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. **Tüm hizmet**  >  **abonelikleri**' ni seçin ve Batch hesabı için kullanmak istediğiniz aboneliği seçin.

1. **Abonelik** sayfasında **Kaynak sağlayıcıları**'nı seçip **Microsoft.Batch**'i arayın. **Microsoft.Batch** kaynak sağlayıcısının aboneliğe kayıtlı olup olmadığını kontrol edin. Aksi takdirde, ekranın üst kısmındaki **kayıt** bağlantısını seçin.

    :::image type="content" source="media/batch-account-create-portal/register_provider.png" alt-text="Microsoft.Batch kaynak sağlayıcısını gösteren ekran görüntüsü.":::

1. **Abonelik** sayfasına dönün, ardından **erişim denetimi (IAM)**  >  **rol atamaları**  >  **Ekle**  >  **rol ataması** Ekle ' yi seçin.

    :::image type="content" source="media/batch-account-create-portal/subscription_iam.png" alt-text="Bir abonelik için rol atamaları sayfasının ekran görüntüsü.":::

1. **Rol ataması Ekle** sayfasında, **katkıda** bulunan veya **sahip** rolü ' nü seçin ve Batch API 'sini arayın. API 'YI bulmak için **Microsoft Azure Batch** veya **MicrosoftAzureBatch** aratın. (**ddbf3205-c6bd-46AE-8127-60eb93363864** , Batch API 'Si IÇIN uygulama kimliğidir.)

1. Batch API'sini bulduktan sonra seçin ve **Kaydet**'i seçin.

### <a name="create-a-key-vault"></a>Anahtar kasası oluşturma

Kullanıcı aboneliği modunda bir [Azure Key Vault](../key-vault/general/overview.md) gereklidir. Key Vault, oluşturulacak Batch hesabıyla aynı abonelikte ve bölgede olmalıdır.

1. [Azure Portal](https://portal.azure.com)ana sayfasında, **kaynak oluştur**' u seçin.
1. Arama kutusuna **Key Vault** girin. Sonuçlardan **Key Vault** seçin ve ardından **Oluştur**' u seçin.
1. **Anahtar Kasası oluştur** sayfasında, Key Vault için bir ad girin ve Batch hesabınız için istediğiniz bölgede yeni bir kaynak grubu oluşturun. Kalan ayarları varsayılan değerlerinde bırakın ve ardından **Oluştur**'u seçin.

Kullanıcı aboneliği modunda Batch hesabını oluştururken, havuz ayırma modu olarak **Kullanıcı aboneliğini** belirtin, oluşturduğunuz Key Vault seçin ve Key Vault Azure Batch erişim izni vermek için kutuyu işaretleyin.

Key Vault el ile erişim izni vermeyi tercih ediyorsanız, Key Vault **erişim ilkeleri** bölümüne gidin ve **erişim ilkesi Ekle**' yi seçin. **Sorumluyu Seç** ' in yanındaki bağlantıyı seçin ve **Microsoft Azure Batch** için arama yapın (uygulama kimliği **ddbf3205-c6bd-46AE-8127-60eb93363864**). Bu sorumluyu seçin ve ardından açılan menüyü kullanarak **gizli izinleri** yapılandırın. Azure Batch, en az **Get**, **list**, **set** ve **Delete** izinleri olarak verilmelidir. [Geçici silme etkin olan anahtar kasaları](../key-vault/general/soft-delete-overview.md)için, Azure Batch de **Kurtarma** izni verilmelidir.

:::image type="content" source="media/batch-account-create-portal/secret-permissions.png" alt-text="Azure Batch için gizli izinler seçimlerinin ekran görüntüsü":::

**Ekle**' yi seçin, sonra **dağıtım Için Azure sanal makinelerinin** ve **şablon dağıtımı için Azure Resource Manager** bağlı **Key Vault** kaynağı için seçili olduğundan emin olun. Değişikliklerinizi uygulamak için **Kaydet** ' i seçin.

:::image type="content" source="media/batch-account-create-portal/key-vault-access-policy.png" alt-text="Erişim ilkesi ekranının ekran görüntüsü.":::

### <a name="configure-subscription-quotas"></a>Abonelik kotalarını yapılandırma

Kullanıcı aboneliği Batch hesapları için [çekirdek kotaların](batch-quota-limit.md) el ile ayarlanması gerekir. Standart Batch çekirdek kotalar, kullanıcı aboneliği modundaki hesaplara ve bölgesel bilgi işlem çekirdekleri, seri başına işlem çekirdekleri ve diğer kaynakların kullanıldığı ve zorlandıkları [Kotalar](../azure-resource-manager/management/azure-subscription-service-limits.md) için geçerlidir.

1. [Azure Portal](https://portal.azure.com), ayarlarını ve özelliklerini göstermek için Kullanıcı abonelik modu Batch hesabınızı seçin.
1. Sol taraftaki menüden **Kotalar** ' ı seçerek Batch hesabınızla ilişkili çekirdek kotalarını görüntüleyin ve yapılandırın.

## <a name="other-batch-account-management-options"></a>Diğer Batch hesabı yönetim seçenekleri

Azure portalını kullanmaya ek olarak Batch hesaplarını aşağıdaki gibi araçlarla oluşturup yönetebilirsiniz:

- [Batch PowerShell cmdlet’leri](batch-powershell-cmdlets-get-started.md)
- [Azure CLI](batch-cli-get-started.md)
- [Batch Yönetimi .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Batch hizmeti iş akışı ve](batch-service-workflow-features.md) havuzlar, düğümler, işler ve görevler gibi birincil kaynaklar hakkında bilgi edinin.
- [Batch .NET istemci kitaplığı](quick-run-dotnet.md) veya [Python](quick-run-python.md) kullanarak Batch özellikli bir uygulama geliştirmenin temellerini öğrenin. Bu hızlı başlangıç, iş yükü dosyası hazırlama ve alma için Azure depolama 'yı kullanarak birden çok işlem düğümünde bir iş yükü yürütmek üzere Batch hizmetini kullanan bir örnek uygulamada size rehberlik sağlar.