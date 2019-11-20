---
title: Yapılandırma sunucusunu Azure Site Recovery olağanüstü durum kurtarma için yönetme
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: ramamill
ms.openlocfilehash: 93b10d56ae34ebdfe78dd20705634dea58721274
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954355"
---
# <a name="manage-the-configuration-server-for-vmware-vmphysical-server-disaster-recovery"></a>VMware VM/fiziksel sunucu olağanüstü durum kurtarma için yapılandırma sunucusunu yönetme

VMware VM 'Leri ve fiziksel sunucuları Azure 'a olağanüstü durum kurtarması için [Azure Site Recovery](site-recovery-overview.md) kullandığınızda bir şirket içi yapılandırma sunucusu ayarlarsınız. Yapılandırma sunucusu, şirket içi VMware ve Azure arasındaki iletişimleri koordine eder ve veri çoğaltmasını yönetir. Bu makalede, yapılandırma sunucusunu dağıtıldıktan sonra yönetmeye yönelik ortak görevler özetlenmektedir.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-windows-license"></a>Windows lisansını Güncelleştir

OVF şablonuyla birlikte sunulan lisans, 180 gün için geçerli bir değerlendirme lisanslayadır. Kesintisiz kullanım için Windows 'u bir temin lisansıyla etkinleştirmeniz gerekir. Lisans güncelleştirmesi, tek başına bir anahtar veya KMS standart anahtarı aracılığıyla yapılabilir. Rehberlik, [işletim sistemini çalıştırmak Için DISM Windows komut satırında](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-windows-edition-servicing-command-line-options)sunulmaktadır. Anahtarları almak için [KMS istemcisi ayarlama](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys)bölümüne bakın.

## <a name="access-configuration-server"></a>Erişim yapılandırma sunucusu

Yapılandırma sunucusuna aşağıdaki gibi erişebilirsiniz:

* Dağıtıldığı VM 'de oturum açın ve masaüstü kısayolundan **Azure Site Recovery Configuration Manager** başlatın.
* Alternatif olarak, https://*Configurationservername*/: 44315/adresinden yapılandırma sunucusuna uzaktan erişebilirsiniz. Yönetici kimlik bilgileriyle oturum açın.

## <a name="modify-vmware-server-settings"></a>VMware sunucu ayarlarını değiştir

1. Farklı bir VMware sunucusunu yapılandırma sunucusuyla ilişkilendirmek için, [oturum](#access-configuration-server)açtıktan sonra **vCenter Server/vSphere ESXi sunucusu Ekle**' yi seçin.
2. Ayrıntıları girip **Tamam**' ı seçin.

## <a name="modify-credentials-for-automatic-discovery"></a>Otomatik bulma için kimlik bilgilerini değiştirme

1. VMware sunucusuna bağlanmak için kullanılan kimlik bilgilerini, VMware VM 'lerinin otomatik bulması için güncelleştirmek üzere [oturum](#access-configuration-server)açtıktan sonra hesabı seçin ve **Düzenle**' ye tıklayın.
2. Yeni kimlik bilgilerini girin ve ardından **Tamam**' ı seçin.

    ![VMware 'yi değiştirme](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)

Kimlik bilgilerini CSPSConfigtool. exe aracılığıyla da değiştirebilirsiniz.

1. Yapılandırma sunucusunda oturum açın ve CSPSConfigtool. exe ' yi başlatın
2. Değiştirmek istediğiniz hesabı seçin ve **Düzenle**' ye tıklayın.
3. Değiştirilen kimlik bilgilerini girin ve **Tamam 'a** tıklayın

## <a name="modify-credentials-for-mobility-service-installation"></a>Mobility hizmeti yüklemesi için kimlik bilgilerini değiştirme

Çoğaltma için etkinleştirdiğiniz VMware VM 'lerine Mobility hizmetini otomatik olarak yüklemek için kullanılan kimlik bilgilerini değiştirin.

1. [Oturum](#access-configuration-server)açtıktan sonra, **sanal makine kimlik bilgilerini Yönet** ' i seçin
2. Değiştirmek istediğiniz hesabı seçin ve **Düzenle** ' ye tıklayın.
3. Yeni kimlik bilgilerini girin ve ardından **Tamam**' ı seçin.

    ![Mobility hizmeti kimlik bilgilerini değiştirme](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

Kimlik bilgilerini CSPSConfigtool. exe aracılığıyla da değiştirebilirsiniz.

1. Yapılandırma sunucusunda oturum açın ve CSPSConfigtool. exe ' yi başlatın
2. Değiştirmek istediğiniz hesabı seçin ve **Düzenle** ' ye tıklayın.
3. Yeni kimlik bilgilerini girip **Tamam**' a tıklayın.

## <a name="add-credentials-for-mobility-service-installation"></a>Mobility hizmeti yüklemesi için kimlik bilgileri ekleme

Yapılandırma sunucusunun OVF dağıtımı sırasında kimlik bilgileri ekleme kaçırdıysanız,

1. [Oturum](#access-configuration-server)açtıktan sonra, **sanal makine kimlik bilgilerini Yönet**' i seçin.
2. **Sanal makine kimlik bilgileri ekle**' ye tıklayın.
    ![Add-Mobility-Credentials](media/vmware-azure-manage-configuration-server/add-mobility-credentials.png)
3. Yeni kimlik bilgilerini girip **Ekle**' ye tıklayın.

CSPSConfigtool. exe ' yi kullanarak da kimlik bilgilerini ekleyebilirsiniz.

1. Yapılandırma sunucusunda oturum açın ve CSPSConfigtool. exe ' yi başlatın
2. **Ekle**' ye tıklayın, yeni kimlik bilgilerini girin ve **Tamam**' a tıklayın.

## <a name="modify-proxy-settings"></a>Ara sunucu ayarlarını değiştir

Configuration Server makinesi tarafından Azure 'a internet erişimi için kullanılan proxy ayarlarını değiştirin. Yapılandırma sunucusu makinesinde çalışan varsayılan işlem sunucusuna ek olarak bir işlem sunucusu makineniz varsa, her iki makinedeki ayarları değiştirin.

1. Yapılandırma sunucusunda [oturum](#access-configuration-server) açtıktan sonra **Bağlantıyı Yönet**' i seçin.
2. Proxy değerlerini güncelleştirin. Sonra ayarları güncelleştirmek için **Kaydet** ' i seçin.

## <a name="add-a-network-adapter"></a>Ağ bağdaştırıcısı ekleme

Open Virtualization Format (OVF) şablonu, tek bir ağ bağdaştırıcısı ile yapılandırma sunucusu VM 'sini dağıtır.

- [VM 'ye ek bir bağdaştırıcı ekleyebilirsiniz](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), ancak yapılandırma sunucusunu kasaya kaydetmeden önce eklemeniz gerekir.
- Yapılandırma sunucusunu kasaya kaydettikten sonra bir bağdaştırıcı eklemek için, bağdaştırıcıyı VM özelliklerine ekleyin. Ardından sunucuyu kasaya [yeniden kaydetmeniz](#reregister-a-configuration-server-in-the-same-vault) gerekir.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Aynı kasada bir yapılandırma sunucusunu yeniden kaydetme

Gerekirse, yapılandırma sunucusunu aynı kasada yeniden kaydedebilirsiniz. Ek bir işlem sunucusu makineniz varsa, yapılandırma sunucusu makinesinde çalışan varsayılan işlem sunucusuna ek olarak, her iki makineyi de yeniden kaydettirin.


1. Kasada, **yapılandırma sunucuları** >  > **Site Recovery altyapıyı** **Yönet** ' i açın.
2. **Sunucular**' da, kasa kimlik bilgileri dosyasını indirmek için **kayıt anahtarını indir** ' i seçin.
3. Yapılandırma sunucusu makinesinde oturum açın.
4. **%ProgramData%\asr\home\svsystems\bin**öğesinde, **Cspsconfigtool. exe**dosyasını açın.
5. **Kasa kaydı** sekmesinde, **Araştır**' ı seçin ve indirdiğiniz kasa kimlik bilgileri dosyasını bulun.
6. Gerekirse, proxy sunucu ayrıntılarını sağlayın. Ardından **Kaydet**’i seçin.
7. Bir yönetici PowerShell komut penceresi açın ve aşağıdaki komutu çalıştırın:
   ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

    >[!NOTE]
    >Yapılandırma sunucusundan genişleme işlem sunucusuna **en son sertifikaları çekmek** için *"\<yükleme sürücüsü \Microsoft Azure Site Recovery\yürüt\cdpcli.exe >"--registermt* komutunu yürütün.

8. Son olarak, aşağıdaki komutu yürüterek obengine hizmetine bağlanamıyor 'i yeniden başlatın.
   ```
        net stop obengine
        net start obengine
   ```


## <a name="register-a-configuration-server-with-a-different-vault"></a>Yapılandırma sunucusunu farklı bir kasaya kaydetme

> [!WARNING]
> Aşağıdaki adım yapılandırma sunucusunu geçerli kasadan ilişkilendirir ve yapılandırma sunucusu altındaki tüm korumalı sanal makinelerin çoğaltılması durdurulur.

1. Yapılandırma sunucusunda oturum açın.
2. Bir yönetici PowerShell komut penceresi açın ve aşağıdaki komutu çalıştırın:

    ```
    reg delete "HKLM\Software\Microsoft\Azure Site Recovery\Registration"
    net stop dra
    ```
3. Masaüstünüzdeki kısayolu kullanarak yapılandırma sunucusu gereç tarayıcı portalını başlatın.
4. Yeni bir yapılandırma sunucusu [kaydıyla](vmware-azure-tutorial.md#register-the-configuration-server)benzer kayıt adımlarını gerçekleştirin.

## <a name="upgrade-the-configuration-server"></a>Yapılandırma sunucusunu yükseltme

Yapılandırma sunucusunu güncelleştirmek için güncelleştirme paketleri çalıştırırsınız. Güncelleştirmeler, en fazla N 4 sürüm için uygulanabilir. Örneğin:

- 9,7, 9,8, 9,9 veya 9,10 çalıştırırsanız, doğrudan 9,11 'e yükseltebilirsiniz.
- 9,6 veya daha önceki bir sürümü çalıştırırsanız ve 9,11 sürümüne yükseltmek istiyorsanız, önce sürüm 9,7 ' ye yükseltmeniz gerekir. 9,11 öncesi.

Azure Site Recovery bileşenleri destek bildiriminde ayrıntılı yönergeler için [buraya](https://aka.ms/asr_support_statement)bakın.
Yapılandırma sunucusunun tüm sürümlerine yükseltmeye yönelik güncelleştirme paketlerinin bağlantıları [burada](https://aka.ms/asr_update_rollups)bulunabilir.

> [!IMPORTANT]
> Yayımlanan bir Azure Site Recovery bileşenin her yeni sürümü ' n ' ile, ' N-4 ' in altındaki tüm sürümler destek dışında kabul edilir. Kullanılabilir en son sürümlere yükseltmeniz her zaman önerilir.</br>
> Azure Site Recovery bileşenleri destek bildiriminde ayrıntılı yönergeler için [buraya](https://aka.ms/asr_support_statement)bakın.

Sunucuyu aşağıdaki gibi yükseltin:

1. Kasada, **yapılandırma sunucuları** >  > **Site Recovery altyapıyı** **Yönet** ' e gidin.
2. Bir güncelleştirme varsa, **Aracı sürümü** > sütununda bir bağlantı görüntülenir.
    ![Güncelleştirme](./media/vmware-azure-manage-configuration-server/update2.png)
3. Güncelleştirme yükleyicisi dosyasını yapılandırma sunucusuna indirin.

    ![Güncelleştirme](./media/vmware-azure-manage-configuration-server/update1.png)

4. Yükleyiciyi çalıştırmak için çift tıklayın.
5. Yükleyici, makinede çalışan geçerli sürümü algılar. Yükseltmeyi başlatmak için **Evet** ' e tıklayın.
6. Yükseltme işlemi tamamlandığında sunucu yapılandırması doğrular.

    ![Güncelleştirme](./media/vmware-azure-manage-configuration-server/update3.png)

7. Yükleyiciyi kapatmak için **son** ' a tıklayın.
8. Site Recovery bileşenlerinin geri kalanını yükseltmek için [yükseltme kılavuzumuza](https://aka.ms/asr_vmware_upgrades)bakın.

## <a name="upgrade-configuration-serverprocess-server-from-the-command-line"></a>Yapılandırma sunucusu/işlem sunucusunu komut satırından yükselt

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
|/BypassProxy|İsteğe Bağlı|Yapılandırma sunucusunun Azure'a bir ara sunucu olmadan bağlandığını belirtir|Yapmak için bu değeri Venu’den alın|
|/ProxySettingsFilePath|İsteğe Bağlı|Ara sunucu ayarları (Varsayılan ara sunucu kimlik doğrulaması gerektirir ya da özel bir ara sunucu kullanılır)|Dosya aşağıda belirtilen biçimde olmalıdır|
|DataTransferSecurePort|İsteğe Bağlı|Çoğaltma verileri için kullanılacak PSIP’deki bağlantı noktası numarası| Geçerli Bağlantı Noktası Numarası (varsayılan değer: 9433)|
|/SkipSpaceCheck|İsteğe Bağlı|Önbellek diski için alan denetimini atlama| |
|/AcceptThirdpartyEULA|Gerekli|Bayrak, üçüncü taraf EULA'nın kabul edildiğini gösterir| |
|/ShowThirdpartyEULA|İsteğe Bağlı|Üçüncü taraf EULA belgesini görüntüler. Giriş olarak sağlanırsa, diğer tüm parametreler yoksayılır| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>MYSQLCredsFilePath için dosya girişi oluşturma

MySQLCredsFilePath parametresi bir dosyayı giriş olarak alır. Aşağıdaki biçimi kullanarak dosyayı oluşturun ve giriş MySQLCredsFilePath parametresi olarak geçirin.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password>"
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

## <a name="delete-or-unregister-a-configuration-server"></a>Yapılandırma sunucusunu silme veya kaydını kaldırma

1. Yapılandırma sunucusu altındaki tüm VM 'Ler için [korumayı devre dışı bırakın](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) .
2. Tüm çoğaltma ilkelerinin yapılandırma sunucusundan [Ilişkisini kaldırın](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) ve [silin](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) .
3. Yapılandırma sunucusuyla ilişkili tüm vCenter sunucularını/vSphere konaklarınızı [silin](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) .
4. Kasada, **yapılandırma sunucuları** > **Site Recovery altyapı** ' yı açın.
5. Kaldırmak istediğiniz yapılandırma sunucusunu seçin. Ardından, **Ayrıntılar** sayfasında **Sil**' i seçin.

    ![Yapılandırma sunucusunu Sil](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)


### <a name="delete-with-powershell"></a>PowerShell ile Sil

Configuration sunucusunu isteğe bağlı olarak PowerShell kullanarak silebilirsiniz.

1. Azure PowerShell modülünü [yükler](https://docs.microsoft.com/powershell/azure/install-Az-ps) .
2. Şu komutu kullanarak Azure hesabınızda oturum açın:

    `Connect-AzAccount`
3. Kasa aboneliğini seçin.

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Kasa bağlamını ayarlayın.

    ```
    $vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Yapılandırma sunucusunu alın.

    `$fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Yapılandırma sunucusunu silin.

    `Remove-AzSiteRecoveryFabric -Fabric $fabric [-Force]`

> [!NOTE]
> Yapılandırma sunucusunu zorla silmeye yönelik Remove-AzSiteRecoveryFabric içinde **-zorlama** seçeneğini kullanabilirsiniz.

## <a name="generate-configuration-server-passphrase"></a>Yapılandırma sunucusu parolası oluştur

1. Yapılandırma sunucunuzda oturum açın ve ardından yönetici olarak bir komut istemi penceresi açın.
2. Dizini bin klasörü olarak değiştirmek için, şu komutu çalıştırın **%ProgramData%\ASR\home\svsystems\bin**
3. Parola dosyasını oluşturmak için **genparola. exe-v > MobSvc. parolasını**yürütün.
4. Parolanız, **%ProgramData%\asr\home\svsystems\bin\mobsvc.exe**yolunda bulunan dosyada depolanır.

## <a name="renew-ssl-certificates"></a>SSL sertifikalarını yenileme

Yapılandırma sunucusunda, Mobility hizmetinin, işlem sunucularının ve buna bağlı ana hedef sunucuların etkinliklerini düzenleyen bir yerleşik Web sunucusu vardır. Web sunucusu, istemcilerin kimliğini doğrulamak için bir SSL sertifikası kullanır. Sertifikanın süresi üç yıl sonra dolar ve herhangi bir zamanda yenilenebilir.

### <a name="check-expiry"></a>Son tarihi denetle

2016 Mayıs 'tan önce yapılandırma sunucusu dağıtımları için, sertifika süre sonu bir yıl olarak ayarlanmıştır. Sona ermek üzere bir sertifikanız varsa, aşağıdakiler gerçekleşir:

- Sona erme tarihi iki ay veya daha azsa, hizmet portalda bildirim göndermeye başlar ve e-posta ile (Site Recovery bildirimlerine abone değilseniz).
- Kasa kaynağı sayfasında bir bildirim başlığı görüntülenir. Daha fazla bilgi için başlık ' ı seçin.
- **Şimdi Yükselt** düğmesini görürseniz, ortamınızdaki bazı bileşenlerin 9.4. xxxx. x veya daha yüksek sürümlere yükseltilmediğini belirtir. Sertifikayı yenilemeden önce bileşenleri yükseltin. Eski sürümlerde yenileyemezsiniz.

### <a name="renew-the-certificate"></a>Sertifikayı Yenile

1. Kasada, **Site Recovery altyapı** > **yapılandırma sunucusu**' nu açın. Gerekli yapılandırma sunucusunu seçin.
2. Süre sonu tarihi, **yapılandırma sunucusu durumu**altında görünür.
3. **Sertifikaları yenile**' yi seçin.

## <a name="refresh-configuration-server"></a>Yapılandırma sunucusunu Yenile

1. Azure portal, **Kurtarma Hizmetleri Kasası** 'na gidin > **VMware > fiziksel makineler** & **yapılandırma sunucuları** için > **Site Recovery altyapı** > **yönetin**
2. Yenilemek istediğiniz yapılandırma sunucusuna tıklayın.
3. Seçilen yapılandırma sunucusu ayrıntılarının bulunduğu dikey pencerede **daha fazla** > **sunucuyu Yenile**' ye tıklayın.
4. **Kurtarma Hizmetleri kasasının** altında iş ilerleme durumunu **izleme > ** **Site Recovery işleri** > .

## <a name="failback-requirements"></a>Yeniden çalışma gereksinimleri

Yeniden koruma ve yeniden çalışma sırasında, şirket içi yapılandırma sunucusunun çalışıyor olması ve bağlı durumda olması gerekir. Başarılı bir yeniden çalışma için, geri yüklenmekte olan sanal makinenin yapılandırma sunucusu veritabanında mevcut olması gerekir.

Yapılandırma sunucunuzun düzenli olarak zamanlanmış yedeklemelerini aldığınızdan emin olun. Bir olağanüstü durum oluşursa ve yapılandırma sunucusu kaybolursa, önce yapılandırma sunucusunu bir yedek kopyadan geri yüklemeniz ve geri yüklenen yapılandırma sunucusunun kasada kayıtlı IP adresine sahip olduğundan emin olmanız gerekir. Geri yüklenen yapılandırma sunucusu için farklı bir IP adresi kullanılıyorsa yeniden çalışma çalışmayacaktır.

## <a name="next-steps"></a>Sonraki adımlar

[VMware VM](vmware-azure-tutorial.md) 'lerinin olağanüstü durum kurtarma ayarlarına yönelik öğreticileri Azure 'a gözden geçirin.
