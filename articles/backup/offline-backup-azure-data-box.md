---
title: Azure Data Box kullanarak çevrimdışı yedekleme
description: MARS aracısından bir kurtarma hizmetleri kasasına çevrimdışı olan büyük ilk yedekleme verilerini temel alarak Azure Data Box nasıl kullanabileceğinizi öğrenin.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 78adc479ce5733e208d2334d30d7b88e4edf8d6b
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576100"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Azure Data Box kullanarak çevrimdışı yedekleme Azure Backup

Büyük ilk Microsoft Azure Kurtarma Hizmetleri (MARS) yedeklemelerinizi bir kurtarma hizmetleri kasasına çevrimdışı olarak (ağ kullanmadan) temel almak için [Azure Data Box](../databox/data-box-overview.md) kullanabilirsiniz. Bu işlem, büyük miktarlarda yedekleme verilerinin yüksek gecikmeli bir ağ üzerinden çevrimiçi olarak harcanmasına neden olacak zaman ve ağ bant genişliğini kaydeder. Bu geliştirme şu anda önizleme aşamasındadır. Azure Data Box tabanlı çevrimdışı yedekleme [, Azure içeri/dışarı aktarma hizmeti 'ni temel alan çevrimdışı yedekleme](./backup-azure-backup-import-export.md)üzerinde iki ayrı avantaj sağlar:

- Azure ile uyumlu disklerinizi ve bağlayıcılarınızı temin etmeniz gerekmez. Azure Data Box seçili [Data Box SKU 'su](https://azure.microsoft.com/services/databox/data/)ile ilişkili diskleri sevk eder.
- Azure Backup (MARS Aracısı), Azure Data Box desteklenen SKU 'Lara doğrudan yedekleme verileri yazabilir. Bu özellik, ilk yedekleme verileriniz için bir hazırlama konumu sağlamanıza yönelik gereksinimi ortadan kaldırır. Ayrıca, bu verileri disklere göre biçimlendirmek ve kopyalamak için yardımcı programlara gerek kalmaz.

## <a name="azure-data-box-with-the-mars-agent"></a>MARS Aracısı ile Azure Data Box

Bu makalede, büyük ilk yedekleme verilerini MARS aracısından bir kurtarma hizmetleri kasasına çevrimdışına almak için Azure Data Box nasıl kullanabileceğiniz açıklanmaktadır.

## <a name="supported-platforms"></a>Desteklenen platformlar

Azure Data Box kullanarak MARS aracısından verileri temel alan işlem, aşağıdaki Windows SKU 'Larında desteklenir.

| **İşletim sistemi**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **İş İstasyonu**                        |                                                              |
| Windows 10 64 bit                     | Enterprise, Pro, Home                                       |
| Windows 8.1 64 bit                    | Enterprise, Pro                                             |
| Windows 8 64 bit                      | Enterprise, Pro                                             |
| Windows 7 64 bit                      | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| **Sunucu**                             |                                                              |
| Windows Server 2019 64 bit            | Standard, Datacenter, Essentials                            |
| Windows Server 2016 64 bit            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2 64 bit         | Standard, Datacenter, Foundation                            |
| Windows Server 2012 64 bit            | Datacenter, Foundation, Standard                            |
| Windows Storage Server 2016 64 bit    | Standard, Workgroup                                         |
| Windows Storage Server 2012 R2 64 bit | Standard, Workgroup, Essential                              |
| Windows Storage Server 2012 64 bit    | Standard, Workgroup                                         |
| Windows Server 2008 R2 SP1 64 bit     | Standard, Enterprise, Datacenter, Foundation                |
| Windows Server 2008 SP2 64 bit        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Yedekleme veri boyutu ve desteklenen Data Box SKU 'Lar

| Yedekleme veri boyutu (MARS tarafından sıkıştırma sonrası) * sunucu başına | Desteklenen Azure Data Box SKU 'SU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <= 7,2 TB                                                    | [Azure Data Box disk](../databox/data-box-disk-overview.md) |
| >7,2 TB ve <= 80 TB * *                                      | [Azure Data Box (100 TB)](../databox/data-box-overview.md) |

* Tipik sıkıştırma ücretleri %10 ile %20 arasında değişir. <br>
* * Tek bir MARS sunucusu için 80 TB 'den fazla ilk yedekleme verisi olmasını bekleliyorsanız, iletişim kurun [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

>[!IMPORTANT]
>Tek bir sunucudan ilk yedekleme verileri tek bir Azure Data Box örneği veya Azure Data Box disk içinde bulunmalıdır ve aynı veya farklı SKU 'ların birden çok cihazı arasında paylaşılamaz. Ancak bir Azure Data Box cihaz, birden çok sunucudan ilk yedeklemeleri içerebilir.

## <a name="prerequisites"></a>Önkoşullar

### <a name="azure-subscription-and-required-permissions"></a>Azure aboneliği ve gerekli izinler

- İşlem için bir Azure aboneliği gerekir.
- Bu işlem, kullanıcının çevrimdışı yedekleme ilkesini gerçekleştirmek için belirlenmiş bir Azure aboneliğinin sahibi olmasını gerektirir.
- Data Box işi ve kurtarma hizmetleri kasasının (verilerin sağlaması gereken) aynı aboneliklerde olması gerekir.
- Azure Data Box işiyle ve kurtarma hizmetleri kasasıyla ilişkili hedef depolama hesabının aynı bölgede olması önerilir. Ancak, bu gerekli değildir.

### <a name="get-azure-powershell-370"></a>Azure PowerShell 3.7.0 al

*Bu işlem için en önemli önkoşuldur*. Sürüm 3.7.0 Azure PowerShell yüklemeden önce aşağıdaki denetimleri gerçekleştirin.

#### <a name="step-1-check-the-powershell-version"></a>1. Adım: PowerShell sürümünü denetleme

1. Windows PowerShell 'i açın ve aşağıdaki komutu çalıştırın:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1. Çıkış 3.7.0 'den daha yüksek bir sürüm görüntülüyorsa, "2. adım" yapın. Aksi takdirde, "Adım 3" e atlayın.

#### <a name="step-2-uninstall-the-powershell-version"></a>2. Adım: PowerShell sürümünü kaldırma

Geçerli PowerShell sürümünü kaldırın.

1. PowerShell 'de aşağıdaki komutu çalıştırarak bağımlı modülleri kaldırın:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. Tüm bağımlı modüllerin başarıyla silinmesini sağlamak için şu komutu çalıştırın:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>3. Adım: PowerShell sürüm 3.7.0 'Yi yükler

Azurerk modülleri bulunmadığını doğruladıktan sonra, aşağıdaki yöntemlerden birini kullanarak sürüm 3.7.0 ' yi yükleyebilirsiniz:

- GitHub 'dan [Bu bağlantıyı](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)kullanın.

İsterseniz şunları yapabilirsiniz:

- PowerShell penceresinde aşağıdaki komutu çalıştırın:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell, MSI dosyası kullanılarak da yüklenmiş olabilir. Kaldırmak için, Denetim Masası 'ndaki **programları** Kaldır seçeneğini kullanarak kaldırın.

### <a name="order-and-receive-the-data-box-device"></a>Data Box cihazı sıralama ve alma

MARS Aracısı 'nı kullanarak çevrimdışı yedeklemeyi tetiklemeniz için, MARS ve Azure Data Box kullanan çevrimdışı yedekleme işlemi, Data Box cihazların teslim edilmiş durumda olmasını gerektirir. Gereksiniminize yönelik en uygun SKU 'YU sıralamak için bkz. [yedekleme veri boyutu ve desteklenen Data Box SKU 'lar](#backup-data-size-and-supported-data-box-skus). Öğreticideki adımları izleyin: Data Box cihazlarınızı sıralamak ve almak için [Azure Data Box diski sıralama](../databox/data-box-disk-deploy-ordered.md) .

> [!IMPORTANT]
> **Hesap türü** Için *blobstorage* ' ı seçmeyin. MARS Aracısı, *Blobstorage* seçildiğinde desteklenmeyen sayfa bloblarını destekleyen bir hesap gerektirir. Azure Data Box işiniz için hedef depolama hesabı oluşturduğunuzda, **Hesap türü** olarak **Storage v2 'yi (genel amaçlı v2)** seçin.

![Örnek ayrıntılarında hesap türünü seçin](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>MARS aracısını yükleyip ayarlama

1. MARS aracısının önceki tüm yüklemelerini kaldırdığınızdan emin olun.
1. [Bu Web sitesinden](https://aka.ms/azurebackup_agent)en son Mars aracısını indirin.
1. *MARSAgentInstaller.exe* çalıştırın ve yalnızca yedeklemelerin depolanmasını Istediğiniz kurtarma hizmetleri kasasında [aracıyı yüklemek ve kaydetmek](./install-mars-agent.md#install-and-register-the-agent) için *yalnızca* adımları uygulayın.

   > [!NOTE]
   > Kurtarma Hizmetleri Kasası, Azure Data Box işle aynı abonelikte olmalıdır.

   Aracı kurtarma hizmetleri kasasına kaydedildikten sonra, sonraki bölümlerde bulunan adımları izleyin.

## <a name="set-up-azure-data-box-devices"></a>Azure Data Box cihazları ayarlama

Sipariş ettiğiniz Azure Data Box SKU 'suna bağlı olarak, aşağıdaki uygun bölümlerde anlatılan adımları uygulayın. Adımlarda, ilk yedekleme verilerini tanımlamak ve aktarmak için MARS aracısına yönelik Data Box cihazlarının nasıl ayarlanacağı ve hazırlanacağı gösterilmektedir.

### <a name="set-up-azure-data-box-disks"></a>Azure Data Box diskleri ayarlama

Bir veya daha fazla Azure Data Box disk sipariş ederseniz (her biri 8 TB 'a kadar), [Data Box diskinizin paketini açmak, bağlanmak ve kilidini açmak](../databox/data-box-disk-deploy-set-up.md)için burada bahsedilen adımları izleyin.

>[!NOTE]
>MARS aracısına sahip sunucu bir USB bağlantı noktasına sahip olmayabilir. Bu durumda, Azure Data Box diskinizi başka bir sunucuya veya istemciye bağlayabilirsiniz ve cihazın kökünü bir ağ paylaşma olarak kullanıma sunabilirsiniz.

### <a name="set-up-azure-data-box"></a>Azure Data Box ayarlama

Bir Azure Data Box örneği (100 TB 'a kadar) sipariş ederseniz, [Data Box örneğinizi ayarlamak için](../databox/data-box-deploy-set-up.md)buradaki adımları izleyin.

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Azure Data Box örneğinizi yerel bir sistem olarak bağlama

MARS Aracısı yerel sistem bağlamında çalışır, bu nedenle Azure Data Box örneğinin bağlı olduğu bağlama yoluna aynı ayrıcalık düzeyinin sağlanması gerekir.

NFS protokolünü kullanarak Data Box cihazınızı yerel bir sistem olarak bağlayabilmeniz için:

1. MARS aracısının yüklü olduğu Windows Server 'da NFS özelliği istemcisini etkinleştirin. Alternatif kaynak *WIM: D: \Sources\ınstall.exe: 4*' i belirtin.
1. [Sysinternals](/sysinternals/downloads/psexec) sayfasından PsExec ' i Mars aracısının yüklü olduğu sunucuya indirin.
1. Yükseltilmiş bir komut istemi açın ve geçerli dizin olarak *PSExec.exe* içeren dizin ile aşağıdaki komutu çalıştırın.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   Önceki komut nedeniyle açılan komut penceresi yerel sistem bağlamında. Azure Sayfa Blobu paylaşımından bir ağ sürücüsü olarak Windows Server 'a bağlama adımlarını yürütmek için bu komut penceresini kullanın.
1. Sunucunuzu bir Data Box cihazına NFS aracılığıyla bağlamak için [Data Box bağlanma](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box) bölümündeki adımları izleyin. Azure sayfa Blobları payını bağlamak için yerel sistem komut isteminde aşağıdaki komutu çalıştırın.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   Paylaşma bağlandıktan sonra, sunucunuza X: erişiminiz olup olmadığını kontrol edin. Bunu yapmak için, bu makalenin sonraki bölümüne devam edin.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>İlk yedekleme verilerini Azure Data Box cihazlara aktarma

1. **Microsoft Azure Backup** uygulamanızı sunucunuzda açın.
1. **Eylemler** bölmesinde, **yedeklemeyi zamanla**' yı seçin.

    ![Yedeklemeyi Zamanla ' yı seçin](./media/offline-backup-azure-data-box/schedule-backup.png)

1. **Yedekleme zamanlaması Sihirbazı**'ndaki adımları izleyin.

1. Öğe **Ekle** düğmesini seçerek öğe ekleyin. Öğelerin toplam boyutunu, sipariş ettiğiniz ve aldığınız [Azure Data Box SKU 'su tarafından desteklenen boyut sınırları](#backup-data-size-and-supported-data-box-skus) içinde tutun.

    ![Yedeklenecek öğeleri ekle](./media/offline-backup-azure-data-box/add-items.png)

1. **Dosyalar ve klasörler** ve **sistem durumu** için uygun yedekleme zamanlamasını ve bekletme ilkesini seçin. Sistem durumu, Windows istemcileri için değil yalnızca Windows Server 'Lar için geçerlidir.
1. Sihirbazın **Ilk yedekleme türünü (dosyalar ve klasörler) seçin** sayfasında, **Microsoft Azure Data Box disklerini kullanarak aktar** seçeneğini belirleyin ve **İleri ' yi** seçin.

    ![İlk yedekleme türünü seçin](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. Azure aboneliğinde sahip erişimi olan kullanıcı kimlik bilgilerini kullanarak istendiğinde Azure 'da oturum açın. Bunu yaptıktan sonra, buna benzer bir sayfa görmeniz gerekir.

    ![Kaynak oluşturma ve gerekli izinleri uygulama](./media/offline-backup-azure-data-box/creating-resources.png)

   MARS Aracısı daha sonra, teslim edilen durumda olan abonelikte Data Box işleri getirir.

    ![Abonelik KIMLIĞI için Data Box işleri getir](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Data Box diskinizin yüklendiği, bağlandığı ve kilidinin açılacağı doğru Data Box sırasını seçin. **İleri**’yi seçin.

    ![Data Box siparişleri seçin](./media/offline-backup-azure-data-box/select-databox-order.png)

1. **Data Box cihaz algılama** sayfasında **cihazı Algıla** ' yı seçin. Bu eylem, MARS aracısının yerel olarak bağlı Azure Data Box diskleri taramasını ve bunları algılamasını sağlar.

    ![Cihaz algılamayı Data Box](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Azure Data Box örneğini bir ağ sürümü olarak bağladıysanız (USB bağlantı noktası kullanılamamasından veya 100-TB Data Box cihazını sipariş ettiğiniz ve bağladığınız için), ilk olarak algılama başarısız olur. Data Box cihazının ağ yolunu girme seçeneği verilir.

    ![Ağ yolunu girin](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Azure Data Box diskinin kök dizinine ağ yolunu sağlayın. Bu dizin, *Pageblob* adlı bir dizin içermelidir.
    >
    >![Azure Data Box diskin kök dizini](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Örneğin, diskin yolu `\\mydomain\myserver\disk1\` ve *Disk1* , *pageblob* ADLı bir dizin içeriyorsa, Mars Aracısı sihirbaz sayfasında girdiğiniz yol olur `\\mydomain\myserver\disk1\` .
    >
    >[Azure Data Box 100-TB bir cihaz ayarlarsanız](#set-up-azure-data-box-devices), `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` cihaza ağ yolu olarak girin.

1. **İleri**' yi seçin ve Azure Data Box kullanarak yedekleme ve bekletme ilkesini çevrimdışı yedeklemenin yapılandırmasıyla kaydetmek için sonraki sayfada **son** ' u seçin.

   Aşağıdaki sayfa, ilkenin başarıyla kaydedildiğini onaylar.

    ![İlke başarıyla kaydedildi](./media/offline-backup-azure-data-box/policy-saved.png)

1. Önceki sayfada **Kapat** ' ı seçin.

1. MARS Aracısı konsolunun **Eylemler** bölmesinde **Şimdi Yedekle** ' yi seçin. Sihirbaz sayfasında **Yedekle** ' yi seçin.

    ![Şimdi Yedekle Sihirbazı](./media/offline-backup-azure-data-box/backup-now.png)

MARS Aracısı, seçtiğiniz verileri Azure Data Box cihazına yedeklemeye başlar. Bu işlem birkaç saatten birkaç güne kadar sürebilir. Süre miktarı, MARS Aracısı ve Azure Data Box diski ile sunucu arasındaki dosya ve bağlantı hızı sayısına bağlıdır.

Verilerin yedeklenmesi tamamlandıktan sonra, MARS aracısında buna benzeyen bir sayfa görürsünüz.

![Yedekleme ilerlemesi gösteriliyor](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Yedekleme sonrası adımlar

Bu bölümde, verilerin yedeklenmesi Azure Data Box Disk başarılı olduktan sonra gerçekleştirilecek adımlar açıklanmaktadır.

- [Azure Data Box diskini Azure 'a göndermek](../databox/data-box-disk-deploy-picked-up.md)için bu makaledeki adımları izleyin. Azure Data Box 100-TB bir cihaz kullandıysanız, [Azure Data Box cihazını Azure 'a](../databox/data-box-deploy-picked-up.md)göndermek için aşağıdaki adımları izleyin.

- Azure portal [Data Box Işini izleyin](../databox/data-box-disk-deploy-upload-verify.md) . Azure Data Box işi tamamlandıktan sonra, MARS Aracısı verileri depolama hesabından otomatik olarak kurtarma hizmetleri kasasından bir sonraki zamanlanmış yedekleme sırasında taşımalıdır. Ardından, bir kurtarma noktası başarıyla oluşturulduysa yedekleme işini *Iş tamamlandı* olarak işaretler.

    >[!NOTE]
    >MARS Aracısı, yedeklemeleri ilke oluşturma sırasında zamanlandığı zamanlarda tetikler. Bu işler, iş tamamlanana kadar "Azure Data Box işin tamamlanmasını bekleniyor" bayrağı.

- MARS Aracısı ilk yedeklemeye karşılık gelen bir kurtarma noktasını başarıyla oluşturduktan sonra, Azure Data Box işiyle ilişkili depolama hesabını veya belirli içerikleri silebilirsiniz.

## <a name="troubleshooting"></a>Sorun giderme

Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı kiracınızda sizin için bir Azure Active Directory (Azure AD) uygulaması oluşturur. Bu uygulama, çevrimdışı bir dengeli dağıtım ilkesi yapılandırdığınızda oluşturulup karşıya yüklenen kimlik doğrulaması için bir sertifika gerektirir. Sertifikayı oluşturmak ve Azure AD uygulamasına yüklemek için Azure PowerShell kullanırız.

### <a name="problem"></a>Sorun

Çevrimdışı yedeklemeyi yapılandırırken, Azure PowerShell cmdlet 'inde bir hata nedeniyle sorun olabilirsiniz. MAB Aracısı tarafından oluşturulan aynı Azure AD uygulamasına birden çok sertifika ekleyemeyebilirsiniz. Bu sorun, aynı veya farklı bir sunucu için çevrimdışı bir dengeli dağıtım ilkesi yapılandırdıysanız sizi etkiler.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>Sorunun bu özel kök nedenin kaynaklanıp kaynaklanmadığını doğrulayın

Sorununuzun daha önce açıklananla aynı olup olmadığını görmek için aşağıdaki adımlardan birini yapın.

#### <a name="step-1-of-verification"></a>Doğrulama 1. adım

Çevrimdışı yedeklemeyi yapılandırdığınız zaman MAB konsolunda aşağıdaki hata iletisinin görünüp göründüğünü denetleyin.

![Geçerli Azure hesabı için çevrimdışı yedekleme ilkesi oluşturulamıyor](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2-of-verification"></a>Doğrulama adım 2

1. Yükleme yolundaki **geçici** klasörü açın. Varsayılan Temp klasörü yolu, *C:\Program Files\Microsoft Azure Recovery Services* SSIS \ temp ' dir. *Cbuicurr* dosyasını bulun ve dosyayı açın.

1. *Cbuicurr* dosyasında son satıra ilerleyin ve sorunun bu hata iletisiyle aynı olup olmadığını denetleyin: `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed` .

### <a name="workaround"></a>Geçici çözüm

Bu sorunu çözmeye yönelik bir geçici çözüm olarak, aşağıdaki adımları uygulayın ve ilke yapılandırmasını yeniden deneyin.

#### <a name="step-1-of-workaround"></a>Geçici çözüm 1. adım

Data Box işi oluşturulan abonelikte yönetici erişimine sahip farklı bir hesap kullanarak MAB Kullanıcı arabiriminde görünen PowerShell 'de oturum açın.

#### <a name="step-2-of-workaround"></a>Adım 2/çözüm

Başka hiçbir sunucuda, çevrimdışı dengeli dağıtım yapılandırılmamışsa ve uygulamaya bağımlı başka bir sunucu yoksa `AzureOfflineBackup_<Azure User Id>` , bu uygulamayı silin. **Azure Portal**  >  **Azure Active Directory**  >  **uygulama kayıtları**' yı seçin.

>[!NOTE]
> `AzureOfflineBackup_<Azure User Id>`Uygulamanın başka bir çevrimdışı dengeli dağıtım olup olmadığını ve bu uygulamaya bağlı başka bir sunucu yoksa da denetleyin.   >  **Ortak anahtarlar** bölümü altında ayarlar **anahtarlar** ' a gidin. Başka hiçbir ortak anahtar eklenmelidir. Başvuru için aşağıdaki ekran görüntüsüne bakın.
>
>![Ortak anahtarlar](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>3. Adım

Çevrimdışı yedekleme için yapılandırmayı denediğiniz sunucudan aşağıdaki eylemleri gerçekleştirin.

1. **Bilgisayar sertifikasını Yönet uygulama**  >  **Kişisel** sekmesine gidin ve adı olan sertifikayı arayın `CB_AzureADCertforOfflineSeeding_<Timestamp>` .

2. Sertifikayı seçin, **Tüm görevler**' e sağ tıklayın ve. cer biçiminde özel anahtar olmadan **dışarı aktar** ' ı seçin.

3. 2. adımda bahsedilen Azure çevrimdışı yedekleme uygulamasına gidin. **Ayarlar**  >  **anahtarlar**  >  **ortak anahtarı karşıya yükle ' yi** seçin. Önceki adımda verdiğiniz sertifikayı karşıya yükleyin.

    ![Ortak anahtarı karşıya yükle](./media/offline-backup-azure-data-box/upload-public-key.png)

4. Sunucusunda, Çalıştır penceresine **Regedit** ' i girerek kayıt defterini açın.

5. Kayıt defteriComputer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider gidin *.* **CloudBackupProvider**' a sağ tıklayın ve adıyla yeni bir dize değeri ekleyin `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Azure kullanıcı KIMLIĞINI almak için şu eylemlerden birini gerçekleştirin:
    >
    >- Azure bağlı PowerShell 'den `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` komutunu çalıştırın.
    > - `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` *Currentuserıd* adlı kayıt defteri yoluna gidin.

6. Önceki adımda eklenen dizeye sağ tıklayın ve **Değiştir**' i seçin. Değerde, adım 2 ' de verdiğiniz sertifikanın parmak izini girin. **Tamam**’ı seçin.

7. Parmak izi değerini almak için, sertifikaya çift tıklayın. **Ayrıntılar** sekmesini seçin ve parmak izi alanını görene kadar aşağı kaydırın. **Parmak izi**' ni seçin ve değeri kopyalayın.

    ![Sertifikanın parmak izi alanı](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Sorular

Karşılaştığınız sorunlar hakkında sorularınız veya açıklamalar için, iletişim kurun [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .
