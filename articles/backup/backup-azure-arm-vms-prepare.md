---
title: Azure VM 'lerini bir kurtarma hizmetleri kasasında yedekleme
description: Azure VM 'Leri bir kurtarma hizmetleri kasasında Azure Backup kullanarak nasıl yedekleyeceğiniz açıklanmaktadır.
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 5c3bc66c2111c347f8ed0e32c9e597a52ed404ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104670435"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Azure VM 'lerini bir kurtarma hizmetleri kasasında yedekleme

Bu makalede, Azure VM 'lerinin [Azure Backup](backup-overview.md) hizmeti kullanılarak bir kurtarma hizmetleri kasasında nasıl yedekleneceği açıklanır.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
>
> * Azure VM 'lerini hazırlayın.
> * Kasa oluşturun.
> * VM 'Leri bulun ve bir yedekleme ilkesi yapılandırın.
> * Azure VM 'Leri için yedeklemeyi etkinleştirin.
> * İlk yedeklemeyi çalıştırın.

> [!NOTE]
> Bu makalede, bir kasasının nasıl ayarlanacağı ve yedeklenecek VM 'Lerin nasıl ayarlanacağı açıklanır. Birden çok VM 'yi yedeklemek istiyorsanız bu yararlı olur. Alternatif olarak, [tek bir Azure VM](backup-azure-vms-first-look-arm.md) 'YI doğrudan VM ayarlarından yedekleyebilirsiniz.

## <a name="before-you-start"></a>Başlamadan önce

* Azure VM yedekleme mimarisini [gözden geçirin](backup-architecture.md#architecture-built-in-azure-vm-backup) .
* [Hakkında bilgi edinin](backup-azure-vms-introduction.md) Azure VM yedeklemesi ve yedekleme uzantısı.
* Yedeklemeyi yapılandırmadan önce [destek matrisini gözden geçirin](backup-support-matrix-iaas.md) .

Ayrıca, bazı durumlarda yapmanız gerekebilecek birkaç şey vardır:

* VM **ARACıSıNı VM 'ye yükleme**: Azure Backup makinede çalışan Azure VM aracısına bir uzantı yükleyerek Azure VM 'lerini yedekler. VM 'niz bir Azure Marketi görüntüsünden oluşturulduysa, aracı yüklenir ve çalışır. Özel bir VM oluşturursanız veya şirket içi bir makineyi geçirirseniz, [aracıyı el ile yüklemeniz](#install-the-vm-agent)gerekebilir.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

### <a name="modify-storage-replication"></a>Depolama çoğaltmasını değiştirme

Varsayılan olarak, [kasalar coğrafi olarak yedekli depolama (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage)kullanır.

* Kasa birincil yedekleme mekanizmanız ise GRS kullanmanızı öneririz.
* Bir ucuz seçeneği için [yerel olarak yedekli depolama (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) kullanabilirsiniz.
* Bölgesel olarak [yedekli depolama (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) , verilerinizi [kullanılabilirlik bölgelerinde](../availability-zones/az-overview.md#availability-zones)çoğaltır, bu da aynı bölgedeki veri fazlalığını ve dayanıklılığı garanti ediyor.

Depolama çoğaltma türünü aşağıdaki gibi değiştirin:

1. Yeni kasada, **Ayarlar** bölümünden **Özellikler** ' i seçin.
2. **Özellikler**' de, **yedekleme yapılandırması** altında **Güncelleştir**' i seçin.
3. Depolama çoğaltma türünü seçin ve **Kaydet**' i seçin.

      ![Yeni kasa için depolama yapılandırması ayarlama](./media/backup-azure-arm-vms-prepare/full-blade.png)

> [!NOTE]
   > Kasa ayarlandıktan ve yedekleme öğeleri içerdiğinde depolama çoğaltma türünü değiştiremezsiniz. Bunu yapmak istiyorsanız kasayı yeniden oluşturmanız gerekir.

## <a name="apply-a-backup-policy"></a>Yedekleme ilkesi uygulama

Kasa için bir yedekleme ilkesi yapılandırın.

1. Kasada **genel bakış** bölümünde **+ yedek** ' i seçin.

   ![Yedekleme düğmesi](./media/backup-azure-arm-vms-prepare/backup-button.png)

1.   >  **İş yükünüzün çalıştığı** yedekleme hedefi alanında **Azure**' ı seçin. **Neleri yedeklemek istiyorsunuz?** **sanal makine**  >   **Tamam ' ı** seçin. Bu, VM uzantısını kasaya kaydeder.

   ![Yedekleme ve yedekleme hedef bölmeleri](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

1. **Yedekleme ilkesinde**, kasa ile ilişkilendirmek istediğiniz ilkeyi seçin.
    * Varsayılan ilke, VM 'yi günde bir kez yedekler. Günlük yedeklemeler 30 gün boyunca tutulur. Anlık kurtarma anlık görüntüleri iki gün boyunca tutulur.

      ![Varsayılan yedekleme ilkesi](./media/backup-azure-arm-vms-prepare/default-policy.png)

    * Varsayılan ilkeyi kullanmak istemiyorsanız, **Yeni oluştur**' u seçin ve sonraki yordamda açıklandığı gibi özel bir ilke oluşturun.

1. **Sanal makineler** altında **Ekle**' yi seçin.

      ![Sanal makine Ekle](./media/backup-azure-arm-vms-prepare/add-virtual-machines.png)

1. **Sanal makineler Seç** bölmesi açılır. İlkeyi kullanarak yedeklemek istediğiniz VM 'Leri seçin. Ardından **Tamam**’ı seçin.

   * Seçilen VM 'Ler doğrulanacak.
   * Yalnızca kasala aynı bölgedeki VM 'Leri seçebilirsiniz.
   * VM 'Ler yalnızca tek bir kasada yedeklenebilir.

     !["Sanal makine seçin" bölmesi](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    >[!NOTE]
    > Kasadaki aynı bölgedeki ve abonelikteki tüm VM 'Ler, yedeklemeyi yapılandırmak için kullanılabilir. Yedeklemeyi yapılandırırken, bu VM 'lerde gerekli izne sahip olmadığınız halde sanal makine adına ve kaynak grubuna gidebilirsiniz. VM 'niz geçici olarak silinmiş durumdaysa, bu listede görünmez. VM 'yi yeniden korumanız gerekiyorsa, geçici silme süresinin dolmasını veya geçici olarak silinen listeden VM 'yi geri silmeyi beklemeniz gerekir. Daha fazla bilgi için bkz. [VM 'ler için geçici silme makalesi](soft-delete-virtual-machines.md#soft-delete-for-vms-using-azure-portal).

1. **Yedekleme**'de **yedeklemeyi etkinleştir**' i seçin. Bu, ilkeyi kasaya ve VM 'lere dağıtır ve yedekleme uzantısını Azure VM 'de çalışan VM aracısına kurar.

Yedeklemeyi etkinleştirdikten sonra:

* Yedekleme hizmeti, sanal makinenin çalışıp çalışmadığını, yedekleme uzantısını da yüklüyor.
* İlk yedekleme, yedekleme zamanlamanıza uygun olarak çalışacaktır.
* Yedeklemeler çalıştırıldığında şunları unutmayın:
  * Çalıştıran bir VM, uygulamayla tutarlı bir kurtarma noktası yakalamak için en büyük şansınız vardır.
  * Ancak, VM devre dışı bırakılsa bile yedeklenir. Bu tür bir VM, çevrimdışı bir VM olarak bilinir. Bu durumda, kurtarma noktası kilitlenmeyle tutarlı olacaktır.
* Azure VM 'lerinin yedeklenmesini sağlamak için açık giden bağlantı gerekli değildir.

### <a name="create-a-custom-policy"></a>Özel ilke oluşturma

Yeni bir yedekleme ilkesi oluşturmayı seçtiyseniz, ilke ayarlarını girin.

1. **İlke adı**' nda anlamlı bir ad belirtin.
2. **Yedekleme zamanlaması**' nda, yedeklemelerin ne zaman alınacağını belirtin. Azure VM 'Leri için günlük veya haftalık yedeklemeler gerçekleştirebilirsiniz.
3. **Anlık geri yükleme**' de anlık geri yükleme için anlık görüntüleri yerel olarak ne kadar süreyle bekletmek istediğinizi belirtin.
    * ' Yi geri yüklerken, yedeklenen VM diskleri depolama alanından, ağ üzerinden kurtarma depolama konumuna kopyalanır. Anında geri yükleme sayesinde, yedekleme verilerinin kasaya aktarılmasını beklemeden, bir yedekleme işi sırasında alınan yerel olarak depolanmış anlık görüntülerin üzerinden yararlanabilirsiniz.
    * Anlık geri yüklemenin anlık görüntülerini bir ila beş gün arasında tutabilirsiniz. Varsayılan ayar iki gün olur.
4. **Bekletme aralığı**' nda, günlük veya haftalık yedekleme noktalarınızı ne kadar süreyle saklamak istediğinizi belirtin.
5. **Aylık yedekleme noktası** ve **yıllık yedekleme noktası bekletmesi** için, günlük veya haftalık yedeklemelerinizin aylık veya yıllık yedeklemesini korumak isteyip istemediğinizi belirtin.
6. İlkeyi kaydetmek için **Tamam ' ı** seçin.
    > [!NOTE]
    > Geri yükleme noktası koleksiyonunu (RPC) depolamak için yedekleme hizmeti ayrı bir kaynak grubu (RG) oluşturur. Bu RG, VM 'nin RG 'den farklıdır. [Daha fazla bilgi edinin](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines).

    ![Yeni yedekleme ilkesi](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure Backup, Azure VM yedeklemeleri için günışığından yararlanma değişiklikleri için otomatik saat ayarlamasını desteklemez. Zaman değişikliği gerçekleştiğinde, yedekleme ilkelerini gerektiği şekilde el ile değiştirin.

## <a name="trigger-the-initial-backup"></a>İlk yedeklemeyi tetikleme

İlk yedekleme zamanlamaya uygun olarak çalışır, ancak bunu hemen aşağıdaki gibi çalıştırabilirsiniz:

1. Kasa menüsünde **yedekleme öğeleri**' ni seçin.
2. **Yedekleme öğeleri**' nde **Azure sanal makine**' yi seçin.
3. **Yedekleme öğeleri** listesinde üç nokta (...) simgesini seçin.
4. **Şimdi Yedekle**' yi seçin.
5. **Şimdi Yedekle**' de, kurtarma noktasının tutulacağı son günü seçmek için Takvim denetimini kullanın. Ardından **Tamam**’ı seçin.
6. Portal bildirimlerini izleyin. İş ilerlemesini kasa panosunda izleyebilirsiniz > **yedekleme işleri**  >  **devam** ediyor. VM’nizin boyutuna bağlı olarak, ilk yedeklemenin oluşturulması biraz zaman alabilir.

## <a name="verify-backup-job-status"></a>Yedekleme işinin durumunu doğrulama

Her VM yedeklemesinin yedekleme işi ayrıntıları iki aşamadan oluşur, **anlık görüntü** aşaması ve ardından **verileri kasaya aktar** aşamasına gelir.<br/>
Anlık görüntü aşaması, **anlık geri yüklemeler** için disklerle birlikte depolanan bir kurtarma noktasının kullanılabilirliğini garanti eder ve Kullanıcı tarafından yapılandırılan anlık görüntü bekletmesine bağlı olarak en fazla beş gün boyunca kullanılabilir. Verileri kasaya aktarma, uzun süreli saklama için kasada bir kurtarma noktası oluşturur. Verileri kasaya aktarma işlemi yalnızca anlık görüntü aşaması tamamlandıktan sonra başlar.

  ![Yedekleme Işi durumu](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Aşağıda verilen şekilde **yedekleme işi** ayrıntıları bölmesinden denetlenebilir ön uç yedekleme işi için bir tane olmak üzere arka uçta çalışan Iki **alt görev** vardır:

  ![Yedekleme Işi durumu alt görevleri](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

**Verileri kasaya aktar** aşamasına kadar, disklerin boyutuna, disk başına dalgalanma ve diğer birçok etkene göre tamamlanması çok gün sürebilir.

İş durumu, aşağıdaki senaryolara bağlı olarak değişebilir:

**Görüntüye** | **Verileri kasaya aktar** | **İş Durumu**
--- | --- | ---
Tamamlandı | Sürüyor | Sürüyor
Tamamlandı | Atlandı | Tamamlandı
Tamamlandı | Tamamlandı | Tamamlandı
Tamamlandı | Başarısız | Uyarıyla tamamlandı
Başarısız | Başarısız | Başarısız

Artık bu özellik ile aynı VM için iki yedek paralel çalışabilir, ancak her iki aşamada (anlık görüntü, verileri kasaya aktar) yalnızca bir alt görev çalışıyor olabilir. Bu nedenle, devam eden bir yedekleme işinin bir sonraki güne ait yedeklemenin başarısız olmasına neden olduğu senaryolarda, bu ayrılmış işlevle kaçınılmaz. Sonraki günlerde, yedeklemelerin anlık görüntü tamamlanabilmesi için, önceki bir günün yedekleme işi devam ediyorsa, **verileri kasaya aktarma** işlemi atlanır.
Kasada oluşturulan artımlı kurtarma noktası, kasada oluşturulan en son kurtarma noktasından tüm dalgalanmasını yakalar. Kullanıcı üzerinde herhangi bir maliyet etkisi yoktur.

## <a name="optional-steps"></a>İsteğe bağlı adımlar

### <a name="install-the-vm-agent"></a>VM aracısını yükleme

Azure Backup, makinede çalışan Azure VM aracısına bir uzantı yükleyerek Azure VM 'lerini yedekler. VM 'niz bir Azure Marketi görüntüsünden oluşturulduysa, aracı yüklenir ve çalışır. Özel bir sanal makine oluşturursanız veya şirket içi bir makineyi geçirirseniz, aracıyı tabloda özetlenen şekilde el ile yüklemeniz gerekebilir.

**VM** | **Ayrıntılar**
--- | ---
**Windows** | 1. aracı MSI dosyasını [indirip yükleyin](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) .<br/><br/> 2. makinede yönetici izinleriyle Install.<br/><br/> 3. yüklemeyi doğrulayın. VM 'deki *c:\windowsazure\packages* ' de **WaAppAgent.exe**  >  **Özellikler**' e sağ tıklayın. **Ayrıntılar** sekmesinde **ürün sürümü** 2.6.1198.718 veya üzeri olmalıdır.<br/><br/> Aracıyı güncelleştiriyorsanız, hiçbir yedekleme işlemi olmadığından emin olun ve [aracıyı yeniden yükleyin](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Dağıtım paketi deposundan bir RPM veya bir DEB paketini kullanarak uygulamasını yükler. Bu, Azure Linux aracısını yüklemek ve yükseltmek için tercih edilen yöntemdir. Tüm [onaylı dağıtım sağlayıcıları](../virtual-machines/linux/endorsed-distros.md) , Azure Linux Aracısı paketini görüntülerle ve depolarında tümleştirin. Aracı [GitHub](https://github.com/Azure/WALinuxAgent)'da kullanılabilir ancak buradan yüklemeyi önermiyoruz.<br/><br/> Aracıyı güncelleştiriyorsanız, yedekleme işlemlerinin çalışmakta olmadığından emin olun ve ikili dosyaları güncelleştirin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure VM aracılarıyla](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) veya [Azure VM yedeklemeyle](backup-azure-vms-troubleshoot.md)ilgili sorunları giderin.
* [Geri yükle](backup-azure-arm-restore-vms.md) Azure VM 'Leri.