---
title: Öğretici-Azure Site Recovery ile Windows VM 'Leri için olağanüstü durum kurtarmayı ayarlama
description: Windows VM 'Leri için olağanüstü durum kurtarmayı Azure Site Recovery hizmetini kullanarak farklı bir Azure bölgesine nasıl etkinleştireceğinizi öğrenin.
author: rayne-wiselman
ms.service: virtual-machines
ms.collection: windows
ms.subservice: recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: fd5d8c3e2c6e4ee5556568ebd23ac99b48300e9d
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382039"
---
# <a name="tutorial-enable-disaster-recovery-for-windows-vms"></a>Öğretici: Windows VM 'Leri için olağanüstü durum kurtarmayı etkinleştirme

Bu öğreticide, Windows çalıştıran Azure VM 'Leri için olağanüstü durum kurtarmayı ayarlama gösterilmektedir. Bu makalede şunları yapmayı öğreneceksiniz:

> [!div class="checklist"]
> * Windows VM için olağanüstü durum kurtarmayı etkinleştirme
> * Beklendiği gibi çalışıp çalışmadığını denetlemek için bir olağanüstü durum kurtarma ayrıntısı çalıştırın
> * Ayrıntıdan sonra VM 'yi çoğaltmayı durdur

Bir sanal makine için çoğaltmayı etkinleştirdiğinizde, Site Recovery Mobility hizmeti uzantısı sanal makineye yüklenir ve [Azure Site Recovery](../../site-recovery/site-recovery-overview.md)kaydeder. Çoğaltma sırasında, VM disk yazmaları kaynak bölgedeki bir önbellek depolama hesabına gönderilir. Veriler buradan hedef bölgeye gönderilir ve kurtarma noktaları verilerden oluşturulur.  Olağanüstü durum kurtarma sırasında bir VM 'nin yükünü devretmek, hedef bölgede bir VM oluşturmak için bir kurtarma noktası kullanılır.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

1. Azure aboneliğinizin hedef bölgede bir VM oluşturmanıza izin verdiğinden emin olun. Ücretsiz Azure hesabınızı oluşturduysanız, aboneliğin yöneticisi olursunuz ve ihtiyacınız olan izinleriniz vardır.
2. Abonelik yöneticisi değilseniz, size atamak için yönetici ile çalışın:
    - Sanal makine katılımcısı için yerleşik rol ya da belirli izinler:
        - Seçilen sanal ağda sanal makine oluşturma.
        - Bir Azure depolama hesabına yazın.
        - Azure tarafından yönetilen bir diske yazma.
    - Site Recovery katkıda bulunan yerleşik rolü, kasadaki Site Recovery işlemlerini yönetmek için. 
3. Windows Server 2012 veya üstünü çalıştıran bir Windows VM kullanmanızı öneririz. Bu öğreticinin amacına uygun olarak VM diski şifrelenmemelidir.
4. VM giden bağlantılar, URL tabanlı bir ara sunucu kullanıyorsa, bu URL 'Lere erişebildiğinden emin olun. Kimliği doğrulanmış bir ara sunucu kullanılması desteklenmez.

    **Ad** | **Genel bulut** | **Kamu Bulutu** | **Ayrıntılar**
    --- | --- | --- | ---
    Depolama | `*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`| VM 'den kaynak bölgesindeki önbellek depolama hesabına veri yazın. 
    Azure AD  | `login.microsoftonline.com` | `login.microsoftonline.us`| Site Recovery hizmeti URL 'Lerini yetkilendirme ve kimlik doğrulama. 
    Çoğaltma | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`  |Site Recovery hizmetiyle VM iletişimi. 
    Service Bus | `*.servicebus.windows.net` | `*.servicebus.usgovcloudapi.net` | Site Recovery izleme ve tanılama verilerine yönelik VM yazma işlemleri. 

4. VM 'Ler için ağ trafiğini sınırlandırmak üzere ağ güvenlik grupları (NSG) kullanıyorsanız, bu hizmet etiketlerini (IP adresi grupları) kullanarak VM için giden bağlantıya (HTTPS 443) izin veren NSG kuralları oluşturun. Önce bir test NSG üzerindeki kuralları deneyin.

    **Tag** | **İzin Ver** 
    --- | --- 
    Depolama etiketi | Verilerin VM 'den önbellek depolama hesabına yazılmasına izin verir.
    Azure AD etiketi | Azure AD 'ye karşılık gelen tüm IP adreslerine erişim sağlar.
    EventsHub etiketi | Site Recovery izlemeye erişim sağlar.
    Azuresterecovery etiketi | Herhangi bir bölgedeki Site Recovery hizmetine erişim sağlar.
    GuestAndHybridManagement | Çoğaltma için etkinleştirilen VM 'lerde çalışan Site Recovery Mobility aracısını otomatik olarak yükseltmek istiyorsanız kullanın.
5.  Windows VM 'lerinde, en son Windows güncelleştirmelerini yükleyerek VM 'Lerin en son kök sertifikalara sahip olduğundan emin olun.

## <a name="create-a-vm-and-enable-disaster-recovery"></a>VM oluşturma ve olağanüstü durum kurtarmayı etkinleştirme

Bir VM oluşturduğunuzda, isteğe bağlı olarak olağanüstü durum kurtarmayı etkinleştirebilirsiniz.

1. [BIR VM oluşturun](quick-create-portal.md).
2. **Yönetim** sekmesinde **olağanüstü durum kurtarmayı etkinleştir**' i seçin.
3. **İkincil bölgede**, olağanüstü durum kurtarma IÇIN bir VM 'yi çoğaltmak istediğiniz hedef bölgeyi seçin.
4. **İkincil abonelikte**, hedef VM 'nin oluşturulacağı hedef aboneliği seçin. Kaynak bölgedeki kaynak sanal makineyi hedef bölgeye devretmek için hedef VM oluşturulur.
5. **Kurtarma Hizmetleri Kasası**' nda, çoğaltma için kullanmak istediğiniz kasayı seçin. Kasanız yoksa **Yeni oluştur**' u seçin. Kasanın yerleştirileceği bir kaynak grubu ve kasa adını seçin.
6. **Site Recovery ilke**' de, varsayılan ilkeyi bırakın veya özel değerleri ayarlamak Için **Yeni oluştur** ' u seçin.

    - Kurtarma noktaları, belirli bir zaman noktasında alınan VM disklerinin anlık görüntülerinden oluşturulur. Bir VM 'nin yükünü devretmek için, hedef bölgedeki VM 'yi geri yüklemek üzere bir kurtarma noktası kullanırsınız. 
    - Her beş dakikada bir çökme ile tutarlı bir kurtarma noktası oluşturulur. Bu ayar değiştirilemez. Kilitlenme ile tutarlı bir anlık görüntü, anlık görüntü çekilirken diskteki verileri yakalar. Bellekte herhangi bir şey içermez. 
    - Varsayılan olarak Site Recovery, kilitlenme tutarlı kurtarma noktalarını 24 saat boyunca tutar. 0 ila 72 saat arasında bir özel değer ayarlayabilirsiniz.
    - Her 4 saatte bir uygulamayla tutarlı bir anlık görüntü alınır. Uygulamayla tutarlı anlık görüntü 
    - Varsayılan olarak Site Recovery kurtarma noktalarını 24 saat depolar.

7. **Kullanılabilirlik seçenekleri**' nde, VM 'nin tek başına, bir kullanılabilirlik alanında veya bir kullanılabilirlik kümesinde dağıtıp dağıtmadığını belirtin.

    ::: Image Type = "content" kaynak = "./Media/tutorial-Disaster-Recovery/create-vm.png" alt-text = "VM yönetim özellikleri sayfasında çoğaltmayı etkinleştirin."

8. VM oluşturmayı tamamlama.

## <a name="enable-disaster-recovery-for-an-existing-vm"></a>Mevcut bir VM için olağanüstü durum kurtarmayı etkinleştirme

Yeni bir VM yerine var olan bir VM 'de olağanüstü durum kurtarmayı etkinleştirmek istiyorsanız, bu yordamı kullanın.

1. Azure portal VM özellikleri sayfasını açın.
2. **İşlemler** menüsünden **Olağanüstü durum kurtarma** seçeneğini belirleyin.

    :::image type="content" source="./media/tutorial-disaster-recovery/existing-vm.png" alt-text="Mevcut bir VM için olağanüstü durum kurtarma seçeneklerini açın.":::

3. **Temel bilgiler**, VM bir kullanılabilirlik alanında dağıtılmışsa, kullanılabilirlik alanları arasında olağanüstü durum kurtarmayı seçebilirsiniz.
4. **Hedef bölge**' de, VM 'yi çoğaltmak istediğiniz bölgeyi seçin. Kaynak ve hedef bölgeler aynı Azure Active Directory kiracısında olmalıdır.

    :::image type="content" source="./media/tutorial-disaster-recovery/basics.png" alt-text="Bir VM için temel olağanüstü durum kurtarma seçeneklerini ayarlayın.":::

5. **İleri ' yi seçin: Gelişmiş ayarlar**.
6. **Gelişmiş ayarlar**' da ayarları gözden geçirebilir ve değerleri özel ayarlar ' da değiştirebilirsiniz. Varsayılan olarak, Site Recovery hedef kaynakları oluşturmak için kaynak ayarlarını yansıtır.

    - **Hedef abonelik**. Yük devretmeden sonra hedef VM 'nin oluşturulduğu abonelik.
    - **Hedef VM kaynak grubu**. Yük devretmeden sonra hedef VM 'nin oluşturulduğu kaynak grubu.
    - **Hedef sanal ağ**. Yük devretmeden sonra oluşturulduğu sırada hedef VM 'nin bulunduğu Azure sanal ağı.
    - **Hedef kullanılabilirliği**. Hedef VM tek bir örnek olarak oluşturulduğunda, bir kullanılabilirlik kümesinde veya kullanılabilirlik bölgesinde.
    - **Yakınlık yerleşimi**. Uygulanabiliyorsa, yük devretmeden sonra hedef VM 'nin bulunduğu yakınlık yerleşimi grubunu seçin.
    - **Depolama ayarları-önbellek depolama hesabı**. Kurtarma, kaynak bölgedeki bir depolama hesabını geçici bir veri deposu olarak kullanır. Kaynak VM değişiklikleri, hedef konuma çoğaltılmadan önce bu hesapta önbelleğe alınır.
        - Varsayılan olarak, kasa başına bir önbellek depolama hesabı oluşturulur ve yeniden kullanılır.
        - VM için önbellek hesabını özelleştirmek istiyorsanız farklı bir depolama hesabı seçebilirsiniz.
    - **Depolama ayarları-çoğaltma yönetilen disk**. Site Recovery, varsayılan olarak, hedef bölgede çoğaltma tarafından yönetilen diskler oluşturur.
        -  Varsayılan olarak, hedef yönetilen disk, aynı depolama türünü (Standart HDD/SSD veya Premium SSD) kullanarak kaynak VM tarafından yönetilen diskleri yansıtır.
        - Depolama türünü gerektiği gibi özelleştirebilirsiniz.
    - **Çoğaltma ayarları**. VM 'nin bulunduğu kasayı ve VM için kullanılan çoğaltma ilkesini gösterir. Varsayılan olarak, VM için Site Recovery tarafından oluşturulan kurtarma noktaları 24 saat boyunca tutulur.
    - **Uzantı ayarları**. Site Recovery, çoğaltılan VM 'lerde yüklü olan Site Recovery Mobility hizmeti uzantısına yönelik güncelleştirmeleri yönettiğini gösterir.
        - Belirtilen Azure Otomasyonu hesabı güncelleştirme işlemini yönetir.
        - Otomasyon hesabını özelleştirebilirsiniz.

    :::image type="content" source="./media/tutorial-disaster-recovery/settings-summary.png" alt-text="Hedef ve çoğaltma ayarlarının özetini gösteren sayfa.":::


6. **Gözden geçir + çoğaltmayı Başlat**' ı seçin.

7. **Çoğaltmayı Başlat**' ı seçin. Dağıtım başlar ve Site Recovery hedef kaynak oluşturmaya başlar. Bildirimlerde çoğaltma ilerlemesini izleyebilirsiniz.

    :::image type="content" source="./media/tutorial-disaster-recovery/notifications.png" alt-text="Çoğaltma ilerlemesi için bildirim.":::

## <a name="check-vm-status"></a>VM durumunu denetle

Çoğaltma işi bittikten sonra, sanal makine çoğaltma durumunu kontrol edebilirsiniz.

1. VM özellikleri sayfasını açın.
2. **İşlemler** menüsünden **Olağanüstü durum kurtarma** seçeneğini belirleyin.
3. Kasa, çoğaltma ilkesi ve hedef ayarları hakkında Varsayılanları gözden geçirmek için **temel** bileşenler bölümünü genişletin.
4. **Sağlık ve durum**' da, VM için çoğaltma durumu, aracı sürümü, yük devretme hazırlığı ve en son kurtarma noktaları hakkında bilgi alın. 

    :::image type="content" source="./media/tutorial-disaster-recovery/essentials.png" alt-text="VM olağanüstü durum kurtarma için Essentials görünümü.":::

5. **Altyapı görünümü**' nde kaynak ve hedef VM 'ler, yönetilen diskler ve önbellek depolama hesabı için görsel bir genel bakış alın.

    :::image type="content" source="./media/tutorial-disaster-recovery/infrastructure.png" alt-text="VM olağanüstü durum kurtarma için altyapı görsel haritası.":::


## <a name="run-a-drill"></a>Detaya gitme Çalıştır

Olağanüstü durum kurtarma 'nın beklendiği gibi çalıştığından emin olmak için bir detaya gitme çalıştırın. Yük devretme testi çalıştırdığınızda, devam eden çoğaltma üzerinde veya üretim ortamınızda hiçbir etkisi olmadan sanal makinenin bir kopyasını oluşturur. 

1. VM olağanüstü durum kurtarma sayfasında **Yük devretmeyi sına**' yı seçin.
2. **Yük devretme testi** bölümünde, kurtarma noktası Için varsayılan **en son IŞLENEN (düşük RPO)** ayarını bırakın.

   Bu seçenek en düşük kurtarma noktası hedefini (RPO) ve genellikle hedef VM 'nin en hızlı şekilde dönmesini sağlar. İlk olarak, Site Recovery hizmetine gönderilen tüm verileri, yük devretmadan önce her VM için bir kurtarma noktası oluşturmak üzere işler. Bu kurtarma noktası, yük devretme tetiklendiğinde Site Recovery tüm verilerin çoğaltılmasını sağlar.

3. Yük devretmeden sonra VM 'nin bulunduğu sanal ağı seçin. 

     :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-settings.png" alt-text="Yük devretme testi seçeneklerini ayarlamak için sayfa.":::

4. Yük devretme testi işlemi başlar. İlerleme durumunu bildirimlerde izleyebilirsiniz.

    :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-notification.png" alt-text="Yük devretme bildirimlerini test edin."::: 
    
   Yük devretme testi tamamlandıktan sonra, VM **Essentials** sayfasında *Temizleme testi yük devretme bekleme* durumunda olur. 



## <a name="clean-up-resources"></a>Kaynakları temizleme

Ayrıntıdan sonra Site Recovery, VM otomatik olarak temizlenir.

1. Otomatik temizlemeyi başlatmak için **Test yük devretmesini temizle**' yi seçin.

    :::image type="content" source="./media/tutorial-disaster-recovery/start-cleanup.png" alt-text="Essentials sayfasında temizlemeyi başlatın."::: 

2. **Yük devretme sınamasını Temizleme** bölümünde, yük devretme için kaydetmek istediğiniz notları yazın ve ardından test Tamam ' ı seçin **. Test yük devretmesi sanal makinesini silin**. Ardından **Tamam**’ı seçin.

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test.png" alt-text="Not kaydetme ve test VM 'yi silme sayfası."::: 

7. Silme işlemi başlar. Bildirimlerin ilerlemesini izleyebilirsiniz.

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test-notification.png" alt-text="İzlenecek bildirimler test sanal makinesini siler."::: 

### <a name="stop-replicating-the-vm"></a>VM 'yi çoğaltmayı durdur

Olağanüstü durum kurtarma detaya gitmeyi tamamladıktan sonra, tam yük devretmeyi denemeye devam etmeyi öneririz. Tam yük devretme yapmak istemiyorsanız, çoğaltmayı devre dışı bırakabilirsiniz. Bu, şunları yapar:

- VM 'yi çoğaltılan makinelerin Site Recovery listesinden kaldırır.
- VM için Site Recovery faturalandırmayı durduruyor.
- Kaynak çoğaltma ayarlarını otomatik olarak temizler.

Şu adımlara göre çoğaltmayı durdurun:

1. VM olağanüstü durum kurtarma sayfasında **çoğaltmayı devre dışı bırak**' ı seçin.
2. **Çoğaltmayı devre dışı bırak**' da, çoğaltmayı devre dışı bırakmak istediğiniz nedenleri seçin. Ardından **Tamam**’ı seçin.

    :::image type="content" source="./media/tutorial-disaster-recovery/disable-replication.png" alt-text="Çoğaltmayı devre dışı bırakma ve bir neden sağlama sayfası."::: 


Çoğaltma sırasında VM 'de yüklü Site Recovery uzantısı otomatik olarak kaldırılmaz. VM için çoğaltmayı devre dışı bırakır ve daha sonra tekrar çoğaltmak istemiyorsanız, Site Recovery uzantısını aşağıdaki şekilde el ile kaldırabilirsiniz: 

1. VM > **ayarları**  >  **uzantılarına** gidin.
2. **Uzantılar** sayfasında, Linux için *Microsoft. Azure. recoveryservices* girişini seçin.
3. Uzantının Özellikler sayfasında **Kaldır**' ı seçin.

   :::image type="content" source="./media/tutorial-disaster-recovery/uninstall-extension.png" alt-text="Site Recovery VM uzantısını kaldırmak için sayfa.":::



## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir Azure VM için olağanüstü durum kurtarma yapılandırmış ve bir olağanüstü durum kurtarma ayrıntısı çalıştırdınız. Şimdi VM için tam yük devretme gerçekleştirebilirsiniz.

> [!div class="nextstepaction"]
> [VM'nin yükünü başka bir bölgeye devretme](../../site-recovery/azure-to-azure-tutorial-dr-drill.md)
