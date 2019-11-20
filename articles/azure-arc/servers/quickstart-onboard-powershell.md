---
title: Hızlı başlangıç-sunucular için Azure Arc kullanarak makineleri Azure 'a bağlama-PowerShell
description: Bu hızlı başlangıçta, PowerShell kullanarak sunucular için Azure Arc kullanarak makineleri Azure 'a bağlamayı öğrenirsiniz.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: Azure Otomasyonu, DSC, PowerShell, istenen durum yapılandırması, güncelleştirme yönetimi, değişiklik izleme, envanter, runbook 'lar, Python, grafik, karma, yerleşik
ms.date: 11/04/2019
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: ddade9472517d080d01b04c853db9dd1848fe0f3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73668458"
---
# <a name="quickstart-connect-machines-to-azure-using-azure-arc-for-servers---powershell"></a>Hızlı başlangıç: sunucular için Azure Arc kullanarak makineleri Azure 'a bağlama-PowerShell

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Ön koşullar

Desteklenen istemcileri ve [sunucu Için Azure Arc genel bakış](overview.md)' da gereken ağ yapılandırmasını gözden geçirin.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Ölçekte ekleme için bir hizmet sorumlusu oluşturma

Hizmet sorumlusu, yalnızca makineleri Azure 'a bağlamak için gereken en düşük izni verilen özel sınırlı bir yönetim kimliğidir. Bu, Kiracı Yöneticisi gibi daha güçlü bir hesap kullanmaktan daha güvenlidir. Hizmet sorumlusu yalnızca ekleme sırasında kullanılır. İstediğiniz sunuculara bağlandıktan sonra hizmet sorumlusunu güvenle silebilirsiniz.

> [!NOTE]
> Bu adım önerilir, ancak gerekli değildir.

### <a name="steps-to-create-the-service-principal"></a>Hizmet sorumlusu oluşturma adımları

Bu örnekte, bir hizmet asıl adı (SPN) oluşturmak için [Azure PowerShell](/powershell/azure/install-az-ps) kullanacağız. Alternatif olarak, bu görev için [Azure Portal kullanarak hizmet sorumlusu oluşturma](../../active-directory/develop/howto-create-service-principal-portal.md) altında listelenen adımları izleyebilirsiniz.

`Azure Connected Machine Onboarding` rolü yalnızca ekleme için gereken izinleri içerir. Kapsamının bir kaynak grubunu veya aboneliği kapsamasını sağlamak için bir SPN iznini tanımlayabilirsiniz.

[`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) cmdlet 'inin çıkışını depolamanız gerekir veya sonraki bir adımda kullanılacak parolayı alamazsınız.

```azurepowershell-interactive
$sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
$sp
```

```output
Secret                : System.Security.SecureString
ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
ObjectType            : ServicePrincipal
DisplayName           : Hybrid-RP
Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
Type                  :
```

Şimdi, PowerShell 'i kullanarak parolayı alın.

```azurepowershell-interactive
$credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
$credential.GetNetworkCredential().password
```

Çıkışta, **parolayı** ve **ApplicationId** 'yi (önceki adımdan) kopyalayın ve daha sonra sunucu yapılandırma aracınız için gizli dizi gibi güvenli bir yerde depolayın. SPN Parolanızı unutur veya kaybederseniz, [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) cmdlet 'ini kullanarak sıfırlayabilirsiniz.

Aracıyı Install ekleme betiği:

* **ApplicationId** özelliği, Install aracısında kullanılan `--service-principal-id` parametresi için kullanılır
* **Password** özelliği, install aracısındaki `--service-principal-secret` parametresi için kullanılır.

## <a name="manually-install-the-agent-and-connect-to-azure"></a>Aracıyı el ile yüklemek ve Azure 'a bağlamak

Aşağıdaki kılavuz, makinede oturum açarak ve adımları gerçekleştirerek bir makineyi Azure 'a bağlamanıza olanak tanır. Ayrıca [portaldan](quickstart-onboard-portal.md)Azure 'a makineler de bağlayabilirsiniz.

### <a name="download-and-install-the-agent"></a>Aracıyı indirme ve yükleme

Aracı paketini yüklemek için hedef sunucuda kök veya yerel yönetici erişimi gerekir, ancak Azure erişimi yoktur.

#### <a name="linux"></a>Linux

**Linux** sunucuları için, aracı dağıtım için tercih edilen paket biçimi kullanılarak [Microsoft 'un paket deposu](https://packages.microsoft.com) aracılığıyla dağıtılır (. RPM veya. DEB).

> [!NOTE]
> Genel Önizleme sırasında, Ubuntu 16,04 veya 18,04 için uygun olan yalnızca bir paket yayımlanmıştır.

<!-- What about this aks? -->
En basit seçenek, paket deposunu kaydetmek ve sonra dağıtımın paket yöneticisini kullanarak paketi yüklemektir.
[https://aka.ms/azcmagent](https://aka.ms/azcmagent) konumunda bulunan Bash betiği aşağıdaki eylemleri gerçekleştirir:

1. `packages.microsoft.com`'den indirilecek konak makineyi yapılandırır.
2. Karma kaynak sağlayıcısı paketini kurar.
3. İsteğe bağlı olarak, `--proxy`belirtirseniz aracıyı proxy işlemi için yapılandırır.

Betik Ayrıca, desteklenen ve desteklenmeyen dağıtımlar için denetimler de içerir ve yüklenmek üzere gerekli izinleri tespit eder.

Aşağıdaki örnek, aracı indirir ve koşullu denetimlerden herhangi biri olmadan yükler.

```bash
# Download the installation package
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. Omit the '--proxy "{proxy-url}"' parameters if proxy is not needed
bash ~/Install_linux_azcmagent.sh--proxy "{proxy-url}"
```

> [!NOTE]
> Microsoft 'un paket deposuna başvurmayı tercih ediyorsanız, paket dosyasını buradan iç deponuza kopyalayabilirsiniz.

#### <a name="windows"></a>Windows

**Windows**için, aracı bir Windows Installer (`.MSI`) dosyasında paketlenir ve [https://download.microsoft.com](https://download.microsoft.com)üzerinde barındırılan [https://aka.ms/AzureConnectedMachineAgent](https://aka.ms/AzureConnectedMachineAgent)indirilebilir.

```powershell
# Download the package
Invoke-WebRequest -Uri https://aka.ms/AzureConnectedMachineAgent -OutFile AzureConnectedMachineAgent.msi

# Install the package
msiexec /i AzureConnectedMachineAgent.msi /l*v installationlog.txt /qn | Out-String
```

> [!NOTE]
> Linux 'ta, yükleme betiğini yeniden çalıştırmak en son sürüme otomatik olarak yükseltilir. Windows 'ta, yükleyiciyi yeniden çalıştırmadan önce "Azure bağlı makine aracısını" kaldırmanız gerekir.

### <a name="connecting-to-azure"></a>Azure 'a bağlanılıyor

Yüklendikten sonra, `azcmagent.exe`adlı bir komut satırı aracını kullanarak aracıyı yönetebilir ve yapılandırabilirsiniz. Aracı, Linux üzerinde `/opt/azcmagent/bin` ve Windows üzerinde `$env:programfiles\AzureConnectedMachineAgent` bulunur.

Windows 'ta, PowerShell 'i bir hedef düğümde yönetici olarak açın ve şunu çalıştırın:

```powershell
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{your-spn-appid}" `
  --service-principal-secret "{your-spn-password}" `
  --resource-group "{your-resource-group-name}" `
  --tenant-id "{your-tenant-id}" `
  --location "{location-of-your-resource-group}" `
  --subscription-id "{your-subscription-id}"
```

Linux 'ta bir kabuğu açın ve şunu çalıştırın

<!-- Same command for linux?-->
```bash
azcmagent connect \
  --service-principal-id "{your-spn-appid}" \
  --service-principal-secret "{your-spn-password}" \
  --resource-group "{your-resource-group-name}" \
  --tenant-id "{your-tenant-id}" \
  --location "{location-of-your-resource-group}" \
  --subscription-id "{your-subscription-id}"
```

Parametreler:

* `tenant-id`: kiracı GUID 'SI. Azure portal, **Azure Active directory** -> **PROPERTIES** -> **dizin kimliği**' ni seçerek bulabilirsiniz.
* `subscription-id`: Azure 'da, makinenizi bağlamak istediğiniz aboneliğin GUID 'SI.
* `resource-group`: makinenizin bağlanmasını istediğiniz kaynak grubu.
* `location`: bkz. [Azure bölgeleri ve konumları](https://azure.microsoft.com/global-infrastructure/regions/). Bu konum, kaynak grubunun konumu olarak aynı veya farklı olabilir. Genel önizleme için, hizmet **WestUS2** ve **Batı Avrupa**desteklenir.
* `resource-name`: (*Isteğe bağlı*) Şirket Içi makinenizin Azure Kaynak temsili için kullanılır. Bu değeri belirtmezseniz makine ana bilgisayar adı kullanılır.

[Azcmagent başvurusunda](azcmagent-reference.md)' azcmagent ' aracında daha fazla bilgi edinebilirsiniz.
<!-- Isn't this still needed to view machines? -->

Başarılı bir şekilde tamamlandıktan sonra makineniz Azure 'a bağlanır. [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal)ziyaret ederek makinenizi Azure Portal görüntüleyebilirsiniz.

![Başarılı ekleme](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

### <a name="proxy-server-configuration"></a>Proxy sunucusu yapılandırması

#### <a name="linux"></a>Linux

<!-- New proxy name? -->

**Linux**için, sunucu bir ara sunucu gerektiriyorsa şunlardan birini yapabilirsiniz:

* `install_linux_hybrid_agent.sh` betiği, yukarıdaki [aracıyı Install](#download-and-install-the-agent) bölümünde `--proxy`ile çalıştırın.
* Aracıyı zaten yüklediyseniz, proxy 'yi yapılandıran ve aracıyı yeniden başlatan `/opt/azcmagent/bin/hybridrp_proxy add https://{proxy-url}:{proxy-port}`komutunu yürütün.

#### <a name="windows"></a>Windows

**Windows**için sunucu, internet kaynaklarına erişim için proxy sunucusu gerektiriyorsa, proxy sunucusu ortam değişkenini ayarlamak için aşağıdaki komutu çalıştırmalısınız. Bu, aracının internet erişimi için proxy sunucu kullanmasına izin verir.

```powershell
# If a proxy server is needed, execute these commands with actual proxy URL
[Environment]::SetEnvironmentVariable("https_proxy", "{https:\\proxy-url:proxyport}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# The agent service needs to be restarted after the proxy environment variable is set in order for the changes to take effect.
Restart-Service -Name himds
```

> [!NOTE]
> Kimliği doğrulanmış proxy 'ler genel önizleme için desteklenmez.

## <a name="clean-up"></a>Temizleme

Bir makinenin sunucular için Azure Arc bağlantısını kesmek için iki adım gerçekleştirmeniz gerekir.

1. [Portalda](https://aka.ms/hybridmachineportal)makineyi seçin, üç noktaya (`...`) tıklayın ve **Sil**' i seçin.
1. Aracıyı makineden kaldırın.

   Windows 'da Aracıyı kaldırmak için "uygulamalar & özellikleri" denetim masasını kullanabilirsiniz.
  
  ![Uygulamalar & özellikleri](./media/quickstart-onboard/apps-and-features.png)

   Kaldırma işlemini yapmak isterseniz, **PackageID** 'yi alan ve `msiexec /X`kullanarak aracıyı kaldırabilen aşağıdaki örneği kullanabilirsiniz.

   `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` kayıt defteri anahtarını bulun ve **PackageID**' i bulun. Ardından `msiexec`kullanarak aracıyı kaldırabilirsiniz.

   Aşağıdaki örnekte aracının kaldırılması gösterilmektedir.

   ```powershell
   Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
   Get-ItemProperty | `
   Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
   ForEach-Object {MsiExec.exe /Quiet /X "$($_.PsChildName)"}
   ```

   Linux 'ta Aracıyı kaldırmak için aşağıdaki komutu yürütün.

   ```bash
   sudo apt purge hybridagent
   ```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bağlı makinelere Ilke atama](../../governance/policy/assign-policy-portal.md)
