---
title: Bir rol için Uzak Masaüstü 'Nü etkinleştirmek üzere PowerShell 'i kullanma
description: Uzak Masaüstü bağlantılarına izin vermek için PowerShell 'i kullanarak Azure Cloud Service uygulamanızı yapılandırma
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 5b1650edb575de8fd59ad2495dafcd628a717c02
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102610416"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic-using-powershell"></a>PowerShell kullanarak Azure Cloud Services (klasik) bir rol için Uzak Masaüstü Bağlantısı etkinleştirme

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).

> [!div class="op_single_selector"]
> * [Azure portalı](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Uzak Masaüstü, Azure 'da çalışan bir rolün masaüstüne erişmenizi sağlar. Çalışırken uygulamanızdaki sorunları gidermek ve tanılamak için Uzak Masaüstü bağlantısı kullanabilirsiniz.

Bu makalede, PowerShell kullanarak bulut hizmeti rolleriniz üzerinde Uzak Masaüstü 'nün nasıl etkinleştirileceği açıklanır. Bu makale için gereken önkoşullar için bkz. [Azure PowerShell nasıl yüklenir ve yapılandırılır](/powershell/azure/) . PowerShell, Uzak Masaüstü uzantısını kullanır, böylece uygulama dağıtıldıktan sonra Uzak Masaüstü 'Nü etkinleştirebilirsiniz.

## <a name="configure-remote-desktop-from-powershell"></a>Uzak Masaüstü 'Nü PowerShell 'den yapılandırma
[Set-Azurezerviceremotedesktopextension](/powershell/module/servicemanagement/azure.service/set-azureserviceremotedesktopextension) cmdlet 'i belirtilen roller üzerinde uzak masaüstünü veya bulut hizmeti dağıtımınızın tüm rollerini etkinleştirmenizi sağlar. Cmdlet 'i, bir PSCredential nesnesini kabul eden *Credential* parametresi aracılığıyla uzak masaüstü kullanıcısına ait Kullanıcı adını ve parolayı belirtmenizi sağlar.

PowerShell 'i etkileşimli olarak kullanıyorsanız, [Get-Credentials](/powershell/module/microsoft.powershell.security/get-credential) cmdlet 'Ini çağırarak PSCredential nesnesini kolayca ayarlayabilirsiniz.

```powershell
$remoteusercredentials = Get-Credential
```

Bu komut, uzak kullanıcı için Kullanıcı adını ve parolayı güvenli bir şekilde girmenize izin veren bir iletişim kutusu görüntüler.

PowerShell Otomasyon senaryolarında yardımcı olduğundan, **PSCredential** nesnesini Kullanıcı etkileşimi gerektirmeyen bir şekilde de ayarlayabilirsiniz. İlk olarak, güvenli bir parola ayarlamanız gerekir. Bir düz metin parolası belirterek, [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring)kullanarak güvenli bir dizeye dönüştürebilirsiniz. Ardından, bu güvenli dizeyi [ConvertFrom-SecureString](/powershell/module/microsoft.powershell.security/convertfrom-securestring)kullanarak şifrelenmiş bir standart dizeye dönüştürmeniz gerekir. Artık bu şifrelenen standart dizeyi, [set-Content](/previous-versions/windows/it-pro/windows-powershell-1.0/ee176959(v=technet.10))kullanarak bir dosyaya kaydedebilirsiniz.

Parolayı her seferinde yazmanız gerekmiyorsa, güvenli bir parola dosyası da oluşturabilirsiniz. Ayrıca, güvenli bir parola dosyası düz metin dosyasından daha iyidir. Güvenli bir parola dosyası oluşturmak için aşağıdaki PowerShell 'i kullanın:

```powershell
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> Parolayı ayarlarken [karmaşıklık gereksinimlerini](/previous-versions/windows/it-pro/windows-server-2003/cc786468(v=ws.10))karşıladığınızdan emin olun.

Güvenli parola dosyasından kimlik bilgisi nesnesini oluşturmak için, dosya içeriğini okumanız ve [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring)kullanarak bunları güvenli bir dizeye dönüştürmeniz gerekir.

[Set-Azurezerviceremotedesktopextension](/powershell/module/servicemanagement/azure.service/set-azureserviceremotedesktopextension) cmdlet 'i Ayrıca, Kullanıcı hesabının süresinin dolacağı bir **Tarih saat** belirten bir *süre sonu* parametresini kabul eder. Örneğin, hesabı geçerli tarih ve saatten birkaç gün dolacak şekilde ayarlayabilirsiniz.

Bu PowerShell örneğinde, bir bulut hizmetinde uzak masaüstü uzantısının nasıl ayarlanacağı gösterilmektedir:

```powershell
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Ayrıca, isteğe bağlı olarak uzak masaüstü 'nü etkinleştirmek istediğiniz dağıtım yuvasını ve rolleri belirtebilirsiniz. Bu parametreler belirtilmemişse, cmdlet, **Üretim** dağıtım yuvasındaki tüm rollerde uzak masaüstü 'nü mümkün bir şekilde sunar.

Uzak Masaüstü uzantısı bir dağıtım ile ilişkili. Hizmet için yeni bir dağıtım oluşturursanız, bu dağıtımda uzak masaüstünü etkinleştirmeniz gerekir. Uzak Masaüstü 'nü her zaman etkinleştirmek istiyorsanız, PowerShell betiklerini dağıtım iş akışınız ile tümleştirmeyi düşünmelisiniz.

## <a name="remote-desktop-into-a-role-instance"></a>Bir rol örneğine uzak masaüstü

[Get-AzureRemoteDesktopFile](/powershell/module/servicemanagement/azure.service/get-azureremotedesktopfile) cmdlet 'i, bulut hizmetinizin belirli bir rol örneğinde uzak masaüstü için kullanılır. Yerel olarak RDP dosyasını indirmek için *LocalPath* parametresini kullanabilirsiniz. Ya da, bulut hizmeti rolü örneğine erişmek için Uzak Masaüstü Bağlantısı iletişim kutusunu doğrudan başlatmak üzere *başlatma* parametresini kullanabilirsiniz.

```powershell
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Bir hizmette uzak masaüstü uzantısının etkinleştirilip etkinleştirilmediğini denetle

[Get-Azurezerviceremotedesktopextension](/powershell/module/servicemanagement/azure.service/get-azureremotedesktopfile) cmdlet 'i bir hizmet dağıtımında uzak masaüstü 'nün etkin veya devre dışı olduğunu gösterir. Cmdlet 'i, uzak masaüstü kullanıcısına ait Kullanıcı adını ve uzak masaüstü uzantısının etkinleştirildiği rolleri döndürür. Varsayılan olarak, bu dağıtım yuvası üzerinde gerçekleşir ve bunun yerine hazırlama yuvasını kullanmayı seçebilirsiniz.

```powershell
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Uzak Masaüstü uzantısını bir hizmetten kaldır

Bir dağıtımda Uzak Masaüstü uzantısını zaten etkinleştirdiyseniz ve uzak masaüstü ayarlarını güncelleştirmeniz gerekiyorsa, önce uzantıyı kaldırın. Ve yeni ayarlarla yeniden etkinleştirin. Örneğin, uzak kullanıcı hesabı için yeni bir parola ayarlamak istiyorsanız veya hesabın kullanım alanı dolmuşsa. Bu, uzak masaüstü uzantısının etkinleştirildiği mevcut dağıtımlarda gereklidir. Yeni dağıtımlar için, uzantıyı doğrudan uygulamanız yeterlidir.

Uzak Masaüstü uzantısını dağıtımdan kaldırmak için, [Remove-Azurezerviceremotedesktopextension](/powershell/module/servicemanagement/azure.service/remove-azureserviceremotedesktopextension) cmdlet 'ini kullanabilirsiniz. İsteğe bağlı olarak, Uzak Masaüstü uzantısını kaldırmak istediğiniz dağıtım yuvasını ve rolünü de belirtebilirsiniz.

```powershell
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> Uzantı yapılandırmasını tamamen kaldırmak için, *kaldırma* cmdlet 'Ini **uninstallconfiguration** parametresiyle çağırmanız gerekir.
>
> **Uninstallconfiguration** parametresi, hizmete uygulanan tüm uzantı yapılandırmalarını kaldırır. Her uzantı yapılandırması, hizmet yapılandırmasıyla ilişkilendirilir. *Kaldırma* cmdlet 'Ini **uninstallconfiguration** olmadan çağırmak, <mark>dağıtımı</mark> uzantı yapılandırmasından ilişkilendirir ve bu nedenle uzantıyı etkin bir şekilde kaldırır. Ancak, uzantı yapılandırması hizmetle ilişkili olarak kalır.

## <a name="additional-resources"></a>Ek kaynaklar

[Cloud Services’ı Yapılandırma](cloud-services-how-to-configure-portal.md)