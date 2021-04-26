---
title: Öğretici-şirket içi ve Azure AD ortamında temel Active Directory.
services: active-directory
description: Temel bir AD ve Azure AD ortamı oluşturmayı öğrenin.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: de59b3f26a9a4696c721cab94cd5b96bd0200e9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98614692"
---
# <a name="tutorial-basic-active-directory-environment"></a>Öğretici: temel Active Directory ortamı

Bu öğreticide, temel bir Active Directory ortamı oluşturma işlemi adım adım açıklanmaktadır. 

![Temel bir Azure A D ortamını gösteren diyagram.](media/tutorial-single-forest/diagram-2.png)

Hibrit kimlik senaryolarının çeşitli yönlerini test etmek için öğreticide oluşturduğunuz ortamı kullanabilirsiniz ve bazı öğreticiler için bir önkoşul olacaktır.  Zaten mevcut bir Active Directory ortamınız varsa bunu alternatif olarak kullanabilirsiniz.  Bu bilgiler, hiç bir şey başlatmayan bireyler için sağlanır.

Bu öğreticiden oluşur
## <a name="prerequisites"></a>Önkoşullar
Bu öğreticiyi tamamlamak için gerekli Önkoşullar aşağıda verilmiştir
- [Hyper-V](/windows-server/virtualization/hyper-v/hyper-v-technology-overview) yüklü bir bilgisayar.  Bunu bir [Windows 10](/virtualization/hyper-v-on-windows/about/supported-guest-os) veya [Windows Server 2016](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) bilgisayarında yapmanız önerilir.
- Sanal makinenin internet ile iletişim kurmasına izin veren bir [dış ağ bağdaştırıcısı](/virtualization/hyper-v-on-windows/quick-start/connect-to-network) .
- [Azure aboneliği](https://azure.microsoft.com/free)
- Windows Server 2016 kopyası
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115)

> [!NOTE]
> Bu öğretici, en hızlı zamanda öğretici ortamını oluşturabilmeniz için PowerShell betikleri kullanır.  Betiklerin her biri, betikleri başlangıcında belirtilen değişkenleri kullanır.  Bu değişkenleri ortamınızı yansıtacak şekilde değiştirebilir ve değiştirebilirsiniz.
>
>Kullanılan betikler Azure AD Connect bulut sağlama aracısını yüklemeden önce genel Active Directory ortamı oluşturur.  Bunlar tüm öğreticilerle ilgilidir.
>
> Bu öğreticide kullanılan PowerShell betiklerinin kopyaları [burada](https://github.com/billmath/tutorial-phs)GitHub 'da bulunabilir.

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma
Karma kimlik ortamınızı çalışır duruma getirmek için yapmanız gereken ilk şey, şirket içi Active Directory sunucusu olarak kullanılacak bir sanal makine oluşturmaktır.  Şunları yapın:

1. PowerShell ıSE 'yi yönetici olarak açın.
2. Aşağıdaki betiği çalıştırın.

    ```powershell
    #Declare variables
    $VMName = 'DC1'
    $Switch = 'External'
    $InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
    $Path = 'D:\VM'
    $VHDPath = 'D:\VM\DC1\DC1.vhdx'
    $VHDSize = '64424509440'

    #Create New Virtual Machine
    New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

    #Set the memory to be non-dynamic
    Set-VMMemory $VMName -DynamicMemoryEnabled $false

    #Add DVD Drive to Virtual Machine
    Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

    #Mount Installation Media
    $DVDDrive = Get-VMDvdDrive -VMName $VMName

    #Configure Virtual Machine to Boot from DVD
    Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive 
    ```

## <a name="complete-the-operating-system-deployment"></a>İşletim sistemi dağıtımını tamamlandırma
Sanal makineyi oluşturma işleminin tamamlanabilmesi için, işletim sistemi yüklemesini bitirebilmeniz gerekir.

1. Hyper-V Yöneticisi, sanal makineye çift tıklayın
2. Başlat düğmesine tıklayın.
3. "CD veya DVD 'den önyüklemek için herhangi bir tuşa basmanız" istenir. Devam edin ve bunu yapın.
4. Windows Server başlangıç ekranında dilinizi seçin ve **İleri**' ye tıklayın.
5. **Şimdi yüklensin**' e tıklayın.
6. Lisans anahtarınızı girip **İleri**' ye tıklayın.
7. Denetle * * lisans koşullarını kabul ediyorum ve **İleri**' ye tıklayın.
8. Özel ' i seçin **: yalnızca Windows 'ı yükler (Gelişmiş)**
9. **İleri** 'ye tıklayın
10. Yükleme tamamlandıktan sonra, sanal makineyi yeniden başlatın, oturum açın ve VM 'nin en güncel olduğundan emin olmak için Windows güncelleştirmelerini çalıştırın.  En son güncelleştirmeleri yükler.

## <a name="install-active-directory-prerequisites"></a>Active Directory önkoşulları yüklensin
Artık bir sanal makineniz olduğuna göre, Active Directory yüklemeden önce birkaç şey yapmanız gerekir.  Diğer bir deyişle, sanal makineyi yeniden adlandırmanız, statik bir IP adresi ve DNS bilgileri ayarlamanız ve uzak sunucu yönetim araçları 'nı yüklemeniz gerekir.   Şunları yapın:

1. PowerShell ıSE 'yi yönetici olarak açın.
2. Aşağıdaki betiği çalıştırın.

    ```powershell
    #Declare variables
    $ipaddress = "10.0.1.117" 
    $ipprefix = "24" 
    $ipgw = "10.0.1.1" 
    $ipdns = "10.0.1.117"
    $ipdns2 = "8.8.8.8" 
    $ipif = (Get-NetAdapter).ifIndex 
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $newname = "DC1"
    $addsTools = "RSAT-AD-Tools" 

    #Set static IP address
    New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

    # Set the DNS servers
    Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

    #Rename the computer 
    Rename-Computer -NewName $newname -force 

    #Install features 
    New-Item $featureLogPath -ItemType file -Force 
    Add-WindowsFeature $addsTools 
    Get-WindowsFeature | Where installed >>$featureLogPath 

    #Restart the computer 
    Restart-Computer
    ```

## <a name="create-a-windows-server-ad-environment"></a>Windows Server AD ortamı oluşturma
VM 'yi oluşturup yeniden adlandırdığınıza ve statik bir IP adresine sahip olduğunuza göre, devam edip Active Directory Domain Services yükleyip yapılandırabilirsiniz.  Şunları yapın:

1. PowerShell ıSE 'yi yönetici olarak açın.
2. Aşağıdaki betiği çalıştırın.

    ```powershell 
    #Declare variables
    $DatabasePath = "c:\windows\NTDS"
    $DomainMode = "WinThreshold"
    $DomainName = "contoso.com"
    $DomaninNetBIOSName = "CONTOSO"
    $ForestMode = "WinThreshold"
    $LogPath = "c:\windows\NTDS"
    $SysVolPath = "c:\windows\SYSVOL"
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $Password = "Pass1w0rd"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

    #Install AD DS, DNS and GPMC 
    start-job -Name addFeature -ScriptBlock { 
    Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
    Wait-Job -Name addFeature 
    Get-WindowsFeature | Where installed >>$featureLogPath

    #Create New AD Forest
    Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
    ```

## <a name="create-a-windows-server-ad-user"></a>Windows Server AD kullanıcısı oluşturma
Active Directory ortamımız olduğuna göre, bir sınama hesabı gerekir.  Bu hesap, şirket içi AD ortamımızda oluşturulur ve ardından Azure AD ile eşitlenir.  Şunları yapın:

1. PowerShell ıSE 'yi yönetici olarak açın.
2. Aşağıdaki betiği çalıştırın.

    ```powershell 
    # Filename:    4_CreateUser.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $Givenname = "Allie"
    $Surname = "McCray"
    $Displayname = "Allie McCray"
    $Name = "amccray"
    $Password = "Pass1w0rd"
    $Identity = "CN=ammccray,CN=Users,DC=contoso,DC=com"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


    #Create the user
    New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

    #Set the password to never expire
    Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
    ```


## <a name="create-an-azure-ad-tenant"></a>Azure AD kiracısı oluşturma
Şimdi, kullanıcılarınızı buluta eşitleyebilmeniz için bir Azure AD kiracısı oluşturmanız gerekir.  Yeni bir Azure AD kiracısı oluşturmak için aşağıdaki adımları uygulayın.

1. [Azure portalına](https://portal.azure.com) gidip Azure aboneliği olan bir hesapla oturum açın.
2. **Artı simgesini (+)** seçip **Azure Active Directory** terimini aratın.
3. Arama sonuçlarında **Azure Active Directory** girişini seçin.
4. **Oluştur**’u seçin.</br>
![Azure portal Azure Active Directory sayfasını gösteren ekran görüntüsü.](media/tutorial-single-forest/create-1.png)</br>
5. **Kuruluş için bir ad** ve **ilk etki alanı adı** girin. Ardından **Oluştur**’u seçin. Dizininiz oluşturulur.
6. Bu tamamlandığında, dizini yönetmek için **buraya** tıklayın bağlantısına tıklayın.

## <a name="create-a-global-administrator-in-azure-ad"></a>Azure AD 'de Genel yönetici oluşturma
Artık bir Azure AD kiracınız olduğuna göre, bir genel yönetici hesabı oluşturacaksınız.  Genel yönetici hesabını oluşturmak için aşağıdakileri yapın.

1.  **Yönet** bölümünde **Kullanıcılar**'ı seçin.</br>
!["Kullanıcılar" seçiliyken "genel bakış" menüsünü gösteren ekran görüntüsü.](media/tutorial-single-forest/administrator-1.png)</br>
2.  **Tüm kullanıcılar**'ı ve ardından **+ Yeni kullanıcı**'yı seçin.
3.  Bu kullanıcı için bir ad ve kullanıcı adı girin. Bu kullanıcı kiracınızın Genel Yöneticisi olacak. Ayrıca, **Dizin rolünü** **genel yönetici** olarak değiştirmek isteyeceksiniz. İsterseniz geçici parolayı da gösterebilirsiniz. İşiniz bittiğinde **Oluştur**'u seçin.</br>
![Oluşturma](media/tutorial-single-forest/administrator-2.png)</br>
4. Bu tamamlandığında, yeni bir Web tarayıcısı açın ve yeni genel yönetici hesabı ile geçici parolayı kullanarak myapps.microsoft.com 'de oturum açın.
5. Genel yönetici parolasını hatırlayacaksınız bir şekilde değiştirin.

## <a name="optional--additional-server-and-forest"></a>İsteğe bağlı: ek sunucu ve orman
Aşağıda ek bir sunucu ve orman oluşturma adımları sağlayan isteğe bağlı bir bölüm verilmiştir.  Bu, [bulut eşitlemesine Azure AD Connect pilot](tutorial-pilot-aadc-aadccp.md)gibi daha gelişmiş öğreticilerde kullanılabilir.

Yalnızca ek bir sunucu gerekiyorsa, **sanal makine oluşturma** adımını başlattıktan sonra durabilir ve sunucuyu yukarıda oluşturulan mevcut etki alanına katabilirsiniz.  

### <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

1. PowerShell ıSE 'yi yönetici olarak açın.
2. Aşağıdaki betiği çalıştırın.

    ```powershell
    # Filename:    1_CreateVM_CP.ps1
    # Description: Creates a VM to be used in the tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. #This script is made available to you without any express, implied or statutory warranty, not even the implied warranty of merchantability or fitness for a particular purpose, or the warranty of title or non-infringement. The entire risk of the use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $VMName = 'CP1'
    $Switch = 'External'
    $InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
    $Path = 'D:\VM'
    $VHDPath = 'D:\VM\CP1\CP1.vhdx'
    $VHDSize = '64424509440'

    #Create New Virtual Machine
    New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

    #Set the memory to be non-dynamic
    Set-VMMemory $VMName -DynamicMemoryEnabled $false

    #Add DVD Drive to Virtual Machine
    Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

    #Mount Installation Media
    $DVDDrive = Get-VMDvdDrive -VMName $VMName

    #Configure Virtual Machine to Boot from DVD
    Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive
    ```

### <a name="complete-the-operating-system-deployment"></a>İşletim sistemi dağıtımını tamamlandırma
Sanal makineyi oluşturma işleminin tamamlanabilmesi için, işletim sistemi yüklemesini bitirebilmeniz gerekir.

1. Hyper-V Yöneticisi, sanal makineye çift tıklayın
2. Başlat düğmesine tıklayın.
3. "CD veya DVD 'den önyüklemek için herhangi bir tuşa basmanız" istenir. Devam edin ve bunu yapın.
4. Windows Server başlangıç ekranında dilinizi seçin ve **İleri**' ye tıklayın.
5. **Şimdi yüklensin**' e tıklayın.
6. Lisans anahtarınızı girip **İleri**' ye tıklayın.
7. Denetle * * lisans koşullarını kabul ediyorum ve **İleri**' ye tıklayın.
8. Özel ' i seçin **: yalnızca Windows 'ı yükler (Gelişmiş)**
9. **İleri** 'ye tıklayın
10. Yükleme tamamlandıktan sonra, sanal makineyi yeniden başlatın, oturum açın ve VM 'nin en güncel olduğundan emin olmak için Windows güncelleştirmelerini çalıştırın.  En son güncelleştirmeleri yükler.

### <a name="install-active-directory-prerequisites"></a>Active Directory önkoşulları yüklensin
Artık bir sanal makineniz olduğuna göre, Active Directory yüklemeden önce birkaç şey yapmanız gerekir.  Diğer bir deyişle, sanal makineyi yeniden adlandırmanız, statik bir IP adresi ve DNS bilgileri ayarlamanız ve uzak sunucu yönetim araçları 'nı yüklemeniz gerekir.   Şunları yapın:

1. PowerShell ıSE 'yi yönetici olarak açın.
2. Aşağıdaki betiği çalıştırın.

    ```powershell
    # Filename:    2_ADPrep_CP.ps1
    # Description: Prepares your environment for Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $ipaddress = "10.0.1.118" 
    $ipprefix = "24" 
    $ipgw = "10.0.1.1" 
    $ipdns = "10.0.1.118"
    $ipdns2 = "8.8.8.8" 
    $ipif = (Get-NetAdapter).ifIndex 
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $newname = "CP1"
    $addsTools = "RSAT-AD-Tools" 

    #Set static IP address
    New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

    #Set the DNS servers
    Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

    #Rename the computer 
    Rename-Computer -NewName $newname -force 

    #Install features 
    New-Item $featureLogPath -ItemType file -Force 
    Add-WindowsFeature $addsTools 
    Get-WindowsFeature | Where installed >>$featureLogPath 

    #Restart the computer 
    Restart-Computer
    ```
### <a name="create-a-windows-server-ad-environment"></a>Windows Server AD ortamı oluşturma
VM 'yi oluşturup yeniden adlandırdığınıza ve statik bir IP adresine sahip olduğunuza göre, devam edip Active Directory Domain Services yükleyip yapılandırabilirsiniz.  Şunları yapın:

1. PowerShell ıSE 'yi yönetici olarak açın.
2. Aşağıdaki betiği çalıştırın.

    ```powershell
    # Filename:    3_InstallAD_CP.ps1
    # Description: Creates an on-premises AD environment.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $DatabasePath = "c:\windows\NTDS"
    $DomainMode = "WinThreshold"
    $DomainName = "fabrikam.com"
    $DomaninNetBIOSName = "FABRIKAM"
    $ForestMode = "WinThreshold"
    $LogPath = "c:\windows\NTDS"
    $SysVolPath = "c:\windows\SYSVOL"
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $Password = "Pass1w0rd"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

    #Install AD DS, DNS and GPMC 
    start-job -Name addFeature -ScriptBlock { 
    Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
    Wait-Job -Name addFeature 
    Get-WindowsFeature | Where installed >>$featureLogPath

    #Create New AD Forest
    Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
    ```

### <a name="create-a-windows-server-ad-user"></a>Windows Server AD kullanıcısı oluşturma
Active Directory ortamımız olduğuna göre, bir sınama hesabı gerekir.  Bu hesap, şirket içi AD ortamımızda oluşturulur ve ardından Azure AD ile eşitlenir.  Şunları yapın:

1. PowerShell ıSE 'yi yönetici olarak açın.
2. Aşağıdaki betiği çalıştırın.

    ```powershell 
    # Filename:    4_CreateUser_CP.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $Givenname = "Anna"
    $Surname = "Ringdal"
    $Displayname = "Anna Ringdal"
    $Name = "aringdal"
    $Password = "Pass1w0rd"
    $Identity = "CN=aringdal,CN=Users,DC=fabrikam,DC=com"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


    #Create the user
    New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

    #Set the password to never expire
    Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
    ```

## <a name="conclusion"></a>Sonuç
Artık var olan öğreticiler için kullanılabilen bir ortamınız var ve bulut eşitleme 'nin sağladığı ek özellikleri test edebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar 

- [Sağlama nedir?](what-is-provisioning.md)
- [Azure AD Connect bulut eşitlemesi nedir?](what-is-cloud-sync.md)