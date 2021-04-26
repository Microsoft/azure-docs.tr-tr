---
title: 'Öğretici: PTA kullanarak tek bir AD ormanını Azure ile tümleştirme'
description: Geçişli kimlik doğrulaması kullanarak karma kimlik ortamı ayarlama hakkında gösterir.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f02ec2220827fbec8c981ab3a1859d633675a6f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96176214"
---
# <a name="tutorial--integrate-a-single-ad-forest-using-pass-through-authentication-pta"></a>Öğretici: geçişli kimlik doğrulaması (PTA) kullanarak tek bir AD ormanını tümleştirme

![Oluştur](media/tutorial-passthrough-authentication/diagram.png)

Aşağıdaki öğretici, geçişli kimlik doğrulaması kullanarak karma kimlik ortamı oluşturma konusunda size yol gösterecektir.  Bu ortam daha sonra sınama için veya karma kimliğin nasıl çalıştığı hakkında daha tanıdık bilgi almak için kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar
Bu öğreticiyi tamamlamak için gerekli Önkoşullar aşağıda verilmiştir
- [Hyper-V](/windows-server/virtualization/hyper-v/hyper-v-technology-overview) yüklü bir bilgisayar.  Bunu bir [Windows 10](/virtualization/hyper-v-on-windows/about/supported-guest-os) veya [Windows Server 2016](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) bilgisayarında yapmanız önerilir.
- [Azure aboneliği](https://azure.microsoft.com/free)
- - Sanal makinenin internet ile iletişim kurmasına izin veren bir [dış ağ bağdaştırıcısı](/virtualization/hyper-v-on-windows/quick-start/connect-to-network) .
- Windows Server 2016 kopyası
- Doğrulanmakta olabilecek [özel bir etki alanı](../../active-directory/fundamentals/add-custom-domain.md)

> [!NOTE]
> Bu öğretici, en hızlı zamanda öğretici ortamını oluşturabilmeniz için PowerShell betikleri kullanır.  Betiklerin her biri, betikleri başlangıcında belirtilen değişkenleri kullanır.  Bu değişkenleri ortamınızı yansıtacak şekilde değiştirebilir ve değiştirebilirsiniz.
>
>Kullanılan betikler, Azure AD Connect yüklemeden önce genel Active Directory ortamı oluşturur.  Bunlar tüm öğreticilerle ilgilidir.
>
> Bu öğreticide kullanılan PowerShell betiklerinin kopyaları [burada](https://github.com/billmath/tutorial-phs)GitHub 'da bulunabilir.

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma
Karma kimlik ortamınızı çalışır duruma getirmek için gereken ilk şey, şirket içi Active Directory sunucusu olarak kullanılacak bir sanal makine oluşturmaktır.  

>[!NOTE]
>Ana makinenizde PowerShell 'de hiç bir komut dosyası çalıştırmadıysanız `Set-ExecutionPolicy remotesigned` , betikleri çalıştırmadan önce PowerShell 'de Evet ' i çalıştırmanız gerekir.

Şunları yapın:

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
2. Komutunu çalıştırın `Set-ExecutionPolicy remotesigned` ve tüm [A] için Evet deyin.  Enter tuşuna basın.
3. Aşağıdaki betiği çalıştırın.

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

## <a name="add-the-custom-domain-name-to-your-directory"></a>Dizine özel etki alanı adı ekleyin
Artık bir kiracının ve genel yöneticimiz olduğuna göre, Azure 'un doğrulayabilmesi için özel etki alanını eklememiz gerekiyor.  Şunları yapın:

1. [Azure Portal](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) geri döndüğünüzde **tüm kullanıcılar** dikey penceresini kapatmayı unutmayın.
2. Sol taraftan **Özel etki alanı adları**'nı seçin.
3. **Özel etki alanı ekle**'yi seçin.</br>
![Özel etki alanı Ekle düğmesinin vurgulandığını gösteren ekran görüntüsü.](media/tutorial-federation/custom1.png)</br>
4. **Özel etki alanı adlarında**, kutuya özel etki alanınızı adını girin ve **etki alanı Ekle**' ye tıklayın.
5. Özel etki alanı adı ekranında, TXT veya MX bilgileriyle birlikte sağlanacaktır.  Bu bilgiler, etki alanınız altındaki etki alanı kayıt kaydedicisinde yer alan DNS bilgilerine eklenmelidir.  Bu nedenle, etki alanı kayıt şirketinize gitmeniz, etki alanınız için DNS ayarları içindeki TXT veya MX bilgilerini girmeniz gerekir.  Bu, Azure 'un etki alanınızı doğrulamasına izin verir.  Bu işlem, Azure 'un doğrulaması 24 saate kadar sürebilir.  Daha fazla bilgi için bkz. [özel etki alanı ekleme](../../active-directory/fundamentals/add-custom-domain.md) belgeleri.</br>
![TXT veya MX bilgilerini eklediğiniz yeri gösteren ekran görüntüsü.](media/tutorial-federation/custom2.png)</br>
6. Doğrulandığından emin olmak için Doğrula düğmesine tıklayın.</br>
![Doğrula ' yı seçtikten sonra başarılı bir doğrulama iletisi gösteren ekran görüntüsü.](media/tutorial-federation/custom3.png)</br>

## <a name="download-and-install-azure-ad-connect"></a>Azure AD Connect indir ve yükle
Şimdi Azure AD Connect indirme ve yükleme işlemi zaman alabilir.  Yüklendikten sonra hızlı yükleme aracılığıyla çalıştıracağız.  Şunları yapın:

1. [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) indir
2. **AzureADConnect.msi** öğesine gidin ve çift tıklayın.
3. Hoş Geldiniz ekranında, lisans koşullarını kabul ettiğinizi belirten kutuyu seçin ve **Devam**'a tıklayın.  
4. Hızlı Ayarlar ekranında, **Özelleştir**' e tıklayın.  
5. Gerekli bileşenleri Install ekranında. **Yükle**'ye tıklayın.  
6. Kullanıcı oturum açma ekranında **doğrudan kimlik doğrulaması** ' nı seçin ve **Çoklu oturum açmayı etkinleştirin** ve **İleri**' ye tıklayın.</br>
![PTA](media/tutorial-passthrough-authentication/pta1.png)</b>
7. Azure AD 'ye Bağlan ekranında, yukarıda oluşturduğum genel yöneticinin kullanıcı adını ve parolasını girin ve **İleri**' ye tıklayın.
2. Dizinlerinizi bağlama ekranında **Dizin Ekle**' ye tıklayın.  Ardından **yenı ad hesabı oluştur** ' u seçin ve contoso\administrator Kullanıcı adını ve parolasını girip **Tamam**' a tıklayın.
3. **İleri**’ye tıklayın.
4. Azure AD oturum açma yapılandırması ekranında, **doğrulanan etki alanları için tüm UPN soneklerini eşleştirilmeden devam** ' ı seçin ve ileri ' ye tıklayın **.**
5. Etki alanı ve OU filtreleme ekranında **İleri**' ye tıklayın.
6. Kullanıcılarınızın benzersiz şekilde tanımlanması ekranında **İleri**' ye tıklayın.
7. Kullanıcıları ve cihazları filtrele ekranında **İleri**' ye tıklayın.
8. Isteğe bağlı Özellikler ekranında **İleri**' ye tıklayın.
9. Çoklu oturum açma kimlik bilgilerini Etkinleştir sayfasında, contoso\Administrator Kullanıcı adını ve parolasını girin ve Ileri ' ye tıklayın **.**
10. Yapılandırma için hazır ekranında **Yükle**'ye tıklayın.
11. Yükleme tamamlandığında **Çıkış**'a tıklayın.
12. Yükleme tamamlandıktan sonra, Synchronization Service Manager veya eşitleme kuralı düzenleyicisini kullanmadan önce oturumunuzu kapatın ve yeniden oturum açın.


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Kullanıcıların oluşturulduğunu ve eşitlemenin gerçekleştiğini doğrula
Şimdi, şirket içi dizinimizde yer alan kullanıcıların Azure AD kiracısında mevcut olduğunu doğrulayacağız.  Bu işlem işleminin tamamlanması birkaç saat sürebilir.  Kullanıcıların eşitlendiğinden emin olmak için aşağıdakileri yapın.


1. [Azure portalına](https://portal.azure.com) gidip Azure aboneliği olan bir hesapla oturum açın.
2. Sol tarafta **Azure Active Directory** ' yi seçin.
3. **Yönet** bölümünde **Kullanıcılar**'ı seçin.
4. Kiracı eşitleme yaptığımız yeni kullanıcıları gördiğinizi doğrulayın ![](media/tutorial-password-hash-sync/synch1.png)

## <a name="test-signing-in-with-one-of-our-users"></a>Kullanıcılarımızdan biriyle oturum açma testi

1. [https://myapps.microsoft.com](https://myapps.microsoft.com) adresine gidin
2. Yeni kiracımızda oluşturulmuş bir kullanıcı hesabıyla oturum açın.  Şu biçimi kullanarak oturum açmanız gerekir: ( user@domain.onmicrosoft.com ). Kullanıcının şirket içinde oturum açması için kullandığı parolayı kullanın.
   ![Doğrulama](media/tutorial-password-hash-sync/verify1.png)

Artık Azure 'un sunabileceği bir karma kimlik ortamını test etmek ve tanımak için kullanabileceğiniz bir karma kimlik ortamı oluşturdunuz.

## <a name="next-steps"></a>Sonraki Adımlar


- [Donanım ve önkoşullar](how-to-connect-install-prerequisites.md) 
- [Özelleştirilmiş ayarlar](how-to-connect-install-custom.md)
- [Geçişli kimlik doğrulaması](how-to-connect-pta.md)