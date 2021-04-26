---
title: MABS ile Hyper-V sanal makinelerini yedekleme
description: Bu makale, Microsoft Azure Backup sunucusu (MABS) kullanarak sanal makinelerin yedeklenmesi ve kurtarılması için prosedürleri içerir.
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: b4de791269161b477fc07d6539feaa975fdd72ad
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740007"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Azure Backup Sunucusu ile Hyper-V sanal makinelerini yedekleme

Bu makalede, Hyper-V sanal makinelerini Microsoft Azure Backup Server (MABS) kullanılarak nasıl yedekleyeceğiniz açıklanmaktadır.

## <a name="supported-scenarios"></a>Desteklenen senaryolar

MABS, Hyper-V ana bilgisayar sunucuları üzerinde çalışan sanal makineleri aşağıdaki senaryolarda yedekleyebilir:

- **Yerel veya doğrudan depolamaya sahip sanal makineler** - Yerel veya doğrudan bağlı depolama alanına sahip Hyper-V konak tek başına sunucularda barındırılan sanal makineleri yedekleyin. Örneğin: bir sabit sürücü, depolama alanı ağı (SAN) cihazı veya ağ bağlantılı bir depolama (NAS) cihazı. MABS koruma aracısının tüm konaklarda yüklü olması gerekir.

- **CSV deposu ile kümede bulunan sanal makineler** - Küme Paylaşılan Birimi (CSV) depolama alanı ile bir Hyper-V kümesi üzerinde barındırılan sanal makineleri yedekleyin. MABS koruma Aracısı her küme düğümüne yüklenir.

## <a name="host-versus-guest-backup"></a>Konuk yedekten karşı konak

MABS 'Ler, Hyper-V VM 'lerinin ana bilgisayar veya Konuk düzeyinde yedeklemesini gerçekleştirebilir. Konak düzeyinde, MABS koruma Aracısı Hyper-V ana bilgisayar sunucusuna veya kümeye yüklenir ve bu konakta çalışan tüm VM 'Leri ve veri dosyalarını korur.   Konuk düzeyinde, aracı her bir sanal makineye yüklenir ve o makinede bulunan iş yükünü korur.

Her iki yöntemin artıları ve eksileri vardır:

- Konak düzeyi yedeklemeler, Konuk makinelerde çalışan işletim sistemi türünden bağımsız olarak çalıştıkları ve her VM 'de MABS koruma aracısının yüklenmesine gerek kalmadığından esnektir. Konak düzeyinde yedekleme dağıtırsanız, bir sanal makinenin tamamını veya dosya ve klasörleri (öğe düzeyinde kurtarma) kurtarabilirsiniz.

- Bir sanal makine üzerinde çalışan belirli iş yüklerini korumak istiyorsanız Konuk düzeyinde yedekleme kullanışlıdır. Konak düzeyinde, tüm VM veya belirli dosyaları kurtarabilirsiniz, ancak belirli bir uygulama bağlamında kurtarma sağlamaz. Örneğin, belirli SharePoint öğelerini yedeklenmiş bir VM 'den kurtarmak için bu VM 'nin Konuk düzeyinde yedeklemesini yapmanız gerekir. Geçiş disklerinde depolanan verileri korumak istiyorsanız Konuk düzeyinde yedekleme kullanın. Geçiş, sanal makinenin depolama cihazına doğrudan erişmesini sağlar ve sanal birim verilerini bir VHD dosyasında depolamaz.

## <a name="how-the-backup-process-works"></a>Yedekleme işlemi nasıl işler?

MABS, VSS ile aşağıdaki şekilde yedekleme gerçekleştirir. Bu açıklamadaki adımlar, anlamayı kolaylaştırmak için numaralanmıştır.

1. MABS blok tabanlı eşitleme altyapısı, korunan sanal makinenin bir başlangıç kopyasını oluşturur ve sanal makine kopyasının tam ve tutarlı olmasını sağlar.

2. İlk kopya yapıldıktan ve doğrulandıktan sonra, MABS yedeklemeleri yakalamak için Hyper-V VSS yazıcısını kullanır. VSS yazıcı, MABS sunucusuyla eşitlenen, veri bakımından tutarlı bir disk blokları kümesi sağlar. Bu yaklaşım, MABS sunucusuyla "tam yedekleme" avantajı sağlar ve ağ üzerinden aktarılması gereken yedekleme verilerini en aza indirir.

3. Hyper-V çalıştıran bir sunucudaki MABS koruma Aracısı, korunan bir sanal makinenin aynı zamanda VSS 'yi destekleyip desteklemediğini belirlemede var olan Hyper-V API 'Lerini kullanır.

   - Bir sanal makine, çevrimiçi yedekleme gereksinimlerini karşılıyorsa ve bu sanal makineye Hyper-V tümleştirme hizmetleri yüklüyse Hyper-V VSS yazıcı, VSS isteğini sanal makinedeki tüm VSS bilinçli işlemlere yinelemeli olarak iletir. Bu işlem, sanal makinede MABS koruma aracısının yüklü olması gerekmeden oluşur. Yinelemeli VSS isteği, Hyper-V VSS yazıcının, bir VSS anlık görüntüsünün veri kaybı yaşanmadan yakalanabilmesi için diske yazma işlemlerini eşitlemesini sağlar.

     Hyper-V tümleştirme hizmetleri bileşeni, uygulama verilerinin tutarlı bir durumda olduğundan emin olmak için Hyper-V VSS yazıcıyı sanal makinelerdeki Birim Gölge Kopyası Hizmetlerinde (VSS) başlatır.

   - Sanal makine çevrimiçi yedekleme gereksinimleriyle uyumlu değilse, MABS, veri dosyalarını yakalamadan önce sanal makineyi duraklatmak için otomatik olarak Hyper-V API 'Lerini kullanır.

4. Sanal makinenin ilk temel kopyası MABS sunucusuyla eşitlendikten sonra sanal makine kaynaklarında yapılan tüm değişiklikler yeni bir kurtarma noktasında yakalanır. Kurtarma noktası, sanal makinenin belirli bir zamandaki tutarlı durumunu temsil eder. Kurtarma noktası yakalamaları günde en az bir kez gerçekleşebilir. Yeni bir kurtarma noktası oluşturulduğunda, MABS, en son kurtarma noktası oluşturulduktan sonra Hyper-V çalıştıran sunucuda hangi blokların değiştirilmediğini anlamak için Hyper-V VSS yazıcı ile birlikte blok düzeyinde çoğaltma kullanır. Bu veri blokları daha sonra MABS sunucusuna aktarılır ve korunan verilerin çoğaltmasına uygulanır.

5. MABS sunucusu, birden fazla gölge kopyanın kullanılabilir olması için kurtarma verilerini barındıran birimlerde VSS kullanır. Bu gölge kopyaların her biri ayrı bir kurtarma sağlar. VSS kurtarma noktaları, MABS sunucusunda depolanır. Hyper-V çalıştıran sunucuda yapılan geçici kopya yalnızca MABS eşitlemesinin süresi boyunca depolanır.

>[!NOTE]
>
>Windows Server 2016 ' den başlayarak, Hyper-V sanal sabit disklerinde dayanıklı değişiklik izleme (RCT) olarak bilinen yerleşik değişiklik izleme vardır. MABS, sanal makine kilitlenmeleri gibi senaryolarda zaman alan tutarlılık denetimleri gereksinimini azaltan RCT (Hyper-V ' d a yerel değişiklik izleme) kullanır. RCT, VSS anlık görüntü tabanlı yedeklemelerin sağladığı değişiklik izlemeden daha dayanıklıdır. MABS v3, herhangi bir tutarlılık denetimi sırasında yalnızca değiştirilen verileri aktararak ağ ve depolama tüketimini iyileştirir.

## <a name="backup-prerequisites"></a>Yedekleme önkoşulları

Bunlar, MABS ile Hyper-V sanal makinelerini yedeklemeye yönelik önkoşullardır:

|Önkoşul|Ayrıntılar|
|------------|-------|
|MABS önkoşulları|-Sanal makineler için öğe düzeyinde kurtarma gerçekleştirmek istiyorsanız (dosyaları, klasörleri, birimleri kurtarma), daha sonra Hyper-V rolünü MABS sunucusuna yüklemeniz gerekir.  Öğe düzeyi değil yalnızca sanal makineyi kurtarmak istiyorsanız rol gerekli değildir.<br />-Her bir MABS sunucusunda 800 GB 'a kadar sanal 100 makine koruyabilir ve daha büyük kümeleri destekleyen birden çok MABS sunucusuna izin verebilirsiniz.<br />-MABS, sanal makine yedekleme performansını geliştirmek için sayfa dosyasını artımlı yedeklerden dışlar.<br />-MABS, bir Hyper-V sunucusu veya kümesini MABS sunucusuyla aynı etki alanında veya alt veya güvenilen bir etki alanında yedekleyebilir. Hyper-V ' y i bir çalışma grubunda veya güvenilmeyen bir etki alanında yedeklemek istiyorsanız, kimlik doğrulamasını ayarlamanız gerekir. Tek bir Hyper-V sunucusu için NTLM veya sertifika kimlik doğrulaması kullanabilirsiniz. Bir küme için yalnızca sertifika kimlik doğrulaması kullanabilirsiniz.<br />-   Doğrudan geçiş disklerinde sanal makine verilerini yedeklemek için konak düzeyinde yedekleme kullanımı desteklenmez. Bu senaryoda, konakta görünmeyen diğer verileri yedeklemek üzere VHD dosyalarını ve konuk düzeyi yedeklemeyi yedeklemek için ana bilgisayar düzeyinde yedekleme kullanmanızı öneririz.<br />   -Yinelenenleri kaldırılmış birimlerde depolanan VM 'Leri yedekleyebilirsiniz.|
|Hyper-V VM önkoşulları|-Sanal makinede çalışan Tümleştirme Bileşenleri sürümünün Hyper-V konağının sürümüyle aynı olması gerekir. <br />-   Her bir sanal makine yedeklemesinde, Hyper-V'ye yedekleme sırasında farklı diskler (AVHD'nin) için yeterince alan bırakmak üzere sanal sabit disk dosyalarını barındıran birimde boş alan olması gerekir. Alan en az, hesaplanan **İlk disk boyutu\*Karmaşıklık oranı\*Yedekleme** penceresi zamanına eşit olmalıdır. Bir kümede birden fazla yedekleme çalıştırıyorsanız AVHD'leri bu hesaplamayı kullanan her bir sanal makine için uygun hale getirmek üzere yeterli depolama kapasitesine sahip olmanız gerekir.<br />-Windows Server 2012 R2 çalıştıran Hyper-V ana bilgisayar sunucularında bulunan sanal makineleri yedeklemek için, sanal makinenin herhangi bir şeye bağlı olmasa bile belirtilen bir SCSI denetleyicisi olmalıdır. (Windows Server 2012 R2 yedekleme 'de, Hyper-V konağı VM 'de yeni bir VHD takar ve daha sonra onu takar. Bunu yalnızca SCSI denetleyicisi destekleyebilir ve bu nedenle sanal makinenin çevrimiçi yedeklemesi için gereklidir.  Bu ayar olmadan, sanal makineyi yedeklemeye çalıştığınızda 10103 olay KIMLIĞI verilecek.)|
|Linux önkoşulları|-MABS kullanarak Linux sanal makinelerini yedekleyebilirsiniz. Yalnızca dosya tutarlı anlık görüntüler desteklenir.|
|CSV depolama ile VM’leri yedekleyin|-   CSV depolama için Hyper-V sunucusuna Birim Gölge Kopya Hizmetleri (VSS) donanım sağlayıcısını yükleyin. VSS donanım sağlayıcısı için depolama alanı ağı (SAN) satıcınıza başvurun.<br />-Tek bir düğüm bir CSV kümesinde beklenmedik bir şekilde kapatılırsa, MABS bu düğümde çalışan sanal makinelere karşı bir tutarlılık denetimi gerçekleştirir.<br />-   CSV kümesinde BitLocker Sürücü Şifrelemesi etkinleştirilmiş bir Hyper-V sunucusunu yeniden başlatmanız gerekirse, Hyper-V sanal makinelerine yönelik bir tutarlılık denetimi gerçekleştirmeniz gerekir.|
|SMB deposu ile VM’leri yedekleyin|-Sanal makine korumasını etkinleştirmek için Hyper-V çalıştıran sunucuda otomatik bağlama özelliğini açın.<br />   -   TCP Kanalı Boşaltma'yı devre dışı bırakın.<br />-   Tüm Hyper-V machine$ hesaplarının ilgili uzak SMB dosya paylaşımlarında tam izinleri olduğundan emin olun.<br />-Diğer konuma kurtarma sırasında tüm sanal makine bileşenlerinin dosya yolunun 260 karakterden kısa olduğundan emin olun. Değilse, kurtarma başarılı olabilir ancak Hyper-V sanal makineyi bağlayamayacak.<br />-Aşağıdaki senaryolar desteklenmez:<br />     Bazı sanal makine bileşenlerinin yerel birimlerde olduğu ve bazı bileşenlerin uzak birimlerde olduğu dağıtımlar; depolama konumu dosya sunucusu için IPv4 veya IPv6 adresi ve bir sanal makinenin uzak SMB paylaşımlarını kullanan bir bilgisayara kurtarılması.<br />-Dosya sunucusu VSS Aracısı hizmetini her SMB sunucusunda etkinleştirmeniz gerekir. **rol ve Özellik Ekle**' de  >  **sunucu rolleri**  >  **dosya ve depolama hizmetleri** dosya  >  **Hizmetleri** dosya  >  **hizmeti** dosya  >  **sunucusu VSS Aracısı hizmeti**' ni seçin.|

## <a name="back-up-virtual-machines"></a>Sanal makineleri yedekleme

1. [Mabs sunucunuzu](backup-azure-microsoft-azure-backup.md) ve [depolama](backup-mabs-add-storage.md)alanınızı ayarlayın. Depolama alanınızı ayarlarken, bu depolama kapasitesi kurallarını kullanın.
   - Ortalama sanal makine boyutu: 100 GB
   - MABS sunucu başına sanal makine sayısı-800
   - 800 VM için toplam boyut: 80 TB
   - Yedekleme deposu için gereken alan: 80 TB

2. MABS koruma aracısını Hyper-V sunucusunda veya Hyper-V küme düğümlerinde ayarlayın.

3. Mabs yönetici **konsolunda koruma**  >  **koruma grubu oluştur** ' u seçerek **yeni koruma grubu oluşturma** Sihirbazı ' nı açın.

4. **Grup Üyelerini Seçin** sayfasında, üzerinde bulundukları Hyper-V konak sunucularından korumak istediğiniz VM’leri seçin. Aynı koruma ilkesine sahip olacak tüm VM’leri tek bir koruma grubuna koymanızı öneririz. Alanı verimli kullanmak için birlikte bulundurmayı etkinleştirin. Birlikte bulundurma, aynı diskte veya bant deposunda bulunan farklı koruma gruplarında veri bulundurmanıza olanak tanır. Böylece birden çok veri kaynağının tek bir çoğaltması ve kurtarma noktası birimi olur.

5. **Veri Koruma Yöntemini Seçin** sayfasında, koruma grubunun adını belirtin. Verileri Azure Yedekleme hizmetini kullanarak Azure'a yedeklemek istiyorsanız **Disk kullanarak kısa vadeli koruma istiyorum** 'u seçin ve **Çevrimiçi koruma istiyorum** 'u seçin.

6. **Short-Term hedefleri**  >  **bekletme aralığını** belirtin bölümünde disk verilerini ne kadar süreyle saklamak istediğinizi belirtin. **Eşitleme sıklığı**' nda verilerin artımlı yedeklemelerinin ne sıklıkta çalıştırılacağını belirtin. Alternatif olarak, artımlı yedeklemeler için bir aralık seçmek yerine **Bir kurtarma noktasından hemen önce** seçeneğini etkinleştirebilirsiniz. Bu ayar etkinleştirildiğinde, MABS her bir zamanlanan kurtarma noktasıyla hemen önce hızlı tam yedekleme çalıştırır.

    > [!NOTE]
    >
    >Uygulama iş yüklerini koruyorsanız, uygulamanın artımlı yedeklemeleri desteklemesi koşuluyla kurtarma noktaları Eşitleme sıklığına uygun olarak oluşturulur. Değilse, MABS artımlı yedekleme yerine hızlı bir tam yedekleme çalıştırır ve hızlı yedekleme zamanlamasına uygun olarak kurtarma noktaları oluşturur.<br></br>Yedekleme işlemi VM 'lerle ilişkili kontrol noktalarını yedeketmez.

7. **Disk ayırmayı İncele** sayfasında, koruma grubu için ayrılmış depolama havuzu disk alanını gözden geçirin.

   **Toplam veri boyutu** , yedeklemek istediğiniz verilerin boyutudur ve **mabs üzerinde sağlanacak disk alanı** , mabs 'nin koruma grubu için önerdiği alandır. MABS, ayarlara bağlı olarak ideal yedekleme birimini seçer. Ancak, yedekleme birimi seçeneklerini **Disk ayırma ayrıntıları**'nda düzenleyebilirsiniz. İş yükleri için açılan menüden tercih edilen depolamayı seçin. Düzenlemeleriniz, **Kullanılabilir Disk Depolaması** bölmesindeki **Toplam Depolama** ve **Boş Depolama** değerlerini değiştirir. Yetersiz sağlanan alan, daha sonra yedeklemeler ile devam etmek için, MABS 'ların birime eklemenizi önerdiği depolama miktarıdır.

8. **Çoğaltma Oluşturma Yöntemini Seçin** sayfasında, veri koruma grubundaki ilk çoğaltmanın nasıl gerçekleştirileceğini belirtin. **Ağ üzerinden otomatik olarak çoğaltmayı** seçerseniz, yoğun olmayan bir zaman seçmeniz önerilir. Büyük miktarlarda veri veya en iyi ağ koşullarına göre, verileri çıkarılabilir medya kullanarak çevrimdışı olarak çoğaltmayı gerektiren **el ile** seçmeyi düşünün.

9. **Tutarlılık Denetimi Seçenekleri** sayfasında, tutarlılık denetimlerinin nasıl otomatikleştirilmesini istediğinizi seçin. Veri çoğaltma tutarsız hale geldiğinde veya bir zamanlamaya göre çalıştırmak için bir denetim etkinleştirebilirsiniz. Otomatik tutarlılık denetimini yapılandırmak istemiyorsanız, herhangi bir zamanda koruma grubuna sağ tıklayarak ve **Tutarlılık Denetimi Gerçekleştir**'i seçerek el ile denetim gerçekleştirebilirsiniz.

    Veri koruma grubu oluşturulduktan sonra ilk veri çoğaltma işlemi seçtiğiniz yönteme uygun olarak gerçekleşir. İlk çoğaltmadan sonra her yedekleme koruma grubu ayarlarına uygun olarak gerçekleşir. Yedeklenen verileri kurtarmanız gerekiyorsa, aşağıdakileri göz önünde bulabilirsiniz:

## <a name="back-up-replica-virtual-machines"></a>Çoğaltma sanal makineleri yedekleme

MABS Windows Server 2012 R2 veya üzeri bir sürümde çalışıyorsa, çoğaltma sanal makinelerini yedekleyebilirsiniz. Bu, çeşitli nedenlerle yararlıdır:

**Çalıştırma iş yükü üzerindeki yedeklemelerin etkisini azaltır** - Bir sanal makinenin yedeğini almak bir anlık görüntü oluşturulduğu için bazı ek yükler getirir. Yedekleme işlemini ikincil bir uzak siteye devrederek, çalışan iş yükü artık yedekleme işleminden etkilenmez. Bu, yalnızca yedekleme kopyasının uzak bir siteye depolandığı dağıtımlar için geçerlidir. Örneğin, geri yüklemelerin hızlı olmasını sağlamak için günlük olarak yedek alabilir ve yerel olarak veri depolayabilirsiniz ancak uzun vadeli bekletme için uzakta depolanan çoğaltma sanal makinelerinden aylık veya üç aylık olarak yedek alın.

**Bant genişliği tasarrufu sağlar** - Tipik bir uzak şubeye/merkeze dağıtımda siteler arasında yedekleme verilerini aktarmak için uygun miktarda bant genişliğiniz olması gerekir. Veri yedekleme stratejinize ek olarak bir çoğaltma ve yük devretme stratejisi oluşturursanız, ağ üzerinden gönderilen fazladan veri miktarını düşürebilirsiniz. Birincil yerine çoğaltma sanal makine verilerini yedekledikten sonra, ağ üzerinden yedeklenen verileri gönderme yükünü kaydedersiniz.

**Barındırıcı yedeği sağlar** - Barındırılan bir veri merkezini bir çoğaltma site olarak kullanabilirsiniz; ikincil bir veri merkezine gerek kalmaz. Bu durumda, barındırma sağlayıcı SLA çoğaltma sanal makinelerinin tutarlı yedeklemesini gerektirir.

Bir yük devretme başlatılana kadar bir çoğaltma sanal makine kapalıdır ve VSS, bir çoğaltma sanal makine için uygulamada tutarlı olan bir yedeklemeyi garanti edemez. Bu nedenle, bir çoğaltma sanal makinesinin yedeklemesi yalnızca kilitlenme tutarlı olur. Kilitlenme tutarlılığı garanti edilemiyorsa yedekleme başarısız olur ve bu birkaç koşulda gerçekleşebilir:

- Çoğaltma sanal makinesi iyi durumda değildir ve kritik durumda.

- Çoğaltma sanal makinesi yeniden eşitleme yapıyor (Yeniden eşitleme sürüyor veya Yeniden Eşitleme Gerekiyor durumunda).

- Birincil ve ikincil site arasındaki ilk çoğaltma sürüyor veya sanal makineyi bekliyor.

- . hrl günlükleri, çoğaltma sanal makinesine uygulanıyor veya. hrl günlüklerini sanal disk üzerinde uygulamaya yönelik önceki bir eylem başarısız oldu ya da iptal edildi veya kesintiye uğradı.

- Çoğaltma sanal makinesinin geçişi veya yük devretmesi sürüyor

## <a name="recover-backed-up-virtual-machines"></a>Yedeklenen sanal makineleri kurtar

Yedeklenen bir sanal makineyi kurtardığınızda, sanal makineyi ve özgül kurtarma noktasını seçmek için Kurtarma sihirbazını kullanırsınız. Kurtarma Sihirbazı'nı açmak ve bir sanal makineyi kurtarmak için:

1. MABS yönetici konsolunda, VM 'nin adını yazın veya korunan öğelerin listesini genişletin, **tüm korumalı hyperv verilerine** gidin ve kurtarmak istediğiniz VM 'yi seçin.

2. Kurtarma **noktaları** bölmesinde, takvim üzerinde, kullanılabilir kurtarma noktalarını görmek için herhangi bir tarih seçin. Sonra, **Yol** bölmesinde, Kurtarma sihirbazında kullanmak istediğiniz kurtarma noktasını seçin.

3. **Eylemler** menüsünde, Kurtarma Sihirbazı 'nı açmak için **kurtar** ' ı seçin.

    Seçtiğiniz VM ve kurtarma noktası, **Kurtarma Seçimini İnceleyin** ekranında görünür. **İleri**’yi seçin.

4. **Kurtarma türü seçin** ekranında, verileri geri yüklemek istediğiniz yeri seçin ve ardından **İleri**' yi seçin.

    - **Özgün örneğe kurtar**: özgün örneği kurtardığınızda, özgün VHD ve tüm ilişkili kontrol noktaları silinir. MABS, VHD 'yi ve diğer yapılandırma dosyalarını Hyper-V VSS yazıcı kullanarak özgün konuma kurtarır. Kurtarma işleminin sonunda, sanal makineler yüksek oranda kullanılabilir olarak kalır.
        Kurtarma için kaynak grubunun mevcut olması gerekir. Mevcut değilse, başka bir konuma kurtarın ve ardından sanal makineyi yüksek oranda kullanılabilir hale getirin.

    - **Sanal makine olarak herhangi bir konağa kurtar**: mabs, korumalı bir Hyper-v sanal makinesinin işlemci mimarisinden bağımsız olarak farklı bir Hyper-v konağına sorunsuz bir şekilde kurtarılmasını sağlayan alternatif konum kurtarmayı (ALR) destekler. Bir küme düğümüne kurtarılan Hyper-V sanal makineleri yüksek oranda kullanılabilir olmaz. Bu seçeneği işaretlerseniz, Kurtarma Sihirbazı, hedefi ve hedef yolunu tanımlamak için size ek bir ekran sunar.
    
        >[!NOTE]
        >Özgün Konağı seçerseniz, davranış **özgün örneğe kurtarma** ile aynı olur. Özgün VHD ve tüm ilişkili denetim noktaları silinir.

    - **Ağ klasörüne kopyala**: Mabs, Hyper-V sanal makinelerinin ana bilgisayar düzeyindeki yedeğinden dosya, klasör, birim ve sanal sabit diskleri (VHD), bir ağ paylaşımında veya mabs korumalı sunucusundaki bir birime öğe düzeyinde kurtarmanıza olanak sağlayan öğe düzeyinde kurtarmayı (ILR) destekler. Öğe düzeyinde kurtarma gerçekleştirmek için MABS koruma aracısının Konuk içinde yüklü olması gerekmez. Bu seçeneği işaretlerseniz, Kurtarma Sihirbazı, hedefi ve hedef yolunu tanımlamak için size ek bir ekran sunar.

5. **Kurtarma seçeneklerini belirtin** bölümünde kurtarma seçeneklerini yapılandırın ve Ileri ' **yi** seçin:

    - Düşük bant genişliğine sahip bir sanal makineyi kurtarıyorsanız, **ağ bant genişliği kullanımını azaltmayı** etkinleştirmek için **Değiştir** ' i seçin. Azaltma seçeneğini açtıktan sonra, kullandırmak istediğiniz bant genişliği miktarını ve bu bant genişliğinin kullanılabileceği zamanı belirtebilirsiniz.
    - Ağınızı yapılandırdıysanız, **donanım anlık görüntülerini kullanarak San tabanlı kurtarmayı etkinleştir** ' i seçin.
    - Kurtarma işlemi tamamlandığında e-posta bildirimleri gönderilmesini istiyorsanız, **Kurtarma tamamlandığında bir e-posta gönder**'i seçin ve e-posta adreslerini girin.

6. Özet ekranında, tüm ayrıntıların doğru olduğundan emin olun. Ayrıntılar doğru değilse veya bir değişiklik yapmak istiyorsanız, **geri**' yi seçin. Ayarları karşıladıysanız kurtarma işlemini başlatmak için **kurtar** ' ı seçin.

7. **Kurtarma Durumu** ekranı kurtarma işi hakkında bilgi sağlar.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Backup Sunucusu’ndan veri kurtarma](./backup-azure-alternate-dpm-server.md)
