---
title: Azure CLı ile blob verilerine erişim yetkisi verme seçeneğini belirleyin
titleSuffix: Azure Storage
description: Azure CLı ile blob verilerine karşı veri işlemlerini yetkilendirme işlemini belirtin. Hesap erişim anahtarı veya paylaşılan erişim imzası (SAS) belirteci ile Azure AD kimlik bilgilerini kullanarak veri işlemlerini yetkilendirebilirsiniz.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 53ad6dd922c1ccebd79aebcd2966b23b38de00e7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776906"
---
# <a name="choose-how-to-authorize-access-to-blob-data-with-azure-cli"></a>Azure CLı ile blob verilerine erişim yetkisi verme seçeneğini belirleyin

Azure depolama, Azure CLı için, blob verilerinde işlem yetkilendirmek istediğinizi belirtmenize olanak tanıyan uzantılar sağlar. Veri işlemlerini aşağıdaki yollarla yetkilendirebilirsiniz:

- Azure Active Directory (Azure AD) güvenlik sorumlusu ile. Microsoft, üstün güvenlik ve kullanım kolaylığı için Azure AD kimlik bilgilerini kullanmayı önerir.
- Hesap erişim anahtarı veya paylaşılan erişim imzası (SAS) belirteci ile.

## <a name="specify-how-data-operations-are-authorized"></a>Veri işlemlerinin nasıl yetkilendirildiğini belirtme

Blob verilerini okumak ve yazmak için Azure CLı komutları isteğe bağlı `--auth-mode` parametresini içerir. Bir veri işleminin nasıl yetkilendirildiğini göstermek için bu parametreyi belirtin:

- `--auth-mode` `login` BIR Azure AD güvenlik sorumlusu kullanarak oturum açmak için parametresini ayarlayın (önerilir).
- `--auth-mode` `key` Yetkilendirme için kullanılacak hesap erişim anahtarını almayı denemek için parametreyi eski değere ayarlayın. `--auth-mode`Parametresini atlarsanız, Azure CLI de erişim anahtarını almaya çalışır.

Parametresini kullanmak için `--auth-mode` Azure CLI sürüm 2.0.46 veya üstünü yüklediğinizden emin olun. `az --version`Yüklü sürümünüzü denetlemek için ' i çalıştırın.

> [!NOTE]
> Bir depolama hesabı Azure Resource Manager **salt okunur** bir kilit ile kilitlendiğinde, bu depolama hesabı Için [liste anahtarları](/rest/api/storagerp/storageaccounts/listkeys) işlemine izin verilmez. **Liste anahtarları** bir post işlemidir ve hesap Için **salt okunur** BIR kilit yapılandırıldığında tüm post işlemleri engellenir. Bu nedenle, hesap **salt okunur** bir kilit ile kilitlendiğinde, hesap anahtarlarına sahip olmayan kullanıcıların blob verilerine erişmek IÇIN Azure AD kimlik bilgilerini kullanması gerekir.

> [!IMPORTANT]
> `--auth-mode`Parametresini atlarsanız veya olarak ayarlarsanız `key` , Azure CLI yetkilendirme için hesap erişim anahtarını kullanmaya çalışır. Bu durumda, Microsoft, erişim anahtarını komuta veya **AZURE_STORAGE_KEY** ortam değişkenine sağlamanızı önerir. Ortam değişkenleri hakkında daha fazla bilgi için bkz. [yetkilendirme parametreleri için ortam değişkenlerini ayarlama](#set-environment-variables-for-authorization-parameters)başlıklı Bölüm.
>
> Erişim anahtarını sağlamazsanız Azure CLı, Azure depolama kaynak sağlayıcısı 'nı her bir işlem için almak üzere çağırmayı dener. Kaynak sağlayıcısına çağrı gerektiren çok sayıda veri işlemi gerçekleştirmek, azaltma işlemine neden olabilir. Kaynak sağlayıcısı limitleri hakkında daha fazla bilgi için bkz. [Azure depolama kaynak sağlayıcısı Için ölçeklenebilirlik ve performans hedefleri](../common/scalability-targets-resource-provider.md).

## <a name="authorize-with-azure-ad-credentials"></a>Azure AD kimlik bilgileriyle yetkilendirme

Azure CLı 'da Azure AD kimlik bilgileriyle oturum açtığınızda bir OAuth 2,0 erişim belirteci döndürülür. Bu belirteç, sonraki veri işlemlerini blob veya kuyruk depolamaya göre yetkilendirmek için Azure CLı tarafından otomatik olarak kullanılır. Desteklenen işlemler için artık komutuyla bir hesap anahtarını veya SAS belirtecini iletmeniz gerekmez.

Azure rol tabanlı erişim denetimi (Azure RBAC) aracılığıyla bir Azure AD güvenlik sorumlusuna blob verilerine izinler atayabilirsiniz. Azure depolama 'daki Azure rolleri hakkında daha fazla bilgi için bkz. Azure [RBAC Ile Azure depolama verilerine erişim haklarını yönetme](../common/storage-auth-aad-rbac-portal.md).

### <a name="permissions-for-calling-data-operations"></a>Veri işlemlerini çağırma izinleri

Azure depolama uzantıları blob verilerinde işlemler için desteklenir. Çağırabilmeniz gereken işlemler, Azure CLı 'de oturum açarken Azure AD güvenlik sorumlusuna verilen izinlere bağlıdır. Azure depolama kapsayıcıları izinleri Azure RBAC aracılığıyla atanır. Örneğin, **Depolama Blobu veri okuyucusu** rolünü atadıysanız, bir kapsayıcıdan veri okuyan betik komutlarını çalıştırabilirsiniz. **Depolama Blobu veri katılımcısı** rolü atandıysa, bir kapsayıcıyı veya içerdiği verileri okuyan, yazan veya silen betik komutlarını çalıştırabilirsiniz.

Bir kapsayıcıdaki her bir Azure depolama işlemi için gereken izinler hakkında daha fazla bilgi için bkz. [OAuth belirteçleriyle depolama Işlemlerini çağırma](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>Örnek: Azure AD kimlik bilgileriyle kapsayıcı oluşturmak için bir işlemi yetkilendirme

Aşağıdaki örnekte Azure AD kimlik bilgilerinizi kullanarak Azure CLı 'dan nasıl kapsayıcı oluşturacağınız gösterilmektedir. Kapsayıcıyı oluşturmak için Azure CLı 'da oturum açmanız gerekir ve bir kaynak grubu ve bir depolama hesabı gerekir. Bu kaynakları oluşturma hakkında bilgi edinmek için bkz. [hızlı başlangıç: Azure CLI ile Blobları oluşturma, indirme ve listeleme](../blobs/storage-quickstart-blobs-cli.md).

1. Kapsayıcıyı oluşturmadan önce, [Depolama Blobu veri katılımcısı](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) rolünü kendinize atayın. Hesap sahibi olsanız bile, depolama hesabında veri işlemleri gerçekleştirmek için açık izinlere sahip olmanız gerekir. Azure rolleri atama hakkında daha fazla bilgi için bkz. [BLOB ve kuyruk verilerine erişim Için Azure rolü atamak üzere Azure Portal kullanma](../common/storage-auth-aad-rbac-portal.md).

    > [!IMPORTANT]
    > Azure rolü atamalarının yayılması birkaç dakika sürebilir.

1. [](/cli/azure/storage/container#az_storage_container_create) `--auth-mode` `login` Azure AD kimlik bilgilerinizi kullanarak kapsayıcıyı oluşturmak için parametresi olarak ayarlanmış az Storage Container Create komutunu çağırın. Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Hesap erişim anahtarıyla yetkilendir

Hesap anahtarına sahip olmanız durumunda herhangi bir Azure depolama veri işlemini çağırabilirsiniz. Genel olarak, hesap anahtarının kullanılması daha az güvenlidir. Hesap anahtarı tehlikeye girerse, hesabınızdaki tüm verilerin güvenliği aşılmış olabilir.

Aşağıdaki örnek, hesap erişim anahtarı kullanılarak nasıl kapsayıcı oluşturulacağını göstermektedir. Hesap anahtarını belirtin ve `--auth-mode` parametresini `key` değere girin:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

> [!IMPORTANT]
> Bir depolama hesabı Azure Resource Manager **salt okunur** bir kilit ile kilitlendiğinde, bu depolama hesabı Için [liste anahtarları](/rest/api/storagerp/storageaccounts/listkeys) işlemine izin verilmez. **Liste anahtarları** bir post işlemidir ve hesap Için **salt okunur** BIR kilit yapılandırıldığında tüm post işlemleri engellenir. Bu nedenle, hesap **salt okunur** bir kilit ile kilitlendiğinde, KULLANıCıLARıN Azure AD kimlik bilgileriyle verilere erişmesi gerekir.

## <a name="authorize-with-a-sas-token"></a>SAS belirteciyle yetkilendirme

Bir SAS belirtecine sahip olmanız durumunda SAS tarafından izin verilen veri işlemlerini çağırabilirsiniz. Aşağıdaki örnek, bir SAS belirteci kullanarak kapsayıcının nasıl oluşturulacağını gösterir:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Yetkilendirme parametreleri için ortam değişkenlerini ayarlama

Her bir Azure depolama veri işlemine yapılan her çağrıya dahil etmek için, ortam değişkenlerinde yetkilendirme parametreleri belirtebilirsiniz. Aşağıdaki tabloda kullanılabilir ortam değişkenleri açıklanmaktadır.

| Ortam değişkeni                  | Description                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    Depolama hesabı adı. Bu değişken, depolama hesabı anahtarı veya SAS belirteci ile birlikte kullanılmalıdır. Hiçbiri yoksa, Azure CLı kimliği doğrulanmış Azure AD hesabını kullanarak depolama hesabı erişim anahtarını almaya çalışır. Tek seferde çok sayıda komut yürütülürse, Azure depolama kaynak sağlayıcısı azaltma sınırına ulaşılmış olabilir. Kaynak sağlayıcısı limitleri hakkında daha fazla bilgi için bkz. [Azure depolama kaynak sağlayıcısı Için ölçeklenebilirlik ve performans hedefleri](../common/scalability-targets-resource-provider.md).             |
|    AZURE_STORAGE_KEY                  |    Depolama hesabı anahtarı. Bu değişkenin depolama hesabı adıyla birlikte kullanılması gerekir.                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    Depolama hesabı anahtarını veya SAS belirtecini içeren bir bağlantı dizesi. Bu değişkenin depolama hesabı adıyla birlikte kullanılması gerekir.                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    Paylaşılan erişim imzası (SAS) belirteci. Bu değişkenin depolama hesabı adıyla birlikte kullanılması gerekir.                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    Komutun çalıştırılacağı yetkilendirme modu. İzin verilen değerler `login` (önerilir) veya `key` . Belirtirseniz `login` , Azure CLI veri işlemini yetkilendirmek Için Azure AD kimlik bilgilerinizi kullanır. Eski `key` modu belirtirseniz, Azure CLI hesap erişim anahtarını sorgulamaya çalışır ve anahtarı anahtarla yetkilendiremez.    |

## <a name="next-steps"></a>Sonraki adımlar

- [Blob ve kuyruk verilerine erişim için Azure rolü atamak üzere Azure CLı 'yi kullanma](../common/storage-auth-aad-rbac-cli.md)
- [Azure kaynakları için yönetilen kimliklerle blob ve kuyruk verilerine erişim yetkisi verme](../common/storage-auth-aad-msi.md)