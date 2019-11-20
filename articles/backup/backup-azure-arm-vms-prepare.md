---
title: Azure VM 'lerini bir kurtarma hizmetleri kasasında yedekleme
description: Azure VM 'Leri bir kurtarma hizmetleri kasasında Azure Backup kullanarak nasıl yedekleyeceğiniz açıklanmaktadır.
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: dc47aa2b4da08a0fc2c9a91b4d547a0d19e1869a
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173353"
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
* **Giden erişime açıkça izin ver**: genel olarak, Azure Backup ile iletişim kurması Için BIR Azure VM 'ye giden ağ erişimine açıkça izin vermeniz gerekmez. Ancak, bazı VM 'Ler bağlantı sorunlarıyla karşılaşabilir ve bağlanmaya çalışırken **Extensionsnapshotfailednonetwork** hatası gösterir. Bu durumda, Azure Backup uzantısı yedekleme trafiği için Azure genel IP adresleriyle iletişim kurabildiğinden, [giden erişime açık bir şekilde izin vermeniz](#explicitly-allow-outbound-access)gerekir.

## <a name="create-a-vault"></a>Kasa oluşturma

 Kasa, zaman içinde oluşturulan yedeklemeleri ve kurtarma noktalarını depolar ve yedeklenen makinelerle ilişkili yedekleme ilkelerini depolar. Aşağıdaki gibi bir kasa oluşturun:

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Arama bölümünde, **Kurtarma Hizmetleri**yazın. **Hizmetler**' in altında, **Kurtarma Hizmetleri kasaları**' na tıklayın.

     ![Kurtarma Hizmetleri kasalarını ara](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

3. **Kurtarma Hizmetleri kasaları** menüsünde **+ Ekle**' ye tıklayın.

     ![Kurtarma Hizmetleri Kasası oluşturma 2. adım](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. **Kurtarma Hizmetleri kasasında**kasayı tanımlamak için bir kolay ad yazın.
    * Adın Azure aboneliği için benzersiz olması gerekir.
    * Bu, 2 ile 50 karakter içerebilir.
    * Bir harfle başlamalı ve yalnızca harf, rakam ve kısa çizgi içerebilir.
5. Kasanın oluşturulması gereken Azure aboneliğini, kaynak grubunu ve coğrafi bölgeyi seçin. Sonra **Oluştur**’a tıklayın.
    * Kasanın oluşturulması biraz zaman alabilir.
    * Portalın sağ üst bölümündeki durum bildirimlerini izleyin.

Kasa oluşturulduktan sonra kurtarma hizmetleri kasaları listesinde görünür. Kasanızı görmüyorsanız **Yenile**' yi seçin.

![Yedekleme kasalarının listesi](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

> [!NOTE]
> Azure Backup hizmet, anlık görüntüyü depolamak için (örneğin: AzureBackupRG_northeurope_1) **AzureBackupRG_geography_number** , farklı bir kaynak grubu (VM kaynak grubu dışında) oluşturur. Bu kaynak grubundaki veriler, Azure sanal makine yedekleme ilkesinin *anlık kurtarma anlık görüntüsünü sakla* bölümünde belirtildiği gibi süre boyunca saklanacaktır.  Bu kaynak grubuna bir kilit uygulandığında yedekleme hatalara neden olabilir.<br>
Bu kaynak grubu, bir kısıtlama ilkesi herhangi bir ad/etiket kısıtlamalarından dışlanmalıdır, çünkü bu durum, bir kısıtlama ilkesi üzerinde kaynak noktası koleksiyonlarının oluşturulmasını engeller.

### <a name="modify-storage-replication"></a>Depolama çoğaltmasını değiştirme

Varsayılan olarak, [kasalar coğrafi olarak yedekli depolama (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)kullanır.

* Kasa birincil yedekleme mekanizmanız ise GRS kullanmanızı öneririz.
* Bir ucuz seçeneği için [yerel olarak yedekli depolama (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) kullanabilirsiniz.

Depolama çoğaltma türünü aşağıdaki gibi değiştirin:

1. Yeni kasada, **Ayarlar** bölümünde **Özellikler** ' e tıklayın.
2. **Özellikler**' de, **yedekleme yapılandırması**altında **Güncelleştir**' e tıklayın.
3. Depolama çoğaltma türünü seçin ve **Kaydet**' e tıklayın.

      ![Yeni kasa için depolama yapılandırması ayarlama](./media/backup-try-azure-backup-in-10-mins/full-blade.png)

> [!NOTE]
   > Kasa ayarlandıktan ve yedekleme öğeleri içerdiğinde depolama çoğaltma türünü değiştiremezsiniz. Bunu yapmak istiyorsanız kasayı yeniden oluşturmanız gerekir.

## <a name="apply-a-backup-policy"></a>Yedekleme ilkesi uygulama

Kasa için bir yedekleme ilkesi yapılandırın.

1. Kasada **genel bakış** bölümünde **+ Yedekle** ' ye tıklayın.

   ![Yedekleme düğmesi](./media/backup-azure-arm-vms-prepare/backup-button.png)

2. **Yedekleme hedefi** 'nde > **Iş yükünüz nerede çalışıyor?** **Azure**' u seçin. **Neleri yedeklemek istiyorsunuz?** **sanal makine** >  **Tamam ' ı**seçin. Bu, VM uzantısını kasaya kaydeder.

   ![Yedekleme ve yedekleme hedef bölmeleri](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. **Yedekleme ilkesinde**, kasa ile ilişkilendirmek istediğiniz ilkeyi seçin.
    * Varsayılan ilke, VM 'yi günde bir kez yedekler. Günlük yedeklemeler 30 gün boyunca tutulur. Anlık kurtarma anlık görüntüleri iki gün boyunca tutulur.
    * Varsayılan ilkeyi kullanmak istemiyorsanız, **Yeni oluştur**' u seçin ve sonraki yordamda açıklandığı gibi özel bir ilke oluşturun.

      ![Varsayılan yedekleme ilkesi](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. **Sanal makineler Seç**bölümünde, ilkeyi kullanarak yedeklemek Istediğiniz VM 'leri seçin. Daha sonra, **Tamam**'a tıklayın.

   * Seçilen VM 'Ler doğrulanacak.
   * Yalnızca kasala aynı bölgedeki VM 'Leri seçebilirsiniz.
   * VM 'Ler yalnızca tek bir kasada yedeklenebilir.

     !["Sanal makine seçin" bölmesi](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. **Yedekleme**'de **yedeklemeyi etkinleştir**' e tıklayın. Bu, ilkeyi kasaya ve VM 'lere dağıtır ve yedekleme uzantısını Azure VM 'de çalışan VM aracısına kurar.

     !["Yedeklemeyi etkinleştir" düğmesi](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Yedeklemeyi etkinleştirdikten sonra:

* Yedekleme hizmeti, sanal makinenin çalışıp çalışmadığını, yedekleme uzantısını da yüklüyor.
* İlk yedekleme, yedekleme zamanlamanıza uygun olarak çalışacaktır.
* Yedeklemeler çalıştırıldığında şunları unutmayın:
  * Çalıştıran bir VM, uygulamayla tutarlı bir kurtarma noktası yakalamak için en büyük şansınız vardır.
  * Ancak, VM kapatılmış olsa bile. Bu tür bir VM, çevrimdışı bir VM olarak bilinir. Bu durumda, kurtarma noktası kilitlenmeyle tutarlı olacaktır.

### <a name="create-a-custom-policy"></a>Özel ilke oluşturma

Yeni bir yedekleme ilkesi oluşturmayı seçtiyseniz, ilke ayarlarını girin.

1. **İlke adı**' nda anlamlı bir ad belirtin.
2. **Yedekleme zamanlaması**' nda, yedeklemelerin ne zaman alınacağını belirtin. Azure VM 'Leri için günlük veya haftalık yedeklemeler gerçekleştirebilirsiniz.
3. **Anlık geri yükleme**' de anlık geri yükleme için anlık görüntüleri yerel olarak ne kadar süreyle bekletmek istediğinizi belirtin.
    * ' Yi geri yüklerken, yedeklenen VM diskleri depolama alanından, ağ üzerinden kurtarma depolama konumuna kopyalanır. Anlık geri yükleme sayesinde, yedekleme verilerinin kasaya aktarılmasını beklemeden, bir yedekleme işi sırasında alınan yerel olarak depolanmış anlık görüntülerden yararlanabilirsiniz.
    * Anlık geri yüklemenin anlık görüntülerini bir ila beş gün arasında tutabilirsiniz. Varsayılan ayar iki gün olur.
4. **Bekletme aralığı**' nda, günlük veya haftalık yedekleme noktalarınızı ne kadar süreyle saklamak istediğinizi belirtin.
5. **Aylık yedekleme noktasını bekletmek**için günlük veya haftalık yedeklemelerinizin aylık bir yedeklemesini korumak isteyip istemediğinizi belirtin.
6. İlkeyi kaydetmek için **Tamam** ' ı tıklatın.

    ![Yeni yedekleme ilkesi](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure Backup, Azure VM yedeklemeleri için günışığından yararlanma değişiklikleri için otomatik saat ayarlamasını desteklemez. Zaman değişikliği gerçekleştiğinde, yedekleme ilkelerini gerektiği şekilde el ile değiştirin.

## <a name="trigger-the-initial-backup"></a>İlk yedeklemeyi tetikleme

İlk yedekleme zamanlamaya uygun olarak çalışır, ancak bunu hemen aşağıdaki gibi çalıştırabilirsiniz:

1. Kasa menüsünde, **yedekleme öğeleri**' ne tıklayın.
2. **Yedekleme öğeleri**' nde **Azure sanal makine**' ye tıklayın.
3. **Yedekleme öğeleri** listesinde üç noktaya (...) tıklayın.
4. **Şimdi Yedekle**'ye tıklayın.
5. **Şimdi Yedekle**' de, kurtarma noktasının tutulacağı son günü seçmek için Takvim denetimini kullanın. Daha sonra, **Tamam**'a tıklayın.
6. Portal bildirimlerini izleyin. İş ilerlemesini kasa panosunda izleyebilirsiniz > **yedekleme işleri** > **devam**ediyor. VM’nizin boyutuna bağlı olarak, ilk yedeklemenin oluşturulması biraz zaman alabilir.

## <a name="verify-backup-job-status"></a>Yedekleme işinin durumunu doğrulama

Her VM yedeklemesinin yedekleme işi ayrıntıları iki aşamadan oluşur, **anlık görüntü** aşaması ve ardından **verileri kasaya aktar** aşamasına gelir.<br/>
Anlık görüntü aşaması, **anlık geri yüklemeler** için disklerle birlikte depolanan bir kurtarma noktasının kullanılabilirliğini garanti eder ve Kullanıcı tarafından yapılandırılan anlık görüntü bekletmesine bağlı olarak en fazla beş gün boyunca kullanılabilir. Verileri kasaya aktarma, uzun süreli saklama için kasada bir kurtarma noktası oluşturur. Verileri kasaya aktarma işlemi yalnızca anlık görüntü aşaması tamamlandıktan sonra başlar.

  ![Yedekleme Işi durumu](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Arka uçta çalışan, **yedekleme işi** ayrıntıları dikey penceresinden aşağıda verilen şekilde denetlenen bir ön uç yedekleme işi olan Iki **alt görev** vardır:

  ![Yedekleme Işi durumu](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

**Verileri kasaya aktar** aşamasına kadar, disklerin boyutuna, disk başına dalgalanma ve diğer birçok etkene göre tamamlanması çok gün sürebilir.

İş durumu, aşağıdaki senaryolara bağlı olarak değişebilir:

**Görüntüye** | **Verileri kasaya aktar** | **İş durumu**
--- | --- | ---
Tamamlandı | Devam ediyor | Devam ediyor
Tamamlandı | Atlanmış | Tamamlandı
Tamamlandı | Tamamlandı | Tamamlandı
Tamamlandı | Başarısız | Uyarıyla tamamlandı
Başarısız | Başarısız | Başarısız

Artık bu özellik ile aynı VM için iki yedek paralel çalışabilir, ancak her iki aşamada (anlık görüntü, verileri kasaya aktar) yalnızca bir alt görev çalışıyor olabilir. Bu nedenle, senaryolarda devam eden bir yedekleme işi olduğundan, bu ayırma işleviyle birlikte yedeklemenin başarısız olmasına neden olur. Sonraki günün yedeklemeleri, daha önceki bir günün yedekleme işi devam ediyorsa, **kasaya veri aktarımı** atlanırken anlık görüntü tamamlanabilir.
Kasada oluşturulan artımlı kurtarma noktası, kasada oluşturulan son kurtarma noktasındaki tüm karmaşıklığı yakalar. Kullanıcı üzerinde bir ücret etkisi yoktur.

## <a name="optional-steps-install-agentallow-outbound"></a>İsteğe bağlı adımlar (aracıyı yükler/giden izin ver)

### <a name="install-the-vm-agent"></a>VM aracısını yükler

Azure Backup, makinede çalışan Azure VM aracısına bir uzantı yükleyerek Azure VM 'lerini yedekler. VM 'niz bir Azure Marketi görüntüsünden oluşturulduysa, aracı yüklenir ve çalışır. Özel bir sanal makine oluşturursanız veya şirket içi bir makineyi geçirirseniz, aracıyı tabloda özetlenen şekilde el ile yüklemeniz gerekebilir.

**'Nın** | **Ayrıntılar**
--- | ---
**Windows** | 1. aracı MSI dosyasını [indirip yükleyin](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) .<br/><br/> 2. makinede yönetici izinleriyle Install.<br/><br/> 3. yüklemeyi doğrulayın. VM 'deki *C:\windowsazure\packages* ' de, **Waappagent. exe** > **Özellikler**' e sağ tıklayın. **Ayrıntılar** sekmesinde **ürün sürümü** 2.6.1198.718 veya üzeri olmalıdır.<br/><br/> Aracıyı güncelleştiriyorsanız, hiçbir yedekleme işlemi olmadığından emin olun ve [aracıyı yeniden yükleyin](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Dağıtım paketi deposundan bir RPM veya bir DEB paketini kullanarak uygulamasını yükler. Bu, Azure Linux aracısını yüklemek ve yükseltmek için tercih edilen yöntemdir. Tüm [onaylı dağıtım sağlayıcıları](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) , Azure Linux Aracısı paketini görüntülerle ve depolarında tümleştirin. Aracı [GitHub](https://github.com/Azure/WALinuxAgent)'da kullanılabilir ancak buradan yüklemeyi önermiyoruz.<br/><br/> Aracıyı güncelleştiriyorsanız, yedekleme işlemlerinin çalışmakta olmadığından emin olun ve ikili dosyaları güncelleştirin.

### <a name="explicitly-allow-outbound-access"></a>Giden erişime açıkça izin ver

VM üzerinde çalışan yedekleme uzantısının Azure genel IP adreslerine giden erişimi olması gerekir.

* Genellikle, Azure Backup ile iletişim kurması için bir Azure VM 'ye giden ağ erişimine açıkça izin vermeniz gerekmez.
* VM 'lerle ilgili zorluklarla karşılaşıyorsanız veya bağlanmaya çalışırken **Extensionsnapshotfailednonetwork** hatası görürseniz, yedekleme uzantısının yedekleme trafiği için Azure genel IP adresleriyle iletişim kurabilmesi için açıkça erişime izin vermelisiniz. Erişim yöntemleri aşağıdaki tabloda özetlenmiştir.

**Seçenek** | **Eylem** | **Ayrıntılar**
--- | --- | ---
**NSG kurallarını ayarlama** | [Azure veri MERKEZI IP aralıklarına](https://www.microsoft.com/download/details.aspx?id=41653)izin verin.<br/><br/> Her adres aralığını izin vermek ve yönetmek yerine, bir [hizmet etiketi](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure)kullanarak Azure Backup hizmetine erişim sağlayan bir kural ekleyebilirsiniz. | Hizmet etiketleri hakkında [daha fazla bilgi edinin](../virtual-network/security-overview.md#service-tags) .<br/><br/> Hizmet etiketleri erişim yönetimini basitleştirir ve ek ücret vermez.
**Proxy dağıtma** | Yönlendirme trafiği için bir HTTP proxy sunucusu dağıtın. | Yalnızca depolama alanı değil, Azure 'un tamamına erişim sağlar.<br/><br/> Depolama URL 'Lerine ayrıntılı denetime izin verilir.<br/><br/> VM 'Ler için tek bir internet erişimi noktası.<br/><br/> Ara sunucu için ek maliyetler.
**Azure Güvenlik duvarını ayarlama** | Azure Backup hizmeti için bir FQDN etiketi kullanarak VM 'deki Azure Güvenlik Duvarı üzerinden trafiğe izin ver | Sanal ağ alt ağında Azure Güvenlik duvarınız ayarlandıysa, kullanımı basittir.<br/><br/> Kendi FQDN etiketlerinizi oluşturamaz veya bir etikette FQDN 'leri değiştirebilirsiniz.<br/><br/> Azure VM 'lerinizde yönetilen diskler varsa güvenlik duvarları üzerinde ek bir bağlantı noktası (8443) açmanız gerekebilir.

#### <a name="establish-network-connectivity"></a>Ağ bağlantısı kurma

NSG ile, ara sunucuya veya güvenlik duvarı üzerinden bağlantı kurma

##### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Azure 'a giden erişime izin vermek için bir NSG kuralı ayarlama

Bir NSG VM erişimini yönetirse, yedekleme depolama için gerekli aralıklar ve bağlantı noktalarına giden erişime izin verin.

1. VM Özellikleri > **ağ**' da, **giden bağlantı noktası kuralı ekle**' yi seçin.
2. **Giden güvenlik kuralı ekle**' de **Gelişmiş**' i seçin.
3. **Kaynak**bölümünde **VirtualNetwork**öğesini seçin.
4. **Kaynak bağlantı noktası aralıklarında**, herhangi bir bağlantı noktasından giden erişime izin vermek için bir yıldız işareti (*) girin.
5. **Hedef**bölümünde **hizmet etiketi**' ni seçin. Listeden **Storage. Region**' ı seçin. Bölge, kasanın ve yedeklemek istediğiniz VM 'Lerin bulunduğu yerdir.
6. **Hedef bağlantı noktası aralıklarında**bağlantı noktasını seçin.
    * Şifrelenmemiş depolama hesabıyla yönetilmeyen diskler kullanan VM: 80
    * Şifrelenmiş depolama hesabıyla yönetilmeyen diskler kullanan VM: 443 (varsayılan ayar)
    * Yönetilen diskleri kullanan VM: 8443.
7. **Protokol**' de **TCP**' yi seçin.
8. **Öncelik**bölümünde, daha yüksek reddetme kurallarından daha düşük bir öncelik değeri belirtin.

   Erişimi engelleyen bir kuralınız varsa, yeni izin verme kuralı daha yüksek olmalıdır. Örneğin, öncelik 1000 ' de ayarlanmış bir **Deny_All** kuralınız varsa, yeni kuralınızın ayarı 1000 ' den az olmalıdır.
9. Kural için bir ad ve Açıklama girip **Tamam**' ı seçin.

Giden erişime izin vermek için NSG kuralını birden çok VM 'ye uygulayabilirsiniz. Bu video, süreç boyunca size yol gösterir.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]

##### <a name="route-backup-traffic-through-a-proxy"></a>Yedekleme trafiğini bir ara sunucu aracılığıyla yönlendirme

Yedekleme trafiğini bir ara sunucu üzerinden yönlendirebilir ve ardından gerekli Azure aralıklarına proxy erişimi verebilirsiniz. Proxy VM 'yi aşağıdakilere izin verecek şekilde yapılandırın:

* Azure VM, genel İnternet 'e ait tüm HTTP trafiğini ara sunucu aracılığıyla yönlendirmelidir.
* Proxy, geçerli sanal ağdaki VM 'lerden gelen trafiğe izin verir.
* NSG, sanal makinenin proxy VM 'den giden internet trafiğine izin veren bir kurala ihtiyacı vardır.

###### <a name="set-up-the-proxy"></a>Proxy 'yi ayarlama

Bir sistem hesabı ara sunucusu yoksa, bir tane aşağıdaki şekilde ayarlayın:

1. [PsExec](https://technet.microsoft.com/sysinternals/bb897553)'yi indirin.
2. Komut istemi 'ni bir sistem hesabı altında çalıştırmak için **PsExec. exe-i-s cmd. exe** ' yi çalıştırın.
3. Tarayıcıyı sistem bağlamında çalıştırın. Örneğin, Internet Explorer için **%ProgramFiles%\Internet Explorer\iexplore.exe** kullanın.  
4. Proxy ayarlarını tanımlayın.
   * Linux makinelerde:
     * Bu satırı **/etc/Environment** dosyasına ekleyin:
       * **http_proxy = http:\//Proxy IP adresi: ara sunucu bağlantı noktası**
     * Bu satırları **/etc/waagent.exe** dosyasına ekleyin:
         * **HttpProxy. Host = Proxy IP adresi**
         * **HttpProxy. Port = proxy bağlantı noktası**
   * Windows makinelerinde, tarayıcı ayarları ' nda, bir ara sunucu kullanılması gerektiğini belirtin. Şu anda bir kullanıcı hesabında ara sunucu kullanıyorsanız, ayarı sistem hesabı düzeyinde uygulamak için bu betiği kullanabilirsiniz.

       ```powershell
      $obj = Get-ItemProperty -Path Registry::"HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
      $obj = Get-ItemProperty -Path Registry::"HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

       ```

###### <a name="allow-incoming-connections-on-the-proxy"></a>Ara sunucuda gelen bağlantılara izin ver

Proxy ayarlarında gelen bağlantılara izin verin.

1. Windows Güvenlik Duvarı 'nda, **Gelişmiş Güvenlik Özellikli Windows Güvenlik Duvarı 'nı**açın.
2. **Yeni kural** > **gelen kurallar** ' a sağ tıklayın.
3. **Kural türü**' nde, **özel** > **İleri**' yi seçin.
4. **Programda**, **Ileri** > **tüm programlar** ' ı seçin.
5. **Protokoller ve bağlantı noktaları**:
   * Türü **TCP**olarak ayarlayın.
   * **Yerel bağlantı noktalarını** **belirli bağlantı noktalarına**ayarlayın.
   * **Uzak bağlantı noktasını** **tüm bağlantı noktalarına**ayarlayın.

6. Sihirbazı tamamlayın ve kural için bir ad belirtin.

###### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>Proxy için NSG 'ye bir özel durum kuralı ekleme

NSG, 10.0.0.5üzerindeki herhangi bir bağlantı noktasından gelen trafiğe, bağlantı noktası 80 (http) veya 443 (https) üzerinde herhangi bir internet adresine izin verin.

Aşağıdaki PowerShell betiği trafiğe izin vermek için bir örnek sağlar.
Herkese açık internet adreslerine izin vermek yerine bir IP adresi aralığı (`-DestinationPortRange`) belirtebilir veya Storage. Region hizmet etiketini kullanabilirsiniz.

```powershell
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

##### <a name="allow-firewall-access-with-an-fqdn-tag"></a>FQDN etiketiyle güvenlik duvarı erişimine izin ver

Azure Backup ağ trafiğine giden erişime izin vermek için Azure Güvenlik Duvarı 'nı ayarlayabilirsiniz.

* Azure Güvenlik Duvarı 'Nı dağıtma [hakkında bilgi edinin](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) .
* [Hakkında bilgi edinin](https://docs.microsoft.com/azure/firewall/fqdn-tags) FQDN etiketleri.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure VM aracılarıyla](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) veya [Azure VM yedeklemeyle](backup-azure-vms-troubleshoot.md)ilgili sorunları giderin.
* [Geri yükle](backup-azure-arm-restore-vms.md) Azure VM 'Leri.
