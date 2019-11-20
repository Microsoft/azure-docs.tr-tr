---
title: PowerShell işlevlerini kullanarak uzak şirket içi kaynakları yönetme
description: Bir PowerShell işlev uygulamasını şirket içi kaynaklara bağlamak için Azure Relay Karma Bağlantılar yapılandırmayı öğrenin. Bu, daha sonra şirket içi kaynağı uzaktan yönetmek için kullanılabilir.
author: eamono
manager: gailey777
ms.service: azure-functions
ms.topic: conceptual
ms.date: 9/5/2019
ms.author: eamono
ms.openlocfilehash: 2a3cf79883d79eb82c361731eb6a632c2df3c9be
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299417"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Azure Işlevleri 'nde PowerShell ile karma ortamları yönetme ve App Service Karma Bağlantılar

Azure App Service Karma Bağlantılar özelliği, diğer ağlardaki kaynaklara erişim sağlar. [Karma bağlantılar](../app-service/app-service-hybrid-connections.md) belgelerinde bu yetenek hakkında daha fazla bilgi edinebilirsiniz. Bu makalede, bir şirket içi sunucuyu hedefleyen PowerShell işlevlerini çalıştırmak için bu özelliğin nasıl kullanılacağı açıklanır. Bu sunucu daha sonra, şirket içi ortamdaki tüm kaynakları bir Azure PowerShell işlevinden yönetmek için kullanılabilir.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>PowerShell uzaktan iletişim için bir şirket içi sunucu yapılandırma

Aşağıdaki betik PowerShell uzaktan iletişimini mümkün kılar ve yeni bir güvenlik duvarı kuralı ve WinRM HTTPS dinleyicisi oluşturur. Sınama amacıyla, otomatik olarak imzalanan bir sertifika kullanılır. Bir üretim ortamında, imzalı bir sertifika kullanmanızı öneririz.

```powershell
# For configuration of WinRM, see
# https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management.

# Enable PowerShell remoting.
Enable-PSRemoting -Force

# Create firewall rule for WinRM. The default HTTPS port is 5986.
New-NetFirewallRule -Name "WinRM HTTPS" `
                    -DisplayName "WinRM HTTPS" `
                    -Enabled True `
                    -Profile "Any" `
                    -Action "Allow" `
                    -Direction "Inbound" `
                    -LocalPort 5986 `
                    -Protocol "TCP"

# Create new self-signed-certificate to be used by WinRM.
$Thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME  -CertStoreLocation Cert:\LocalMachine\My).Thumbprint

# Create WinRM HTTPS listener.
$Cmd = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:COMPUTERNAME ""; CertificateThumbprint=""$Thumbprint""}"
cmd.exe /C $Cmd
```

## <a name="create-a-powershell-function-app-in-the-portal"></a>Portalda bir PowerShell işlev uygulaması oluşturma

App Service Karma Bağlantılar özelliği yalnızca temel, standart ve yalıtılmış fiyatlandırma planlarında kullanılabilir. PowerShell ile işlev uygulaması oluşturduğunuzda, bu planlardan birini oluşturun veya seçin.

1. [Azure Portal](https://portal.azure.com)sol taraftaki menüden **+ kaynak oluştur** ' u seçin ve ardından **işlev uygulaması**' nı seçin.

1. **Barındırma planı**için **App Service plan**' ı seçin ve **App Service plan/konum**' u seçin.

1. **Yeni oluştur**' u seçin, **App Service bir plan** adı yazın, kendinize yakın bir [bölgede](https://azure.microsoft.com/regions/) bir konum seçin ya da Işlevlerinizin erişebileceği diğer hizmetlere yakın bir **konum** seçin ve ardından **fiyatlandırma katmanı**' nı seçin.

1. S1 standart planını seçin ve ardından **Uygula**' yı seçin.

1. Planı oluşturmak için **Tamam** ' ı seçin ve ardından tabloda belirtilen geri kalan **işlev uygulaması** ayarlarını aşağıdaki ekran görüntüsünden hemen sonra yapılandırın:

    ![PowerShell Çekirdek işlev uygulaması](./media/functions-hybrid-powershell/create-function-powershell-app.png)  

    | Ayar      | Önerilen değer  | Açıklama                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Uygulama adı** | Genel olarak benzersiz bir ad | Yeni işlev uygulamanızı tanımlayan ad. Geçerli karakterler: `a-z`, `0-9`, ve `-`.  | 
    | **Abonelik** | Aboneliğiniz | Bu yeni işlev uygulamasının oluşturulduğu abonelik. |
    | **Kaynak Grubu** |  myResourceGroup | İşlev uygulamanızın oluşturulacağı yeni kaynak grubunun adı. Önerilen değeri de kullanabilirsiniz. |
    | **OS** | Tercih edilen işletim sistemi | Windows ' u seçin. |
    | **Çalışma zamanı yığını** | Tercih edilen dil | PowerShell Core ' u seçin. |
    | **Depolama** |  Genel olarak benzersiz bir ad |  İşlev uygulamanız tarafından kullanılan bir depolama hesabı oluşturun. Depolama hesabı adları 3 ile 24 karakter uzunluğunda olmalı ve yalnızca rakamlar ve küçük harfler içerebilir. Var olan bir hesabı da kullanabilirsiniz.
    | **Application Insights** | Varsayılan | En yakın desteklenen bölgede aynı *uygulama adının* Application Insights kaynağını oluşturur. Bu ayarı genişleterek, **Yeni kaynak adını** değiştirebilir veya verilerinizi depolamak istediğiniz [Azure Coğrafya](https://azure.microsoft.com/global-infrastructure/geographies/) bölgesinde farklı bir **konum** seçebilirsiniz. |

1. Ayarlarınız doğrulandıktan sonra **Oluştur**' u seçin.

1. Portalın sağ üst köşesindeki **bildirim** simgesini seçin ve "dağıtım başarılı" iletisini bekleyin.

1. Yeni işlev uygulamanızı görüntülemek için **Kaynağa git**’i seçin. **Panoya sabitle ' yi**de seçebilirsiniz. Sabitleme, panonuzdan bu işlev uygulama kaynağına döndürülmesini kolaylaştırır.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>İşlev uygulaması için karma bağlantı oluşturma

Karma bağlantılar, işlev uygulamasının ağ bölümünde yapılandırılır:

1. İşlev uygulamasındaki **platform özellikleri** sekmesini seçin ve ardından **ağ**' ı seçin. 
   ![Platform ağı için uygulamaya genel bakış](./media/functions-hybrid-powershell/app-overview-platform-networking.png)  
1. **Karma bağlantı uç noktalarınızı yapılandırın**' ı seçin.
   ![Ağ](./media/functions-hybrid-powershell/select-network-feature.png)  
1. **Karma bağlantı ekle**' yi seçin.
   ![Karma bağlantı](./media/functions-hybrid-powershell/hybrid-connection-overview.png)  
1. Aşağıdaki ekran görüntüsünden hemen sonra gösterildiği gibi karma bağlantıyla ilgili bilgileri girin. Uzak komutları çalıştırırken sunucu daha sonra hatırlanmasını kolaylaştırmak için **uç nokta ana bilgisayar** ayarını şirket içi sunucunun ana bilgisayar adıyla eşleştirme seçeneğiniz vardır. Bağlantı noktası, sunucuda daha önce tanımlanan varsayılan Windows Uzaktan Yönetim hizmeti bağlantı noktasıyla eşleşir.
  ![Karma bağlantı ekle](./media/functions-hybrid-powershell/add-hybrid-connection.png)  

    **Karma bağlantı adı**: ContosoHybridOnPremisesServer
    
    **Uç nokta ana bilgisayarı**: finance1
    
    **Uç nokta bağlantı noktası**: 5986
    
    **ServiceBus ad alanı**: Yeni Oluştur
    
    **Konum**: Kullanılabilir bir konum seçin
    
    **Ad**: contosopowershellhibrit

5. Karma bağlantıyı oluşturmak için **Tamam ' ı** seçin.

## <a name="download-and-install-the-hybrid-connection"></a>Karma bağlantıyı indirme ve yükleme

1. . Msi dosyasını bilgisayarınıza yerel olarak kaydetmek için **bağlantı yöneticisini indir** ' i seçin.
![Yükleyiciyi indir](./media/functions-hybrid-powershell/download-hybrid-connection-installer.png)  
1. . Msi dosyasını yerel bilgisayarınızdan şirket içi sunucuya kopyalayın.
1. Hizmeti şirket içi sunucuya yüklemek için Karma Bağlantı Yöneticisi yükleyicisini çalıştırın.
![Karma bağlantıyı yükler](./media/functions-hybrid-powershell/hybrid-installation.png)  
1. Portalda karma bağlantıyı açın ve ağ geçidi bağlantı dizesini panoya kopyalayın.
![Karma bağlantı dizesini Kopyala](./media/functions-hybrid-powershell/copy-hybrid-connection.png)  
1. Şirket içi sunucuda Karma Bağlantı Yöneticisi Kullanıcı arabirimini açın.
![Karma bağlantı kullanıcı arabirimini açın](./media/functions-hybrid-powershell/hybrid-connection-ui.png)  
1. **El Ile gir** düğmesini seçin ve bağlantı dizesini panodan yapıştırın.
![Bağlantıyı Yapıştır](./media/functions-hybrid-powershell/enter-manual-connection.png)  
1. Karma Bağlantı Yöneticisi, bağlı olarak gösterilmezse PowerShell 'den yeniden başlatın.
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Yönetici hesabının parolası için bir uygulama ayarı oluşturma

1. İşlev uygulamasındaki **platform özellikleri** sekmesini seçin.
1. **Genel ayarlar**altında **yapılandırma**' yı seçin.
![Platform yapılandırması seçin](./media/functions-hybrid-powershell/select-configuration.png)  
1. **Yeni uygulama ayarını** genişleterek parola için yeni bir ayar oluşturun.
1. Bu ayarı _Contosouserpassword_olarak adlandırın ve parolayı girin.
1. **Tamam** ' ı ve ardından Kaydet ' i seçerek parolayı işlev uygulamasında depolayın.
![Parola için uygulama ayarı Ekle](./media/functions-hybrid-powershell/add-appsetting-password.png)  

## <a name="create-a-function-http-trigger-to-test"></a>Test etmek için bir işlev http tetikleyicisi oluşturun

1. İşlev uygulamasından yeni bir HTTP tetikleyici işlevi oluşturun.
![Yeni HTTP tetikleyicisi oluştur](./media/functions-hybrid-powershell/create-http-trigger-function.png)  
1. Şablondaki PowerShell kodunu aşağıdaki kodla değiştirin:

    ```powershell
    # Input bindings are passed in via param block.
    param($Request, $TriggerMetadata)
    
    # Write to the Azure Functions log stream.
    Write-Output "PowerShell HTTP trigger function processed a request."
    
    # Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
    $UserName = "ContosoUser"
    $securedPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
    $Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)
    
    # This is the name of the hybrid connection Endpoint.
    $HybridEndpoint = "finance1"
    
    $Script = {
        Param(
            [Parameter(Mandatory=$True)]
            [String] $Service
        )
        Get-Service $Service
    }
    
    Write-Output "Scenario 1: Running command via Invoke-Command"
    Invoke-Command -ComputerName $HybridEndpoint `
                   -Credential $Credential `
                   -Port 5986 `
                   -UseSSL `
                   -ScriptBlock $Script `
                   -ArgumentList "*" `
                   -SessionOption (New-PSSessionOption -SkipCACheck)
    ```

3. İşlevi test etmek için **Kaydet** ve **Çalıştır** ' ı seçin.
![İşlev uygulamasını test etme](./media/functions-hybrid-powershell/test-function-hybrid.png)  

## <a name="managing-other-systems-on-premises"></a>Şirket içi diğer sistemleri yönetme

Yerel ortamdaki diğer sunuculara ve yönetim sistemlerine bağlanmak için bağlı şirket içi sunucuyu kullanabilirsiniz. Bu, PowerShell işlevlerinizi kullanarak Azure 'dan veri merkezi işlemlerinizi yönetmenizi sağlar. Aşağıdaki betik, belirtilen kimlik bilgileri altında çalışan bir PowerShell yapılandırma oturumu kaydeder. Bu kimlik bilgileri, uzak sunuculardaki bir yönetici için olmalıdır. Daha sonra bu yapılandırmayı yerel sunucu veya veri merkezindeki diğer uç noktalara erişmek için kullanabilirsiniz.

```powershell
# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
$UserName = "ContosoUser"
$SecuredPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
$Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)

# This is the name of the hybrid connection Endpoint.
$HybridEndpoint = "finance1"

# The remote server that will be connected to run remote PowerShell commands on
$RemoteServer = "finance2".

Write-Output "Use hybrid connection server as a jump box to connect to a remote machine"

# We are registering an endpoint that runs under credentials ($Credential) that has access to the remote server.
$SessionName = "HybridSession"
$ScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $SessionName)

    if (-not (Get-PSSessionConfiguration -Name $SessionName -ErrorAction SilentlyContinue))
    {
        Register-PSSessionConfiguration -Name $SessionName -RunAsCredential $Using:Credential
    }
}

Write-Output "Registering session on hybrid connection jumpbox"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $ScriptCommand `
               -ArgumentList $SessionName `
               -SessionOption (New-PSSessionOption -SkipCACheck)

# Script to run on the jump box to run against the second machine.
$RemoteScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $ComputerName)
        # Write out the hostname of the hybrid connection server.
        hostname
        # Write out the hostname of the remote server.
        Invoke-Command -ComputerName $ComputerName -Credential $Using:Credential -ScriptBlock {hostname} `
                        -UseSSL -Port 5986 -SessionOption (New-PSSessionOption -SkipCACheck)
}

Write-Output "Running command against remote machine via jumpbox by connecting to the PowerShell configuration session"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $RemoteScriptCommand `
               -ArgumentList $RemoteServer `
               -SessionOption (New-PSSessionOption -SkipCACheck) `
               -ConfigurationName $SessionName
```

Bu betikteki aşağıdaki değişkenleri ortamınızdaki ilgili değerlerle değiştirin:
* $HybridEndpoint
* $RemoteServer

Yukarıdaki iki senaryoda, Azure Işlevleri ve Karma Bağlantılar PowerShell kullanarak şirket içi ortamlarınızı bağlanabilir ve yönetebilirsiniz. İşlevlerde [karma bağlantılar](../app-service/app-service-hybrid-connections.md) ve [PowerShell](./functions-reference-powershell.md)hakkında daha fazla bilgi edinmenize önerilir.

Azure [sanal ağlarını](./functions-create-vnet.md) Azure işlevleri aracılığıyla şirket içi ortamınıza bağlanmak için de kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"] 
> [PowerShell işlevleriyle çalışma hakkında daha fazla bilgi edinin](functions-reference-powershell.md)
