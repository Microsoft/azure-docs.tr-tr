---
title: Azure Içeri/dışarı aktarma hizmeti ile çekirdek çevrimdışı yedekleme
description: Azure Içeri/dışarı aktarma hizmetini kullanarak ağdan veri göndermek için Azure Backup nasıl kullanabileceğinizi öğrenin. Bu makalede, Azure Içeri/dışarı aktarma hizmeti kullanılarak ilk yedekleme verilerinin çevrimdışı dengeli dağıtımı açıklanır.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 3ea470c2e732b7e0ef46e9e5fa78c744aa30c955
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98704372"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Azure Backup çevrimdışı yedekleme iş akışı

Azure Backup, Azure 'a verilerin ilk tam yedeklemesi sırasında ağ ve depolama maliyetlerini kaydetmekle ilgili çeşitli yerleşik verimlilik içerir. İlk tam yedeklemeler genellikle büyük miktarda veriyi aktarır ve yalnızca deltas/artımlarsa aktarımı yapan sonraki yedeklemelerle karşılaştırıldığında daha fazla ağ bant genişliği gerektirir. Azure Backup çevrimdışı dağıtım sürecinde, çevrimdışı yedekleme verilerini Azure 'a yüklemek için diskleri kullanabilir.

Azure Backup çevrimdışı dengeli dağıtım işlemi, [Azure içeri/dışarı aktarma hizmeti](../import-export/storage-import-export-service.md)ile sıkı bir şekilde tümleşiktir. Bu hizmeti kullanarak, diskleri kullanarak ilk yedekleme verilerini Azure 'a aktarabilirsiniz. Yüksek gecikmeli ve düşük bant genişliğine sahip bir ağ üzerinden aktarılması gereken ilk yedekleme verilerinin terabayt (TBs) varsa, ilk yedekleme kopyasını bir veya daha fazla sabit sürücüde bir Azure veri merkezine göndermek için çevrimdışı dengeli dağıtım iş akışını kullanabilirsiniz. Aşağıdaki görüntüde, iş akışındaki adımlara bir genel bakış sunulmaktadır.

  ![Çevrimdışı içeri aktarma iş akışı işlemine genel bakış](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Çevrimdışı yedekleme işlemi aşağıdaki adımları içerir:

1. Yedekleme verilerini ağ üzerinden göndermek yerine, yedekleme verilerini bir hazırlama konumuna yazın.
1. Hazırlama konumundaki verileri bir veya daha fazla SATA diskine yazmak için *AzureOfflineBackupDiskPrep* yardımcı programını kullanın.
1. Hazırlık işinin bir parçası olarak, *AzureOfflineBackupDiskPrep* yardımcı programı bir Azure içeri aktarma işi oluşturur. SATA sürücüleri en yakın Azure veri merkezine gönderin ve etkinlikleri bağlamak için içeri aktarma işine başvurun.
1. Azure veri merkezinde, disklerdeki veriler bir Azure depolama hesabına kopyalanır.
1. Azure Backup, yedekleme verilerini depolama hesabından kurtarma hizmetleri kasasına kopyalar ve artımlı yedeklemeler zamanlanır.

## <a name="supported-configurations"></a>Desteklenen yapılandırmalar

Aşağıdaki Azure Backup özellikleri veya iş yükleri, için çevrimdışı yedeklemenin kullanımını destekler:

> [!div class="checklist"]
>
> * Azure Backup Aracısı olarak da adlandırılan Microsoft Azure Kurtarma Hizmetleri (MARS) aracısıyla dosya ve klasörlerin yedeklenmesi.
> * Tüm iş yüklerini ve dosyaları System Center Data Protection Manager (DPM) ile yedekleyin.
> * Tüm iş yüklerini ve dosyaları Microsoft Azure Backup sunucusuna yedekleyin.

   > [!NOTE]
   > Çevrimdışı yedekleme, Azure Backup Aracısı kullanılarak gerçekleştirilen sistem durumu yedeklemeleri için desteklenmez.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Önkoşullar

  > [!NOTE]
  > Aşağıdaki Önkoşullar ve iş akışı yalnızca [en son Azure kurtarma hizmetleri Aracısı](https://aka.ms/azurebackup_agent)kullanılarak dosya ve klasörlerin çevrimdışı yedeklemesi için geçerlidir. System Center DPM veya Azure Backup Sunucusu kullanarak iş yükleri için çevrimdışı yedeklemeler gerçekleştirmek için bkz. [DPM ve Azure Backup sunucusu Için çevrimdışı yedekleme iş akışı](backup-azure-backup-server-import-export.md).

Çevrimdışı yedekleme iş akışını başlamadan önce, aşağıdaki önkoşulları izleyin:

* Bir [Kurtarma Hizmetleri Kasası](backup-azure-recovery-services-vault-overview.md)oluşturun. Kasa oluşturmak için [Kurtarma Hizmetleri Kasası oluşturma](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)bölümündeki adımları izleyin.
* [Azure Backup aracısının yalnızca en son sürümünün](https://aka.ms/azurebackup_agent) Windows Server veya Windows istemcisinde yüklü olduğundan ve bilgisayarın kurtarma hizmetleri kasasına kaydedildiğinden emin olun.
* Azure Backup Aracısı 'nı çalıştıran bilgisayarda Azure PowerShell 3.7.0 gereklidir. [Azure PowerShell 3.7.0 sürümünü indirip yükleyin](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* Azure Backup Aracısı 'nı çalıştıran bilgisayarda, Microsoft Edge veya Internet Explorer 11 ' in yüklü olduğundan ve JavaScript 'in etkinleştirildiğinden emin olun.
* Kurtarma Hizmetleri kasasıyla aynı abonelikte bir Azure depolama hesabı oluşturun.
* Azure Active Directory uygulamasını oluşturmak için [gerekli izinlere](../active-directory/develop/howto-create-service-principal-portal.md) sahip olduğunuzdan emin olun. Çevrimdışı yedekleme iş akışı, Azure depolama hesabıyla ilişkili abonelikte bir Azure Active Directory uygulaması oluşturur. Uygulamanın amacı, Azure Içeri/dışarı aktarma hizmeti 'ne güvenli ve kapsamlı erişim ile Azure Backup sağlamaktır, bu da çevrimdışı yedekleme iş akışı için gereklidir.
* *Microsoft. ımportexport* kaynak sağlayıcısını Azure Storage hesabını içeren abonelikle kaydedin. Kaynak sağlayıcısını kaydetmek için:
    1. Ana menüden **abonelikler**' i seçin.
    1. Birden çok aboneliğe abone değilseniz, çevrimdışı yedekleme için kullanmayı planladığınız aboneliği seçin. Yalnızca bir abonelik kullanıyorsanız, aboneliğiniz görüntülenir.
    1. Sağlayıcı listesini görüntülemek için abonelik menüsünde **kaynak sağlayıcıları** ' nı seçin.
    1. Sağlayıcılar listesinde, *Microsoft. ımportexport*' a kaydırın. **Durum** **Notregistered** ise, **Kaydet**' i seçin.

        ![Kaynak sağlayıcısını kaydetme](./media/backup-azure-backup-import-export/registerimportexport.png)

* İlk kopyanızı tutmak için yeterli disk alanına sahip bir ağ paylaşımının veya bilgisayarda, iç veya dış olan herhangi bir ek sürücü olabilen bir hazırlama konumu oluşturulur. Örneğin, bir 500 GB dosya sunucusunu yedeklemek istiyorsanız, hazırlama alanının en az 500 GB olduğundan emin olun. (Sıkıştırma nedeniyle daha küçük bir miktar kullanılır.)
* Azure 'a disk gönderdiğinizde yalnızca 2,5 inç SSD veya 2,5-inç veya 3,5-inç SATA II/III iç sabit sürücü kullanın. Sabit sürücüleri 10 TB 'a kadar kullanabilirsiniz. Hizmetin desteklediği en son sürücü kümesi için [Azure içeri/dışarı aktarma hizmeti belgelerini](../import-export/storage-import-export-requirements.md#supported-hardware) denetleyin.
* SATA sürücülerin, hazırlama konumundan SATA sürücülere olan yedekleme verilerinin kopyasının yapıldığı bir bilgisayara ( *kopya bilgisayar* olarak adlandırılır) bağlanması gerekir. Kopya bilgisayarda BitLocker 'ın etkinleştirildiğinden emin olun.

## <a name="workflow"></a>İş akışı

Bu bölüm, verilerinizin bir Azure veri merkezine teslim edilebilmesi ve Azure depolama 'ya yüklenebilmesi için çevrimdışı yedekleme iş akışını açıklar. İçeri aktarma hizmeti veya işlemin herhangi bir yönü hakkında sorularınız varsa bkz. [Azure içeri/dışarı aktarma hizmeti genel bakış belgeleri](../import-export/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Çevrimdışı Yedeklemeyi Başlat

1. Kurtarma Hizmetleri aracısında bir yedekleme zamanladığınızda, bu sayfayı görürsünüz.

    ![İçeri aktarma sayfası](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. **Kendi disklerimi kullanarak Aktarım** seçeneğini belirleyin.

    > [!NOTE]
    > İlk yedekleme verilerini çevrimdışı olarak aktarmak için Azure Data Box seçeneğini kullanın. Bu seçenek, Azure ile uyumlu disklerinizi temin etmek için gereken çabayı kaydeder. Yedekleme verilerinin kurtarma hizmetleri Aracısı tarafından doğrudan yazılabileceği Microsoft 'a özel, güvenli ve Wonu Azure Data Box cihazları sunar.

1. **İleri**' yi seçin ve kutuları dikkatle girin.

    ![Disk ayrıntılarınızı girin](./media/backup-azure-backup-import-export/your-disk-details.png)

   İçinde doldurduğunuz kutular şunlardır:

    * **Hazırlama konumu**: ilk yedekleme kopyasının yazıldığı geçici depolama konumu. Hazırlama konumu bir ağ paylaşımında veya yerel bir bilgisayarda olabilir. Kopya bilgisayar ve kaynak bilgisayar farklıysa, hazırlama konumunun tam ağ yolunu belirtin.
    * **Azure Resource Manager depolama hesabı**: herhangi bir Azure aboneliğinde Kaynak Yöneticisi türü depolama hesabının adı (genel amaçlı v1 veya genel amaçlı v2).
    * **Azure depolama kapsayıcısı**: yedekleme verilerinin, kurtarma hizmetleri kasasına kopyalanmadan önce Içe aktarıldığı Azure depolama hesabındaki hedef BLOB depolama kapsayıcısının adı.
    * **Azure ABONELIK kimliği**: Azure depolama hesabının oluşturulduğu Azure aboneliğinin kimliği.
    * **Azure Içeri aktarma Işi adı**: Azure Içeri/dışarı aktarma hizmetinin ve Azure Backup disklere gönderilen verilerin Azure 'a aktarılmasını izleyen benzersiz ad.
  
   Kutuları doldurduktan sonra **İleri**' yi seçin. **Hazırlama konumunu** ve **Azure Içeri aktarma işi adı** bilgilerini kaydedin. Diskleri hazırlamak için gereklidir.

1. İstendiğinde, Azure aboneliğinizde oturum açın. Azure Backup Azure Active Directory uygulamayı oluşturabilmesi için oturum açmalısınız. Azure Içeri/dışarı aktarma hizmetine erişmek için gerekli izinleri girin.

    ![Azure aboneliği oturum açma sayfası](./media/backup-azure-backup-import-export/azure-login.png)

1. İş akışını sona erdirin. Azure Backup Aracısı konsolunda **Şimdi Yedekle**' yi seçin.

    ![Şimdi Yedekle](./media/backup-azure-backup-import-export/backupnow.png)

1. Sihirbazın **onay** sayfasında **Yedekle**' yi seçin. İlk yedekleme, kurulumun bir parçası olarak hazırlama alanına yazılır.

   ![Şimdi yedeklemeye hazır olduğunuzu onaylayın](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    İşlem bittikten sonra, hazırlama konumu, disk hazırlığı için kullanılabilir hale gelir.

   ![Şimdi Yedekle Sihirbazı sayfası](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA sürücüleri hazırlayın ve Azure 'a gönderin

*AzureOfflineBackupDiskPrep* yardımcı programı, en yakın Azure veri merkezıne gönderilen SATA sürücüleri hazırlar. Bu yardımcı program, aşağıdaki yoldaki Azure Backup Aracısı yükleme dizininde bulunur:

```*\Microsoft Azure Recovery Services Agent\Utils\\*```

1. Dizinine gidin ve *AzureOfflineBackupDiskPrep* dizinini SATA sürücülerin bağlı olduğu başka bir bilgisayara kopyalayın. Bağlı SATA sürücüleri olan bilgisayarda şunları doğrulayın:

   * Kopya bilgisayar, "çevrimdışı yedekleme başlatma" bölümünde iş akışında sağlanan ağ yolunu kullanarak çevrimdışı dağıtım iş akışının hazırlama konumuna erişebilir.
   * BitLocker, kopyalama bilgisayarında etkinleştirilmiştir.
   * Azure PowerShell 3.7.0 yüklendi.
   * En son uyumlu tarayıcılar (Microsoft Edge veya Internet Explorer 11) yüklenir ve JavaScript etkindir.
   * Kopya bilgisayar Azure portal erişebilir. Gerekirse, kopya bilgisayar kaynak bilgisayarla aynı olabilir.

     > [!IMPORTANT]
     > Kaynak bilgisayar bir sanal makinedir, kopya bilgisayar kaynak bilgisayardan farklı bir fiziksel sunucu veya istemci makinesi olmalıdır.

1. Geçerli dizin olarak *AzureOfflineBackupDiskPrep* yardımcı program dizini ile kopyalama bilgisayarında yükseltilmiş bir komut istemi açın. Şu komutu çalıştırın:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parametre | Açıklama |
    | --- | --- |
    | s: &lt; *hazırlama konumu yolu*&gt; |Bu zorunlu giriş, "çevrimdışı yedekleme 'yi başlatma" bölümünde iş akışına girdiğiniz hazırlama konumunun yolunu sağlamak için kullanılır. |
    | p: &lt; *PublishSettingsFile yolu*&gt; |Bu isteğe bağlı giriş, Azure yayımlama ayarları dosyasının yolunu sağlamak için kullanılır.  |

    Komutunu çalıştırdığınızda, yardımcı program, hazırlanması gereken sürücülere karşılık gelen Azure içeri aktarma işinin seçimini ister. Yalnızca tek bir içeri aktarma işi belirtilen hazırlama konumuyla ilişkiliyse, bunun gibi bir sayfa görürsünüz.

    ![Azure disk hazırlama aracı girişi](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. Azure 'a aktarmaya hazırlamak istediğiniz bağlı disk için sondaki iki nokta olmadan sürücü harfini girin.
1. İstendiğinde sürücünün biçimlendirmesi için onay sağlayın.
1. Azure aboneliğinizde oturum açmanız istenir. Kimlik bilgilerinizi girin.

    ![Azure aboneliği oturum açma](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Araç daha sonra diski hazırlamaya ve yedekleme verilerini kopyalamaya başlar. Sunulan disk, yedekleme verileri için yeterli alana sahip değilse araç tarafından istendiğinde ek diskler eklemeniz gerekebilir. <br/>

    Aracın başarıyla yürütülmesinin sonunda, komut istemi üç bilgi sağlar:

   1. Verdiğiniz bir veya daha fazla disk, Azure 'a gönderim için hazırlandı.
   1. İçeri aktarma işinizin oluşturulduğunu onaylayın. İçeri aktarma işi verdiğiniz adı kullanır.
   1. Araç, Azure veri merkezi 'nin sevkiyat adresini görüntüler.

      ![Azure disk hazırlığı tamamlandı](./media/backup-azure-backup-import-export/console2.png)<br/>

1. Komut yürütmenin sonunda, sevkiyat bilgilerini güncelleştirebilirsiniz.

1. Diskleri, aracın sağlandığı adrese gönderin. Daha sonra başvurmak üzere izleme numarasını saklayın.

   > [!IMPORTANT]
   > İki Azure içeri aktarma işi aynı izleme numarasına sahip olamaz. Tek bir Azure içeri aktarma işi altında yardımcı program tarafından hazırlanan sürücülerin tek bir pakette birlikte ve paket için tek bir benzersiz izleme numarası bulunduğundan emin olun. Ayrı Azure içeri aktarma işlerinin bir parçası olarak hazırlanan sürücüleri tek bir pakette birleştirmeyin.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Azure içeri aktarma işinde gönderi ayrıntılarını güncelleştirme

Aşağıdaki yordamda Azure içeri aktarma işi gönderme ayrıntıları güncelleştirilir. Bu bilgiler hakkındaki ayrıntıları içerir:

* Diskleri Azure 'a sunan taşıyıcının adı.
* Disklerinizin gönderim ayrıntılarını geri döndürür.

1. Azure aboneliğinizde oturum açın.
1. Ana menüden **tüm hizmetler**' i seçin. **Tüm hizmetler** Iletişim kutusunda **içeri aktar**' ı girin. **İçeri/dışarı aktarma işlerini** gördüğünüzde, seçin.

    ![Sevkiyat bilgilerini girin](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    **İçeri/dışarı aktarma işleri** menüsü açılır ve seçili abonelikteki tüm içeri/dışarı aktarma işlerinin listesi görüntülenir.

1. Birden çok aboneliğiniz varsa, yedekleme verilerini içeri aktarmak için kullanılan aboneliği seçin. Ardından yeni oluşturulan içeri aktarma işini seçerek ayrıntılarını açın.

    ![Sevkiyat bilgilerini gözden geçirme](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. İçeri aktarma işinin **Ayarlar** menüsünde, **gönderim bilgilerini Yönet**' i seçin. İade gönderimi ayrıntılarını girin.

    ![Mağaza Sevkiyat bilgileri](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. Sevkiyat taşıyıcınızdan izleme numarası varsa, Azure içeri aktarma işine Genel Bakış sayfasında başlık ' ı seçin ve aşağıdaki ayrıntıları girin.

   > [!IMPORTANT]
   > Azure içeri aktarma işini oluşturduktan itibaren iki hafta içinde taşıyıcı bilgilerini ve takip numarasını güncelleştirmeyi unutmayın. Bu bilgilerin iki hafta içinde doğrulanamaması, işin silinme ve sürücü işlenmesin oluşmasına neden olabilir.

   ![İzleme bilgileri güncelleştirme uyarısı](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Taşıyıcı bilgileri ve izleme numarası](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Sürücüleri işleme süresi

Bir Azure içeri aktarma işinin işlenmesi için gereken süre farklılık gösterir. İşlem süresi, Sevkiyat zamanı, iş türü, kopyalandığı verilerin türü ve boyutu ile verilen disklerin boyutu gibi faktörlerin temel alınarak hesaplanır. Azure Içeri/dışarı aktarma hizmeti bir SLA 'ya sahip değil. Diskler alındıktan sonra hizmet, Azure depolama hesabınıza 7 ila 10 gün içinde yedekleme verilerinin kopyalanmasını tamamlayacak kadar çaba harcar.

### <a name="monitor-azure-import-job-status"></a>Azure içeri aktarma işinin durumunu izleme

Azure portal içeri aktarma işinizin durumunu izleyebilirsiniz. **Işleri içeri/dışarı aktarma** sayfasına gidin ve işinizi seçin. İçeri aktarma işlerinin durumu hakkında daha fazla bilgi için bkz. [Azure içeri/dışarı aktarma hizmeti nedir?](../import-export/storage-import-export-service.md).

### <a name="finish-the-workflow"></a>İş akışını tamamlama

İçeri aktarma işi başarıyla tamamlandıktan sonra, depolama hesabınızda ilk yedekleme verileri kullanılabilir. Sonraki zamanlanmış yedekleme sırasında Azure Backup, verilerin içeriğini depolama hesabından kurtarma hizmetleri kasasına kopyalar.

   ![Verileri kurtarma hizmetleri kasasına kopyalama](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Bir sonraki zamanlanmış yedekleme sırasında, Azure Backup artımlı yedekleme gerçekleştirir.

### <a name="clean-up-resources"></a>Kaynakları temizleme

İlk yedekleme tamamlandıktan sonra, Azure depolama kapsayıcısına ve hazırlama konumundaki yedekleme verilerine aktarılan verileri güvenle silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Içeri/dışarı aktarma hizmeti iş akışıyla ilgili herhangi bir soru için bkz. [BLOB depolama alanına veri aktarmak için Microsoft Azure içeri/dışarı aktarma hizmetini kullanma](../import-export/storage-import-export-service.md).
