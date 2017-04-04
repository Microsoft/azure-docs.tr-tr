---
title: "VMware’den Azure’a Azure Site Recovery dağıtım planlayıcısı| Microsoft Docs"
description: "Bu belge Azure Site Recovery dağıtım planlayıcısı kullanıcı kılavuzudur."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 2/21/2017
ms.author: nisoneji
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 431f73e1be45dec9aa0fe186cb22078f8d95588d
ms.lasthandoff: 03/28/2017


---
# <a name="azure-site-recovery-deployment-planner"></a>Azure Site Recovery dağıtım planlayıcısı
Bu makale, VMware’den Azure’a üretim dağıtımları için Azure Site Recovery kullanım kılavuzudur.

## <a name="overview"></a>Genel Bakış

Site Recovery kullanarak herhangi bir VMware sanal makinesini (VM) korumaya başlamadan önce, istenen kurtarma noktası hedefini (RPO) karşılayacak günlük veri değişikliği hızınıza göre yeterli bant genişliğini ayırmanız gerekir. Şirket içinde doğru sayıda yapılandırma sunucusu ve işlem sunucusu dağıttığınızdan emin olun.

Ayrıca doğru tür ve sayıda hedef Azure depolama hesabı oluşturmanız gerekir. Zaman içinde artan kullanım nedeniyle kaynak üretim sunucularınızdaki büyümeyi hesaba katarak standart veya premium depolama hesapları oluşturun. İş yükü özelliklerine (örneğin, saniyede okuma/yazma G/Ç işlemi [IOPS] veya veri değişim sıklığı) ve Site Recovery limitlerine göre VM başına depolama türü seçin.

Azure Site Recovery dağıtım planlayıcısı genel önizlemesi, şu anda yalnızca VMware’den Azure’a senaryoları için kullanılabilen bir komut satırı aracıdır. Başarılı çoğaltma ve yük devretme testine yönelik bant genişliği ve Azure depolama gereksinimlerini anlamak için, bu aracı kullanarak VMware sanal makinelerinizin profilini uzaktan oluşturabilirsiniz (herhangi bir üretim etkisi olmadan). Şirket içinde herhangi bir Site Recovery bileşeni yüklemeden aracı çalıştırabilirsiniz. Ancak, elde edilen aktarım hızı sonuçlarını doğru şekilde almak için, planlayıcıyı üretim dağıtımının ilk adımlarından birinde dağıtmanız gereken Site Recovery yapılandırma sunucusunun en düşük gereksinimlerini karşılayan bir Windows Server üzerinde çalıştırmanız önerilir.

Araç aşağıdaki bilgileri sağlar:

**Uyumluluk değerlendirmesi**

* Disk sayısı, disk boyutu, IOPS ve değişim sıklığına göre VM uygunluk değerlendirmesi
* Delta çoğaltma için gereken tahmini ağ bant genişliği

**Ağ bant genişliği ile RPO değerlendirmesi karşılaştırması**

* Delta çoğaltma için gereken tahmini ağ bant genişliği
* Site Recovery’nin şirket içinden Azure’a alabileceği aktarım hızı
* Belirli bir süre içinde ilk çoğaltmayı tamamlamak için tahmin edilen bant genişliğine göre toplu hale getirilecek VM sayısı

**Azure altyapı gereksinimleri**

* Her VM için depolama türü (standart veya premium depolama hesabı) gereksinimi
* Çoğaltma için ayarlanacak toplam standart ve premium depolama hesabı sayısı
* Azure Depolama kılavuzuna göre depolama hesabı adlandırma önerileri
* Tüm sanal makineler için depolama hesabı yerleşimi
* Abonelik üzerinde yük devretme testi veya yük devretme öncesinde ayarlanacak Azure çekirdek sayısı
* Şirket içindeki her VM için önerilen Azure VM boyutu

**Şirket içi altyapı gereksinimleri**
* Şirket içinde dağıtılması gereken yapılandırma sunucusu ve işlem sunucusu sayısı

>[!IMPORTANT]
>
>Kullanım zaman içinde çoğalabileceğinden, önceki tüm araç hesaplamaları iş yükü özelliklerinde yüzde 30’luk bir büyüme olduğu varsayılarak ve tüm profil oluşturma ölçümlerinin (okuma/yazma IOPS, değişim hızı vb) yüzde 95’lik dilim değeri kullanılarak yapılır. Büyüme faktörü ve yüzdelik dilim hesaplaması öğelerinin her ikisi de yapılandırılabilir özelliktedir. Büyüme faktörü hakkında daha fazla bilgi almak için "Büyüme faktörü ile ilgili dikkat edilecek noktalar" bölümüne bakın. Yüzdelik dilim değeri hakkında daha fazla bilgi için "Hesaplama için kullanılan yüzdelik dilim değeri" bölümüne bakın.
>

## <a name="requirements"></a>Gereksinimler
Araçta başlıca iki aşama vardır: profil oluşturma ve rapor oluşturma. Yalnızca aktarım hızını hesaplamaya yönelik üçüncü bir seçenek de mevcuttur. Profil oluşturma ve aktarım hızı ölçümünün başlatıldığı sunucuya yönelik gereksinimler, aşağıdaki tabloda sunulmuştur:

| Sunucu gereksinimi | Açıklama|
|---|---|
|Profil oluşturma ve aktarım hızı ölçümü| <ul><li>İşletim sistemi: Microsoft Windows Server 2012 R2<br>(En azından [yapılandırma sunucusuna yönelik boyut önerileri](https://aka.ms/asr-v2a-on-prem-components) ile eşleşmesi idealdir)</li><li>Makine yapılandırması: 8 vCPU, 16 GB RAM, 300 GB HDD</li><li>[Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://developercenter.vmware.com/tool/vsphere_powercli/6.0)</li><li>[Visual Studio 2012 için Microsoft Visual C++ Yeniden Dağıtılabilir](https://aka.ms/vcplusplus-redistributable)</li><li>Bu sunucudan Azure’a İnternet erişimi</li><li>Azure depolama hesabı</li><li>Sunucu üzerinde yönetici erişimi</li><li>En az 100 GB boş disk alanı (30 günlük profili oluşturulmuş ve her biri ortalama üç diske sahip 1000 VM varsayıldığında)</li><li>VMware vCenter istatistik düzeyi ayarları 2 veya daha yüksek bir düzeye ayarlanmalıdır</li></ul>|
| Rapor oluşturma | Microsoft Excel 2013 ve üzerinin yüklü olduğu herhangi bir Windows PC ya da Windows Server |
| Kullanıcı izinleri | Profil oluşturma sırasında VMware vCenter sunucusuna/VMware vSphere ESXi ana bilgisayarına erişmek için kullanılan kullanıcı hesabına yönelik salt okunur izin |

> [!NOTE]
>
>Araç yalnızca VMDK ve RDM disklerine sahip VM’lerin profilini oluşturabilir. VM profilini iSCSI veya NFS diskleri ile oluşturamaz. Site Recovery, VMware sunucuları için iSCSI ve NFS disklerini desteklese de, dağıtım planlayıcısının konuk içinde bulunmaması ve yalnızca vCenter performans sayaçlarını kullanarak profil oluşturması nedeniyle araç bu disk türlerini görüntüleyemez.
>

## <a name="download-and-extract-the-public-preview"></a>Genel önizlemeyi indirin ve ayıklayın
1. [Site Recovery dağıtım planlayıcısı genel önizlemesinin](https://aka.ms/asr-deployment-planner) son sürümünü indirin.  
Araç bir zip klasöründe paketlenmiştir. Aracın geçerli sürümü yalnızca VMware’den Azure’a senaryosunu destekler.

2. Zip klasörünü, aracı çalıştırmak istediğiniz Windows sunucusuna kopyalayın.  
Sunucu, profili oluşturulacak VM’leri tutan vCenter sunucusu/vSphere ESXi ana bilgisayarına bağlanmak için ağ erişimine sahipse, aracı Windows Server 2012 R2’den çalıştırabilirsiniz. Ancak, aracı donanım yapılandırması [yapılandırma sunucusu boyutlandırma yönergeleri](https://aka.ms/asr-v2a-on-prem-components) ile eşleşen bir sunucu üzerinde çalıştırmanız önerilir. Site Recovery bileşenlerini şirket içinde zaten dağıttıysanız, aracı yapılandırma sunucusundan çalıştırın.

 Aracı çalıştırdığınız sunucuda, yapılandırma sunucusu (yerleşik bir işlem sunucusuna sahiptir) ile aynı donanım yapılandırmasına sahip olmanız önerilir. Bu tür bir yapılandırma, araç tarafından elde edildiği rapor edilen aktarım hızının, Site Recovery tarafından profil oluşturma sırasında elde edilebilecek gerçek aktarım hızı ile eşleşmesini sağlar. Aktarım hızı hesaplaması, sunucu üzerinde kullanılabilir ağ bant genişliğine ve sunucunun donanım yapılandırmasına (CPU, depolama vb.) bağlıdır. Aracı başka bir sunucudan çalıştırırsanız, bu sunucudan Microsoft Azure’a aktarım hızı hesaplanır. Ayrıca, sunucunun donanım yapılandırması, yapılandırma sunucusundan farklı olabileceği için, aracın elde edildiğini rapor ettiği aktarım hızı hatalı olabilir.

3. .zip klasörünü ayıklayın.  
Klasör birden fazla dosya ve alt klasör içerir. Yürütülebilir dosya, üst klasördeki ASRDeploymentPlanner.exe dosyasıdır.

    Örnek:  
    .zip dosyasını E:\ sürücüsüne kopyalayıp ayıklayın.
   E:\ASR Deployment Planner-Preview_v1.1.zip

    E:\ASR Deployment Planner-Preview_v1.1\ ASR Deployment Planner-Preview_v1.1\ ASRDeploymentPlanner.exe

## <a name="capabilities"></a>Özellikler
Komut satırı aracını (ASRDeploymentPlanner.exe) aşağıdaki üç modun herhangi birinde çalıştırabilirsiniz:

1. Profil oluşturma  
2. Rapor oluşturma
3. Aktarım hızı alma

İlk olarak, VM veri değişim sıklığı ve IOPS verilerini toplamak için profil oluşturma modunda aracı çalıştırın. Ardından, ağ bant genişliği ve depolama gereksinimlerini bulmak üzere raporu oluşturmak için aracı çalıştırın.

## <a name="profiling"></a>Profil oluşturma
Profil oluşturma modunda dağıtım planlayıcısı aracı, sanal makineye ilişkin performans verilerini toplamak için vCenter sunucusu/vSphere ESXi ana bilgisayarına bağlanır.

* Profil oluşturma işlemi sırasında üretim VM’leri ile doğrudan bağlantı kurulmadığı için bu VM’lerin performansı etkilenmez. Tüm performans verileri vCenter sunucusu/ vSphere ESXi ana bilgisayarından toplanır.
* Profil oluşturma nedeniyle sunucu üzerindeki etkinin önemsiz olduğundan emin olmak için, araç vCenter sunucusu/vSphere EXSi ana bilgisayarını 15 dakikada bir sorgular. Araç her dakikaya ait performans sayacı verilerini depoladığı için, sorgu aralığı profil oluşturma doğruluğunu tehlikeye atmaz.

### <a name="create-a-list-of-vms-to-profile"></a>Profili oluşturulacak sanal makinelerin listesini oluşturma
İlk olarak, profili oluşturulacak sanal makinelerin bir listesi gerekir. Aşağıdaki yordamda VMware vSphere PowerCLI komutlarını kullanarak bir VMware vCenter sunucusu/vSphere ESXi ana bilgisayarındaki tüm sanal makinelerin adlarını alabilirsiniz. Alternatif olarak, profilini el ile oluşturmak istediğiniz sanal makinelerin kolay adlarını veya IP adreslerini bir dosyada listeleyebilirsiniz.

1. VMware vSphere PowerCLI’nin yüklü olduğu sanal makinede oturum açın.
2. VMware vSphere PowerCLI konsolunu açın.
3. Betik için yürütme ilkesinin etkin olduğundan emin olun. Devre dışı bırakılmışsa, VMware vSphere PowerCLI konsolunu yönetici modunda başlatın ve aşağıdaki komutu çalıştırarak etkinleştirin:

            Set-ExecutionPolicy –ExecutionPolicy AllSigned

4. Bir vCenter sunucusu/vSphere ESXi ana bilgisayarındaki tüm VM’lerin adlarını almak ve listeyi bir .txt dosyasında depolamak için burada listelenen iki komutu çalıştırın.
&lsaquo;Sunucu adı&rsaquo;, &lsaquo;kullanıcı adı&rsaquo;, &lsaquo;parola&rsaquo;, &lsaquo;outputfile.txt&rsaquo; değerlerini girdilerinizle değiştirin.

            Connect-VIServer -Server <server name> -User <user name> -Password <password>

            Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>

5. Çıktı dosyasını Not Defteri’nde açın ve sonra profilini oluşturmak istediğiniz tüm VM’lerin adlarını, her satıra bir VM gelecek şekilde başka bir dosyaya (örneğin, ProfileVMList.txt) kopyalayın. Bu dosya, komut satırı aracının *-VMListFile* parametresinin girdisi olarak kullanılır.

    ![Dağıtım planlayıcısındaki VM ad listesi](./media/site-recovery-deployment-planner/profile-vm-list.png)

### <a name="start-profiling"></a>Profil oluşturmaya başlama
Profili oluşturulacak sanal makinelerin listesini oluşturduktan sonra, aracı profil oluşturma modunda çalıştırabilirsiniz. Aracın profil oluşturma modunda çalıştırılmasına yönelik zorunlu ve isteğe bağlı parametreler aşağıda verilmiştir.

ASRDeploymentPlanner.exe -Operation StartProfiling /?

| Parametre adı | Açıklama |
|---|---|
| -Operation | StartProfiling |
| -Server | Sanal makineleri için profil oluşturulacak vCenter sunucusunun/vSphere ESXi ana bilgisayarının tam etki alanı adı.|
| -User | vCenter sunucusuna/vSphere ESXi ana bilgisayarına bağlanmak için kullanıcı adı. Kullanıcının en azından salt okunur erişimi olmalıdır.|
| -VMListFile |    Profili oluşturulacak sanal makinelerin listesini içeren dosya. Dosya yolu mutlak veya göreli olabilir. Bu dosya her satırda bir VM adı/IP adresi içermelidir. Dosyada belirtilen sanal makine adı, vCenter sunucusu/vSphere ESXi ana bilgisayarındaki VM adıyla aynı olmalıdır.<br>Örneğin, VMList.txt dosyası aşağıdaki sanal makineleri içerir:<ul><li>virtual_machine_A</li><li>10.150.29.110</li><li>virtual_machine_B</li><ul> |
| -NoOfDaysToProfile | Profil oluşturmanın çalıştırılacağı gün sayısı. Ortamınızda belirtilen süre içindeki iş yükü deseninin doğru bir öneri sağlayacak şekilde gözlemlenip kullanılması için profil oluşturma işleminin 15 günden uzun süre çalıştırılmaması önerilir. |
| -Directory | (İsteğe bağlı) Profil oluşturma sırasında oluşturulan profil oluşturma verilerini depolamak için evrensel adlandırma kuralı (UNC) veya yerel dizin yolu. Dizin adı belirtilmemişse, geçerli yolun altındaki ‘ProfiledData’ adlı dizin varsayılan dizin olarak kullanılır. |
| -Password | (İsteğe bağlı) vCenter sunucusuna/vSphere ESXi ana bilgisayarına bağlanmak için kullanılacak parola. Şu anda belirtmezseniz, komut yürütülürken sorulacaktır.|
| -StorageAccountName | (İsteğe bağlı) Şirket içinden Azure’a veri çoğaltma için ulaşılabilir aktarım hızını bulmak için depolama hesabı adı. Araç, aktarım hızını hesaplamak için test verilerini bu depolama hesabına yükler.|
| -StorageAccountKey | (İsteğe bağlı) Depolama hesabına erişmek için kullanılan depolama hesabı anahtarı. Azure portalı > Depolama hesapları > <*Depolama hesabı adı*> > Ayarlar > Erişim Anahtarları > Anahtar1 (veya klasik depolama hesabı için birincil erişim anahtarı) öğesine gidin. |

VM’lerinizin en az 15 ila 30 günlük profilinin oluşturulması önerilir. Profil oluşturma süresi boyunca ASRDeploymentPlanner.exe çalışmaya devam eder. Araç, profil oluşturma süre girdisini gün cinsinden alır. Aracın hızlı bir testi için birkaç saat veya dakika boyunca profil oluşturmak isterseniz, genel önizleme sürümünde saati karşılık gelen gün ölçüsüne dönüştürmeniz gerekir. Örneğin, 30 dakika boyunca profil oluşturmak için girdinin 30/(60*24) = 0,021 gün olması gerekir. İzin verilen en kısa profil oluşturma süresi 30 dakikadır.

Profil oluşturma sırasında, Site Recovery’nin çoğaltma sırasında yapılandırma sunucusu veya işlem sunucusundan Azure’a elde edilebileceği aktarım hızını bulmak için, isteğe bağlı olarak bir depolama hesabı adı ve anahtarı geçirebilirsiniz. Profil oluşturma sırasında depolama hesabı adı ve anahtarı geçirilmezse, araç ulaşılabilir aktarım hızını hesaplamaz.

Çeşitli sanal makine kümeleri için aracın birden çok örneğini çalıştırabilirsiniz. Sanal makine adlarının, profil kümelerinin hiçbirinde yinelenmediğinden emin olun. Örneğin, on sanal makine (VM1 - VM10) profili oluşturdunuz ve birkaç gün sonra beş sanal makine (VM11 - VM15) profili daha oluşturmak istiyorsunuz; bu durumda, ikinci sanal makine kümesi (VM11 - VM15) için başka bir komut satırı konsolundan aracı çalıştırabilirsiniz. Ancak, ikinci sanal makine kümesinde birinci profil oluşturma örneğinden herhangi bir sanal makine adı olmadığından veya ikinci çalıştırma için farklı bir çıktı dizini kullandığınızdan emin olun. Aracın iki örneği aynı sanal makinelerin profilini oluşturmak için kullanılır ve aynı çıktı dizinini kullanırsa, oluşturulan rapor hatalı olacaktır.

Sanal makine yapılandırması, profil oluşturma işleminin başında bir kez yakalanır ve VMDetailList.xml adlı bir dosyada depolanır. Rapor oluşturulduğunda bu bilgiler kullanılır. Profil oluşturmanın başlangıcı ile bitişi arasında VM yapılandırmasında meydana gelen hiçbir değişiklik (örneğin, çekirdek, disk veya NIC sayısının artması) yakalanmaz. Profili oluşturulmuş bir VM yapılandırması profil oluşturma sırasında değiştiyse, genel önizleme sürümünde rapor oluştururken en son VM bilgilerini almaya yönelik geçici çözüm aşağıda verilmiştir:

* VMdetailList.xml dosyasını yedekleyip, dosyayı geçerli konumundan silin.
* -User ve -Password bağımsız değişkenlerini rapor oluşturma sırasında geçirin.

Profil oluşturma komutu, profil oluşturma dizininde birkaç dosya oluşturur. Rapor oluşturmayı etkileyeceği için hiçbir dosyayı silmeyin.

#### <a name="example-1-profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Örnek 1: 30 günlük sanal makine profili oluşturma ve şirket içinden Azure’a aktarım hızını bulma
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  30  -User vCenterUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="example-2-profile-vms-for-15-days"></a>Örnek 2: 15 günlük sanal makine profili oluşturma

```
ASRDeploymentPlanner.exe -Operation StartProfiling -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  15  -User vCenterUser1
```

#### <a name="example-3-profile-vms-for-1-hour-for-a-quick-test-of-the-tool"></a>Örnek 3: Aracın hızlı bir testine yönelik 1 saatlik VM profili oluşturma
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  0.04  -User vCenterUser1
```

>[!NOTE]
>
>* Aracın çalıştığı sunucu yeniden başlatılırsa veya kilitlenmişse ya da Ctrl+C tuşlarını kullanarak aracı kapatırsanız, profili oluşturulan veriler korunur. Ancak, profili oluşturulan verilen son 15 dakikası kaybedilebilir. Böyle bir durumda, sunucu yeniden başlatıldıktan sonra aracı profil oluşturma modunda yeniden çalıştırmanız gerekir.
>* Depolama hesabı adı ve anahtarı geçirildiğinde, araç profil oluşturma işleminin son adımında aktarım hızını ölçer. Profil oluşturma tamamlanmadan önce araç kapatılırsa, aktarım hızı hesaplanmaz. Raporu oluşturmadan önce aktarım hızını bulmak için, komut satırı konsolundan GetThroughput işlemini çalıştırabilirsiniz. Aksi takdirde, oluşturulan rapor aktarım hızı bilgilerini içermez.


## <a name="generate-a-report"></a>Rapor oluşturma
Araç, rapor çıktısı olarak tüm dağıtım önerilerini özetleyen makro özellikli bir Microsoft Excel dosyası (XLSM dosyası) oluşturur. Rapor, DeploymentPlannerReport_<*benzersiz sayısal tanımlayıcı*>.xlsm olarak adlandırılıp belirtilen dizine yerleştirilir.

Profil oluşturma tamamlandıktan sonra, aracı rapor oluşturma modunda çalıştırabilirsiniz. Aşağıdaki tabloda, rapor oluşturma modunda çalışmaya yönelik zorunlu ve isteğe bağlı parametreler listelenmiştir.

`ASRDeploymentPlanner.exe -Operation GenerateReport /?`

|Parametre adı | Açıklama |
|-|-|
| -Operation | GenerateReport |
| -Server |  Raporu oluşturulacak profili oluşturulmuş sanal makinelerin bulunduğu vCenter/vSphere sunucusu tam etki alanı adı veya IP adresi (profil oluşturma sırasında kullandığınız adın veya IP adresinin aynısını kullanın). Profil oluşturma sırasında bir vCenter sunucusu kullandıysanız, rapor oluşturma için vSphere sunucusu kullanamazsınız.|
| -VMListFile | Raporun oluşturulacağı profili oluşturulmuş sanal makinelerin listesini içeren dosya. Dosya yolu mutlak veya göreli olabilir. Bu dosya her satırda bir VM adı veya IP adresi içermelidir. Dosyada belirtilen VM adları, vCenter sunucusu/vSphere ESXi ana bilgisayarındakilerle aynı olmalı ve profil oluşturma sırasında kullanılanla eşleşmelidir.|
| -Directory | (İsteğe bağlı) Profili oluşturulan verilerin (profil oluşturma sırasında oluşturulan dosyalar) depolandığı UNC veya yerel dizin yolu. Bu veriler, rapor oluşturmak için gereklidir. Bir ad belirtilmezse, 'ProfiledData' dizini kullanılır. |
| -GoalToCompleteIR | (İsteğe bağlı) Profili oluşturulan sanal makinelerin ilk çoğaltmasının tamamlanması gereken saat sayısı. Oluşturulan rapor, ilk çoğaltması belirtilen süre içinde tamamlanması gereken VM sayısını sağlar. Varsayılan değer 72 saattir. |
| -User | (İsteğe bağlı) vCenter/vSphere sunucusuna bağlanmak için kullanılacak kullanıcı adı. Bu ad, sanal makinelerin disk sayısı, çekirdek sayısı, NIC sayısı gibi raporda kullanılacak en son yapılandırma bilgilerini getirmek için kullanılır. Bir ad belirtilmezse, profil oluşturma işleminin başında toplanan yapılandırma bilgileri kullanılır. |
| -Password | (İsteğe bağlı) vCenter sunucusuna/vSphere ESXi ana bilgisayarına bağlanmak için kullanılacak parola. Parola parametre olarak belirtilmezse, daha sonra komut yürütülürken sorulacaktır. |
| -DesiredRPO | (İsteğe bağlı) Dakika cinsinden istenen kurtarma noktası hedefi. Varsayılan değer 15 dakikadır.|
| -Bandwidth | MB/sn cinsinden bant genişliği. Belirtilen bant genişliği için ulaşılabilecek RPO’yu hesaplamak için kullanılan parametre. |
| -StartDate | (İsteğe bağlı) AA-GG-YYYY:SS:DD (24 saat biçiminde) cinsinden başlangıç tarihi ve saati. *StartDate* değeri *EndDate* ile birlikte belirtilmelidir. StartDate belirtildiğinde, StartDate ile EndDate arasında toplanan profili oluşturulmuş veriler için rapor oluşturulur. |
| -EndDate | (İsteğe bağlı) AA-GG-YYYY:SS:DD (24 saat biçiminde) cinsinden bitiş tarihi ve saati. *EndDate* değeri *StartDate* ile birlikte belirtilmelidir. EndDate belirtildiğinde, StartDate ile EndDate arasında toplanan profili oluşturulmuş veriler için rapor oluşturulur. |
| -GrowthFactor | (İsteğe bağlı) Yüzde olarak ifade edilen büyüme faktörü. Varsayılan değer yüzde 30'dur. |

#### <a name="example-1-generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Örnek 1: Profili oluşturulan veriler yerel sürücüde olduğunda raporu varsayılan değerlerle oluşturma
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-2-generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Örnek 2: Profili oluşturulan veriler uzak bir sunucuda olduğunda rapor oluşturma
Uzak dizin üzerinde okuma/yazma erişiminiz olmalıdır.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-3-generate-a-report-with-a-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>Örnek 3: Belirli bir bant genişliği ve belirtilen süre içinde IR tamamlama hedefi ile rapor oluşturma
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -Bandwidth 100 -GoalToCompleteIR 24
```

#### <a name="example-4-generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Örnek 4: Yüzde 30’luk varsayılan değer yerine yüzde 5 büyüme faktörü ile rapor oluşturma
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="example-5-generate-a-report-with-a-subset-of-profiled-data"></a>Örnek 5: Profili oluşturulan verilerin bir alt kümesi ile rapor oluşturma
Örneğin, 30 günlük profili oluşturulmuş verilerinizin olduğunu ve raporu yalnızca 20 gün için oluşturduğunuzu varsayalım.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="example-6-generate-a-report-for-5-minute-rpo"></a>Örnek 6: 5 dakikalık RPO için rapor oluşturma
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

## <a name="percentile-value-used-for-the-calculation"></a>Hesaplama için kullanılan yüzdelik değer
**Rapor oluşturulurken, profil oluşturma sırasında toplanan performans ölçümlerinin hangi varsayılan yüzdelik dilim değeri araç tarafından kullanılır?**

Aracın, tüm sanal makinelerin profili oluşturulurken toplanan okuma/yazma IOPS, yazma IOPS ve veri değişim sıklığı için varsayılan değeri yüzde 95’lik dilimdir. Bu ölçüm, VM’lerinizin geçici olaylar nedeniyle görebileceği %100’lük dilim artışının, hedef depolama hesabı ve kaynak bant genişliği gereksinimlerini belirlemek için kullanılmamasını sağlar. Örneğin, geçici olay günde bir kez gerçekleştirilen bir yedekleme işi, düzenli aralıklarla yapılan veritabanı dizini oluşturma veya analiz raporu oluşturma etkinliği ya da kısa süreli diğer benzer olaylar olabilir.

Yüzde 95’lik dilim değeri, gerçek iş yükü özelliklerinin gerçek bir resmini verir ve bu iş yükleri Azure üzerinde çalışırken en iyi performansı sağlar. Bu sayıyı değiştirmenizin gerekli olacağı düşünülmemektedir. Bu sayıyı değiştirirseniz (örn. yüzde 90’lık dilime), bu *ASRDeploymentPlanner.exe.config* yapılandırma dosyasını varsayılan klasörde güncelleştirebilir ve kaydederek var olan profili oluşturulmuş verilere ilişkin yeni bir rapor oluşturabilirsiniz.
```
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

## <a name="growth-factor-considerations"></a>Büyüme faktörü ile ilgili dikkat edilmesi gerekenler
**Dağıtımları planlarken neden büyüme faktörünü göz önünde bulundurmalıyım?**

Zaman içindeki kullanımın olası artışı varsayılarak, iş yükü özelliklerinizde büyümenin hesaba katılması önemlidir. Koruma uygulandıktan sonra iş yükü özellikleriniz değişirse, korumayı devre dışı bırakıp yeniden etkinleştirmeden farklı bir depolama hesabına geçiş yapamazsınız.

Örneğin, bugün sanal makinenizin standart bir depolama çoğaltma hesabına uygun olduğunu düşünelim. Önümüzdeki üç ay üzerinde bazı değişiklikler oluşabilir:

* VM üzerinde çalışan uygulamanın kullanıcı sayısı artar.
* VM üzerinde artan değişim hızı, Site Recovery çoğaltmasının gerçekleştirilebilmesi için VM’nizin premium depolamaya geçmesini gerektirir.
* Sonuç olarak, bir premium depolama hesabında korumayı devre dışı bırakıp yeniden etkinleştirmeniz gerekir.

Büyümeyi dağıtım planlaması sırasında ve varsayılan değer yüzde 30 iken planlamanız önerilir. Uygulamalarınızın kullanım desenini ve büyüme tahminlerini en iyi siz bilirsiniz ve rapor oluştururken bu sayıyı uygun şekilde siz değiştirebilirsiniz. Ayrıca, profili oluşturulmuş aynı verilerle farklı büyüme faktörleri içeren birden fazla rapor oluşturabilir ve en çok işinize yarayacak hedef depolama ile kaynak bant genişliği önerilerini belirleyebilirsiniz.

Oluşturulan Microsoft Excel raporu aşağıdaki bilgileri içerir:

* [Girdi](site-recovery-deployment-planner.md#input)
* [Öneriler](site-recovery-deployment-planner.md#recommendations-with-desired-rpo-as-input)
* [Öneriler-Bant Genişliği Girdisi](site-recovery-deployment-planner.md#recommendations-with-available-bandwidth-as-input)
* [VM<->Depolama Yerleşimi](site-recovery-deployment-planner.md#vm-storage-placement)
* [Uyumlu VM’ler](site-recovery-deployment-planner.md#compatible-vms)
* [Uyumsuz VM’ler](site-recovery-deployment-planner.md#incompatible-vms)

![Dağıtım planlayıcısı](./media/site-recovery-deployment-planner/dp-report.png)

## <a name="get-throughput"></a>Aktarım hızı alma

Site Recovery’nin çoğaltma sırasında şirket içinden Azure’a elde edebildiği aktarım hızını tahmin etmek için aracı GetThroughput modunda çalıştırın. Araç, üzerinde çalıştığı sunucudan aktarım hızını hesaplar. Bu sunucunun yapılandırma sunucusu boyutlandırma kılavuzunu temel alması idealdir. Site Recovery altyapı bileşenlerini şirket içinde zaten dağıttıysanız, aracı yapılandırma sunucusunda çalıştırın.

Bir komut satırı konsolu açın ve Site Recovery dağıtım planlama aracının klasörüne gidin. ASRDeploymentPlanner.exe dosyasını aşağıdaki parametrelerle çalıştırın.

`ASRDeploymentPlanner.exe -Operation GetThroughput /?`

|Parametre adı | Açıklama |
|-|-|
| -operation | GetThroughput |
| -Directory | (İsteğe bağlı) Profili oluşturulan verilerin (profil oluşturma sırasında oluşturulan dosyalar) depolandığı UNC veya yerel dizin yolu. Bu veriler, rapor oluşturmak için gereklidir. Bir dizin adı belirtilmezse ‘ProfiledData’ dizini kullanılır. |
| -StorageAccountName | Şirket içinden Azure’a veri çoğaltma için kullanılan bant genişliğini bulmak için depolama hesabı adı. Araç, kullanılan bant genişliğini bulmak için test verilerini bu depolama hesabına yükler. |
| -StorageAccountKey | Depolama hesabına erişmek için kullanılan depolama hesabı anahtarı. Azure portalı > Depolama hesapları > <*Depolama hesabı adı*> > Ayarlar > Erişim Anahtarları > Anahtar1 (veya klasik depolama hesabı için birincil erişim anahtarı) öğesine gidin. |
| -VMListFile | Kullanılan bant genişliğini hesaplamak için profili oluşturulacak sanal makinelerin listesini içeren dosya. Dosya yolu mutlak veya göreli olabilir. Bu dosya her satırda bir VM adı/IP adresi içermelidir. Dosyada belirtilen sanal makine adı, vCenter sunucusu/vSphere ESXi ana bilgisayarındaki VM adıyla aynı olmalıdır.<br>Örneğin, VMList.txt dosyası aşağıdaki sanal makineleri içerir:<ul><li>VM_A</li><li>10.150.29.110</li><li>VM_B</li></ul>|

Araç, belirtilen dizinde 64 MB’lık birkaç asrvhdfile<#>.vhd (“#” sayıdır) dosyası oluşturur. Araç, aktarım hızını bulmak için dosyaları depolama hesabına yükler. Aktarım hızı ölçüldükten sonra araç tüm dosyaları depolama hesabından ve yerel sunucudan siler. Araç aktarım hızını hesaplarken herhangi bir nedenle sonlandırılırsa, dosyaları depolama alanından veya yerel sunucudan silmez. Bunları el ile silmeniz gerekir.

Aktarım hızı belirli bir zaman noktasında ölçülür ve diğer tüm faktörlerin aynı kalması koşuluyla Site Recovery’nin çoğaltma sırasında ulaşabileceği en yüksek aktarım hızıdır. Örneğin, herhangi bir uygulama aynı ağ üzerinde daha fazla bant genişliği tüketmeye başlarsa, çoğaltma sırasında gerçek aktarım hızı farklılık gösterir. Bir yapılandırma sunucusundan GetThroughput komutunu çalıştırıyorsanız, araç korunan sanal makineleri ve devam eden çoğaltmayı fark etmez. Korunan VM’ler yüksek veri değişim sıklığına sahip olduğunda GetThroughput işlemi çalıştırılırsa, ölçülen aktarım hızının sonucu farklı olur. Çeşitli zamanlarda hangi aktarım hızı düzeylerine ulaşılabileceğini anlamak için, profil oluşturma sırasında farklı zaman noktalarında aracın çalıştırılması önerilir. Raporda, araç son ölçülen aktarım hızını gösterir.

### <a name="example"></a>Örnek
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Directory  E:\vCenter1_ProfiledData -VMListFile E:\vCenter1_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

>[!NOTE]
>
> Aracı, yapılandırma sunucusu ile aynı depolama ve CPU özelliklerine sahip bir sunucuda çalıştırın.
>
> Çoğaltma için, önerilen bant genişliğini sürenin yüzde 100’ünü karşılayacak şekilde ayarlayın. Doğru bant genişliğini sağladıktan sonra araç tarafından ulaşıldığı bildirilen aktarım hızında herhangi bir artış görmezseniz aşağıdakileri yapın:
>
>  1. Site Recovery aktarım hızını sınırlayan herhangi bir ağ Hizmet Kalitesi (QoS) olup olmadığını denetleyin.
>
>  2. Ağ gecikme süresini en aza indirmek için, Site Recovery kasanızın desteklenen en yakın fiziksel Microsoft Azure bölgesinde olup olmadığını denetleyin.
>
>  3. Donanımı iyileştirip iyileştiremeyeceğinizi (örneğin, HDD’den SSD’ye) belirlemek için yerel depolama özelliklerini denetleyin.
>
>  4. [Çoğaltma için kullanılan ağ bant genişliği miktarını artırmak](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth) üzere işlem sunucusundaki Site Recovery ayarlarını değiştirin.

## <a name="recommendations-with-desired-rpo-as-input"></a>Girdi olarak istenen RPO ile öneriler

### <a name="profiled-data"></a>Profili oluşturulan veriler

![Dağıtım planlayıcısında profili oluşturulmuş veriler görünümü](./media/site-recovery-deployment-planner/profiled-data-period.png)

**Profili oluşturulmuş veri süresi**: Profil oluşturma işleminin gerçekleştirildiği süre. Varsayılan olarak, rapor oluşturma sırasında StartDate ve EndDate seçenekleri kullanılarak rapor belirli bir süre için oluşturulmadıkça araç, profili oluşturulmuş tüm verileri hesaplamaya dahil eder.

**Sunucu Adı**: sanal makinelerinin raporu oluşturulan VMware vCenter veya ESXi ana bilgisayarının adı ya da IP adresi.

**İstenen RPO**: Dağıtımınıza yönelik kurtarma noktası hedefi. Varsayılan olarak, gerekli ağ bant genişliği 15, 30 ve 60 dakikalık RPO değerleri için hesaplanır. Seçim temel alınarak, etkilenen değerler sayfada güncelleştirilir. Raporu oluştururken *DesiredRPOinMin* parametresini kullandıysanız, değer İstenen RPO sonucunda gösterilir.

### <a name="profiling-overview"></a>Profil oluşturmaya genel bakış

![Dağıtım planlayıcısında profil oluşturma sonuçları](./media/site-recovery-deployment-planner/profiling-overview.png)

**Profili Oluşturulan Toplam Sanal Makine Sayısı**: Profili oluşturulan verileri bulunan sanal makinelerin toplam sayısı. VMListFile dosyasında profili oluşturulmamış sanal makineler varsa, bu sanal makineler rapor oluşturma işleminde göz önünde bulundurulmaz ve profili oluşturulan toplam sanal makine sayısının dışında tutulur.

**Uyumlu Sanal Makineler**: Site Recovery kullanılarak Azure’da korunabilen sanal makine sayısı. Bu sayı, gerekli ağ bant genişliği, depolama hesabı sayısı, Azure çekirdek sayısı ve yapılandırma sunucusu ile ek işlem sunucularının sayısının hesaplandığı uyumlu sanal makinelerin toplamıdır. Her uyumlu VM’nin ayrıntıları "Uyumlu VM’ler" bölümünde bulunabilir.

**Uyumsuz Sanal Makineler**: Site Recovery ile koruma için uygun olmayan, profili oluşturulmuş sanal makine sayısı. Uyumsuzluğun nedenleri, “Uyumsuz VM’ler” bölümünde belirtilmiştir. VMListFile içinde profili oluşturulmamış bir sanal makinenin adı varsa, bu sanal makineler uyumsuz sanal makine sayısının dışında bırakılır. Bu sanal makineler, “Uyumsuz VM’ler” bölümünün sonunda “Veri bulunamadı” olarak listelenir.

**İstenen RPO**: Dakika cinsinden istediğiniz kurtarma noktası hedefi. Rapor üç RPO değeri için oluşturulur: 15 (varsayılan), 30 ve 60 dakika. Rapordaki bant genişliği önerisi, tablonun sağ üst köşesinde bulunan İstenen RPO açılır listesindeki seçiminize göre değişir. Raporu özel bir değer ile *-DesiredRPO* parametresini kullanarak oluşturduysanız, bu özel değer İstenen RPO açılır listesinde varsayılan olarak gösterilir.

### <a name="required-network-bandwidth-mbps"></a>Gerekli ağ bant genişliği (Mb/sn)

![Dağıtım planlayıcısında gerekli ağ bant genişliği](./media/site-recovery-deployment-planner/required-network-bandwidth.png)

**Yüzde 100 RPO süresini karşılamak için:** İstediğiniz yüzde 100 RPO süresini karşılamak için Mb/sn cinsinden ayrılacak önerilen bant genişliğidir. Bu bant genişliği miktarı, herhangi bir RPO ihlalini önlemek üzere tüm uyumlu sanal makinelerinizin kararlı durum delta çoğaltması için ayrılmalıdır.

**Yüzde 90 RPO süresini karşılamak için**: Geniş bant fiyatlandırması veya istediğiniz yüzde 100 RPO süresini karşılamak için gereken bant genişliğini ayarlayamamanız durumunda başka bir nedenle, istediğiniz yüzde 90 RPO süresini karşılayabilen daha düşük bir bant genişliği ayarlamayı seçebilirsiniz. Daha düşük olan bu bant genişliğini ayarlamanın etkilerini anlamak için, raporda beklenen RPO ihlallerinin sayısı ve süresine ilişkin bir ne yapmalı analizi sağlar.

**Elde Edilen Aktarım Hızı**: Depolama hesabının bulunduğu Microsoft Azure bölgesine GetThroughput komutunu gönderdiğiniz sunucudan aktarım hızıdır. Bu aktarım hızı sayısı, yapılandırma sunucusu veya işlem sunucusu depolama ve ağ özelliklerinin, aracı çalıştırdığınız sunucudaki özelliklerle aynı kalması şartıyla, Site Recovery kullanarak uyumlu VM’leri korurken elde edebileceğiniz tahmini düzeyi gösterir.

Çoğaltma için, önerilen bant genişliğini sürenin yüzde 100’ünü karşılayacak şekilde ayarlamanız gerekir. Bant genişliğini ayarladıktan sonra araç tarafından ulaşıldığı bildirilen aktarım hızında herhangi bir artış görmezseniz aşağıdakileri yapın:

1. Site Recovery aktarım hızını sınırlayan herhangi bir ağ Hizmet Kalitesi (QoS) olup olmadığını denetleyin.

2. Ağ gecikme süresini en aza indirmek için, Site Recovery kasanızın desteklenen en yakın fiziksel Microsoft Azure bölgesinde olup olmadığını denetleyin.

3. Donanımı iyileştirip iyileştiremeyeceğinizi (örneğin, HDD’den SSD’ye) belirlemek için yerel depolama özelliklerini denetleyin.

4. [Çoğaltma için kullanılan ağ bant genişliği miktarını artırmak](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth) üzere işlem sunucusundaki Site Recovery ayarlarını değiştirin.

Aracı korunan sanal makinelere zaten sahip olan bir yapılandırma sunucusu veya işlem sunucusu üzerinde çalıştırıyorsanız, aracı birkaç kez çalıştırın. Elde edilen aktarım hızı sayısı, zamanın o noktasında işlenen değişim hızı miktarına bağlı olarak değişir.

Tüm kurumsal Site Recovery dağıtımları için [ExpressRoute](https://aka.ms/expressroute) kullanılması önerilir.

### <a name="required-storage-accounts"></a>Gerekli depolama hesapları
Aşağıdaki grafikte tüm uyumlu sanal makineleri korumak için gereken depolama hesaplarının (standart ve premium) toplam sayısı gösterilmektedir. Her bir VM için kullanılacak depolama hesabını öğrenmek için "VM depolama yerleşimi" bölümüne bakın.

![Dağıtım planlayıcısında gerekli depolama hesapları](./media/site-recovery-deployment-planner/required-azure-storage-accounts.png)

### <a name="required-number-of-azure-cores"></a>Gerekli Azure çekirdek sayısı
Bu sonuç, tüm uyumlu sanal makinelerin yük devretme işlemi ya da yük devretme testi öncesinde ayarlanması gereken toplam çekirdek sayısıdır. Abonelikte çok az sayıda çekirdek varsa, yük devretme testi veya yük devretme sırasında Azure Site Recovery, sanal makineleri oluşturamaz.

![Dağıtım planlayıcısında gereken Azure çekirdek sayısı](./media/site-recovery-deployment-planner/required-number-of-azure-cores.png)

### <a name="required-on-premises-infrastructure"></a>Gerekli şirket içi altyapısı
Bu sayı, tüm uyumlu sanal makineleri korumak için yapılandırılması gereken yapılandırma sunucusu ve ek işlem sunucularının toplam sayısıdır. [Yapılandırma sunucusu için desteklenen boyut önerilerine](https://aka.ms/asr-v2a-on-prem-components) bağlı olarak, araç ek sunucular önerebilir. Öneri, günlük değişim sıklığı veya en fazla korunan VM sayısından (VM başına ortalama üç disk olduğu varsayılarak) yapılandırma sunucusu veya ek işlem sunucusunda ilk gerçekleşen olaya göre yapılır. Günlük toplam değişim sıklığı ve toplam korunan disk sayısına ilişkin ayrıntılar “Girdi” tablosunda bulunabilir.

![Dağıtım planlayıcısında gerekli şirket içi altyapı](./media/site-recovery-deployment-planner/required-on-premises-infrastructure.png)

### <a name="what-if-analysis"></a>Benzetim analiz
Bu analiz, sürenin yalnızca yüzde 90’ını karşılamasını istediğiniz RPO için daha düşük bir bant genişliği ayarladığınızda profil oluşturma sırasında kaç tane ihlal oluşabileceğini özetler. Belirli bir günde bir veya daha fazla RPO ihlali ortaya çıkabilir. Grafik, günün yoğun RPO değerini gösterir.
Bu analizi temel alarak, belirtilen düşük bant genişliği ile tüm günlerdeki RPO ihlali sayısının ve bir gündeki en yoğun RPO gerçekleşme zamanının kabul edilebilir olup olmadığına karar verebilirsiniz. Değer kabul edilebilir düzeydeyse, çoğaltma için düşük bant genişliği ayırabilirsiniz; aksi takdirde, istenilen yüzde 100 RPO süresini karşılamak üzere önerilen yüksek bant genişliğini ayırmanız gerekir.

![Dağıtım planlayıcısında benzetim analizi](./media/site-recovery-deployment-planner/what-if-analysis.png)

### <a name="recommended-vm-batch-size-for-initial-replication"></a>İlk çoğaltma için önerilen VM toplu iş boyutu
Bu bölümde, ayarlanmakta olan sürenin %100 RPO değerini karşılamak amacıyla ilk çoğaltmayı önerilen bant genişliği ile 72 saat içinde tamamlamak için paralel olarak korunması gereken sanal makine sayısı önerilmektedir. Bu değer yapılandırılabilir bir değerdir. Rapor oluşturma zamanında bu değeri değiştirmek için *GoalToCompleteIR* parametresini kullanın.

Buradaki grafikte, tüm uyumlu makinelerde algılanan ortalama sanal makine boyutuna göre 72 saat içinde ilk çoğaltmayı tamamlamak için bir bant genişliği değer aralığı ve hesaplanmış sanal makine toplu iş boyutu sayısı gösterilmektedir.

Genel önizleme sürümünde rapor, bir toplu işe hangi sanal makinelerin dahil edilmesi gerektiğini belirtmez. Her bir sanal makinenin boyutunu bulmak ve bir toplu işe yönelik sanal makinelerinizi seçmek ya da bilinen iş yükü özelliklerine göre seçim yapmak için, “Uyumlu VM’ler” bölümünde gösterilen disk boyutunu kullanabilirsiniz. İlk çoğaltmayı tamamlama süresi, gerçek VM disk boyutu, kullanılan disk alanı ve kullanılabilir ağ aktarım hızı ile doğru orantılı bir şekilde değişir.

![Önerilen VM toplu iş boyutu](./media/site-recovery-deployment-planner/recommended-vm-batch-size.png)

### <a name="growth-factor-and-percentile-values-used"></a>Kullanılan büyüme faktörü ve yüzdelik değerler
Tablonun alt kısmındaki bu bölümde, profili oluşturulan sanal makinelerin tüm performans sayaçları için kullanılan yüzdelik dilim değeri (varsayılan değer yüzde 95’lik dilim) ve tüm hesaplamalarda kullanılan büyüme faktörü (varsayılan değer yüzde 30) gösterilmektedir.

![Kullanılan büyüme faktörü ve yüzdelik değerler](./media/site-recovery-deployment-planner/max-iops-and-data-churn-setting.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Girdi olarak kullanılabilir bant genişliği ile ilgili öneriler

![Girdi olarak kullanılabilir bant genişliği ile ilgili öneriler](./media/site-recovery-deployment-planner/profiling-overview-bandwidth-input.png)

Site Recovery çoğaltması için x MB/sn’den fazla bant genişliği ayarlayamayacağınızı bildiğiniz bir durumla karşılaşabilirsiniz. Araç, kullanılabilir bant genişliğini girmenize (rapor oluşturma sırasında -Bandwidth parametresini kullanarak) ve dakika cinsinden ulaşılabilir RPO değerini almanıza olanak tanır. Bu ulaşılabilir RPO değeriyle ek bant genişliği ayarlamanızın gerekli olup olmadığına veya bu RPO ile bir olağanüstü durum kurtarma çözümüne sahip olmak isteyip istemediğinize karar verebilirsiniz.

![500 MB/sn bant genişliği için elde edilebilen RPO](./media/site-recovery-deployment-planner/achievable-rpos.png)

## <a name="input"></a>Girdi
Girdi çalışma sayfası, profili oluşturulmuş VMware ortamına genel bir bakış sağlar.

![Profili oluşturulmuş VMware ortamına genel bakış](./media/site-recovery-deployment-planner/Input.png)

**Başlangıç Tarihi** ve **Bitiş Tarihi**: Rapor oluşturma için göz önünde bulundurulan profil oluşturma verilerinin başlangıç ve bitiş tarihleri. Varsayılan olarak, başlangıç tarihi profil oluşturmanın başladığı, bitiş tarihi ise profil oluşturmanın durdurulduğu tarihtir. Rapor bu parametrelerle oluşturulduysa bunlar ‘StartDate’ ve ‘EndDate’ değerleri olabilir.

**Profil oluşturulan toplam gün sayısı**: Raporun oluşturulduğu başlangıç ile bitiş tarihleri arasında profil oluşturulan toplam gün sayısı.

**Uyumlu sanal makine sayısı**: Gerekli ağ bant genişliği, gerekli depolama hesabı sayısı, Microsoft Azure çekirdek sayısı ve yapılandırma sunucusu ile ek işlem sunucularının sayısının hesaplandığı uyumlu sanal makinelerin toplamı.

**Tüm uyumlu sanal makinelerdeki toplam disk sayısı**: Bu sayı, dağıtımda kullanılacak yapılandırma sunucusu ve ek işlem sunucusu sayısına karar vermeye yönelik girdilerden biri olarak kullanılır.

**Bir uyumlu sanal makinedeki ortalama disk sayısı**: Tüm uyumlu sanal makinelerde hesaplanan ortalama disk sayısıdır.

**Ortalama disk boyutu (GB)**: Tüm uyumlu sanal makinelerde hesaplanan ortalama disk boyutudur.

**İstenen RPO (dakika)**: Varsayılan kurtarma noktası hedefi veya rapor oluşturma sırasında gerekli bant genişliğini tahmin etmek üzere ‘DesiredRPO’ parametresi için geçirilen değer.

**İstenen bant genişliği (Mb/sn)**: Rapor oluşturma sırasında ulaşılabilir RPO’yu tahmin etmek üzere ‘Bandwidth’ parametresi için geçirdiğiniz değerdir.

**Bir günde gözlemlenen tipik veri değişim sıklığı (GB)**: Profil oluşturulan tüm günlerde gözlemlenen ortalama veri değişim sıklığıdır. Bu sayı, dağıtımda kullanılacak yapılandırma sunucusu ve ek işlem sunucusu sayısına karar vermeye yönelik girdilerden biri olarak kullanılır.


## <a name="vm-storage-placement"></a>VM-depolama yerleşimi

![VM-depolama yerleşimi](./media/site-recovery-deployment-planner/vm-storage-placement.png)

**Disk Depolama Türü**: **Yerleştirilecek VM’ler** sütununda bahsedilen tüm ilgili sanal makineleri çoğaltmak için kullanılan standart veya premium depolama hesabıdır.

**Önerilen Ön Ek**: depolama hesabını adlandırmak için kullanılabilecek, üç karakterli bir önerilen ön ektir. Kendi ön ekinizi kullanabilirsiniz, ancak aracın önerisi [depolama hesapları için bölüm adlandırma kuralına](https://aka.ms/storage-performance-checklist) uygundur.

**Önerilen Hesap Adı**: Önerilen ön eki ekledikten sonra depolama hesabı adı. Köşeli ayraç (< ve >) içindeki adı özel girdinizle değiştirin.

**Kayıt Depolama Hesabı:** Tüm çoğaltma kayıtları standart bir depolama hesabında depolanır. Premium depolama hesabına çoğaltılan sanal makineler için günlük depolamaya yönelik ek bir standart depolama hesabı oluşturun. Tek bir standart kayıt depolama hesabı, birden fazla premium çoğaltma depolama hesabı tarafından kullanılabilir. Standart depolama hesaplarına çoğaltılan sanal makineler, kayıtlarla aynı depolama hesabını kullanır.

**Önerilen Günlük Hesabı Adı**: Önerilen ön eki ekledikten sonra depolama günlük hesabı adı. Köşeli ayraç (< ve >) içindeki adı özel girdinizle değiştirin.

**Yerleştirme Özeti**: Çoğaltma ve yük devretme testi veya yük devretme sırasında depolama hesabındaki toplam sanal makine yükünün özeti. Buna depolama hesabıyla eşlenmiş toplam sanal makine sayısı, bu depolama hesabına yerleştirilen tüm sanal makinelerdeki toplam okuma/yazma IOPS sayısı, toplam yazma (çoğaltma) IOPS sayısı, tüm disklerde ayarlanan toplam boyut ve toplam disk sayısı dahildir.

**Yerleştirilecek Sanal Makineler**: En iyi performans ve kullanım için belirli bir depolama hesabına yerleştirilmesi gereken tüm sanal makinelerin listesi.

## <a name="compatible-vms"></a>Uyumlu VM’ler
![Uyumlu VM'lerin Excel elektronik tablosu](./media/site-recovery-deployment-planner/compatible-vms.png)

**VM Adı**: Bir rapor oluşturulurken VMListFile içinde kullanılan VM adı veya IP adresi. Bu sütunda ayrıca sanal makinelere bağlanan diskler (VMDK) listelenir. Yinelenen adlara veya IP adreslerine sahip vCenter sanal makinelerini birbirinden ayırt etmek için, adlar ESXi ana bilgisayar adını içerir. Listelenen ESXi ana bilgisayarı, profil oluşturma sırasında araç keşfettiğinde VM’in yerleştirildiği yerdir.

**VM Uyumluluğu**: Değerler **Evet** ve **Evet**\* şeklindedir. **Evet**\* değer, VM’nin [Azure Premium Depolama](https://aka.ms/premium-storage-workload) için uygun olduğu örneklere yöneliktir. Burada, profili oluşturulmuş yüksek değişim sıklığı veya IOPS diski P20 ya da P30 kategorisine uyar, ancak diskin boyutu diskin bir P10 veya P20 ile eşlenmesine neden olur. Depolama hesabı, bir diskin boyutuna göre hangi premium depolama disk türüne eşleneceğine karar verir. Örneğin:
* <128 GB bir P10’dur.
* 128 GB ile 512 GB arası P20'dir.
* 512 GB ile 1023 GB arası P30'dur.

Bir diskin iş yükü özellikleri diski P20 veya P30 kategorisine koyarken boyutu nedeniyle daha düşük bir premium depolama disk türüne eşleniyorsa, araç bu VM’yi **Evet**\* olarak işaretler. Araç ayrıca kaynak disk boyutunu önerilen premium depolama disk türüne uyacak şekilde değiştirmenizi veya hedef disk türünü yük devretme sonrasını değiştirmenizi önerir.

**Depolama Türü**: Standart veya Premium.

**Önerilen Ön Ek**: Üç karakterli depolama hesabı ön ekidir.

**Depolama Hesabı**: Önerilen depolama hesabı ön ekini kullanan ad.

**Okuma/Yazma IOPS (Büyüme Faktörü ile)**: Disk üzerinde gelecekteki büyüme faktörünü de (varsayılan değer yüzde 30) içeren en yoğun iş yükü okuma/yazma IOPS değeridir (varsayılan değer yüzde 95’lik dilim). Sanal makinenin en yoğun okuma/yazma IOPS değeri profil oluşturma işleminin her dakikası boyunca tek disklerin okuma/yazma IOPS değerinin toplamı olduğundan, bir sanal makinenin toplam okuma/yazma IOPS değeri her zaman sanal makinenin ayrı disklerinin okuma/yazma IOPS değerine eşit değildir.

**Mb/sn cinsinden Veri Değişim Sıklığı (Büyüme Faktörü ile)**: Disk üzerinde gelecekteki büyüme faktörünü de (varsayılan değer yüzde 30) içeren en yüksek erime oranıdır (varsayılan değer yüzde 95’lik dilim). Sanal makinenin en yoğun veri değişim sıklığı, profil oluşturma döneminin her dakikasında içindeki ayrı disklerin veri değişim sıklığı toplamının tepe noktası olduğundan, sanal makinenin toplam veri değişim sıklığı her zaman sanal makinedeki ayrı disklerin veri değişim sıklığının toplamı olmayacaktır.

**Azure VM Boyutu**: Bu şirket içi sanal makine için eşlenen ideal Azure Cloud Services makine boyutudur. Eşleme, şirket içi sanal makinenin belleğine, disk/çekirdek/NIC sayısına ve okuma/yazma IOPS değerine bağlıdır. Her zaman şirket içi VM özelliklerinin tümüyle eşleşen en düşük Azure VM boyutunun kullanılması önerilir.

**Disk Sayısı**: Sanal makine üzerindeki toplam disk sayısı (VMDK).

**Disk boyutu (GB)**: Sanal makinenin tüm disklerinin toplam kurulum boyutu. Araç ayrıca sanal makinedeki ayrı diskler için disk boyutunu gösterir.

**Çekirdek**: Sanal makine üzerindeki CPU çekirdeği sayısı.

**Bellek (MB)**: VM üzerindeki RAM.

**NIC**: VM üzerindeki NIC sayısı.

## <a name="incompatible-vms"></a>Uyumsuz VM’ler

![Uyumsuz VM'lerin Excel elektronik tablosu](./media/site-recovery-deployment-planner/incompatible-vms.png)

**VM Adı**: Bir rapor oluşturulurken VMListFile içinde kullanılan VM adı veya IP adresi. Bu sütunda ayrıca sanal makinelere bağlanan VMDK’lar listelenir. Yinelenen adlara veya IP adreslerine sahip vCenter sanal makinelerini birbirinden ayırt etmek için, adlar ESXi ana bilgisayar adını içerir. Listelenen ESXi ana bilgisayarı, profil oluşturma sırasında araç keşfettiğinde VM’in yerleştirildiği yerdir.

**VM Uyumluluğu**: Belirli bir sanal makinenin, Site Recovery ile kullanım için neden uyumlu olmadığını gösterir. Sanal makinenin her uyumsuz diski için, yayımlanan [depolama sınırlarına](https://aka.ms/azure-storage-scalbility-performance) göre nedenler aşağıdakilerden biri olabilir:

* Disk boyutu > 1023 GB’dir. Azure Depolama şu anda 1 TB’den büyük disk boyutlarını desteklememektedir.

* Toplam VM boyutu (çoğaltma + TFO), desteklenen depolama hesabı boyut sınırını (35 TB) aşıyor. Bu uyumsuzluk genellikle sanal makine içindeki tek bir diskin standart depolama için desteklenen Azure veya Site Recovery sınırlarını aşan bir performans özelliği olduğunda gerçekleşir. Bu tür bir örnek, sanal makineyi premium depolama bölgesine iter. Ancak, bir premium depolama hesabı için desteklenen en büyük boyut 35 TB’dir ve tek bir korunan sanal makine birden fazla depolama hesabında korunamaz. Ayrıca, korunan bir sanal makine üzerinde yük devretme testi yürütüldüğünde, test ile çoğaltma aynı depolama hesabında devam eder. Bu örnekte, çoğaltmanın ilerlemesi ve yük devretme testinin paralel olarak başarılı olması için disk boyutunun 2 katını ayarlayın.
* Kaynak IOPS, depolama IOPS için disk başına desteklenen 5000 limitini aşıyor.
* Kaynak IOPS, depolama IOPS için VM başına desteklenen 80.000 limitini aşıyor.
* Ortalama veri değişim sıklığı, disk için ortalama G/Ç’ye yönelik 10 Mb/sn’lik Site Recovery veri değişim sıklığı limitini aşıyor.
* Sanal makine üzerindeki tüm disklerde bulunan toplam veri değişim sıklığı, VM başına 54 MB/sn’lik desteklenen Site Recovery veri değişim sıklığı sınırını aşıyor.
* Algılanan ortalama yazma IOPS değeri, disk için 840 olan desteklenen Site Recovery IOPS limitini aşıyor.
* Hesaplanan anlık görüntü depolama alanı, 10 TB’lik desteklenen anlık görüntü depolama limitini aşıyor.

**Okuma/Yazma IOPS (Büyüme Faktörü ile)**: Disk üzerinde gelecekteki büyüme faktörünü de (varsayılan değer yüzde 30) içeren en yoğun iş yükü IOPS değeridir (varsayılan değer yüzde 95’lik dilim). Sanal makinenin en yoğun okuma/yazma IOPS değeri profil oluşturma işleminin her dakikası boyunca tek disklerin okuma/yazma IOPS değerinin toplamı olduğundan, bir sanal makinenin toplam okuma/yazma IOPS değeri her zaman sanal makinenin ayrı disklerinin okuma/yazma IOPS değerine eşit değildir.

**Mb/sn cinsinden Veri Değişim Sıklığı (Büyüme Faktörü ile)**: disk üzerinde gelecekteki büyüme faktörünü de (varsayılan değer yüzde 30) içeren en yüksek erime oranıdır (varsayılan değer yüzde 95’lik dilim). Sanal makinenin en yoğun veri değişim sıklığı, profil oluşturma döneminin her dakikasında içindeki ayrı disklerin veri değişim sıklığı toplamının tepe noktası olduğundan, sanal makinenin toplam veri değişim sıklığı her zaman sanal makinedeki ayrı disklerin veri değişim sıklığının toplamı olmayacaktır.

**Disk Sayısı**: Sanal makine üzerindeki toplam VMDK sayısı.

**Disk boyutu (GB)**: Sanal makinenin tüm disklerinin toplam kurulum boyutu. Araç ayrıca sanal makinedeki ayrı diskler için disk boyutunu gösterir.

**Çekirdek**: Sanal makine üzerindeki CPU çekirdeği sayısı.

**Bellek (MB)**: VM üzerindeki RAM miktarı.

**NIC**: VM üzerindeki NIC sayısı.


## <a name="site-recovery-limits"></a>Site Recovery limitleri

**Çoğaltma depolama hedefi** | **Ortalama kaynak disk G/Ç boyutu** |**Ortalama kaynak disk veri değişim sıklığı** | **Günlük toplam kaynak disk veri değişim sıklığı**
---|---|---|---
Standart depolama | 8 KB    | 2 Mb/sn | Disk başına 168 GB
Premium P10 disk | 8 KB    | 2 Mb/sn | Disk başına 168 GB
Premium P10 disk | 16 KB | 4 Mb/sn |    Disk başına 336 GB
Premium P10 disk | 32 KB veya daha büyük | 8 Mb/sn | Disk başına 672 GB
Premium P20 veya P30 disk | 8 KB    | 5 Mb/sn | Disk başına 421 GB
Premium P20 veya P30 disk | 16 KB veya daha büyük |10 Mb/sn | Disk başına 842 GB

Bunlar yüzde 30 G/Ç çakışmasını varsayan ortalama sayılardır. Site Recovery; çakışma oranı, büyük yazma boyutları ve gerçek iş yükü G/Ç davranışına göre daha yüksek aktarım hızını işleyebilir. Yukarıdaki sayılar yaklaşık beş dakikalık tipik bir kapsamı varsayar. Diğer bir deyişle, veriler karşıya yüklendikten sonra işlenir ve beş dakika içinde kurtarma noktası oluşturulur.

Bu limitler yaptığımız testleri temel alsa da mümkün olan tüm uygulama G/Ç birleşimlerini kapsamamaktadır. Gerçek sonuçlar, uygulamanızın G/Ç karışımına göre değişebilir. En iyi sonuçlar için, gerçek performans görüntüsünü elde etmek üzere, dağıtım planlamasından sonra bile yük devretme testi kullanılarak her zaman kapsamlı uygulama testleri gerçekleştirilmesi önerilir.

## <a name="updating-the-deployment-planner"></a>Dağıtım planlayıcısını güncelleştirme
Dağıtım planlayıcısını güncelleştirmek için aşağıdakileri yapın:

1. [Azure Site Recovery dağıtım planlayıcısı](https://aka.ms/asr-deployment-planner)’nın en son sürümünü indirin.

2. .zip klasörünü çalıştırmak istediğiniz sunucuya kopyalayın.

3. .zip klasörünü ayıklayın.

4. Aşağıdakilerden birini yapın:
 * En son sürüm bir profil oluşturma düzeltmesi içermiyor ve profil oluşturma planlayıcının geçerli sürümünde devam ediyorsa, profil oluşturmaya devam edin.
 * En son sürüm bir profil oluşturma düzeltmesi içeriyorsa, geçerli sürümünüzde profil oluşturmayı durdurmanız ve profil oluşturma işlemini yeni sürümle yeniden başlatmanız önerilir.

  >[!NOTE]
  >
  >Yeni sürümle profil oluşturma işlemini başlattığınızda, aracın profil verilerini mevcut dosyalara iliştirebilmesi için aynı çıktı dizini yolunu geçirin. Raporu oluşturmak için profili oluşturulmuş verilerin tamamı kullanılır. Farklı bir çıktı dizininden geçerseniz, yeni dosyalar oluşturulur ve profili oluşturulmuş eski veriler rapor oluşturma işleminde kullanılamaz.
  >
  >Her yeni dağıtım planlayıcısı, .zip dosyasının toplu bir güncelleştirmesidir. En yeni dosyaları önceki klasöre kopyalamanız gerekmez. Yeni bir klasör oluşturup kullanabilirsiniz.


## <a name="version-history"></a>Sürüm geçmişi
### <a name="11"></a>1.1
Güncelleştirme: 9 Mart 2017

Aşağıdaki sorunlar çözülmüştür:

* vCenter farklı ESXi ana bilgisayarları üzerinde aynı ad veya IP adresine sahip iki veya daha fazla sanal makineye sahipse araç, sanal makinelerin profilini oluşturamaz.
* Uyumlu VM'ler ve Uyumsuz VM’ler çalışma sayfaları için kopyalama ve arama devre dışı bırakıldı.

### <a name="10"></a>1.0
Güncelleme: 23 Şubat 2017

Azure Site Recovery Dağıtım Planlayıcısı genel önizleme 1.0 sürümünde aşağıdaki bilinen sorunlar bulunmaktadır (gelecek güncelleştirmelerde giderilecek):

* Araç yalnızca VMware’den Azure’a senaryosu için çalışır, Hyper-V’den Azure’a dağıtımlar için çalışmaz. Hyper-V’den Azure’a dağıtım senaryosu için [Hyper-V kapasite planlayıcısı aracını](./site-recovery-capacity-planning-for-hyper-v-replication.md) kullanın.
* GetThroughput işlemi, Microsoft Azure’un US Government ve Çin bölgelerinde desteklenmez.
* vCenter sunucusu farklı ESXi ana bilgisayarları üzerinde aynı ad veya IP adresine sahip iki veya daha fazla sanal makineye sahipse araç, sanal makinelerin profilini oluşturamaz. Bu sürümde, araç VMListFile dosyasındaki yinelenen sanal makine adları veya IP adresleri için profil oluşturmayı atlar. Bunun geçici çözümü, sanal makine profillerinin vCenter sunucusu yerine bir ESXi ana bilgisayarı kullanılarak oluşturulmasıdır. Her ESXi ana bilgisayarı için bir örnek çalıştırmanız gerekir.

