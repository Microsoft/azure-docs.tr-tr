---
title: Azure HPC önbelleğindeki verileri şifrelemek için müşteri tarafından yönetilen anahtarları kullanma
description: Microsoft tarafından yönetilen varsayılan şifreleme anahtarlarını kullanmak yerine şifreleme anahtarı erişimini denetlemek için Azure HPC Cache ile Azure Key Vault kullanma
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/20/2020
ms.author: v-erkel
ms.openlocfilehash: ae4c52ec1390166eccb0e73d6f81a8553c445b2e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813299"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Azure HPC Cache için müşteri tarafından yönetilen şifreleme anahtarlarını kullanma

Azure HPC Cache 'te verilerinizi şifrelemek için kullanılan anahtarların sahipliğini denetlemek için Azure Key Vault kullanabilirsiniz. Bu makalede, önbellek veri şifrelemesi için müşteri tarafından yönetilen anahtarların nasıl kullanılacağı açıklanmaktadır.

> [!NOTE]
> Azure 'da depolanan tüm veriler, önbellek disklerinde de dahil olmak üzere varsayılan olarak Microsoft tarafından yönetilen anahtarlar kullanılarak şifrelenir. Yalnızca verilerinizi şifrelemek için kullanılan anahtarları yönetmek istiyorsanız bu makaledeki adımları izlemeniz gerekir.

Azure HPC önbelleği, önbellek diskleri için bir müşteri anahtarı eklemeseniz bile, önbelleğe alınmış verilerinizi tutan yönetilen disklerde [VM ana bilgisayar şifrelemesi](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data) ile de korunur. Çift şifreleme için müşteri tarafından yönetilen bir anahtar eklemek, yüksek güvenlik gereksinimlerine sahip müşteriler için ek bir güvenlik düzeyi sağlar. Ayrıntılar için [Azure disk depolaması 'Nın sunucu tarafı şifrelemesini](../virtual-machines/disk-encryption.md) okuyun.

Azure HPC Cache için müşteri tarafından yönetilen anahtar şifrelemeyi etkinleştirmek için üç adım vardır:

1. Anahtarları depolamak için bir Azure Key Vault ayarlayın.
1. Azure HPC önbelleğini oluştururken, müşteri tarafından yönetilen anahtar şifrelemesi ' ni seçin ve kullanılacak anahtar kasasını ve anahtarı belirtin.
1. Önbellek oluşturulduktan sonra anahtar kasasına erişim yetkisi verin.

Şifreleme, yeni oluşturulan önbellekten yetkilendirilene kadar tamamen ayarlanamaz (3. adım). Bunun nedeni, yetkili bir kullanıcı yapmak için önbelleğin kimliğini anahtar kasasına iletmeniz gerekir. Önbellek oluşturuluncaya kadar kimlik bulunmadığından, önbelleği oluşturmadan önce bunu yapamazsınız.

Önbelleği oluşturduktan sonra, müşteri tarafından yönetilen anahtarlar ve Microsoft tarafından yönetilen anahtarlar arasında geçiş yapamazsınız. Ancak önbelleğiniz müşteri tarafından yönetilen anahtarlar kullanıyorsa, şifreleme anahtarını, anahtar sürümünü ve anahtar kasasını gerektiği gibi [değiştirebilirsiniz](#update-key-settings) .

## <a name="understand-key-vault-and-key-requirements"></a>Anahtar kasasını ve anahtar gereksinimlerini anlayın

Anahtar Kasası ve anahtarın Azure HPC önbelleğiyle çalışması için bu gereksinimleri karşılaması gerekir.

Anahtar Kasası özellikleri:

* **Abonelik** -önbellek için kullanılan aboneliği kullanın.
* **Bölge** -Anahtar Kasası, Azure HPC önbelleğiyle aynı bölgede olmalıdır.
* **Fiyatlandırma katmanı** -Standart katman, Azure HPC Cache ile kullanım için yeterlidir.
* **Geçici silme** -Azure HPC önbelleği, anahtar kasasında zaten yapılandırılmamışsa geçici silme olanağı sağlar.
* **Temizleme koruması** -Temizleme korumasının etkinleştirilmesi gerekir.
* **Erişim ilkesi** -varsayılan ayarlar yeterlidir.
* **Ağ bağlantısı** -Azure HPC önbelleğinin, seçtiğiniz uç nokta ayarlarından bağımsız olarak anahtar kasasına erişebilmesi gerekir.

Anahtar özellikleri:

* **Anahtar türü** -RSA
* **RSA anahtar boyutu** -2048
* **Etkin** -Evet

Anahtar Kasası erişim izinleri:

* Azure HPC önbelleğini oluşturan kullanıcının [Key Vault katkıda bulunan rolüne](../role-based-access-control/built-in-roles.md#key-vault-contributor)eşdeğer izinlere sahip olması gerekir. Azure Key Vault ayarlamak ve yönetmek için aynı izinler gerekir.

  Daha fazla bilgi için [bir anahtar kasasına güvenli erişimi](../key-vault/general/security-features.md) okuyun.

## <a name="1-set-up-azure-key-vault"></a>1. Azure Key Vault ayarla

Önbelleği oluşturmadan önce bir anahtar kasası ve anahtar ayarlayabilir veya önbellek oluşturmanın bir parçası olarak bunu yapabilirsiniz. Bu kaynakların [yukarıda](#understand-key-vault-and-key-requirements)özetlenen gereksinimleri karşıladığından emin olun.

Önbellek oluşturma sırasında, önbelleğin şifrelemesi için kullanılacak bir kasa, anahtar ve anahtar sürümü belirtmeniz gerekir.

Ayrıntılar için [Azure Key Vault belgelerini](../key-vault/general/overview.md) okuyun.

> [!NOTE]
> Azure Key Vault aynı aboneliği kullanmalıdır ve Azure HPC önbelleğiyle aynı bölgede olmalıdır. Seçtiğiniz bölgenin [her iki ürünü de desteklediğinden](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault)emin olun.

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. bir önbelleği, müşteri tarafından yönetilen anahtarlar etkin olarak oluşturun

Azure HPC önbelleğinizi oluştururken şifreleme anahtarı kaynağını belirtmeniz gerekir. [Azure HPC önbelleği oluşturma](hpc-cache-create.md)bölümündeki yönergeleri Izleyin ve **disk şifreleme anahtarları** sayfasında anahtar kasasını ve anahtarı belirtin. Önbellek oluşturma sırasında yeni bir anahtar kasası ve anahtar oluşturabilirsiniz.

> [!TIP]
> **Disk şifreleme anahtarları** sayfası görünmezse, önbelleğinizin [desteklenen bölgelerden](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault)birinde olduğundan emin olun.

Önbelleği oluşturan kullanıcının [Key Vault katkıda bulunan rolüne](../role-based-access-control/built-in-roles.md#key-vault-contributor) veya üstüne eşit ayrıcalıklara sahip olması gerekir.

1. Özel olarak yönetilen anahtarları etkinleştirmek için düğmeye tıklayın. Bu ayarı değiştirdikten sonra Anahtar Kasası ayarları görüntülenir.

1. Anahtar seçim sayfasını açmak için **Anahtar Kasası Seç** ' e tıklayın. Bu önbelleğin disklerinde verileri şifrelemek için anahtar kasası ve anahtar seçin veya oluşturun.

   Azure Key Vault listede görünmüyorsa, şu gereksinimleri kontrol edin:

   * Önbellek, anahtar kasası ile aynı abonelikte mi?
   * Önbellek, anahtar kasası ile aynı bölgede mi?
   * Azure portal ile Anahtar Kasası arasında ağ bağlantısı var mı?

1. Bir kasa seçtikten sonra, kullanılabilir seçeneklerden ayrı anahtarı seçin veya yeni bir anahtar oluşturun. Anahtar, 2048 bitlik bir RSA anahtarı olmalıdır.

1. Seçili anahtar için sürüm belirtin. [Azure Key Vault belgelerinde](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)sürüm oluşturma hakkında daha fazla bilgi edinin.

Belirtimlerin geri kalanı ile devam edin ve [Azure HPC önbelleği oluşturma](hpc-cache-create.md)bölümünde açıklandığı gibi önbelleği oluşturun.

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. Azure Key Vault şifrelemeyi önbellekten yetkilendir
<!-- header is linked from create article, update if changed -->

Birkaç dakika sonra, yeni Azure HPC önbelleği Azure portal görüntülenir. Azure Key Vault erişim yetkisi vermek ve müşterinin yönettiği anahtar şifrelemesini etkinleştirmek için **genel bakış** sayfasına gidin.

> [!TIP]
> Önbellek, "dağıtım çalışma" iletileri silinmeden önce kaynaklar listesinde görünebilir. Bir başarı bildirimi beklemek yerine bir dakika veya iki dakika sonra kaynaklarınızın listesini denetleyin.

Bu iki adımlı işlem gereklidir çünkü Azure HPC önbellek örneğinin yetkilendirme için Azure Key Vault bir kimliğe geçmesi gerekir. Önbellek kimliği, ilk oluşturma adımları tamamlanana kadar mevcut değil.

> [!NOTE]
> Önbelleği oluşturduktan sonra 90 dakika içinde şifrelemeyi yetkilendirmelisiniz. Bu adımı tamamlamazsanız, önbellek zaman aşımına uğrar ve başarısız olur. Başarısız bir önbelleğin yeniden oluşturulması gerekir, bu, sabit olamaz.

Önbellek, **anahtar Için bekleyen** durumu gösterir. Önbelleğin belirtilen anahtar kasasına erişmesini sağlamak için sayfanın üst kısmındaki **şifrelemeyi etkinleştir** düğmesine tıklayın.

![Portalda önbelleğe Genel Bakış sayfasının ekran görüntüsü, şifrelemeyi etkinleştir düğmesi (üst satır) ve durum: anahtar bekleniyor](media/waiting-for-key.png)

**Şifrelemeyi etkinleştir** ' e tıklayın ve ardından önbelleğin şifreleme anahtarını kullanmasını sağlamak için **Evet** düğmesine tıklayın. Bu eylem, anahtar kasasında geçici silme ve Temizleme korumasını (zaten etkinleştirilmemişse) da sunar.

![portalda, kullanıcıdan şifrelemeyi etkinleştirmesini isteyen üst kısımdaki bir başlık iletisi içeren, önbelleğe Genel Bakış sayfasının ekran görüntüsü](media/enable-keyvault.png)

Önbellek anahtar kasasına erişim istediğinde, önbelleğe alınmış verileri depolayan diskleri oluşturabilir ve şifreleyebilir.

Şifrelemeyi yetkilendirdikten sonra Azure HPC Cache, şifrelenmiş diskler ve ilgili altyapıyı oluşturmak için birkaç dakika daha sürer.

## <a name="update-key-settings"></a>Anahtar ayarlarını Güncelleştir

Önbelleğiniz için Anahtar Kasası, anahtar veya anahtar sürümünü Azure portal değiştirebilirsiniz. **Müşteri anahtarı ayarları** sayfasını açmak Için önbelleğin **şifreleme** ayarları bağlantısına tıklayın.

Bir önbelleği, müşteri tarafından yönetilen anahtarlar ve sistem tarafından yönetilen anahtarlar arasında değiştiremezsiniz.

![Azure portal önbellek sayfasından ayarlar > şifreleme ' ya tıklanarak erişilen "müşteri anahtarları ayarları" sayfasının ekran görüntüsü](media/change-key-click.png)

Anahtarı **Değiştir** bağlantısına tıklayın, sonra anahtar seçiciyi açmak için anahtar **kasasını, anahtarı veya sürümü değiştirin** ' e tıklayın.

![Anahtar Kasası, anahtar ve sürümü seçmek için üç aşağı açılan seçici içeren "Azure Key Vault anahtar seçin" sayfasında ekran görüntüsü](media/select-new-key.png)

Aynı abonelikte ve bu önbelleğiyle aynı bölgedeki Anahtar kasaları listede gösteriliyor.

Yeni şifreleme anahtarı değerlerini seçtikten sonra **Seç**' e tıklayın. Yeni değerlerle bir onay sayfası görüntülenir. Seçimi sonlandırmak için **Kaydet** ' e tıklayın.

![Sol üstteki Kaydet düğmesine sahip onay sayfasının ekran görüntüsü](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Azure 'da müşteri tarafından yönetilen anahtarlar hakkında daha fazla bilgi edinin

Bu makaleler, Azure 'da verileri şifrelemek için Azure Key Vault ve müşteri tarafından yönetilen anahtarları kullanma hakkında daha fazla bilgi açıklamaktadır:

* [Azure depolama şifrelemeye genel bakış](../storage/common/storage-service-encryption.md)
* [Müşteri tarafından yönetilen anahtarlarla disk şifrelemesi](../virtual-machines/disk-encryption.md#customer-managed-keys) -Azure HPC önbelleğine benzer bir senaryo olan yönetilen disklerle Azure Key Vault kullanmaya yönelik belgeler

## <a name="next-steps"></a>Sonraki adımlar

Azure HPC önbelleğini oluşturduktan ve Key Vault tabanlı şifrelemeyi yetkilendirdikten sonra, veri kaynaklarınıza erişim izni vererek önbelleğinizi ayarlamaya devam edin.

* [Depolama hedefleri ekleme](hpc-cache-add-storage.md)
