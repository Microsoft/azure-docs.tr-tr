---
title: Hizmet bağlantısı Windows sanal masaüstü-Azure sorunlarını giderme
description: Windows sanal masaüstü kiracı ortamında hizmet bağlantıları ayarlanırken sorunları çözme.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 10/15/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 68468276546eade7c068f1aa48d3ac65fc8aadaa
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445389"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows sanal masaüstü hizmeti bağlantıları

>[!IMPORTANT]
>Bu içerik Azure Resource Manager Windows sanal masaüstü nesneleri ile Windows sanal masaüstü için geçerlidir. Azure Resource Manager nesneleri olmadan Windows sanal masaüstü (klasik) kullanıyorsanız, [Bu makaleye](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md)bakın.

Windows sanal masaüstü istemci bağlantılarıyla ilgili sorunları gidermek için bu makaleyi kullanın.

## <a name="provide-feedback"></a>Geribildirim gönderme

Windows sanal masaüstü [Teknik topluluğu](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)'nda ürün ekibi ve diğer etkin topluluk üyeleriyle geri bildirimde bulunun ve Windows Sanal Masaüstü hizmetini tartışabilirsiniz.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Kullanıcı bağlanıyor, ancak hiçbir şey görüntülenmiyor (akış yok)

Kullanıcı, uzak masaüstü istemcileri başlatabilir ve kimlik doğrulaması yapabilir, ancak kullanıcı Web bulma akışında herhangi bir simge görmez.

1. Bu komut satırını kullanarak, sorunları raporlayan kullanıcının uygulama gruplarına atandığını doğrulayın:

     ```powershell
     Get-AzRoleAssignment -SignInName <userupn>
     ```

2. Kullanıcının doğru kimlik bilgileriyle oturum açmasını onaylayın.

3. Web istemcisi kullanılıyorsa, önbelleğe alınmış kimlik bilgileri sorunları olduğunu doğrulayın.

4. Kullanıcı bir Azure Active Directory (AD) Kullanıcı grubunun parçasıysa, Kullanıcı grubunun bir dağıtım grubu yerine bir güvenlik grubu olduğundan emin olun. Windows sanal masaüstü, Azure AD Dağıtım gruplarını desteklemez.

## <a name="user-loses-existing-feed-and-no-remote-resource-is-displayed-no-feed"></a>Kullanıcı var olan akışı kaybeder ve hiçbir uzak kaynak gösterilmez (akış yok)

Bu hata genellikle, bir Kullanıcı aboneliğini bir Azure AD kiracısından diğerine taşıdıktan sonra görüntülenir. Sonuç olarak, bunlar hala eski Azure AD kiracısına bağlı olduğundan hizmet Kullanıcı atamalarının izini kaybeder.

Bunu çözmek için tüm yapmanız gereken, kullanıcıları uygulama gruplarına yeniden atayabilir.

Bu durum, bir CSP sağlayıcısı aboneliği oluşturup müşteriye aktarıldığında de gerçekleşebilir. Bu sorunu çözmek için kaynak sağlayıcıyı yeniden kaydedin.

1. Azure portalında oturum açın.
2. **Aboneliğe** gidin ve aboneliğinizi seçin.
3. Sayfanın sol tarafındaki menüde **kaynak sağlayıcısı**' nı seçin.
4. **Microsoft. DesktopVirtualization** bulun ve seçin, sonra **yeniden kaydet**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

- Windows sanal masaüstü ve yükseltme izlemelerinin sorunlarını giderme hakkında genel bilgi için bkz. [sorun giderme genel bakış, geri bildirim ve destek](troubleshoot-set-up-overview.md).
- Windows sanal masaüstü ortamında bir Windows sanal masaüstü ortamı ve konak havuzu oluştururken oluşan sorunları gidermek için, bkz. [ortam ve konak havuzu oluşturma](troubleshoot-set-up-issues.md).
- Windows sanal masaüstündeki bir sanal makineyi (VM) yapılandırırken oluşan sorunları gidermek için bkz. [oturum ana bilgisayarı sanal makine yapılandırması](troubleshoot-vm-configuration.md).
- Windows sanal masaüstü Aracısı veya oturum bağlantısıyla ilgili sorunları gidermek için bkz. [Genel Windows sanal masaüstü Aracısı sorunlarını giderme](troubleshoot-agent.md).
- Windows sanal masaüstü ile PowerShell kullanırken karşılaşılan sorunları gidermek için bkz. [Windows sanal masaüstü PowerShell](troubleshoot-powershell.md).
- Sorun giderme öğreticisini öğrenmek için bkz. [öğretici: Kaynak Yöneticisi şablonu dağıtımlarının sorunlarını giderme](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
