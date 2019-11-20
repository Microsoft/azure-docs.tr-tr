---
title: VMware olağanüstü durum kurtarma için Dağıtım Planlayıcısı Azure Site Recovery çalıştırın
description: Bu makalede, Azure 'da VMware olağanüstü durum kurtarma için Azure Site Recovery Dağıtım Planlayıcısı nasıl çalıştırılacağı açıklanır.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: mayg
ms.openlocfilehash: 044e5c5df8e0af67e4717b864de1e31fc2520408
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953293"
---
# <a name="run-the-deployment-planner-for-vmware-disaster-recovery"></a>VMware olağanüstü durum kurtarma için Dağıtım Planlayıcısı çalıştırma
Bu makale, VMware’den Azure’a üretim dağıtımları için Azure Site Recovery Dağıtım Planlayıcısı kullanım kılavuzudur.


## <a name="modes-of-running-deployment-planner"></a>Dağıtım planlayıcısını çalıştırma modları
Komut satırı aracını (ASRDeploymentPlanner.exe) aşağıdaki üç modun herhangi birinde çalıştırabilirsiniz:

1.  [Profil oluşturma](#profile-vmware-vms)
2.  [Rapor oluşturma](#generate-report)
3.  [Aktarım hızı alma](#get-throughput)

İlk olarak, VM veri değişim sıklığı ve IOPS verilerini toplamak için profil oluşturma modunda aracı çalıştırın. Ardından, ağ bant genişliği, depolama gereksinimleri ve DR maliyetini bulmak üzere raporu oluşturmak için aracı çalıştırın.

## <a name="profile-vmware-vms"></a>VMware VM'lerinin profilini oluşturma
Profil oluşturma modunda dağıtım planlayıcısı aracı, sanal makineye ilişkin performans verilerini toplamak için vCenter sunucusu/vSphere ESXi ana bilgisayarına bağlanır.

* Profil oluşturma işlemi sırasında üretim VM’leri ile doğrudan bağlantı kurulmadığı için bu VM’lerin performansı etkilenmez. Tüm performans verileri vCenter sunucusu/ vSphere ESXi ana bilgisayarından toplanır.
* Profil oluşturma nedeniyle sunucu üzerindeki etkinin önemsiz olduğundan emin olmak için, araç vCenter sunucusu/vSphere EXSi ana bilgisayarını 15 dakikada bir sorgular. Araç her dakikaya ait performans sayacı verilerini depoladığı için, sorgu aralığı profil oluşturma doğruluğunu tehlikeye atmaz.

### <a name="create-a-list-of-vms-to-profile"></a>Profili oluşturulacak sanal makinelerin listesini oluşturma
İlk olarak, profili oluşturulacak sanal makinelerin bir listesi gerekir. Aşağıdaki yordamda VMware vSphere PowerCLI komutlarını kullanarak bir VMware vCenter sunucusu/vSphere ESXi ana bilgisayarındaki tüm sanal makinelerin adlarını alabilirsiniz. Alternatif olarak, profilini el ile oluşturmak istediğiniz sanal makinelerin kolay adlarını veya IP adreslerini bir dosyada listeleyebilirsiniz.

1. VMware vSphere PowerCLI’nin yüklü olduğu sanal makinede oturum açın.
2. VMware vSphere PowerCLI konsolunu açın.
3. Betik için yürütme ilkesinin etkin olduğundan emin olun. Devre dışı bırakılmışsa, VMware vSphere PowerCLI konsolunu yönetici modunda başlatın ve aşağıdaki komutu çalıştırarak etkinleştirin:

            Set-ExecutionPolicy –ExecutionPolicy AllSigned

4. Cmdlet 'in adı olarak Connect-VIServer tanınmazsa, isteğe bağlı olarak aşağıdaki komutu çalıştırmanız gerekebilir.

            Add-PSSnapin VMware.VimAutomation.Core

5. Bir vCenter sunucusu/vSphere ESXi ana bilgisayarındaki tüm VM’lerin adlarını almak ve listeyi bir .txt dosyasında depolamak için burada listelenen iki komutu çalıştırın.
&lsaquo;Sunucu adı&rsaquo;, &lsaquo;kullanıcı adı&rsaquo;, &lsaquo;parola&rsaquo;, &lsaquo;outputfile.txt&rsaquo; değerlerini girdilerinizle değiştirin.

            Connect-VIServer -Server <server name> -User <user name> -Password <password>

            Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>

6. Çıktı dosyasını Not Defteri’nde açın ve sonra profilini oluşturmak istediğiniz tüm VM’lerin adlarını, her satıra bir VM gelecek şekilde başka bir dosyaya (örneğin, ProfileVMList.txt) kopyalayın. Bu dosya, komut satırı aracının *-VMListFile* parametresinin girdisi olarak kullanılır.

    ![Dağıtım planlayıcısındaki VM ad listesi
](media/site-recovery-vmware-deployment-planner-run/profile-vm-list-v2a.png)

### <a name="start-profiling"></a>Profil oluşturmaya başlama
Profili oluşturulacak sanal makinelerin listesini oluşturduktan sonra, aracı profil oluşturma modunda çalıştırabilirsiniz. Aracın profil oluşturma modunda çalıştırılmasına yönelik zorunlu ve isteğe bağlı parametreler aşağıda verilmiştir.

```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```

| Parametre adı | Açıklama |
|---|---|
| -Operation | StartProfiling |
| -Server | Sanal makineleri için profil oluşturulacak vCenter sunucusunun/vSphere ESXi ana bilgisayarının tam etki alanı adı.|
| -User | vCenter sunucusuna/vSphere ESXi ana bilgisayarına bağlanmak için kullanıcı adı. Kullanıcının en azından salt okunur erişimi olmalıdır.|
| -VMListFile | Profili oluşturulacak sanal makinelerin listesini içeren dosya. Dosya yolu mutlak veya göreli olabilir. Bu dosya her satırda bir VM adı/IP adresi içermelidir. Dosyada belirtilen sanal makine adı, vCenter sunucusu/vSphere ESXi ana bilgisayarındaki VM adıyla aynı olmalıdır.<br>Örneğin, VMList.txt dosyası aşağıdaki sanal makineleri içerir:<ul><li>virtual_machine_A</li><li>10.150.29.110</li><li>virtual_machine_B</li><ul> |
|-NoOfMinutesToProfile|Profil oluşturmanın çalıştırılacağı dakika sayısı. En az 30 dakika olmalıdır.|
|-NoOfHoursToProfile|Profil oluşturmanın çalıştırılacağı saat sayısı.|
| -NoOfDaysToProfile | Profil oluşturmanın çalıştırılacağı gün sayısı. Ortamınızda belirtilen süre içindeki iş yükü deseninin doğru bir öneri sağlayacak şekilde gözlemlenip kullanılması için profil oluşturma işleminin 7 günden uzun süre çalıştırılması önerilir. |
|-Sanallaştırma|Sanallaştırma türünü (VMware veya Hyper-V) belirtin.|
| -Directory | (İsteğe bağlı) Profil oluşturma sırasında oluşturulan profil oluşturma verilerini depolamak için evrensel adlandırma kuralı (UNC) veya yerel dizin yolu. Dizin adı belirtilmemişse, geçerli yolun altındaki ‘ProfiledData’ adlı dizin varsayılan dizin olarak kullanılır. |
| -Password | (İsteğe bağlı) vCenter sunucusuna/vSphere ESXi ana bilgisayarına bağlanmak için kullanılacak parola. Şu anda belirtmezseniz, komut yürütülürken sorulacaktır.|
|-Bağlantı noktası|(İsteğe bağlı) VCenter/ESXi ana bilgisayarına bağlanmak için bağlantı noktası numarası. Varsayılan bağlantı noktası 443'tür.|
|-Protokol| (İsteğe bağlı) vCenter’a bağlanmak için protokol 'http' veya 'https' olarak belirtildi. Varsayılan protokol https’dir.|
| -StorageAccountName | (İsteğe bağlı) Şirket içinden Azure’a veri çoğaltma için ulaşılabilir aktarım hızını bulmak için depolama hesabı adı. Araç, aktarım hızını hesaplamak için test verilerini bu depolama hesabına yükler. Depolama hesabı Genel amaçlı v1 (GPv1) türünde olmalıdır. |
| -StorageAccountKey | (İsteğe bağlı) Depolama hesabına erişmek için kullanılan depolama hesabı anahtarı. Azure portalı > Depolama hesapları > <*Depolama hesabı adı*> > Ayarlar > Erişim Anahtarları > Anahtar1 adımlarını izleyin. |
| -Ortam | (isteğe bağlı) Bu, hedef Azure depolama hesabı ortamınızdır. Şu üç değerden herhangi birini alabilir: AzureCloud,AzureUSGovernment, AzureChinaCloud. Varsayılan seçenek AzureCloud değeridir. Hedef Azure bölgeniz Azure ABD kamu veya Azure Çin 21Vianet olduğunda bu parametreyi kullanın. |


VM’lerinizin en az 7 günlük profilinin oluşturulması önerilir. Değişim sıklığı bir ay içinde değişiklik gösteriyorsa, bu sıklığın en yüksek seviyeye ulaştığı hafta sırasında profil oluşturmanız önerilir. En iyi yöntem, daha iyi öneriler almak için 31 günlük profil oluşturmaktır. Profil oluşturma süresi boyunca ASRDeploymentPlanner.exe çalışmaya devam eder. Araç, profil oluşturma süre girdisini gün cinsinden alır. Aracı hızla sınamak veya kavram kanıtı için, birkaç saatlik veya birkaç dakikalık profil oluşturabilirsiniz. İzin verilen en kısa profil oluşturma süresi 30 dakikadır.

Profil oluşturma sırasında, Site Recovery’nin çoğaltma sırasında yapılandırma sunucusu veya işlem sunucusundan Azure’a elde edilebileceği aktarım hızını bulmak için, isteğe bağlı olarak bir depolama hesabı adı ve anahtarı geçirebilirsiniz. Profil oluşturma sırasında depolama hesabı adı ve anahtarı geçirilmezse, araç ulaşılabilir aktarım hızını hesaplamaz.

Çeşitli sanal makine kümeleri için aracın birden çok örneğini çalıştırabilirsiniz. Sanal makine adlarının, profil kümelerinin hiçbirinde yinelenmediğinden emin olun. Örneğin, on sanal makine (VM1 - VM10) profili oluşturdunuz ve birkaç gün sonra beş sanal makine (VM11 - VM15) profili daha oluşturmak istiyorsunuz; bu durumda, ikinci sanal makine kümesi (VM11 - VM15) için başka bir komut satırı konsolundan aracı çalıştırabilirsiniz. Ancak, ikinci sanal makine kümesinde birinci profil oluşturma örneğinden herhangi bir sanal makine adı olmadığından veya ikinci çalıştırma için farklı bir çıktı dizini kullandığınızdan emin olun. Aracın iki örneği aynı sanal makinelerin profilini oluşturmak için kullanılır ve aynı çıktı dizinini kullanırsa, oluşturulan rapor hatalı olacaktır.

Araç, varsayılan olarak 1000 VM 'ye kadar rapor oluşturacak ve rapor oluşturacak şekilde yapılandırılmıştır. *ASRDeploymentPlanner.exe.config* dosyasındaki MaxVMsSupported anahtar değerini değiştirerek sınırı değiştirebilirsiniz.
```
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```
Varsayılan ayarlarla, örneğin 1500 VM profili oluşturmak için iki VMList.txt dosyası oluşturun. Biri 1000 VM, diğeri 500 VM ile listelenir. Azure Site Recovery Dağıtım Planlayıcısı iki örneğini çalıştırın, biri VMList1. txt ile diğeri VMList2. txt ile diğeri. Her iki VMList VM’lerin profil verilerini depolamak için aynı dizin yolunu kullanabilirsiniz.

Başta aracın raporu oluşturmak üzere çalıştırıldığı sunucunun RAM boyutu olmak üzere donanım yapılandırmasına göre, işlem yetersiz bellek nedeniyle başarısız olabilir. Donanımınız iyiyse, MaxVMsSupported değerini daha yüksek bir değerle değiştirebilirsiniz.  

Birden fazla vCenter sunucunuz varsa, profil oluşturma sırasında her bir vCenter sunucusu için bir ASRDeploymentPlanner örneğini çalıştırmanız gerekir.

Sanal makine yapılandırması, profil oluşturma işleminin başında bir kez yakalanır ve VMDetailList.xml adlı bir dosyada depolanır. Rapor oluşturulduğunda bu bilgiler kullanılır. Profil oluşturmanın başlangıcı ile bitişi arasında VM yapılandırmasında meydana gelen hiçbir değişiklik (örneğin, çekirdek, disk veya ağ arabirimi sayısının artması) yakalanmaz. Profili oluşturulmuş bir VM yapılandırması profil oluşturma sırasında değiştiyse, genel önizleme sürümünde rapor oluştururken en son VM bilgilerini almaya yönelik geçici çözüm aşağıda verilmiştir:

* VMdetailList.xml dosyasını yedekleyip, dosyayı geçerli konumundan silin.
* -User ve -Password bağımsız değişkenlerini rapor oluşturma sırasında geçirin.

Profil oluşturma komutu, profil oluşturma dizininde birkaç dosya oluşturur. Rapor oluşturmayı etkileyeceği için hiçbir dosyayı silmeyin.

#### <a name="example-1-profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Örnek 1: 30 günlük sanal makine profili oluşturma ve şirket içinden Azure’a aktarım hızını bulma
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  30  -User vCenterUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="example-2-profile-vms-for-15-days"></a>Örnek 2: 15 günlük sanal makine profili oluşturma

```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfDaysToProfile  15  -User vCenterUser1
```

#### <a name="example-3-profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>Örnek 3: Aracın hızlı bir testine yönelik 60 dakikalık VM profili oluşturma
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfMinutesToProfile 60  -User vCenterUser1
```

#### <a name="example-4-profile-vms-for-2-hours-for-a-proof-of-concept"></a>Örnek 4: Kavram kanıtı için VM’lerin 2 saatlik profilini oluşturma
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfHoursToProfile 2 -User vCenterUser1
```

>[!NOTE]
>
>* Aracın çalıştığı sunucu yeniden başlatılırsa veya kilitlenmişse ya da Ctrl+C tuşlarını kullanarak aracı kapatırsanız, profili oluşturulan veriler korunur. Ancak, profili oluşturulan verilen son 15 dakikası kaybedilebilir. Böyle bir durumda, sunucu yeniden başlatıldıktan sonra aracı profil oluşturma modunda yeniden çalıştırmanız gerekir.
>* Depolama hesabı adı ve anahtarı geçirildiğinde, araç profil oluşturma işleminin son adımında aktarım hızını ölçer. Profil oluşturma tamamlanmadan önce araç kapatılırsa, aktarım hızı hesaplanmaz. Raporu oluşturmadan önce aktarım hızını bulmak için, komut satırı konsolundan GetThroughput işlemini çalıştırabilirsiniz. Aksi takdirde, oluşturulan rapor aktarım hızı bilgilerini içermez.


## <a name="generate-report"></a>Rapor oluşturma
Araç, rapor çıktısı olarak tüm dağıtım önerilerini özetleyen makro özellikli bir Microsoft Excel dosyası (XLSM dosyası) oluşturur. Rapor `DeploymentPlannerReport_<unique numeric identifier>.xlsm` olarak adlandırılır ve belirtilen dizine yerleştirilir.

>[!NOTE]
>Rapor oluşturma, Excel 2013 veya üzeri bir Windows BILGISAYARı veya Windows Server gerektirir. Bu makinedeki ondalık sembol, maliyet tahminleri üretmek için "." olarak yapılandırılmalıdır. "," Öğesini ondalık sembol olarak ayarlamış olmanız durumunda, lütfen Denetim Masası 'nda "Tarih, saat veya sayı biçimlerini Değiştir" e gidin ve ondalık sembolünü "." olarak değiştirmek için "ek ayarlar" a gidin.

Profil oluşturma tamamlandıktan sonra, aracı rapor oluşturma modunda çalıştırabilirsiniz. Aşağıdaki tabloda, rapor oluşturma modunda çalışmaya yönelik zorunlu ve isteğe bağlı parametreler listelenmiştir.

`ASRDeploymentPlanner.exe -Operation GenerateReport /?`

|Parametre adı | Açıklama |
|-|-|
| -Operation | GenerateReport |
| -Server |  Raporu oluşturulacak profili oluşturulmuş sanal makinelerin bulunduğu vCenter/vSphere sunucusu tam etki alanı adı veya IP adresi (profil oluşturma sırasında kullandığınız adın veya IP adresinin aynısını kullanın). Profil oluşturma sırasında bir vCenter sunucusu kullandıysanız, rapor oluşturma için vSphere sunucusu kullanamazsınız.|
| -VMListFile | Raporun oluşturulacağı profili oluşturulmuş sanal makinelerin listesini içeren dosya. Dosya yolu mutlak veya göreli olabilir. Bu dosya her satırda bir VM adı veya IP adresi içermelidir. Dosyada belirtilen VM adları, vCenter sunucusu/vSphere ESXi ana bilgisayarındakilerle aynı olmalı ve profil oluşturma sırasında kullanılanla eşleşmelidir.|
|-Sanallaştırma|Sanallaştırma türünü (VMware veya Hyper-V) belirtin.|
| -Directory | (İsteğe bağlı) Profili oluşturulan verilerin (profil oluşturma sırasında oluşturulan dosyalar) depolandığı UNC veya yerel dizin yolu. Bu veriler, rapor oluşturmak için gereklidir. Bir ad belirtilmezse, 'ProfiledData' dizini kullanılır. |
| -GoalToCompleteIR | (İsteğe bağlı) Profili oluşturulan sanal makinelerin ilk çoğaltmasının tamamlanması gereken saat sayısı. Oluşturulan rapor, ilk çoğaltması belirtilen süre içinde tamamlanması gereken VM sayısını sağlar. Varsayılan değer 72 saattir. |
| -User | (İsteğe bağlı) vCenter/vSphere sunucusuna bağlanmak için kullanılacak kullanıcı adı. Bu ad, sanal makinelerin disk sayısı, çekirdek sayısı, ağ arabirimi sayısı gibi raporda kullanılacak en son yapılandırma bilgilerini getirmek için kullanılır. Bir ad belirtilmezse, profil oluşturma işleminin başında toplanan yapılandırma bilgileri kullanılır. |
| -Password | (İsteğe bağlı) vCenter sunucusuna/vSphere ESXi ana bilgisayarına bağlanmak için kullanılacak parola. Parola parametre olarak belirtilmezse, daha sonra komut yürütülürken sorulacaktır. |
|-Bağlantı noktası|(İsteğe bağlı) VCenter/ESXi ana bilgisayarına bağlanmak için bağlantı noktası numarası. Varsayılan bağlantı noktası 443'tür.|
|-Protokol|(İsteğe bağlı) vCenter’a bağlanmak için protokol 'http' veya 'https' olarak belirtildi. Varsayılan protokol https’dir.|
| -DesiredRPO | (İsteğe bağlı) Dakika cinsinden istenen kurtarma noktası hedefi. Varsayılan değer 15 dakikadır.|
| -Bandwidth | MB/sn cinsinden bant genişliği. Belirtilen bant genişliği için ulaşılabilecek RPO’yu hesaplamak için kullanılan parametre. |
| -StartDate | (İsteğe bağlı) AA-GG-YYYY:SS:DD (24 saat biçiminde) cinsinden başlangıç tarihi ve saati. *StartDate* değeri *EndDate* ile birlikte belirtilmelidir. StartDate belirtildiğinde, StartDate ile EndDate arasında toplanan profili oluşturulmuş veriler için rapor oluşturulur. |
| -EndDate | (İsteğe bağlı) AA-GG-YYYY:SS:DD (24 saat biçiminde) cinsinden bitiş tarihi ve saati. *EndDate* değeri *StartDate* ile birlikte belirtilmelidir. EndDate belirtildiğinde, StartDate ile EndDate arasında toplanan profili oluşturulmuş veriler için rapor oluşturulur. |
| -GrowthFactor | (İsteğe bağlı) Yüzde olarak ifade edilen büyüme faktörü. Varsayılan değer yüzde 30'dur. |
| -UseManagedDisks | (Optional) UseManagedDisks - Evet/Hayır. Varsayılan değer Evet’tir. Tek bir depolama hesabında bulunabilecek sanal makine sayısı, sanal makinelerin Yük devretme işleminin/Yük devretme testinin yönetilmeyen disk yerine yönetilen disk üzerinde yapılıp yapılmadığına bağlı olarak hesaplanır. |
|-SubscriptionId |(İsteğe bağlı) Abonelik GUID’si. Aboneliğiniz temelinde maliyet tahmini raporunu oluşturmanız gerektiğinde bu parametrenin gerekli olduğunu, aboneliğinizle ilişkili teklifi, **belirtilen para birimindeki belirli hedef Azure bölgeniz için de kullanabilirsiniz** .|
|-TargetRegion|(İsteğe bağlı) Çoğaltmanın hedeflendiği Azure bölgesi. Azure maliyetleri bölgelere göre değiştiğinden, belirli bir Azure bölgesini hedef alan bir rapor oluşturmak için bu parametreyi kullanın.<br>Varsayılan olarak WestUS2 veya en son kullanılan hedef bölge kullanılır.<br>[Desteklenen hedef bölgeler](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) listesine başvurun.|
|-OfferId|(İsteğe bağlı) Belirtilen abonelikle ilişkili teklif. Varsayılan olarak MS-AZR-0003P (Kullandıkça Öde) kullanılır.|
|-Currency|(İsteğe bağlı) Oluşturulan raporda maliyetin gösterileceği para birimi. Varsayılan olarak ABD doları ($) veya en son kullanılan para birimi kullanılır.<br>[Desteklenen para birimleri](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies) listesine başvurun.|

Araç, varsayılan olarak 1000 VM 'ye kadar rapor oluşturacak ve rapor oluşturacak şekilde yapılandırılmıştır. *ASRDeploymentPlanner.exe.config* dosyasındaki MaxVMsSupported anahtar değerini değiştirerek sınırı değiştirebilirsiniz.
```xml
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```

#### <a name="example-1-generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Örnek 1: Profili oluşturulan veriler yerel sürücüde olduğunda raporu varsayılan değerlerle oluşturma
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-2-generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Örnek 2: Profili oluşturulan veriler uzak bir sunucuda olduğunda rapor oluşturma
Uzak dizin üzerinde okuma/yazma erişiminiz olmalıdır.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-3-generate-a-report-with-a-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>Örnek 3: Belirli bir bant genişliği ve belirtilen süre içinde IR tamamlama hedefi ile rapor oluşturma
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -Bandwidth 100 -GoalToCompleteIR 24
```

#### <a name="example-4-generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Örnek 4: Yüzde 30’luk varsayılan değer yerine yüzde 5 büyüme faktörü ile rapor oluşturma
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="example-5-generate-a-report-with-a-subset-of-profiled-data"></a>Örnek 5: Profili oluşturulan verilerin bir alt kümesi ile rapor oluşturma
Örneğin, 30 günlük profili oluşturulmuş verilerinizin olduğunu ve raporu yalnızca 20 gün için oluşturduğunuzu varsayalım.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="example-6-generate-a-report-for-5-minute-rpo"></a>Örnek 6: 5 dakikalık RPO için rapor oluşturma
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

#### <a name="example-7-generate-a-report-for-south-india-azure-region-with-indian-rupee-and-specific-offer-id"></a>Örnek 7: Güney Hindistan Azure bölgesi için Hindistan Rupisi ve belirli teklif kimliğini içeren rapor oluşturma

Belirli bir para biriminde maliyet raporu oluşturmak için abonelik KIMLIĞININ gerekli olduğunu unutmayın.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware  -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```

## <a name="percentile-value-used-for-the-calculation"></a>Hesaplama için kullanılan yüzdelik değer
**Rapor oluşturulurken, profil oluşturma sırasında toplanan performans ölçümlerinin hangi varsayılan yüzdelik dilim değeri araç tarafından kullanılır?**

Aracın, tüm sanal makinelerin profili oluşturulurken toplanan okuma/yazma IOPS, yazma IOPS ve veri değişim sıklığı için varsayılan değeri yüzde 95’lik dilimdir. Bu ölçüm, VM’lerinizin geçici olaylar nedeniyle görebileceği %100’lük dilim artışının, hedef depolama hesabı ve kaynak bant genişliği gereksinimlerini belirlemek için kullanılmamasını sağlar. Örneğin, geçici olay günde bir kez gerçekleştirilen bir yedekleme işi, düzenli aralıklarla yapılan veritabanı dizini oluşturma veya analiz raporu oluşturma etkinliği ya da kısa süreli diğer benzer olaylar olabilir.

Yüzde 95’lik dilim değeri, gerçek iş yükü özelliklerinin gerçek bir resmini verir ve bu iş yükleri Azure üzerinde çalışırken en iyi performansı sağlar. Bu sayıyı değiştirmenizin gerekli olacağı düşünülmemektedir. Bu sayıyı değiştirirseniz (örn. yüzde 90’lık dilime), bu *ASRDeploymentPlanner.exe.config* yapılandırma dosyasını varsayılan klasörde güncelleştirebilir ve kaydederek var olan profili oluşturulmuş verilere ilişkin yeni bir rapor oluşturabilirsiniz.
```xml
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

* [Şirket İçi Özeti](site-recovery-vmware-deployment-planner-analyze-report.md#on-premises-summary)
* [Öneriler](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations)
* [VM<->Depolama Yerleşimi](site-recovery-vmware-deployment-planner-analyze-report.md#vm-storage-placement)
* [Uyumlu VM’ler](site-recovery-vmware-deployment-planner-analyze-report.md#compatible-vms)
* [Uyumsuz VM’ler](site-recovery-vmware-deployment-planner-analyze-report.md#incompatible-vms)
* [Maliyet Tahmini](site-recovery-vmware-deployment-planner-cost-estimation.md)

![Dağıtım planlayıcısı](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

## <a name="get-throughput"></a>Aktarım hızı alma

Site Recovery’nin çoğaltma sırasında şirket içinden Azure’a elde edebildiği aktarım hızını tahmin etmek için aracı GetThroughput modunda çalıştırın. Araç, üzerinde çalıştığı sunucudan aktarım hızını hesaplar. Bu sunucunun yapılandırma sunucusu boyutlandırma kılavuzunu temel alması idealdir. Site Recovery altyapı bileşenlerini şirket içinde zaten dağıttıysanız, aracı yapılandırma sunucusunda çalıştırın.

Bir komut satırı konsolu açın ve Site Recovery dağıtım planlama aracının klasörüne gidin. ASRDeploymentPlanner.exe dosyasını aşağıdaki parametrelerle çalıştırın.

`ASRDeploymentPlanner.exe -Operation GetThroughput /?`

|Parametre adı | Açıklama |
|-|-|
| -Operation | GetThroughput |
|-Sanallaştırma|Sanallaştırma türünü (VMware veya Hyper-V) belirtin.|
| -Directory | (İsteğe bağlı) Profili oluşturulan verilerin (profil oluşturma sırasında oluşturulan dosyalar) depolandığı UNC veya yerel dizin yolu. Bu veriler, rapor oluşturmak için gereklidir. Bir dizin adı belirtilmezse ‘ProfiledData’ dizini kullanılır. |
| -StorageAccountName | Şirket içinden Azure’a veri çoğaltma için kullanılan bant genişliğini bulmak için depolama hesabı adı. Araç, kullanılan bant genişliğini bulmak için test verilerini bu depolama hesabına yükler. Depolama hesabı Genel amaçlı v1 (GPv1) türünde olmalıdır.|
| -StorageAccountKey | Depolama hesabına erişmek için kullanılan depolama hesabı anahtarı. Azure portal > Depolama hesapları > <*Depolama hesabı adı*> > Ayarlar > Erişim Anahtarları > Anahtar1 (veya klasik depolama hesabı için birincil erişim anahtarı) öğesine gidin. |
| -VMListFile | Kullanılan bant genişliğini hesaplamak için profili oluşturulacak sanal makinelerin listesini içeren dosya. Dosya yolu mutlak veya göreli olabilir. Bu dosya her satırda bir VM adı/IP adresi içermelidir. Dosyada belirtilen sanal makine adı, vCenter sunucusu/vSphere ESXi ana bilgisayarındaki VM adıyla aynı olmalıdır.<br>Örneğin, VMList.txt dosyası aşağıdaki sanal makineleri içerir:<ul><li>VM_A</li><li>10.150.29.110</li><li>VM_B</li></ul>|
| -Ortam | (isteğe bağlı) Bu, hedef Azure depolama hesabı ortamınızdır. Şu üç değerden herhangi birini alabilir: AzureCloud,AzureUSGovernment, AzureChinaCloud. Varsayılan seçenek AzureCloud değeridir. Hedef Azure bölgeniz Azure ABD kamu veya Azure Çin 21Vianet olduğunda bu parametreyi kullanın. |

Araç, belirtilen dizinde 64 MB’lık birkaç asrvhdfile<#>.vhd (“#” sayıdır) dosyası oluşturur. Araç, aktarım hızını bulmak için dosyaları depolama hesabına yükler. Aktarım hızı ölçüldükten sonra araç tüm dosyaları depolama hesabından ve yerel sunucudan siler. Araç aktarım hızını hesaplarken herhangi bir nedenle sonlandırılırsa, dosyaları depolama alanından veya yerel sunucudan silmez. Bunları el ile silmeniz gerekir.

Aktarım hızı belirli bir zaman noktasında ölçülür ve diğer tüm faktörlerin aynı kalması koşuluyla Site Recovery’nin çoğaltma sırasında ulaşabileceği en yüksek aktarım hızıdır. Örneğin, herhangi bir uygulama aynı ağ üzerinde daha fazla bant genişliği tüketmeye başlarsa, çoğaltma sırasında gerçek aktarım hızı farklılık gösterir. Bir yapılandırma sunucusundan GetThroughput komutunu çalıştırıyorsanız, araç korunan sanal makineleri ve devam eden çoğaltmayı fark etmez. Korunan VM’ler yüksek veri değişim sıklığına sahip olduğunda GetThroughput işlemi çalıştırılırsa, ölçülen aktarım hızının sonucu farklı olur. Çeşitli zamanlarda hangi aktarım hızı düzeylerine ulaşılabileceğini anlamak için, profil oluşturma sırasında farklı zaman noktalarında aracın çalıştırılması önerilir. Raporda, araç son ölçülen aktarım hızını gösterir.

### <a name="example"></a>Örnek
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Directory  E:\vCenter1_ProfiledData -Virtualization VMware -VMListFile E:\vCenter1_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
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

## <a name="next-steps"></a>Sonraki adımlar
* [Oluşturulan raporu analiz etme](site-recovery-vmware-deployment-planner-analyze-report.md).
