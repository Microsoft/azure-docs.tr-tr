---
title: Azure Event Hubs verilerini bekleyen bir şekilde şifrelemek için kendi anahtarınızı yapılandırın
description: Bu makalede, Azure Event Hubs Data Rest 'i şifrelemek için kendi anahtarınızı yapılandırma hakkında bilgi verilmektedir.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: spelluru
ms.openlocfilehash: 63fe6c4a2d02489b5e25100aa6aa23407bbe6bc7
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809373"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Azure Event Hubs verilerini Rest 'te şifrelemek için müşteri tarafından yönetilen anahtarları Azure portal kullanarak yapılandırın
Azure Event Hubs, Azure Depolama Hizmeti Şifrelemesi (Azure SSE) ile bekleyen verilerin şifrelenmesini sağlar. Event Hubs, verileri depolamak için Azure depolama 'yı kullanır ve varsayılan olarak, Azure Storage ile depolanan tüm veriler Microsoft tarafından yönetilen anahtarlar kullanılarak şifrelenir. 

## <a name="overview"></a>Genel Bakış
Azure Event Hubs artık, Microsoft tarafından yönetilen anahtarlarla veya müşteri tarafından yönetilen anahtarlarla (Kendi Anahtarını Getir – BYOK), bekleyen verileri şifreleme seçeneğini desteklemektedir. Bu özellik, Azure Event Hubs verilerini bekleyen bir şekilde şifrelemek için kullanılan müşteri tarafından yönetilen anahtarlara erişimi oluşturmanıza, döndürmenize, devre dışı bırakmanızı ve iptal etmenize olanak sağlar.

BYOK özelliğinin etkinleştirilmesi, ad alanınız üzerinde bir kerelik kurulum işlemidir.

> [!NOTE]
> BYOK özelliği [Event Hubs adanmış tek kiracılı](event-hubs-dedicated-overview.md) kümeler tarafından desteklenir. Standart Event Hubs ad alanları için etkinleştirilemez.

Anahtarlarınızı yönetmek ve anahtar kullanımınızı denetlemek için Azure Key Vault kullanabilirsiniz. Kendi anahtarlarınızı oluşturabilir ve bunları bir anahtar kasasında saklayabilir veya Azure Key Vault API 'Lerini kullanarak anahtarlar oluşturabilirsiniz. Azure Key Vault hakkında daha fazla bilgi için bkz. [Azure Key Vault nedir?](../key-vault/key-vault-overview.md)

Bu makalede, Azure portal kullanarak, müşteri tarafından yönetilen anahtarlarla bir anahtar kasasının nasıl yapılandırılacağı gösterilmektedir. Azure portal kullanarak bir Anahtar Kasası oluşturmayı öğrenmek için bkz. [hızlı başlangıç: Azure Portal kullanarak Azure Key Vault gizli dizi ayarlama ve alma](../key-vault/quick-create-portal.md).

> [!IMPORTANT]
> Azure Event Hubs ile müşteri tarafından yönetilen anahtarların kullanılması, anahtar kasasının iki gerekli özelliği yapılandırılmış olmasını gerektirir. Bunlar: **geçici silme** ve **Temizleme**. Azure portal yeni bir Anahtar Kasası oluşturduğunuzda, bu özellikler varsayılan olarak etkinleştirilir. Ancak, var olan bir anahtar kasasında bu özellikleri etkinleştirmeniz gerekiyorsa, PowerShell veya Azure CLı kullanmanız gerekir.

## <a name="enable-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarları etkinleştir
Azure portal müşteri tarafından yönetilen anahtarları etkinleştirmek için şu adımları izleyin:

1. Event Hubs Ayrılmış kümenize gidin.
1. BYOK ' u etkinleştirmek istediğiniz ad alanını seçin.
1. Event Hubs ad alanının **Ayarlar** sayfasında **şifreleme (Önizleme)** öğesini seçin. 
1. Aşağıdaki görüntüde gösterildiği gibi **geri kalan müşteri tarafından yönetilen anahtar şifrelemesini** seçin. 

    ![Müşteri tarafından yönetilen anahtarı etkinleştir](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Anahtarlar içeren bir Anahtar Kasası ayarlama
Müşteri tarafından yönetilen anahtarları etkinleştirdikten sonra, müşteri tarafından yönetilen anahtarı Azure Event Hubs ad alanınız ile ilişkilendirmeniz gerekir. Event Hubs yalnızca Azure Key Vault destekler. Önceki bölümde, **müşteri tarafından yönetilen anahtar seçeneğiyle şifrelemeyi** etkinleştirirseniz, anahtarın Azure Key Vault içine aktarılması gerekir. Ayrıca, anahtarlar için **yumuşak silme** ve anahtar Için de **Temizleme** yapılandırması olmalıdır. Bu ayarlar, [PowerShell](../key-vault/key-vault-soft-delete-powershell.md) veya [CLI](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection)kullanılarak yapılandırılabilir.

1. Yeni bir Anahtar Kasası oluşturmak için Azure Key Vault [hızlı](../key-vault/key-vault-overview.md)başlangıcı ' nı izleyin. Varolan anahtarları içeri aktarma hakkında daha fazla bilgi için bkz. [anahtarlar, gizlilikler ve sertifikalar hakkında](../key-vault/about-keys-secrets-and-certificates.md).
1. Bir kasa oluştururken hem geçici silme hem de Temizleme korumasını açmak için [az keykasa Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) komutunu kullanın.

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Var olan bir kasaya Temizleme koruması eklemek için (zaten geçici silme etkindir), [az keykasa Update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) komutunu kullanın.

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Aşağıdaki adımları izleyerek anahtarlar oluşturun:
    1. Yeni bir anahtar oluşturmak için **Ayarlar**altındaki **anahtarlar** menüsünden **Oluştur/içeri aktar** ' ı seçin.
        
        ![Oluştur/Içeri Aktar düğmesini seçin](./media/configure-customer-managed-key/select-generate-import.png)
    1. Oluşturma **seçeneklerini** belirleyin ve anahtara bir ad verin.

        ![Bir anahtar oluşturma](./media/configure-customer-managed-key/create-key.png) 
    1. Şimdi, açılan listeden şifrelemek için Event Hubs ad alanıyla ilişkilendirmek üzere bu anahtarı seçebilirsiniz. 

        ![Anahtar kasasından anahtar seçin](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Anahtarın ayrıntılarını girin ve **Seç**' e tıklayın. Bu, müşteri tarafından yönetilen anahtar ile ad alanındaki bekleyen verilerin şifrelenmesini sağlar. 

        > [!NOTE]
        > Önizleme için yalnızca tek bir anahtar seçebilirsiniz. 

## <a name="rotate-your-encryption-keys"></a>Şifreleme anahtarlarınızı döndürün
Anahtarı anahtar kasasında Azure Anahtar Kasası döndürme mekanizmasını kullanarak döndürebilirsiniz. Daha fazla bilgi için bkz. [anahtar dönüşü ve denetimini ayarlama](../key-vault/key-vault-key-rotation-log-monitoring.md). Etkinleştirme ve sona erme tarihleri, anahtar döndürmeyi otomatik hale getirmek için de ayarlanabilir. Event Hubs hizmet yeni anahtar sürümlerini algılar ve otomatik olarak kullanmaya başlar.

## <a name="revoke-access-to-keys"></a>Anahtarlara erişimi iptal et
Şifreleme anahtarlarına erişimin iptal edilmemesi Event Hubs verileri temizleyemezsiniz. Ancak, verilere Event Hubs ad alanından erişilemez. Şifreleme anahtarını erişim ilkesi veya anahtarı silerek iptal edebilirsiniz. Erişim ilkeleri hakkında daha fazla bilgi edinin ve anahtar kasasının güvenliğini [güvenli bir şekilde bir anahtar kasasına erişin](../key-vault/key-vault-secure-your-key-vault.md).

Şifreleme anahtarı iptal edildiğinde, şifrelenen ad alanındaki Event Hubs hizmeti çalışamaz hale gelir. Anahtara erişim etkinleştirilirse veya silme anahtarı geri yüklenirse, şifrelenmiş Event Hubs ad alanındaki verilere erişebilmek için Event Hubs hizmet anahtarı seçer.

> [!NOTE]
> Anahtar kasanızdan var olan bir şifreleme anahtarını silip Event Hubs ad alanındaki yeni bir anahtarla değiştirirseniz, silme anahtarı bir saate kadar geçerli olduğundan (eski anahtarla şifrelenen), eski verileriniz de erişilebilir durumda olabilir  Yeni veriler ile, artık yalnızca yeni anahtar kullanılarak erişilebilir. Bu davranış, özelliğin önizleme sürümünde tasarımdır. 

## <a name="set-up-diagnostic-logs"></a>Tanılama günlükleri ayarlama 
BYOK etkinleştirilmiş ad alanları için tanılama günlüklerini ayarlama, bir ad alanı müşteri tarafından yönetilen anahtarlarla şifrelendiğinde, işlemler hakkında gerekli bilgileri sağlar. Bu Günlükler etkinleştirilebilir ve daha sonra bir olay hub 'ına bağlanabilir veya Log Analytics aracılığıyla analiz edilebilir ya da özelleştirilmiş analizler gerçekleştirmek için depolama alanına akışı yapılabilir. Tanılama günlükleri hakkında daha fazla bilgi edinmek için bkz. [Azure tanılama günlüklerine genel bakış](../azure-monitor/platform/resource-logs-overview.md).

## <a name="enable-user-logs"></a>Kullanıcı günlüklerini etkinleştir
Müşteri tarafından yönetilen anahtarlar için günlükleri etkinleştirmek üzere bu adımları izleyin.

1. Azure portal, BYOK etkinleştirilmiş olan ad alanına gidin.
1. Izleme altında **Tanılama ayarları** ' **nı**seçin.

    ![Tanılama ayarlarını seçin](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. **+ Tanılama ayarı Ekle**' yi seçin. 

    ![Tanılama ayarı Ekle 'yi seçin](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Bir **ad** girin ve günlüklerin akışını istediğiniz yeri seçin.
1. **Customermanagedkeyuserlogs** ve **Kaydet**' i seçin. Bu eylem, ad alanı üzerinde BYOK için günlüklere izin vermez.

    ![Müşteri tarafından yönetilen anahtar Kullanıcı günlükleri seçeneğini belirleyin](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Günlük şeması 
Tüm Günlükler JavaScript Nesne Gösterimi (JSON) biçiminde depolanır. Her girdinin aşağıdaki tabloda açıklanan biçimi kullanan dize alanları vardır. 

| Adı | Açıklama |
| ---- | ----------- | 
| Silinecek | Başarısız olan görevin açıklaması. |
| Etkinlik kimliği | İzleme için kullanılan iç KIMLIK. |
| category | Görevin sınıflandırmasını tanımlar. Örneğin, anahtar kasanızın anahtarı devre dışı bırakılmışsa, bir bilgi kategorisi olur veya bir anahtarın sarmalanmamış olması durumunda hataya neden olabilir. |
| resourceId | Azure Resource Manager kaynak KIMLIĞI |
| KeyVault | Anahtar kasasının tam adı. |
| anahtar | Event Hubs ad alanını şifrelemek için kullanılan anahtar adı. |
| version | Kullanılan anahtarın sürümü. |
| Çalışmasını | Anahtar kasasındaki anahtarda gerçekleştirilen işlem. Örneğin, anahtarı devre dışı bırakma/etkinleştirme, sarmalama veya kaydırmayı kaldırma |
| Kodudur | İşlemle ilişkili kod. Örnek: hata kodu, 404, anahtarın bulunamadığı anlamına gelir. |
| message | İşlemle ilişkili herhangi bir hata iletisi |

Müşteri tarafından yönetilen anahtar için günlüğe bir örnek aşağıda verilmiştir:

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="troubleshoot"></a>Sorun giderme
En iyi uygulama olarak, önceki bölümde gösterildiği gibi günlükleri her zaman etkinleştirin. BYOK şifrelemesi etkinleştirildiğinde etkinlikleri izlemeye yardımcı olur. Ayrıca sorunların kapsamını belirlemek için de yardımcı olur.

Aşağıda, BYOK şifrelemesi etkinleştirildiğinde aranacak ortak hatalar kodları verilmiştir.

| Eylem | Hata kodu | Verilerin sonuç durumu |
| ------ | ---------- | ----------------------- | 
| Anahtar kasasından kaydırmayı/kaydırmayı kaldırma iznini kaldır | 403 |    Erişilemez |
| Bir AAD sorumlusunun sarmalama/sarmalama izni verilen AAD rolü üyeliğini kaldırma | 403 |  Erişilemez |
| Anahtar kasasından bir şifreleme anahtarı silme | 404 | Erişilemez |
| Anahtar kasasını silme | 404 | Erişilebilir değil (gerekli bir ayar olan, geçici silme özelliğinin etkin olduğunu varsayar.) |
| Şifreleme anahtarındaki süre sonu süresini, zaten süresi dolduğu için değiştirme | 403 |   Erişilemez  |
| Bu anahtar şifreleme anahtarı etkin olmayan NBF (daha önce değil) değiştiriliyor | 403 | Erişilemez  |
| Anahtar Kasası güvenlik duvarı için **MSFT hizmetlerine Izin ver** seçeneğini belirleme veya başka bir şekilde şifreleme anahtarına sahip anahtar kasasına ağ erişimini engelleme | 403 | Erişilemez |
| Anahtar kasasını farklı bir kiracıya taşıma | 404 | Erişilemez |  
| Aralıklı ağ sorunu veya DNS/AAD/MSI kesintisi |  | Önbelleğe alınmış veri şifreleme anahtarı kullanılarak erişilebilir |

> [!IMPORTANT]
> BYOK şifrelemesini kullanan bir ad alanında coğrafi DR 'yi etkinleştirmek için, eşleştirme için ikincil ad alanı adanmış bir kümede olmalıdır ve üzerinde sistem tarafından atanmış bir yönetilen kimlik etkin olmalıdır. Daha fazla bilgi için bkz. [Azure kaynakları Için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md).

> [!NOTE]
> Sanal ağ (VNet) hizmet uç noktaları Event Hubs ad alanınız için Azure Key Vault yapılandırılmışsa BYOK desteklenmez. 


## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın:
- [Event Hubs’a genel bakış](event-hubs-about.md)
- [Key Vault genel bakış](../key-vault/key-vault-overview.md)




