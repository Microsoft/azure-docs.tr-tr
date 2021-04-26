---
title: Bekleyen verileri şifrelemek için müşteri tarafından yönetilen anahtarları ayarlama
description: Azure Logic Apps içindeki tümleştirme hizmeti ortamları (sesleri) için bekleyen verileri güvenli hale getirmek üzere kendi şifreleme anahtarlarınızı oluşturun ve yönetin
services: logic-apps
ms.suite: integration
ms.reviewer: mijos, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 01/20/2021
ms.openlocfilehash: d31fbd813f0c5d63ee9eddbff5b299209618626b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98629683"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>Azure Logic Apps içindeki tümleştirme hizmeti ortamları (sesleri) için bekleyen verileri şifrelemek üzere müşteri tarafından yönetilen anahtarlar ayarlayın

Azure Logic Apps, [bekleyen verileri](../storage/common/storage-service-encryption.md)depolamak ve otomatik olarak şifrelemek Için Azure Storage 'ı kullanır. Bu şifreleme, verilerinizi korur ve kurumsal güvenlik ve uyumluluk taahhütlerinizi karşılamanıza yardımcı olur. Azure depolama, verilerinizi şifrelemek için varsayılan olarak Microsoft tarafından yönetilen anahtarları kullanır. Azure depolama şifrelemesi 'nin nasıl çalıştığı hakkında daha fazla bilgi için bkz. [bekleyen veriler Için Azure depolama şifrelemesi](../storage/common/storage-service-encryption.md) ve [bekleyen Azure veri şifreleme](../security/fundamentals/encryption-atrest.md).

Logic Apps 'i barındırmak için bir [tümleştirme hizmeti ortamı (ıSE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) oluşturduğunuzda ve Azure Storage tarafından kullanılan şifreleme anahtarları üzerinde daha fazla denetim istiyorsanız, [Azure Key Vault](../key-vault/general/overview.md)kullanarak kendi anahtarınızı ayarlayabilir, kullanabilir ve yönetebilirsiniz. Bu özellik "Kendi Anahtarını Getir" (BYOK) olarak bilinir ve anahtarınız "müşteri tarafından yönetilen anahtar" olarak adlandırılır. Bu özellik sayesinde Azure Storage, anahtarınız için [platform tarafından yönetilen anahtarlar kullanılarak otomatik olarak çift şifrelemeyi veya *altyapı şifrelemesini*](../security/fundamentals/double-encryption.md) otomatik olarak sunar. Daha fazla bilgi edinmek için bkz. [altyapı şifrelemesi ile verileri](../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)daha fazla şifreleme.

Bu konuda, Logic Apps REST API kullanarak ıSE oluştururken kullanmak üzere kendi şifreleme anahtarınızı ayarlama ve belirtme işlemlerinin nasıl yapılacağı gösterilmektedir. Logic Apps REST API aracılığıyla bir ıSE oluşturmanın genel adımları için, bkz. [Logic Apps REST API kullanarak bir tümleştirme hizmeti ortamı (ISE) oluşturma](../logic-apps/create-integration-service-environment-rest-api.md).

## <a name="considerations"></a>Dikkat edilmesi gerekenler

* Şu anda bir ıSE için müşteri tarafından yönetilen anahtar desteği yalnızca şu Azure bölgelerinde kullanılabilir: Batı ABD 2, Doğu ABD ve Orta Güney ABD

* Müşteri tarafından yönetilen bir anahtarı, daha sonra değil *yalnızca Ise 'nizi oluşturduğunuz zaman* belirtebilirsiniz. ISE oluşturulduktan sonra bu anahtarı devre dışı bırakabilirsiniz. Şu anda, bir ıSE için müşteri tarafından yönetilen bir anahtarı döndürmek için destek yok.

* Müşteri tarafından yönetilen anahtarları desteklemek için, ıSE, [sistem tarafından atanan veya Kullanıcı tarafından atanan yönetilen kimlik](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)seçeneklerinden birini etkinleştirmenizi gerektirir. Bu kimlik, ıSE 'nin sanal makineler ve bir Azure sanal ağı 'nda bulunan ya da bağlı olan sanal makineler ve diğer sistemler veya hizmetler gibi güvenli kaynaklara erişimi kimlik doğrulamasına olanak tanır. Bu şekilde, kimlik bilgilerinizle oturum açmanız gerekmez.

* Şu anda, müşteri tarafından yönetilen anahtarları destekleyen ve yönetilen kimlik türü etkinleştirilmiş bir ıSE oluşturmak için, HTTPS PUT isteği kullanarak Logic Apps REST API çağırmanız gerekir.

* [Ise 'nin yönetilen kimliğine Anahtar Kasası erişimi vermeniz](#identity-access-to-key-vault)gerekir, ancak zamanlama kullandığınız yönetilen kimliğe bağlıdır.

  * **Sistem tarafından atanan yönetilen kimlik**: Ise 'YI oluşturan https put isteğini gönderdikten *sonra* , [Ise 'nin yönetilen kimliğine Anahtar Kasası erişimi sağlamanız](#identity-access-to-key-vault)gerekir. Aksi takdirde, ıSE oluşturma işlemi başarısız olur ve bir izin hatası alırsınız.

  * **Kullanıcı tarafından atanan yönetilen kimlik**: Ise 'YI oluşturan https put isteğini göndermeden önce, [Ise 'nin yönetilen kimliğine Anahtar Kasası erişimi verin](#identity-access-to-key-vault).

## <a name="prerequisites"></a>Önkoşullar

* Azure portal bir ıSE oluşturduğunuzda, [Ise için erişimi etkinleştirmek için](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) aynı [Önkoşullar](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) ve gereksinimler

* **Geçici silme** ve **Temizleme** özellikleri etkin olan bir Azure Anahtar Kasası

  Bu özellikleri etkinleştirme hakkında daha fazla bilgi için bkz. [Azure Key Vault geçici genel bakış](../key-vault/general/soft-delete-overview.md) ve [müşteri tarafından yönetilen anahtarları Azure Key Vault ile yapılandırma](../storage/common/customer-managed-keys-configure-key-vault.md). [Azure Key Vault](../key-vault/general/overview.md)yeni başladıysanız, [Azure Portal](../key-vault/general/quick-create-portal.md), [Azure CLI](../key-vault/general/quick-create-cli.md)veya [Azure PowerShell](../key-vault/general/quick-create-powershell.md)kullanarak bir Anahtar Kasası oluşturmayı öğrenin.

* Anahtar Kasanızda, bu özellik değerleriyle oluşturulmuş bir anahtar:

  | Özellik | Değer |
  |----------|-------|
  | **Anahtar türü** | RSA |
  | **RSA anahtar boyutu** | 2048 |
  | **Etkin** | Yes |
  |||

  ![Müşteri tarafından yönetilen şifreleme anahtarınızı oluşturma](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  Daha fazla bilgi için bkz. Azure Key Vault veya Azure PowerShell komutuyla [müşteri tarafından yönetilen anahtarları yapılandırma](../storage/common/customer-managed-keys-configure-key-vault.md) , [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey).

* HTTPS PUT isteğiyle Logic Apps REST API çağırarak ıSE oluşturmak için kullanabileceğiniz bir araç. Örneğin [Postman](https://www.getpostman.com/downloads/)'ı kullanabilir veya bu görevi gerçekleştiren bir mantıksal uygulama oluşturabilirsiniz.

<a name="enable-support-key-managed-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>Anahtar Kasası ve yönetilen kimlik desteği ile ıSE oluşturma

Logic Apps REST API çağırarak ıSE oluşturmak için, bu HTTPS PUT isteğini yapın:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Logic Apps REST API 2019-05-01 sürümü, ıSE bağlayıcıları için kendi HTTPS PUT isteğinizi yapmanızı gerektirir.

Dağıtımın tamamlanabilmesi için genellikle iki saat içinde sürer. Bazen dağıtım dört saate kadar sürebilir. Dağıtım durumunu denetlemek için, Azure araç çubuğinizdeki [Azure Portal](https://portal.azure.com)bildirimler bölmesini açan Bildirimler simgesini seçin.

> [!NOTE]
> Dağıtım başarısız olursa veya ıSE 'yi silerseniz, Azure, alt ağlarınızı serbest bırakmadan önce bir saat kadar sürebilir. Bu gecikme, başka bir ıSE içinde bu alt ağları yeniden kullanmadan önce beklemeniz gerekebilecek anlamına gelir.
>
> Sanal ağınızı silerseniz Azure, alt ağlarınızı serbest bırakmadan genellikle iki saate kadar sürer, ancak bu işlem daha uzun sürebilir. 
> Sanal ağları silerken, hala bağlı hiçbir kaynak bulunmadığından emin olun. 
> Bkz. [sanal ağı silme](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

### <a name="request-header"></a>İstek üst bilgisi

İstek üstbilgisinde şu özellikleri ekleyin:

* `Content-type`: Bu özellik değerini olarak ayarlayın `application/json` .

* `Authorization`: Bu özellik değerini, kullanmak istediğiniz Azure aboneliğine veya kaynak grubuna erişimi olan müşterinin taşıyıcı belirtecine ayarlayın.

### <a name="request-body"></a>İstek gövdesi

İstek gövdesinde, bilgileri ıSE tanımınızda bilgilerini sağlayarak bu ek öğelere yönelik desteği etkinleştirin:

* ISE 'nizin anahtar kasanıza erişmek için kullandığı yönetilen kimlik
* Anahtar kasanızın ve kullanmak istediğiniz müşteri tarafından yönetilen anahtar

#### <a name="request-body-syntax"></a>İstek gövdesi sözdizimi

İşte, ıSE 'nizi oluştururken kullanılacak özellikleri açıklayan istek gövdesi sözdizimi şöyledir:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "identity": {
      "type": <"SystemAssigned" | "UserAssigned">,
      // When type is "UserAssigned", include the following "userAssignedIdentities" object:
      "userAssignedIdentities": {
         "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{user-assigned-managed-identity-object-ID}": {
            "principalId": "{principal-ID}",
            "clientId": "{client-ID}"
         }
      }
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.KeyVault/vaults/{key-vault-name}",
            },
            "keyName": "{customer-managed-key-name}",
            "keyVersion": "{key-version-number}"
         }
      }
   }
}
```

#### <a name="request-body-example"></a>İstek gövdesi örneği

Bu örnek istek gövdesinde örnek değerler gösterilmektedir:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
         "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/*********************************": {
            "principalId": "*********************************",
            "clientId": "*********************************"
         }
      }
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.KeyVault/vaults/FabrikamKeyVault",
            },
            "keyName": "Fabrikam-Encryption-Key",
            "keyVersion": "********************"
         }
      }
   }
}
```

<a name="identity-access-to-key-vault"></a>

## <a name="grant-access-to-your-key-vault"></a>Anahtar kasanıza erişim izni verin

Zamanlama, kullandığınız yönetilen kimliğe göre farklılık gösterir, ancak [Ise 'nin yönetilen kimliğine Anahtar Kasası erişimi sağlamanız](#identity-access-to-key-vault)gerekir.

* **Sistem tarafından atanan yönetilen kimlik**: Ise 'YI oluşturan https put isteğini gönderdikten *sonra 30 dakika* içinde, Ise 'nin sistem tarafından atanan yönetilen kimliği için anahtar kasanıza bir erişim ilkesi eklemeniz gerekir. Aksi takdirde, ıSE 'niz için oluşturma işlemi başarısız olur ve bir izin hatası alırsınız.

* **Kullanıcı tarafından atanan yönetilen kimlik**: Ise 'YI oluşturan https put isteğini göndermeden önce, Ise 'nin Kullanıcı tarafından atanan yönetilen kimliği için anahtar kasanıza bir erişim ilkesi ekleyin.

Bu görev için Azure PowerShell [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) komutunu kullanabilir ya da Azure Portal aşağıdaki adımları izleyebilirsiniz:

1. [Azure Portal](https://portal.azure.com)Azure anahtar kasanızı açın.

1. Anahtar Kasası menüsünde erişim **ilkeleri**  >  **ekleme İlkesi Ekle**' yi seçin, örneğin:

   ![Sistem tarafından atanan yönetilen kimlik için erişim ilkesi ekleme](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. **Erişim Ilkesi Ekle** bölmesi açıldıktan sonra aşağıdaki adımları izleyin:

   1. Şu seçenekleri belirleyin:

      | Ayar | Değerler |
      |---------|--------|
      | **Şablondan yapılandırma (isteğe bağlı) listesinden** | Anahtar yönetimi |
      | **Anahtar izinleri** | - **Anahtar yönetim işlemleri**: get, List <p><p>- **Şifreleme işlemleri**: anahtar kaydırmayı geri al, tuşu sarmala |
      |||

      !["Anahtar yönetimi" > "anahtar izinleri" ni seçin](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. **Asıl seçin** Için **Seçili hiçbiri**' ni seçin. **Asıl** bölmesi açıldıktan sonra arama kutusunda, Ise 'nizi bulun ve seçin. İşiniz bittiğinde Ekle ' **yi seçin**  >  .

      ![Asıl öğe olarak kullanılacak ıSE 'yi seçin](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. **Erişim ilkeleri** bölmesi ile Işiniz bittiğinde **Kaydet**' i seçin.

Daha fazla bilgi için bkz. [kimlik doğrulaması Key Vault](../key-vault/general/authentication.md) ve [Key Vault erişim ilkesi atama](../key-vault/general/assign-access-policy-portal.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Key Vault](../key-vault/general/overview.md) hakkında daha fazla bilgi edinin
