---
title: Azure DevTest Labs laboratuvar tarafından kullanılan bir Azure Depolama hesabını şifreleyin
description: Azure DevTest Labs laboratuvar tarafından kullanılan Azure depolama 'nın şifrelenmesini nasıl yapılandıracağınızı öğrenin
ms.topic: how-to
ms.date: 07/29/2020
ms.openlocfilehash: dcede89fb23c532742e41121688bcb51a5a73833
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92149314"
---
# <a name="encrypt-azure-storage-used-by-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs 'da laboratuvar tarafından kullanılan Azure depolama alanını şifreleyin
Azure DevTest Labs ' de oluşturulan her laboratuvar, ilişkili bir Azure depolama hesabıyla oluşturulur. Depolama hesabı aşağıdaki amaçlar için kullanılır: 

- Sanal makineler oluşturmak için kullanılabilecek [Formül](devtest-lab-manage-formulas.md) belgelerini depolama.
- Yapıtları uygulamadan oluşturulan dağıtım ve uzantı günlüklerini içeren yapıt sonuçları depolanıyor. 
- [Laboratuvarda özel görüntüler oluşturmak için sanal sabit diskleri (VHD 'ler) karşıya yükleme.](devtest-lab-create-template.md)
- Sanal makine/ortam oluşturma sırasında daha hızlı bir şekilde almak için sık kullanılan [yapıtları](add-artifact-vm.md) ve [Azure Resource Manager şablonlarını](devtest-lab-create-environment-from-arm.md) önbelleğe alma

> [!NOTE]
> Yukarıdaki bilgiler, laboratuvarın çalışması için kritik öneme sahiptir. Açıkça silinmediği takdirde laboratuvarın (ve laboratuvar kaynaklarının) ömrü için depolanır. Bu kaynakları el ile silmek, laboratuvar VM 'Leri oluşturma ve/veya Formüllerin daha sonra kullanılmak üzere bozulmuş olmasına neden olabilir. 

## <a name="locate-the-storage-account-and-view-its-contents"></a>Depolama hesabını bulun ve içeriğini görüntüleyin

1. Laboratuvarın giriş sayfasında **genel bakış** sayfasında **kaynak grubunu** seçin. Laboratuvarın bulunduğu kaynak grubu için **kaynak grubu** sayfasını görmeniz gerekir. 

    :::image type="content" source="./media/encrypt-storage/overview-resource-group-link.png" alt-text="Genel Bakış sayfasında kaynak grubu ' nu seçin":::
1. Laboratuvarın Azure Storage hesabını seçin. Laboratuvar depolama hesabı için adlandırma kuralı: `a<labNameWithoutInvalidCharacters><4-digit number>` . Örneğin, laboratuvar adı ise `contosolab` , depolama hesabı adı olabilir `acontosolab7576` . 

    :::image type="content" source="./media/encrypt-storage/select-storage-account.png" alt-text="Laboratuvarın kaynak grubunda depolama hesabı seçin":::
3. **Depolama hesabı** sayfasında, soldaki menüden **Depolama Gezgini (Önizleme)** öğesini seçin ve ardından laboratuarda ilgili Ilgili IÇERIĞI bulmak için **BLOB kapsayıcıları** ' nı seçin. 

   :::image type="content" source="./media/encrypt-storage/storage-explorer.png" alt-text="Depolama Gezgini (Önizleme)" lightbox="./media/encrypt-storage/storage-explorer.png":::

## <a name="encrypt-the-lab-storage-account"></a>Laboratuvar depolama hesabını şifreleyin
Azure depolama, bulutta kalıcı olduğunda verilerinizi otomatik olarak şifreler. Azure depolama şifrelemesi verilerinizi korur ve kurumsal güvenlik ve uyumluluk taahhütlerinizi karşılamanıza yardımcı olur. Daha fazla bilgi için bkz. [bekleyen veriler Için Azure depolama şifrelemesi](../storage/common/storage-service-encryption.md).

Laboratuvar depolama hesabındaki veriler, **Microsoft tarafından yönetilen bir anahtarla** şifrelenir. Verilerinizin şifrelenmesi için Microsoft tarafından yönetilen anahtarları kullanabilir veya kendi anahtarlarınız ile şifrelemeyi yönetebilirsiniz. Laboratuvarın depolama hesabı için kendi anahtarlarınız ile şifrelemeyi yönetmeyi seçerseniz, blob depolamada ve Azure dosyalarında verileri şifrelemek/şifrelerini çözmek için Azure Key Vault ile **müşteri tarafından yönetilen bir anahtar** belirtebilirsiniz. Müşteri tarafından yönetilen anahtarlar hakkında daha fazla bilgi için bkz. [Azure depolama şifrelemesini yönetmek için Azure Key Vault ile müşteri tarafından yönetilen anahtarları kullanma](../storage/common/customer-managed-keys-overview.md).

Azure depolama şifrelemesi için müşteri tarafından yönetilen anahtarların nasıl yapılandırılacağını öğrenmek için aşağıdaki makalelere bakın: 

- [Azure portalındaki](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure PowerShell](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure CLI](../storage/common/customer-managed-keys-configure-key-vault.md)


## <a name="manage-the-azure-blob-storage-life-cycle"></a>Azure Blob depolama yaşam döngüsünü yönetme
Belirtildiği gibi, laboratuvarın depolama hesabında depolanan bilgiler, laboratuvarın herhangi bir hata olmadan çalışması için kritik öneme sahiptir. Açıkça silinmediği takdirde, bu veriler, verilerin türüne bağlı olarak, laboratuvarın veya belirli laboratuvar sanal makinelerinin ömrü boyunca laboratuvarın depolama hesabında kalmaya devam eder.

### <a name="uploaded-vhds"></a>Karşıya yüklenen VHD 'ler
Bu VHD 'ler, özel görüntüler oluşturmak için kullanılır. Bunları kaldırmak, bu VHD 'lerden özel görüntüler oluşturmak için artık mümkün olmayacaktır.

### <a name="artifacts-cache"></a>Yapıt önbelleği
Bu önbellekler, yapıtların uygulandığı zaman yeniden oluşturulur. İlgili başvurulan depolardaki en son içerikle yenilenir. Bu nedenle, depolama ile ilgili giderleri kaydetmek için bu bilgileri silerseniz, emniyet geçici olur.

### <a name="azure-resource-manager-template-cache"></a>Azure Resource Manager şablonu önbelleği
Bu önbellekler, Azure Resource Manager tabanlı şablon depoları bağlı olduğunda ve laboratuvarda baştan sona kadar her zaman yeniden oluşturulacaktır. İlgili başvurulan depolardaki en son içerikle yenilenir. Bu nedenle, depolama ile ilgili giderleri kaydetmek için bu bilgileri silerseniz, emniyet geçici olur.

### <a name="formulas"></a>Formüller
Bu belgeler, hem mevcut VM 'lerden formül oluşturma hem de formüllerin VM oluşturma seçeneğini desteklemek için kullanılır. Bu formül belgelerinin silinmesi, aşağıdaki işlemleri gerçekleştirirken hatalara neden olabilir:

- Mevcut laboratuvar VM 'sinden formül oluşturma
- Formüller oluşturma veya güncelleştirme 
- Formülden VM oluşturma.

### <a name="artifact-results"></a>Yapıt sonuçları
Yapıtlar uygulanırken, ilgili yapıt sonuçlarının boyutu, laboratuvar VM 'lerinde çalıştırılan yapıların sayısına ve türüne bağlı olarak zaman içinde artabilir. Bu nedenle, bir laboratuvar sahibi olarak söz konusu belgelerin yaşam döngüsünü denetlemek isteyebilirsiniz. Daha fazla bilgi için bkz. [Azure Blob depolama yaşam döngüsünü yönetme](../storage/blobs/storage-lifecycle-management-concepts.md).

> [!IMPORTANT]
> Azure depolama hesabıyla ilişkili giderleri azaltmak için bu adımı yapmanızı öneririz. 

Örneğin, aşağıdaki kural özel olarak yapıt sonuçları için 90 günlük bir süre sonu kuralı ayarlamak üzere kullanılır. Eski yapıt sonuçlarının düzenli bir temposunda depolama hesabından geri dönüştürüldüğünden emin olur.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "artifacts/results" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 90 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Sonraki adımlar
Azure depolama şifrelemesi için müşteri tarafından yönetilen anahtarların nasıl yapılandırılacağını öğrenmek için aşağıdaki makalelere bakın: 

- [Azure portalındaki](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure PowerShell](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure CLI](../storage/common/customer-managed-keys-configure-key-vault.md)