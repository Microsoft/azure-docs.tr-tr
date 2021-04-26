---
title: Data Protection Manager (DPM) ve Microsoft Azure Backup sunucusu (MABS) için çevrimdışı yedekleme-önceki sürümler
description: Azure Backup, Azure Içeri/dışarı aktarma hizmetini kullanarak ağ üzerinden veri gönderebilirsiniz. Bu makalede, DPM 'nin önceki sürümleri ve Azure Backup Sunucusu için çevrimdışı yedekleme iş akışı açıklanmaktadır.
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 0405ab66b7714f00349419e94bb064267ca711a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98702194"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-previous-versions"></a>DPM ve Azure Backup Sunucusu için çevrimdışı yedekleme iş akışı (önceki sürümler)

>[!IMPORTANT]
>Bu adımlar DPM 2019 RTM ve önceki sürümleri ve MABS v3 RTM ve önceki sürümleri için geçerlidir.

Azure Backup, Azure 'a verilerin ilk tam yedeklemesi sırasında ağ ve depolama maliyetlerini kaydetmekle ilgili çeşitli yerleşik verimlilik içerir. İlk tam yedeklemeler genellikle büyük miktarda veriyi aktarır ve yalnızca deltas/artımlarsa aktarımı yapan sonraki yedeklemelerle karşılaştırıldığında daha fazla ağ bant genişliği gerektirir. Azure Backup ilk yedeklemeleri sıkıştırır. Azure Backup çevrimdışı dağıtım sürecinde, sıkıştırılmış ilk yedekleme verilerini çevrimdışı olarak Azure 'a yüklemek için diskleri kullanabilir.

Azure Backup çevrimdışı dengeli işlem, [Azure içeri/dışarı aktarma hizmeti](../import-export/storage-import-export-service.md)ile sıkı bir şekilde tümleşiktir. Bu hizmeti kullanarak, diskleri kullanarak Azure 'a veri aktarabilirsiniz. Yüksek gecikmeli ve düşük bant genişliğine sahip bir ağ üzerinden aktarılması gereken ilk yedekleme verilerinin terabayt (TBs) varsa, ilk yedekleme kopyasını bir veya daha fazla sabit sürücüye bir Azure veri merkezine göndermek için çevrimdışı dengeli dağıtım iş akışını kullanabilirsiniz. Bu makalede, System Center Data Protection Manager (DPM) ve Microsoft Azure Backup sunucusu (MABS) için bu iş akışını izleyen bir genel bakış ve diğer adımlar sağlanmaktadır.

> [!NOTE]
> Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı için çevrimdışı yedekleme işlemi DPM 'den ve MABS 'den farklıdır. MARS aracısıyla çevrimdışı yedekleme kullanma hakkında bilgi için, bkz. [Azure Backup çevrimdışı yedekleme iş akışı](backup-azure-backup-import-export.md). Çevrimdışı yedekleme, Azure Backup Aracısı kullanılarak gerçekleştirilen sistem durumu yedeklemeleri için desteklenmez.
>

## <a name="overview"></a>Genel Bakış

Azure Backup ve Azure Içeri/dışarı aktarma hizmeti 'nin çevrimdışı dengeli hale getirme özelliği sayesinde, diskler kullanılarak verileri Azure 'a çevrimdışı yüklemek basittir. Çevrimdışı yedekleme işlemi aşağıdaki adımları içerir:

> [!div class="checklist"]
>
> * Yedekleme verileri, ağ üzerinden gönderilmek yerine bir hazırlama konumuna yazılır.
> * Hazırlama konumundaki veriler, *AzureOfflineBackupDiskPrep* yardımcı programını kullanarak bir veya daha fazla SATA diskine yazılır.
> * Bir Azure içeri aktarma işi otomatik olarak yardımcı program tarafından oluşturulur.
> * Daha sonra SATA sürücüler en yakın Azure veri merkezine gönderilir.
> * Yedekleme verilerinin Azure 'a yüklenmesi tamamlandıktan sonra, Azure Backup yedekleme verileri yedekleme kasasına kopyalanır ve artımlı yedeklemeler zamanlanır.

## <a name="supported-configurations"></a>Desteklenen yapılandırmalar

Çevrimdışı yedekleme, verileri Şirket içinden Microsoft bulutuna yedekleyen tüm Azure Backup dağıtım modelleriyle desteklenir. Bu modeller şunları içerir:

> [!div class="checklist"]
>
> * MARS Aracısı veya Azure Backup aracısıyla dosya ve klasörlerin yedeklenmesi.
> * Tüm iş yüklerini ve dosyaları DPM ile yedekleyin.
> * Tüm iş yüklerini ve MABS dosyalarını yedekleyin.

>[!NOTE]
>Azure CSP abonelikleri, DPM 2019 RTM ve önceki sürümleri ve MABS v3 RTM ve önceki sürümleri için çevrimdışı dengeli dağıtım ile kullanım için desteklenmez. Ağ üzerinden çevrimiçi yedeklemeler hala desteklenmektedir.

## <a name="prerequisites"></a>Önkoşullar

Çevrimdışı yedekleme iş akışını başlamadan önce aşağıdaki önkoşulların karşılandığından emin olun:

* Bir [Kurtarma Hizmetleri Kasası](backup-azure-recovery-services-vault-overview.md) oluşturuldu. Bir tane oluşturmak için [Kurtarma Hizmetleri Kasası oluşturma](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault) bölümündeki adımları izleyin.
* Bir Azure Backup Aracısı veya MABS ya da DPM, uygun olduğu şekilde Windows Server veya Windows istemcisine yüklenmiştir ve bilgisayar kurtarma hizmetleri kasasına kaydedilir. Yalnızca [Azure Backup en son sürümünün](https://go.microsoft.com/fwlink/?linkid=229525) kullanıldığından emin olun.
* Verilerinizi yedeklemeyi planladığınız bilgisayarda [Azure yayımlama ayarları dosyasını indirin](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) . Yayımlama ayarları dosyasını yüklediğiniz abonelik, kurtarma hizmetleri kasasını içeren abonelikten farklı olabilir. Aboneliğiniz sogeign Azure bulutlarında ise, Azure yayımlama ayarları dosyasını indirmek için aşağıdaki bağlantıları uygun şekilde kullanın.

    | Sovereign bulut bölgesi | Azure yayımlama ayarları dosyası bağlantısı |
    | --- | --- |
    | Birleşik Devletler | [Bağlantı](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | Çin | [Bağlantı](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Yayımlama ayarları dosyasını indirdiğiniz abonelikte Kaynak Yöneticisi dağıtım modeliyle bir Azure depolama hesabı oluşturuldu. Depolama hesabında, hedef olarak kullanılacak yeni bir blob kapsayıcısı oluşturun.

  ![Kaynak Yöneticisi geliştirmeyle bir depolama hesabı oluşturma](./media/offline-backup-dpm-mabs-previous-versions/storage-account-resource-manager.png)

* İlk kopyanızı tutmak için yeterli disk alanına sahip bir ağ paylaşımının veya bilgisayarda, iç veya dış olan herhangi bir ek sürücü olabilen bir hazırlama konumu oluşturulur. Örneğin, bir 500 GB dosya sunucusunu yedeklemek istiyorsanız, hazırlama alanının en az 500 GB olduğundan emin olun. (Sıkıştırma nedeniyle daha küçük bir miktar kullanılır.)
* Azure 'a gönderilen diskler için yalnızca 2,5 inç SSD veya 2,5-inç veya 3,5-inç SATA II/III iç sabit disk sürücülerinin kullanıldığından emin olun. Sabit sürücüleri 10 TB 'a kadar kullanabilirsiniz. Hizmetin desteklediği en son sürücü kümesi için [Azure içeri/dışarı aktarma hizmeti belgelerini](../import-export/storage-import-export-requirements.md#supported-hardware) denetleyin.
* SATA sürücülerin, hazırlama konumundan SATA sürücülere olan yedekleme verilerinin kopyasının yapıldığı bir bilgisayara ( *kopya bilgisayar* olarak adlandırılır) bağlanması gerekir. Kopya bilgisayarda BitLocker 'ın etkinleştirildiğinden emin olun.

## <a name="prepare-the-server-for-the-offline-backup-process"></a>Sunucuyu çevrimdışı yedekleme işlemi için hazırlama

>[!NOTE]
> *AzureOfflineBackupCertGen.exe* gibi listelenen yardımcı programları BULAMıYORSANıZ, Mars Aracısı yüklemenizde, AskAzureBackupTeam@microsoft.com bunlara erişim sağlamak için öğesine yazın.

* Sunucuda yükseltilmiş bir komut istemi açın ve aşağıdaki komutu çalıştırın:

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    Araç, yoksa bir Azure çevrimdışı yedekleme Active Directory uygulaması oluşturur.

    Bir uygulama zaten varsa, bu yürütülebilir dosya sertifikayı Kiracıdaki uygulamaya el ile yüklemenizi ister. Sertifikayı uygulamaya el ile yüklemek için [Bu bölümdeki](#manually-upload-an-offline-backup-certificate) adımları izleyin.

* *AzureOfflineBackupCertGen.exe* aracı bir *OfflineApplicationParams.xml* dosyası oluşturur. Bu dosyayı MABS veya DPM ile sunucuya kopyalayın.
* DPM örneğine veya Azure Backup sunucusuna [en son Mars aracısını](https://aka.ms/azurebackup_agent) yükler.
* Sunucuyu Azure 'a kaydedin.
* Şu komutu çalıştırın:

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname to be used for offline backup>
    ```

* Önceki komut dosyayı oluşturur `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml` .

## <a name="manually-upload-an-offline-backup-certificate"></a>Çevrimdışı yedekleme sertifikasını el ile karşıya yükleme

Çevrimdışı yedekleme sertifikasını çevrimdışı yedekleme için daha önce oluşturulmuş bir Azure Active Directory uygulamasına el ile yüklemek için aşağıdaki adımları izleyin.

1. Azure portalında oturum açın.
1. **Azure Active Directory**  >  **uygulama kayıtları** gidin.
1. Sahip olan **uygulamalar** sekmesinde görünen ad biçimiyle bir uygulama bulun `AzureOfflineBackup _<Azure User Id` .

    ![Uygulamanın sahip olduğu uygulamalar sekmesinde bul](./media/offline-backup-dpm-mabs-previous-versions/owned-applications.png)

1. Uygulamayı seçin. Sol bölmedeki **Yönet** ' ın altında **Sertifikalar & gizlilikler**' a gidin.
1. Önceden var olan sertifikaları veya ortak anahtarları denetleyin. Hiçbiri yoksa, uygulamanın **genel bakış** sayfasında **Sil** düğmesini seçerek uygulamayı güvenle silebilirsiniz. Daha sonra, [sunucuyu çevrimdışı yedekleme işlemine hazırlamak](#prepare-the-server-for-the-offline-backup-process) için adımları yeniden deneyebilir ve aşağıdaki adımları atlayabilirsiniz. Aksi takdirde, çevrimdışı yedeklemeyi yapılandırmak istediğiniz DPM örneğinden veya Azure Backup sunucusundan bu adımları izlemeye devam edin.
1. **Başlat** – **Çalıştır**' dan *Certlm. msc* yazın. **Sertifikalar-Yerel bilgisayar** penceresinde **Sertifikalar – Yerel bilgisayar**  >  **Kişisel** sekmesini seçin. ada sahip sertifikayı arayın `CB_AzureADCertforOfflineSeeding_<ResourceId>` .
1. Sertifikayı seçin, **Tüm görevler**' e sağ tıklayın ve ardından özel anahtar olmadan. cer biçiminde **dışarı aktar**' ı seçin.
1. Azure portal Azure çevrimdışı yedekleme uygulamasına gidin.
1. Sertifikaları **Yönet**  >  **& gizlilikler**  >  **sertifikayı yükle**' yi seçin. Önceki adımda dışarıya aktarılmış sertifikayı karşıya yükleyin.

    ![Sertifikayı güncelleştirin](./media/offline-backup-dpm-mabs-previous-versions/upload-certificate.png)

1. Sunucusunda, Çalıştır penceresine **Regedit** ' i girerek kayıt defterini açın.
1. *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider* kayıt defteri girişine gidin.
1. **CloudBackupProvider**' a sağ tıklayın ve adıyla yeni bir dize değeri ekleyin `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Azure kullanıcı KIMLIĞINI bulmak için aşağıdaki adımlardan birini yapın:
    >
    >* Azure bağlı PowerShell 'den `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as appears in the portal"` komutunu çalıştırın.
    >* Kayıt defteri yoluna gidin `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;` .

1. Önceki adımda eklenen dizeye sağ tıklayın ve **Değiştir**' i seçin. Değerde, 7. adımda verdiğiniz sertifikanın parmak izini girin. Ardından **Tamam**’ı seçin.
1. Parmak izi değerini almak için, sertifikaya çift tıklayın. **Ayrıntılar** sekmesini seçin ve parmak izi alanını görene kadar aşağı kaydırın. **Parmak izi**' ni seçin ve değeri kopyalayın.

    ![Parmak izi alanından değeri Kopyala](./media/offline-backup-dpm-mabs-previous-versions/thumbprint-field.png)

1. Çevrimdışı yedekleme işlemine devam etmek için [Iş akışı](#workflow) bölümüne devam edin.

## <a name="workflow"></a>İş akışı

Bu bölümdeki bilgiler, verilerinizin bir Azure veri merkezine teslim edilebilmesi ve Azure depolama 'ya yüklenebilmesi için çevrimdışı yedekleme iş akışını bitirebilmenizi sağlar. İçeri aktarma hizmeti veya işlemin herhangi bir yönü hakkında sorularınız varsa, daha önce başvurulan [hizmete genel bakış belgelerine](../import-export/storage-import-export-service.md) bakın.

### <a name="initiate-offline-backup"></a>Çevrimdışı Yedeklemeyi Başlat

1. Bir yedekleme zamanladığınızda, Windows Server, Windows istemcisi veya DPM 'de aşağıdaki sayfayı görürsünüz.

    ![İçeri aktarma sayfası](./media/offline-backup-dpm-mabs-previous-versions/offline-backup-screen-inputs.png)

    DPM 'de karşılık gelen sayfa aşağıda verilmiştir. <br/>

    ![DPM ve Azure Backup Sunucusu içeri aktarma sayfası](./media/offline-backup-dpm-mabs-previous-versions/dpm-offline.png)

    İçinde doldurduğunuz kutular şunlardır:

   * **Hazırlama konumu**: ilk yedekleme kopyasının yazıldığı geçici depolama konumu. Hazırlama konumu bir ağ paylaşımında veya yerel bir bilgisayarda olabilir. Kopya bilgisayar ve kaynak bilgisayar farklıysa, hazırlama konumunun tam ağ yolunu belirtin.
   * **Azure yayımlama ayarları**: yayımlama ayarları dosyasının yerel yolu.
   * **Azure Içeri aktarma Işi adı**: Azure Içeri/dışarı aktarma hizmetinin ve Azure Backup disklere gönderilen verilerin Azure 'a aktarılmasını izleyen benzersiz ad.
   * **Azure ABONELIK kimliği**: Azure yayımlama ayarları dosyasını Indirdiğiniz aboneliğin Azure abonelik kimliği.
   * Azure **depolama hesabı**: Azure yayımlama ayarları dosyasıyla ilişkili Azure aboneliğindeki depolama hesabının adı.
   * **Azure depolama kapsayıcısı**: yedekleme verilerinin Içeri aktarıldığı Azure depolama hesabındaki hedef depolama blobunun adı.

   **Hazırlama konumunu** ve verdiğiniz **Azure Içeri aktarma işi adı** bilgilerini kaydedin. Diskleri hazırlamak için gereklidir.

1. İş akışını sona erdirin. Çevrimdışı yedekleme kopyasını başlatmak için Azure Backup aracı yönetim konsolundan **Şimdi Yedekle** ' yi seçin. İlk yedekleme, bu adımın bir parçası olarak hazırlama alanına yazılır.

    ![Şimdi Yedekle](./media/offline-backup-dpm-mabs-previous-versions/backup-now.png)

    DPM veya Azure Backup Sunucusu ilgili iş akışını sona erdirmeyi sağlamak için **koruma grubuna** sağ tıklayın. **Kurtarma noktası oluştur** seçeneğini belirleyin. Sonra **çevrimiçi koruma** seçeneğini belirleyin.

    ![DPM ve MABS Şimdi Yedekle](./media/offline-backup-dpm-mabs-previous-versions/dpm-backup-now.png)

    İşlem bittikten sonra, hazırlama konumu, disk hazırlığı için kullanılabilir hale gelir.

    ![Yedekleme ilerleme durumu](./media/offline-backup-dpm-mabs-previous-versions/op-backup-now.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA sürücüleri hazırlayın ve Azure 'a gönderin

En yakın Azure veri merkezine gönderilen SATA sürücüleri hazırlamak için *AzureOfflineBackupDiskPrep* yardımcı programı kullanılır. Bu yardımcı program, kurtarma hizmetleri aracısının yükleme dizininde aşağıdaki yolda bulunur:

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. Dizinine gidin ve *AzureOfflineBackupDiskPrep* dizinini, HAZıRLANMAKTA olan SATA sürücülerin bağlı olduğu bir kopya bilgisayara kopyalayın. Aşağıdakileri doğrulayın:

   * Kopya bilgisayar, "çevrimdışı yedekleme başlatma" bölümünde iş akışında sağlanan ağ yolunu kullanarak çevrimdışı dağıtım iş akışının hazırlama konumuna erişebilir.
   * BitLocker, kopyalama bilgisayarında etkinleştirilmiştir.
   * Kopya bilgisayar Azure portal erişebilir. Gerekirse, kopya bilgisayar kaynak bilgisayarla aynı olabilir.

     > [!IMPORTANT]
     > Kaynak bilgisayar bir sanal makinedir, kopya bilgisayar olarak farklı bir fiziksel sunucu veya istemci makine kullanılması zorunludur.

1. Geçerli dizin olarak *AzureOfflineBackupDiskPrep* yardımcı program dizini ile kopyalama bilgisayarında yükseltilmiş bir komut istemi açın. Şu komutu çalıştırın:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parametre | Açıklama |
    | --- | --- |
    | s: &lt; *hazırlama konumu yolu*&gt; |Bu zorunlu giriş, "çevrimdışı yedekleme 'yi başlatma" bölümünde iş akışına girdiğiniz hazırlama konumunun yolunu sağlamak için kullanılır. |
    | p: &lt; *PublishSettingsFile yolu*&gt; |Bu isteğe bağlı giriş, "çevrimdışı yedekleme 'yi başlatma" bölümünde iş akışına girdiğiniz Azure yayımlama ayarları dosyasının yolunu sağlamak için kullanılır. |

    > [!NOTE]
    > &lt; &gt; Bilgisayar ve kaynak bilgisayar farklı olduğunda Azuyeniden yayımcı settingfile değerinin yolu zorunludur.
    >
    >

    Komutunu çalıştırdığınızda, yardımcı program, hazırlanması gereken sürücülere karşılık gelen Azure içeri aktarma işinin seçimini ister. Yalnızca tek bir içeri aktarma işi belirtilen hazırlama konumuyla ilişkiliyse, bunun gibi bir sayfa görürsünüz.

    ![Azure disk hazırlama aracı girişi](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-drive-input.png) <br/>

1. Azure 'a aktarmaya hazırlamak istediğiniz bağlı disk için sondaki iki nokta olmadan sürücü harfini girin. İstendiğinde, sürücünün biçimlendirmesi için onay sağlayın.

    Araç daha sonra diski hazırlamaya ve yedekleme verilerini kopyalamaya başlar. Sunulan disk, yedekleme verileri için yeterli alana sahip değilse araç tarafından istendiğinde ek diskler eklemeniz gerekebilir. <br/>

    Araç başarıyla tamamlandıktan sonra, verdiğiniz bir veya daha fazla disk Azure 'a gönderim için hazırlanır. "Çevrimdışı Yedeklemeyi Başlat" bölümünde iş akışı sırasında verdiğiniz ada sahip bir içeri aktarma işi de Azure 'da oluşturulur. Son olarak, araç, disklerin sevk edilmesi gereken Azure veri merkezine sevkiyat adresini görüntüler.

    ![Azure disk hazırlığı tamamlandı](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-success.png)<br/>

1. Komut yürütmenin sonunda, sevkiyat bilgilerini güncelleştirme seçeneğini de görürsünüz.

    ![Gönderim bilgilerini Güncelleştir seçeneği](./media/offline-backup-dpm-mabs-previous-versions/update-shipping-utility.png)<br/>

1. Ayrıntıları hemen girebilirsiniz. Araç, bir dizi girişi kapsayan işlem boyunca size rehberlik eder. İzleme numarası veya gönderme ile ilgili diğer ayrıntılar gibi bilgileriniz yoksa oturumu sona erdirmek için bu bilgileri kullanabilirsiniz. Daha sonra bu makalede sevkiyat ayrıntılarını güncelleştirme adımları verilmiştir.

1. Diskleri, aracın sağlandığı adrese gönderin. Daha sonra başvurmak üzere izleme numarasını saklayın.

   > [!IMPORTANT]
   > İki Azure içeri aktarma işi aynı izleme numarasına sahip olamaz. Tek bir Azure içeri aktarma işi altında yardımcı program tarafından hazırlanan sürücülerin tek bir pakette birlikte ve paket için tek bir benzersiz izleme numarası bulunduğundan emin olun. Farklı Azure içeri aktarma işlerinin bir parçası olarak hazırlanan sürücüleri tek bir pakette birleştirmeyin.

1. İzleme numarası bilgisine sahip olduğunuzda, kaynak bilgisayara gidin ve içeri aktarma işleminin tamamlanması bekleniyor. Geçerli dizin olarak *AzureOfflineBackupDiskPrep* yardımcı programı dizini ile yükseltilmiş bir komut isteminde aşağıdaki komutu çalıştırın.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   İsteğe bağlı olarak, kopya bilgisayar gibi farklı bir bilgisayardan, geçerli dizin olarak *AzureOfflineBackupDiskPrep* yardımcı program dizini ile aşağıdaki komutu çalıştırabilirsiniz.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parametre | Açıklama |
    | --- | --- |
    | larınız | Bu zorunlu giriş, bir Azure içeri aktarma işinin sevkiyat ayrıntılarını güncelleştirmek için kullanılır. |
    | s: &lt; *hazırlama konumu yolu*&gt; | Bu zorunlu giriş, komut kaynak bilgisayarda çalıştırıldığında kullanılır. "Çevrimdışı yedekleme başlatma" bölümünde iş akışına girdiğiniz hazırlama konumunun yolunu sağlamak için kullanılır. |
    | p: &lt; *PublishSettingsFile yolu*&gt; | Bu zorunlu giriş, komut kaynak bilgisayarda çalıştırıldığında kullanılır. "Çevrimdışı yedekleme başlatma" bölümünde iş akışına girdiğiniz Azure yayımlama ayarları dosyasının yolunu sağlamak için kullanılır. |

    Yardımcı program, kaynak bilgisayarın beklediği içeri aktarma işini veya komut farklı bir bilgisayarda çalıştırıldığında hazırlama konumuyla ilişkili içeri aktarma işlerini otomatik olarak algılar. Daha sonra teslim bilgilerini bir dizi giriş aracılığıyla güncelleştirme seçeneğini sağlar.

    ![Sevkiyat bilgilerini girin](./media/offline-backup-dpm-mabs-previous-versions/shipping-inputs.png)<br/>

1. Tüm girişler sağlandığında, ayrıntıları dikkatlice inceleyin ve **Evet** girerek verdiğiniz sevkiyat bilgilerini işleyin.

    ![Sevkiyat bilgilerini gözden geçirme](./media/offline-backup-dpm-mabs-previous-versions/review-shipping-information.png)<br/>

1. Sevkiyat bilgileri başarıyla güncelleştirildikten sonra, hizmet, girdiğiniz sevkıyat ayrıntılarının depolandığı yerel bir konum sağlar.

    ![Mağaza Sevkiyat bilgileri](./media/offline-backup-dpm-mabs-previous-versions/storing-shipping-information.png)<br/>

   > [!IMPORTANT]
   > *AzureOfflineBackupDiskPrep* yardımcı programını kullanarak, sürücülerin sevkiyat bilgilerini sağlamanın iki haftası içinde Azure veri merkezine ulaşmasını sağlayın. Bunun yapılmaması, sürücülerin işlenmesine neden olabilir.

Önceki adımları tamamladıktan sonra, Azure veri merkezi sürücüleri almaya ve yedek verileri sürücülerden oluşturulan klasik Azure depolama hesabına aktarmaya yönelik olarak daha fazla işlem yapmaya hazırlanmalıdır.

### <a name="time-to-process-the-drives"></a>Sürücüleri işleme süresi

Bir Azure içeri aktarma işinin işlenmesi için gereken süre farklılık gösterir. İşlem süresi, Sevkiyat zamanı, iş türü, kopyalandığı verilerin türü ve boyutu ile verilen disklerin boyutu gibi faktörlere bağlıdır. Azure Içeri/dışarı aktarma hizmeti bir SLA 'ya sahip değil. Diskler alındıktan sonra hizmet, Azure depolama hesabınıza 7 ila 10 gün içinde yedekleme verilerinin kopyalanmasını tamamlayacak kadar çaba harcar. Sonraki bölümde, Azure içeri aktarma işinin durumunu nasıl izleyebileceğinizi anlatmaktadır.

### <a name="monitor-azure-import-job-status"></a>Azure içeri aktarma işinin durumunu izleme

Sürücüleriniz aktarım sırasında veya Azure veri merkezinde depolama hesabına kopyalanmak olsa da, Azure Backup Aracısı veya DPM ya da kaynak bilgisayardaki MABS konsolu zamanlanan yedeklemeleriniz için aşağıdaki iş durumunu gösterir:

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

İçeri aktarma işinin durumunu denetlemek için:

1. Kaynak bilgisayarda yükseltilmiş bir komut istemi açın ve aşağıdaki komutu çalıştırın:

     `AzureOfflineBackupDiskPrep.exe u:`

1. Çıktı, içeri aktarma işinin geçerli durumunu gösterir.

    ![İçeri aktarma işinin durumunu denetle](./media/offline-backup-dpm-mabs-previous-versions/import-job-status-reporting.png)<br/>

Azure içeri aktarma işinin çeşitli durumları hakkında daha fazla bilgi için bkz. [Azure içeri/dışarı aktarma işlerinin durumunu görüntüleme](../import-export/storage-import-export-view-drive-status.md).

### <a name="finish-the-workflow"></a>İş akışını tamamlama

İçeri aktarma işi bittikten sonra, depolama hesabınızda ilk yedekleme verileri kullanılabilir. Sonraki zamanlanmış yedekleme sırasında Azure Backup, verilerin içeriğini depolama hesabından kurtarma hizmetleri kasasına kopyalar.

   ![Verileri kurtarma hizmetleri kasasına kopyalama](./media/offline-backup-dpm-mabs-previous-versions/copying-from-storage-account-to-azure-backup.png)<br/>

Sonraki zamanlanmış yedekleme sırasında, Azure Backup ilk yedekleme kopyası üzerinde artımlı yedekleme gerçekleştirir.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Içeri/dışarı aktarma hizmeti iş akışıyla ilgili herhangi bir soru için bkz. [BLOB depolama alanına veri aktarmak için Microsoft Azure içeri/dışarı aktarma hizmetini kullanma](../import-export/storage-import-export-service.md).
