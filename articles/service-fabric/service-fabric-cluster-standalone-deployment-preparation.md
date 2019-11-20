---
title: Azure Service Fabric tek başına küme dağıtımını hazırlama | Microsoft Docs
description: Ortamı hazırlama ve üretim iş yükü işlemek için hedeflenen küme dağıtmadan önce göz önünde bulundurulması için küme yapılandırması oluşturma ile ilgili belgeler.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/11/2018
ms.author: dekapur
ms.openlocfilehash: 8b9f659098e563a3dc0692530ad798a5c763551f
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133407"
---
# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Plan ve hazırlık, Service Fabric tek başına Küme dağıtımı

<a id="preparemachines"></a>Kümenizi oluşturmadan önce aşağıdaki adımları gerçekleştirin.

## <a name="plan-your-cluster-infrastructure"></a>Küme altyapınızı planlama
Hangi tür hatalardan istediğiniz kümenin hayatta kalmak için karar verebilmek için "sahibi", makineler üzerinde bir Service Fabric kümesi oluşturmak üzere olduğunuz. Örneğin, güç satırları veya Internet bağlantıları bu makineler için sağlanan ayrı? Ayrıca, bu makineler, fiziksel güvenlik göz önünde bulundurun. Makineleri nerede bulunuyor ve onlara yönelik erişimi gerek duyan? Bu kararlar yaptıktan sonra makineleri mantıksal olarak (bir sonraki adıma bakın) çeşitli hata etki alanlarına eşleyebilirsiniz. Altyapı planlama üretim kümeleri için test kümeleri için daha daha karmaşıktır.

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>Hata etki alanları sayısını belirlemek ve yükseltme etki alanları
A [ *hata etki alanı* (FD)](service-fabric-cluster-resource-manager-cluster-description.md) hatası fiziksel birimidir ve Veri merkezlerindeki fiziksel altyapı doğrudan ilgilidir. Hata etki alanı, bir tek hata noktası paylaşan donanım bileşenlerinin (bilgisayarlar, anahtarlar, ağ ve daha fazla) oluşur. Hata etki alanları ve raflar arasında 1:1 eşleme yok olsa da, gevşek açıklamak gerekirse, her bir rafa hata etki alanı kabul edilebilir.

İçinde ClusterConfig.json FD belirttiğinizde, her FD için ad seçebilirsiniz. Service Fabric hiyerarşik FD desteklediğinden, altyapı topolojinizde bunları yansıtabilir.  Örneğin, aşağıdaki FD geçerlidir:

* "faultDomain": "fd: / raf1/Room1/Machine1"
* "faultDomain": "fd: / FD1"
* "faultDomain": "fd: / Room1/raf1/PDU1/M1"

Bir *yükseltme etki alanı* (UD) olan bir mantıksal birim düğümleri. Service Fabric düzenlenen yükseltmeleri sırasında (Uygulama yükseltme veya bir küme yükseltmesi), tüm düğümlerin bir UD diğer UD düğümler isteklere hizmet kullanılabilir olmaya yükseltmeyi gerçekleştirmek için alınır. Bunları aşağıdakilerden yapmalısınız şekilde makinelerinizde gerçekleştirdiğiniz üretici yazılımını yükseltme UD, uygulamayan bir zaman makine.

Bu kavramlar hakkında düşünün en basit yolu, UD ve planlanmamış hatası birimi olarak planlı bakım birimi olarak FD göz önünde bulundurun sağlamaktır.

UD içinde ClusterConfig.json belirttiğinizde, her UD için ad seçebilirsiniz. Örneğin, aşağıdaki adları geçerlidir:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "Mavi"

FD ve Ud'ler hakkında daha ayrıntılı bilgi için bkz: [açıklayan bir Service Fabric kümesi](service-fabric-cluster-resource-manager-cluster-description.md).

Bir üretim ortamında desteklenmesi için üretim bir kümede en az üç FD yayılacağı düğümlerin yönetim ve Bakım üzerinde tam denetime sahip başka bir deyişle, güncelleştirme ve makineleri değiştirme sorumlu vardır. (Diğer bir deyişle, Amazon Web Services sanal makine örnekleri) ortamlarında, makine üzerinde tam denetime sahip olduğu değil çalıştıran kümeler için en az beş FD kümenizde sahip olmalıdır. Her FD bir veya daha fazla düğüme sahip olabilir. Bu makine yükseltmeleri ve güncelleştirmeleri, hangi kullanıcıların zamanlama çalışan uygulamaların ve hizmetlerin kümelerinde engelleyebilir kaynaklanan sorunları önlemek içindir.

## <a name="determine-the-initial-cluster-size"></a>İlk küme boyutunu belirler

Genellikle, kümenizdeki düğüm sayısını, iş gereksinimlerinize, kaç Hizmetleri ve kapsayıcıları kümede üzerinde çalışacağı bağlı olarak belirlenir ve iş yüklerinizi desteklemek gereken kaç kaynak. Üretim kümeleri için kümenizin en az beş düğüme sahip kapsayıcı 5 FD öneririz. Ancak, düğümlerinizi üzerinde tam denetime sahip ve üç FD yayılabilir, yukarıda açıklandığı gibi sonra üç düğüm ayrıca iş yapmanız gerekir.

Bir düğüm yalnızca yalnızca durum bilgisiz iş yükleri çalıştıran test kümeleri gerekiyor ancak test kümeleri durum bilgisi olan iş yükleri çalıştıran üç düğüm olmalıdır. Bu, geliştirme amacıyla birden fazla düğüm bir makinede sağlayabilirsiniz de unutulmamalıdır. Bir üretim ortamında, ancak yalnızca bir düğümün fiziksel veya sanal makine başına Service Fabric destekler.

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>Düğümleri olarak hizmet verecek makineleri hazırlama

Kümeye eklemek istediğiniz her makine için önerilen bazı özellikleri şunlardır:

* En az 16 GB RAM
* 40 GB kullanılabilir disk alanı en az
* Bir 4 çekirdekli veya daha fazla CPU
* Güvenli bir ağ veya tüm makineler için ağ bağlantısı
* Windows Server işletim sistemi yüklü (geçerli sürümler: 2012 R2, 2016, 1709 veya 1803). Service Fabric Version 6.4.654.9590 ve üzeri, Server 2019 ve 1809 sürümlerini de destekler.
* [.NET framework 4.5.1 veya üzeri](https://www.microsoft.com/download/details.aspx?id=40773), tam yükleme
* [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/install/installing-windows-powershell)
* [RemoteRegistry hizmeti](https://technet.microsoft.com/library/cc754820) tüm makinelerde çalıştırılması
* Service Fabric yükleme sürücüsü NTFS dosya sistemi olmalıdır

Dağıtma ve yapılandırma kümenin Küme Yöneticisi olmalıdır [yönetici ayrıcalıkları](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) her makine. Service Fabric’i bir etki alanı denetleyicisine yükleyemezsiniz.

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>Windows Server için Service Fabric tek başına paketin indirin
[Bağlantı - Service Fabric tek başına paketin - Windows Server'ı indirin](https://go.microsoft.com/fwlink/?LinkId=730690) ve kümenin parçası olmayan bir dağıtım makinesine veya kümenizin parçası olan makinelerden biri için paketin sıkıştırmasını açın.

## <a name="modify-cluster-configuration"></a>Küme yapılandırmasını Değiştir
Tek başına küme oluşturma için küme belirtimi açıklayan bir tek başına küme yapılandırma ClusterConfig.json dosyası oluşturmanız gerekir. Bulunan şablonlar yapılandırma dosyası dayandırabilirsiniz aşağıdaki bağlantıda verilmiştir. <br>
[Tek başına küme yapılandırmaları](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Bu dosyadaki bölümleri hakkında daha fazla bilgi için bkz: [tek başına Windows Küme için yapılandırma ayarlarını](service-fabric-cluster-manifest.md).

İndirdiğiniz paketinden ClusterConfig.json dosyalarından birini açın ve aşağıdaki ayarları değiştirin:

| **Yapılandırma ayarı** | **Açıklama** |
| --- | --- |
| **NodeType** |Düğüm türleri, Küme düğümlerinizi çeşitli gruplar halinde ayırmanıza olanak sağlar. Bir küme en az bir NodeType olması gerekir. Bir gruptaki tüm düğümleri aşağıdaki ortak özelliklere sahiptir: <br> **Ad** -düğüm türü adı budur. <br>**Uç nokta bağlantı noktası** - bu çeşitli uç bu düğüm türü ile ilişkili olan noktaları (bağlantı noktaları) olarak adlandırılır. Bu bildirimde başka bir şey ile çakışmadığından sürece istediğiniz ve zaten olmayan makine/VM'de çalışan herhangi bir uygulama tarafından kullanılmakta olan bağlantı noktası numarasını kullanabilirsiniz. <br> **Yerleştirme özelliklerini** -bunlar sistem hizmetleri veya hizmetleriniz için yerleştirme kısıtlamaları kullanan bu düğüm türü için özellikleri açıklar. Bu özellikler belirli bir düğümde ek meta veriler sağlayan kullanıcı tanımlı anahtar/değer çiftleridir. Düğüm özellikleri örnekleri, bir sabit sürücü veya grafik kartı, sabit sürücüsünü, çekirdek ve diğer fiziksel özellikler iğnelerinin sayısı düğüm olup olacaktır. <br> **Kapasiteleri** -düğüm kapasiteleri, belirli bir kaynak miktarını ve adını tanımlayın belirli bir düğüm tüketim için kullanılabilir olduğunu. Örneğin, bir düğüm "MemoryInMb" adlı bir ölçüm için kapasiteye sahip olduğundan ve varsayılan olarak 2048 MB kullanılabilir olduğunu tanımlayabilir. Bu kapasite, çalışma zamanında belirli miktarda kaynağı gerektiren hizmetler kaynaklarla gerekli tutarları kullanılabilir olan düğümleri yerleştirildiğinden emin olmak için kullanılır.<br>**Isprimary** - yalnızca bir değerle için birincil ayarlandığından emin olun birden fazla NodeType tanımlı varsa *true*, burada Çalıştır Sistem Hizmetleri olduğu. Diğer tüm düğüm türleri değerine ayarlanmalıdır *false* |
| **Düğümleri** |Bu, küme (düğüm türü, düğüm adı, IP adresi, hata etki alanı ve yükseltme etki alanı düğümünün) parçası olan düğümler için ayrıntılar bulunur. Küme IP adreslerini burada listelenebileceğinizi gerek oluşturulacak istediğiniz makineleri. <br> Tüm düğümleri aynı IP adresini kullanın, ardından hazır bir küme, test amacıyla kullanabileceğiniz oluşturulur. One-box kümelerini üretim iş yükleri dağıtmak için kullanmayın. |

Küme yapılandırmasını ortama yapılandırılan tüm ayarları aldıktan sonra (adım 7) küme ortamında test edilebilir.

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>Ortam kurulumu

Bir Küme Yöneticisi, Service Fabric tek başına küme yapılandırdığında, ortamın aşağıdaki ölçütleri ile ayarlanmış olması gerekir: <br>
1. Kümeyi oluşturan kullanıcının küme yapılandırma dosyasında düğümleri olarak listelenen tüm makineler için yönetici düzeyinde güvenlik ayrıcalıkları olmalıdır.
2. Her küme düğümünde makine yanı sıra küme oluşturulduğu makine gerekir:
   * Service Fabric SDK'sı kaldırmış
   * Service Fabric çalışma zamanı kaldırıldı
   * Windows Güvenlik Duvarı (mpssvc) hizmet etkinleştirdiniz mi
   * Uzak Kayıt Defteri hizmeti (uzak kayıt defteri) etkinleştirdiniz mi
   * Dosya Paylaşımı (SMB) etkin
   * Gerekli bağlantı noktaları açıldı, küme yapılandırması bağlantı noktalarına bağlı olan
   * Gerekli bağlantı noktalarını Windows SMB ve uzak kayıt defteri hizmeti için açılmış olması: 135 ve 137, 138, 139'dur ve 445
   * Başka bir ağ bağlantısına sahip
3. Küme düğümü makinelerin hiçbirinin bir etki alanı denetleyicisi olmalıdır.
4. Kümenin dağıtılması için güvenli bir küme ise, Önkoşullar içinde yerleştirin ve yapılandırmanın karşı doğru yapılandırıldığından gerekli güvenlik doğrulayın.
5. Küme makinelerin İnternet'ten erişilebilen emin değilseniz, aşağıdaki küme yapılandırmasında ayarlayın:
   * Telemetri devre dışı bırak: altında *özellikleri* ayarlamak *"enableTelemetry": false*
   * Otomatik yapı sürümü indirme & Geçerli Küme sürümü, destek sonuna yaklaşıyor bildirimleri devre dışı bırak: altında *özellikleri* ayarlamak *"fabricClusterAutoupgradeEnabled": false*
   * Alternatif olarak, ağ internet erişimi beyaz listelenen etki alanları için sınırlı ise, aşağıdaki etki alanlarına otomatik yükseltme için gereken: go.microsoft.com download.microsoft.com

6. Service Fabric uygun virüsten koruma dışlamaları ayarlayın:

| **Virüsten koruma hariç tutulan dizinler** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (küme yapılandırmasından) |
| FabricLogRoot (küme yapılandırmasından) |

| **Virüsten koruma dışlanan işlemler** |
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

## <a name="validate-environment-using-testconfiguration-script"></a>TestConfiguration betik kullanarak ortamı doğrulama
Tek başına paketteki TestConfiguration.ps1 betiği bulunamadı. Yukarıdaki ölçütlere bazıları doğrulamak için bir en iyi yöntemler Çözümleyicisi kullanılır ve sağlamlık denetim olarak bir küme belirli bir ortamda dağıtılıp dağıtılamayacağını doğrulamak için kullanılmalıdır. Herhangi bir hata varsa, listenin altında başvurmak [ortam Kurulumu](service-fabric-cluster-standalone-deployment-preparation.md) sorun giderme.

Bu betik, küme yapılandırma dosyasında düğümleri olarak listelenen tüm makineler için yönetici erişimi olan herhangi bir makinede çalıştırılabilir. Bu betiğin çalıştırıldığı makine kümesinin parçası olacak gerekmez.

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

Şu anda bu bağımsız olarak yapılması gerekir, böylece bu yapılandırmayı test modül güvenlik yapılandırması doğrulamaz.

> [!NOTE]
> Hatalı veya eksik bir durumda ise düşünüyorsanız, bu nedenle sürekli olarak iyileştirmeler Bu modülün daha sağlam hale getirmek için şu anda TestConfiguration tarafından yakalanan yapıyoruz, aracılığıyla bize bildirin bizim [destek kanalları](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
>
>

## <a name="next-steps"></a>Sonraki adımlar
* [Windows Server üzerinde çalışan tek başına küme oluşturma](service-fabric-cluster-creation-for-windows-server.md)
