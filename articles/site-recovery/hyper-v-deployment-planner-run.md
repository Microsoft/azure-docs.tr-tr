---
title: Hyper-V Dağıtım Planlayıcısı Azure Site Recovery içinde çalıştırma
description: Bu makalede, Azure 'da Hyper-V olağanüstü durum kurtarma için Azure Site Recovery Dağıtım Planlayıcısı nasıl çalıştırılacağı açıklanır.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: mayg
ms.openlocfilehash: ba1979c940d4a92b3d1a7a52a4f356b2896ece55
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082616"
---
# <a name="run-the-azure-site-recovery-deployment-planner-for-hyper-v-disaster-recovery-to-azure"></a>Azure 'da Hyper-V olağanüstü durum kurtarma için Azure Site Recovery dağıtım planlayıcısı 'nı çalıştırın

Aşağıdaki dört moddan birinde Site Recovery dağıtım planlayıcısı komut satırı aracını (ASRDeploymentPlanner. exe) çalıştırabilirsiniz: 
-   Sanal makine (VM) listesini al
-   [Profil](#profile-hyper-v-vms)
-   Rapor oluşturma
-   [Aktarım hızı alma](#get-throughput)

Öncelikle, bir veya birden fazla Hyper-V konağından VM’lerin listesini almak için aracı çalıştırın. Ardından VM veri değişim sıklığı ve IOPS toplamak için aracı profil oluşturma modunda çalıştırın. Ardından, ağ bant genişliği ve depolama gereksinimlerini bulmak üzere raporu oluşturmak için aracı çalıştırın.

## <a name="get-the-vm-list-for-profiling-hyper-v-vms"></a>Hyper-V VM’lerinin profilini oluşturmak için VM listesini alma
İlk olarak, profili oluşturulacak sanal makinelerin bir listesi gerekir. Birden çok Hyper-V konağında bulunan VM’lerin listesini tek bir komutta oluşturmak için Dağıtım planlayıcısı aracının GetVMList modunu kullanın. Tam listeyi oluşturduktan sonra, profilini oluşturmak istemediğiniz VM’leri çıkış dosyasından kaldırabilirsiniz. Ardından profil oluşturma, rapor oluşturma ve aktarım hızını alma gibi diğer tüm işlemler için bu çıktı dosyasını kullanın.

Aracı bir Hyper-V kümesine, tek başına bir Hyper-V konağına veya ikisinin birleşimine yönlendirerek VM listesini oluşturabilirsiniz.

### <a name="command-line-parameters"></a>Komut satırı parametreleri
Aşağıdaki tabloda, aracın GetVMList modunda çalışması için gereken zorunlu ve isteğe bağlı parametreler listelenmiştir. 
```
ASRDeploymentPlanner.exe -Operation GetVMList /?
```

| Parametre adı | Açıklama |
|---|---|
| -Operation | GetVMList |
| -User | Hyper-V konağı veya Hyper-V kümesine bağlanmak için gereken kullanıcı adı. Kullanıcının yönetici erişimi olmalıdır.|
| -ServerListFile | Profili oluşturulacak VM’leri içeren sunucuların listesinin bulunduğu dosya. Dosya yolu mutlak veya göreli olabilir. Bu dosya her satırda aşağıdakilerden birini içermelidir:<ul><li>Hyper-V konak adı veya IP adresi</li><li>Hyper-V küme adı veya IP adresi</li></ul><br>**Örnek:** ServerList.txt dosyası aşağıdaki sunucuları içerir:<ul><li>Host_1</li><li>10.8.59.27</li><li>Cluster_1</li><li>Host_2</li>|
| -Directory|(İsteğe bağlı) Bu işlem sırasında oluşturulan verileri depolamak için evrensel adlandırma kuralı (UNC) veya yerel dizin yolu. Bir ad belirtilmemişse, varsayılan dizin olarak geçerli yolun altındaki “ProfiledData” adlı dizin kullanılır.|
|-OutputFile| Seçim Hyper-V sunucularından alınan VM 'lerin listesini içeren dosya kaydedilir. Bir ad belirtilmezse, ayrıntılar VMList.txt dosyasında depolanır.  Profili gerekmeyen VM'ler kaldırdıktan sonra profil oluşturmayı başlatmak için bu dosyayı kullanın.|
|-Password|(İsteğe bağlı) Hyper-V konağına bağlanmak için gereken parola. Bunu bir parametre olarak belirtmezseniz komutu çalıştırdığında belirtmeniz istenir.|

### <a name="getvmlist-discovery"></a>GetVMList bulma

- **Hyper-V kümesi**: Sunucunun liste dosyasında Hyper-V küme adı belirtildiğinde, araç kümenin tüm Hyper-V düğümlerinin bulur ve her Hyper-V konağında mevcut olan VM'leri alır.
**Hyper-V konağı**: Hyper-V konak adı verildiğinde, araç öncelikle bu adın bir kümeye ait olup olmadığını denetler. Değerin evet olması durumunda araç kümeye ait düğümleri getirir. Ardından sanal makineleri her Hyper-V ana bilgisayarından alır. 

Dilerseniz profilini el ile oluşturmak istediğiniz sanal makinelerin kolay adlarını veya IP adreslerini bir dosyada listeleyebilirsiniz.

Çıktı dosyasını Not Defteri’nde açın ve sonra profilini oluşturmak istediğiniz tüm VM’lerin adlarını başka bir dosyaya (örneğin, ProfileVMList.txt) kopyalayın. Satır başına bir VM adı kullanın. Bu dosya, profil oluşturma, rapor oluşturma ve aktarım hızını alma gibi diğer tüm işlemler için aracın - VMListFile parametresinde girdi olarak kullanılır.

### <a name="examples"></a>Örnekler

#### <a name="store-the-list-of-vms-in-a-file"></a>VM'lerin listesini bir dosyada depolama
```
ASRDeploymentPlanner.exe -Operation GetVMlist -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1 -OutputFile "E:\Hyper-V_ProfiledData\VMListFile.txt"
```

#### <a name="store-the-list-of-vms-at-the-default-location--directory-path"></a>VM'lerin listesini varsayılan konumda (-Dizin yolu) depolama
```
ASRDeploymentPlanner.exe -Operation GetVMList -Directory "E:\Hyper-V_ProfiledData" -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1
```

## <a name="profile-hyper-v-vms"></a>Hyper-V VM’lerinin profilini oluşturma
Profil oluşturma modunda dağıtım planlayıcısı aracı, sanal makineye ilişkin performans verilerini toplamak için Hyper-V konaklarının her birine bağlanır. 

Profil oluşturma işlemi sırasında üretim VM’leri ile doğrudan bağlantı kurulmadığı için bu VM’lerin performansı etkilenmez. Tüm performans verileri Hyper-V konağından toplanır.

Araç, oluşturulan profilin doğru olmasını sağlamak için Hyper-V konağını 15 saniyede bir sorgular. Her dakikaya ait performans sayaç verilerinin ortalamasını saklar.

Araç, kümedeki bir düğümden başka bir düğüme VM geçişini ve bir konak içindeki depolama geçişini sorunsuz bir şekilde işler.

### <a name="getting-the-vm-list-to-profile"></a>Profili oluşturulacak VM listesini alma
Profili oluşturulacak VM 'lerin listesini oluşturmak için GetVMList işlemine bakın.

Profili oluşturulacak sanal makinelerin listesini oluşturduktan sonra, aracı profil oluşturma modunda çalıştırabilirsiniz. 

### <a name="command-line-parameters"></a>Komut satırı parametreleri
Aşağıdaki tabloda, aracın profil oluşturma modunda çalışması için gereken zorunlu ve isteğe bağlı parametreler listelenmiştir. VMware’den Azure’a ve Hyper-V’den Azure’a geçiş senaryoları için bu araç yaygın olarak kullanılır. Bu parametreler Hyper-V’de kullanılabilir. 
```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```

| Parametre adı | Açıklama |
|---|---|
| -Operation | StartProfiling |
| -User | Hyper-V konağı veya Hyper-V kümesine bağlanmak için gereken kullanıcı adı. Kullanıcının yönetici erişimi olmalıdır.|
| -VMListFile | Profili oluşturulacak VM’lerin listesini içeren dosya. Dosya yolu mutlak veya göreli olabilir. Hyper-V için, GetVMList işleminin çıkış dosyası bu dosyadır. El ile hazırlıyorsanız, dosyada bir sunucu adı ya da IP adresi ve sonra VM adı (her satırı \ ile ayrılan) bulunmalıdır. Dosyada belirtilen VM adı, Hyper-V konağındaki VM adıyla aynı olmalıdır.<br><br>**Örnek:** VMList.txt dosyası aşağıdaki sanal makineleri içerir:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-NoOfMinutesToProfile|Profil oluşturmanın çalıştırılacağı dakika sayısı. En az 30 dakika olmalıdır.|
|-NoOfHoursToProfile|Profil oluşturmanın çalıştırılacağı saat sayısı.|
|-NoOfDaysToProfile |Profil oluşturmanın çalıştırılacağı gün sayısı. Profil oluşturmayı 7 günden uzun bir süre çalıştırmanız önerilir. Bu, ortamınızda belirtilen dönem içindeki iş yükü deseninin doğru bir öneri sağlayacak şekilde gözlemlenip kullanılmasını sağlamanıza yardımcı olur.|
|-Sanallaştırma|Sanallaştırma türü (VMware veya Hyper-V).|
|-Directory|(İsteğe Bağlı) Profil oluşturma sırasında oluşturulan profil oluşturma verilerini depolamak için UNC veya yerel dizin yolu. Bir ad belirtilmemişse, geçerli yol altındaki ProfiledData adlı dizin varsayılan dizin olarak kullanılır.|
|-Password|(İsteğe bağlı) Hyper-V konağına bağlanmak için gereken parola. Bunu bir parametre olarak belirtmezseniz komutu çalıştırdığında belirtmeniz istenir.|
|-StorageAccountName|(İsteğe bağlı) Şirket içinden Azure’a veri çoğaltma için ulaşılabilir aktarım hızını bulmak için depolama hesabı adı. Araç, aktarım hızını hesaplamak için test verilerini bu depolama hesabına yükler. Depolama hesabı Genel amaçlı v1 (GPv1) türünde olmalıdır.|
|-StorageAccountKey|(İsteğe bağlı) Depolama hesabına erişmek için kullanılan anahtar. Azure portalı > **Depolama hesapları** > *Depolama hesabı adı* > **Ayarlar** > **Erişim Anahtarları** > **Anahtar1** (veya klasik depolama hesabı için birincil erişim anahtarı) seçeneğine gidin.|
|-Ortam|(İsteğe bağlı) Azure depolama hesabı için hedef ortamınız. Şu üç değerden herhangi birini alabilir: AzureCloud, AzureUSGovernment veya AzureChinaCloud. Varsayılan seçenek AzureCloud değeridir. Hedef bölgeniz Azure ABD kamu veya Azure Çin 21Vianet olduğunda bu parametreyi kullanın.|

VM’lerinizin en az 7 günlük profilinin oluşturulması önerilir. Değişim sıklığı bir ay içinde değişiklik gösteriyorsa, bu sıklığın en yüksek seviyeye ulaştığı hafta sırasında profil oluşturmanız önerilir. En iyi yöntem, daha iyi bir öneri almak için 31 günlük profil oluşturmaktır. 

Profil oluşturma süresi boyunca ASRDeploymentPlanner.exe çalışmaya devam eder. Araç, profil oluşturma süre girdisini gün cinsinden alır. Aracı hızla sınamak veya kavram kanıtı için, birkaç saatlik veya birkaç dakikalık bir profil oluşturabilirsiniz. İzin verilen en kısa profil oluşturma süresi 30 dakikadır. 

Profil oluşturma sırasında, Azure Site Recovery’nin çoğaltma sırasında Hyper-V sunucusundan Azure’a elde edilebileceği aktarım hızını bulmak için, isteğe bağlı olarak bir depolama hesabı adı ve anahtarı geçirebilirsiniz. Profil oluşturma sırasında depolama hesabı adı ve anahtarı geçirilmezse, araç ulaşılabilir aktarım hızını hesaplamaz.

Çeşitli sanal makine kümeleri için aracın birden çok örneğini çalıştırabilirsiniz. Sanal makine adlarının, profil kümelerinin hiçbirinde yinelenmediğinden emin olun. Örneğin, 10 VM’nin (VM1’den VM10’a kadar) profilini oluşturduğunuzu varsayalım. Birkaç gün sonra 5 VM’nin (VM11’den VM15’e kadar) daha profilini oluşturmak istiyorsunuz diyelim. İkinci VM kümesi (VM11’den VM15’e kadar) için aracı başka bir komut satırı konsolundan çalıştırabilirsiniz. 

İkinci sanal makine kümesinde birinci profil oluşturma örneğinden herhangi bir VM adı olmadığından veya ikinci çalıştırma için farklı bir çıktı dizini kullandığınızdan emin olun. Aracın iki örneği aynı sanal makinelerin profilini oluşturmak için kullanılır ve aynı çıktı dizinini kullanırsa, oluşturulan rapor hatalı olacaktır. 

Araç, varsayılan olarak 1.000 VM'ye kadar profil ve rapor oluşturmak üzere yapılandırılmıştır. ASRDeploymentPlanner.exe.config dosyasındaki MaxVMsSupported anahtar değerini değiştirerek sınırı değiştirebilirsiniz.
```
<!-- Maximum number of VMs supported-->
<add key="MaxVmsSupported" value="1000"/>
```
Varsayılan ayarlarla, örneğin 1.500 VM profili oluşturmak için iki VMList.txt dosyası oluşturun. Biri 1.000 VM, diğeri 500 VM içerir. Azure Site Recovery dağıtım planlayıcısının iki örneğinden birini VMList1.txt, diğerini VMList2.txt ile çalıştırın. Her iki VMList VM’lerin profil verilerini depolamak için aynı dizin yolunu kullanabilirsiniz. 

Aracın raporu oluşturmak üzere çalıştırıldığı sunucunun başta RAM boyutu olmak üzere donanım yapılandırmasına göre, işlem yetersiz bellek nedeniyle başarısız olabilir. Donanımınız iyiyse, MaxVMsSupported değerini daha istediğiniz yüksek bir değerle değiştirebilirsiniz.  

Sanal makine yapılandırması, profil oluşturma işleminin başında bir kez yakalanır ve VMDetailList.xml adlı bir dosyada depolanır. Rapor oluşturulduğunda bu bilgiler kullanılır. Profil oluşturmanın başlangıcı ile bitişi arasında VM yapılandırmasında meydana gelen hiçbir değişiklik (örneğin, çekirdek, disk veya ağ arabirimi sayısının artması) yakalanmaz. Profili oluşturulmuş bir VM yapılandırması profil oluşturma sırasında değiştiyse, raporu oluştururken en son VM bilgilerini almaya yönelik geçici çözüm aşağıda verilmiştir:

* VMdetailList.xml dosyasını yedekleyip, dosyayı geçerli konumundan silin.
* -User ve -Password bağımsız değişkenlerini rapor oluşturma sırasında geçirin.

Profil oluşturma komutu, profil oluşturma dizininde birkaç dosya oluşturur. Rapor oluşturmayı etkileyeceği için hiçbir dosyayı silmeyin.

### <a name="examples"></a>Örnekler

#### <a name="profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>30 günlük sanal makine profili oluşturma ve şirket içinden Azure’a aktarım hızını bulma
```
ASRDeploymentPlanner.exe -Operation StartProfiling -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfDaysToProfile 30 -User Contoso\HyperVUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="profile-vms-for-15-days"></a>15 günlük sanal makine profili oluşturma
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\vCenter1_ProfiledData\ProfileVMList1.txt"  -NoOfDaysToProfile  15  -User contoso\HypreVUser1
```

#### <a name="profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>Aracın hızlı bir testine yönelik 60 dakikalık VM profili oluşturma
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfMinutesToProfile 60 -User Contoso\HyperVUser1
```

#### <a name="profile-vms-for-2-hours-for-a-proof-of-concept"></a>Kavram kanıtı için VM’lerin 2 saatlik profilini oluşturma
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfHoursToProfile 2 -User Contoso\HyperVUser1
```

### <a name="considerations-for-profiling"></a>Profil oluşturma ile ilgili önemli noktalar

Aracın çalıştığı sunucu yeniden başlatılırsa veya kilitlenmişse ya da Ctrl+C tuşlarını kullanarak aracı kapatırsanız, profili oluşturulan veriler korunur. Ancak, profili oluşturulan verilen son 15 dakikası kaybedilebilir. Böyle durumlarda, sunucu yeniden başlatıldıktan sonra aracı profil oluşturma modunda yeniden çalıştırmanız gerekir.

Depolama hesabı adı ve anahtarı geçirildiğinde, araç profil oluşturma işleminin son adımında aktarım hızını ölçer. Profil oluşturma tamamlanmadan önce araç kapatılırsa, aktarım hızı hesaplanmaz. Raporu oluşturmadan önce aktarım hızını bulmak için, komut satırı konsolundan GetThroughput işlemini çalıştırabilirsiniz. Aksi takdirde, oluşturulan rapor aktarım hızı bilgilerini içermez.

Azure Site Recovery, Iscsı ve geçiş diskleri olan VM 'Leri desteklemez. Araç, VM 'lere eklenmiş Iscsı ve geçiş disklerini algılayamaz ve profil ekleyemez.

## <a name="generate-a-report"></a>Rapor oluşturma
Araç, rapor çıktısı olarak makro özellikli bir Microsoft Excel dosyası (XLSM dosyası) oluşturur. Bu dosya tüm dağıtım önerilerini özetler. Rapor, DeploymentPlannerReport_*benzersiz sayısal tanımlayıcı*.xlsm olarak adlandırılıp belirtilen dizine yerleştirilir.

Profil oluşturma tamamlandıktan sonra, aracı rapor oluşturma modunda çalıştırabilirsiniz. 

### <a name="command-line-parameters"></a>Komut satırı parametreleri
Aşağıdaki tabloda, rapor oluşturma modunda çalışmaya yönelik zorunlu ve isteğe bağlı parametreler listelenmiştir. VMware’den Azure’a ve Hyper-V’den Azure’a geçiş için bu araç yaygın olarak kullanılır. Aşağıdaki parametreler Hyper-V’de kullanılabilir.
```
ASRDeploymentPlanner.exe -Operation GenerateReport /?
```

| Parametre adı | Açıklama |
|---|---|
| -Operation | GenerateReport |
|-VMListFile | Raporun oluşturulacağı profili oluşturulmuş sanal makinelerin listesini içeren dosya. Dosya yolu mutlak veya göreli olabilir. Hyper-V için, GetVMList işleminin çıkış dosyası bu dosyadır. El ile hazırlıyorsanız, dosyada bir sunucu adı ya da IP adresi ve sonra VM adı (her satırı \ ile ayrılan) bulunmalıdır. Dosyada belirtilen VM adı, Hyper-V konağındaki VM adıyla aynı olmalıdır.<br><br>**Örnek:** VMList.txt dosyası aşağıdaki sanal makineleri içerir:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Sanallaştırma|Sanallaştırma türü (VMware veya Hyper-V).|
|-Directory|(İsteğe bağlı) Profili oluşturulan verilerin (profil oluşturma sırasında oluşturulan dosyalar) depolandığı UNC veya yerel dizin yolu. Bu veriler, rapor oluşturmak için gereklidir. Bir ad belirtilmemişse, geçerli yol altındaki ProfiledData adlı dizin varsayılan dizin olarak kullanılır.|
| -User | (İsteğe Bağlı) Hyper-V konağı veya Hyper-V kümesine bağlanmak için gereken kullanıcı adı. Kullanıcının yönetici erişimi olmalıdır. Kullanıcı ve parola, raporda kullanılacak en son yapılandırma bilgilerini (VM’lerin disk sayısı, çekirdek sayısı ve NIC sayısı gibi) getirmek için kullanılır. Bu değer belirtilmezse, profil oluşturma sırasında toplanan yapılandırma bilgileri kullanılır.|
|-Password|(İsteğe bağlı) Hyper-V konağına bağlanmak için gereken parola. Bunu bir parametre olarak belirtmezseniz komutu çalıştırdığında belirtmeniz istenir.|
| -DesiredRPO | (İsteğe bağlı) Dakika cinsinden istenen kurtarma noktası (RPO) hedefi. Varsayılan değer 15 dakikadır.|
| -Bandwidth | (İsteğe bağlı) Saniye başına megabit cinsinden bant genişliği. Belirtilen bant genişliği için ulaşılabilecek RPO’yu hesaplamak için bu parametreyi kullanın. |
| -StartDate | (İsteğe bağlı) AA-GG-YYYY:SS:DD (24 saat) biçiminde başlangıç tarihi ve saati. EndDate ile birlikte StartDate değeri belirtilmelidir. StartDate belirtildiğinde, StartDate ile EndDate arasında toplanan profili oluşturulmuş veriler için rapor oluşturulur. |
| -EndDate | (İsteğe bağlı) AA-GG-YYYY:SS:DD (24 saat) biçiminde bitiş tarihi ve saati. StartDate ile birlikte EndDate değeri belirtilmelidir. EndDate belirtildiğinde, StartDate ile EndDate arasında toplanan profili oluşturulmuş veriler için rapor oluşturulur. |
| -GrowthFactor | (İsteğe bağlı) Yüzde olarak ifade edilen büyüme faktörü. Varsayılan değer yüzde 30'dur. |
| -UseManagedDisks | (İsteğe bağlı) UseManagedDisks: Evet/Hayır. Varsayılan değer Evet’tir. Tek bir depolama hesabında bulunabilecek sanal makine sayısı, sanal makinelerin yük devretme işleminin/yük devretme testinin yönetilmeyen disk yerine yönetilen disk üzerinde yapılıp yapılmadığına bağlı olarak hesaplanır. |
|-SubscriptionId |(İsteğe bağlı) Abonelik GUID’si. Aboneliğinizin son fiyatına, aboneliğinizle ilişkili teklife ve hedef Azure bölgenize dayalı olarak ve belirli bir para birimini kullanarak maliyet tahmini oluşturmak için bu parametreyi kullanın.|
|-TargetRegion|(İsteğe bağlı) Çoğaltmanın hedeflendiği Azure bölgesi. Azure maliyetleri bölgelere göre değiştiğinden, belirli bir Azure bölgesini hedef alan bir rapor oluşturmak için bu parametreyi kullanın. Varsayılan olarak WestUS2 veya en son kullanılan hedef bölge kullanılır. [Desteklenen hedef bölgeler](hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) listesine başvurun.|
|-OfferId|(İsteğe bağlı) Abonelikle ilişkili teklif. Varsayılan olarak MS-AZR-0003P (Kullandıkça Öde) kullanılır.|
|-Currency|(İsteğe bağlı) Oluşturulan raporda maliyetin gösterileceği para birimi. Varsayılan olarak ABD doları ($) veya en son kullanılan para birimi kullanılır. [Desteklenen para birimleri](hyper-v-deployment-planner-cost-estimation.md#supported-currencies) listesine başvurun.|

Araç, varsayılan olarak 1.000 VM'ye kadar profil ve rapor oluşturmak üzere yapılandırılmıştır. ASRDeploymentPlanner.exe.config dosyasındaki MaxVMsSupported anahtar değerini değiştirerek sınırı değiştirebilirsiniz.
```
<!-- Maximum number of VMs supported-->
<add key="MaxVmsSupported" value="1000"/>
```

### <a name="examples"></a>Örnekler
#### <a name="generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Profili oluşturulan veriler yerel sürücüde olduğunda raporu varsayılan değerlerle oluşturma
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"
```

#### <a name="generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Profili oluşturulan veriler uzak bir sunucuda olduğunda rapor oluşturma
Uzak dizin üzerinde okuma/yazma erişiminiz olmalıdır.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "\\PS1-W2K12R2\Hyper-V_ProfiledData" -VMListFile "\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt"
```

#### <a name="generate-a-report-with-a-specific-bandwidth-that-you-will-provision-for-the-replication"></a>Çoğaltma için sağlayacağınız belirli bir bant genişliğine sahip bir rapor oluşturma
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -Bandwidth 100
```

#### <a name="generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Varsayılan yüzde 30 değeri yerine yüzde 5 büyüme faktörü ile rapor oluşturma 
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -GrowthFactor 5
```

#### <a name="generate-a-report-with-a-subset-of-profiled-data"></a>Profili oluşturulan verilerin bir alt kümesi ile rapor oluşturma
Örneğin, 30 günlük profili oluşturulmuş verilerinizin olduğunu ve raporu yalnızca 20 gün için oluşturduğunuzu varsayalım.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="generate-a-report-for-a-5-minute-rpo"></a>5 dakikalık bir RPO için rapor oluşturma
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -DesiredRPO 5
```

#### <a name="generate-a-report-for-the-south-india-azure-region-with-indian-rupee-and-a-specific-offer-id"></a>Güney Hindistan Azure bölgesi için Hindistan Rupisi ve belirli bir teklif kimliğini içeren rapor oluşturma
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```


### <a name="percentile-value-used-for-the-calculation"></a>Hesaplama için kullanılan yüzdelik değer
Araç bir rapor oluşturduğunda okuma/yazma IOPS, yazma IOPS ve veri değişim sıklığı için varsayılan olarak 95 yüzdelik değerini alır. Tüm VM’lerin profili oluşturulduğu sırada bu değerler toplanır. Bu ölçüm, VM’lerinizin geçici olaylar nedeniyle görebileceği 100’lük dilim artışının, hedef depolama hesabı ve kaynak bant genişliği gereksinimlerini belirlemek için kullanılmamasını sağlar. Örneğin, geçici olay günde bir kez gerçekleştirilen bir yedekleme işi, düzenli aralıklarla yapılan veritabanı dizini oluşturma veya analiz raporu oluşturma etkinliği ya da kısa süreli, belirli bir noktada gerçekleşen başka bir olay olabilir.

Yüzdelik dilim olarak 95 değerinin kullanılması, gerçek iş yükü özelliklerinin gerçek bir resmini sunar ve bu iş yükleri Azure üzerinde çalışırken en iyi performansı sağlar. Bu sayıyı değiştirmenizin gerekli olacağı düşünülmemektedir. Bu sayıyı değiştirirseniz (örn. yüzde 90’lık dilime), bu ASRDeploymentPlanner.exe.config yapılandırma dosyasını varsayılan klasörde güncelleştirebilir ve kaydederek var olan profili oluşturulmuş verilere ilişkin yeni bir rapor oluşturabilirsiniz.
```
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

### <a name="considerations-for-growth-factor"></a>Büyüme faktörü konusunda dikkat edilmesi gerekenler
Zaman içindeki kullanımın olası artışı varsayılarak, iş yükü özelliklerinizde büyümenin hesaba katılması önemlidir. Koruma uygulandıktan sonra iş yükü özellikleriniz değişirse, korumayı devre dışı bırakıp yeniden etkinleştirmeden farklı bir depolama hesabına geçiş yapamazsınız.

Örneğin, bugün sanal makinenizin standart bir depolama çoğaltma hesabına uygun olduğunu düşünelim. Önümüzdeki üç ay üzerinde aşağıdaki değişikliklerden bazıları gerçekleştirilebilir:

1. VM üzerinde çalışan uygulamanın kullanıcı sayısı artar.
2. VM üzerinde artan değişim sıklığı, Azure Site Recovery çoğaltmasının gerçekleştirilebilmesi için VM’nizin premium depolamaya geçmesini gerektirir.
3. Bir premium depolama hesabında korumayı devre dışı bırakıp yeniden etkinleştirmeniz gerekir.

Büyümeyi dağıtım planlaması sırasında planlamanız önerilir. Varsayılan değer yüzde 30 olsa da uygulamanızın kullanım düzenini ve büyüme beklentilerini en iyi siz bilirsiniz. Bir rapor oluştururken bu değeri uygun bir biçimde değiştirebilirsiniz. Ayrıca, profili oluşturulan verilerin aynısıyla çeşitli büyüme faktörlerine sahip birden çok rapor oluşturabilirsiniz. Daha sonra, hangi hedef depolama ve kaynak bant genişliği önerilerinin size uygun olduğunu belirleyebilirsiniz. 

Oluşturulan Microsoft Excel raporu aşağıdaki bilgileri içerir:

* [Şirket içi özet](hyper-v-deployment-planner-analyze-report.md#on-premises-summary)
* [Öneriler](hyper-v-deployment-planner-analyze-report.md#recommendations)
* [VM depolama yerleşimi](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation)
* [Uyumlu VM’ler](hyper-v-deployment-planner-analyze-report.md#compatible-vms)
* [Uyumsuz VM’ler](hyper-v-deployment-planner-analyze-report.md#incompatible-vms)
* [Şirket içi depolama gereksinimi](hyper-v-deployment-planner-analyze-report.md#on-premises-storage-requirement)
* [IR toplu işlemesi](hyper-v-deployment-planner-analyze-report.md#initial-replication-batching)
* [Maliyet tahmini](hyper-v-deployment-planner-cost-estimation.md)

![Dağıtım planlayıcısı raporu](media/hyper-v-deployment-planner-run/deployment-planner-report-h2a.png)


## <a name="get-throughput"></a>Aktarım hızı alma
Azure Site Recovery’nin çoğaltma sırasında şirket içinden Azure’a elde edebildiği aktarım hızını tahmin etmek için aracı GetThroughput modunda çalıştırın. Araç, üzerinde çalıştığı sunucudan aktarım hızını hesaplar. İdeal olarak bu sunucu, korunacak olan VM'lerin bulunduğu Hyper-V sunucusudur. 

### <a name="command-line-parameters"></a>Komut satırı parametreleri 
Bir komut satırı konsolu açın ve Azure Site Recovery dağıtım planlama aracının klasörüne gidin. ASRDeploymentPlanner.exe dosyasını aşağıdaki parametrelerle çalıştırın.
```
ASRDeploymentPlanner.exe -Operation GetThroughput /?
```

 Parametre adı | Açıklama |
|---|---|
| -Operation | GetThroughput |
|-Sanallaştırma|Sanallaştırma türü (VMware veya Hyper-V).|
|-Directory|(İsteğe bağlı) Profili oluşturulan verilerin (profil oluşturma sırasında oluşturulan dosyalar) depolandığı UNC veya yerel dizin yolu. Bu veriler, rapor oluşturmak için gereklidir. Bir ad belirtilmemişse, geçerli yol altındaki ProfiledData adlı dizin varsayılan dizin olarak kullanılır.|
| -StorageAccountName | Şirket içinden Azure’a veri çoğaltma için kullanılan bant genişliğini bulmak için depolama hesabı adı. Araç, kullanılan bant genişliğini bulmak için test verilerini bu depolama hesabına yükler. Depolama hesabı Genel amaçlı v1 (GPv1) türünde olmalıdır.|
| -StorageAccountKey | Depolama hesabına erişmek için kullanılan depolama hesabı anahtarı. Azure portalı > **Depolama hesapları** > *depolama hesabı adı* > **Ayarlar** > **Erişim Anahtarları** > **Anahtar1** seçeneğine gidin.|
| -VMListFile | Kullanılan bant genişliğini hesaplamak için profili oluşturulacak sanal makinelerin listesini içeren dosya. Dosya yolu mutlak veya göreli olabilir. Hyper-V için, GetVMList işleminin çıkış dosyası bu dosyadır. El ile hazırlıyorsanız, dosyada bir sunucu adı ya da IP adresi ve sonra VM adı (her satırı \ ile ayrılan) bulunmalıdır. Dosyada belirtilen VM adı, Hyper-V konağındaki VM adıyla aynı olmalıdır.<br><br>**Örnek:** VMList.txt dosyası aşağıdaki sanal makineleri içerir:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Ortam|(İsteğe bağlı) Azure depolama hesabı için hedef ortamınız. Şu üç değerden herhangi birini alabilir: AzureCloud, AzureUSGovernment veya AzureChinaCloud. Varsayılan seçenek AzureCloud değeridir. Hedef Azure bölgeniz Azure ABD kamu veya Azure Çin 21Vianet olduğunda bu parametreyi kullanın.|

### <a name="example"></a>Örnek
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

### <a name="throughput-considerations"></a>Aktarım hızı konusunda dikkat edilmesi gerekenler

Araç, belirtilen dizinde 64 MB’lık birkaç asrvhdfile*number*.vhd (*number* dosya sayısıdır) dosyası oluşturur. Araç, aktarım hızını bulmak için dosyaları depolama hesabına yükler. Aktarım hızı ölçüldükten sonra araç tüm dosyaları depolama hesabından ve yerel sunucudan siler. Araç aktarım hızını hesaplarken herhangi bir nedenle sonlandırılırsa, dosyaları depolama hesabından veya yerel sunucudan silmez. Bunları el ile silmeniz gerekir.

Aktarım hızı zaman içinde belirli bir noktada çözülür. Diğer tüm faktörler aynı kalırsa Azure Site Recovery’nin çoğaltma sırasında ulaşabileceği en yüksek aktarım hızıdır. Örneğin, herhangi bir uygulama aynı ağ üzerinde daha fazla bant genişliği tüketmeye başlarsa, çoğaltma sırasında gerçek aktarım hızı farklılık gösterir. Korunan VM’ler yüksek veri değişim sıklığına sahip olduğunda GetThroughput işlemi çalıştırılırsa, ölçülen aktarım hızının sonucu farklı olur. 

Çeşitli zamanlarda hangi aktarım hızı düzeylerine ulaşılabileceğini anlamak için, profil oluşturma sırasında aracın farklı zaman noktalarında çalıştırılması önerilir. Raporda, araç son ölçülen aktarım hızını gösterir.

> [!NOTE]
> Aracı, Hyper-V sunucusu ile aynı depolama ve CPU özelliklerine sahip bir sunucuda çalıştırın.

Çoğaltma için, önerilen bant genişliğini sürenin yüzde 100’ünü karşılayacak şekilde ayarlayın. Doğru bant genişliğini sağladıktan sonra araç tarafından ulaşıldığı bildirilen aktarım hızında herhangi bir artış görmezseniz aşağıdakileri yapın:

1. Azure Site Recovery aktarım hızını sınırlayan herhangi bir ağ Hizmet Kalitesi (QoS) sorunu olup olmadığını denetleyin.
2. Ağ gecikme süresini en aza indirmek için, Azure Site Recovery kasanızın desteklenen en yakın fiziksel Microsoft Azure bölgesinde olup olmadığını denetleyin.
3. Donanımı iyileştirip iyileştiremeyeceğinizi (örneğin, HDD’den SSD’ye) belirlemek için yerel depolama özelliklerini denetleyin.

    
## <a name="next-steps"></a>Sonraki adımlar
* [Oluşturulan raporu analiz etme](hyper-v-deployment-planner-analyze-report.md)
