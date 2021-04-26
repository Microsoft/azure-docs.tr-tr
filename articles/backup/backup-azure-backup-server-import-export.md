---
title: DPM ve Azure Backup Sunucusu için çevrimdışı yedekleme
description: Azure Backup, Azure Içeri/dışarı aktarma hizmetini kullanarak ağ üzerinden veri gönderebilirsiniz. Bu makalede, DPM ve Azure Backup Sunucusu için çevrimdışı yedekleme iş akışı açıklanmaktadır.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: 006c0fa4d67c9a85426d7a007912df65876313da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98701822"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-mabs"></a>DPM ve Azure Backup Sunucusu (MABS) için çevrimdışı yedekleme iş akışı

>[!IMPORTANT]
> Bu adımlar DPM 2019 UR1 (veya üzeri) ve MABS v3 UR1 (veya üzeri) için geçerlidir.

System Center Data Protection Manager ve Azure Backup Sunucusu (MABS), Azure Backup ile tümleşir ve verilerin ilk tam yedeklemesi sırasında ağ ve depolama maliyetlerini tasarruf eden çeşitli yerleşik verimlilik kullanır. İlk tam yedeklemeler genellikle büyük miktarda veriyi aktarır ve yalnızca deltas/artımlarsa aktarımı yapan sonraki yedeklemelerle karşılaştırıldığında daha fazla ağ bant genişliği gerektirir. Azure Backup ilk yedeklemeleri sıkıştırır. Azure Backup çevrimdışı dağıtım sürecinde, sıkıştırılmış ilk yedekleme verilerini çevrimdışı olarak Azure 'a yüklemek için diskleri kullanabilir.

Azure Backup çevrimdışı dengeli işlem, [Azure içeri/dışarı aktarma hizmeti](../import-export/storage-import-export-service.md)ile sıkı bir şekilde tümleşiktir. Bu hizmeti kullanarak, diskleri kullanarak Azure 'a veri aktarabilirsiniz. Yüksek gecikmeli ve düşük bant genişliğine sahip bir ağ üzerinden aktarılması gereken ilk yedekleme verilerinin terabayt (TBs) varsa, ilk yedekleme kopyasını bir veya daha fazla sabit sürücüye bir Azure veri merkezine göndermek için çevrimdışı dengeli dağıtım iş akışını kullanabilirsiniz. Bu makalede, System Center Data Protection Manager (DPM) ve Microsoft Azure Backup sunucusu (MABS) için bu iş akışını izleyen bir genel bakış ve diğer adımlar sağlanmaktadır.

> [!NOTE]
> Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı için çevrimdışı yedekleme işlemi DPM 'den ve MABS 'den farklıdır. MARS aracısıyla çevrimdışı yedekleme kullanma hakkında bilgi için, bkz. [Azure Backup çevrimdışı yedekleme iş akışı](backup-azure-backup-import-export.md). Çevrimdışı yedekleme, Azure Backup Aracısı kullanılarak gerçekleştirilen sistem durumu yedeklemeleri için desteklenmez.
>
> MABS UR1 güncelleştirmesi, MABS içindeki Azure Data Box kullanarak çevrimdışı yedekleme önizlemesini de getirir. [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com)Daha fazla bilgi edinmek için iletişim kurun.

## <a name="overview"></a>Genel Bakış

Azure Backup ve Azure Içeri/dışarı aktarma hizmeti 'nin çevrimdışı dengeli hale getirme özelliği sayesinde, diskler kullanılarak verileri Azure 'a çevrimdışı yüklemek basittir. Çevrimdışı yedekleme işlemi aşağıdaki adımları içerir:

> [!div class="checklist"]
>
> * Yedekleme verileri, ağ üzerinden gönderilmek yerine bir hazırlama konumuna yazılır.
> * Hazırlama konumundaki veriler, *AzureOfflineBackupDiskPrep* yardımcı programını kullanarak bir veya daha fazla SATA diskine yazılır.
> * Bir Azure içeri aktarma işi otomatik olarak yardımcı program tarafından oluşturulur.
> * Daha sonra SATA sürücüler en yakın Azure veri merkezine gönderilir.
> * Yedekleme verilerinin Azure 'a yüklenmesi tamamlandıktan sonra, Azure Backup yedekleme verileri yedekleme kasasına kopyalanır ve artımlı yedeklemeler zamanlanır.

## <a name="prerequisites"></a>Önkoşullar

Çevrimdışı yedekleme iş akışını başlamadan önce aşağıdaki önkoşulların karşılandığından emin olun:

* Bir [Kurtarma Hizmetleri Kasası](backup-azure-recovery-services-vault-overview.md) oluşturuldu. Bir tane oluşturmak için [Kurtarma Hizmetleri Kasası oluşturma](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)öğreticisi-yedekleme-Windows-sunucu-Azure # Create-a-Recovery-Services-kasa ' daki adımları izleyin.
* SC DPM veya MABS üzerinde yalnızca [Microsoft Azure kurtarma hizmetleri aracısının en son sürümünün](https://aka.ms/azurebackup_agent) yüklü olduğundan ve kurtarma hizmetleri kasasında kayıtlı olduğundan emin olun.
* Güncelleştirme paketi 1, SC DPM 2019 veya MABS v3 üzerine yüklenir.

  > [!NOTE]
  > DPM 2019 UR1 ve MABS v3 UR1 ile Azure Active Directory kullanarak çevrimdışı dengeli dağıtım kimliğini doğrular.

* DPM veya MABS sunucusunda, Microsoft Edge veya Internet Explorer 11 ' in yüklü olduğundan ve JavaScript 'in etkinleştirildiğinden emin olun.
* Kurtarma Hizmetleri kasasıyla aynı abonelikte bir Azure depolama hesabı oluşturun.
* Azure Active Directory uygulamasını oluşturmak için [gerekli izinlere](../active-directory/develop/howto-create-service-principal-portal.md) sahip olduğunuzdan emin olun. Çevrimdışı yedekleme iş akışı, Azure depolama hesabıyla ilişkili abonelikte bir Azure Active Directory uygulaması oluşturur. Uygulamanın amacı, Azure Içeri aktarma hizmeti 'ne yönelik güvenli ve kapsamlı erişime sahip Azure Backup, çevrimdışı yedekleme iş akışı için gereklidir.
* Microsoft. ımportexport kaynak sağlayıcısını Azure Storage hesabını içeren abonelikle kaydedin. Kaynak sağlayıcısını kaydetmek için:
    1. Ana menüden **abonelikler**' i seçin.
    2. Birden çok aboneliğe abone değilseniz, çevrimdışı yedekleme için kullanmakta olduğunuz aboneliği seçin. Yalnızca bir abonelik kullanıyorsanız, aboneliğiniz görüntülenir.
    3. Sağlayıcı listesini görüntülemek için abonelik menüsünde **kaynak sağlayıcıları** ' nı seçin.
    4. Sağlayıcılar listesinde, Microsoft. ımportexport 'a gidin. Durum NotRegistered ise, **Kaydet**' i seçin.

       ![Kaynak sağlayıcısı kaydediliyor](./media/backup-azure-backup-server-import-export/register-import-export.png)

* İlk kopyanızı tutmak için yeterli disk alanına sahip bir ağ paylaşımının veya bilgisayarda, iç veya dış olan herhangi bir ek sürücü olabilen bir hazırlama konumu oluşturulur. Örneğin, bir 500 GB dosya sunucusunu yedeklemek istiyorsanız, hazırlama alanının en az 500 GB olduğundan emin olun. (Sıkıştırma nedeniyle daha küçük bir miktar kullanılır.)
* Azure 'a gönderilen diskler için yalnızca 2,5 inç SSD veya 2,5-inç veya 3,5-inç SATA II/III iç sabit disk sürücülerinin kullanıldığından emin olun. Sabit sürücüleri 10 TB 'a kadar kullanabilirsiniz. Hizmetin desteklediği en son sürücü kümesi için [Azure içeri/dışarı aktarma hizmeti belgelerini](../import-export/storage-import-export-requirements.md#supported-hardware) denetleyin.
* SATA sürücülerin, hazırlama konumundan SATA sürücülere olan yedekleme verilerinin kopyasının yapıldığı bir bilgisayara ( *kopya bilgisayar* olarak adlandırılır) bağlanması gerekir. Kopya bilgisayarda BitLocker 'ın etkinleştirildiğinden emin olun.

## <a name="workflow"></a>İş akışı

Bu bölümdeki bilgiler, verilerinizin bir Azure veri merkezine teslim edilebilmesi ve Azure depolama 'ya yüklenebilmesi için çevrimdışı yedekleme iş akışını tamamlamanıza yardımcı olur. Içeri aktarma hizmeti veya işlemin herhangi bir yönü hakkında sorularınız varsa, daha önce başvurulan [hizmete genel bakış](../import-export/storage-import-export-service.md) belgelerine bakın.

## <a name="initiate-offline-backup"></a>Çevrimdışı Yedeklemeyi Başlat

1. Çevrimiçi koruma ile yeni bir koruma grubu oluşturduğunuzda veya mevcut koruma grubuna çevrimiçi koruma eklediğinizde, aşağıdaki ekranı görürsünüz. Ilk çevrimiçi çoğaltma yöntemini seçmek için **kendi diskimi kullanarak aktar** ' ı seçin ve **İleri ' yi** seçin.

    ![İçeri aktarma ekranı](./media/backup-azure-backup-server-import-export/create-new-protection-group.png)

2. Azure oturum açma sayfası açılır. Azure aboneliğinde *sahip* rolü Iznine sahip Azure kullanıcı hesabınızı kullanarak oturum açın.

    ![Azure oturum açma sayfası](./media/backup-azure-backup-server-import-export/choose-initial-online-replication.png)

3. **Kendi disk kullanın** sayfasında girişleri sağlayın.

   ![Kendi diskinizin kullanım girişleri](./media/backup-azure-backup-server-import-export/use-your-own-disk.png)

   Girişlerin açıklaması aşağıdaki gibidir:

   * **Hazırlama konumu**: ilk yedekleme kopyasının yazıldığı geçici depolama konumu. Hazırlama konumu bir ağ paylaşımında veya yerel bir bilgisayarda olabilir. Kopya bilgisayar ve kaynak bilgisayar farklıysa, hazırlama konumunun tam ağ yolunu belirtin.
   * **Azure Resource Manager depolama hesabı**: herhangi bir Azure aboneliğinde Kaynak Yöneticisi türü depolama hesabının adı (genel amaçlı v1 veya genel amaçlı v2).
   * **Azure depolama kapsayıcısı**: yedekleme verilerinin Içeri aktarıldığı Azure depolama hesabındaki hedef BLOB depolama kapsayıcısının adı.
   * **Azure ABONELIK kimliği**: Azure depolama hesabının oluşturulduğu Azure aboneliğinin kimliği.
   * **Azure Içeri aktarma Işi adı**: Azure içeri aktarma hizmeti 'nin ve Azure Backup disklere gönderilen verilerin Azure 'a aktarılmasını izleyen benzersiz ad.

    **Hazırlama konumunu** ve verdiğiniz **Azure Içeri aktarma işi adı** bilgilerini kaydedin. Diskleri hazırlamak için gereklidir.

4. Korumayı oluşturmak veya güncelleştirmek için iş akışını doldurun. Çevrimdışı yedekleme kopyasını başlatmak için **koruma grubuna** sağ tıklayın ve ardından **Kurtarma noktası oluştur** seçeneğini belirleyin. Sonra **çevrimiçi koruma** seçeneğini belirleyin.

   ![Kurtarma noktası oluştur](./media/backup-azure-backup-server-import-export/create-recovery-point.png)

5. İzleme bölmesinde çevrimiçi çoğaltma oluşturma işini izleyin. İş, *Azure Içeri aktarma işinin bitmesi beklenirken* uyarı ile başarıyla tamamlanmalıdır.

   ![Kurtarma noktasını Tamam](./media/backup-azure-backup-server-import-export/complete-recovery-point.png)

6. İşlem bittikten sonra, hazırlama konumu, disk hazırlığı için kullanılabilir hale gelir.

## <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA sürücüleri hazırlayın ve Azure 'a gönderin

*AzureOfflineBackupDiskPrep* yardımcı programı, en yakın Azure veri merkezıne gönderilen SATA sürücüleri hazırlar. Bu yardımcı program, Azure Backup Aracısı yükleme dizininde (aşağıdaki yolda) kullanılabilir: `*\Microsoft Azure Recovery Services Agent\Utils\\*`

1. Dizine gidin ve **AzureOfflineBackupDiskPrep** dizinini SATA sürücülerin bağlı olduğu başka bir bilgisayara kopyalayın. Bağlı SATA sürücülerine sahip bilgisayarda şunları doğrulayın:

   * Kopya bilgisayar, "çevrimdışı yedekleme başlatma" bölümünde iş akışında sağlanan ağ yolunu kullanarak çevrimdışı dağıtım iş akışının hazırlama konumuna erişebilir.
   * BitLocker, kopyalama bilgisayarında etkinleştirilmiştir.
   * Azure PowerShell 3.7.0, kopyalama bilgisayarında yüklüdür (DPM veya MABS sunucusunda AzureOfflineBackupDiskPrep yardımcı programını çalıştırıyorsanız gerekli değildir).
   * En son uyumlu tarayıcılar (Microsoft Edge veya Internet Explorer 11) yüklenir ve JavaScript etkindir.
   * Kopya bilgisayar Azure portal erişebilir. Gerekirse, kopya bilgisayar kaynak bilgisayarla aynı olabilir.

     > [!IMPORTANT]
     > Kaynak bilgisayar bir sanal makinedir, kopya bilgisayar olarak farklı bir fiziksel sunucu veya istemci makine kullanılması zorunludur.

1. Geçerli dizin olarak *AzureOfflineBackupDiskPrep* yardımcı program dizini ile kopyalama bilgisayarında yükseltilmiş bir komut istemi açın. Şu komutu çalıştırın:

    ```console
    .\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>
    ```

    | Parametre | Açıklama |
    | --- | --- |
    | s: &lt; *hazırlama konumu yolu*&gt; |Bu zorunlu giriş, "çevrimdışı yedekleme 'yi başlatma" bölümünde iş akışına girdiğiniz hazırlama konumunun yolunu sağlamak için kullanılır. |
    | p: &lt; *PublishSettingsFile yolu*&gt; |Bu isteğe bağlı giriş, Azure yayımlama ayarları dosyasının yolunu sağlamak için kullanılır. |

    Komutunu çalıştırdığınızda, yardımcı program, hazırlanması gereken sürücülere karşılık gelen Azure Içeri aktarma işinin seçimini ister. Yalnızca tek bir içeri aktarma işi belirtilen hazırlama konumuyla ilişkiliyse, aşağıdakine benzer bir ekran görürsünüz.

      ![Disk hazırlama konsolu](./media/backup-azure-backup-server-import-export/disk-prep-console.png)

1. Azure 'a aktarmaya hazırlamak istediğiniz bağlı disk için sondaki iki nokta olmadan sürücü harfini girin.
1. İstendiğinde sürücünün biçimlendirmesi için onay sağlayın.
1. Azure aboneliğinizde oturum açmanız istenir. Kimlik bilgilerinizi sağlayın.

    ![Azure oturum açma ekranı](./media/backup-azure-backup-server-import-export/signin-disk-prep.png)

    Araç daha sonra diski hazırlamaya ve yedekleme verilerini kopyalamaya başlar. Sunulan disk, yedekleme verileri için yeterli alana sahip değilse araç tarafından istendiğinde ek diskler eklemeniz gerekebilir. <br/>

    Aracın başarıyla yürütülmesinin sonunda, komut istemi üç bilgi sağlar:
    * Verdiğiniz bir veya daha fazla disk, Azure 'a gönderim için hazırlandı.
    * İçeri aktarma işinizin oluşturulduğunu onaylayın. İçeri aktarma işi verdiğiniz adı kullanır.
    * Araç, Azure veri merkezi 'nin sevkiyat adresini görüntüler.

     ![Azure disk hazırlığı Tamam](./media/backup-azure-backup-server-import-export/console.png)

1. Komut yürütmenin sonunda, sevkiyat bilgilerini güncelleştirme seçeneğini de görürsünüz.

1. Diskleri aracın verdiği adrese gönderin ve daha sonra başvurmak üzere izleme numarasını saklayın.

   > [!IMPORTANT]
   > İki Azure içeri aktarma işi aynı izleme numarasına sahip olamaz. Tek bir Azure içeri aktarma işi altında yardımcı program tarafından hazırlanan sürücülerin tek bir pakette birlikte ve paket için tek bir benzersiz izleme numarası bulunduğundan emin olun. Farklı Azure içeri aktarma işlerinin bir parçası olarak hazırlanan sürücüleri tek bir pakette birleştirmeyin.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Azure Içeri aktarma işinde gönderi ayrıntılarını güncelleştirme

Aşağıdaki yordamda Azure Içeri aktarma işi gönderme ayrıntıları güncelleştirilir. Bu bilgiler hakkındaki ayrıntıları içerir:

* diskleri Azure 'a teslim eden taşıyıcının adı
* diskleriniz için geri dönüş gönderimi ayrıntıları

   1. Azure aboneliğinizde oturum açın.
   2. Ana menüde **tüm hizmetler** ' i seçin ve tüm hizmetler Iletişim kutusunda içeri aktar yazın. **İçeri/dışarı aktarma işlerini** gördüğünüzde, seçin.
       ![Sevkiyat bilgilerini girme](./media/backup-azure-backup-server-import-export/search-import-job.png)

       **İçeri/dışarı aktarma işleri** menüsü açılır ve seçili abonelikteki tüm içeri/dışarı aktarma işlerinin listesi görüntülenir.

   3. Birden çok aboneliğiniz varsa, yedekleme verilerini içeri aktarmak için kullanılan aboneliği seçtiğinizden emin olun. Ardından yeni oluşturulan Içeri aktarma işini seçerek ayrıntılarını açın.

       ![Sevkiyat bilgilerini gözden geçirme](./media/backup-azure-backup-server-import-export/import-job-found.png)

   4. Içeri aktarma işinin ayarlar menüsünde, **gönderim bilgilerini Yönet** ' i seçin ve iade gönderimi ayrıntılarını girin.

       ![Sevkiyat bilgilerini depolama](./media/backup-azure-backup-server-import-export/shipping-info.png)

   5. Sevkiyat taşıyıcınızdan izleme numarası varsa, Azure Içeri aktarma işine Genel Bakış sayfasında başlık ' ı seçin ve aşağıdaki ayrıntıları girin:

      > [!IMPORTANT]
      > Azure içeri aktarma işini oluşturduktan itibaren iki hafta içinde taşıyıcı bilgilerini ve takip numarasını güncelleştirmeyi unutmayın. Bu bilgilerin iki hafta içinde doğrulanamaması, işin silinme ve sürücüler işlenmemiş olabilir.

      ![İşe genel bakış](./media/backup-azure-backup-server-import-export/job-overview.png)

      ![İzleme bilgileri](./media/backup-azure-backup-server-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Sürücüleri işleme süresi

Bir Azure içeri aktarma işinin işlenmesi için gereken süre farklılık gösterir. İşlem süresi, Sevkiyat zamanı, iş türü, kopyalandığı verilerin türü ve boyutu ile verilen disklerin boyutu gibi faktörlere bağlıdır. Azure Içeri/dışarı aktarma hizmeti bir SLA 'ya sahip değil. Diskler alındıktan sonra hizmet, Azure depolama hesabınıza 7 ila 10 gün içinde yedekleme verilerinin kopyalanmasını tamamlayacak kadar çaba harcar. Sonraki bölümde, Azure içeri aktarma işinin durumunu nasıl izleyebileceğinizi anlatmaktadır.

### <a name="monitor-azure-import-job-status"></a>Azure içeri aktarma işinin durumunu izleme

İçeri **/dışarı aktarma işleri** sayfasına giderek ve Işinizi seçerek içeri aktarma işinizin durumunu Azure Portal izleyebilirsiniz. Içeri aktarma işlerinin durumu hakkında daha fazla bilgi için bkz. [depolama içeri aktarma verme hizmeti](../import-export/storage-import-export-service.md) makalesi.

### <a name="complete-the-workflow"></a>İş akışını doldurun

İçeri aktarma işi bittikten sonra, depolama hesabınızda ilk yedekleme verileri kullanılabilir. Sonraki zamanlanmış yedekleme sırasında Azure Backup, verilerin içeriğini depolama hesabından kurtarma hizmetleri kasasına kopyalar.

Bir sonraki zamanlanmış çevrimiçi çoğaltma oluşturma işi sırasında, Data Protection Manager ilk yedekleme kopyası üzerinde artımlı yedekleme gerçekleştirir.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Içeri/dışarı aktarma hizmeti iş akışıyla ilgili herhangi bir soru için bkz. [BLOB depolama alanına veri aktarmak için Microsoft Azure içeri/dışarı aktarma hizmetini kullanma](../import-export/storage-import-export-service.md).
