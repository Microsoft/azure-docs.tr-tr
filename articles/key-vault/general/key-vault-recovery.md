---
title: Azure Key Vault kurtarmaya genel bakış | Microsoft Docs
description: Key Vault kurtarma özellikleri, anahtar kasalarınızın, anahtar kasasının içinde depolanan gizli dizileri, anahtarları ve sertifikaları yanlışlıkla veya kötü amaçlı olarak silmenin önlenmesi için tasarlanmıştır.
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.author: mbaldwin
author: msmbaldwin
ms.date: 09/30/2020
ms.openlocfilehash: b9c249bedd0432458b3e6f5c010cdc5ff39dff44
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815675"
---
# <a name="azure-key-vault-recovery-management-with-soft-delete-and-purge-protection"></a>Geçici silme ve Temizleme koruması ile kurtarma yönetimi Azure Key Vault

Bu makalede Azure Key Vault, geçici silme ve Temizleme korumasının iki kurtarma özelliği ele alınmaktadır. Bu belge, bu özelliklere genel bir bakış sağlar ve bunları Azure portal, Azure CLı ve Azure PowerShell aracılığıyla nasıl yönetebileceğinizi gösterir.

Key Vault hakkında daha fazla bilgi için bkz.
- [Anahtar Kasasına genel bakış](overview.md)
- [Azure Key Vault anahtarlar, gizlilikler ve sertifikalara genel bakış](about-keys-secrets-certificates.md)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/dotnet)
* [PowerShell modülü](/powershell/azure/install-az-ps).
* [Azure CLI](/cli/azure/install-azure-cli)
* Key Vault- [Azure clı](../general/quick-create-cli.md) [Azure Portal](../general/quick-create-portal.md) veya [Azure PowerShell](../general/quick-create-powershell.md) kullanarak bir tane oluşturabilirsiniz.
* Kullanıcı, geçici olarak silinen kasaların işlemlerini gerçekleştirmek için aşağıdaki izinlere (abonelik düzeyinde) gerek duyar:

  | İzin | Description |
  |---|---|
  |Microsoft. Keykasası/konumlar/Silinkaults/okuma|Geçici olarak silinen bir anahtar kasasının özelliklerini görüntüleyin|
  |Microsoft. Keykasası/konumlar/Silinkaults/Temizleme/eylem|Geçici olarak silinen bir anahtar kasasını temizle|


## <a name="what-are-soft-delete-and-purge-protection"></a>Geçici silme ve Temizleme koruması nedir?

[Geçici silme](soft-delete-overview.md) ve Temizleme koruması iki farklı Anahtar Kasası kurtarma özellikleridir.

> [!IMPORTANT]
> Geçici silme özelliğini açmak, anahtar kasalarınızın ve kimlik bilgilerinizin yanlışlıkla silinmeye karşı korunmasını sağlamak açısından önemlidir. Ancak, geçici silme özelliğini açmak, Uygulama mantığınızı değiştirmenizi veya hizmet sorumlularınız için ek izinler sağlamanızı gerektirebileceğinden, bir son değişiklik olarak değerlendirilir. Aşağıdaki yönergeleri kullanarak geçici silme özelliğini açmadan önce, lütfen uygulamanızın bu belgeyi kullanarak değişiklik ile uyumlu olduğundan emin olun [ .](soft-delete-change.md)

**Geçici silme** , Anahtar Kasası içinde depolanan anahtar kasası ve anahtarlarınızın, Sırlarınızın ve sertifikaların yanlışlıkla silinmesini engellemek için tasarlanmıştır. Geri dönüşüm kutusu gibi geçici silme düşünün. Bir anahtar kasasını veya bir Anahtar Kasası nesnesini sildiğinizde, kullanıcı yapılandırılabilir bir saklama süresi veya varsayılan 90 gün için kurtarılabilir olarak kalır. Geçici olarak silinen durumdaki Anahtar kasaları **de silinebilir, bu da** kalıcı olarak silinir. Bu, anahtar kasalarını ve Anahtar Kasası nesnelerini aynı adla yeniden oluşturmanızı sağlar. Anahtar kasalarını ve nesneleri kurtarmak ve silmek, yükseltilmiş erişim ilkesi izinleri gerektirir. **Geçici silme etkinleştirildikten sonra devre dışı bırakılamaz.**

**Anahtar Kasası adlarının genel olarak benzersiz** olduğunu ve bu nedenle, geçici olarak silinen durumunda anahtar kasası ile aynı ada sahip bir Anahtar Kasası oluşturamayabilmeniz önemlidir. Benzer şekilde, anahtarlar, gizlilikler ve sertifikaların adları bir Anahtar Kasası içinde benzersizdir. Geçici olarak silinen durumunda diğeri ile aynı ada sahip bir gizli dizi, anahtar veya sertifika oluşturamazsınız.

**Temizleme koruması** , kötü amaçlı bir Insider tarafından anahtar kasasının, anahtarlarınızın, Sırlarınızın ve sertifikalarınızın silinmesini engellemek için tasarlanmıştır. Bunu, zaman tabanlı bir kilide geri dönüşüm kutusu olarak düşünün. Yapılandırılabilir Bekletme dönemi boyunca herhangi bir noktada öğeleri kurtarabilirsiniz. **Bekletme süresi sona erdiğinde bir anahtar kasasını kalıcı olarak silemez veya temizleyemezsiniz.** Bekletme süresi, anahtar kasasını geçtiğinde veya Anahtar Kasası nesnesi otomatik olarak temizlenir.

> [!NOTE]
> Temizleme koruması, hiçbir yönetici rolü veya izninin, temizleme korumasını geçersiz kılabilir, devre dışı bırakamaz veya atlatmak için tasarlanmıştır. **Temizleme koruması etkinleştirildikten sonra, Microsoft dahil herkes tarafından devre dışı bırakılamaz veya geçersiz kılınamaz.** Bu, silinen bir anahtar kasasını kurtarmanız veya Anahtar Kasası adını yeniden kullanmadan önce bekletme süresinin geçmesini beklemeniz gerektiği anlamına gelir.

Geçici silme hakkında daha fazla bilgi için bkz. [Azure Key Vault geçici genel bakış](soft-delete-overview.md)

# <a name="azure-portal"></a>[Azure portalı](#tab/azure-portal)

## <a name="verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete"></a>Bir anahtar kasasında geçici silmenin etkinleştirilip etkinleştirilmediğini ve geçici silme özelliğini etkinleştirip etkinleştirmediğinizi doğrulayın

1. Azure portalında oturum açın.
1. Anahtar kasanızı seçin.
1. "Özellikler" dikey penceresine tıklayın.
1. Geçici düğmenin yanındaki radyo düğmesinin "kurtarmayı etkinleştir" olarak ayarlandığını doğrulayın.
1. Anahtar kasasında geçici silme etkinleştirilmemişse radyo düğmesine tıklayarak geçici silme özelliğini etkinleştirin ve "Kaydet" e tıklayın.

:::image type="content" source="../media/key-vault-recovery-1.png" alt-text="Özellikler sayfasında, geçici silme, etkinleştirmek için değer olduğu gibi vurgulanır.":::

## <a name="grant-access-to-a-service-principal-to-purge-and-recover-deleted-secrets"></a>Silinen gizli dizileri temizlemek ve kurtarmak için bir hizmet sorumlusuna erişim izni verme

1. Azure portalında oturum açın.
1. Anahtar kasanızı seçin.
1. "Erişim Ilkesi" dikey penceresine tıklayın.
1. Tabloda, erişim vermek istediğiniz güvenlik sorumlusunun satırını bulun (veya yeni bir güvenlik sorumlusu ekleyin).
1. Anahtarlar, sertifikalar ve gizli dizileri için açılan liste ' ye tıklayın.
1. Açılan pencerenin alt kısmına ilerleyin ve "kurtar" ve "Temizle" seçeneğine tıklayın
1. Güvenlik sorumluları, çoğu işlemi gerçekleştirmek için Get ve List işlevselliğine de ihtiyaç duyar.

:::image type="content" source="../media/key-vault-recovery-2.png" alt-text="Sol gezinti bölmesinde, erişim ilkeleri vurgulanır. Erişim ilkelerinde, gizli pozisyonlar açılan listesi gösterilir ve dört öğe seçilir: get, List, Recover ve temizle.":::

## <a name="list-recover-or-purge-a-soft-deleted-key-vault"></a>Geçici olarak silinen bir anahtar kasasını listeleme, kurtarma veya temizleme

1. Azure portalında oturum açın.
1. Sayfanın üst kısmındaki arama çubuğuna tıklayın.
1. "Son hizmetler" altında "Key Vault" düğmesine tıklayın. Tek bir anahtar kasasına tıklamayın.
1. Ekranın üst kısmında "silinmiş kasaları yönetme" seçeneğine tıklayın.
1. Ekranınızın sağ tarafında bir bağlam bölmesi açılır.
1. Aboneliğinizi seçin.
1. Anahtar kasanızın geçici olarak silinmesi durumunda, sağ taraftaki bağlam bölmesinde görünür.
1. Çok fazla kasa varsa, bağlam bölmesinin alt kısmındaki "daha fazla yükle" seçeneğine tıklayabilir ya da sonuçları almak için CLı veya PowerShell kullanabilirsiniz.
1. Kurtarmak veya temizlemek istediğiniz kasayı bulduktan sonra yanındaki onay kutusunu işaretleyin.
1. Anahtar kasasını kurtarmak istiyorsanız bağlam bölmesinin alt kısmındaki kurtar seçeneğini belirleyin.
1. Anahtar kasasını kalıcı olarak silmek istiyorsanız Temizleme seçeneğini belirleyin.

:::image type="content" source="../media/key-vault-recovery-3.png" alt-text="Anahtar kasalarında, silinen kasaları Yönet seçeneği vurgulanır.":::

:::image type="content" source="../media/key-vault-recovery-4.png" alt-text="Silinen anahtar kasalarını Yönet sayfasında, yalnızca listelenen Anahtar Kasası vurgulanır ve seçilir ve kurtar düğmesi vurgulanır.":::

## <a name="list-recover-or-purge-soft-deleted-secrets-keys-and-certificates"></a>Geçici silinen gizli dizileri, anahtarları ve sertifikaları listeleme, kurtarma veya temizleme

1. Azure portalında oturum açın.
1. Anahtar kasanızı seçin.
1. Yönetmek istediğiniz gizli dizi türüne karşılık gelen dikey pencereyi (anahtarlar, gizlilikler veya sertifikalar) seçin.
1. Ekranın üst kısmında "silinmiş öğeleri Yönet (anahtarlar, gizlilikler veya sertifikalar) seçeneğine tıklayın.
1. Ekranınızın sağ tarafında bir bağlam bölmesi görünür.
1. Gizli anahtar, anahtarınız veya sertifikanız listede görünmezse, geçici olarak silinen durumunda değildir.
1. Yönetmek istediğiniz gizli dizi, anahtar veya sertifikayı seçin.
1. Bağlam bölmesinin alt kısmındaki kurtarma veya Temizleme seçeneğini belirleyin.

:::image type="content" source="../media/key-vault-recovery-5.png" alt-text="Anahtarlar üzerinde, silinen anahtarları Yönet seçeneği vurgulanır.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="key-vault-cli"></a>Key Vault (CLı)

* Anahtar kasasının geçici silme özelliğinin etkin olup olmadığını doğrulama

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Anahtar kasasında geçici silme özelliğini etkinleştirme

    Tüm yeni anahtar kasaları, varsayılan olarak etkinleştirilmiş geçici bir silme özelliği vardır. Şu anda geçici silme özelliği etkin olmayan bir anahtar kasanıza sahipseniz, geçici silme özelliğini etkinleştirmek için aşağıdaki komutu kullanın.

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-soft-delete true
    ```

* Anahtar kasasını Sil (geçici silme etkinse kurtarılabilir)

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Tüm yazılımla Silinen anahtar kasalarını Listele

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type vault
    ```

* Geçici olarak silinen anahtar kasasını kurtar

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Geçici olarak silinen anahtar kasasını Temizle **(uyarı! Bu Işlem, anahtar KASANıZı kalıcı olarak sıler)**

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Anahtar Kasası üzerinde Temizleme korumasını etkinleştir

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-purge-protection true
    ```

## <a name="certificates-cli"></a>Sertifikalar (CLı)

* Sertifikaları temizlemeye ve kurtarmaya yönelik erişim izni verme

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --certificate-permissions recover purge
    ```

* Sertifikayı Sil

    ```azurecli
    az keyvault certificate delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Silinen sertifikaları listeleme

    ```azurecli
    az keyvault certificate list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Silinen sertifikayı kurtar

    ```azurecli
    az keyvault certificate recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Geçici olarak silinen sertifikayı Temizle **(uyarı! Bu Işlem, SERTIFIKANıZı kalıcı olarak sıler)**

    ```azurecli
    az keyvault certificate purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

## <a name="keys-cli"></a>Anahtarlar (CLı)

* Temizleme ve Kurtarma anahtarlarına erişim izni verme

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --key-permissions recover purge
    ```

* Delete tuşu

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Silinen anahtarları Listele

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Silinen anahtarı kurtar

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Geçici olarak silinen anahtarı Temizle **(uyarı! Bu Işlem, ANAHTARıNıZı kalıcı olarak SILECEK**

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

## <a name="secrets-cli"></a>Gizlilikler (CLı)

* Gizli dizileri temizlemek ve kurtarmak için erişim izni verme

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --secret-permissions recover purge
    ```

* Gizli dizi Sil

    ```azurecli
    az keyvault secret delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Silinen gizli dizileri Listele

    ```azurecli
    az keyvault secret list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Silinen gizli anahtarı kurtar

    ```azurecli
    az keyvault secret recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Geçici silinen gizli anahtarı Temizle **(uyarı! Bu Işlem, gızlı DIZINIZI kalıcı olarak SILECEK**

    ```azurecli
    az keyvault secret purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

## <a name="key-vault-powershell"></a>Key Vault (PowerShell)

* Anahtar kasasının geçici silme özelliğinin etkin olup olmadığını doğrulama

    ```powershell
    Get-AzKeyVault -VaultName "ContosoVault"
    ```

* Anahtar kasasını Sil

    ```powershell
    Remove-AzKeyVault -VaultName 'ContosoVault'
    ```

* Tüm yazılımla Silinen anahtar kasalarını Listele

    ```powershell
    Get-AzKeyVault -InRemovedState
    ```

* Geçici olarak silinen anahtar kasasını kurtar

    ```powershell
    Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
    ```

* Geçici olarak silinen anahtar kasasını Temizle **(uyarı! Bu Işlem, anahtar KASANıZı kalıcı olarak sıler)**

    ```powershell
    Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
    ```

* Anahtar Kasası üzerinde Temizleme korumasını etkinleştir

    ```powershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="certificates-powershell"></a>Sertifikalar (PowerShell)

* Sertifikaları kurtarma ve Temizleme izinleri verme

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToCertificates recover,purge
    ```

* Sertifikayı silme

  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

* Bir anahtar kasasındaki tüm silinen sertifikaları listeleme

  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

* Silinen durumdaki bir sertifikayı kurtarma

  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

* Geçici olarak silinen bir sertifikayı Temizleme **(uyarı! Bu Işlem, SERTIFIKANıZı kalıcı olarak sıler)**

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState
  ```

## <a name="keys-powershell"></a>Anahtarlar (PowerShell)

* Anahtarları kurtarma ve Temizleme izinleri verme

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys recover,purge
    ```

* Bir anahtarı silme

  ```powershell
  Remove-AzKeyVaultKey -VaultName ContosoVault -Name 'MyKey'
  ```

* Bir anahtar kasasındaki tüm silinen sertifikaları listeleme

  ```powershell
  Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
  ```

* Geçici olarak silinen bir anahtarı kurtarmak için

    ```powershell
    Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
    ```

* Geçici olarak silinen bir anahtarı Temizle **(uyarı! Bu Işlem, ANAHTARıNıZı kalıcı olarak SILECEK**

    ```powershell
    Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
    ```

## <a name="secrets-powershell"></a>Gizlilikler (PowerShell)

* Gizli dizileri kurtarmak ve temizlemek için izin verme

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToSecrets recover,purge
    ```

* SQLPassword adlı bir gizli dizi silme

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

* Bir anahtar kasasında silinen tüm gizli dizileri listeleme

  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

* Silinen durumda gizli dizi kurtarma

  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

* Silinen durumda gizli dizi Temizleme **(uyarı! Bu Işlem, ANAHTARıNıZı kalıcı olarak SILECEK**

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```
---

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Key Vault PowerShell cmdlet 'leri](/powershell/module/az.keyvault)
- [Azure CLı komutlarını Key Vault](/cli/azure/keyvault)
- [Azure Key Vault yedekleme](backup.md)
- [Key Vault günlüğü etkinleştirme](howto-logging.md)
- [Azure Key Vault güvenlik özellikleri](security-features.md)
- [Geliştirici Kılavuzu Azure Key Vault](developers-guide.md)