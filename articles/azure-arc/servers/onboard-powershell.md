---
title: PowerShell kullanarak karma makineleri Azure 'a bağlama
description: Bu makalede, Azure Arc etkin sunucularını kullanarak aracıyı yüklemeyi ve bir makineyi Azure 'a bağlamayı öğreneceksiniz. Bunu PowerShell ile yapabilirsiniz.
ms.date: 10/28/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d6963a53ac14c9d6727a8d53e781bc8b8389b76e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831627"
---
# <a name="connect-hybrid-machines-to-azure-by-using-powershell"></a>PowerShell kullanarak karma makineleri Azure 'a bağlama

Azure Arc ile etkinleştirilen sunucular için el ile ortamınızdaki bir veya daha fazla Windows veya Linux makinesi için bunları etkinleştirmek üzere el ile adımları gerçekleştirebilirsiniz. Alternatif olarak, [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine) PowerShell cmdlet 'Ini kullanarak bağlı makine aracısını indirebilir, aracıyı yükleyebilir ve makineyi Azure Arc 'a kaydedebilirsiniz. Cmdlet 'i Microsoft Yükleme Merkezi 'nden Windows Agent paketini (Windows Installer) ve Linux Aracısı paketini Microsoft paket deposundan indirir.

Bu yöntem, aracıyı yüklemek ve yapılandırmak için makinede yönetici izinlerine sahip olmanızı gerektirir. Linux 'ta, kök hesabı kullanarak ve Windows 'ta, yerel Yöneticiler grubunun üyesi olursunuz. [PowerShell uzaktan](/powershell/scripting/learn/ps101/08-powershell-remoting)iletişimini kullanarak bir Windows Server üzerinde bu işlemi etkileşimli veya uzaktan tamamlayabilirsiniz.

Başlamadan önce [önkoşulları](agent-overview.md#prerequisites) gözden geçirin ve aboneliğinizin ve kaynaklarınızın gereksinimleri karşıladığından emin olun. Desteklenen bölgeler ve diğer ilgili konular hakkında daha fazla bilgi için bkz. [desteklenen Azure bölgeleri](overview.md#supported-regions).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

- Azure PowerShell olan bir makine. Yönergeler için bkz. [Azure PowerShell yükleyip yapılandırma](/powershell/azure/).

Azure Arc etkin sunucuları tarafından yönetilen karma sunucularınızdaki VM uzantılarını yönetmek için PowerShell 'i kullanırsınız. PowerShell 'i kullanmadan önce modülünü yükledikten sonra `Az.ConnectedMachine` . Azure Arc ile etkin sunucunuzda aşağıdaki komutu çalıştırın:

```powershell
Install-Module -Name Az.ConnectedMachine
```

Yükleme tamamlandığında aşağıdaki iletiyi görürsünüz:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>Aracıyı yükleyip Azure 'a bağlanın

1. Yükseltilmiş ayrıcalıklarla bir PowerShell konsolu açın.

2. Komutunu çalıştırarak Azure 'da oturum açın `Connect-AzAccount` .

3. Bağlı makine aracısını yüklemek için,, `Connect-AzConnectedMachine` `-Name` `-ResourceGroupName` ve `-Location` parametreleriyle kullanın. `-SubscriptionId`Oturum açtıktan sonra oluşturulan Azure bağlamının bir sonucu olarak varsayılan aboneliği geçersiz kılmak için parametresini kullanın. Aşağıdaki komutlardan birini çalıştırın:

    * Bağlı makine aracısını doğrudan Azure ile iletişim kurabilen hedef makineye yüklemek için şunu çalıştırın:

        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region>
        ```
    
    * Bağlı makine aracısını bir ara sunucu üzerinden iletişim kuran hedef makineye yüklemek için şunu çalıştırın:
        
        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -Proxy http://<proxyURL>:<proxyport>
        ```

Kurulum tamamlandıktan sonra aracı başlatılamazsa, ayrıntılı hata bilgileri için günlüklere bakın. Windows üzerinde şu dosyayı kontrol edin: *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*. Linux 'ta şu dosyayı kontrol edin: */var/seçenek/azcmagent/log/hımds.log*.

## <a name="install-and-connect-by-using-powershell-remoting"></a>PowerShell uzaktan iletişimini kullanarak yükleyip bağlama

Azure Arc ile etkinleştirilmiş sunucularla bir veya daha fazla Windows sunucusunu yapılandırma hakkında bilgi edinebilirsiniz. Uzak makinede PowerShell uzaktan iletişimini etkinleştirmeniz gerekir. Bunu yapmak için `Enable-PSRemoting` cmdlet’ini kullanın.

1. Yönetici olarak bir PowerShell konsolu açın.

2. Komutunu çalıştırarak Azure 'da oturum açın `Connect-AzAccount` .

3. Bağlı makine aracısını yüklemek için `Connect-AzConnectedMachine` `-ResourceGroupName` ve `-Location` parametreleriyle kullanın. Azure Kaynak adları otomatik olarak her sunucunun ana bilgisayar adını kullanır. `-SubscriptionId`Oturum açtıktan sonra oluşturulan Azure bağlamının bir sonucu olarak varsayılan aboneliği geçersiz kılmak için parametresini kullanın.

    * Bağlı makine aracısını doğrudan Azure ile iletişim kurabilen hedef makineye yüklemek için şu komutu çalıştırın:
    
        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```
    
    * Bağlı makine aracısını aynı anda birden fazla uzak makineye yüklemek için, her biri virgülle ayırarak uzak makine adlarının bir listesini ekleyin.

        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName1, myMachineName2, myMachineName3
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```

    Aşağıdaki örnek, tek bir makineyi hedefleyen komutun sonuçlarını gösterir:
    
    ```azurepowershell
    time="2020-08-07T13:13:25-07:00" level=info msg="Onboarding Machine. It usually takes a few minutes to complete. Sometimes it may take longer depending on network and server load status."
    time="2020-08-07T13:13:25-07:00" level=info msg="Check network connectivity to all endpoints..."
    time="2020-08-07T13:13:29-07:00" level=info msg="All endpoints are available... continue onboarding"
    time="2020-08-07T13:13:50-07:00" level=info msg="Successfully Onboarded Resource to Azure" VM Id=f65bffc7-4734-483e-b3ca-3164bfa42941
    
    Name           Location OSName   Status     ProvisioningState
    ----           -------- ------   ------     -----------------
    myMachineName  eastus   windows  Connected  Succeeded
    ```

## <a name="verify-the-connection-with-azure-arc"></a>Azure Arc ile bağlantıyı doğrulama

Aracıyı, Azure Arc etkin sunucularıyla kaydolmak üzere yükledikten ve yapılandırdıktan sonra, sunucunun başarıyla bağlandığını doğrulamak için Azure portal gidin. Makinenizde [Azure Portal](https://portal.azure.com)görüntüleyin.

![Başarılı bir sunucu bağlantısı gösteren sunucu panosunun ekran görüntüsü.](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Sonraki adımlar

* Gerekirse, [bağlı makine Aracısı sorunlarını giderme kılavuzuna](troubleshoot-agent-onboard.md)bakın.

* [Azure ilkesi](../../governance/policy/overview.md)'ni kullanarak makinenizi yönetme hakkında bilgi edinin. VM [Konuk yapılandırması](../../governance/policy/concepts/guest-configuration.md)' nı kullanabilir, makinenin beklenen Log Analytics çalışma alanına rapor ettiğini ve [VM 'lerle Azure izleyici](../../azure-monitor/vm/vminsights-enable-policy.md)ile izlemeyi etkinleştirebildiğini doğrulayabilirsiniz.

* [Log Analytics Aracısı](../../azure-monitor/agents/log-analytics-agent.md)hakkında daha fazla bilgi edinin. Windows ve Linux için Log Analytics Aracısı, işletim sistemi ve iş yükü izleme verilerini toplamak veya Azure Otomasyonu runbook 'larını veya Güncelleştirme Yönetimi gibi özellikleri kullanarak yönetmek istediğinizde gereklidir. Bu aracı, [Azure Güvenlik Merkezi](../../security-center/security-center-introduction.md)gibi diğer Azure hizmetlerini kullanmak için de gereklidir.
