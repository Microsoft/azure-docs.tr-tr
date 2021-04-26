---
title: Azure Service Fabric hakkında daha fazla bilgi
description: Azure Service Fabric 'nin temel kavramları ve ana alanlarıyla ilgili bilgi edinin. Service Fabric genişletilmiş bir genel bakış sağlar ve mikro hizmetlerin nasıl oluşturulacağını açıklar.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 6e5a7445706a6e7842822ec784a64555d9226923
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727663"
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Service Fabric hakkında bilgi edinmek istiyor musunuz?
Azure Service Fabric; ölçeklenebilir ve güvenilir mikro hizmetleri paketlemeyi, dağıtmayı ve yönetmeyi kolaylaştırmayı sağlayan bir dağıtılmış sistemler platformudur.  Service Fabric, ancak büyük bir yüzey alanına sahiptir ve öğrenilmesi çok önemlidir.  Bu makalede bir Service Fabric Özeti ve temel kavramlar, programlama modelleri, uygulama yaşam döngüsü, test, kümeler ve sistem durumu izleme açıklanmaktadır. Bir giriş ve mikro hizmetler oluşturmak için Service Fabric nasıl kullanılabileceği hakkında [genel bakış](service-fabric-overview.md) ve [mikro hizmetler nelerdir?](service-fabric-overview-microservices.md) makalesini okuyun. Bu makale kapsamlı bir içerik listesi içermez, ancak Service Fabric her alanı için genel bakış ve başlangıç makalelerine bağlantı sağlar. 

## <a name="core-concepts"></a>Temel kavramlar
[Service Fabric terminoloji](service-fabric-technical-overview.md), [uygulama modeli](service-fabric-application-model.md)ve [desteklenen programlama modelleri](service-fabric-choose-framework.md) daha fazla kavram ve açıklama sağlar, ancak burada temel bilgiler verilmiştir.

### <a name="design-time-service-type-service-package-and-manifest-application-type-application-package-and-manifest"></a>Tasarım Zamanı: hizmet türü, hizmet paketi ve bildirim, uygulama türü, uygulama paketi ve bildirim
Hizmet türü, bir hizmetin kod paketlerine, veri paketlerine ve yapılandırma paketlerine atanan ad/sürümdür. Bu, bir ServiceManifest.xml dosyasında tanımlanmıştır. Hizmet türü, çalışma zamanında yüklenen yürütülebilir kod ve hizmet yapılandırma ayarlarından ve hizmet tarafından tüketilen statik verilerle oluşur.

Hizmet paketi, hizmet türü için koda, statik verilere ve yapılandırma paketlerine başvuran hizmet türünün ServiceManifest.xml dosyasını içeren bir disk dizinidir. Örneğin, bir hizmet paketi, bir veritabanı hizmetini oluşturan koda, statik verilere ve yapılandırma paketlerine başvurabilir.

Uygulama türü, bir hizmet türleri koleksiyonuna atanan ad/sürümdür. Bu, bir ApplicationManifest.xml dosyasında tanımlanmıştır.

![Uygulama türlerini ve hizmet türlerini Service Fabric][cluster-imagestore-apptypes]

Uygulama paketi uygulama türünün ApplicationManifest.xml dosyasını içeren bir disk dizinidir ve bu, uygulama türünü oluşturan her hizmet türü için hizmet paketlerine başvurur. Örneğin, bir e-posta uygulama türü için uygulama paketi bir kuyruk hizmeti paketine, bir ön uç hizmet paketine ve bir veritabanı hizmeti paketine başvuru içerebilir.  

Uygulama paketi dizinindeki dosyalar Service Fabric kümesinin görüntü deposuna kopyalanır. Daha sonra bu uygulama türünden adlandırılmış bir uygulama oluşturabilir ve ardından küme içinde çalışır. Adlandırılmış bir uygulama oluşturduktan sonra, uygulama türünün hizmet türlerinden birinden adlandırılmış bir hizmet oluşturabilirsiniz. 

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Çalışma Zamanı: uygulamalar, hizmetler, bölümler ve çoğaltmalar adlı kümeler ve düğümler
[Service Fabric küme](service-fabric-deploy-anywhere.md) , mikro hizmetlerinizin dağıtıldığı ve yönetildiği, ağa bağlı bir sanal veya fiziksel makine kümesidir. Kümeler binlerce makine içerecek şekilde ölçeklendirilebilir.

Bir kümenin parçası olan makine veya VM’lere düğüm denir. Her düğüme bir düğüm adı (bir dize) atanır. Düğümlerin yerleşim özellikleri gibi özellikleri vardır. Her makine veya VM, `FabricHost.exe` önyükleme sonrasında çalışmaya başlayan ve ardından iki yürütülebilir dosya Başlatan bir otomatik başlatma Windows hizmetine sahiptir: `Fabric.exe` ve `FabricGateway.exe` . Bu iki yürütülebilir dosya düğümü yapar. Geliştirme veya test senaryoları için, birden fazla ve birden çok örneğini çalıştırarak tek bir makinede veya VM 'de birden çok düğümü barındırabilirsiniz `Fabric.exe` `FabricGateway.exe` .

Adlandırılmış bir uygulama, belirli bir işlevi veya işlevleri gerçekleştiren adlandırılmış hizmetlerden oluşan bir koleksiyondur. Bir hizmet, bir bütün ve tek başına işlevi gerçekleştirir (diğer hizmetlerden bağımsız olarak başlatılabilir ve çalıştırılabilir) ve kod, yapılandırma ve verilerden oluşur. Bir uygulama paketi görüntü deposuna kopyalandıktan sonra, uygulama paketinin uygulama türünü (adını/sürümünü kullanarak) belirterek küme içinde uygulamanın bir örneğini oluşturursunuz. Her uygulama türü örneğine *doku:/MyNamedApp* gibi görünen bir URI adı atanır. Bir küme içinde, tek bir uygulama türünden birden çok adlandırılmış uygulama oluşturabilirsiniz. Farklı uygulama türlerinden adlandırılmış uygulamalar da oluşturabilirsiniz. Adlandırılmış her uygulama bağımsız olarak yönetilir ve sürümü oluşturulur.

Adlandırılmış bir uygulama oluşturduktan sonra hizmet türünü belirterek (adını/sürümünü kullanarak) küme içinde hizmet türlerinden birinin (adlandırılmış bir hizmet) bir örneğini oluşturabilirsiniz. Her hizmet türü örneğine, adlandırılmış uygulamasının URI 'sinin kapsamında bir URI adı atanır. Örneğin, "MyNamedApp" adlı uygulama içinde bir "MyDatabase" hizmeti oluşturursanız, URI şöyle görünür: *Fabric:/MyNamedApp/MyDatabase*. Adlandırılmış bir uygulama içinde, bir veya daha fazla adlandırılmış hizmet oluşturabilirsiniz. Her bir adlandırılmış hizmet kendi bölüm düzenine ve örnek/çoğaltma sayısına sahip olabilir. 

İki tür hizmet vardır: durum bilgisiz ve durum bilgisi. Durum bilgisi olmayan hizmetler, durumu hizmet içinde depolamaz. Durum bilgisi olmayan hizmetlerin hiç kalıcı depolama alanı yok veya Azure depolama, Azure SQL veritabanı veya Azure Cosmos DB gibi bir dış depolama hizmetinde kalıcı durumda. Durum bilgisi olan bir hizmet, durumu hizmet içinde depolar ve durumu yönetmek için güvenilir koleksiyonlar veya Reliable Actors programlama modelleri kullanır. 

Adlandırılmış bir hizmet oluştururken bir bölüm düzeni belirtirsiniz. Büyük miktarda duruma sahip hizmetler verileri bölümler arasında böler. Her bölüm, kümenin düğümlerine yayılan hizmetin tamamen durumunun bir kısmından sorumludur.  

Aşağıdaki diyagramda uygulamalar ve hizmet örnekleri, bölümler ve çoğaltmalar arasındaki ilişki gösterilmektedir.

![Bir hizmet içindeki bölümler ve çoğaltmalar][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Bölümlendirme, ölçekleme ve kullanılabilirlik
[Bölümlendirme](service-fabric-concepts-partitioning.md) Service Fabric için benzersiz değil. Bir bölümlemenin iyi bilinen bir biçimi, veri bölümleme veya parçalama. Büyük miktarlı durum bilgisi olan hizmetler verileri bölümler arasında böler. Her bölüm hizmetin tamamlanma durumunun bir kısmından sorumludur. 

Her bölümün çoğaltmaları kümenin düğümlerine yayılır, bu da adlandırılmış hizmetin durumunun [ölçeklendirilmesine](service-fabric-concepts-scalability.md)olanak tanır. Verilerin büyümesi, bölümlerin büyümesi ve Service Fabric donanım kaynaklarının verimli bir şekilde kullanılması için bölümleri düğümler arasında yeniden dengeleyebilmesi. Kümeye yeni düğümler eklerseniz Service Fabric, Bölüm çoğaltmalarını artan düğüm sayısı genelinde yeniden dengeleyebilir. Genel uygulama performansı, bellek düşüşlerine erişim için gelişir ve çekişmeyi geliştirir. Kümedeki düğümler verimli bir şekilde kullanılmıyorsa, kümedeki düğümlerin sayısını azaltabilirsiniz. Service Fabric, her düğümdeki donanımın daha iyi kullanılmasını sağlamak için bölüm çoğaltmalarını azalmış düğüm sayısı genelinde yeniden dengeler.

Bir bölüm içinde, durum bilgisi olan adlandırılmış hizmetler, durum bilgisi olan adlandırılmış hizmetler çoğaltmaları olduğu sürece örnekleri vardır. Genellikle, durum bilgisi olmayan adlandırılmış hizmetler, hiçbir zaman [özel durumlar](./service-fabric-concepts-partitioning.md#partition-service-fabric-stateless-services)olmasına rağmen iç durumu olmadığından yalnızca bir bölüme sahiptir. Bölüm örnekleri [kullanılabilirlik](service-fabric-availability-services.md)sağlar. Bir örnek başarısız olursa, diğer örnekler normal şekilde çalışmaya devam eder ve ardından Service Fabric yeni bir örnek oluşturur. Durum bilgisi olan adlandırılmış hizmetler çoğaltmalar içinde durumlarını korur ve her bölüm kendi çoğaltma kümesine sahiptir. Okuma ve yazma işlemleri, bir çoğaltmada (birincil olarak adlandırılır) gerçekleştirilir. Yazma işlemlerinden durum değişikliği, birden fazla kopyaya (etkin Ikincil adı verilir) çoğaltılır. Çoğaltma başarısız olduğunda, Service Fabric var olan çoğaltmalardan yeni bir çoğaltma oluşturur.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Service Fabric için durum bilgisi olan ve olmayan mikro hizmetler
Service Fabric, mikro hizmetlerden veya kapsayıcılardan oluşmuş uygulamalar hazırlamanıza olanak tanır. Durum bilgisi olmayan mikro hizmetler (protokol ağ geçitleri ve web ara sunucuları gibi), isteğin veya hizmetten gelen yanıtının dışında değişebilir bir durum bilgisi bulundurmaz. Azure Cloud Services çalışan rolleri, durum bilgisi olmayan hizmet örnekleridir. Durum bilgisi olan mikro hizmetler (kullanıcı hesapları, veritabanları, cihazlar, alışveriş sepetleri ve kuyruklar gibi), isteğin ve yanıtının ötesinde değişebilir, yetkili bir durum bilgisi bulundurur. Günümüzün İnternet ölçeğindeki uygulamaları, durum bilgisi olan ve olmayan mikro hizmetlerin bileşiminden oluşur. 

Service Fabric bir temel fark, [yerleşik programlama modelleriyle](service-fabric-choose-framework.md) ya da Kapsayıcılı durum bilgisi olan hizmetler ile durum bilgisi olan hizmetler oluşturmaya yönelik güçlü bir odadır. [Uygulama senaryoları](service-fabric-application-scenarios.md), durum bilgisi olan hizmetlerin kullanıldığı senaryoları açıklar.

Durum bilgisi olmayan mikro hizmetleri neden durum bilgisiz ile birlikte? İki temel neden şunlardır:

* Kod ve verileri aynı makinede kapatarak yüksek aktarım hızı, düşük gecikme süreli, hataya dayanıklı çevrimiçi işlem (OLTP) hizmetleri oluşturabilirsiniz. Etkileşimli stoınronth, arama, Nesnelerin İnterneti (IoT) sistemleri, ticaret sistemleri, kredi kartı işleme ve sahtekarlık algılama sistemleri ve kişisel kayıt yönetimi bazı örneklere örnektir.
* Uygulama tasarımını kolaylaştırabilirsiniz. Durum bilgisi olan mikro hizmetler, tamamen durum bilgisiz bir uygulamanın kullanılabilirlik ve gecikme süresi gereksinimlerini karşılamak için geleneksel olarak gereken ek kuyruklar ve önbellekler gereksinimini ortadan kaldırır. Durum bilgisi olan hizmetler doğal olarak yüksek kullanılabilirlik ve düşük gecikme süreli olduğundan uygulamanızda bir bütün olarak yönetilecek hareketli parçaların sayısını azaltır.

## <a name="supported-programming-models"></a>Desteklenen programlama modelleri
Service Fabric hizmetlerinizi yazmak ve yönetmek için birçok yol sunar. Hizmetler, platformun özelliklerinden ve uygulama çerçevelerinden tam olarak yararlanmak için Service Fabric API 'Lerini kullanabilir. Hizmetler Ayrıca herhangi bir dilde yazılmış ve Service Fabric kümesinde barındırılan bir derlenmiş yürütülebilir program olabilir. Daha fazla bilgi için bkz. [desteklenen programlama modelleri](service-fabric-choose-framework.md).

### <a name="containers"></a>Kapsayıcılar
Varsayılan olarak, Service Fabric Hizmetleri işlem olarak dağıtır ve etkinleştirir. Service Fabric Ayrıca, [kapsayıcılardaki](service-fabric-containers-overview.md)Hizmetleri dağıtabilir. Daha önemlisi, aynı uygulamadaki kapsayıcılardaki süreçler ve hizmetlerde Hizmetleri karıştırabilirsiniz. Service Fabric, Windows Server 2016 ' de Linux kapsayıcıları ve Windows kapsayıcıları dağıtımını destekler. Mevcut uygulamaları, durum bilgisi olmayan hizmetleri veya kapsayıcılarda durum bilgisi olmayan Hizmetleri dağıtabilirsiniz. 

### <a name="reliable-services"></a>Reliable Services
[Reliable Services](service-fabric-reliable-services-introduction.md) , Service Fabric platformuyla tümleştirilen ve platform özelliklerinin tam kümesinden faydalanabilecek Hizmetleri yazmak için basit bir çerçevedir. Reliable Services durum bilgisiz olabilir (Web sunucuları veya Azure Cloud Services çalışan rolleri gibi çoğu hizmet platformlarına benzer); burada durum, Azure DB veya Azure Tablo depolaması gibi bir dış çözümde kalıcı hale getirilir. Reliable Services durum bilgisi olabilir, burada durum, güvenilir koleksiyonlar kullanılarak doğrudan hizmette kalıcı hale getirilir. Durum, çoğaltma aracılığıyla [yüksek oranda kullanılabilir](service-fabric-availability-services.md) hale getirilir ve [bölümleme](service-fabric-concepts-partitioning.md)aracılığıyla dağıtılır, Service Fabric tarafından otomatik olarak yönetilir.

### <a name="reliable-actors"></a>Reliable Actors
Reliable Services üzerine inşa edilen [güvenilir aktör](service-fabric-reliable-actors-introduction.md) çerçevesi, aktör tasarım düzenine göre sanal aktör modelini uygulayan bir uygulama çerçevesidir. Güvenilir aktör çerçevesi, aktör olarak adlandırılan tek iş parçacıklı yürütme ile bağımsız işlem ve durum birimleri kullanır. Güvenilir aktör çerçevesi, aktörler ve önceden ayarlanmış durum kalıcılığı ve genişleme yapılandırmalarına yönelik yerleşik iletişim sağlar.

### <a name="aspnet-core"></a>ASP.NET Core
Service Fabric Web ve API uygulamaları oluşturmak için birinci sınıf programlama modeli olarak [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) tümleştirilir.  ASP.NET Core, Service Fabric iki farklı şekilde kullanılabilir:

- Konuk yürütülebilir dosyası olarak barındırılır. Bu, birincil olarak, mevcut ASP.NET Core uygulamalarını kod değişikliği olmadan Service Fabric üzerinde çalıştırmak için kullanılır.
- Güvenilir bir hizmetin içinde çalıştırın. Bu, Service Fabric çalışma zamanıyla daha iyi tümleştirme sağlar ve durum bilgisi olan ASP.NET Core hizmetlerine izin verir.

### <a name="guest-executables"></a>Konuk yürütülebilir dosyaları
[Konuk yürütülebilir dosyası](service-fabric-guest-executables-introduction.md) , diğer hizmetlerle birlikte Service Fabric kümesinde barındırılan mevcut, rastgele bir yürütülebilir dosyadır (herhangi bir dilde yazılmış). Konuk yürütülebilir dosyaları, Service Fabric API 'Leriyle doğrudan tümleştirmez. Ancak, REST API 'Leri çağırarak özel sistem durumu ve yük raporlama ve hizmet bulunabilirliği gibi platform tekliflerinin özelliklerinden de faydalanır. Ayrıca, tam uygulama yaşam döngüsü desteği de vardır. 

## <a name="application-lifecycle"></a>Uygulama yaşam döngüsü
Diğer platformlarda olduğu gibi, Service Fabric bir uygulama genellikle şu aşamalardan geçer: tasarım, geliştirme, test, dağıtım, yükseltme, bakım ve kaldırma. Service Fabric bulut uygulamalarının tam uygulama yaşam döngüsü için, dağıtım, günlük yönetim ve son kullanımdan kaldırma ile bakım aracılığıyla geliştirme aşamasından ilk sınıf destek sağlar. Hizmet modeli birçok farklı rolün uygulama yaşam döngüsüne bağımsız olarak katılmasına olanak sağlar. [Service Fabric uygulama yaşam döngüsü](service-fabric-application-lifecycle.md) , API 'lerin yanı sıra Service Fabric uygulama yaşam döngüsünün aşamaları boyunca farklı roller tarafından nasıl kullanıldıklarından bir genel bakış sağlar. 

Tüm uygulama yaşam döngüsü [PowerShell cmdlet](/powershell/module/servicefabric/?preserve-view=true&view=azureservicefabricps)'leri, [CLI komutları](service-fabric-sfctl.md), [C# API](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)'Leri, [Java API 'leri](/java/api/overview/azure/servicefabric)ve [REST API](/rest/api/servicefabric/)'leri kullanılarak yönetilebilir. Ayrıca, [Azure Pipelines](./service-fabric-tutorial-deploy-app-with-cicd-vsts.md) veya [Jenkins](/azure/developer/jenkins/deploy-to-service-fabric-cluster)gibi araçları kullanarak sürekli tümleştirme/sürekli dağıtım işlem hatları da ayarlayabilirsiniz.

## <a name="test-applications-and-services"></a>Uygulama ve hizmetleri test etme
Gerçekten bulut ölçekli hizmetler oluşturmak için, uygulamalarınızın ve hizmetlerinizin gerçek hayatta hatalara sahip olduğunu doğrulamak önemlidir. Hata analiz hizmeti, Service Fabric oluşturulan Hizmetleri test etmek için tasarlanmıştır. [Hata analiz hizmeti](service-fabric-testability-overview.md)ile, anlamlı hatalara sahip olabilir ve uygulamalarınızda test senaryolarını tamamen çalıştırabilirsiniz. Bu hatalar ve senaryolar, bir hizmetin yaşam süresi boyunca, denetimli, güvenli ve tutarlı bir şekilde deneyilecek çok sayıda durum ve geçiş yapar ve doğrular.

[Eylemler](service-fabric-testability-actions.md) , bireysel hataları kullanarak test için bir hizmeti hedefleyin. Bir hizmet geliştiricisi, bunları karmaşık senaryolar yazmak için yapı taşları olarak kullanabilir. Benzetimli hata örnekleri şunlardır:

* Bir makinenin veya VM 'nin yeniden başlatıldığı sayıda durum benzetimi yapmak için bir düğümü yeniden başlatın.
* Yük Dengeleme, yük devretme veya uygulama yükseltme benzetimi yapmak için durum bilgisi olan hizmetinizin bir çoğaltmasını taşıyın.
* Yeni verileri kabul etmek için yeterli "yedekleme" veya "ikincil" çoğaltma olmadığından yazma işlemlerinin devam edemeyeceği bir durum oluşturmak için durum bilgisi olan bir hizmette çekirdek kaybı 'nı çağırın.
* Tüm bellek içi durumun tamamen temizlendiğinden oluşan bir durum oluşturmak için durum bilgisi olan bir hizmette veri kaybını çağırma.

[Senaryolar](service-fabric-testability-scenarios.md) , bir veya daha fazla eylemden oluşan karmaşık işlemlerdir. Hata Analizi hizmeti, iki yerleşik tam senaryo sağlar:

* [Chaos senaryosu](service-fabric-controlled-chaos.md)-kümede uzun süre boyunca sürekli ve araya eklemeli hataların (hem düzgün kapanma hem de düzgün şekilde değil) benzetimini yapar.
* [Yük devretme senaryosu](service-fabric-testability-scenarios.md#failover-test)-diğer hizmetlerden etkilenirken belirli bir hizmet bölümünü hedefleyen Chaos test senaryosunun bir sürümüdür.

## <a name="clusters"></a>Kümeler
[Service Fabric küme](service-fabric-deploy-anywhere.md) , mikro hizmetlerinizin dağıtıldığı ve yönetildiği, ağa bağlı bir sanal veya fiziksel makine kümesidir. Kümeler binlerce makine içerecek şekilde ölçeklendirilebilir. Bir kümenin parçası olan makineye veya VM 'ye küme düğümü denir. Her düğüme bir düğüm adı (bir dize) atanır. Düğümlerin yerleşim özellikleri gibi özellikleri vardır. Her makine veya VM, `FabricHost.exe` önyükleme sonrasında çalışmaya başlayan ve sonra iki yürütülebilir dosya Başlatan bir otomatik başlatma hizmetine sahiptir: Fabric.exe ve FabricGateway.exe. Bu iki yürütülebilir dosya düğümü yapar. Test senaryoları için birden fazla ve birden çok örneğini çalıştırarak tek bir makinede veya VM 'de birden çok düğümü barındırabilirsiniz `Fabric.exe` `FabricGateway.exe` .

Service Fabric kümeler, Windows Server veya Linux çalıştıran sanal veya fiziksel makinelerde oluşturulabilir. Birbirine bağlı bir Windows Server veya Linux bilgisayar kümesine sahip olduğunuz herhangi bir ortamda Service Fabric uygulamalar dağıtabilir ve çalıştırabilirsiniz: şirket içi, Microsoft Azure veya herhangi bir bulut sağlayıcısında.

### <a name="clusters-on-azure"></a>Azure’da kümeler
Azure 'da Service Fabric kümeleri çalıştırmak, diğer Azure özellikleriyle ve hizmetleriyle tümleştirme sağlar. Bu, kümenin işlemlerini ve yönetimini daha kolay ve güvenilir hale getirir. Küme bir Azure Resource Manager kaynağıdır, bu nedenle kümeleri Azure 'daki diğer kaynaklar gibi modelleyebilirsiniz. Kaynak Yöneticisi ayrıca, küme tarafından kullanılan tüm kaynakların tek bir birim olarak kolay yönetimi de sağlar. Azure üzerindeki kümeler Azure tanılama ve Azure Izleyici günlükleriyle tümleşiktir. Küme düğümü türleri, [sanal makine ölçek kümeleridir](../virtual-machine-scale-sets/index.yml), bu nedenle otomatik ölçeklendirme işlevselliği yerleşik olarak bulunur.

Azure 'da [Azure Portal](service-fabric-cluster-creation-via-portal.md), bir [şablondan](service-fabric-cluster-creation-via-arm.md)veya [Visual Studio](./service-fabric-cluster-creation-via-arm.md)'dan bir küme oluşturabilirsiniz.

Linux üzerinde Service Fabric, Linux üzerinde yüksek oranda kullanılabilir, yüksek düzeyde ölçeklenebilir uygulamaları Windows 'da olduğu gibi oluşturmanıza, dağıtmanıza ve yönetmenize olanak sağlar. Service Fabric çerçeveleri (Reliable Services ve Reliable Actors) C# ' ın yanı sıra Linux 'ta Java 'da (.NET Core) kullanılabilir. Ayrıca, herhangi bir dil veya çerçeveye [Konuk yürütülebilir hizmetler](service-fabric-guest-executables-introduction.md) de oluşturabilirsiniz. Docker kapsayıcılarını düzenleme de desteklenir. Docker kapsayıcıları, Service Fabric çerçeveleri kullanan Konuk yürütülebilir dosyaları veya yerel Service Fabric Hizmetleri çalıştırabilir. Daha fazla bilgi için [Linux üzerinde Service Fabric](service-fabric-deploy-anywhere.md)hakkında bilgi edinin.

Windows üzerinde desteklenen ancak Linux üzerinde olmayan bazı özellikler vardır. Daha fazla bilgi edinmek için [Linux ve Windows üzerinde Service Fabric arasındaki farkları](service-fabric-linux-windows-differences.md)okuyun.

### <a name="standalone-clusters"></a>Tek başına kümeler
Service Fabric, şirket içinde veya herhangi bir bulut sağlayıcısında tek başına Service Fabric kümeleri oluşturmanız için bir yükleme paketi sağlar. Tek başına kümeler, istediğiniz yerde bir kümeyi barındırmak için bir özgürlük sağlar. Verileriniz uyumluluk veya yönetmelik kısıtlamalarına tabidir veya verilerinizi yerel tutmak istiyorsanız kendi kümenizi ve uygulamalarınızı barındırabilirsiniz. Service Fabric uygulamalar, hiçbir değişiklik yapmadan birden çok barındırma ortamında çalışabilir, böylece uygulama oluşturma bilginiz bir barındırma ortamından diğerine taşınır. 

[İlk tek başına Service Fabric kümenizi oluşturma](service-fabric-cluster-creation-for-windows-server.md)

Linux tek başına kümeleri henüz desteklenmiyor.

### <a name="cluster-security"></a>Küme güvenliği
Özellikle de üzerinde çalışan üretim iş yükleri olduğunda, yetkisiz kullanıcıların kümenize bağlanmasını engellemek için kümelerin güvenliği sağlanmalıdır. Güvenli olmayan bir küme oluşturmak mümkün olsa da, bu, yönetim uç noktaları genel İnternet 'e sunulsa anonim kullanıcıların bu sunucuya bağlanmasına izin verir. Güvenli olmayan bir kümede güvenliği etkinleştirmek mümkün değildir: küme güvenliği küme oluşturma sırasında etkinleştirilir.

Küme güvenliği senaryoları şunlardır:
* Düğümden düğüme güvenlik
* İstemciden düğüme güvenlik
* Rol tabanlı erişim denetimi Service Fabric

Daha fazla bilgi için [bir kümeyi güvenli bir](service-fabric-cluster-security.md)şekilde okuyun.

### <a name="scaling"></a>Ölçeklendirme
Kümeye yeni düğümler eklerseniz, Bölüm çoğaltmaları ve örneklerinin artan düğüm sayısı genelinde yeniden dengelenmesi Service Fabric. Genel uygulama performansı, bellek düşüşlerine erişim için gelişir ve çekişmeyi geliştirir. Kümedeki düğümler verimli bir şekilde kullanılmıyorsa, kümedeki düğümlerin sayısını azaltabilirsiniz. Service Fabric, her düğümdeki donanımın daha iyi kullanılmasını sağlamak için bölüm çoğaltmalarını ve örnekleri, azaltılmış düğüm sayısı genelinde yeniden dengeler. Azure 'da kümeleri [el ile](service-fabric-cluster-scale-in-out.md) veya [programlama yoluyla](service-fabric-cluster-programmatic-scaling.md)ölçeklendirebilirsiniz. Tek başına kümeler [el ile](service-fabric-cluster-windows-server-add-remove-nodes.md)ölçeklendirilebilirler.

### <a name="cluster-upgrades"></a>Küme yükseltmeleri
Service Fabric çalışma zamanının yeni sürümleri düzenli aralıklarla yayımlanır. [Desteklenen bir sürümü](service-fabric-support.md)her zaman çalıştırmak için kümenizde çalışma zamanı veya yapı, yükseltme gerçekleştirin. Doku yükseltmelerine ek olarak, sertifikalar veya uygulama bağlantı noktaları gibi küme yapılandırmalarını da güncelleştirebilirsiniz.

Service Fabric küme, sahip olduğunuz ancak kısmen Microsoft tarafından yönetilen bir kaynaktır. Microsoft, temel alınan işletim sistemine düzeltme eki uygulama ve kümenizde yapı yükseltmeleri gerçekleştirmeye sorumludur. Kümenizi otomatik yapı yükseltmeleri alacak şekilde ayarlayabilir, Microsoft yeni bir sürüm yayınlar veya istediğiniz desteklenen bir yapı sürümünü seçebilirsiniz. Doku ve yapılandırma yükseltmeleri Azure portal veya Kaynak Yöneticisi aracılığıyla ayarlanabilir. Daha fazla bilgi için [Service Fabric kümesini yükseltme](service-fabric-cluster-upgrade.md)makalesini okuyun. 

Tek başına küme, tamamen sahip olduğunuz bir kaynaktır. Temel alınan işletim sisteminde düzeltme eki uygulama ve doku yükseltmeleri başlatma konusunda siz sorumlusunuz. Kümenizin bağlantısı varsa [https://www.microsoft.com/download](https://www.microsoft.com/download) , kümenizi yeni Service Fabric çalışma zamanı paketini otomatik olarak indirip sağlayacak şekilde ayarlayabilirsiniz. Ardından yükseltmeyi başlatabilirsiniz. Kümeniz erişemıyorsa [https://www.microsoft.com/download](https://www.microsoft.com/download) , internet 'e bağlı bir makineden yeni çalışma zamanı paketini el ile indirebilir ve ardından yükseltmeyi başlatabilirsiniz. Daha fazla bilgi için [tek başına Service Fabric kümesini yükseltme](service-fabric-cluster-upgrade-windows-server.md)makalesini okuyun.

## <a name="health-monitoring"></a>Sistem durumu izleme
Service Fabric, belirli varlıklara uygun olmayan kümeyi ve uygulama koşullarını işaretlemek için tasarlanan bir [sistem durumu modeli](service-fabric-health-introduction.md) tanıtır (küme düğümleri ve hizmet çoğaltmaları gibi). Sistem durumu modeli, sistem durumu reporlarını (sistem bileşenleri ve Watchdogs) kullanır. Amaç kolay ve hızlı tanılama ve onarma. Hizmet yazıcılarının sistem durumunu ve [sistem durumu raporlamasını nasıl tasarlayacağınızı](service-fabric-report-health.md#design-health-reporting)düşünmesine ihtiyacı vardır. Özellikle, sorunların köke yakın olduğunu bayrakladığında, sistem durumunu etkileyebilecek herhangi bir koşul tarihinde bildirilmelidir. Sistem durumu bilgileri, hizmet çalışır duruma getirildikten sonra, hata ayıklama ve araştırma konusunda zaman ve çaba tasarrufu sağlayabilir.

Service Fabric Raporlayıcıları, belirtilen ilgi koşullarını izler. Bunlar yerel görünümüne göre bu koşullara göre raporlarlar. [Sistem durumu deposu](service-fabric-health-introduction.md#health-store) , varlıkların küresel olarak sağlıklı olup olmadığını öğrenmek için tüm raporlayıcılar tarafından gönderilen sistem durumu verilerini toplar. Modelin zengin, esnek ve kullanımı kolay olması amaçlanmıştır. Sistem durumu raporlarının kalitesi, kümenin sistem durumu görünümünün doğruluğunu belirler. Hatalı olmayan sorunları yanlışlıkla gösteren yanlış pozitif sonuçlar, yükseltmeleri veya sistem durumu verilerini kullanan diğer hizmetleri olumsuz yönde etkileyebilir. Bu hizmetlere örnek olarak onarım hizmetleri ve uyarı mekanizmaları verilebilir. Bu nedenle, olası en iyi şekilde ilgilendiğiniz koşulları yakalayan raporlar sağlamak için bazı düşünce gerekir.

Raporlama şunları yapabilir:
* İzlenen Service Fabric hizmeti çoğaltması veya örneği.
* Service Fabric bir hizmet olarak dağıtılan iç Watchdogs (örneğin, koşulları ve sorun raporlarını izleyen bir durum bilgisi olmayan Service Fabric hizmeti). Watchdogs tüm düğümlere dağıtılabilir veya izlenen hizmete eklenebilir.
* Service Fabric düğümlerinde çalışan ancak Service Fabric Hizmetleri olarak uygulanmayan iç Watchdogs.
* Kaynağı Service Fabric kümenin dışından yoklamış dış Watchdogs (örneğin, Gomez gibi izleme hizmeti).

Kutudan çıkan, bileşen Service Fabric kümedeki tüm varlıklarda sistem durumunu rapor ediyor. [Sistem durumu raporları](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) küme ve uygulama işlevlerine ilişkin görünürlük sağlar ve sistem durumu ile ilgili sorunları bayrakla ayırır. Uygulamalar ve hizmetler için sistem durumu raporları varlıkların uygulandığını ve Service Fabric çalışma zamanının perspektifinden doğru şekilde çalıştığını doğrular. Raporlar, hizmetin iş mantığını herhangi bir sistem durumu izlemesi veya yanıt vermeyi durdurmuş olan süreçler tespit etmez. Hizmetinizin mantığına özgü sistem durumu bilgileri eklemek için, hizmetlerinize [özel sistem durumu raporlaması uygulayın](service-fabric-report-health.md) .

Service Fabric sistem durumu deposunda toplanan [sistem durumu raporlarını görüntülemek](service-fabric-view-entities-aggregated-health.md) için birden çok yol sağlar:
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) veya diğer görselleştirme araçları.
* Sistem durumu sorguları ( [PowerShell](/powershell/module/servicefabric/?preserve-view=true&view=azureservicefabricps), [CLI](service-fabric-sfctl.md), [C# FabricClient API](/dotnet/api/system.fabric.fabricclient.healthclient) 'Leri ve [Java FabricClient API 'leri](/java/api/system.fabric)veya [REST API](/rest/api/servicefabric)'leri aracılığıyla).
* Özelliklerden biri (PowerShell, CLı, API 'Ler veya REST aracılığıyla) durumuna sahip varlıkların bir listesini döndüren genel sorgular.

## <a name="monitoring-and-diagnostics"></a>İzleme ve tanılama
[İzleme ve tanılama](service-fabric-diagnostics-overview.md) , her türlü ortamda uygulama ve hizmet geliştirmek, test etmek ve dağıtmak için önemlidir. Service Fabric çözümler, uygulama ve hizmetlerin yerel bir geliştirme ortamında veya üretimde beklendiği gibi çalıştığından emin olmanıza yardımcı olan izleme ve tanılamayı planlayıp uyguladığınızda en iyi şekilde çalışır.

İzleme ve Tanılamanın ana amaçları şunlardır:

- Donanım ve altyapı sorunlarını tespit edin ve tanılayın
- Yazılım ve uygulama sorunlarını algılama, hizmet kesinti süresini azaltma
- Kaynak tüketimini anlayın ve yardım sürücüsü işlemler kararları
- Uygulama, hizmet ve altyapı performansını iyileştirme
- İş öngörüleri oluşturun ve geliştirme alanını tanımla
 
İzleme ve Tanılamanın genel iş akışı üç adımdan oluşur:

1. Olay oluşturma: buna altyapı (küme), platform ve uygulama/hizmet düzeyinde Olaylar (Günlükler, izlemeler, özel olaylar) dahildir
2. Olay toplama: oluşturulan olayların görüntülenebilmesi için toplanması ve toplanması gerekir
3. Analiz: olayların Analize izin vermek ve gerektiğinde görüntülenmek üzere bir biçimde görselleştirildiği ve erişilebilir olması gerekir

Bu üç alanı kapsayan birden fazla ürün mevcuttur ve her biri için farklı teknolojiler seçebilirsiniz. Daha fazla bilgi için [Azure Service Fabric izleme ve tanılamayı](service-fabric-diagnostics-overview.md)okuyun.

## <a name="next-steps"></a>Sonraki adımlar
* [Azure’da küme](service-fabric-cluster-creation-via-portal.md) veya [Windows’ta tek başına küme](service-fabric-cluster-creation-for-windows-server.md) oluşturma hakkında bilgi edinin.
* [Reliable Services](service-fabric-reliable-services-quick-start.md) veya [Reliable Actors](service-fabric-reliable-actors-get-started.md) programlama modelini kullanan bir hizmet oluşturmayı deneyin.
* [Cloud Services geçiş](service-fabric-cloud-services-migration-differences.md)yapmayı öğrenin.
* [Hizmetleri izlemeyi ve tanılamayı](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)öğrenin. 
* [Uygulamalarınızı ve hizmetlerinizi test](service-fabric-testability-overview.md)etme hakkında bilgi edinin.
* [Küme kaynaklarını yönetmeyi ve](service-fabric-cluster-resource-manager-introduction.md)düzenlemeyi öğrenin.
* [Service Fabric örneklerine](/samples/browse/?products=azure)bakın.
* [Service Fabric destek seçenekleri](service-fabric-support.md)hakkında bilgi edinin.
* Makaleler ve duyurular için [ekip blogunu](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) okuyun.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png
