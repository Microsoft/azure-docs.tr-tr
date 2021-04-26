---
title: Windows sanal masaüstü (klasik) konak havuzu Azure Marketi-Azure
description: Azure Marketi 'ni kullanarak Windows sanal masaüstü (klasik) konak havuzu oluşturma.
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: fa271ba87f68d46452633d2b426c7fd805f8566b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445168"
---
# <a name="tutorial-create-a-host-pool-in-windows-virtual-desktop-classic"></a>Öğretici: Windows sanal masaüstü 'nde bir konak havuzu oluşturma (klasik)

>[!IMPORTANT]
>Bu içerik, Windows sanal masaüstü nesneleri Azure Resource Manager desteklemeyen Windows sanal masaüstü (klasik) için geçerlidir. Azure Resource Manager Windows sanal masaüstü nesnelerini yönetmeye çalışıyorsanız, [Bu makaleye](../create-host-pools-azure-marketplace.md)bakın.

Bu öğreticide, bir Microsoft Azure Market teklifi kullanarak Windows sanal masaüstü kiracısı içinde bir konak havuzu oluşturmayı öğreneceksiniz.

Konak havuzları, Windows sanal masaüstü kiracı ortamlarında bir veya daha fazla özdeş sanal makine koleksiyonudur. Her konak havuzu, kullanıcıların fiziksel bir masaüstünde yaptıkları gibi etkileşime girebilecekleri bir uygulama grubu içerebilir.

Bu öğreticideki görevler şunları içerir:

> [!div class="checklist"]
>
> * Windows sanal masaüstü 'nde bir konak havuzu oluşturun.
> * Bir Azure aboneliğinde VM 'Ler içeren bir kaynak grubu oluşturun.
> * VM 'Leri Active Directory etki alanına katın.
> * VM 'Leri Windows sanal masaüstü ile kaydedin.

## <a name="prerequisites"></a>Önkoşullar

* Sanal masaüstündeki kiracı. Önceki [öğreticide](tenant-setup-azure-active-directory.md) kiracı oluşturulur.
* [Windows sanal masaüstü PowerShell modülü](/powershell/windows-virtual-desktop/overview/).

Bu modülden sonra, hesabınızda oturum açmak için aşağıdaki cmdlet 'i çalıştırın:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Yeni bir konak havuzu sağlamak için Azure Marketi teklifini çalıştırın

Yeni bir konak havuzu sağlamak için Azure Marketi teklifini çalıştırmak için:

1. Azure portal menüsünde veya **Giriş** sayfasında, **Kaynak oluştur**’u seçin.
1. Market arama penceresinde **Windows sanal masaüstü** ' nu girin.
1. **Windows Sanal Masaüstü ' nü seçin-bir konak havuzu sağlayın** ve **Oluştur**' u seçin.

Bundan sonra, uygun sekmelerin bilgilerini girmek için sonraki bölümde yer alan yönergeleri izleyin.

### <a name="basics"></a>Temel Bilgiler

**Temel kavramlar** sekmesi için şunları yapın:

1. Bir **abonelik** seçin.
1. **Kaynak grubu** Için **Yeni oluştur** ' u seçin ve yeni kaynak grubu için bir ad sağlayın.
1. Bir **bölge** seçin.
1. Windows sanal masaüstü kiracısı içinde benzersiz olan konak havuzu için bir ad girin.
1. **Masaüstü türünü** seçin. **Kişisel**' i seçerseniz, bu konak havuzuna bağlanan her bir Kullanıcı, bir sanal makineye kalıcı olarak atanır.
1. Windows sanal masaüstü istemcilerinde oturum açabilen ve bir masaüstüne erişebilen kullanıcıları girin. Virgülle ayrılmış bir liste kullanın. Örneğin, atamak `user1@contoso.com` ve `user2@contoso.com` erişmek istiyorsanız, şunu girin *`user1@contoso.com,user2@contoso.com`*
1. **Hizmet meta verileri konumu** için, Active Directory sunucusuyla bağlantısı olan sanal ağla aynı konumu seçin.

   >[!IMPORTANT]
   >Saf Azure Active Directory Domain Services (Azure AD DS) ve Azure Active Directory (Azure AD) çözümü kullanıyorsanız, etki alanına ekleme ve kimlik bilgisi hatalarından kaçınmak için konak havuzunuzu Azure AD DS ile aynı bölgede dağıttığınızdan emin olun.

1. **İleri ' yi seçin: sanal makineleri yapılandırın**.

### <a name="configure-virtual-machines"></a>Sanal makineleri yapılandırma

**Sanal makineleri Yapılandır** sekmesini için:

1. Varsayılanları kabul edin ya da sanal makinelerin sayısını ve boyutunu özelleştirin.

    >[!NOTE]
    >Aradığınız belirli bir sanal makine boyutu, boyut seçicisinde görünmüyorsa, bunun nedeni, henüz Azure Market aracına eklendi.

2. Sanal makinelerin adları için bir ön ek girin. Örneğin, *ön ek* girerseniz, sanal makineler **önek-0**, **ön ek-1** vb. olarak adlandırılır.
3. **İleri ' yi seçin: sanal makine ayarları**.

### <a name="virtual-machine-settings"></a>Sanal makine ayarları

**Sanal makine ayarları** sekmesi için:

1. **Görüntü kaynağı** için kaynağı seçin ve nasıl bulacağınızı ve nasıl depolanacağını öğrenmek için uygun bilgileri girin. Seçenekleriniz BLOB depolama, yönetilen görüntü ve Galeri için farklılık gösterir.

   Yönetilen diskler kullanmayı tercih ederseniz, *. vhd* dosyasını içeren depolama hesabını seçin.
1. Kullanıcı asıl adını ve parolasını girin. Bu hesap, sanal makinelere Active Directory etki alanına katılacak etki alanı hesabı olmalıdır. Bu aynı Kullanıcı adı ve parola, sanal makinelerde yerel bir hesap olarak oluşturulacaktır. Bu yerel hesapları daha sonra sıfırlayabilirsiniz.

   >[!NOTE]
   > Sanal makinelerinizi bir Azure AD DS ortamına katılıyorsanız, etki alanına katılma Kullanıcı tarafından [AAD DC yöneticileri grubunun](../../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)bir üyesi olduğundan emin olun.
   >
   > Hesap Ayrıca Azure AD DS yönetilen etki alanının veya Azure AD kiracısının bir parçası olmalıdır. Azure AD kiracınızla ilişkilendirilen dış dizinlerden hesaplar, etki alanına ekleme işlemi sırasında doğru şekilde kimlik doğrulaması yapamaz.

1. Active Directory sunucusuyla bağlantısı olan **sanal ağı** seçin ve ardından sanal makineleri barındırmak için bir alt ağ seçin.
1. **İleri ' yi seçin: Windows sanal masaüstü bilgileri**.

### <a name="windows-virtual-desktop-tenant-information"></a>Windows sanal masaüstü kiracı bilgileri

**Windows sanal masaüstü kiracı bilgileri** sekmesi için:

1. **Windows sanal masaüstü kiracı grubu adı** için kiracınızı içeren kiracı grubunun adını girin. Belirli bir kiracı grubu adı sağlanmadığınız sürece varsayılan olarak bırakın.
1. **Windows sanal masaüstü kiracı adı** için, bu konak havuzunu oluşturacağınız kiracının adını girin.
1. Windows sanal masaüstü kiracı RDS sahibi olarak kimlik doğrulaması yapmak için kullanmak istediğiniz kimlik bilgileri türünü belirtin. UPN veya hizmet sorumlusu ve bir parola girin.

   [PowerShell öğreticisi ile hizmet sorumluları ve rol atamaları oluştur](create-service-principal-role-powershell.md)' u tamamladıysanız **hizmet sorumlusu**' nı seçin.

1. **Hizmet sorumlusu** Için, **Azure AD Kiracı kimliği** Için HIZMET sorumlusunu içeren Azure AD örneği için kiracı yönetici hesabını girin. Yalnızca parola kimlik bilgilerine sahip hizmet sorumluları desteklenir.
1. **Sonraki: Gözden geçirme ve oluşturma**’yı seçin.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Kurulumu tamamladıktan sonra sanal makineyi oluşturun

**İnceleme ve oluşturma** bölümünde kurulum bilgilerini gözden geçirin. Bir değişiklik yapmanız gerekiyorsa, geri dönüp değişiklik yapın. Hazırsanız, konak havuzunuzu dağıtmak için **Oluştur** ' u seçin.

Bu işlem, kaç sanal makineye sahip olduğunuza bağlı olarak tamamlanması 30 dakika veya daha fazla sürebilir.

>[!IMPORTANT]
> Azure 'da Windows sanal masaüstü ortamınızı güvenli hale getirmeye yardımcı olmak için, sanal makinelerinizde gelen bağlantı noktası 3389 ' i açmanız önerilir. Windows sanal masaüstü, kullanıcıların konak havuzunun sanal makinelerine erişmesi için açık bir gelen bağlantı noktası 3389 gerektirmez.
>
> Sorun giderme amacıyla bağlantı noktası 3389 ' i açmanız gerekiyorsa, tam zamanında erişim kullanmanızı öneririz. Daha fazla bilgi için bkz. [tam zamanında erişim ile yönetim bağlantı noktalarınızı güvenli hale getirme](../../security-center/security-center-just-in-time.md).

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>Seçim Masaüstü uygulama grubuna ek kullanıcılar atama

Azure Marketi havuzu oluşturmayı tamamladıktan sonra, Masaüstü uygulama grubuna daha fazla kullanıcı atayabilirsiniz. Daha fazla eklemek istemiyorsanız, bu bölümü atlayın.

Kullanıcıları Masaüstü uygulama grubuna atamak için:

1. PowerShell penceresini açın.

1. Windows sanal masaüstü ortamında oturum açmak için aşağıdaki komutu çalıştırın:

   ```powershell
   Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
   ```

1. Şu komutu kullanarak masaüstü uygulama grubuna kullanıcı ekleyin:

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
   ```

   Kullanıcının UPN 'si kullanıcının Azure AD 'deki kimliğiyle eşleşmelidir; Örneğin, *user1@contoso.com* . Birden çok kullanıcı eklemek istiyorsanız, her kullanıcı için komutunu çalıştırın.

Masaüstü uygulama grubuna eklediğiniz kullanıcılar, desteklenen uzak masaüstü istemcileri ile Windows sanal masaüstü 'nde oturum açabilir ve oturum Masaüstü için bir kaynak görebilirler.

Desteklenen geçerli istemciler şunlardır:

* [Windows 7 ve Windows 10 için Uzak Masaüstü istemcisi](connect-windows-7-10-2019.md)
* [Windows Sanal Masaüstü Web istemcisi](connect-web-2019.md)

## <a name="next-steps"></a>Sonraki adımlar

Bir konak havuzu oluşturdunuz ve kullanıcılara masaüstüne erişim atadık. Konak havuzunuzu RemoteApp programları ile doldurabilirsiniz. Windows sanal masaüstündeki uygulamaları yönetme hakkında daha fazla bilgi edinmek için şu öğreticiye bakın:

> [!div class="nextstepaction"]
> [Uygulama gruplarını yönetme öğreticisi](manage-app-groups-2019.md)
