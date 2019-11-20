---
title: Azure Key Vault-PowerShell ile geçici silme nasıl kullanılır
description: PowerShell kod alıntılarını kullanarak geçici silme örneği örnekleri
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 6a24f2dd52c3ac3c51df54bf5c01c7b31ca16147
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985764"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>PowerShell ile Key Vault geçici silme kullanma

Azure Key Vault geçici silme özelliği, silinen kasaların ve kasa nesnelerinin kurtarılmasına olanak tanır. Özellikle, geçici silme aşağıdaki senaryolara yöneliktir:

- Bir anahtar kasasının kurtarılabilir silme desteği
- Anahtar Kasası nesnelerinin kurtarılabilir silme desteği; Anahtarlar, gizlilikler ve, sertifikalar

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- Azure PowerShell 1.0.0 veya üzeri: Bu ayarı zaten yüklemediyseniz, Azure PowerShell yükleyip Azure aboneliğinizle ilişkilendiremezseniz bkz. [Azure PowerShell yükleme ve yapılandırma](https://docs.microsoft.com/powershell/azure/overview). 

>[!NOTE]
> Doğru sürüm yerine ortamınıza yüklenebilecek Key Vault PowerShell çıkış biçimlendirme dosyasının güncel olmayan bir sürümü var. PowerShell 'in güncelleştirilmiş bir sürümünü, çıkış biçimlendirmesi için gerekli düzeltmeyi içerecek şekilde benimsemeyi bekleme ve bu konuyu bu sırada güncellenecektir. Şu anki geçici çözüm, bu biçimlendirme sorunuyla karşılaşmanız gerekir:
> - Bu konuda açıklanan geçici silme etkin özelliğini gördüğünüzü fark ederseniz aşağıdaki sorguyu kullanın: `$vault = Get-AzKeyVault -VaultName myvault; $vault.EnableSoftDelete`.


PowerShell için Key Vault belirli başvuru bilgileri için bkz. [Azure Key Vault PowerShell Başvurusu](/powershell/module/az.keyvault).

## <a name="required-permissions"></a>Gerekli izinler

Key Vault işlemler, rol tabanlı erişim denetimi (RBAC) izinleri aracılığıyla aşağıdaki şekilde ayrı yönetilir:

| Çalışma | Açıklama | Kullanıcı izni |
|:--|:--|:--|
|List|Silinen anahtar kasalarını listeler.|Microsoft. Keykasası/Silinleults/okuma|
|Kurtar|Silinen bir anahtar kasasını geri yükler.|Microsoft. Keykasası/Vaults/yazma|
|Temizle|Silinen bir anahtar kasasını ve tüm içeriğini kalıcı olarak kaldırır.|Microsoft. Keykasası/konumlar/Silinkaults/Temizleme/eylem|

İzinler ve erişim denetimi hakkında daha fazla bilgi için bkz. [anahtar kasanızın güvenliğini sağlama](key-vault-secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Geçici silme etkinleştiriliyor

Silinen bir anahtar kasasının kurtarılmasına veya bir anahtar kasasında depolanan nesnelere izin vermek için "geçici silme" özelliğini etkinleştirirsiniz.

> [!IMPORTANT]
> Anahtar kasasında ' geçici silme ' özelliğinin etkinleştirilmesi geri alınamaz bir eylemdir. Soft-Delete özelliği "true" olarak ayarlandıktan sonra değiştirilemez veya kaldırılamaz.  

### <a name="existing-key-vault"></a>Mevcut Anahtar Kasası

Contosokasası adlı mevcut bir Anahtar Kasası için aşağıdaki gibi geçici silme özelliğini etkinleştirin. 

```powershell
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Yeni Anahtar Kasası

Yeni bir Anahtar Kasası için geçici silme özelliğinin etkinleştirilmesi, oluşturma Komutunuz için geçici silme etkin bayrağını ekleyerek oluşturma sırasında yapılır.

```powershell
New-AzKeyVault -Name "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>Geçici silme etkinleştirmeyi doğrula

Bir anahtar kasasının geçici silme özelliğinin etkin olduğunu doğrulamak için *göster* komutunu çalıştırın ve ' geçici silme etkin? ' öğesini arayın. özniteliğe

```powershell
Get-AzKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Geçici silme korumalı anahtar kasasını silme

Yazılım, geçici silme özelliğinin etkin olup olmadığına bağlı olarak davranışındaki bir Anahtar Kasası değişikliğini silme komutu.

> [!IMPORTANT]
>Geçici silme etkin olmayan bir Anahtar Kasası için aşağıdaki komutu çalıştırırsanız, bu anahtar kasasını ve tüm içeriğini kurtarma seçeneği olmadan kalıcı olarak silersiniz!

```powershell
Remove-AzKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Yumuşak silme, anahtar kasalarınızı korur

Geçici silme etkin:

- Silinen bir Anahtar Kasası, kaynak grubundan kaldırılır ve oluşturulduğu konumla ilişkili ayrılmış bir ad alanına yerleştirilir. 
- Anahtarlar, gizli diziler ve sertifikalar gibi silinen nesneler, kapsayan Anahtar Kasası silinen durumunda olduğu sürece erişilemez olur. 
- Silinen bir Anahtar Kasası için DNS adı ayrılmıştır ve aynı ada sahip yeni bir anahtar kasasının oluşturulmasını önler.  

Aşağıdaki komutu kullanarak, aboneliğinizle ilişkili silinmiş durum anahtarı kasalarını görüntüleyebilirsiniz:

```powershell
Get-AzKeyVault -InRemovedState 
```

- *Kimliği* , kurtarılırken veya temizlenirken kaynağı tanımlamak için kullanılabilir. 
- *Kaynak kimliği* , bu kasanın ORIJINAL kaynak kimliğidir. Bu Anahtar Kasası artık silinmiş durumda olduğundan, bu kaynak KIMLIĞINE sahip bir kaynak yok. 
- *Zamanlanan Temizleme tarihi* , hiçbir işlem yapılmaz kasanın kalıcı olarak silinme tarihidir. *Zamanlanan Temizleme tarihini*hesaplamak için kullanılan varsayılan saklama süresi 90 gündür.

## <a name="recovering-a-key-vault"></a>Anahtar kasasını kurtarma

Bir anahtar kasasını kurtarmak için Anahtar Kasası adı, kaynak grubu ve konum belirtirsiniz. Kurtarma işlemi için ihtiyaç duyduğunuz şekilde, silinen anahtar kasasının konumunu ve kaynak grubunu aklınızda bulabilirsiniz.

```powershell
Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

Bir Anahtar Kasası kurtarılırken, anahtar kasasının orijinal kaynak KIMLIĞIYLE yeni bir kaynak oluşturulur. Özgün kaynak grubu kaldırılırsa, kurtarma denenmeye başlamadan önce bir ad aynı adla oluşturulmalıdır.

## <a name="deleting-and-purging-key-vault-objects"></a>Anahtar Kasası nesnelerini silme ve Temizleme

Aşağıdaki komut, "Contosokasası" adlı bir anahtar kasasında (geçici silme özelliği etkin olan) ' ContosoFirstKey ' anahtarını silecek:

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

Anahtar kasanızın geçici silme için etkinleştirilmiş olması halinde, silinen anahtarları açıkça listelemediğiniz takdirde silinen bir anahtar hala silinir. Silinen bir anahtarın listelenmesi, kurtarılması ve temizlenmesi dışında, silinen durumdaki bir anahtar üzerindeki çoğu işlem başarısız olur. 

Örneğin, aşağıdaki komut ' Contosokasasında ' anahtar kasasındaki silinen anahtarları listeler:

```powershell
Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Geçiş durumu 

Geçici silme etkinken anahtar kasasındaki bir anahtarı sildiğinizde, geçişin tamamlanması birkaç saniye sürebilir. Bu geçiş sırasında, anahtarın etkin durumda veya silinmiş durumda olmaması görünebilir. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Anahtar Kasası nesneleriyle geçici silme kullanma

Anahtar kasaları, silinen bir anahtar, gizli dizi ya da sertifika gibi, kurtarmadığınız veya temizolmadığınız sürece 90 güne kadar, silinen durumda kalır. 

#### <a name="keys"></a>Anahtarlar

Geçici olarak silinen bir anahtarı kurtarmak için:

```powershell
Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

Kalıcı olarak silinen bir anahtarı (Temizleme olarak da bilinir) kalıcı olarak silmek için:

> [!IMPORTANT]
> Bir anahtarı temizlemek onu kalıcı olarak siler ve kurtarılamaz! 

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

**Kurtarma** ve **Temizleme** eylemlerinin bir Anahtar Kasası erişim ilkesiyle ilişkili kendi izinleri vardır. Bir kullanıcı veya hizmet sorumlusunun bir **Kurtarma** veya **Temizleme** eylemi yürütebilmesi için bu anahtar veya gizli dizi için ilgili izinlere sahip olmaları gerekir. Varsayılan olarak, tüm izinleri vermek için ' tümü ' kısayolu kullanıldığında, bir anahtar kasasının erişim ilkesine **Temizleme** eklenmez. Özellikle **Temizleme** izni vermelisiniz. 

#### <a name="set-a-key-vault-access-policy"></a>Anahtar Kasası erişim ilkesi ayarlama

Aşağıdaki komut, user@contoso.com *contosokasasındaki* anahtarlar üzerinde **Temizleme**dahil birkaç işlemi kullanma izni verir:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Yalnızca geçici silme özelliği etkinleştirilmiş olan bir anahtar kasanıza sahipseniz, **Kurtarma** ve **Temizleme** izinleriniz olmayabilir.

#### <a name="secrets"></a>Gizli diziler

Anahtarlar gibi gizli dizileri kendi komutlarıyla yönetilir:

- SQLPassword adlı bir gizli dizi silin: 
  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

- Bir anahtar kasasındaki tüm silinen gizli dizileri listeleyin: 
  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

- Silinen durumda gizli dizi kurtarma: 
  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

- Silinen durumda gizli dizi temizle: 

  > [!IMPORTANT]
  > Gizli dizi Temizleme bunu kalıcı olarak siler ve kurtarılamaz!

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```

## <a name="purging-a-soft-delete-protected-key-vault"></a>Geçici silme korumalı anahtar kasasını Temizleme

> [!IMPORTANT]
> Bir anahtar kasasını veya kapsanan nesnelerinden birini temizlemek, kalıcı olarak silinecek, aksi, kurtarılabilir olmayacaktır!

Temizleme işlevi, daha önce geçici olarak silinmiş olan bir Anahtar Kasası nesnesini veya tüm anahtar kasasını kalıcı olarak silmek için kullanılır. Önceki bölümde gösterildiği gibi, yumuşak silme özelliği etkinken bir anahtar kasasında depolanan nesneler birden fazla duruma geçebilir:
- **Etkin**: silinmeden önce.
- **Geçici**silme: silinmeden sonra, listelenmiş ve etkin duruma geri kurtarılabiliyor.
- **Kalıcı olarak silindi**: temizleme sonrasında kurtarılamaz, kurtarılamaz.


Aynı değer, Anahtar Kasası için de geçerlidir. Geçici olarak silinen bir anahtar kasasını ve içeriğini kalıcı olarak silmek için anahtar kasasının kendisini temizlemeniz gerekir.

### <a name="purging-a-key-vault"></a>Anahtar kasasını Temizleme

Bir Anahtar Kasası temizlendiğinde, anahtarlar, gizlilikler ve Sertifikalar dahil olmak üzere tüm içerikleri kalıcı olarak silinir. Geçici olarak silinen bir anahtar kasasını temizlemek için, seçeneğini `Remove-AzKeyVault` seçeneğiyle `-InRemovedState` ve `-Location location` Silinen anahtar kasasının konumunu bağımsız değişkenle belirterek komutunu kullanın. Silinen bir kasanın konumunu komutunu `Get-AzKeyVault -InRemovedState`kullanarak bulabilirsiniz.

```powershell
Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

### <a name="purge-permissions-required"></a>Temizleme izinleri gerekli
- Silinen bir anahtar kasasını temizlemek için, kullanıcının *Microsoft. Keykasası/konumlar/deletedVaults/Temizleme/eylem* IŞLEMI için RBAC iznine sahip olması gerekir. 
- Silinen bir anahtar kasasını listelemek için, kullanıcının *Microsoft. Keykasası/deletedVaults/Read* IŞLEMI için RBAC iznine sahip olması gerekir. 
- Varsayılan olarak, yalnızca bir abonelik Yöneticisi bu izinlere sahiptir. 

### <a name="scheduled-purge"></a>Zamanlanmış temizleme

Silinen Anahtar Kasası nesnelerinin listelenmesi Ayrıca Key Vault tarafından temizlenmek üzere zamanlandığında de gösterilir. *Zamanlanan Temizleme tarihi* , hiçbir işlem yapılmaz bir Anahtar Kasası nesnesinin kalıcı olarak silineceğini gösterir. Varsayılan olarak, silinen bir Anahtar Kasası nesnesi için bekletme süresi 90 gündür.

>[!IMPORTANT]
>*Zamanlanan Temizleme tarihi* alanı tarafından tetiklenen temizlenen bir kasa nesnesi kalıcı olarak silinir. Kurtarılamaz!

## <a name="enabling-purge-protection"></a>Temizleme korumasını etkinleştirme

Temizleme koruması açık olduğunda, 90 günlük bekletme süresi geçene kadar bir kasa veya silinmiş durumdaki bir nesne kaldırılamaz. Bu kasa veya nesne yine de kurtarılabilir. Bu özellik, bir kasasının veya bir nesnenin Bekletme dönemi geçene kadar hiçbir zaman kalıcı olarak silinebileceğini güvence altına almanızı sağlar.

Temizleme korumasını yalnızca geçici silme de etkinse etkinleştirebilirsiniz. 

Kasa oluştururken hem geçici silme hem de Temizleme korumasını açmak için [New-Azkeykasa](/powershell/module/az.keyvault/new-azkeyvault?view=azps-1.5.0) cmdlet 'ini kullanın:

```powershell
New-AzKeyVault -Name ContosoVault -ResourceGroupName ContosoRG -Location westus -EnableSoftDelete -EnablePurgeProtection
```

Var olan bir kasaya Temizleme koruması eklemek için (zaten geçici silme etkindir), [Get-Azkeykasası](/powershell/module/az.keyvault/Get-AzKeyVault?view=azps-1.5.0), [Get-Azresource](/powershell/module/az.resources/get-azresource?view=azps-1.5.0)ve [set-azresource](/powershell/module/az.resources/set-azresource?view=azps-1.5.0) cmdlet 'lerini kullanın:

```
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

## <a name="other-resources"></a>Diğer kaynaklar

- Key Vault geçici silme özelliğine genel bakış için bkz. [Azure Key Vault geçici silme genel bakış](key-vault-ovw-soft-delete.md).
- Azure Key Vault kullanımı için genel bir bakış için bkz. [Azure Key Vault nedir?](key-vault-overview.md). ate = başarılı}
