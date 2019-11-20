---
title: Azure dosyaları için SMB üzerinden Azure Active Directory kimlik doğrulamasını etkinleştirme-Azure depolama
description: Azure Active Directory Domain Services aracılığıyla Azure dosyaları için sunucu Ileti bloğu (SMB) üzerinden kimlik tabanlı kimlik doğrulamasını nasıl etkinleştireceğinizi öğrenin. Etki alanına katılmış Windows sanal makineleriniz (VM), Azure AD kimlik bilgilerini kullanarak Azure dosya paylaşımlarına erişebilir.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: rogarana
ms.openlocfilehash: 886cacc5e90136380a183f6b9ddd1123d726dcf3
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129221"
---
# <a name="enable-azure-active-directory-domain-services-authentication-over-smb-for-azure-files"></a>Azure dosyaları için SMB üzerinden Azure Active Directory Domain Services kimlik doğrulamasını etkinleştirme
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Azure dosyaları için SMB üzerinden Azure AD kimlik doğrulamasına genel bakış için bkz. [Azure dosyaları IÇIN SMB üzerinden Azure Active Directory kimlik doğrulamasına genel bakış](storage-files-active-directory-overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>İş akışına genel bakış
Azure dosyaları için SMB üzerinden Azure AD DS kimlik doğrulamasını etkinleştirmeden önce Azure AD ve Azure depolama ortamlarınızın doğru şekilde yapılandırıldığını doğrulayın. Gerekli tüm adımları tamamladığınızdan emin olmak için [önkoşulları](#prerequisites) gözden etmenizi öneririz.

Ardından, aşağıdaki adımları izleyerek Azure AD kimlik bilgileriyle Azure dosya kaynaklarına erişim izni verin: 

1. Depolama hesabınızı ilişkili Azure AD DS dağıtımına kaydetmek için depolama hesabınız için SMB üzerinden Azure AD DS kimlik doğrulamasını etkinleştirin.
2. Bir Azure AD kimliğine (bir Kullanıcı, Grup veya hizmet sorumlusu) bir paylaşıma yönelik erişim izinleri atayın.
3. Dizinler ve dosyalar için SMB üzerinde NTFS izinleri yapılandırın.
4. Etki alanına katılmış bir VM 'den Azure dosya paylaşımının bağlama.

Aşağıdaki diyagramda Azure dosyaları için SMB üzerinden Azure AD DS kimlik doğrulamasını etkinleştirmeye yönelik uçtan uca iş akışı gösterilmektedir. 

![Azure dosyaları için SMB üzerinden Azure AD iş akışı gösteren diyagram](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Önkoşullar

Azure dosyaları için SMB üzerinden Azure AD 'yi etkinleştirmeden önce, aşağıdaki önkoşulları tamamladığınızdan emin olun:

1.  **Bir Azure AD kiracısı seçin veya oluşturun.**

    SMB üzerinden Azure AD kimlik doğrulaması için yeni veya mevcut bir kiracı kullanabilirsiniz. Erişmek istediğiniz kiracı ve dosya paylaşımının aynı abonelikle ilişkilendirilmesi gerekir.

    Yeni bir Azure AD kiracısı oluşturmak için [bir Azure AD kiracısı ve bir Azure AD aboneliği ekleyebilirsiniz](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Mevcut bir Azure AD kiracınız varsa ancak Azure dosyaları ile kullanmak üzere yeni bir kiracı oluşturmak istiyorsanız, bkz. [Azure Active Directory kiracı oluşturma](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

2.  **Azure AD kiracısında Azure AD Domain Services etkinleştirin.**

    Azure AD kimlik bilgileriyle kimlik doğrulamasını desteklemek için, Azure AD kiracınız için Azure AD Domain Services etkinleştirmeniz gerekir. Azure AD kiracısı yöneticisi değilseniz, yöneticiye başvurun ve [Azure Portal kullanarak Azure Active Directory Domain Services etkinleştirmek](../../active-directory-domain-services/tutorial-create-instance.md)için adım adım yönergeleri izleyin.

    Azure AD DS dağıtımının tamamlanabilmesi için genellikle yaklaşık 15 dakika sürer. Bir sonraki adıma geçmeden önce, Azure AD DS sistem durumunun **çalıştığını**, Parola karması eşitlemesi etkinken çalıştığını doğrulayın.

3.  **Etki alanı-Azure AD DS ile bir Azure VM 'ye katın.**

    Bir VM 'den Azure AD kimlik bilgilerini kullanarak bir dosya paylaşımıyla erişmek için, sanal makinenizin Azure AD DS etki alanına katılmış olması gerekir. VM 'nin etki alanına katılması hakkında daha fazla bilgi için, bkz. [Windows Server sanal makinesini yönetilen bir etki alanına katma](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > Azure dosyaları ile SMB üzerinden Azure AD DS kimlik doğrulaması, yalnızca Windows 7 veya Windows Server 2008 R2 üzerindeki işletim sistemi sürümlerinde çalışan Azure VM 'lerinde desteklenir.

4.  **Bir Azure dosya paylaşımından seçim yapın veya oluşturun.**

    Azure AD kiracınızla aynı abonelikle ilişkili yeni veya var olan bir dosya paylaşma seçin. Yeni bir dosya paylaşma oluşturma hakkında daha fazla bilgi için bkz. [Azure dosyalarında dosya paylaşma oluşturma](storage-how-to-create-file-share.md). 
    En iyi performans için, dosya paylaşımınızın paylaşıma erişmeyi planladığınız VM ile aynı bölgede olmasını öneririz.

5.  **Azure dosya paylaşımlarını depolama hesabı anahtarınızı kullanarak bağlayarak Azure dosyaları bağlantısını doğrulayın.**

    VM ve dosya paylaşımınızın düzgün yapılandırıldığını doğrulamak için, depolama hesabı anahtarınızı kullanarak dosya paylaşımının bağlanmasını deneyin. Daha fazla bilgi için bkz. [Azure dosya paylaşma bağlama ve Windows 'da paylaşıma erişme](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Hesabınız için Azure AD DS kimlik doğrulamasını etkinleştirin

Azure dosyaları için SMB üzerinden Azure AD DS kimlik doğrulamasını etkinleştirmek için, Azure portal, Azure PowerShell veya Azure CLı kullanarak 24 Eylül 2018 ' den sonra oluşturulan depolama hesaplarında bir özelliği ayarlayabilirsiniz. Bu özelliği ayarlamak, depolama hesabını ilişkili Azure AD DS dağıtımına kaydeder. SMB üzerinden Azure AD DS kimlik doğrulaması, depolama hesabındaki tüm yeni ve var olan dosya paylaşımları için etkinleştirilir.

Azure AD DS kimlik doğrulamasını yalnızca Azure AD DS Azure AD kiracınıza başarıyla dağıttıktan sonra SMB üzerinden etkinleştirebileceğinizi aklınızda bulundurun. Daha fazla bilgi için bkz. [Önkoşullar](#prerequisites).

### <a name="azure-portal"></a>Azure portal

[Azure Portal](https://portal.azure.com)SMB üzerinden Azure AD DS kimlik doğrulamasını etkinleştirmek için şu adımları izleyin:

1. Azure portal, mevcut depolama hesabınıza gidin veya [bir depolama hesabı oluşturun](../common/storage-quickstart-create-account.md).
2. **Ayarlar** bölümünde **yapılandırma**' yı seçin.
3. **Azure dosya kimlik doğrulaması Için kimlik tabanlı dizin hizmetinden** **Azure Active Directory Domain Services (Azure AD DS)** öğesini seçin.

Aşağıdaki görüntüde, depolama hesabınız için SMB üzerinden Azure AD DS kimlik doğrulamasının nasıl etkinleştirileceği gösterilmektedir.

![Azure portal SMB üzerinden Azure AD kimlik doğrulamasını etkinleştirme](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)
  
### <a name="powershell"></a>PowerShell  

Azure PowerShell SMB üzerinden Azure AD DS kimlik doğrulamasını etkinleştirmek için en son az modülünü (2,4 veya daha yeni) veya az. Storage modülünü (1,5 veya daha yeni bir sürüm). PowerShell 'i yükleme hakkında daha fazla bilgi için bkz. [PowerShellGet Ile Windows 'da Azure PowerShell yükleme](https://docs.microsoft.com/powershell/azure/install-Az-ps).

Yeni bir depolama hesabı oluşturmak için [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0)' ı çağırın ve sonra **Enableazureactivedirectorydomainservicesforfile** parametresini **true**olarak ayarlayın. Aşağıdaki örnekte, yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın. (Önceki önizleme modülünü kullanıyorsanız, özellik etkinleştirme parametresi **Enableazurefilesaadıntegrationforsmb**' dir.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

Mevcut depolama hesaplarında bu özelliği etkinleştirmek için aşağıdaki komutu kullanın:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Azure CLI

Azure CLı ile SMB üzerinden Azure AD kimlik doğrulamasını etkinleştirmek için en son CLı sürümünü (sürüm 2.0.70 veya üzeri) yüklemelisiniz. Azure CLı yükleme hakkında daha fazla bilgi için bkz. [Azure CLI 'Yı yükleme](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Yeni bir depolama hesabı oluşturmak için[az Storage Account Create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)çağırın ve `--enable-files-aadds` özelliği **true**olarak ayarlayın. Aşağıdaki örnekte, yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın. (Önceki önizleme modülünü kullanıyorsanız, özellik etkinleştirme parametresi **Dosya-AAD**' dir.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Mevcut depolama hesaplarında bu özelliği etkinleştirmek için aşağıdaki komutu kullanın:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```


## <a name="assign-access-permissions-to-an-identity"></a>Kimliğe erişim izinleri atama

Azure AD kimlik bilgileriyle Azure dosyaları kaynaklarına erişmek için bir kimlik (Kullanıcı, Grup veya hizmet sorumlusu), paylaşma düzeyinde gerekli izinlere sahip olmalıdır. Bu işlem, belirli bir kullanıcının dosya paylaşımında sahip olduğu erişim türünü belirttiğiniz Windows paylaşma izinlerinin belirtilmesine benzer. Bu bölümdeki kılavuzda, bir kimliğe bir dosya paylaşımının okuma, yazma veya silme izinlerinin nasıl atanacağı gösterilmektedir.

Kullanıcılara paylaşma düzeyi izinleri vermek için iki Azure yerleşik rolü sunuyoruz:

- **Depolama dosyası VERI SMB paylaşımı okuyucusu** , SMB üzerinden Azure depolama dosya paylaşımlarında okuma erişimine izin verir.
- **Depolama dosyası VERI SMB paylaşımı katılımcısı** , SMB üzerinden Azure depolama dosya paylaşımlarında okuma, yazma ve silme erişimine izin verir.
- **Depolama dosyası VERI SMB paylaşımı yükseltilmiş katkıda bulunanlar** , SMB üzerinden Azure depolama dosya paylaşımlarında okuma, yazma, silme ve değiştirme izinleri sağlar.

> [!IMPORTANT]
> Bir kimliğe rol atama özelliği de dahil olmak üzere bir dosya paylaşımının tam yönetim denetimi, depolama hesabı anahtarının kullanılmasını gerektirir. Yönetim denetimi, Azure AD kimlik bilgileriyle desteklenmez.

Azure portal, PowerShell veya Azure CLı kullanarak, yerleşik rolleri, bir kullanıcının Azure AD kimliğine, paylaşma düzeyi izinleri vermek için atayabilirsiniz.

#### <a name="azure-portal"></a>Azure portal
[Azure Portal](https://portal.azure.com)kullanarak BIR Azure AD kimliğine RBAC rolü atamak için aşağıdaki adımları izleyin:

1. Azure portal, dosya paylaşımınıza gidin veya [Azure dosyalarında bir dosya paylaşma oluşturun](storage-how-to-create-file-share.md).
2. Seçin **erişim denetimi (IAM)** .
3. **Rol ataması Ekle** ' yi seçin
4. **Rol ataması Ekle** dikey penceresinde, **rol** listesinden uygun yerleşik rolü (depolama dosyası veri SMB paylaşma okuyucusu, depolama dosya veri SMB paylaşma katılımcısı) seçin. Varsayılan ayarda **erişim ata** seçeneğini bırakın: **Azure AD kullanıcısı, grubu veya hizmet sorumlusu**. Hedef Azure AD kimliğini ada veya e-posta adresine göre seçin.
5. Rol atama işlemini gerçekleştirmek için **Kaydet** ' i seçin.

#### <a name="powershell"></a>PowerShell

Aşağıdaki PowerShell komutu, oturum açma adına göre bir Azure AD kimliğine RBAC rolü atamayı gösterir. PowerShell ile RBAC rolleri atama hakkında daha fazla bilgi için bkz. [RBAC ve Azure PowerShell kullanarak erişimi yönetme](../../role-based-access-control/role-assignments-powershell.md).

Aşağıdaki örnek betiği çalıştırmadan önce, parantez dahil yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
Aşağıdaki CLı 2,0 komutunda, oturum açma adına göre bir Azure AD kimliğine RBAC rolü atama gösterilmektedir. Azure CLı ile RBAC rolleri atama hakkında daha fazla bilgi için bkz. [RBAC ve Azure CLI kullanarak erişimi yönetme](../../role-based-access-control/role-assignments-cli.md). 

Aşağıdaki örnek betiği çalıştırmadan önce, parantez dahil yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>SMB üzerinde NTFS izinlerini yapılandırma 
RBAC ile paylaşma düzeyi izinleri atadıktan sonra, kök, dizin veya dosya düzeyinde uygun NTFS izinleri atamanız gerekir. Bir kullanıcının paylaşıma erişip erişemeyeceğini belirleyen üst düzey ağ geçidi olarak paylaşma düzeyi izinleri düşünün. NTFS izinleri, kullanıcının dizin veya dosya düzeyinde hangi işlemleri yapabileceğini belirlemek için daha ayrıntılı bir düzeyde davranır.

Azure dosyaları, tüm NTFS temel ve gelişmiş izinleri kümesini destekler. Windows Dosya Gezgini 'ni kullanarak veya Windows [ıacl](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) veya [set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) komutunu çalıştırarak bir Azure dosya PAYLAŞıMıNDAKI dizin ve dosyalarda NTFS izinlerini görüntüleyebilir ve yapılandırabilirsiniz. 

NTFS 'yi üst Kullanıcı izinleriyle yapılandırmak için, etki alanına katılmış VM 'nizden depolama hesabı anahtarınızı kullanarak paylaşıma bağlamanız gerekir. Bir sonraki bölümdeki yönergeleri izleyerek bir Azure dosya paylaşımından komut isteminden bağlama yapın ve NTFS izinlerini uygun şekilde yapılandırın.

Aşağıdaki izin kümeleri bir dosya paylaşımının kök dizininde desteklenir:

- BUILTIN\Administrators: (OI) (CI) (F)
- NT AUTHORITY\SYSTEM: (OI) (CI) (F)
- BUILTIN\Users: (RX)
- BUILTIN\Users: (OI) (CI) (GÇ) (GR, GE)
- NT Authorıty\authenticated users: (OI) (CI) (ı)
- NT AUTHORITY\SYSTEM: (F)
- OLUŞTURAN SAHIBI: (OI) (CI) (GÇ) (F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Komut isteminden bir dosya paylaşma bağlama

Azure dosya paylaşımından bağlamak için Windows **net use** komutunu kullanın. Aşağıdaki örnekteki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın. Dosya paylaşımları bağlama hakkında daha fazla bilgi için bkz. [Azure dosya paylaşımını bağlama ve Windows 'da paylaşıma erişme](storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Windows Dosya Gezgini ile NTFS izinlerini yapılandırma
Kök dizin dahil olmak üzere dosya paylaşımındaki tüm dizinlere ve dosyalara tam izin vermek için Windows Dosya Gezgini 'ni kullanın.

1. Windows Dosya Gezgini 'ni açın ve dosya/dizine sağ tıklayıp **Özellikler** ' i seçin.
2. **Güvenlik** sekmesine tıklayın
3. Düzenle 'ye tıklayın **..** . izinleri değiştirme düğmesi
4. Mevcut kullanıcıların iznini değiştirebilir veya yeni kullanıcılara izin vermek için **Ekle..** . seçeneğine tıklayabilirsiniz
5. Yeni Kullanıcı ekleme istemi penceresinde, izin vermek istediğiniz hedef Kullanıcı adını **Seçilecek nesne adlarını girin** kutusuna girin ve hedef kullanıcının tam UPN adını bulmak Için **adları denetle** ' ye tıklayın.
7.  **Tamam 'a** tıklayın
8.  Güvenlik sekmesinde, Yeni Kullanıcı Ekle ' ye vermek istediğiniz tüm izinleri seçin
9.  **Uygula** ' ya tıklayın

### <a name="configure-ntfs-permissions-with-icacls"></a>Iacl 'lerle NTFS izinlerini yapılandırma
Kök dizin dahil olmak üzere, dosya paylaşımındaki tüm dizin ve dosyalara tam izinler vermek için aşağıdaki Windows komutunu kullanın. Örnekteki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

NTFS izinlerini ve desteklenen farklı türlerdeki izinleri ayarlamak için ıacl 'leri kullanma hakkında daha fazla bilgi için bkz. [ıacl 'ler için komut satırı başvurusu](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Etki alanına katılmış bir VM 'den dosya paylaşma bağlama

Aşağıdaki işlem, Azure AD kimlik bilgilerinizin doğru şekilde ayarlandığını ve etki alanına katılmış bir VM 'den bir Azure dosya paylaşımının erişebileceğini doğrular: 

Aşağıdaki görüntüde gösterildiği gibi, izin verdiğiniz Azure AD kimliğini kullanarak VM 'de oturum açın.

![Kullanıcı kimlik doğrulaması için Azure AD oturum açma ekranını gösteren ekran görüntüsü](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

Azure dosya paylaşımından bağlama için aşağıdaki komutu kullanın. Yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın. Zaten kimliğiniz doğrulandıktan sonra depolama hesabı anahtarını veya Azure AD Kullanıcı adını ve parolasını sağlamanız gerekmez. SMB üzerinden Azure AD, Azure AD kimlik bilgileriyle çoklu oturum açma deneyimini destekler.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

Azure AD kimlik doğrulamasını SMB üzerinden başarıyla etkinleştirdiniz ve bir Azure AD kimliğiyle Azure dosya paylaşımıyla erişim sağlayan özel bir rol atadınız. Dosya paylaşımınıza ek kullanıcılar için erişim izni vermek üzere, [bir kimliğe erişim atama izinleri](#assign-access-permissions-to-an-identity) ve [SMB bölümleri üzerinde NTFS izinleri yapılandırma](#configure-ntfs-permissions-over-smb) içindeki yönergeleri izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Azure dosyaları ve SMB üzerinden Azure AD kullanma hakkında daha fazla bilgi için şu kaynaklara bakın:

- [Azure dosyaları 'na giriş](storage-files-introduction.md)
- [Azure dosyaları için SMB üzerinden Azure Active Directory kimlik doğrulamasına genel bakış](storage-files-active-directory-overview.md)
- [SSS](storage-files-faq.md)
