---
title: Windows sanal masaüstü konak havuzu Azure Marketi-Azure
description: Azure Marketi 'ni kullanarak Windows sanal masaüstü konak havuzu oluşturma.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: helohr
ms.openlocfilehash: e5be2490ebf756c030e6a53b226f91c49c7bccaf
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605579"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>Öğretici: Azure Marketi 'ni kullanarak bir konak havuzu oluşturma

Konak havuzları, Windows sanal masaüstü kiracı ortamlarında bir veya daha fazla özdeş sanal makine koleksiyonudur. Her konak havuzu, kullanıcıların fiziksel bir masaüstünde yaptıkları gibi etkileşime girebilecekleri bir uygulama grubu içerebilir.

Bu öğreticide, bir Microsoft Azure Market teklifi kullanarak Windows sanal masaüstü kiracısı içinde bir konak havuzunun nasıl oluşturulacağı açıklanmaktadır. Görevler şunları içerir:

> [!div class="checklist"]
> * Windows sanal masaüstü 'nde bir konak havuzu oluşturun.
> * Bir Azure aboneliğinde VM 'Ler içeren bir kaynak grubu oluşturun.
> * VM 'Leri Active Directory etki alanına katın.
> * VM 'Leri Windows sanal masaüstü ile kaydedin.

Başlamadan önce, henüz yapmadıysanız PowerShell oturumunuzda kullanmak üzere [Windows sanal masaüstü PowerShell modülünü indirip içeri aktarın](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) . Bundan sonra hesabınızda oturum açmak için aşağıdaki cmdlet 'i çalıştırın:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Yeni bir konak havuzu sağlamak için Azure Marketi teklifini çalıştırın

Yeni bir konak havuzu sağlamak için Azure Marketi teklifini çalıştırmak için:

1. Azure portal menüsünde veya **giriş** sayfasında, **kaynak oluştur**' u seçin.
2. Market arama penceresinde **Windows sanal masaüstü** ' nu girin.
3. **Windows Sanal Masaüstü ' nü seçin-bir konak havuzu sağlayın**ve **Oluştur**' u seçin.

Bundan sonra, uygun dikey pencereler için bilgi girmek üzere sonraki bölümdeki yönergeleri izleyin.

### <a name="basics"></a>Temel Bilgiler

**Temel bilgiler** dikey penceresinde şunları yapın:

1. Windows sanal masaüstü kiracısı içinde benzersiz olan konak havuzu için bir ad girin.
2. Kişisel masaüstü için uygun seçeneği belirleyin. **Evet**' i seçerseniz, bu konak havuzuna bağlanan her bir Kullanıcı, bir sanal makineye kalıcı olarak atanır.
3. Windows sanal masaüstü istemcilerinde oturum açabilen ve Azure Marketi teklifi bittikten sonra bir masaüstüne erişebilen kullanıcıların virgülle ayrılmış bir listesini girin. Örneğin, user1@contoso.com atamak ve erişim user2@contoso.com istiyorsanız "user1@contoso.com,user2@contoso.com" yazın.
4. Yeni **Oluştur** ' u seçin ve yeni kaynak grubu için bir ad sağlayın.
5. **Konum**için, Active Directory sunucusuyla bağlantısı olan sanal ağla aynı konumu seçin.
6. **Tamam**’ı seçin.

>[!IMPORTANT]
>Saf Azure Active Directory Domain Services ve Azure Active Directory çözümü kullanıyorsanız, etki alanına ekleme ve kimlik bilgisi hatalarından kaçınmak için konak havuzunuzu Azure Active Directory Domain Services ile aynı bölgede dağıttığınızdan emin olun.

### <a name="configure-virtual-machines"></a>Sanal makineleri yapılandırma

**Sanal makineleri Yapılandır** dikey penceresi için:

1. Varsayılanları kabul edin ya da VM 'lerin sayısını ve boyutunu özelleştirin.
2. Sanal makinelerin adları için bir ön ek girin. Örneğin, "önek" adını girerseniz, sanal makineler "ön ek-0", "ön ek-1" olarak adlandırılır ve bu şekilde devam eder.
3. **Tamam**’ı seçin.

### <a name="virtual-machine-settings"></a>Sanal makine ayarları

**Sanal makine ayarları** dikey penceresi için:

>[!NOTE]
> VM 'lerinizi bir Azure Active Directory Domain Services (Azure AD DS) ortamına katılıyorsanız, etki alanına katılma Kullanıcı tarafından [AAD DC yöneticileri grubunun](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)bir üyesi olduğundan emin olun.
>
> Hesap Ayrıca Azure AD DS yönetilen etki alanının veya Azure AD kiracısının bir parçası olmalıdır-Azure AD kiracınızla ilişkili dış dizinlerden gelen hesaplar, etki alanına ekleme işlemi sırasında doğru şekilde kimlik doğrulaması yapamaz. 

1. **Görüntü kaynağı**için kaynağı seçin ve nasıl bulacağınızı ve nasıl depolanacağını öğrenmek için uygun bilgileri girin. Yönetilen diskler kullanmayı tercih ederseniz,. vhd dosyasını içeren depolama hesabını seçin.
2. VM 'Lere Active Directory etki alanına katılacak etki alanı hesabının kullanıcı asıl adını ve parolasını girin. Bu aynı Kullanıcı adı ve parola, sanal makinelerde yerel bir hesap olarak oluşturulacaktır. Bu yerel hesapları daha sonra sıfırlayabilirsiniz.
3. Active Directory sunucusuyla bağlantısı olan sanal ağı seçin ve ardından sanal makineleri barındırmak için bir alt ağ seçin.
4. **Tamam**’ı seçin.

### <a name="windows-virtual-desktop-tenant-information"></a>Windows sanal masaüstü kiracı bilgileri

**Windows sanal masaüstü kiracı bilgileri dikey penceresi** için:

1. **Windows sanal masaüstü kiracı grubu adı**için kiracınızı içeren kiracı grubunun adını girin. Belirli bir kiracı grubu adı sağlanmadığınız sürece varsayılan olarak bırakın.
2. **Windows sanal masaüstü kiracı adı**için, bu konak havuzunu oluşturacağınız kiracının adını girin.
3. Windows sanal masaüstü kiracı RDS sahibi olarak kimlik doğrulaması yapmak için kullanmak istediğiniz kimlik bilgileri türünü belirtin. [PowerShell öğreticisi ile hizmet sorumluları ve rol atamaları oluştur](./create-service-principal-role-powershell.md)' u tamamladıysanız **hizmet sorumlusu**' nı seçin. **Azure AD KIRACı kimliği** göründüğünde, hizmet sorumlusunu içeren Azure ACTIVE DIRECTORY örneğinin kimliğini girin.
4. Kiracı yönetici hesabı için kimlik bilgilerini girin. Yalnızca parola kimlik bilgilerine sahip hizmet sorumluları desteklenir.
5. **Tamam**’ı seçin.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Kurulumu tamamladıktan sonra sanal makineyi oluşturun

Son iki dikey pencere için:

1. **Özet** dikey penceresinde kurulum bilgilerini gözden geçirin. Bir değişiklik yapmanız gerekiyorsa, uygun dikey pencereye geri dönün ve devam etmeden önce değişikliği yapın. Bilgiler doğru görünüyorsa **Tamam**' ı seçin.
2. **Satın al** dikey penceresinde, Azure Marketi 'nden satın alma ile ilgili ek bilgileri gözden geçirin.
3. Konak havuzunuzu dağıtmak için **Oluştur** ' u seçin.

Oluşturmakta olduğunuz VM sayısına bağlı olarak, bu işlemin tamamlanması 30 dakika veya daha fazla sürebilir.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>Seçim Masaüstü uygulama grubuna ek kullanıcılar atama

Azure Marketi teklifi bittikten sonra, sanal makinelerinizdeki tam oturum masaüstlerini teste başlamadan önce Masaüstü uygulama grubuna daha fazla kullanıcı atayabilirsiniz. Zaten Azure Marketi sunumuna varsayılan kullanıcılar eklediyseniz ve daha fazla eklemek istemiyorsanız, bu bölümü atlayabilirsiniz.

Kullanıcıları Masaüstü uygulama grubuna atamak için önce bir PowerShell penceresi açmanız gerekir. Bundan sonra, aşağıdaki iki cmdlet 'i girmeniz gerekir.

Windows sanal masaüstü ortamında oturum açmak için aşağıdaki cmdlet 'i çalıştırın:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Bu cmdlet 'i kullanarak masaüstü uygulama grubuna kullanıcı ekleyin:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Kullanıcının UPN 'si Azure Active Directory kullanıcının kimliğiyle eşleşmelidir (örneğin, user1@contoso.com). Birden çok kullanıcı eklemek istiyorsanız, her kullanıcı için bu cmdlet 'i çalıştırmanız gerekir.

Bu adımları tamamladıktan sonra, Masaüstü uygulama grubuna eklenen kullanıcılar Windows sanal masaüstünde desteklenen uzak masaüstü istemcileriyle oturum açabilir ve oturum Masaüstü için bir kaynak görebilirler.

Desteklenen geçerli istemciler şunlardır:

- [Windows 7 ve Windows 10 için Uzak Masaüstü istemcisi](connect-windows-7-and-10.md)
- [Windows Sanal Masaüstü Web istemcisi](connect-web.md)

>[!IMPORTANT]
>Azure 'da Windows sanal masaüstü ortamınızı güvenli hale getirmeye yardımcı olmak için, VM 'leriniz üzerinde gelen bağlantı noktası 3389 ' i açmanız önerilir. Windows sanal masaüstü, kullanıcıların konak havuzunun VM 'lerine erişmesi için açık bir gelen bağlantı noktası 3389 gerektirmez. Sorun giderme amacıyla bağlantı noktası 3389 ' i açmanız gerekiyorsa, [tam ZAMANıNDA VM erişimi](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)kullanmanızı öneririz.

## <a name="next-steps"></a>Sonraki adımlar

Artık bir konak havuzu oluşturduğunuza ve kullanıcılara masaüstüne erişim atadığınıza göre, konak havuzunuzu RemoteApp programları ile doldurabilirsiniz. Windows sanal masaüstündeki uygulamaları yönetme hakkında daha fazla bilgi edinmek için şu öğreticiye bakın:

> [!div class="nextstepaction"]
> [Uygulama gruplarını yönetme öğreticisi](./manage-app-groups.md)
