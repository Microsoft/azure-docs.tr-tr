---
title: İzleyici Azure Site Recovery | Microsoft Docs
description: Portalı kullanarak Azure Site Recovery çoğaltma sorunlarını ve işlemlerini izleme ve sorun giderme
author: raynew
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: raynew
ms.openlocfilehash: aa9d776df50306ab1705426c923413b5a5d545a5
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717361"
---
# <a name="monitor-site-recovery"></a>İzleyici Site Recovery

Bu makalede, Site Recovery yerleşik izlemeyi kullanarak Azure [Site Recovery](site-recovery-overview.md)izleme hakkında bilgi edinin.  Şunları izleyebilirsiniz:

- Site Recovery tarafından çoğaltılan makinelerin sistem durumu ve durumu
- Makinelerin yük devretme durumunu test edin.
- Yapılandırma ve çoğaltmayı etkileyen sorunlar ve hatalar.
- Şirket içi sunucular gibi altyapı bileşenleri.


## <a name="before-you-start"></a>Başlamadan önce

Başlamadan önce [yaygın izleme sorularını](monitoring-common-questions.md) gözden geçirmek isteyebilirsiniz.

## <a name="monitor-in-the-dashboard"></a>Panoda izleme

1. Kasada **Genel Bakış ' a**tıklayın. Kurtarma Hizmetleri panosu, kasadaki tüm izleme bilgilerini tek bir konumda birleştirir. Hem Site Recovery hem de Azure Backup hizmeti için sayfalar vardır ve bunlar arasında geçiş yapabilirsiniz.

    ![Site Recovery panosu](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2. Panodan, farklı alanlarda ayrıntıya gidin. 

    ![Site Recovery panosu](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. **Çoğaltılan öğelerde**, kasadaki tüm sunucuları görmek Için **Tümünü görüntüle** ' ye tıklayın.
4. Ayrıntıya gitmek için her bölümün durum ayrıntılarına tıklayın.
5. **Altyapı görünümü**' nde, çoğaltmakta olduğunuz makine türüne göre izleme bilgilerini sıralayın.

## <a name="monitor-replicated-items"></a>Çoğaltılan öğeleri izleme

**Çoğaltılan öğelerde**, kasadaki çoğaltmanın etkin olduğu tüm makinelerin sistem durumunu izleyin.

**State** | **Ayrıntılar**
--- | ---
Sorunsuz | Çoğaltma normal ilerliyor. Herhangi bir hata veya uyarı belirtiyle algılanamamış.
Uyarı | Çoğaltmayı etkileyebilecek bir veya daha fazla uyarı belirtiyle karşılaşmış.
Kritik | Bir veya daha fazla kritik çoğaltma hatası belirtiyle algılandı.<br/><br/> Bu hata belirtileri genellikle çoğaltmanın takılmasına veya veri değişim oranı kadar hızlı ilerlemiyor.
Geçerli değil | Şu anda çoğaltılmasının beklenmediği sunucular. Bu, yük devredilen makineleri içerebilir.

## <a name="monitor-test-failovers"></a>Test yük devretmeleri izleme

**Yük devretme testi başarısı**bölümünde, kasadaki makinelerin yük devretme durumunu izleyin.

- Çoğaltılan makinelerde, her altı ayda bir yük devretme testi çalıştırmanızı öneririz. Üretim ortamınızı kesintiye uğratmadan yük devretmenin beklenen şekilde çalıştığını denetetmenin bir yoludur. 
- Yük devretme testi, yalnızca yük devretme ve yük devretme sonrası Temizleme başarıyla tamamlandıktan sonra başarılı olarak kabul edilir.

**State** | **Ayrıntılar**
--- | ---
Sınama önerilir | Koruma etkinleştirildikten sonra yük devretme testi olmayan makineler.
Başarıyla gerçekleştirildi | Ya da daha fazla başarılı test yük devretme makineleri.
Geçerli değil | Yük devretme testi için şu anda uygun olmayan makineler. Örneğin, yük devredilen makineler için ilk çoğaltma/test yük devretmesi/yük devretme devam ediyor.

## <a name="monitor-configuration-issues"></a>Yapılandırma sorunlarını izleme

**Yapılandırma sorunları**' nda, başarıyla yük devretme yeteneğinizi etkileyebilecek tüm sorunları izleyin.

- Yapılandırma sorunları (yazılım güncelleştirme kullanılabilirliği hariç), varsayılan olarak her 12 saatte bir çalışan düzenli Doğrulayıcı işlemi tarafından algılanır. **Yapılandırma sorunları** bölüm başlığının yanındaki Yenile simgesine tıklayarak Doğrulayıcı işlemini hemen çalışacak şekilde zorlayabilirsiniz.
- Daha fazla ayrıntı edinmek için bağlantılara tıklayın. Belirli makineleri etkileyen sorunlar için **hedef konfigürasyonlar** sütununda **ilgilenilmesi gerekiyor** ' a tıklayın. Ayrıntılar düzeltme önerilerini içerir.

**State** | **Ayrıntılar**
--- | ---
Eksik yapılandırma | Kurtarma ağı veya kaynak grubu gibi gerekli bir ayar eksiktir.
Eksik kaynaklar | Belirtilen bir kaynak bulunamıyor veya abonelikte kullanılamıyor. Örneğin, kaynak silinmiş veya geçirilmiş. İzlenen kaynaklar hedef kaynak grubu, hedef VNet/alt ağ, günlük/hedef depolama hesabı, hedef kullanılabilirlik kümesi, hedef IP adresi dahil edilmiştir.
Abonelik kotası |  Kullanılabilir abonelik kaynak kotası bakiyesi, kasadaki makinelerin tümünün yükünü devretmek için gereken bakiyeye göre karşılaştırılır.<br/><br/> Yeterli kaynak yoksa, yetersiz kota bakiyesi raporlanır.<br/><br/> Kotalar VM çekirdek sayısı, VM ailesi çekirdek sayısı, ağ arabirim kartı (NIC) sayısı için izlenlerdir.
Yazılım güncelleştirmeleri | Yeni yazılım güncelleştirmelerinin kullanılabilirliği ve süresi dolan yazılım sürümleri hakkında bilgiler.


## <a name="monitor-errors"></a>Hataları izle

**Hata Özeti**' nde, kasadaki sunucuların çoğaltılmasını etkileyebilecek Şu anda etkin olan hata belirtilerini izleyin ve etkilenen makinelerin sayısını izleyin.

- Şirket içi altyapı bileşenlerini etkileyen hatalar, bölümünün başlangıcıdır. Örneğin, şirket içi yapılandırma sunucusu veya Hyper-V konağı Azure Site Recovery sağlayıcısından bir sinyal alınmıyor.
- Ardından, çoğaltılan sunucuları etkileyen çoğaltma hatası belirtileri gösterilir.
- Tablo girdileri, hata önem derecesine göre azalan sırada ve etkilenen makinelerin sayım sırası azaltılarak sıralanır.
- Etkilenen sunucu sayısı, tek bir temel sorunun birden çok makineyi etkileyebileceğini anlamak için kullanışlı bir yoldur. Örneğin, bir ağ hatası Azure 'a çoğaltılan tüm makineleri etkileyebilir. 
- Tek bir sunucuda birden çok çoğaltma hatası meydana gelebilir. Bu durumda, her hata belirtisi, etkilenen sunucuları listesindeki sunucuyu sayar. Sorun giderildikten sonra, çoğaltma parametreleri geliştirir ve hata makineden silinir.

## <a name="monitor-the-infrastructure"></a>Altyapıyı izleyin.

**Altyapı görünümü**' nde, çoğaltmada yer alan altyapı bileşenlerini ve sunucular ile Azure hizmetleri arasındaki bağlantı durumunu izleyin.

- Yeşil bir çizgi bağlantının sağlıklı olduğunu gösterir.
- Çakışan hata simgesiyle kırmızı bir çizgi, bağlantıyı etkileyen bir veya daha fazla hata belirtiinin varlığını gösterir.
-  Hatayı ve etkilenen varlıkların sayısını göstermek için fare işaretçisini hata simgesinin üzerine getirin. Etkilenen varlıkların filtrelenmiş bir listesi için simgeye tıklayın.

    ![Site Recovery altyapı görünümü (kasa)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

### <a name="tips-for-monitoring-the-infrastructure"></a>Altyapıyı izlemeye yönelik ipuçları

- Şirket içi altyapı bileşenlerinin (yapılandırma sunucusu, işlem sunucuları, VMM sunucuları, Hyper-V konakları, VMware makineleri) Site Recovery sağlayıcının ve/veya aracılarının en son sürümlerini çalıştırdığından emin olun.
- Altyapı görünümündeki tüm özellikleri kullanmak için bu bileşenler için [güncelleştirme paketi 22](https://support.microsoft.com/help/4072852) ' yi çalıştırıyor olmanız gerekir.
- Altyapı görünümünü kullanmak için ortamınızda uygun çoğaltma senaryosunu seçin. Daha fazla ayrıntı için görünümde ayrıntıya gidebilirsiniz. Aşağıdaki tabloda hangi senaryoların temsil edildiği gösterilmektedir.

    **Senaryo** | **State**  | **Görünüm kullanılabilir mi?**
    --- |--- | ---
    **Şirket içi siteler arasında çoğaltma** | Tüm durumlar | Hayır 
    **Azure bölgeleri arasında Azure VM çoğaltma**  | Çoğaltma etkin/ilk çoğaltma devam ediyor | Evet
    **Azure bölgeleri arasında Azure VM çoğaltma** | Yük devredildi/başarısız oldu | Hayır   
    **Azure 'a VMware çoğaltması** | Çoğaltma etkin/ilk çoğaltma devam ediyor | Evet     
    **Azure 'a VMware çoğaltması** | Yük devredildi/başarısız oldu | Hayır      
    **Azure 'da Hyper-V çoğaltma** | Yük devredildi/başarısız oldu | Hayır

- Tek bir çoğaltma makinesine ait altyapı görünümünü görmek için kasa menüsünde **çoğaltılan öğeler**' e tıklayın ve bir sunucu seçin.  




## <a name="monitor-recovery-plans"></a>Kurtarma planlarını izleme

**Kurtarma planları**' nda, plan sayısını izleyin, yeni planlar oluşturun ve var olanları değiştirin.  

## <a name="monitor-jobs"></a>İşleri izleme

**İşler**' de Site Recovery işlemlerinin durumunu izleyin.

- Azure Site Recovery çoğu işlem zaman uyumsuz olarak yürütülemekte ve işlemin ilerlemesini izlemek için kullanılan bir izleme işi. 
- İş nesnesi, işlemin durumunu ve ilerlemesini izlemek için ihtiyacınız olan tüm bilgileri içerir. 

İşleri şu şekilde izleyin:

1. Pano > **işleri** bölümünde, son 24 saat içinde tamamlanmış, sürmekte olan veya giriş bekleyen işlerin özetini görebilirsiniz. İlgili işler hakkında daha fazla bilgi edinmek için herhangi bir duruma tıklayabilirsiniz.
2. Son 24 saat içindeki tüm işleri görmek için **Tümünü görüntüle** ' ye tıklayın.

    > [!NOTE]
    > İş bilgilerine Ayrıca, **Site Recovery işleri**> kasa menüsünden erişebilirsiniz. 

2. **Site Recovery işleri** listesinde, işlerin bir listesi görüntülenir. Üstteki menüde, belirli bir iş için hata ayrıntılarını alabilir, belirli ölçütlere göre işler listesini filtreleyebilir ve seçilen iş ayrıntılarını Excel 'e aktarabilirsiniz.
3. Bir işe tıklayarak gidebilirsiniz. 

## <a name="monitor-virtual-machines"></a>Sanal makineleri izleme

**Çoğaltılan öğeler**' de, çoğaltılan makinelerin bir listesini alın. 
    ![Çoğaltılan öğe listesi görünümünü Site Recovery](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. Bilgileri görüntüleyebilir ve filtreleyebilirsiniz. Üstteki Eylem menüsünde, yük devretme testi çalıştırma veya belirli hataları görüntüleme dahil olmak üzere belirli bir makine için eylemler gerçekleştirebilirsiniz.
3. Ek sütunları göstermek için **sütunlar** ' a tıklayın, örneğin RPO, hedef yapılandırma sorunları ve çoğaltma hatalarını göstermek için.
4. Çoğaltma durumu veya belirli bir çoğaltma ilkesi gibi belirli parametrelere göre bilgileri görüntülemek için **Filtrele** ' ye tıklayın.
5. Test yük devretmesi veya onunla ilişkili belirli hata ayrıntılarını görüntülemek için bir makineye sağ tıklayın.
6. Daha ayrıntılı bilgi edinmek için bir makineye tıklayın. Ayrıntılar şunları içerir:
   - **Çoğaltma bilgileri**: Makinenin geçerli durumu ve durumu.
   - **RPO** (kurtarma noktası hedefi): Sanal makine için geçerli RPO ve RPO 'nun en son hesaplanacağı zaman.
   - **Kurtarma noktaları**: Makinenin en son kullanılabilir kurtarma noktaları.
   - **Yük devretme hazırlığı**: Makine için bir yük devretme testi çalıştırma, makinede çalışan aracı sürümü (Mobility hizmetini çalıştıran makineler için) ve herhangi bir yapılandırma sorunu olduğunu gösterir.
   - **Hatalar**: Şu anda makinede gözlemlendiği çoğaltma hatası belirtileri ve olası nedenler/eylemler listesi.
   - **Olaylar**: Makineyi etkileyen son olayların kronolojik bir listesi. Hata ayrıntıları şu anda observable hata belirtilerini gösterir. olaylar, makineyi etkileyen sorunların geçmiş bir kaydıdır.
   - **Altyapı görünümü**: Makineler Azure 'a çoğaltılırken senaryo için altyapının durumunu gösterir.

     ![Site Recovery çoğaltılan öğe ayrıntıları/genel bakış](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

## <a name="subscribe-to-email-notifications"></a>E-posta bildirimlerine abone olma

Bu kritik olaylar için e-posta bildirimleri almak üzere abone olabilirsiniz:
 
- Çoğaltılan makine için kritik durum.
- Şirket içi altyapı bileşenleri ve Site Recovery hizmeti arasında bağlantı yok. Bir kasada kayıtlı Site Recovery ve şirket içi sunucular arasındaki bağlantı, sinyal mekanizması kullanılarak algılanır.
- Yük devretme sorunları.

Şu şekilde abone olun:

Kasa > **izleme** bölümünde **Site Recovery olaylar**' a tıklayın.
1. **E-posta bildirimleri**öğesine tıklayın.
1. **E-posta bildiriminde**bildirimleri açın ve kime gönderileceğini belirtin. Tüm abonelik yöneticilerine gönderilen bildirimler ve isteğe bağlı olarak belirli e-posta adresleri gönderebilirsiniz.

    ![E-posta bildirimleri](./media/site-recovery-monitor-and-troubleshoot/email.png)

## <a name="next-steps"></a>Sonraki adımlar

Azure Izleyici ile Site Recovery izleme [hakkında bilgi edinin](monitor-log-analytics.md) .
