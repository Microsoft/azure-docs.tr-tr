---
title: MABS ile Hyper-V sanal makinelerini yedekleme
description: Bu makale, Microsoft Azure Backup sunucusu (MABS) kullanarak sanal makinelerin yedeklenmesi ve kurtarılması için prosedürleri içerir.
ms.topic: conceptual
ms.date: 07/18/2019
ms.openlocfilehash: 3bca1b46a867c2967dfcebe4bc8477d5f9c9447d
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173525"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Azure Backup Sunucusu ile Hyper-V sanal makinelerini yedekleme

Bu makalede, Hyper-V sanal makinelerini Microsoft Azure Backup Server (MABS) kullanılarak nasıl yedekleyeceğiniz açıklanmaktadır.

## <a name="supported-scenarios"></a>Desteklenen senaryolar

MABS, Hyper-V ana bilgisayar sunucuları üzerinde çalışan sanal makineleri aşağıdaki senaryolarda yedekleyebilir:

- **Yerel veya doğrudan depolama alanı olan sanal makineler** -yerel veya doğrudan bağlı depolama alanına sahip Hyper-V konağı tek başına sunucularda barındırılan sanal makineleri yedekleyin. Örneğin: bir sabit sürücü, depolama alanı ağı (SAN) cihazı veya ağ bağlantılı bir depolama (NAS) cihazı. MABS koruma aracısının tüm konaklarda yüklü olması gerekir.

- **CSV depolama ile bir kümedeki sanal makineler** -küme PAYLAŞıLAN BIRIMI (CSV) depolama alanı Ile bir Hyper-V kümesinde barındırılan sanal makineleri yedekleyin. MABS koruma Aracısı her küme düğümüne yüklenir.

## <a name="host-versus-guest-backup"></a>Konuk yedekten karşı konak

MABS 'Ler, Hyper-V VM 'lerinin ana bilgisayar veya Konuk düzeyinde yedeklemesini gerçekleştirebilir. Konak düzeyinde, MABS koruma Aracısı Hyper-V ana bilgisayar sunucusuna veya kümeye yüklenir ve bu konakta çalışan tüm VM 'Leri ve veri dosyalarını korur.   Konuk düzeyinde, aracı her bir sanal makineye yüklenir ve o makinede bulunan iş yükünü korur.

Her iki yöntemde de olumlu ve olumsuz yönleri vardır:

- Konak düzeyi yedeklemeler, Konuk makinelerde çalışan işletim sistemi türünden bağımsız olarak çalıştıkları ve her VM 'de MABS koruma aracısının yüklenmesine gerek kalmadığından esnektir. Konak düzeyinde yedekleme dağıtırsanız, bir sanal makinenin tamamını veya dosya ve klasörleri (öğe düzeyinde kurtarma) kurtarabilirsiniz.

- Bir sanal makine üzerinde çalışan belirli iş yüklerini korumak istiyorsanız Konuk düzeyinde yedekleme kullanışlıdır. Konak düzeyinde, tüm VM veya belirli dosyaları kurtarabilirsiniz, ancak belirli bir uygulama bağlamında kurtarma sağlamaz. Örneğin, belirli SharePoint öğelerini yedeklenmiş bir VM 'den kurtarmak için bu VM 'nin Konuk düzeyinde yedeklemesini yapmanız gerekir. Geçiş disklerinde depolanan verileri korumak istiyorsanız Konuk düzeyinde yedekleme kullanın. Geçiş, sanal makinenin depolama cihazına doğrudan erişmesini sağlar ve sanal birim verilerini bir VHD dosyasında depolamaz.

## <a name="how-the-backup-process-works"></a>Yedekleme işlemi nasıl işler?

MABS, VSS ile aşağıdaki şekilde yedekleme gerçekleştirir. Bu açıklamadaki adımlar, netlik sağlamaya yardımcı olmak için numaralandırılır.

1. MABS blok tabanlı eşitleme altyapısı, korunan sanal makinenin bir başlangıç kopyasını oluşturur ve sanal makine kopyasının tam ve tutarlı olmasını sağlar.

2. İlk kopya yapıldıktan ve doğrulandıktan sonra, MABS yedeklemeleri yakalamak için Hyper-V VSS yazıcısını kullanır. VSS yazıcı, MABS sunucusuyla eşitlenen, veri bakımından tutarlı bir disk blokları kümesi sağlar. Bu yaklaşım, MABS sunucusuyla "tam yedekleme" avantajı sağlar ve ağ üzerinden aktarılması gereken yedekleme verilerini en aza indirir.

3. Hyper-V çalıştıran bir sunucudaki MABS koruma Aracısı, korunan bir sanal makinenin aynı zamanda VSS 'yi destekleyip desteklemediğini belirlemede var olan Hyper-V API 'Lerini kullanır.

   - Bir sanal makine, çevrimiçi yedekleme gereksinimleriyle uyumludur ve Hyper-V Tümleştirme Hizmetleri bileşeni yüklüyse, Hyper-V VSS yazıcı, VSS isteğini sanal makinedeki tüm VSS kullanan işlemlere yinelemeli olarak iletir. Bu işlem, sanal makinede MABS koruma aracısının yüklü olması gerekmeden oluşur. Özyinelemeli VSS isteği Hyper-V VSS yazıcısının, veri kaybı olmadan bir VSS anlık görüntüsünün yakalanması için disk yazma işlemlerinin eşitlendiğinden emin olmasını sağlar.

     Hyper-V Tümleştirme Hizmetleri bileşeni, uygulama verilerinin tutarlı bir durumda olduğundan emin olmak için Hyper-V VSS yazıcısını sanal makinelerde birim gölge kopyası hizmetlerinde (VSS) çağırır.

   - Sanal makine çevrimiçi yedekleme gereksinimleriyle uyumlu değilse, MABS, veri dosyalarını yakalamadan önce sanal makineyi duraklatmak için otomatik olarak Hyper-V API 'Lerini kullanır.

4. Sanal makinenin ilk temel kopyası MABS sunucusuyla eşitlendikten sonra sanal makine kaynaklarında yapılan tüm değişiklikler yeni bir kurtarma noktasında yakalanır. Kurtarma noktası, sanal makinenin belirli bir zamanda tutarlı durumunu temsil eder. Kurtarma noktası yakalamaları günde en az bir kez gerçekleşebilir. Yeni bir kurtarma noktası oluşturulduğunda, MABS, en son kurtarma noktası oluşturulduktan sonra Hyper-V ' y i çalıştıran sunucuda hangi blokların değiştirilmediğini anlamak için Hyper-V VSS yazıcı ile birlikte blok düzeyinde çoğaltma kullanır. Bu veri blokları daha sonra MABS sunucusuna aktarılır ve korunan verilerin çoğaltmasına uygulanır.

5. MABS sunucusu, birden fazla gölge kopyanın kullanılabilir olması için kurtarma verilerini barındıran birimlerde VSS kullanır. Bu gölge kopyaların her biri ayrı bir kurtarma sağlar. VSS kurtarma noktaları, MABS sunucusunda depolanır. Hyper-V çalıştıran sunucuda yapılan geçici kopya yalnızca MABS eşitlemesinin süresi boyunca depolanır.

>[!NOTE]
>
>Windows Server 2016 ' den başlayarak, Hyper-V sanal sabit disklerinde dayanıklı değişiklik izleme (RCT) olarak bilinen yerleşik değişiklik izleme vardır. MABS, sanal makine kilitlenmeleri gibi senaryolarda zaman alan tutarlılık denetimleri gereksinimini azaltan RCT (Hyper-V ' d a yerel değişiklik izleme) kullanır. RCT, VSS anlık görüntü tabanlı yedeklemeler tarafından sağlanan değişiklik izlemeden daha iyi dayanıklılık sağlar. MABS v3, herhangi bir tutarlılık denetimi sırasında yalnızca değiştirilen verileri aktararak ağ ve depolama tüketimini iyileştirir.

## <a name="backup-prerequisites"></a>Yedekleme önkoşulları

Bunlar, MABS ile Hyper-V sanal makinelerini yedeklemeye yönelik önkoşullardır:

|Önkoşul|Ayrıntılar|
|------------|-------|
|MABS önkoşulları|-Sanal makineler için öğe düzeyinde kurtarma gerçekleştirmek istiyorsanız (dosyaları, klasörleri, birimleri kurtarma), daha sonra Hyper-V rolünü MABS sunucusuna yüklemeniz gerekir.  Öğe düzeyi değil yalnızca sanal makineyi kurtarmak istiyorsanız rol gerekli değildir.<br />-Her bir MABS sunucusunda 800 GB 'a kadar sanal 100 makine koruyabilir ve daha büyük kümeleri destekleyen birden çok MABS sunucusuna izin verebilirsiniz.<br />-MABS, sanal makine yedekleme performansını geliştirmek için sayfa dosyasını artımlı yedeklerden dışlar.<br />-MABS, bir Hyper-V sunucusu veya kümesini MABS sunucusuyla aynı etki alanında veya alt veya güvenilen bir etki alanında yedekleyebilir. Hyper-V ' y i bir çalışma grubunda veya güvenilmeyen bir etki alanında yedeklemek istiyorsanız, kimlik doğrulamasını ayarlamanız gerekir. Tek bir Hyper-V sunucusu için NTLM veya sertifika kimlik doğrulaması kullanabilirsiniz. Bir küme için yalnızca sertifika kimlik doğrulaması kullanabilirsiniz.<br />-Geçiş disklerinde sanal makine verilerini yedeklemek için ana bilgisayar düzeyinde yedekleme kullanılması desteklenmez. Bu senaryoda, konakta görünmeyen diğer verileri yedeklemek üzere VHD dosyalarını ve konuk düzeyi yedeklemeyi yedeklemek için ana bilgisayar düzeyinde yedekleme kullanmanızı öneririz.<br />   -Yinelenenleri kaldırılmış birimlerde depolanan VM 'Leri yedekleyebilirsiniz.|
|Hyper-V VM önkoşulları|-Sanal makinede çalışan Tümleştirme Bileşenleri sürümünün Hyper-V konağının sürümüyle aynı olması gerekir. <br />-Her sanal makine yedeklemesi için sanal sabit disk dosyalarını barındıran birimde, yedekleme sırasında fark kayıt diskleri (AVHD 'nin) için yeterli yere yetecek kadar boş alan olması gerekir. Alanın en azından hesaplama **Başlangıç disk boyutu\*değişim oranı\*yedekleme** penceresi zamanına eşit olması gerekir. Bir kümede birden fazla yedekleme çalıştırıyorsanız, bu hesaplamayı kullanan her bir sanal makine için AVHDs 'ye uyum sağlamak üzere yeterli depolama kapasitesine sahip olmanız gerekir.<br />-Windows Server 2012 R2 çalıştıran Hyper-V ana bilgisayar sunucularında bulunan sanal makineleri yedeklemek için, sanal makinenin herhangi bir şeye bağlı olmasa bile belirtilen bir SCSI denetleyicisi olmalıdır. (Windows Server 2012 R2 online yedekleme 'de, Hyper-V konağı VM 'de yeni bir VHD takar ve daha sonra onu takar. Bunu yalnızca SCSI denetleyicisi destekleyebilir ve bu nedenle sanal makinenin çevrimiçi yedeklemesi için gereklidir.  Bu ayar olmadan, sanal makineyi yedeklemeye çalıştığınızda 10103 olay KIMLIĞI verilecek.)|
|Linux önkoşulları|-MABS 2012 R2 'yi kullanarak Linux sanal makinelerini yedekleyebilirsiniz. Yalnızca dosya ile tutarlı anlık görüntüler desteklenir.|
|CSV depolama ile VM 'Leri yedekleme|-CSV depolaması için, Hyper-V sunucusuna birim gölge kopyası Hizmetleri (VSS) donanım sağlayıcısını yükler. VSS donanım sağlayıcısı için depolama alanı ağı (SAN) satıcınıza başvurun.<br />-Tek bir düğüm bir CSV kümesinde beklenmedik bir şekilde kapatılırsa, MABS bu düğümde çalışan sanal makinelere karşı bir tutarlılık denetimi gerçekleştirir.<br />-CSV kümesinde BitLocker Sürücü Şifrelemesi etkinleştirilmiş olan bir Hyper-V sunucusunu yeniden başlatmanız gerekirse, Hyper-V sanal makineleri için bir tutarlılık denetimi çalıştırmanız gerekir.|
|SMB depolama ile VM 'Leri yedekleme|-Sanal makine korumasını etkinleştirmek için Hyper-V ' d i çalıştıran sunucuda otomatik bağlama özelliğini açın.<br />   -TCP Kanalı Boşaltma devre dışı bırakın.<br />-Tüm Hyper-V Machine $ hesaplarının belirli uzak SMB dosya paylaşımlarında tam izinleri olduğundan emin olun.<br />-Diğer konuma kurtarma sırasında tüm sanal makine bileşenlerinin dosya yolunun 260 karakterden kısa olduğundan emin olun. Değilse, kurtarma başarılı olabilir ancak Hyper-V sanal makineyi bağlayamayacak.<br />-Aşağıdaki senaryolar desteklenmez:<br />     Bazı sanal makine bileşenlerinin yerel birimlerde olduğu ve bazı bileşenlerin uzak birimlerde olduğu dağıtımlar; depolama konumu dosya sunucusu için IPv4 veya IPv6 adresi ve bir sanal makinenin uzak SMB paylaşımlarını kullanan bir bilgisayara kurtarılması.<br />-Dosya sunucusu VSS Aracısı hizmetini her SMB sunucusunda etkinleştirmeniz gerekir. **rol ve Özellik Ekle** ' ye tıklayın > **sunucu rolleri** > **dosya ve depolama hizmetleri** > **Dosya HIZMETLERI** > **Dosya hizmeti** > dosya **sunucusu VSS Aracısı hizmeti**' ni seçin.|

## <a name="back-up-virtual-machines"></a>Sanal makineleri yedekleme

1. [Mabs sunucunuzu](backup-azure-microsoft-azure-backup.md) ve [depolama](backup-mabs-add-storage.md)alanınızı ayarlayın. Depolama alanınızı ayarlarken, bu depolama kapasitesi kılavuzlarını kullanın.
   - Ortalama sanal makine boyutu-100 GB
   - MABS sunucu başına sanal makine sayısı-800
   - Toplam 800 VM boyutu-80 TB
   - Yedekleme depolaması için gereken alan-80 TB

2. MABS koruma aracısını Hyper-V sunucusunda veya Hyper-V küme düğümlerinde ayarlayın. Konuk düzeyinde yedekleme yapıyorsanız, aracıyı Konuk düzeyinde yedeklemek istediğiniz VM 'Lere yükleyeceksiniz.

3. MABS yönetici konsolunda **, koruma** **grubu oluştur** ' a tıklayarak **yeni koruma grubu oluşturma** Sihirbazı ' > nı açın.

4. **Grup üyelerini seçin** sayfasında, Içinde bulundukları Hyper-V konak sunucularından korumak Istediğiniz VM 'leri seçin. Aynı koruma ilkesine sahip olacak tüm VM 'Leri tek bir koruma grubuna koymanız önerilir. Alanı verimli bir şekilde kullanmak için birlikte bulundurmayı etkinleştirin. Birlikte bulundurma, birden çok veri kaynağının tek bir çoğaltma ve kurtarma noktası birimi olması için, farklı koruma gruplarındaki verileri aynı diskte veya bant deposunda bulmanıza olanak tanır.

5. **Veri koruma yöntemini seçin** sayfasında bir koruma grubu adı belirtin. Azure Backup hizmetini kullanarak verileri Azure 'a yedeklemek istiyorsanız **disk kullanarak kısa vadeli koruma** istiyorum ' u seçin ve **çevrimiçi koruma** istiyorum ' u seçin.

6. **Saklama aralığı** > **kısa vadeli hedefleri belirtin** bölümünde disk verilerini ne kadar süreyle saklamak istediğinizi belirtin. **Eşitleme sıklığı**' nda verilerin artımlı yedeklemelerinin ne sıklıkta çalıştırılacağını belirtin. Alternatif olarak, artımlı yedeklemeler için bir Aralık seçmek yerine, **bir kurtarma noktasına hemen önce**seçeneğini etkinleştirebilirsiniz. Bu ayar etkinleştirildiğinde, MABS her bir zamanlanan kurtarma noktasıyla hemen önce hızlı tam yedekleme çalıştırır.

    > [!NOTE]
    >
    >Uygulama iş yüklerini koruyorsanız, uygulama artımlı yedeklemeleri desteklediğinden, kurtarma noktaları eşitleme sıklığına uygun olarak oluşturulur. Değilse, MABS artımlı yedekleme yerine hızlı bir tam yedekleme çalıştırır ve hızlı yedekleme zamanlamasına uygun olarak kurtarma noktaları oluşturur.

7. **Disk ayırmayı İncele** sayfasında, koruma grubu için ayrılmış depolama havuzu disk alanını gözden geçirin.

   **Toplam veri boyutu** , yedeklemek istediğiniz verilerin boyutudur ve **mabs üzerinde sağlanacak disk alanı** , mabs 'nin koruma grubu için önerdiği alandır. MABS, ayarlara bağlı olarak ideal yedekleme birimini seçer. Ancak, **disk ayırma ayrıntılarında**yedekleme birimi seçimlerini düzenleyebilirsiniz. İş yükleri için açılan menüden tercih edilen depolamayı seçin. Düzenlemeleriniz, **kullanılabilir disk depolama** bölmesinde **toplam depolama** ve **boş depolama** değerlerini değiştirir. Yetersiz sağlanan alan, daha sonra yedeklemeler ile devam etmek için, MABS 'ların birime eklemenizi önerdiği depolama miktarıdır.

8. **Çoğaltma oluşturma yöntemini seçin** sayfasında, veri koruma grubundaki ilk çoğaltmanın nasıl gerçekleştirileceğini belirtin. **Ağ üzerinden otomatik olarak çoğaltmayı**seçerseniz, yoğun olmayan bir zaman seçmeniz önerilir. Büyük miktarlarda veri veya en iyi ağ koşullarına göre, verileri çıkarılabilir medya kullanarak çevrimdışı olarak çoğaltmayı gerektiren **el ile**seçmeyi düşünün.

9. **Tutarlılık denetimi seçenekleri** sayfasında, tutarlılık denetimlerinin nasıl otomatikleştirilmesi istediğinizi seçin. Yalnızca çoğaltma verileri tutarsız hale geldiğinde veya bir zamanlamaya göre çalışacak şekilde bir denetim yapabilirsiniz. Otomatik tutarlılık denetimini yapılandırmak istemiyorsanız, koruma grubuna sağ tıklayıp **tutarlılık denetimi gerçekleştir**' i seçerek istediğiniz zaman el ile denetim gerçekleştirebilirsiniz.

    Koruma grubunu oluşturduktan sonra, verilerin ilk çoğaltması seçtiğiniz yönteme uygun olarak gerçekleşir. İlk çoğaltmadan sonra, her yedekleme koruma grubu ayarlarına göre gerçekleşir. Yedeklenen verileri kurtarmanız gerekiyorsa, aşağıdakileri göz önünde bulabilirsiniz:

## <a name="back-up-virtual-machines-configured-for-live-migration"></a>Dinamik geçiş için yapılandırılmış sanal makineleri yedekleme

Sanal makineler Dinamik geçişe dahil edildiğinde, mabs koruma Aracısı Hyper-V konağına yüklendiği sürece MABS sanal makineleri korumaya devam eder. MABS 'nin sanal makineleri koruduğu yol, söz konusu dinamik geçişin türüne bağlıdır.

**Küme Içinde dinamik geçiş** -sanal makine bir küme içinde geçirildiğinde, mabs geçişi algılar ve Kullanıcı müdahalesi gerektirmeden sanal makineyi yeni küme düğümünden yedekler. Depolama konumu değişmediğinden, MABS hızlı tam yedeklemelerle devam eder.

**Küme dışında dinamik geçiş** -sanal makine tek başına sunucular, farklı kümeler veya tek başına sunucu ile bir küme arasında geçirildiğinde, mabs geçişi algılar ve Kullanıcı müdahalesi olmadan sanal makineyi yedekleyebilir.

### <a name="requirements-for-maintaining-protection"></a>Korumayı sürdürmek için gerekenler

Dinamik geçiş sırasında korumayı sürdürmek için gerekenler şunlardır:

- Sanal makinelerin Hyper-V konakları, bir VMM sunucusunda, en az System Center 2012 SP1 çalıştıran bir System Center VMM bulutunda bulunmalıdır.

- Tüm Hyper-V konaklarında MABS koruma aracısının yüklü olması gerekir.

- MABS sunucularının VMM sunucusuna bağlı olması gerekir. VMM bulutundaki tüm Hyper-V ana bilgisayar sunucularının Ayrıca MABS sunucularına bağlanması gerekir. Bu, MABS 'in VMM sunucusuyla iletişim kurmasına izin vererek, MABS 'nin sanal makinenin Şu anda hangi Hyper-V konak sunucusuna çalıştığını bulabilir ve bu Hyper-V sunucusundan yeni bir yedekleme oluşturabilir. Hyper-V sunucusuna bir bağlantı kurulamazsa, yedekleme, MABS koruma aracısının ulaşılamaz olduğu iletiyle başarısız olur.

- Tüm MABS sunucuları, VMM sunucuları ve Hyper-V konak sunucuları aynı etki alanında olmalıdır.

### <a name="details-about-live-migration"></a>Dinamik geçiş hakkında ayrıntılar

Dinamik geçiş sırasında yedekleme için aşağıdakileri göz önünde edin:

- Dinamik geçiş depolama alanını aktarmışsa, MABS sanal makinenin tam tutarlılık denetimini gerçekleştirir ve hızlı tam yedeklemelerle devam eder. Depolama alanının dinamik geçişi gerçekleştiğinde, Hyper-V sanal sabit diski (VHD) veya VHDX 'i yeniden düzenler ve bu, MABS yedekleme verilerinin boyutunda tek seferlik bir ani artış oluşmasına neden olur.

- Sanal makine konağında, sanal korumayı etkinleştirmek için otomatik bağlama 'yı açın ve TCP Kanalı Boşaltma devre dışı bırakın.

- MABS, DPM-VMM yardımcı hizmetini barındırmak için varsayılan bağlantı noktası olarak 6070 numaralı bağlantı noktasını kullanır. Kayıt defterini değiştirmek için:

    1. **Hklm\software\microsoft\microsoft Data Protection Manager\Configuration**dizinine gidin.
    2. 32 bitlik bir DWORD değeri oluşturun: DpmVmmHelperServicePort ve güncelleştirilmiş bağlantı noktası numarasını kayıt defteri anahtarının bir parçası olarak yazın.
    3. ```<Install directory>\Azure Backup Server\DPM\DPM\VmmHelperService\VmmHelperServiceHost.exe.config```açın ve 6070 numaralı bağlantı noktasını yeni bağlantı noktasına değiştirin. Örneğin, ```<add baseAddress="net.tcp://localhost:6080/VmmHelperService/" />```
    4. DPM-VMM yardımcı hizmetini yeniden başlatın ve DPM hizmetini yeniden başlatın.

### <a name="set-up-protection-for-live-migration"></a>Dinamik geçiş için koruma ayarlama

Dinamik geçiş korumasını ayarlamak için:

1. MABS sunucusunu ve depolamayı ayarlayın ve MABS koruma aracısını VMM bulutundaki her Hyper-V ana bilgisayar sunucusuna veya küme düğümüne kurun. Bir kümede SMB depolama alanı kullanıyorsanız, tüm küme düğümlerine MABS koruma aracısını yükler.

2. MABS 'lerin VMM sunucusuyla iletişim kurabilmesi için, MABS sunucusunda VMM konsolunu istemci bileşeni olarak yükler. Konsol, VMM sunucusunda çalışan sürümle aynı sürümde olmalıdır.

3. MABSMachineName $ hesabını, VMM yönetim sunucusunda salt tanımlı bir yönetici hesabı olarak atayın.

4. Tüm Hyper-V ana bilgisayar sunucularını `Set-DPMGlobalProperty` PowerShell cmdlet 'i ile tüm MABS sunucularına bağlayın. Cmdlet birden çok MABS sunucu adını kabul eder. Şu biçimi kullanın: `Set-DPMGlobalProperty -dpmservername <MABSservername> -knownvmmservers <vmmservername>`. Daha fazla bilgi için bkz. [Set-DPMGlobalProperty](https://technet.microsoft.com/library/hh881752.aspx).

5. VMM bulutlarındaki Hyper-V konakları üzerinde çalışan tüm sanal makineler VMM 'de bulunduktan sonra bir koruma grubu ayarlayın ve korumak istediğiniz sanal makineleri ekleyin. Otomatik tutarlılık denetimleri, sanal makine Mobility senaryoları altında koruma için koruma grubu düzeyinde etkinleştirilmelidir.

6. Ayarlar yapılandırıldıktan sonra, bir sanal makine bir kümeden diğerine geçirildiğinde tüm yedeklemeler beklendiği gibi devam eder. Dinamik geçişin beklendiği gibi etkinleştirildiğini aşağıdaki gibi doğrulayabilirsiniz:

   1. DPM-VMM yardımcı hizmeti 'nin çalıştığını denetleyin. Aksi takdirde başlatın.

   2. Microsoft SQL Server Management Studio açın ve MABS veritabanını (DPMDB) barındıran örneğe bağlanın. DPMDB 'de şu sorguyu çalıştırın: `SELECT TOP 1000 [PropertyName] ,[PropertyValue] FROM[DPMDB].[dbo].[tbl_DLS_GlobalSetting]`.

      Bu sorgu `KnownVMMServer`adlı bir özellik içerir. Bu değer, `Set-DPMGlobalProperty` cmdlet 'i ile birlikte girdiğiniz değerle aynı olmalıdır.

   3. Belirli bir sanal makine için `PhysicalPathXML` *VMMIdentifier* parametresini doğrulamak üzere aşağıdaki sorguyu çalıştırın. `VMName`, sanal makinenin adıyla değiştirin.

      ```sql
      select cast(PhysicalPath as XML) from tbl_IM_ProtectedObject where DataSourceId in (select datasourceid from tbl_IM_DataSource   where DataSourceName like '%<VMName>%')
      ```

   4. Bu sorgunun döndürdüğü. xml dosyasını açın ve *VMMIdentifier* alanının bir değere sahip olduğunu doğrulayın.

### <a name="run-manual-migration"></a>El ile geçiş Çalıştır

Önceki bölümlerdeki adımları tamamladıktan sonra, MABS Özet Yöneticisi işi tamamlandığında, geçiş etkinleştirilir. Varsayılan olarak, bu iş gece yarısı başlar ve her sabah çalışır. El ile geçiş çalıştırmak istiyorsanız, her şeyin beklendiği gibi çalıştığını denetlemek için aşağıdakileri yapın:

1. SQL Server Management Studio açın ve MABS veritabanını barındıran örneğe bağlanın.

2. Şu sorguyu çalıştırın: `select * from tbl_SCH_ScheduleDefinition where JobDefinitionID='9B30D213-B836-4B9E-97C2-DB03C3EB39D7'`. Bu sorgu, **ScheduleID**değerini döndürür. Sonraki adımda kullanacağınız şekilde bu KIMLIĞI aklınızda bulabilirsiniz.

3. SQL Server Management Studio, **SQL Server Agent**' i genişletin ve ardından **işler**' i genişletin. Not ettiğiniz **ScheduleID** ' ye sağ tıklayın ve **adımdaki işi Başlat**' ı seçin.

İş çalıştırıldığında yedekleme performansı etkilenir. Dağıtımınızın boyutu ve ölçeği işin ne kadar zaman sona ermesini belirler.

## <a name="back-up-replica-virtual-machines"></a>Çoğaltma sanal makinelerini yedekleme

MABS Windows Server 2012 R2 veya üzeri bir sürümde çalışıyorsa, çoğaltma sanal makinelerini yedekleyebilirsiniz. Bu, birkaç nedenden dolayı yararlı olur:

**Çalışan iş yükü üzerindeki yedeklemelerin etkisini azaltır** -bir sanal makinenin yedeğinin alınması, anlık görüntü oluşturulurken bazı ek yüke neden olur. Yedekleme işlemini ikincil bir uzak siteye devrederek, çalışan iş yükü artık yedekleme işleminden etkilenmez. Bu yalnızca, yedekleme kopyasının uzak bir sitede depolandığı dağıtımlar için geçerlidir. Örneğin, günlük yedeklemeler alabilir ve hızlı geri yükleme sürelerini sağlamak için verileri yerel olarak saklayabilir, ancak uzun süreli saklama için uzaktan depolanan çoğaltma sanal makinelerinden aylık veya üç aylık yedeklemeler elde edebilirsiniz.

**Bant genişliğini kaydeder** -tipik bir uzak şube ofisi/Merkez dağıtımında, siteler arasında yedekleme verilerini aktarmak için uygun miktarda sağlanan bant genişliğine sahip olmanız gerekir. Bir çoğaltma ve yük devretme stratejisi oluşturursanız, veri yedekleme stratejinize ek olarak ağ üzerinden gönderilen gereksiz veri miktarını azaltabilirsiniz. Birincil yerine çoğaltma sanal makine verilerini yedekledikten sonra, ağ üzerinden yedeklenen verileri gönderme yükünü kaydedersiniz.

**Barındırıcı yedeklemesini etkinleştirilir** -barındırılan bir veri merkezini, ikincil veri merkezine gerek olmadan bir çoğaltma sitesi olarak kullanabilirsiniz. Bu durumda, barındırma sağlayıcı SLA çoğaltma sanal makinelerinin tutarlı yedeklemesini gerektirir.

Bir yük devretme başlatılana kadar bir çoğaltma sanal makinesi kapalıdır ve VSS, bir çoğaltma sanal makinesi için uygulamayla tutarlı bir yedeklemeyi garanti edemez. Bu nedenle, bir çoğaltma sanal makinesinin yedeklemesi yalnızca kilitlenme tutarlı olur. Kilitlenme tutarlılığı garantilendirilemeyebilir, yedekleme başarısız olur ve bu işlem birkaç koşulda gerçekleşebilir:

- Çoğaltma sanal makinesi sağlıklı değil ve kritik bir durumda.

- Çoğaltma sanal makinesi yeniden eşitleniyor (devam eden yeniden eşitleme veya yeniden eşitleme gerekiyor durumunda).

- Birincil ve ikincil site arasındaki ilk çoğaltma işlemi devam ediyor veya sanal makine için bekliyor.

- . hrl günlükleri, çoğaltma sanal makinesine uygulanıyor veya. hrl günlüklerini sanal disk üzerinde uygulamaya yönelik önceki bir eylem başarısız oldu ya da iptal edildi veya kesintiye uğradı.

- Çoğaltma sanal makinesinin geçişi veya yük devretmesi devam ediyor

## <a name="recover-backed-up-virtual-machines"></a>Yedeklenen sanal makineleri kurtarma

Yedeklenen bir sanal makineyi kurtarabilmeniz için, Kurtarma Sihirbazı 'nı kullanarak sanal makineyi ve belirli kurtarma noktasını seçebilirsiniz. Kurtarma Sihirbazı 'nı açmak ve bir sanal makineyi kurtarmak için:

1. MABS Yönetici Konsolu 'nda, VM 'nin adını yazın veya korunan öğeler listesini genişletin ve kurtarmak istediğiniz VM 'yi seçin.

2. Kurtarma **noktaları** bölmesinde, takvim üzerinde, kullanılabilir kurtarma noktalarını görmek için herhangi bir tarihe tıklayın. Ardından **yol** bölmesinde, Kurtarma Sihirbazı 'nda kullanmak istediğiniz kurtarma noktasını seçin.

3. **Eylemler** menüsünde, Kurtarma Sihirbazı 'nı açmak için **kurtar** ' ı tıklatın.

    Seçtiğiniz VM ve kurtarma noktası, **Kurtarma seçimini inceleyin** ekranında görünür. **İleri**’ye tıklayın.

4. **Kurtarma türü seçin** ekranında, verileri nereye geri yüklemek istediğinizi seçin ve ardından **İleri**' ye tıklayın.

    - **Özgün örneğe kurtar**: özgün örneği kurtardığınızda, özgün VHD silinir. MABS, VHD 'yi ve diğer yapılandırma dosyalarını Hyper-V VSS yazıcı kullanarak özgün konuma kurtarır. Kurtarma işleminin sonunda, sanal makineler hala yüksek oranda kullanılabilir.
        Kurtarma için kaynak grubunun mevcut olması gerekir. Kullanılabilir değilse, alternatif bir konuma kurtarın ve ardından sanal makineyi yüksek oranda kullanılabilir hale getirin.

    - **Sanal makine olarak herhangi bir konağa kurtar**: mabs, korumalı bir Hyper-v sanal makinesinin işlemci mimarisinden bağımsız olarak farklı bir Hyper-v konağına sorunsuz bir şekilde kurtarılmasını sağlayan alternatif konum kurtarmayı (ALR) destekler. Bir küme düğümüne kurtarılan Hyper-V sanal makineleri yüksek oranda kullanılabilir olmayacaktır. Bu seçeneği belirlerseniz, Kurtarma Sihirbazı hedef ve hedef yolu belirlemeye yönelik ek bir ekran sunar.

    - **Ağ klasörüne kopyala**: Mabs, Hyper-V sanal makinelerinin ana bilgisayar düzeyindeki yedeğinden dosya, klasör, birim ve sanal sabit diskleri (VHD), bir ağ paylaşımında veya mabs korumalı sunucusundaki bir birime öğe düzeyinde kurtarmanıza olanak sağlayan öğe düzeyinde kurtarmayı (ILR) destekler. Öğe düzeyinde kurtarma gerçekleştirmek için MABS koruma aracısının Konuk içinde yüklü olması gerekmez. Bu seçeneği belirlerseniz, Kurtarma Sihirbazı hedef ve hedef yolu belirlemeye yönelik ek bir ekran sunar.

5. **Kurtarma seçeneklerini belirtin** bölümünde kurtarma seçeneklerini yapılandırın ve **İleri**' ye tıklayın:

    - Düşük bant genişliğine sahip bir sanal makineyi kurtarıyorsanız, **ağ bant genişliği kullanımını azaltmayı**etkinleştirmek için **Değiştir** ' e tıklayın. Daraltma seçeneğini etkinleştirdikten sonra, kullanılabilir hale getirmek istediğiniz bant genişliği miktarını ve bu bant genişliğinin kullanılabildiği zamanı belirtebilirsiniz.
    - Ağınızı yapılandırdıysanız, **donanım anlık görüntülerini kullanarak San tabanlı kurtarmayı etkinleştir** ' i seçin.
    - Kurtarma işlemi tamamlandıktan sonra e **-posta bildirimi gönder** ' i seçin ve ardından e-posta bildirimlerinin gönderilmesini isterseniz kurtarma işlemi tamamlandıktan sonra e-posta adreslerini girin.

6. Özet ekranında tüm ayrıntıların doğru olduğundan emin olun. Ayrıntılar doğru değilse veya bir değişiklik yapmak istiyorsanız **geri**' ye tıklayın. Ayarları tatmin ediyorsanız kurtarma işlemini başlatmak için **kurtar** ' ı tıklatın.

7. **Kurtarma durumu** ekranı, kurtarma işi hakkında bilgi sağlar.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Backup Sunucusu’ndan veri kurtarma](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server)
