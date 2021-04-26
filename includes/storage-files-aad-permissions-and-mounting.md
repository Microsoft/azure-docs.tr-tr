---
title: include dosyası
description: include dosyası
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/26/2020
ms.author: rogara
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 200bf290543747cf9abab6113b8013e2eec852a8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107513611"
---
## <a name="assign-access-permissions-to-an-identity"></a>Kimliğe erişim izinleri atama

Kimlik tabanlı kimlik doğrulaması ile Azure dosyaları kaynaklarına erişmek için bir kimlik (Kullanıcı, Grup veya hizmet sorumlusu), paylaşma düzeyinde gerekli izinlere sahip olmalıdır. Bu işlem, belirli bir kullanıcının dosya paylaşımında sahip olduğu erişim türünü belirttiğiniz Windows paylaşma izinlerinin belirtilmesine benzer. Bu bölümdeki kılavuzda, bir kimliğe bir dosya paylaşımının okuma, yazma veya silme izinlerinin nasıl atanacağı gösterilmektedir. 

Kullanıcılara paylaşma düzeyi izinleri vermek için üç Azure yerleşik rolü ekledik:

- **Depolama dosyası VERI SMB paylaşımı okuyucusu** , SMB üzerinden Azure depolama dosya paylaşımlarında okuma erişimine izin verir.
- **Depolama dosyası VERI SMB paylaşımı katılımcısı** , SMB üzerinden Azure depolama dosya paylaşımlarında okuma, yazma ve silme erişimine izin verir.
- **Depolama dosyası VERI SMB paylaşımı yükseltilmiş katkıda bulunanlar** , SMB üzerinden Azure depolama dosya paylaşımlarında okuma, yazma, silme ve değiştirme izinleri sağlar.

> [!IMPORTANT]
> Bir dosyanın sahipliğini alma özelliği de dahil olmak üzere bir dosya paylaşımının tam yönetim denetimi, depolama hesabı anahtarının kullanılmasını gerektirir. Yönetim denetimi, Azure AD kimlik bilgileriyle desteklenmez.

Azure portal, PowerShell veya Azure CLı kullanarak, yerleşik rolleri, bir kullanıcının Azure AD kimliğine, paylaşma düzeyi izinleri vermek için atayabilirsiniz. Paylaşılan düzeyi Azure rolü atamasının etkili bir zaman alabilir olduğunu unutmayın. 

> [!NOTE]
> Şirket içi AD DS kimlik doğrulaması için kullanmayı planlıyorsanız [AD DS kimlik bilgilerinizi Azure AD 'ye eşitlemeyi](../articles/active-directory/hybrid/how-to-connect-install-roadmap.md) unutmayın. AD DS 'den Azure AD 'ye Parola karması eşitlemesi isteğe bağlıdır. Paylaşma düzeyi izni, şirket içi AD DS eşitlenmiş Azure AD kimliğine verilecek.

Genel öneri, bir Kullanıcı ve kimlik grubunu temsil eden bir AD grubuna yüksek düzeyde erişim yönetimi için paylaşma düzeyi iznini kullanmak ve ardından dizin/dosya düzeyinde ayrıntılı erişim denetimi için NTFS izinlerinden faydalanır. 

### <a name="assign-an-azure-role-to-an-ad-identity"></a>Bir AD kimliğine Azure rolü atama

# <a name="portal"></a>[Portal](#tab/azure-portal)
Azure AD kimliğine [Azure Portal](https://portal.azure.com)kullanarak bir Azure rolü atamak için aşağıdaki adımları izleyin:

1. Azure portal dosya paylaşımınıza gidin veya [bir dosya paylaşma oluşturun](../articles/storage/files/storage-how-to-create-file-share.md).
2. **Access Control (IAM)** seçeneğini belirleyin.
3. **Rol ataması Ekle** ' yi seçin
4. **Rol ataması Ekle** dikey penceresinde, **rol** listesinden uygun yerleşik rolü (depolama dosyası veri SMB paylaşma okuyucusu, depolama dosya veri SMB paylaşma katılımcısı) seçin. Varsayılan ayarda **erişime ata erişimi** bırakın: **Azure AD kullanıcısı, Grup veya hizmet sorumlusu**. Hedef Azure AD kimliğini ada veya e-posta adresine göre seçin.
5. Rol atama işlemini gerçekleştirmek için **Kaydet** ' i seçin.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aşağıdaki PowerShell örneği, oturum açma adına göre bir Azure rolünün Azure AD kimliğine nasıl atanacağını göstermektedir. PowerShell ile Azure rolleri atama hakkında daha fazla bilgi için bkz. [RBAC ve Azure PowerShell kullanarak erişimi yönetme](../articles/role-based-access-control/role-assignments-powershell.md).

Aşağıdaki örnek betiği çalıştırmadan önce, parantez dahil yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
  
Aşağıdaki CLı 2,0 komutunda, oturum açma adına göre bir Azure rolünün Azure AD kimliğine nasıl atanacağı gösterilmektedir. Azure CLı ile Azure rolleri atama hakkında daha fazla bilgi için bkz. [RBAC ve Azure CLI kullanarak erişimi yönetme](../articles/role-based-access-control/role-assignments-cli.md). 

Aşağıdaki örnek betiği çalıştırmadan önce, parantez dahil yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```
---

## <a name="configure-ntfs-permissions-over-smb"></a>SMB üzerinde NTFS izinlerini yapılandırma

RBAC ile paylaşma düzeyi izinleri atadıktan sonra, kök, dizin veya dosya düzeyinde uygun NTFS izinleri atamanız gerekir. Bir kullanıcının paylaşıma erişip erişemeyeceğini belirleyen üst düzey ağ geçidi olarak paylaşma düzeyi izinleri düşünün. NTFS izinleri, kullanıcının dizin veya dosya düzeyinde hangi işlemleri yapabileceğini belirlemek için daha ayrıntılı bir düzeyde davranır.

Azure dosyaları, tüm NTFS temel ve gelişmiş izinleri kümesini destekler. Windows Dosya Gezgini 'ni kullanarak veya Windows [ıacl](/windows-server/administration/windows-commands/icacls) veya [set-ACL](/powershell/module/microsoft.powershell.security/set-acl) komutunu çalıştırarak bir Azure dosya PAYLAŞıMıNDAKI dizin ve dosyalarda NTFS izinlerini görüntüleyebilir ve yapılandırabilirsiniz. 

NTFS 'yi üst Kullanıcı izinleriyle yapılandırmak için, etki alanına katılmış VM 'nizden depolama hesabı anahtarınızı kullanarak paylaşıma bağlamanız gerekir. Bir sonraki bölümdeki yönergeleri izleyerek bir Azure dosya paylaşımından komut isteminden bağlama yapın ve NTFS izinlerini uygun şekilde yapılandırın.

Aşağıdaki izin kümeleri bir dosya paylaşımının kök dizininde desteklenir:

- BUILTIN\Administrators: (OI) (CI) (F)
- NT AUTHORıTY\SYSTEM: (OI) (Cı) (F)
- BUILTIN\Users: (RX)
- BUILTIN\Users: (OI) (CI) (GÇ) (GR, GE)
- NT Authorıty\authenticated users: (OI) (CI) (ı)
- NT AUTHORıTY\SYSTEM: (F)
- OLUŞTURAN SAHIBI: (OI) (Cı) (GÇ) (F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Komut isteminden bir dosya paylaşma bağlama

Azure dosya paylaşımından bağlamak için Windows **net use** komutunu kullanın. Aşağıdaki örnek içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın. Dosya paylaşımları bağlama hakkında daha fazla bilgi için bkz. [Windows Ile Azure dosya paylaşımı kullanma](../articles/storage/files/storage-how-to-use-files-windows.md). 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Azure dosyalarına bağlanırken sorunlarla karşılaşırsanız, lütfen [Windows 'Ta Azure dosyaları bağlama hataları için yayımladığımız sorun giderme aracına](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/)bakın. Ayrıca, 445 numaralı bağlantı noktası engellendiğinde senaryolar etrafında çalışmak için [rehberlik](../articles/storage/files/storage-files-faq.md#on-premises-access) sunuyoruz. 


### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Windows Dosya Gezgini ile NTFS izinlerini yapılandırma

Kök dizin dahil olmak üzere dosya paylaşımındaki tüm dizinlere ve dosyalara tam izin vermek için Windows Dosya Gezgini 'ni kullanın.

1. Windows Dosya Gezgini 'ni açın ve dosya/dizine sağ tıklayıp **Özellikler**' i seçin.
2. **Güvenlik** sekmesini seçin.
3. Düzenle 'yi seçin **.** izinleri değiştirmek için.
4. Mevcut kullanıcıların izinlerini değiştirebilir veya yeni kullanıcılara izin vermek için **Ekle..** . seçeneğini belirleyebilirsiniz.
5. Yeni Kullanıcı ekleme istemi penceresinde, izin vermek istediğiniz hedef Kullanıcı adını **Seçilecek nesne adlarını girin** kutusuna girin ve hedef kullanıcının tam UPN adını bulmak Için **adları denetle** ' yi seçin.
7.    **Tamam**’ı seçin.
8.    **Güvenlik** sekmesinde, yeni kullanıcıya vermek istediğiniz tüm izinleri seçin.
9.    **Uygula**’yı seçin.

### <a name="configure-ntfs-permissions-with-icacls"></a>Iacl 'lerle NTFS izinlerini yapılandırma

Kök dizin dahil olmak üzere, dosya paylaşımındaki tüm dizin ve dosyalara tam izinler vermek için aşağıdaki Windows komutunu kullanın. Örnekteki yer tutucu değerlerini kendi değerlerinizle değiştirdiğinizden hatırlayın.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

NTFS izinlerini ve desteklenen farklı türlerdeki izinleri ayarlamak için ıacl 'leri kullanma hakkında daha fazla bilgi için bkz. [ıacl 'ler için komut satırı başvurusu](/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Etki alanına katılmış bir VM 'den dosya paylaşma bağlama

Aşağıdaki işlem, dosya paylaşımınızın ve erişim izninizin doğru ayarlandığını ve bir Azure dosya paylaşımının etki alanına katılmış bir VM 'den erişebileceğini doğrular. Paylaşılan düzeyi Azure rolü atamasının etkili bir zaman alabilir olduğunu unutmayın. 

Aşağıdaki görüntüde gösterildiği gibi, izin verdiğiniz Azure AD kimliğini kullanarak VM 'de oturum açın. Azure dosyaları için şirket içi AD DS kimlik doğrulamasını etkinleştirdiyseniz, AD DS kimlik bilgilerinizi kullanın. Azure AD DS kimlik doğrulaması için Azure AD kimlik bilgileriyle oturum açın.

![Kullanıcı kimlik doğrulaması için Azure AD oturum açma ekranını gösteren ekran görüntüsü](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Azure dosya paylaşımından bağlama için aşağıdaki komutu kullanın. Yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın. Kimliğiniz doğrulandıktan sonra depolama hesabı anahtarını, şirket içi AD DS kimlik bilgilerini veya Azure AD DS kimlik bilgilerini sağlamanız gerekmez. Çoklu oturum açma deneyimi, şirket içi AD DS veya Azure AD DS kimlik doğrulaması için desteklenir. AD DS kimlik bilgileriyle bağlama sorunları yaşıyorsanız, kılavuz için bkz. [Windows 'Da Azure dosyaları sorunlarını giderme](../articles/storage/files/storage-troubleshoot-windows-file-connection-problems.md) .

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}
```
