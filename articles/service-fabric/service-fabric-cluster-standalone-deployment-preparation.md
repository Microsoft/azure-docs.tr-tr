---
title: Tek başına küme dağıtımı hazırlığı
description: Ortamı hazırlamaya ve küme yapılandırması oluşturmaya ilişkin belgeler, bir üretim iş yükünü işlemeye yönelik bir kümeyi dağıtmadan önce göz önünde bulundurulmalıdır.
ms.topic: conceptual
ms.date: 9/11/2018
ms.openlocfilehash: 18b8b0ce8c0e877bf9dd274596b19f85b1febe12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98790961"
---
# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Service Fabric tek başına küme dağıtımınızı planlayın ve hazırlayın

<a id="preparemachines"></a>Kümenizi oluşturmadan önce aşağıdaki adımları gerçekleştirin.

## <a name="plan-your-cluster-infrastructure"></a>Küme altyapınızı planlayın
"Sahip olduğunuz" makinelerde bir Service Fabric kümesi oluşturmak üzeresiniz, bu sayede kümenin ne tür hatalara karşı olmasını istediğinize karar verebilirsiniz. Örneğin, bu makinelere ayrı güç çizgilere veya Internet bağlantılarına gerek var mı? Ayrıca, bu makinelerin fiziksel güvenliğini göz önünde bulundurun. Makineler nerede bulunur ve bunlara erişmesi gerekiyor? Bu kararları verdikten sonra, makineleri çeşitli hata etki alanlarına mantıksal olarak eşleyebilirsiniz (bkz. sonraki adım). Üretim kümeleri için altyapı planlaması, test kümelerine göre daha karmaşıktır.

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>Hata etki alanları ve yükseltme etki alanı sayısını belirleme
Bir [ *hata etki alanı* (FD)](service-fabric-cluster-resource-manager-cluster-description.md) , fiziksel bir hata birimidir ve veri merkezlerindeki fiziksel altyapıyla doğrudan ilgilidir. Hata etki alanı, tek bir başarısızlık noktasını paylaşan donanım bileşenlerinden (bilgisayarlar, anahtarlar, ağlar ve daha fazlası) oluşur. Hata etki alanları ve dolaplar arasında 1:1 eşleme olmamasına karşın, gevşekçe, her raf bir hata etki alanı olarak kabul edilebilir.

ClusterConfig.jsüzerinde FDs belirttiğinizde her bir FD için adı seçebilirsiniz. Service Fabric hiyerarşik FDs 'yi desteklediğinden, bu sayede altyapı topolojinizi yansıtabilmenizi sağlayabilirsiniz.  Örneğin, aşağıdaki FDs geçerlidir:

* "faultDomain": "FD:/room1/raf1/Machine1"
* "faultDomain": "FD:/FD1"
* "faultDomain": "FD:/room1/raf1/PDU1/M1"

*Yükseltme etki alanı* (UD), mantıksal bir düğüm birimidir. Service Fabric düzenlenmiş yükseltmeler (bir uygulama yükseltmesi veya bir küme yükseltmesi) sırasında, bir UD 'deki tüm düğümler, başka bir düğüm içindeki düğümler istek görmesi için kullanılabilir olmaya devam ederken yükseltmeyi gerçekleştirmek için alınır. Makinelerinizde gerçekleştirdiğiniz üretici yazılımı yükseltmeleri UDs 'yi kabul etmez, bu nedenle bunları aynı anda bir makine yapmanız gerekir.

Bu kavramları düşünmek için en basit yol, planlı bakım birimi olarak bir plansız hata ve UDs birimi olarak FDs 'yi göz önünde bulundurmaktır.

ClusterConfig.jsüzerinde UDs belirttiğinizde, her bir UD için adı seçebilirsiniz. Örneğin, aşağıdaki adlar geçerlidir:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "mavi"

FDs ve UDs hakkında daha ayrıntılı bilgi için bkz. [Service Fabric kümesini açıklama](service-fabric-cluster-resource-manager-cluster-description.md).

Üretimde bir küme, bir üretim ortamında desteklenmek üzere en az üç FI kapsamalıdır. düğümlerin bakımı ve yönetimi üzerinde tam denetime sahipseniz, diğer bir deyişle, makineleri güncelleştirmek ve değiştirmek sizin sorumluluğunuzdadır. Makinelerde çalışan kümeler için (yani, sanal makine örnekleri Amazon Web Services), makinenizde tam denetime sahip değilsiniz, kümenizde en az beş tane olmalıdır. Her FD bir veya daha fazla düğüme sahip olabilir. Bu, zaman zamanlamalarına bağlı olarak makine yükseltmelerinden ve güncelleştirmelerden kaynaklanan sorunları önlemektir. bu sayede, zamanlamaya göre uygulamaların ve hizmetlerin çalıştırılmasına engel olabilir.

## <a name="determine-the-initial-cluster-size"></a>İlk küme boyutunu belirleme

Genellikle, kümenizdeki düğümlerin sayısı, iş gereksinimlerinize göre belirlenir, diğer bir deyişle, kümede kaç tane hizmet ve kapsayıcı çalışır ve iş yüklerinize karşı çok sayıda kaynak gereklidir. Üretim kümeleri için, kümenizde 5 düğüm arasında en az beş düğüm olmasını öneririz. Ancak, yukarıda açıklandığı gibi, düğümleriniz üzerinde tam denetime sahipseniz ve üç düğüm yayılabilirse, üç düğüm de işi de yapabilmelidir.

Durum bilgisiz iş yükleri çalıştıran test kümelerinde üç düğüm olmalıdır; Oysa yalnızca durum bilgisi olmayan iş yükleri çalıştırılan test kümelerinde yalnızca bir düğüm gerekir Ayrıca, geliştirme amaçlarıyla, belirli bir makinede birden fazla düğüme sahip olabilirsiniz. Ancak bir üretim ortamında, Service Fabric fiziksel veya sanal makine başına yalnızca bir düğüm destekler.

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>Düğüm olarak kullanılacak makineleri hazırlama

Bir Service Fabric kümesindeki makineler için önerilen özellikler aşağıda verilmiştir:

* En az 16 GB RAM
* En az 40 GB kullanılabilir disk alanı
* 4 çekirdek veya daha büyük CPU
* Tüm makineler için güvenli ağ veya ağlarla bağlantı
* Windows Server işletim sistemi yüklü (geçerli sürümler: 2012 R2, 2016, 1709 veya 1803). Service Fabric Version 6.4.654.9590 ve üzeri, Server 2019 ve 1809 sürümlerini de destekler.
* [.NET Framework 4.5.1 veya üzeri](https://www.microsoft.com/download/details.aspx?id=40773), tam yüklemesi
* [Windows PowerShell 3.0](/powershell/scripting/windows-powershell/install/installing-windows-powershell)
* [RemoteRegistry hizmetinin](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754820(v=ws.11)) tüm makinelerde çalışıyor olması gerekir
* **Service Fabric yükleme sürücüsü NTFS dosya sistemi olmalıdır**
* **Windows Hizmetleri *performans günlükleri & uyarıları* ve *Windows olay günlüğü* [etkin](/previous-versions/windows/it-pro/windows-server-2008-r2-and-2008/cc755249(v=ws.11))olmalıdır**.

> [!IMPORTANT]
> Kümeyi dağıtan ve yapılandıran küme yöneticisinin her makinede [yönetici ayrıcalıklarına](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) sahip olması gerekir. Service Fabric’i bir etki alanı denetleyicisine yükleyemezsiniz.

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>Windows Server için Service Fabric tek başına paketini indirin
[Bağlantı Service Fabric tek başına paketini indirin-Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) ve paketin bir parçası olmayan bir dağıtım makinesine ya da kümenizin bir parçası olacak makinelerden birine ayıklayın.

## <a name="modify-cluster-configuration"></a>Küme yapılandırmasını değiştirme
Tek başına küme oluşturmak için, bir tek başına küme ClusterConfig.jsyapılandırması oluşturmanız gerekir ve bu küme, kümenin belirtimini açıklar. Yapılandırma dosyasını aşağıdaki bağlantıda bulunan şablonlarda temel alabilirsiniz. <br>
[Tek başına küme yapılandırması](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Bu dosyadaki bölümlerle ilgili ayrıntılar için bkz. [tek başına Windows kümesi Için yapılandırma ayarları](service-fabric-cluster-manifest.md).

İndirdiğiniz paketteki dosyalardaki ClusterConfig.jsbirini açın ve aşağıdaki ayarları değiştirin:

| **Yapılandırma ayarı** | **Açıklama** |
| --- | --- |
| **NodeTypes** |Düğüm türleri, küme düğümlerinizi çeşitli gruplar halinde ayırmanızı sağlar. Bir kümenin en az bir NodeType olması gerekir. Bir gruptaki tüm düğümler aşağıdaki ortak özelliklere sahiptir: <br> **Ad** -bu düğüm türü adıdır. <br>**Uç nokta bağlantı noktaları** -bu düğüm türüyle ilişkili çeşitli adlandırılmış uç noktaları (bağlantı noktaları) vardır. İstediğiniz herhangi bir bağlantı noktası numarasını, bu bildirimde başka hiçbir şeyle çakışmadığı ve makine/VM üzerinde çalışan başka bir uygulama tarafından kullanımda olmadığı sürece kullanabilirsiniz. <br> **Yerleştirme özellikleri** -bu düğüm türü için, sistem hizmetleri veya hizmetleriniz için yerleştirme kısıtlamaları olarak kullandığınız özellikler açıklanır. Bu özellikler, belirli bir düğüm için ek meta veri sağlayan Kullanıcı tanımlı anahtar/değer çiftleridir. Düğüm özelliklerinin örnekleri, düğümün sabit sürücü veya grafik kartına sahip olup olmadığı, sabit sürücü, çekirdek sayısı ve diğer fiziksel özelliklerle ilgili olarak bulunduğu sayıdır. <br> **Kapasiteler** -düğüm kapasiteleri, belirli bir düğümün tüketim için kullanabileceği belirli bir kaynağın adını ve miktarını tanımlar. Örneğin, bir düğüm, "MemoryMB" adlı bir ölçüm için kapasiteye sahip olduğunu ve varsayılan olarak 2048 MB olduğunu tanımlayabilir. Bu kapasiteler, belirli miktarlarda kaynak gerektiren hizmetlerin gerekli miktarlarda kullanılabilir kaynakları bulunan düğümlere yerleştirildiğinden emin olmak için çalışma zamanında kullanılır.<br>**IsPrimary** -birden fazla NodeType tanımlı ise, sistem hizmetlerinin çalıştığı, *doğru* değeri olan yalnızca bir tane birincil olarak ayarlandığından emin olun. Diğer tüm düğüm türleri *false* değerine ayarlanmalıdır |
| **Düğümler** |Bunlar kümenin parçası olan düğümlerin (düğüm türü, düğüm adı, IP adresi, hata etki alanı ve yükseltme etki alanı) her biri için ayrıntılardır. Kümenin oluşturulmasını istediğiniz makinelerin, IP adresleriyle birlikte burada listelenmesi gerekir. <br> Tüm düğümler için aynı IP adresini kullanırsanız, test amacıyla kullanabileceğiniz bir tek Box kümesi oluşturulur. Üretim iş yüklerini dağıtmak için tek Box kümeleri kullanmayın. |

Küme yapılandırmasının tüm ayarları ortama yapılandırıldıktan sonra, küme ortamına karşı test edilebilir (adım 7).

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>Ortamı ayarlama

Bir Küme Yöneticisi Service Fabric tek başına kümesi yapılandırdığında, ortamın aşağıdaki ölçütlere göre ayarlanması gerekir: <br>
1. Kümeyi oluşturan kullanıcı, küme yapılandırma dosyasında düğüm olarak listelenen tüm makinelere yönetici düzeyinde güvenlik ayrıcalıklarına sahip olmalıdır.
2. Kümenin oluşturulduğu makine ve her küme düğümü makinesi şunları içermelidir:
   * Service Fabric SDK 'Sı kaldırıldı
   * Service Fabric çalışma zamanının kaldırılması
   * Windows Güvenlik Duvarı hizmeti (MpsSvc) etkin
   * Uzak kayıt defteri hizmeti (uzak kayıt defteri) etkin
   * Dosya paylaşımı (SMB) etkin
   * Küme yapılandırma bağlantı noktalarına bağlı olarak, gerekli bağlantı noktaları açık olmalıdır
   * Windows SMB ve uzak kayıt defteri hizmeti için gerekli bağlantı noktaları açıldı: 135, 137, 138, 139 ve 445
   * Birbiriyle ağ bağlantısı var
3. Küme düğümü makinelerinin hiçbiri bir etki alanı denetleyicisi olmamalıdır.
4. Dağıtılacak küme güvenli bir kümesa, gerekli güvenlik önkoşullarının yerinde olduğunu ve yapılandırmaya göre doğru şekilde yapılandırıldığını doğrulayın.
5. Küme makineleri internet 'e erişemezse, küme yapılandırmasında aşağıdakileri ayarlayın:
   * Telemetriyi devre dışı bırak: *Özellikler* altında *"enabletelemetri" kümesi: false*
   * Otomatik doku sürümü indirmeyi devre dışı bırak & geçerli küme sürümünün destek sonuna yaklaştığı bildirimler: *"Fabricclusterotomatik Upgradeenabled"* olarak ayarlanmış *Özellikler* altında: false
   * Alternatif olarak, ağ internet erişimi, allowlistelenmiş etki alanlarıyla sınırlı ise, otomatik yükseltme için aşağıdaki etki alanları gerekir: go.microsoft.com download.microsoft.com

6. Virüsten koruma dışlamalarını uygun Service Fabric ayarlayın:

| **Virüsten koruma için dışlanan dizinler** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (küme yapılandırmasından) |
| FabricLogRoot (küme yapılandırmasından) |

| **Virüsten koruma hariç tutulan süreçler** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |

## <a name="validate-environment-using-testconfiguration-script"></a>TestConfiguration betiği kullanarak ortamı doğrulama
TestConfiguration.ps1 betiği tek başına pakette bulunabilir. Yukarıdaki ölçütlerden bazılarını doğrulamak için bir en iyi yöntemler Çözümleyicisi olarak kullanılır ve bir kümenin belirli bir ortamda dağıtılıp dağıtılamayacağını doğrulamak için bir sağlamlık denetimi olarak kullanılmalıdır. Herhangi bir hata varsa, sorun giderme için [ortam kurulumu](service-fabric-cluster-standalone-deployment-preparation.md) altındaki listeye bakın.

Bu betik, küme yapılandırma dosyasında düğüm olarak listelenen tüm makinelere yönetici erişimi olan herhangi bir makinede çalıştırılabilir. Bu betiğin çalıştırıldığı makinenin kümenin bir parçası olması gerekmez.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 : True
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
Passed                     : True
```

Şu anda bu yapılandırma testi modülü güvenlik yapılandırmasını doğrulamaz çünkü bunun bağımsız olarak gerçekleştirilmesi gerekmez.

> [!NOTE]
> Bu modülü daha sağlam hale getirmek için sürekli geliştirmeler yapıyoruz. bu nedenle, TestConfiguration tarafından şu anda yakalanmadığınız bir hatalı veya eksik durum varsa lütfen [destek kanallarımızla](./service-fabric-support.md)bize bildirin.
>
>

## <a name="next-steps"></a>Sonraki adımlar
* [Windows Server üzerinde çalıştırılan bir tek başına küme oluşturma](service-fabric-cluster-creation-for-windows-server.md)
