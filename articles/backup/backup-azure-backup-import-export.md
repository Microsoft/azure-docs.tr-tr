---
title: Içeri/dışarı aktarma hizmeti ile çekirdek çevrimdışı yedekleme
description: Azure Backup Azure Içeri/dışarı aktarma hizmetini kullanarak ağ üzerinden veri göndermenizi nasıl sağlayacağınızı öğrenin. Bu makalede, Azure Içeri aktarma hizmeti kullanılarak ilk yedekleme verilerinin çevrimdışı dengeli dağıtımı açıklanmaktadır.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 331d5528c8f124f4d43142ff7be4daa3169b0381
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173298"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Azure Backup’ta çevrimdışı yedekleme iş akışı

Azure Backup, Azure 'a verilerin ilk tam yedeklemesi sırasında ağ ve depolama maliyetlerini kaydetmekle ilgili çeşitli yerleşik verimlilik içerir. İlk tam yedeklemeler genellikle büyük miktarda veriyi aktarır ve yalnızca deltas/artımlarsa aktarımı yapan sonraki yedeklemelerle karşılaştırıldığında daha fazla ağ bant genişliği gerektirir. Azure Backup çevrimdışı dağıtım sürecinde, çevrimdışı yedekleme verilerini Azure 'a yüklemek için diskleri kullanabilir.

Azure Backup çevrimdışı dağıtım işlemi, diskleri kullanarak ilk yedekleme verilerini Azure 'a aktarmanızı sağlayan [Azure içeri/dışarı aktarma hizmeti](../storage/common/storage-import-export-service.md) ile sıkı bir şekilde tümleşiktir. Yüksek gecikme süresine ve düşük bant genişliğine sahip bir ağ üzerinden aktarılması gereken ilk yedekleme verilerinin terabayt (TBs) varsa, bir veya daha fazla sabit sürücüde ilk yedek kopyayı bir Azure veri merkezine göndermek için çevrimdışı dengeli dağıtım iş akışını kullanabilirsiniz. Aşağıdaki görüntüde, iş akışındaki adımlara bir genel bakış sunulmaktadır.

  ![Çevrimdışı içeri aktarma iş akışı işlemine genel bakış](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Çevrimdışı yedekleme işlemi aşağıdaki adımları içerir:

1. Yedekleme verilerini ağ üzerinden göndermek yerine, yedekleme verilerini bir hazırlama konumuna yazın.
2. Hazırlama konumundaki verileri bir veya daha fazla SATA diskine yazmak için AzureOfflineBackupDiskPrep yardımcı programını kullanın.
3. Hazırlık işinin bir parçası olarak, AzureOfflineBackupDiskPrep yardımcı programı bir Azure Içeri aktarma işi oluşturur. SATA sürücüleri en yakın Azure veri merkezine gönderin ve etkinlikleri bağlamak için içeri aktarma işine başvurun.
4. Azure veri merkezinde, disklerdeki veriler bir Azure depolama hesabına kopyalanır.
5. Azure Backup, yedekleme verilerini depolama hesabından kurtarma hizmetleri kasasına kopyalar ve artımlı yedeklemeler zamanlanır.

## <a name="supported-configurations"></a>Desteklenen yapılandırmalar

Aşağıdaki Azure Backup özellikleri veya iş yükleri çevrimdışı yedekleme kullanımını destekler.

> [!div class="checklist"]
>
> * Azure Backup Aracısı olarak da adlandırılan Microsoft Azure Kurtarma Hizmetleri (MARS) aracısıyla dosya ve klasörlerin yedeklenmesi.
> * Tüm iş yüklerini ve dosyaları System Center Data Protection Manager (SC DPM) ile yedekleme
> * Tüm iş yüklerini ve dosyaları Microsoft Azure Backup sunucusuna yedekleme
 
   > [!NOTE]
   > Çevrimdışı yedekleme, Azure Backup Aracısı kullanılarak gerçekleştirilen sistem durumu yedeklemeleri için desteklenmez.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Önkoşullar

  > [!NOTE]
  > Aşağıdaki Önkoşullar ve iş akışı yalnızca [en son Mars aracısını](https://aka.ms/azurebackup_agent)kullanarak dosya ve klasörlerin çevrimdışı yedeklemesi için geçerlidir. System Center DPM veya Azure Backup Sunucusu kullanarak iş yükleri için çevrimdışı yedeklemeler gerçekleştirmek için, [Bu makaleye](backup-azure-backup-server-import-export-.md)bakın.

Çevrimdışı yedekleme iş akışını başlatmadan önce aşağıdaki önkoşulları doldurun:

* Bir [Kurtarma Hizmetleri Kasası](backup-azure-recovery-services-vault-overview.md)oluşturun. Kasa oluşturmak için [Bu makaledeki](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault) adımlara bakın
* [Azure Backup aracısının yalnızca en son sürümünün](https://aka.ms/azurebackup_agent) Windows Server/Windows istemcisinde yüklü olduğundan ve bilgisayarın kurtarma hizmetleri kasasına kaydedildiğinden emin olun.
* Azure Backup Aracısı 'nı çalıştıran bilgisayarda Azure PowerShell 3.7.0 gereklidir. [Azure PowerShell 3.7.0 sürümünü](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)indirmeniz ve kurmanız önerilir.
* Azure Backup Aracısı 'nı çalıştıran bilgisayarda, Microsoft Edge veya Internet Explorer 11 ' in yüklü olduğundan ve JavaScript 'in etkinleştirildiğinden emin olun.
* Kurtarma Hizmetleri kasasıyla aynı abonelikte bir Azure depolama hesabı oluşturun.
* Azure Active Directory uygulamasını oluşturmak için [gerekli izinlere](../active-directory/develop/howto-create-service-principal-portal.md) sahip olduğunuzdan emin olun. Çevrimdışı yedekleme iş akışı, Azure depolama hesabıyla ilişkili abonelikte bir Azure Active Directory uygulaması oluşturur. Uygulamanın amacı, Azure Içeri aktarma hizmeti 'ne yönelik güvenli ve kapsamlı erişime sahip Azure Backup, çevrimdışı yedekleme iş akışı için gereklidir.
* Microsoft. ımportexport kaynak sağlayıcısını Azure Storage hesabını içeren abonelikle kaydedin. Kaynak sağlayıcısını kaydetmek için:
    1. Ana menüden **abonelikler**' e tıklayın.
    2. Birden çok aboneliğe abone değilseniz, çevrimdışı yedekleme için kullanmakta olduğunuz aboneliği seçin. Yalnızca bir abonelik kullanıyorsanız, aboneliğiniz görüntülenir.
    3. Sağlayıcı listesini görüntülemek için abonelik menüsünde **kaynak sağlayıcıları** ' na tıklayın.
    4. Sağlayıcılar listesinde, Microsoft. ımportexport 'a gidin. Durum NotRegistered ise, **Kaydet**' e tıklayın.
    Kaynak sağlayıcısını kaydetmek ![](./media/backup-azure-backup-import-export/registerimportexport.png)
* İlk kopyanızı tutmak için yeterli disk alanına sahip bir ağ paylaşımının veya bilgisayarda, iç veya dış olan herhangi bir ek sürücü olabilen bir hazırlama konumu oluşturulur. Örneğin, bir 500 GB dosya sunucusunu yedeklemeye çalışıyorsanız, hazırlama alanının en az 500 GB olduğundan emin olun. (Sıkıştırma nedeniyle daha küçük bir miktar kullanılır.)
* Diskler Azure 'a gönderilirken yalnızca 2,5 inç SSD veya 2,5 inç veya 3,5-inç SATA II/III iç sabit sürücü kullanın. Sabit sürücüleri 10 TB 'a kadar kullanabilirsiniz. Hizmetin desteklediği en son sürücü kümesi için [Azure içeri/dışarı aktarma hizmeti belgelerini](../storage/common/storage-import-export-requirements.md#supported-hardware) denetleyin.
* SATA sürücülerin, *hazırlama KONUMUNDAN* SATA sürücülere olan yedekleme verilerinin kopyasının yapıldığı bir bilgisayara ( *kopya bilgisayar*olarak adlandırılır) bağlanması gerekir. *Kopya bilgisayarda*BitLocker 'ın etkinleştirildiğinden emin olun.

## <a name="workflow"></a>İş akışı

Bu bölüm, verilerinizin bir Azure veri merkezine teslim edilebilmesi ve Azure depolama 'ya yüklenebilmesi için çevrimdışı yedekleme iş akışını açıklar. Içeri aktarma hizmeti veya işlemin herhangi bir yönü hakkında sorularınız varsa bkz. [Içeri aktarma hizmeti genel bakış belgeleri](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Çevrimdışı Yedeklemeyi Başlat

1. MARS aracısında bir yedekleme zamanladığınızda, aşağıdaki ekranı görürsünüz.

    ![İçeri aktarma ekranı](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

   Girişlerin açıklaması aşağıdaki gibidir:

    * **Hazırlama konumu**: ilk yedekleme kopyasının yazıldığı geçici depolama konumu. Hazırlama konumu bir ağ paylaşımında veya yerel bir bilgisayarda olabilir. Kopya bilgisayar ve kaynak bilgisayar farklıysa, hazırlama konumunun tam ağ yolunu belirtmenizi öneririz.
    * **Azure Resource Manager depolama hesabı**: herhangi bir Azure aboneliğinde Kaynak Yöneticisi türü depolama hesabının adı (genel amaçlı v1 veya genel amaçlı v2).
    * **Azure depolama kapsayıcısı**: yedekleme verilerinin, kurtarma hizmetleri kasasına kopyalanmadan önce Içe aktarıldığı Azure depolama hesabındaki hedef depolama blobunun adı.
    * **Azure ABONELIK kimliği**: Azure depolama hesabının oluşturulduğu Azure aboneliğinin kimliği.
    * **Azure Içeri aktarma Işi adı**: Azure içeri aktarma hizmeti 'nin ve Azure Backup disklere gönderilen verilerin Azure 'a aktarılmasını izleyen benzersiz ad.
  
   Ekrandaki girişleri girip **İleri**' ye tıklayın. Belirtilen *hazırlama konumunu* ve *Azure Içeri aktarma işi adını*kaydederek diskleri hazırlamak için bu bilgiler gereklidir.

2. İstendiğinde, Azure aboneliğinizde oturum açın. Azure Backup Azure Active Directory uygulamayı oluşturabilmesi ve Azure Içeri aktarma hizmetine erişmek için gerekli izinleri sağlayabilmeniz için oturum açmalısınız.

    ![Şimdi Yedekle](./media/backup-azure-backup-import-export/azurelogin.png)

3. İş akışını tamamlayıp Azure Backup aracı konsolunda **Şimdi Yedekle**' ye tıklayın.

    ![Şimdi Yedekle](./media/backup-azure-backup-import-export/backupnow.png)

4. Sihirbazın onay sayfasında **Yedekle**' ye tıklayın. İlk yedekleme, kurulumun bir parçası olarak hazırlama alanına yazılır.

   ![Şimdi yedeklemeye hazır olduğunuzu onaylayın](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    İşlem bittikten sonra, hazırlama konumu, disk hazırlığı için kullanılabilir hale gelir.

   ![Şimdi Yedekle](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA sürücüleri hazırlayın ve Azure 'a gönderin

*AzureOfflineBackupDiskPrep* yardımcı programı, en yakın Azure veri merkezıne gönderilen SATA sürücüleri hazırlar. Bu yardımcı program, Azure Backup Aracısı yükleme dizininde (aşağıdaki yolda) kullanılabilir:

    *\Microsoft Azure Recovery Services Agent\Utils\\*

1. Dizine gidin ve **AzureOfflineBackupDiskPrep** dizinini SATA sürücülerin bağlı olduğu başka bir bilgisayara kopyalayın. Bağlı SATA sürücülerine sahip bilgisayarda şunları doğrulayın:

   * Kopya bilgisayar **, çevrimdışı dağıtım için hazırlama iş akışında** sağlanan aynı ağ yolunu kullanarak çevrimdışı dengeli dağıtım iş akışının hazırlama konumuna erişebilir.
   * BitLocker, kopyalama bilgisayarında etkinleştirilmiştir.
   * Azure PowerShell 3.7.0 yüklendi.
   * En son uyumlu tarayıcılar (Microsoft Edge veya Internet Explorer 11) yüklenir ve JavaScript etkindir.
   * Kopya bilgisayar Azure portal erişebilir. Gerekirse, kopya bilgisayar kaynak bilgisayarla aynı olabilir.

     > [!IMPORTANT]
     > Kaynak bilgisayar bir sanal makinedir, kopya bilgisayar kaynak bilgisayardan farklı bir fiziksel sunucu veya istemci makinesi olmalıdır.

2. Geçerli dizin olarak *AzureOfflineBackupDiskPrep* yardımcı program dizini ile kopyalama bilgisayarında yükseltilmiş bir komut istemi açın ve aşağıdaki komutu çalıştırın:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parametre | Açıklama |
    | --- | --- |
    | s:&lt;*hazırlama konumu yolu*&gt; |**Çevrimdışı yedekleme 'Yi Başlat** iş akışına girdiğiniz hazırlama konumunun yolunu sağlamak için kullanılan zorunlu giriş. |
    | p: *PublishSettingsFile dosyasının yolunu*&lt;&gt; |**Çevrimdışı yedekleme 'Yi Başlat** iş akışında girdiğiniz **Azure yayımlama ayarları** dosyasının yolunu sağlamak için kullanılan isteğe bağlı giriş. |

    Komutunu çalıştırdığınızda, yardımcı program, hazırlanması gereken sürücülere karşılık gelen Azure Içeri aktarma işinin seçimini ister. Yalnızca tek bir içeri aktarma işi belirtilen hazırlama konumuyla ilişkiliyse, aşağıdakine benzer bir ekran görürsünüz.

    ![Azure disk hazırlama aracı girişi](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

3. Azure 'a aktarmaya hazırlamak istediğiniz bağlı disk için sondaki iki nokta olmadan sürücü harfini girin.
4. İstendiğinde sürücünün biçimlendirmesi için onay sağlayın.
5. Azure aboneliğinizde oturum açmanız istenir. Kimlik bilgilerinizi sağlayın.

    ![Azure disk hazırlama aracı girişi](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Araç daha sonra diski hazırlamaya ve yedekleme verilerini kopyalamaya başlar. Belirtilen diskte yedekleme verileri için yeterli alan olmadığında araç tarafından istendiğinde ek diskler eklemeniz gerekebilir. <br/>

    Aracın başarıyla yürütülmesinin sonunda, komut istemi üç bilgi sağlar:
   1. Verdiğiniz bir veya daha fazla disk, Azure 'a gönderim için hazırlandı.
   2. İçeri aktarma işinizin oluşturulduğunu onaylayın. İçeri aktarma işi verdiğiniz adı kullanır.
   3. Araç, Azure veri merkezi 'nin sevkiyat adresini görüntüler.

      ![Azure disk hazırlığı Tamam](./media/backup-azure-backup-import-export/console2.png)<br/>

6. Komut yürütmenin sonunda, sevkiyat bilgilerini güncelleştirebilirsiniz.

7. Diskleri aracın verdiği adrese gönderin ve daha sonra başvurmak üzere izleme numarasını saklayın.

   > [!IMPORTANT]
   > İki Azure Içeri aktarma Işi aynı izleme numarasına sahip olamaz. Tek bir Azure Içeri aktarma işi altında yardımcı program tarafından hazırlanan sürücülerin tek bir pakette birlikte ve paket için tek bir benzersiz izleme numarası bulunduğundan emin olun. Tek bir pakette ayrı Azure Içeri aktarma işlerinin bir parçası olarak hazırlanan sürücüleri birleştirmeyin.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Azure Içeri aktarma işinde gönderi ayrıntılarını güncelleştirme

Aşağıdaki yordamda Azure Içeri aktarma işi gönderme ayrıntıları güncelleştirilir. Bu bilgiler hakkındaki ayrıntıları içerir:

* diskleri Azure 'a teslim eden taşıyıcının adı
* diskleriniz için geri dönüş gönderimi ayrıntıları

1. Azure aboneliğinizde oturum açın.
2. Ana menüde **tüm hizmetler** ' e tıklayın ve tüm hizmetler Iletişim kutusunda içeri aktar yazın. **İçeri/dışarı aktarma işlerini**gördüğünüzde, buna tıklayın.
    ![sevkiyat bilgilerini girme](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    **İçeri/dışarı aktarma işleri** menüsü açılır ve seçili abonelikteki tüm içeri/dışarı aktarma işlerinin listesi görüntülenir.

3. Birden çok aboneliğiniz varsa, yedekleme verilerini içeri aktarmak için kullanılan aboneliği seçtiğinizden emin olun. Ardından yeni oluşturulan Içeri aktarma işini seçerek ayrıntılarını açın.

    ![Sevkiyat bilgilerini gözden geçirme](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

4. Içeri aktarma işinin ayarlar menüsünde, **gönderim bilgilerini Yönet** ' e tıklayın ve iade gönderimi ayrıntılarını girin.

    ![Sevkiyat bilgilerini depolama](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

5. Sevkiyat taşıyıcınızdan izleme numarası varsa, Azure Içeri aktarma işine Genel Bakış sayfasında başlık ' a tıklayın ve aşağıdaki ayrıntıları girin:

   > [!IMPORTANT]
   > Azure içeri aktarma işini oluşturduktan itibaren iki hafta içinde taşıyıcı bilgilerini ve takip numarasını güncelleştirmeyi unutmayın. Bu bilgilerin iki hafta içinde doğrulanamaması, işin silinme ve sürücüler işlenmemiş olabilir.

   ![Sevkiyat bilgilerini depolama](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Sevkiyat bilgilerini depolama](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Sürücüleri işleme süresi

Bir Azure içeri aktarma işinin işlenmesi için gereken süre, Sevkiyat zamanı, iş türü, kopyalandığı verilerin türü ve boyutu ile verilen disklerin boyutu gibi farklı faktörlere bağlı olarak farklılık gösterir. Azure Içeri/dışarı aktarma hizmeti bir SLA 'ya sahip değil, ancak diskler alındıktan sonra hizmet, Azure depolama hesabınıza 7 ila 10 gün sonra yedek veri kopyalamayı tamamlayacak kadar çaba harcar.

### <a name="monitoring-azure-import-job-status"></a>Azure Içeri aktarma Işi durumunu izleme

İçeri **/dışarı aktarma işleri** sayfasına giderek ve Işinizi seçerek içeri aktarma işinizin durumunu Azure Portal izleyebilirsiniz. Içeri aktarma işlerinin durumu hakkında daha fazla bilgi için bkz. [depolama içeri aktarma verme hizmeti](../storage/common/storage-import-export-service.md) makalesi.

### <a name="complete-the-workflow"></a>İş akışını doldurun

İçeri aktarma işi başarıyla tamamlandıktan sonra, depolama hesabınızda ilk yedekleme verileri kullanılabilir. Sonraki zamanlanmış yedekleme sırasında, Azure Backup verilerin içeriğini depolama hesabından kurtarma hizmetleri kasasına aşağıda gösterildiği gibi kopyalar:

   ![Verileri kurtarma hizmetleri kasasına kopyalama](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

Bir sonraki zamanlanmış yedekleme sırasında, Azure Backup artımlı yedekleme gerçekleştirir.

### <a name="cleaning-up-resources"></a>Kaynakları Temizleme

İlk yedekleme tamamlandıktan sonra Azure depolama kapsayıcısına ve hazırlama konumundaki yedekleme verilerine aktarılan verileri güvenle silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Içeri/dışarı aktarma iş akışı hakkında herhangi bir soru için, [BLOB depolamaya veri aktarmak için Microsoft Azure içeri/dışarı aktarma hizmetini kullanma](../storage/common/storage-import-export-service.md)konusuna bakın.
* İş akışıyla ilgili sorular için Azure Backup [SSS](backup-azure-backup-faq.md) konusunun çevrimdışı yedekleme bölümüne bakın.
