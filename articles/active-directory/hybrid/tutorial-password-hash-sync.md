---
title: "Öğretici: tek bir AD ormanını Azure 'da PHS kullanarak tümleştirme"
description: Bir karma kimlik ortamının Parola karması eşitlemesi kullanılarak nasıl ayarlanacağını gösterir.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b82dcc633f0f2bb13cb84f80873117f9eb94a24f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96176129"
---
# <a name="tutorial--integrate-a-single-ad-forest-using-password-hash-sync-phs"></a>Öğretici: Parola karması eşitleme (PHS) kullanarak tek bir AD ormanını tümleştirme

![Oluştur](media/tutorial-password-hash-sync/diagram.png)

Aşağıdaki öğreticide, Parola karması eşitlemesini kullanarak karma kimlik ortamı oluşturma işleminde size yol gösterilir.  Bu ortam daha sonra sınama için veya karma kimliğin nasıl çalıştığı hakkında daha tanıdık bilgi almak için kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar
Bu öğreticiyi tamamlamak için gerekli Önkoşullar aşağıda verilmiştir
- [Hyper-V](/windows-server/virtualization/hyper-v/hyper-v-technology-overview) yüklü bir bilgisayar.  Bunu bir [Windows 10](/virtualization/hyper-v-on-windows/about/supported-guest-os) veya [Windows Server 2016](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) bilgisayarında yapmanız önerilir.
- Sanal makinenin internet ile iletişim kurmasına izin veren bir [dış ağ bağdaştırıcısı](/virtualization/hyper-v-on-windows/quick-start/connect-to-network) .
- [Azure aboneliği](https://azure.microsoft.com/free)
- Windows Server 2016 kopyası

> [!NOTE]
> Bu öğretici, en hızlı zamanda öğretici ortamını oluşturabilmeniz için PowerShell betikleri kullanır.  Betiklerin her biri, betikleri başlangıcında belirtilen değişkenleri kullanır.  Bu değişkenleri ortamınızı yansıtacak şekilde değiştirebilir ve değiştirebilirsiniz.
>
>Kullanılan betikler, Azure AD Connect yüklemeden önce genel Active Directory ortamı oluşturur.  Bunlar tüm öğreticilerle ilgilidir.
>
> Bu öğreticide kullanılan PowerShell betiklerinin kopyaları [burada](https://github.com/billmath/tutorial-phs)GitHub 'da bulunabilir.

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma
Karma kimlik ortamınızı çalışır duruma getirmek için gereken ilk şey, şirket içi Active Directory sunucusu olarak kullanılacak bir sanal makine oluşturmaktır.  Şunları yapın:

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
VM 'nin oluşturulduğuna ve yeniden adlandırıldığına ve statik bir IP adresine sahip olduğumuz için artık Active Directory Domain Services yükleyip yapılandırabiliriz.  Şunları yapın:

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
Artık Active Directory ortamımız olduğuna göre, bir sınama hesabı gerekir.  Bu hesap, şirket içi AD ortamımızda oluşturulur ve ardından Azure AD ile eşitlenir.  Şunları yapın:

1. PowerShell ıSE 'yi yönetici olarak açın.
2. Aşağıdaki betiği çalıştırın.

```powershell 
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
Şimdi, kullanıcılarınızı bulutumuza eşitleyebilmemiz için bir Azure AD kiracısı oluşturmamız gerekiyor.  Yeni bir Azure AD kiracısı oluşturmak için aşağıdaki adımları uygulayın.

1. [Azure portalına](https://portal.azure.com) gidip Azure aboneliği olan bir hesapla oturum açın.
2. **Artı simgesini (+)** seçip **Azure Active Directory** terimini aratın.
3. Arama sonuçlarında **Azure Active Directory** girişini seçin.
4. **Oluştur**’u seçin.</br>
![Azure AD kiracısı oluşturmayı gösteren ekran görüntüsü.](media/tutorial-password-hash-sync/create1.png)</br>
5. **Kuruluş için bir ad** ve **ilk etki alanı adı** girin. Ardından **Oluştur**’u seçin. Dizininiz oluşturulur.
6. Bu tamamlandığında, dizini yönetmek için **buraya** tıklayın bağlantısına tıklayın.

## <a name="create-a-global-administrator-in-azure-ad"></a>Azure AD 'de Genel yönetici oluşturma
Artık bir Azure AD kiracımız olduğuna göre, genel yönetici hesabı oluşturacağız.  Bu hesap Azure AD Connect yüklemesi sırasında Azure AD bağlayıcı hesabı oluşturmak için kullanılır.  Azure AD Bağlayıcısı hesabı, Azure AD 'ye bilgi yazmak için kullanılır.   Genel yönetici hesabını oluşturmak için aşağıdakileri yapın.

1.  **Yönet** bölümünde **Kullanıcılar**'ı seçin.</br>
![Azure AD 'de Genel yönetici oluşturduğunuz Yönet bölümünde seçilen kullanıcı seçeneğini gösteren ekran görüntüsü.](media/tutorial-password-hash-sync/gadmin1.png)</br>
2.  **Tüm kullanıcılar**'ı ve ardından **+ Yeni kullanıcı**'yı seçin.
3.  Bu kullanıcı için bir ad ve kullanıcı adı girin. Bu kullanıcı kiracınızın Genel Yöneticisi olacak. Ayrıca, **Dizin rolünü** **genel yönetici** olarak değiştirmek isteyeceksiniz. İsterseniz geçici parolayı da gösterebilirsiniz. İşiniz bittiğinde **Oluştur**'u seçin.</br>
![Azure AD 'de Genel yönetici oluştururken seçtiğiniz Oluştur düğmesini gösteren ekran görüntüsü.](media/tutorial-password-hash-sync/gadmin2.png)</br>
4. Bu tamamlandığında, yeni bir Web tarayıcısı açın ve yeni genel yönetici hesabı ile geçici parolayı kullanarak myapps.microsoft.com 'de oturum açın.
5. Genel yönetici parolasını hatırlayacaksınız bir şekilde değiştirin.

## <a name="download-and-install-azure-ad-connect"></a>Azure AD Connect indir ve yükle
Şimdi Azure AD Connect indirme ve yükleme işlemi zaman alabilir.  Yüklendikten sonra hızlı yükleme aracılığıyla çalıştıracağız.  Şunları yapın:

1. [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) indir
2. **AzureADConnect.msi** öğesine gidin ve çift tıklayın.
3. Hoş Geldiniz ekranında, lisans koşullarını kabul ettiğinizi belirten kutuyu seçin ve **Devam**'a tıklayın.  
4. Hızlı ayarlar ekranında **Hızlı ayarları kullan**'a tıklayın.</br>  
![Hızlı ayarlar ekranını ve hızlı ayarları kullan düğmesini gösteren ekran görüntüsü.](media/tutorial-password-hash-sync/express1.png)</br>
5. Azure AD 'ye Bağlan ekranında, Azure AD için genel yönetici kullanıcı adını ve parolasını girin. **İleri**’ye tıklayın.  
6. AD DS'ye Bağlanma ekranında kuruluş yöneticisi hesabına ilişkin kullanıcı adını ve parolayı girin. **İleri**’ye tıklayın.  
7. Yapılandırma için hazır ekranında **Yükle**'ye tıklayın.
8. Yükleme tamamlandığında **Çıkış**'a tıklayın.
9. Yükleme tamamlandıktan sonra, Synchronization Service Manager veya eşitleme kuralı düzenleyicisini kullanmadan önce oturumunuzu kapatın ve yeniden oturum açın.


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Kullanıcıların oluşturulduğunu ve eşitlemenin gerçekleştiğini doğrula
Şimdi, şirket içi dizinimizde yer alan kullanıcıların Azure AD kiracısında mevcut olduğunu doğrulayacağız.  Bu işlem işleminin tamamlanması birkaç saat sürebilir.  Kullanıcıların eşitlendiğinden emin olmak için aşağıdakileri yapın.


1. [Azure portalına](https://portal.azure.com) gidip Azure aboneliği olan bir hesapla oturum açın.
2. Sol tarafta **Azure Active Directory** ' yi seçin.
3. **Yönet** bölümünde **Kullanıcılar**'ı seçin.
4. Kiracımızda yeni kullanıcıları gördiğinizi doğrulayın</br>
![Tablosunun](media/tutorial-password-hash-sync/synch1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Kullanıcılarımızdan biriyle oturum açma testi

1. [https://myapps.microsoft.com](https://myapps.microsoft.com) adresine gidin
2. Yeni kiracımızda oluşturulmuş bir kullanıcı hesabıyla oturum açın.  Şu biçimi kullanarak oturum açmanız gerekir: ( user@domain.onmicrosoft.com ). Kullanıcının şirket içinde oturum açması için kullandığı parolayı kullanın.</br>
   ![Doğrulama](media/tutorial-password-hash-sync/verify1.png)</br>

Artık Azure 'un sunabileceği bir karma kimlik ortamını test etmek ve tanımak için kullanabileceğiniz bir karma kimlik ortamı oluşturdunuz.

## <a name="next-steps"></a>Sonraki Adımlar


- [Donanım ve önkoşullar](how-to-connect-install-prerequisites.md) 
- [Hızlı ayarlar](how-to-connect-install-express.md)
- [Parola karması eşitleme](how-to-connect-password-hash-synchronization.md)|