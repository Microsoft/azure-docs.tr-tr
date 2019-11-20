---
title: Azure Site Recovery ' deki fiziksel sunucular için yapılandırma sunucusunu yönetme
description: Bu makalede, Azure 'da fiziksel sunucu olağanüstü durum kurtarma için Azure Site Recovery yapılandırma sunucusunun nasıl yönetileceği açıklanır.
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: article
ms.date: 02/28/2019
ms.author: mayg
ms.openlocfilehash: f443f0362ecad8448895322686a7175b2813141e
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084607"
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>Fiziksel sunucu olağanüstü durum kurtarma için yapılandırma sunucusunu yönetme

Fiziksel sunucuların olağanüstü durum kurtarması için Azure 'a [Azure Site Recovery](site-recovery-overview.md) hizmetini kullandığınızda bir şirket içi yapılandırma sunucusu ayarlarsınız. Yapılandırma sunucusu, şirket içi makineler ve Azure arasındaki iletişimleri koordine eder ve veri çoğaltmasını yönetir. Bu makalede, yapılandırma sunucusunu dağıtıldıktan sonra yönetmek için ortak görevler özetlenmektedir.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Ön koşullar

Tablo, şirket içi yapılandırma sunucusu makinesini dağıtmaya yönelik önkoşulları özetler.

| **Bileşen** | **Gereksinim** |
| --- |---|
| CPU çekirdekleri| 8 |
| RAM | 16 GB|
| Disk sayısı | 3, işletim sistemi diski, işlem sunucusu önbellek diski ve yeniden çalışma için bekletme sürücüsü dahil |
| Boş disk alanı (işlem sunucusu önbelleği) | 600 GB
| Boş disk alanı (bekletme diski) | 600 GB|
| İşletim sistemi  | Windows Server 2012 R2 <br> Windows Server 2016 |
| İşletim sistemi yerel ayarı | English (US)|
| VMware vSphere PowerCLI sürümü | Gerekli değil|
| Windows Server rolleri | Bu rolleri etkinleştirmeyin: <br> - Active Directory Domain Services <br>- İnternet Bilgi Hizmetleri <br> - Hyper-V |
| Grup ilkeleri| Bu grup ilkelerini etkinleştirmeyin: <br> -Komut istemine erişimi engelle <br> -Kayıt defteri düzenlemesi araçlarına erişimi engelle <br> -Dosya ekleri için güven mantığı <br> -Betik yürütmeyi aç <br> [Daha fazla bilgi](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
| IIS | -Önceden var olan varsayılan Web sitesi yok <br> - [Anonim kimlik doğrulamasını](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) etkinleştir <br> - [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) ayarını etkinleştir  <br> -Var olan bir Web sitesi/uygulama dinleme 443 bağlantı noktasında dinleniyor<br>|
| NIC türü | VMXNET3 (bir VMware sanal makinesi olarak dağıtıldığında) |
| IP adresi türü | Statik |
| İnternet erişimi | Sunucunun bu URL 'Lere erişmesi gerekir: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://management.azure.com <br> - *.services.visualstudio.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi (genişleme Işlem sunucuları için gerekli değildir) <br> - time.nist.gov <br> - time.windows.com |
| Bağlantı Noktaları | 443 (Denetim kanalı düzenleme)<br>9443 (Veri aktarımı)|

## <a name="download-the-latest-installation-file"></a>En son yükleme dosyasını indirin

Yapılandırma sunucusu yükleme dosyasının en son sürümü Site Recovery portalında kullanılabilir. Ayrıca, doğrudan [Microsoft Indirme merkezi](https://aka.ms/unifiedsetup)' nden indirilebilir.

1. Azure portal oturum açın ve kurtarma hizmetleri kasanıza gidin.
2. **Yapılandırma sunucuları** > **Site Recovery altyapısına** (VMware & fiziksel makineler için) gidin.
3. **+ Sunucular** düğmesine tıklayın.
4. **Sunucu Ekle** sayfasında, kayıt anahtarını Indirmek için İndir düğmesine tıklayın. Configuration Server yüklemesi sırasında Azure Site Recovery hizmetine kaydetmek için bu anahtara ihtiyacınız vardır.
5. Yapılandırma sunucusunun en son sürümünü indirmek için **Microsoft Azure Site Recovery Birleşik kurulum bağlantısını indirin** bağlantısına tıklayın.

   ![İndirme sayfası](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>Sunucuyu yükleyip kaydetme

1. Birleşik Kurulum yükleme dosyasını çalıştırın.
2. **Başlamadan önce**' de, **yapılandırma sunucusunu ve işlem sunucusunu yükler**' i seçin.

    ![Başlamadan önce](./media/physical-manage-configuration-server/combined-wiz1.png)

3. MySQL indirip yüklemek için **Üçüncü Taraf Yazılım Lisansı** bölümünde **Kabul Ediyorum**’a tıklayın.
4. **İnternet Ayarları** alanında, yapılandırma sunucusunda çalışan Sağlayıcının Azure Site Recovery'ye İnternet üzerinden nasıl bağlanacağını belirtin. Gerekli URL 'Lere izin verildiğinden emin olun.

    - Makinede ayarlanmış olan ara sunucuya bağlanmak istiyorsanız, **proxy sunucusu kullanarak Azure Site Recovery Bağlan**' ı seçin.
    - Sağlayıcının doğrudan bağlanmasını istiyorsanız, **proxy sunucusu olmadan Azure Site Recovery doğrudan Bağlan**' ı seçin.
    - Mevcut ara sunucu kimlik doğrulaması gerektiriyorsa veya sağlayıcı bağlantısı için özel bir ara sunucu kullanmak istiyorsanız **özel ara sunucu ayarlarıyla Bağlan**' ı seçin ve adresi, bağlantı noktasını ve kimlik bilgilerini belirtin.
     ![Güvenlik duvarı](./media/physical-manage-configuration-server/combined-wiz4.png)
6. **Önkoşul Denetimi** menüsünde Kurulum, yüklemenin çalışabildiğinden emin olmak üzere bir denetim gerçekleştirir. **Genel saat eşitleme denetimi** hakkında bir uyarı görünürse, sistem saatindeki zamanın (**Tarih ve Saat** ayarları) saat dilimiyle aynı olduğunu doğrulayın.

    ![Ön koşullar](./media/physical-manage-configuration-server/combined-wiz5.png)
7. **MySQL Yapılandırması** menüsünde, yüklü MySQL sunucu örneğinde oturum açmak için kimlik bilgileri oluşturun.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. **Ortam Ayrıntıları**’nda VMware sanal makinelerini çoğaltıp çoğaltmayacağınızı seçin. Bu durumda, kurulum PowerCLI 6,0 'nin yüklü olduğunu denetler.
9. **Yükleme Konumu** alanında ikili dosyaları yüklemek ve önbelleği depolamak istediğiniz konumu seçin. Seçtiğiniz sürücü en az 5 GB kullanılabilir disk alanına sahip olmalıdır, ancak en az 600 GB boş alanı olan bir önbellek sürücüsü seçmeniz önerilir.

    ![Yükleme konumu](./media/physical-manage-configuration-server/combined-wiz8.png)
10. **Ağ seçimi**' nde, ilk olarak, yerleşik işlem sunucusunun kaynak makinelerde Mobility hizmeti bulma ve yükleme IÇIN kullandığı NIC 'i seçin ve ardından yapılandırma sunucusunun Azure ile bağlantı IÇIN kullanacağı NIC 'i seçin. Bağlantı noktası 9443, çoğaltma trafiğini gönderip almak için kullanılan varsayılan bağlantı noktasıdır, ancak bu bağlantı noktası numarasını ortamınızın gereksinimlerine uyacak şekilde değiştirebilirsiniz. Bağlantı noktası 9443’e ek olarak, çoğaltma işlemlerini düzenlemek için web sunucusu tarafından kullanılan bağlantı noktası 443 de açılır. Çoğaltma trafiğini göndermek veya almak için 443 bağlantı noktasını kullanmayın.

    ![Ağ seçimi](./media/physical-manage-configuration-server/combined-wiz9.png)


11. **Özet** alanındaki bilgileri gözden geçirin ve **Yükle**’ye tıklayın. Yükleme tamamlandığında bir parola oluşturulur. Çoğaltmayı etkinleştirdiğinizde bu parola gerekli olacaktır; bu yüzden kopyalayıp güvenli bir yerde saklayın.


Kayıt tamamlandıktan sonra, sunucu kasadaki **Ayarlar** > **Sunucular** dikey penceresinde görüntülenir.


## <a name="install-from-the-command-line"></a>Komut satırından yükler

Yükleme dosyasını aşağıdaki gibi çalıştırın:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Örnek kullanım
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>Parametreler

|Parametre Adı| Tür | Açıklama| Değerler|
|-|-|-|-|
| /ServerMode|Gerekli|Hem yapılandırma hem de işlem sunucusunun mu yoksa yalnızca işlem sunucusunun mu yükleneceğini belirtir|CS<br>PS|
|/InstallLocation|Gerekli|Bileşenlerin yüklendiği klasör| Bilgisayardaki herhangi bir klasör|
|/MySQLCredsFilePath|Gerekli|MySQL sunucusu kimlik bilgilerinin depolandığı dosya yolu|Dosya aşağıda belirtilen biçimde olmalıdır|
|/VaultCredsFilePath|Gerekli|Kasa kimlik bilgileri dosyasının yolu|Geçerli dosya yolu|
|/EnvType|Gerekli|Korumak istediğiniz ortam türü |VMware<br>NonVMware|
|/PSIP|Gerekli|Çoğaltma veri aktarımı için kullanılacak NIC’nin IP adresi| Herhangi bir geçerli IP adresi|
|/CSIP|Gerekli|Yapılandırma sunucusunun dinleme yaptığı NIC’nin IP adresi| Herhangi bir geçerli IP adresi|
|/PassphraseFilePath|Gerekli|Parola dosyası konumunun tam yolu|Geçerli dosya yolu|
|/BypassProxy|İsteğe bağlı|Yapılandırma sunucusunun Azure'a bir ara sunucu olmadan bağlandığını belirtir|Yapmak için bu değeri Venu’den alın|
|/ProxySettingsFilePath|İsteğe bağlı|Ara sunucu ayarları (Varsayılan ara sunucu kimlik doğrulaması gerektirir ya da özel bir ara sunucu kullanılır)|Dosya aşağıda belirtilen biçimde olmalıdır|
|DataTransferSecurePort|İsteğe bağlı|Çoğaltma verileri için kullanılacak PSIP’deki bağlantı noktası numarası| Geçerli Bağlantı Noktası Numarası (varsayılan değer: 9433)|
|/SkipSpaceCheck|İsteğe bağlı|Önbellek diski için alan denetimini atlama| |
|/AcceptThirdpartyEULA|Gerekli|Bayrak, üçüncü taraf EULA'nın kabul edildiğini gösterir| |
|/ShowThirdpartyEULA|İsteğe bağlı|Üçüncü taraf EULA belgesini görüntüler. Giriş olarak sağlanırsa, diğer tüm parametreler yoksayılır| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>MYSQLCredsFilePath için dosya girişi oluşturma

MySQLCredsFilePath parametresi bir dosyayı giriş olarak alır. Aşağıdaki biçimi kullanarak dosyayı oluşturun ve giriş MySQLCredsFilePath parametresi olarak geçirin.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>ProxySettingsFilePath için dosya girişi oluştur
ProxySettingsFilePath parametresi bir dosyayı giriş olarak alır. Aşağıdaki biçimi kullanarak dosyayı oluşturun ve giriş ProxySettingsFilePath parametresi olarak geçirin.

```ini
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modify-proxy-settings"></a>Ara sunucu ayarlarını değiştir

Yapılandırma sunucusu makinesi için proxy ayarlarını aşağıdaki gibi değiştirebilirsiniz:

1. Yapılandırma sunucusunda oturum açın.
2. Masaüstünüzdeki kısayolu kullanarak Cspsconfigtool. exe ' yi başlatın.
3. **Kasa kaydı** sekmesine tıklayın.
4. Portaldan yeni bir kasa kayıt dosyası indirin ve araca giriş olarak sağlayın.

   ![kayıt-yapılandırma-sunucu](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Yeni proxy ayrıntılarını girip **Kaydet** düğmesine tıklayın.
6. Yönetici PowerShell komut penceresini açın.
7. Şu komutu çalıştırın:

   ```powershell
   $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
   net stop obengine
   net start obengine
   ```

   > [!WARNING]
   > Yapılandırma sunucusuna bağlı ek işlem sunucularınız varsa, dağıtımınızdaki [tüm genişleme işlem sunucularındaki proxy ayarlarını çözmeniz](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server) gerekir.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Bir yapılandırma sunucusunu aynı kasaya yeniden kaydetme
1. Yapılandırma sunucunuzda oturum açın.
2. Masaüstünüzdeki kısayolu kullanarak Cspsconfigtool. exe ' yi başlatın.
3. **Kasa kaydı** sekmesine tıklayın.
4. Portaldan yeni bir kayıt dosyası indirin ve araca giriş olarak sağlayın.
      ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Proxy sunucusu ayrıntılarını girip **Kaydet** düğmesine tıklayın.  
6. Yönetici PowerShell komut penceresini açın.
7. Aşağıdaki komutu çalıştırın

    ```powershell
    $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
    net stop obengine
    net start obengine
    ```

   > [!WARNING]
   > Birden çok işlem sunucunuz varsa, [bunları yeniden kaydetmeniz](vmware-azure-manage-process-server.md#reregister-a-process-server)gerekir.

## <a name="register-a-configuration-server-with-a-different-vault"></a>Yapılandırma sunucusunu farklı bir kasaya kaydetme

> [!WARNING]
> Aşağıdaki adım yapılandırma sunucusunu geçerli kasadan ilişkilendirir ve yapılandırma sunucusu altındaki tüm korumalı sanal makinelerin çoğaltılması durdurulur.

1. Yapılandırma sunucusunda oturum açın
2. Yönetici komut isteminden şu komutu çalıştırın:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Masaüstünüzdeki kısayolu kullanarak Cspsconfigtool. exe ' yi başlatın.
4. **Kasa kaydı** sekmesine tıklayın.
5. Portaldan yeni bir kayıt dosyası indirin ve araca giriş olarak sağlayın.
6. Proxy sunucusu ayrıntılarını girip **Kaydet** düğmesine tıklayın.  
7. Yönetici PowerShell komut penceresini açın.
8. Aşağıdaki komutu çalıştırın
    ```powershell
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Yapılandırma sunucusunu yükseltme

Yapılandırma sunucusunu güncelleştirmek için güncelleştirme paketleri çalıştırırsınız. Güncelleştirmeler, en fazla N 4 sürüm için uygulanabilir. Örneğin:

- 9,7, 9,8, 9,9 veya 9,10 çalıştırıyorsanız, doğrudan 9,11 'e yükseltebilirsiniz.
- 9,6 veya önceki bir sürümünü çalıştırıyorsanız ve 9,11 sürümüne yükseltmek istiyorsanız, önce sürüm 9,7 ' ye yükseltmeniz gerekir. 9,11 öncesi.

Yapılandırma sunucusunun tüm sürümlerine yükseltmeye yönelik güncelleştirme paketlerinin bağlantıları, [wiki güncelleştirmeleri sayfasında](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx)bulunur.

Sunucuyu aşağıdaki gibi yükseltin:

1. Güncelleştirme yükleyicisi dosyasını yapılandırma sunucusuna indirin.
2. Yükleyiciyi çalıştırmak için çift tıklayın.
3. Yükleyici, makinede çalışan geçerli sürümü algılar.
4. Onaylamak ve yükseltmeyi çalıştırmak için **Tamam** ' ı tıklatın. 


## <a name="delete-or-unregister-a-configuration-server"></a>Yapılandırma sunucusunu silme veya kaydını kaldırma

> [!WARNING]
> Yapılandırma sunucunuza yetki vermeden önce aşağıdakilerden emin olun.
> 1. Bu yapılandırma sunucusu altındaki tüm sanal makineler için [korumayı devre dışı bırakın](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) .
> 2. Tüm çoğaltma ilkelerinin yapılandırma sunucusundan [Ilişkisini kaldırın](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) ve [silin](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) .
> 3. Yapılandırma sunucusuyla ilişkili tüm vCenters sunucularını/vSphere konaklarınızı [silin](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) .


### <a name="delete-the-configuration-server-from-azure-portal"></a>Yapılandırma sunucusunu Azure portal Sil
1. Azure portal, kasa menüsündeki **yapılandırma sunucularına** **Site Recovery altyapı** > ' a gidin.
2. Yetkisini kaldırmak istediğiniz yapılandırma sunucusuna tıklayın.
3. Yapılandırma sunucusunun ayrıntılar sayfasında **Sil** düğmesine tıklayın.
4. Sunucu silme işlemini onaylamak için **Evet** ' e tıklayın.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>Yapılandırma sunucusunu ve bağımlılıklarını kaldırma
> [!TIP]
>   Yapılandırma sunucusunu yeniden Azure Site Recovery yeniden kullanmayı planlıyorsanız, doğrudan 4. adıma atlayabilirsiniz

1. Yapılandırma sunucusunda yönetici olarak oturum açın.
2. Program > programları kaldırma > Denetim Masası 'Nı açın
3. Aşağıdaki sırada programları kaldırın:
   * Microsoft Azure Kurtarma Hizmetleri Aracısı
   * Microsoft Azure Site Recovery Mobility hizmeti/ana hedef sunucusu
   * Microsoft Azure Site Recovery sağlayıcısı
   * Microsoft Azure Site Recovery yapılandırma sunucusu/Işlem sunucusu
   * Microsoft Azure Site Recovery yapılandırma sunucusu bağımlılıkları
   * MySQL Server 5,5
4. Ve yönetici komut isteminden aşağıdaki komutu çalıştırın.
   ```
   reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
   ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Yapılandırma sunucusunu silme veya kaydını kaldırma (PowerShell)

1. [Yüklemesi](https://docs.microsoft.com/powershell/azure/install-Az-ps) Azure PowerShell modülü
2. Komutunu kullanarak Azure hesabınızda oturum açın
    
    `Connect-AzAccount`
3. Kasasının bulunduğu aboneliği seçin

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Şimdi kasa bağlamını ayarlayın
    
    ```powershell
    $Vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $Vault
    ```
4. Yapılandırma sunucunuzu seçin

    `$Fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Yapılandırma sunucusunu Sil

    `Remove-AzSiteRecoveryFabric -Fabric $Fabric [-Force]`

> [!NOTE]
> Remove-AzSiteRecoveryFabric ' daki **-zorlama** seçeneği, yapılandırma sunucusunu kaldırmayı/silmeyi zorlamak için kullanılabilir.

## <a name="renew-ssl-certificates"></a>SSL sertifikalarını yenileme
Yapılandırma sunucusunda, Mobility hizmetinin, işlem sunucularının ve buna bağlı ana hedef sunucuların etkinliklerini düzenleyen bir yerleşik Web sunucusu vardır. Web sunucusu, istemcilerin kimliğini doğrulamak için bir SSL sertifikası kullanır. Sertifikanın süresi üç yıl sonra dolar ve herhangi bir zamanda yenilenebilir.

### <a name="check-expiry"></a>Son tarihi denetle

2016 Mayıs 'tan önce yapılandırma sunucusu dağıtımları için, sertifika süre sonu bir yıl olarak ayarlanmıştır. Bir sertifikanızın kullanım süreleri dolarsa aşağıdakiler gerçekleşir:

- Sona erme tarihi iki ay veya daha azsa, hizmet portalda bildirim göndermeye başlar ve e-posta ile (Azure Site Recovery bildirimlerine abone değilseniz).
- Kasa kaynağı sayfasında bir bildirim başlığı görüntülenir. Daha fazla ayrıntı için başlığa tıklayın.
- **Şimdi Yükselt** düğmesini görürseniz, ortamınızda 9.4. xxxx. x veya üzeri sürümlere yükseltilmeyen bazı bileşenler olduğunu gösterir. Sertifikayı yenilemeden önce bileşenleri yükseltin. Eski sürümlerde yenileyemezsiniz.

### <a name="renew-the-certificate"></a>Sertifikayı Yenile

1. Kasada, **Site Recovery altyapı** > **yapılandırma sunucusu**' nu açın ve gerekli yapılandırma sunucusuna tıklayın.
2. Süre sonu tarihi **yapılandırma sunucusu sistem durumu** altında görüntülenir
3. **Sertifikaları yenile**' ye tıklayın. 




## <a name="common-issues"></a>Genel sorunlar
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Sonraki adımlar

[Fiziksel sunucuları](tutorial-physical-to-azure.md) Azure 'a olağanüstü durum kurtarmayı ayarlamaya yönelik öğreticileri gözden geçirin.

