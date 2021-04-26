---
title: SMB üzerinden dosya verilerine erişim yetkisi vermek için Azure AD Domain Services kullanın
description: Azure Active Directory Domain Services aracılığıyla Azure dosyaları için sunucu Ileti bloğu (SMB) üzerinden kimlik tabanlı kimlik doğrulamasını nasıl etkinleştireceğinizi öğrenin. Etki alanına katılmış Windows sanal makineleriniz (VM), Azure AD kimlik bilgilerini kullanarak Azure dosya paylaşımlarına erişebilir.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/03/2021
ms.author: rogarana
ms.subservice: files
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: e82ac4e1fdb8e5f88c9d83924da50b1ff5c659cc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777932"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Azure dosyalarında Azure Active Directory Domain Services kimlik doğrulamasını etkinleştirme

[Azure dosyaları](storage-files-introduction.md)   iki tür etki alanı hizmeti arasında sunucu Ileti bloğu (SMB) üzerinden kimlik tabanlı kimlik doğrulamasını destekler: şirket içi Active Directory Domain Services (AD DS) ve Azure Active Directory Domain Services (Azure AD DS). Kimlik doğrulaması için doğru etki alanı hizmetini seçmek üzere [nasıl çalıştığını](./storage-files-active-directory-overview.md#how-it-works) incelemenizi önemle öneririz. Kurulum, seçtiğiniz etki alanı hizmetine göre farklılık açmış. Bu makale, Azure dosya paylaşımları ile kimlik doğrulaması için Azure AD DS etkinleştirme ve yapılandırma konusunda odaklanır.

Azure dosya paylaşımlarından yeni bir deneyiminiz varsa, aşağıdaki makale serisini okumadan önce [planlama kılavuzumuzu](storage-files-planning.md) okumanızı öneririz.

> [!NOTE]
> Azure dosyaları, Azure AD DS yalnızca RC4-HMAC ile Kerberos kimlik doğrulamasını destekler. AES Kerberos şifrelemesi henüz desteklenmiyor.
> Azure dosyaları Azure AD ile tam eşitlemeyle Azure AD DS kimlik doğrulamasını destekler. Azure AD 'de yalnızca sınırlı bir kimlik kümesini eşitleyen Azure AD DS kapsamlı eşitlemeyi etkinleştirdiyseniz, kimlik doğrulama ve yetkilendirme desteklenmez.

## <a name="prerequisites"></a>Önkoşullar

Azure dosya paylaşımları için SMB üzerinden Azure AD 'yi etkinleştirmeden önce, aşağıdaki önkoşulları tamamladığınızdan emin olun:

1.  **Bir Azure AD kiracısı seçin veya oluşturun.**

    SMB üzerinden Azure AD kimlik doğrulaması için yeni veya mevcut bir kiracı kullanabilirsiniz. Erişmek istediğiniz kiracı ve dosya paylaşımının aynı abonelikle ilişkilendirilmesi gerekir.

    Yeni bir Azure AD kiracısı oluşturmak için [bir Azure AD kiracısı ve bir Azure AD aboneliği ekleyebilirsiniz](/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Mevcut bir Azure AD kiracınız varsa ancak Azure dosya paylaşımlarıyla kullanılmak üzere yeni bir kiracı oluşturmak istiyorsanız, bkz. [Azure Active Directory kiracı oluşturma](/rest/api/datacatalog/create-an-azure-active-directory-tenant).

1.  **Azure AD kiracısında Azure AD Domain Services etkinleştirin.**

    Azure AD kimlik bilgileriyle kimlik doğrulamasını desteklemek için, Azure AD kiracınız için Azure AD Domain Services etkinleştirmeniz gerekir. Azure AD kiracısı yöneticisi değilseniz, yöneticiye başvurun ve [Azure Portal kullanarak Azure Active Directory Domain Services etkinleştirmek](../../active-directory-domain-services/tutorial-create-instance.md)için adım adım yönergeleri izleyin.

    Azure AD DS dağıtımının tamamlanabilmesi için genellikle yaklaşık 15 dakika sürer. Bir sonraki adıma geçmeden önce, Azure AD DS sistem durumunun **çalıştığını**, Parola karması eşitlemesi etkinken çalıştığını doğrulayın.

1.  **Etki alanı-Azure AD DS ile bir Azure VM 'ye katın.**

    Bir VM 'den Azure AD kimlik bilgilerini kullanarak bir dosya paylaşımıyla erişmek için, sanal makinenizin Azure AD DS etki alanına katılmış olması gerekir. VM 'nin etki alanına katılması hakkında daha fazla bilgi için, bkz. [Windows Server sanal makinesini yönetilen bir etki alanına katma](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > Azure dosya paylaşımları ile SMB üzerinden Azure AD DS kimlik doğrulaması, yalnızca Windows 7 veya Windows Server 2008 R2 üzerindeki işletim sistemi sürümlerinde çalışan Azure VM 'lerde desteklenir.

1.  **Bir Azure dosya paylaşımından seçim yapın veya oluşturun.**

    Azure AD kiracınızla aynı abonelikle ilişkili yeni veya var olan bir dosya paylaşma seçin. Yeni bir dosya paylaşma oluşturma hakkında daha fazla bilgi için bkz. [Azure dosyalarında dosya paylaşma oluşturma](storage-how-to-create-file-share.md).
    En iyi performans için, dosya paylaşımınızın paylaşıma erişmeyi planladığınız VM ile aynı bölgede olmasını öneririz.

1.  **Azure dosya paylaşımlarını depolama hesabı anahtarınızı kullanarak bağlayarak Azure dosyaları bağlantısını doğrulayın.**

    VM ve dosya paylaşımınızın düzgün yapılandırıldığını doğrulamak için, depolama hesabı anahtarınızı kullanarak dosya paylaşımının bağlanmasını deneyin. Daha fazla bilgi için bkz. [Azure dosya paylaşma bağlama ve Windows 'da paylaşıma erişme](storage-how-to-use-files-windows.md).

## <a name="regional-availability"></a>Bölgesel kullanılabilirlik

Azure AD DS ile Azure dosyaları kimlik doğrulaması, [tüm Azure genel, gov ve Çin bölgelerinde](https://azure.microsoft.com/global-infrastructure/locations/)kullanılabilir.

## <a name="overview-of-the-workflow"></a>İş akışına genel bakış

Azure dosya paylaşımları için SMB üzerinden Azure AD DS kimlik doğrulamasını etkinleştirmeden önce Azure AD ve Azure depolama ortamlarınızın düzgün şekilde yapılandırıldığını doğrulayın. Gerekli tüm adımları tamamladığınızdan emin olmak için [önkoşulları](#prerequisites) gözden etmenizi öneririz.

Daha sonra, Azure AD kimlik bilgileriyle Azure dosya kaynaklarına erişim vermek için aşağıdaki işlemleri yapın:

1. Depolama hesabınızı ilişkili Azure AD DS dağıtımına kaydetmek için depolama hesabınız için SMB üzerinden Azure AD DS kimlik doğrulamasını etkinleştirin.
2. Bir Azure AD kimliğine (bir Kullanıcı, Grup veya hizmet sorumlusu) bir paylaşıma yönelik erişim izinleri atayın.
3. Dizinler ve dosyalar için SMB üzerinde NTFS izinleri yapılandırın.
4. Etki alanına katılmış bir VM 'den Azure dosya paylaşımının bağlama.

Aşağıdaki diyagramda Azure dosyaları için SMB üzerinden Azure AD DS kimlik doğrulamasını etkinleştirmeye yönelik uçtan uca iş akışı gösterilmektedir.

![Azure dosyaları için SMB üzerinden Azure AD iş akışı gösteren diyagram](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Hesabınız için Azure AD DS kimlik doğrulamasını etkinleştirin

Azure dosyaları için SMB üzerinden Azure AD DS kimlik doğrulamasını etkinleştirmek için, Azure portal, Azure PowerShell veya Azure CLı kullanarak depolama hesaplarında bir özelliği ayarlayabilirsiniz. Bu özelliğin örtük olarak "etki alanı" olarak ayarlanması, depolama hesabını ilişkili Azure AD DS dağıtımıyla birleştirir. SMB üzerinden Azure AD DS kimlik doğrulaması, depolama hesabındaki tüm yeni ve var olan dosya paylaşımları için etkinleştirilir.

Azure AD DS kimlik doğrulamasını yalnızca Azure AD DS Azure AD kiracınıza başarıyla dağıttıktan sonra SMB üzerinden etkinleştirebileceğinizi aklınızda bulundurun. Daha fazla bilgi için bkz. [Önkoşullar](#prerequisites).

# <a name="portal"></a>[Portal](#tab/azure-portal)

[Azure Portal](https://portal.azure.com)SMB üzerinden Azure AD DS kimlik doğrulamasını etkinleştirmek için şu adımları izleyin:

1. Azure portal, mevcut depolama hesabınıza gidin veya [bir depolama hesabı oluşturun](../common/storage-account-create.md).
1. **Ayarlar** bölümünde **yapılandırma**' yı seçin.
1. **Dosya paylaşımları Için kimlik tabanlı erişim** altında **Azure Active Directory etki alanı HIZMETI 'nın (AAD DS)** **etkin** olarak değiştirin.
1. **Kaydet**’i seçin.

Aşağıdaki görüntüde, depolama hesabınız için SMB üzerinden Azure AD DS kimlik doğrulamasının nasıl etkinleştirileceği gösterilmektedir.

:::image type="content" source="media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png" alt-text="Depolama hesabınızdaki yapılandırma dikey penceresinin ekran görüntüsü, Azure Active Directory etki alanı Hizmetleri etkindir." lightbox="media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell SMB üzerinden Azure AD DS kimlik doğrulamasını etkinleştirmek için en son az modülünü (2,4 veya daha yeni) veya az. Storage modülünü (1,5 veya daha yeni bir sürüm). PowerShell 'i yükleme hakkında daha fazla bilgi için bkz. [PowerShellGet Ile Windows 'da Azure PowerShell yükleme](/powershell/azure/install-Az-ps).

Yeni bir depolama hesabı oluşturmak için [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount)' ı çağırın ve sonra **Enableazureactivedirectorydomainservicesforfile** parametresini **true** olarak ayarlayın. Aşağıdaki örnekte, yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın. (Önceki önizleme modülünü kullanıyorsanız, özelliği etkinleştirme parametresi **Enableazurefilesaadıntegrationforsmb**' dir.)

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


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı ile SMB üzerinden Azure AD kimlik doğrulamasını etkinleştirmek için en son CLı sürümünü (sürüm 2.0.70 veya üzeri) yüklemelisiniz. Azure CLı yükleme hakkında daha fazla bilgi için bkz. [Azure CLI 'Yı yükleme](/cli/azure/install-azure-cli).

Yeni bir depolama hesabı oluşturmak için [az Storage Account Create](/cli/azure/storage/account#az_storage_account_create)çağırın ve `--enable-files-aadds` özelliği **true** olarak ayarlayın. Aşağıdaki örnekte, yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın. (Önceki önizleme modülünü kullanıyorsanız, özellik etkinleştirme parametresi **Dosya-AAD**' dir.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Mevcut depolama hesaplarında bu özelliği etkinleştirmek için aşağıdaki komutu kullanın:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```
---

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Azure AD DS kimlik doğrulamasını SMB üzerinden başarıyla etkinleştirdiniz ve bir Azure AD kimliğiyle Azure dosya paylaşımıyla erişim sağlayan özel bir rol atamış oldunuz. Dosya paylaşımınıza ek kullanıcılar için erişim izni vermek üzere, bir kimlik kullanmak ve [SMB bölümleri ÜZERINDE NTFS Izinleri yapılandırmak](#configure-ntfs-permissions-over-smb)Için [erişim atama izinleri](#assign-access-permissions-to-an-identity) içindeki yönergeleri izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Azure dosyaları ve SMB üzerinden Azure AD kullanma hakkında daha fazla bilgi için şu kaynaklara bakın:

- [SMB için Azure Dosyalar kimlik tabanlı kimlik doğrulaması desteğine genel bakış](storage-files-active-directory-overview.md)
- [SSS](storage-files-faq.md)
