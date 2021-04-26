---
title: Microsoft Azure Kurtarma Hizmetleri (MARS) aracısını yükler
description: Windows makinelerini yedeklemek için Microsoft Azure Kurtarma Hizmetleri (MARS) aracısını yüklemeyi öğrenin.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 3ea48aaa6aad4a51463c4c028ead22f31163f810
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519230"
---
# <a name="install-the-azure-backup-mars-agent"></a>Azure Backup MARS aracısını yükler

Bu makalede Microsoft Azure Kurtarma Hizmetleri (MARS) aracısının nasıl yükleneceği açıklanır. MARS, Azure Backup Aracısı olarak da bilinir.

## <a name="about-the-mars-agent"></a>MARS Aracısı hakkında

Azure Backup, şirket içi makinelerden ve Azure VM 'lerinden dosya, klasör ve sistem durumunu yedeklemek için MARS Aracısı 'nı kullanır. Bu yedeklemeler Azure 'da bir kurtarma hizmetleri kasasında depolanır. Aracıyı çalıştırabilirsiniz:

* Doğrudan şirket içi Windows makineler üzerinde. Bu makineler, doğrudan Azure 'daki bir kurtarma hizmetleri kasasına yedekleyebilir.
* Azure VM yedekleme uzantısıyla Windows 'u yan yana çalıştıran Azure VM 'lerinde. Aracı, sanal makinede belirli dosya ve klasörleri yedekler.
* Microsoft Azure Backup Server (MABS) örneği veya bir System Center Data Protection Manager (DPM) sunucusu. Bu senaryoda, makineler ve iş yükleri MABS veya Data Protection Manager yedekler. Daha sonra MABS veya Data Protection Manager, Azure 'daki bir kasaya yedeklemek için MARS Aracısı 'nı kullanır.

Yedekleme için kullanılabilen veriler, aracının yüklü olduğu yere bağlıdır.

> [!NOTE]
> Genellikle, VM 'de bir Azure Backup uzantısı kullanarak bir Azure VM 'yi yedeklemeniz gerekir. Bu yöntem tüm VM 'yi yedekler. SANAL makinede belirli dosya ve klasörleri yedeklemek istiyorsanız, bu uzantıyı uzantısıyla birlikte MARS aracısını yükleyip kullanın. Daha fazla bilgi için bkz. [yerleşik bir Azure VM yedeklemesi mimarisi](backup-architecture.md#architecture-built-in-azure-vm-backup).

![Yedekleme işlemi adımları](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Başlamadan önce

* [Azure Backup Windows makinelerini yedeklemek IÇIN Mars aracısını nasıl kullandığını](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)öğrenin.
* Bir ikincil MABS veya Data Protection Manager sunucusu üzerinde MARS aracısını çalıştıran [yedekleme mimarisi](backup-architecture.md#architecture-back-up-to-dpmmabs) hakkında bilgi edinin.
* [Desteklenen öğeleri ve Mars Aracısı tarafından neleri yedekleyebileceğinize](backup-support-matrix-mars-agent.md) bakın.
* Bir sunucu veya istemcisini Azure 'a yedeklemeniz gerekiyorsa Azure hesabınız olduğundan emin olun. Hesabınız yoksa yalnızca birkaç dakika içinde [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturabilirsiniz.
* Yedeklemek istediğiniz makinelerde internet erişimini doğrulayın.
* MARS aracısının yüklemesini ve yapılandırmasını gerçekleştiren kullanıcının korunabilecek sunucuda yerel yönetici ayrıcalıklarına sahip olduğundan emin olun.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Depolama çoğaltmasını değiştirme

Varsayılan olarak, [kasalar coğrafi olarak yedekli depolama (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage)kullanır.

* Kasa birincil yedekleme mekanizmanız ise GRS kullanmanızı öneririz.
* Azure depolama maliyetlerini azaltmak için [yerel olarak yedekli depolama (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) kullanabilirsiniz.

Depolama çoğaltma türünü değiştirmek için:

1. Yeni kasada, **Ayarlar** bölümünün altındaki **Özellikler** ' i seçin.

1. **Özellikler** sayfasında, **yedekleme yapılandırması** altında **Güncelleştir**' i seçin.

1. Depolama çoğaltma türünü seçin ve **Kaydet**' i seçin.

    ![Yedekleme yapılandırmasını Güncelleştir](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Kasa ayarlandıktan ve yedekleme öğeleri içerdiğinde depolama çoğaltma türünü değiştiremezsiniz. Bunu yapmak istiyorsanız, kasayı yeniden oluşturmanız gerekir.
>

### <a name="verify-internet-access"></a>İnternet erişimini doğrulama

Makinenizin internet erişimi sınırlı ise, makinedeki veya proxy 'deki güvenlik duvarı ayarlarının aşağıdaki URL 'Lere ve IP adreslerine izin verildiğinden emin olun:

* URL’ler
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP adresleri
  * 20.190.128.0/18
  * 40.126.0.0/18

### <a name="use-azure-expressroute"></a>Azure ExpressRoute kullanma

Azure ExpressRoute üzerinden verilerinizi, genel eşlemeyi (eski devreler için kullanılabilir) ve Microsoft eşlemesi 'ni kullanarak yedekleyebilirsiniz. Özel eşleme üzerinde yedekleme desteklenmez.

Ortak eşlemeyi kullanmak için önce aşağıdaki etki alanlarına ve adreslere erişim sağlayın:

* `http://www.msftncsi.com/ncsi.txt`
* `http://www.msftconnecttest.com/connecttest.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`
* IP adresleri
  * 20.190.128.0/18
  * 40.126.0.0/18

Microsoft eşlemesini kullanmak için aşağıdaki hizmetleri, bölgeleri ve ilgili topluluk değerlerini seçin:

* Azure Active Directory (12076:5060)
* Kurtarma Hizmetleri kasanızın konumuna göre Azure bölgesi
* Azure depolama, kurtarma hizmetleri kasanızın konumuna göre

Daha fazla bilgi için bkz. [ExpressRoute yönlendirme gereksinimleri](../expressroute/expressroute-routing.md).

> [!NOTE]
> Ortak eşleme, yeni devreler için kullanım dışıdır.

Önceki URL 'Ler ve IP adresleri, 443 numaralı bağlantı noktasında HTTPS protokolünü kullanır.

### <a name="private-endpoints"></a>Özel Uç Noktalar

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>MARS aracısını indirin

Yedeklemek istediğiniz makinelere yükleyebilmek için MARS aracısını indirin.

Aracıyı herhangi bir makineye zaten yüklediyseniz aracının en son sürümünü çalıştırdığınızdan emin olun. Portalda en son sürümü bulun veya doğrudan [indirmeye](https://aka.ms/azurebackup_agent)gidin.

1. Kasadaki **Başlarken**' ın altında **Yedekle**' yi seçin.

    ![Yedekleme hedefini açın](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. **İş yükünüz nerede çalışıyor?** altında **Şirket içi**' ı seçin. MARS aracısını bir Azure VM 'ye yüklemek istiyor olsanız bile bu seçeneği belirleyin.
1. **Neleri yedeklemek istiyorsunuz?** altında **Dosyalar ve klasörler**' i seçin. Ayrıca **sistem durumu**' nu da seçebilirsiniz. Birçok diğer seçenek mevcuttur, ancak bu seçenekler yalnızca ikincil bir yedekleme sunucusu çalıştırıyorsanız desteklenir. **Altyapıyı hazırla**' yı seçin.

    ![Dosyaları ve klasörleri yapılandırma](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. **Altyapıyı hazırlama** bölümünde, **Kurtarma Hizmetleri ARACıSı yükleme** altında Mars aracısını indirin.

    ![Altyapıyı hazırlama](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. İndir menüsünde **Kaydet**' i seçin. Varsayılan olarak, *MARSagentinstaller.exe* dosyası İndirilenler klasörünüze kaydedilir.

1. **Zaten indirmeyi seçin veya en son kurtarma hizmetleri aracısını** kullanın ve ardından kasa kimlik bilgilerini indirin.

    ![Kasa kimlik bilgilerini indirme](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. **Kaydet**’i seçin. Dosya, Indirmeler klasörünüze indirilir. Kasa kimlik bilgileri dosyasını açamazsınız.

## <a name="install-and-register-the-agent"></a>Aracıyı yükleme ve kaydetme

1. Yedeklemek istediğiniz makinelerde *MARSagentinstaller.exe* dosyasını çalıştırın.
1. MARS Aracısı kurulum sihirbazında, **yükleme ayarları**' nı seçin. Burada, aracının yükleneceği yeri seçin ve önbellek için bir konum seçin. Sonra **İleri**’yi seçin.
   * Azure Backup, veri anlık görüntülerini Azure 'a göndermeden önce depolamak için önbelleği kullanır.
   * Önbellek konumunda, yedeklediğiniz verilerin boyutunun en az %5 ' i kadar boş alan olmalıdır.

    ![MARS Aracısı kurulum sihirbazında yükleme ayarlarını seçin](./media/backup-configure-vault/mars1.png)

1. **Ara sunucu yapılandırması** Için, Windows makinesinde çalışan aracının internet 'e nasıl bağlanacağını belirtin. Sonra **İleri**’yi seçin.

   * Özel bir proxy kullanıyorsanız, gerekli tüm proxy ayarlarını ve kimlik bilgilerini belirtin.
   * Aracının [belirli URL 'lere](#before-you-start)erişmesi gerektiğini unutmayın.

    ![MARS sihirbazında Internet erişimini ayarlama](./media/backup-configure-vault/mars2.png)

1. **Yükleme** için önkoşulları gözden geçirin ve **yükleme**' yi seçin.
1. Aracı yüklendikten sonra, **kayda devam et**' i seçin.
1. **Sunucu kaydetme Sihirbazı**  >  **kasa kimliği**' nde, indirdiğiniz kimlik bilgileri dosyasına gidin ve seçin. Sonra **İleri**’yi seçin.

    ![Sunucu kaydetme Sihirbazı 'Nı kullanarak kasa kimlik bilgileri ekleme](./media/backup-configure-vault/register1.png)

1. **Şifreleme ayarı** sayfasında, makinenin yedeklemelerini şifrelemek ve şifresini çözmek için kullanılacak bir parola belirtin. İzin verilen parola karakterleri hakkında daha fazla bilgi için [buraya bakın](backup-azure-file-folder-backup-faq.yml#what-characters-are-allowed-for-the-passphrase-) .

    * Parolayı güvenli bir konuma kaydedin. Bir yedeklemeyi geri yüklemeniz gerekir.
    * Parolayı kaybeder veya unutursanız, Microsoft, yedekleme verilerini kurtarmanıza yardımcı olamaz.

1. **Son**'u seçin. Aracı Şimdi yüklendi ve makineniz kasaya kayıtlı. Yedeklemenizi yapılandırıp zamanlamak için hazırsınız.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Backup Mars Aracısı 'nı kullanarak Windows makinelerini nasıl yedekleyeceğinizi](backup-windows-with-mars-agent.md) öğrenin
