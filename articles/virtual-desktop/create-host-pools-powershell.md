---
title: Windows sanal masaüstü konak havuzu oluşturma PowerShell-Azure
description: PowerShell cmdlet 'leriyle Windows sanal masaüstü 'nde bir konak havuzu oluşturma.
author: Heidilohr
ms.topic: how-to
ms.date: 10/02/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 2c1ce95f9eba8c31b20d8e992fa1880a5d33da8d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447870"
---
# <a name="create-a-windows-virtual-desktop-host-pool-with-powershell"></a>PowerShell ile Windows sanal masaüstü konak havuzu oluşturma

>[!IMPORTANT]
>Bu içerik Azure Resource Manager Windows sanal masaüstü nesneleri ile Windows sanal masaüstü için geçerlidir. Azure Resource Manager nesneleri olmadan Windows sanal masaüstü (klasik) kullanıyorsanız, [Bu makaleye](./virtual-desktop-fall-2019/create-host-pools-powershell-2019.md)bakın.

Konak havuzları, Windows sanal masaüstü kiracı ortamlarında bir veya daha fazla özdeş sanal makine koleksiyonudur. Her konak havuzu birden çok RemoteApp grubuyla, bir masaüstü uygulama grubuyla ve birden çok oturum ana bilgisayarıyla ilişkilendirilebilir.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede, [PowerShell modülünü ayarlama](powershell-module.md)bölümündeki yönergeleri zaten takip ettiğiniz varsayılmaktadır.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>PowerShell istemcinizi kullanarak bir konak havuzu oluşturun

Windows sanal masaüstü ortamında oturum açmak için aşağıdaki cmdlet 'i çalıştırın:

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -HostPoolType <Pooled|Personal> -LoadBalancerType <BreadthFirst|DepthFirst|Persistent> -Location <region> -DesktopAppGroupName <appgroupname>
```

Bu cmdlet konak havuzunu, çalışma alanını ve Masaüstü uygulama grubunu oluşturur. Ayrıca, Masaüstü uygulama grubunu çalışma alanına kaydeder. Bu cmdlet ile bir çalışma alanı oluşturabilir veya var olan bir çalışma alanını kullanabilirsiniz.

Bir oturum ana bilgisayarının konak havuzuna katılması ve yerel bilgisayarınızdaki yeni bir dosyaya kaydetmesi için bir kayıt belirteci oluşturmak üzere bir sonraki cmdlet 'i çalıştırın. Kayıt belirtecinin,-ExpirationHours parametresini kullanarak ne kadar süreyle geçerli olduğunu belirtebilirsiniz.

>[!NOTE]
>Belirtecin sona erme tarihi bir saatten az olamaz ve bir aydan daha fazla olamaz. *-ExpirationTime* 'ı bu sınırın dışında ayarlarsanız, cmdlet belirteci oluşturmaz.

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddDays(1).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

Örneğin, iki saat içinde süresi dolan bir belirteç oluşturmak istiyorsanız şu cmdlet 'i çalıştırın:

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddHours(2).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

Bundan sonra, konak havuzu için varsayılan masaüstü uygulama grubuna Azure Active Directory kullanıcıları eklemek için bu cmdlet 'i çalıştırın.

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Konak havuzu için varsayılan masaüstü uygulama grubuna Azure Active Directory Kullanıcı grupları eklemek için bu sonraki cmdlet 'i çalıştırın:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Kayıt belirtecini, daha sonra [sanal makineleri Windows sanal masaüstü ana bilgisayar havuzuna kaydet](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)bölümünde kullanacağınız bir değişkene dışarı aktarmak için aşağıdaki cmdlet 'i çalıştırın.

```powershell
$token = Get-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname>
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Konak havuzu için sanal makineler oluşturma

Artık Windows sanal masaüstü ana bilgisayar havuzunuza katılebilecek bir Azure sanal makinesi oluşturabilirsiniz.

Bir sanal makineyi birden çok şekilde oluşturabilirsiniz:

- [Azure Galeri görüntüsünden sanal makine oluşturma](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Yönetilen görüntüden sanal makine oluşturma](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Yönetilmeyen görüntüden sanal makine oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>Konak işletim sistemi olarak Windows 7 ' yi kullanarak bir sanal makine dağıtıyorsanız, oluşturma ve dağıtım işlemi biraz farklı olacaktır. Daha ayrıntılı bilgi için bkz. Windows [sanal masaüstü 'Nde Windows 7 sanal makinesi dağıtma](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md).

Oturum Ana bilgisayar sanal makinelerinizi oluşturduktan sonra, Windows veya Windows Server sanal makinelerinizi başka bir lisans için ödeme yapmadan çalıştırmak için bir [Oturum Ana BILGISAYAR VM 'sine bir Windows lisansı uygulayın](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) .

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Windows sanal masaüstü Aracısı yüklemeleri için sanal makineleri hazırlama

Windows sanal masaüstü aracılarını yüklemeden ve sanal makineleri Windows sanal masaüstü konak havuzunuza kaydedebilmeniz için önce sanal makinelerinizi hazırlamak üzere aşağıdaki işlemleri yapmanız gerekir:

- Makineyi etki alanına katmalısınız. Bu, gelen Windows sanal masaüstü kullanıcılarının Azure Active Directory hesabından Active Directory hesaplarına eşlenmesini ve sanal makineye başarıyla erişim izni verilmesini sağlar.
- Sanal makine bir Windows Server işletim sistemi çalıştırıyorsa Uzak Masaüstü Oturumu Ana Bilgisayarı (RDSH) rolünü yüklemelisiniz. RDSH rolü Windows sanal masaüstü aracılarının düzgün yüklenmesine izin verir.

Başarıyla etki alanına katılmayı sağlamak için, her bir sanal makine için aşağıdaki işlemleri yapın:

1. Sanal makineyi oluştururken girdiğiniz kimlik bilgileriyle [sanal makineye bağlanın](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) .
2. Sanal makinede, **Denetim Masası** ' nı başlatın ve **sistem**' i seçin.
3. **Bilgisayar adı**' nı seçin, **Ayarları Değiştir**' i seçin ve ardından Değiştir ' i seçin **.**
4. **Etki alanı** ' nı seçin ve ardından sanal ağda Active Directory etki alanını girin.
5. Etki alanına katma makinelere ayrıcalıkları olan bir etki alanı hesabıyla kimlik doğrulaması yapın.

    >[!NOTE]
    > VM 'lerinizi bir Azure Active Directory Domain Services (Azure AD DS) ortamına katılıyorsanız, etki alanına katılma kullanıcısının [AAD DC Yöneticiler grubunun](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)da bir üyesi olduğundan emin olun.

>[!IMPORTANT]
>Windows Installer devre dışı bırakan herhangi bir ilkeyi veya yapılandırmayı etkinleştirmemenizi öneririz. Windows Installer devre dışı bırakırsanız, hizmet oturum konaklarınıza aracı güncelleştirmelerini yükleyemeyecektir ve oturum ana bilgisayarları düzgün şekilde çalışmaz.

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Sanal makineleri Windows sanal masaüstü ana bilgisayar havuzuna Kaydet

Sanal makinelerin bir Windows sanal masaüstü konak havuzuna kaydedilmesi, Windows sanal masaüstü aracılarını yüklemek kadar basittir.

Windows sanal masaüstü aracılarını kaydettirmek için, her bir sanal makinede aşağıdakileri yapın:

1. Sanal makineyi oluştururken girdiğiniz kimlik bilgileriyle [sanal makineye bağlanın](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) .
2. Windows sanal masaüstü aracısını indirip yükleyin.
   - [Windows sanal masaüstü aracısını](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv)indirin.
   - Yükleyiciyi çalıştırın. Yükleyici kayıt belirtecini istediğinde, **Get-AzWvdRegistrationInfo** cmdlet 'inden aldığınız değeri girin.
3. Windows sanal masaüstü Aracısı önyükleme yükleyicisine indirin ve yükleyin.
   - [Windows sanal masaüstü Aracısı önyükleme yükleyicisine](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)indirin.
   - Yükleyiciyi çalıştırın.

>[!IMPORTANT]
>Azure 'da Windows sanal masaüstü ortamınızı güvenli hale getirmeye yardımcı olmak için, VM 'leriniz üzerinde gelen bağlantı noktası 3389 ' i açmanız önerilir. Windows sanal masaüstü, kullanıcıların konak havuzunun VM 'lerine erişmesi için açık bir gelen bağlantı noktası 3389 gerektirmez. Sorun giderme amacıyla bağlantı noktası 3389 ' i açmanız gerekiyorsa, [tam ZAMANıNDA VM erişimi](../security-center/security-center-just-in-time.md)kullanmanızı öneririz. Ayrıca VM 'lerinizi genel bir IP 'ye atamazsınız.

## <a name="update-the-agent"></a>Aracıyı güncelleştirme

Aşağıdaki durumlardan birini kullanıyorsanız aracıyı güncelleştirmeniz gerekir:

- Daha önce kayıtlı bir oturum konağını yeni bir konak havuzuna geçirmek istiyorsunuz
- Oturum Ana bilgisayarı, bir güncelleştirmeden sonra konak havuzunuzdaki görünmüyor

Aracıyı güncelleştirmek için:

1. VM 'de yönetici olarak oturum açın.
2. **Hizmetler**' e gidin, ardından **rdadgent** ve **Uzak Masaüstü Aracısı yükleyici** süreçlerini durdurun.
3. Ardından, aracıyı ve önyükleme yükleyicisi msu ' ni bulun. Bunlar, **C:\deployagent** klasöründe konumlanır veya onu yüklediğinizde kaydettiğiniz konuma kaydedilir.
4. Aşağıdaki dosyaları bulun ve kaldırın:
     
     - Microsoft. RDInfra. Rdadgent. Installer-x64-verx. x. x
     - Microsoft. RDInfra. Rdadgentönyükleme yükleyicisi. Installer-x64

   Bu dosyaları kaldırmak için, her bir dosya adına sağ tıklayın ve ardından **Kaldır**' ı seçin.
5. İsteğe bağlı olarak, aşağıdaki kayıt defteri ayarlarını da kaldırabilirsiniz:
     
     - Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDInfraAgent
     - Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDAgentBootLoader

6. Bu öğeleri kaldırdıktan sonra, eski ana bilgisayar havuzuyla tüm ilişkilendirmeleri kaldırması gerekir. Bu Konağı hizmete yeniden kaydettirmek isterseniz, [sanal makineleri Windows sanal masaüstü ana bilgisayar havuzuna kaydetme](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)bölümündeki yönergeleri izleyin.


## <a name="next-steps"></a>Sonraki adımlar

Artık bir konak havuzu oluşturduğunuza göre, bunu RemoteApps ile doldurabilirsiniz. Windows sanal masaüstündeki uygulamaları yönetme hakkında daha fazla bilgi edinmek için bkz. uygulama gruplarını yönetme öğreticisi.

> [!div class="nextstepaction"]
> [Uygulama gruplarını yönetme öğreticisi](./manage-app-groups.md)
