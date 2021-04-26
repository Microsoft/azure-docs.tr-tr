---
title: Azure Service Fabric terminolojisini öğrenin
description: Belgelerin geri kalanında kullanılan anahtar Service Fabric terminolojisini ve kavramları öğrenin.
author: masnider
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: masnider
ms.openlocfilehash: 2ac4b81a284ed8c38bc9cefccd08db5afa51d600
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575950"
---
# <a name="service-fabric-terminology-overview"></a>Service Fabric terimlere genel bakış

Azure Service Fabric; ölçeklenebilir ve güvenilir mikro hizmetleri paketlemeyi, dağıtmayı ve yönetmeyi kolaylaştırmayı sağlayan bir dağıtılmış sistemler platformudur.  [Service Fabric kümelerini her yerde barındırabilirsiniz](service-fabric-deploy-anywhere.md): Azure, şirket içi bir veri merkezinde veya herhangi bir bulut sağlayıcısında.  Service Fabric, [Azure Service Fabric ağı](../service-fabric-mesh/index.yml)destekleyen Orchestrator. Hizmetlerinizi yazmak için herhangi bir çerçeveyi kullanabilir ve uygulamayı birden çok ortam seçiminizden nerede çalıştıracağınızı seçebilirsiniz. Bu makalede, belgelerde kullanılan koşulları anlamak için Service Fabric tarafından kullanılan terminoloji ayrıntılı olarak açıklanır.

## <a name="infrastructure-concepts"></a>Altyapı kavramları

**Küme**: mikro hizmetlerinizin dağıtıldığı ve yönetildiği, ağa bağlı bir sanal veya fiziksel makine kümesi.  Kümeler binlerce makine içerecek şekilde ölçeklendirilebilir.

**Düğüm**: bir kümenin parçası olan makıne veya VM, *düğüm* olarak adlandırılır. Her düğüme bir düğüm adı (dize) atanır. Düğümlerin yerleştirme özellikleri gibi özellikleri vardır. Her makine veya VM, `FabricHost.exe` önyükleme sonrasında çalışmaya başlayan ve ardından iki yürütülebilir dosya Başlatan bir otomatik başlatma Windows hizmetine sahiptir: `Fabric.exe` ve `FabricGateway.exe` . Bu iki yürütülebilir dosya düğümü yapar. Test senaryoları için birden fazla ve birden çok örneğini çalıştırarak tek bir makinede veya VM 'de birden çok düğümü barındırabilirsiniz `Fabric.exe` `FabricGateway.exe` .

## <a name="application-and-service-concepts"></a>Uygulama ve hizmet kavramları

**Service Fabric kafes uygulaması**: Service Fabric ağ uygulamaları, kaynak MODELI (YAML ve JSON kaynak dosyaları) tarafından tanımlanır ve Service Fabric çalıştığı herhangi bir ortama dağıtılabilir.

Yerel **uygulama Service Fabric**: Service Fabric yerel uygulamalar, yerel uygulama MODELIYLE (XML tabanlı uygulama ve hizmet bildirimleri) açıklanır.  Service Fabric yerel uygulamalar Service Fabric ağ içinde çalıştırılamaz.

### <a name="service-fabric-mesh-application-concepts"></a>Service Fabric kafes uygulaması kavramları

**Uygulama**: bir uygulama, bir kafes uygulamasının dağıtım, sürüm oluşturma ve yaşam süresi birimidir. Her uygulama örneğinin yaşam döngüsü bağımsız olarak yönetilebilir.  Uygulamalar bir veya daha fazla hizmet kodu paketi ve ayarlarından oluşur. Bir uygulama, Azure kaynak modeli (RM) şeması kullanılarak tanımlanır.  Hizmetler, bir RM şablonundaki uygulama kaynağının özellikleri olarak açıklanır.  Uygulama tarafından kullanılan ağlara ve birimlere uygulama tarafından başvurulur.  Bir uygulama oluştururken, uygulama, hizmet ve birimler Service Fabric kaynak modeli kullanılarak modellenir.

**Hizmet**: bir uygulamadaki bir hizmet mikro hizmeti temsil eder ve bir tamamen ve tek başına işlevi gerçekleştirir. Her hizmet, kod paketiyle ilişkili kapsayıcı görüntüsünü çalıştırmak için gereken her şeyi tanımlayan bir veya daha fazla kod paketinden oluşur.  Bir uygulamadaki hizmetlerin sayısı yukarı ve aşağı ölçeklendirilebilir.

**Ağ**: bir ağ kaynağı, uygulamalarınız için özel bir ağ oluşturur ve buna başvurabilen uygulamalardan veya hizmetlerden bağımsızdır. Farklı uygulamalardan gelen birden çok hizmet aynı ağın parçası olabilir. Ağlar, uygulamalar tarafından başvurulan dağıtılabilir kaynaklardır.

**Kod paketi**: kod paketleri aşağıdakiler de dahil olmak üzere kod paketiyle ilişkili kapsayıcı görüntüsünü çalıştırmak için gereken her şeyi anlatmaktadır:

* Kapsayıcı adı, sürümü ve kayıt defteri
* Her kapsayıcı için gereken CPU ve bellek kaynakları
* Ağ uç noktaları
* Kapsayıcıda takılacak ve ayrı bir birim kaynağına başvuran birimler.

Bir uygulama kaynağının parçası olarak tanımlanan tüm kod paketleri, bir grup olarak birlikte dağıtılır ve etkinleştirilir.

**Birim**: birimler, durumu kalıcı hale getirmek için kullanabileceğiniz kapsayıcı örneklerinizin içine bağlanmış olan dizinlerdir. Azure dosyaları birim sürücüsü bir Azure dosyaları payını bir kapsayıcıya bağlar ve dosya depolamayı destekleyen API aracılığıyla güvenilir veri depolama sağlar. Birimler, uygulamalar tarafından başvurulan dağıtılabilir kaynaklardır.

### <a name="service-fabric-native-application-concepts"></a>Yerel uygulama kavramlarını Service Fabric

**Uygulama**: bir uygulama, belirli bir işlevi veya işlevleri gerçekleştiren bir Bileşen Hizmetleri koleksiyonudur. Her uygulama örneğinin yaşam döngüsü bağımsız olarak yönetilebilir.

**Hizmet**: bir hizmet, tüm ve tek başına işlevleri gerçekleştirir ve diğer hizmetlerden bağımsız olarak başlatılabilir ve çalıştırılabilir. Bir hizmet, koddan, yapılandırmadan ve verilerden oluşur. Her hizmet için, kod yürütülebilir ikili dosyalardan oluşur, yapılandırma çalışma zamanında yüklenebilecek hizmet ayarlarından oluşur ve veriler, hizmet tarafından tüketilen rastgele statik verilerden oluşur.

**Uygulama türü**: hizmet türleri koleksiyonuna atanan ad/sürüm. Bir `ApplicationManifest.xml` dosyada tanımlanır ve bir uygulama paketi dizinine katıştırılır. Dizin daha sonra Service Fabric kümesinin görüntü deposuna kopyalanır. Daha sonra küme içindeki bu uygulama türünden adlandırılmış bir uygulama oluşturabilirsiniz.

Daha fazla bilgi için [uygulama modeli](service-fabric-application-model.md) makalesini okuyun.

**Uygulama paketi**: uygulama türünün dosyasını içeren bir disk dizini `ApplicationManifest.xml` . Uygulama türünü oluşturan her bir hizmet türü için hizmet paketlerine başvurur. Uygulama paketi dizinindeki dosyalar Service Fabric kümenin görüntü deposuna kopyalanır. Örneğin, bir e-posta uygulama türü için uygulama paketi bir kuyruk hizmeti paketine, ön uç hizmet paketine ve bir veritabanı hizmeti paketine başvuru içerebilir.

**Adlandırılmış uygulama**: bir uygulama paketini görüntü deposuna kopyaladıktan sonra, küme içinde uygulamanın bir örneğini oluşturursunuz. Uygulama paketinin uygulama türünü, adını veya sürümünü kullanarak belirttiğinizde bir örnek oluşturursunuz. Her uygulama türü örneğine şöyle görünen bir Tekdüzen Kaynak tanımlayıcısı (URI) adı atanır: `"fabric:/MyNamedApp"` . Bir küme içinde, tek bir uygulama türünden birden çok adlandırılmış uygulama oluşturabilirsiniz. Farklı uygulama türlerinden adlandırılmış uygulamalar da oluşturabilirsiniz. Adlandırılmış her uygulama bağımsız olarak yönetilir ve sürümü oluşturulur.

**Hizmet türü**: bir hizmetin kod paketlerine, veri paketlerine ve yapılandırma paketlerine atanan ad/sürüm. Hizmet türü, `ServiceManifest.xml` dosyasında tanımlanır ve bir hizmet paketi dizinine katıştırılır. Daha sonra hizmet paketi dizinine bir uygulama paketi dosyası tarafından başvurulur `ApplicationManifest.xml` . Küme içinde, adlandırılmış bir uygulama oluşturduktan sonra uygulama türünün hizmet türlerinden birinden adlandırılmış bir hizmet oluşturabilirsiniz. Hizmet türünün `ServiceManifest.xml` dosyası hizmeti açıklar.

Daha fazla bilgi için [uygulama modeli](service-fabric-application-model.md) makalesini okuyun.

İki tür hizmet vardır:

* **Durum bilgisiz**: hizmetin kalıcı durumu Azure depolama, Azure SQL veritabanı veya Azure Cosmos DB gibi bir dış depolama hizmetinde depolandığında, durum bilgisi olmayan bir hizmet kullanın. Hizmetin kalıcı depolaması olmadığında durum bilgisi olmayan bir hizmet kullanın. Örneğin, değerlerin hizmete geçirildiği bir Hesaplayıcı hizmeti için, bu değerleri kullanan bir hesaplama gerçekleştirilir ve sonuç döndürülür.
* **Durum bilgisi**: hizmet durumunu güvenilir koleksiyonlar veya Reliable Actors programlama modelleri aracılığıyla yönetmek Service Fabric istediğinizde, durum bilgisi olan bir hizmet kullanın. Adlandırılmış bir hizmet oluşturduğunuzda, durumu ölçeklenebilirlik için kaç bölümden istediğinizi belirleyin. Ayrıca, güvenilirlik açısından, durumlarınızın düğümleri arasında kaç kez çoğaltılacağını belirtin. Her bir adlandırılmış hizmetin tek bir birincil çoğaltması ve birden çok ikincil çoğaltması vardır. Birincil çoğaltmaya yazarken, adlandırılmış hizmetin durumunu değiştirirsiniz. Service Fabric, durumu eşitlenmiş halde tutmak için bu durumu tüm ikincil çoğaltmalara çoğaltır. Service Fabric, birincil çoğaltmanın başarısız olduğu zaman otomatik olarak algılar ve var olan bir ikincil çoğaltmayı birincil bir çoğaltmaya yükseltir. Service Fabric sonra yeni bir ikincil çoğaltma oluşturur.  

**Çoğaltmalar veya örnekler** , dağıtılan ve çalışan bir hizmetin koduna (durum bilgisi olmayan hizmetler için durum) başvurur. Bkz. [çoğaltmalar ve örnekler](service-fabric-concepts-replica-lifecycle.md).

Yeniden **yapılandırma** , bir hizmetin çoğaltma kümesindeki herhangi bir değişikliğin işlemini ifade eder. Bkz. yeniden [yapılandırma](service-fabric-concepts-reconfiguration.md).

**Hizmet paketi**: hizmet türünün dosyasını içeren bir disk dizini `ServiceManifest.xml` . Bu dosya, hizmet türü için koda, statik verilere ve yapılandırma paketlerine başvurur. Hizmet paketi dizinindeki dosyalara uygulama türünün dosyası tarafından başvurulur `ApplicationManifest.xml` . Örneğin, bir hizmet paketi, bir veritabanı hizmetini oluşturan koda, statik verilere ve yapılandırma paketlerine başvurabilir.

**Adlandırılmış hizmet**: adlandırılmış bir uygulama oluşturduktan sonra, küme içindeki hizmet türlerinden birinin bir örneğini oluşturabilirsiniz. Hizmet türünü, adını/sürümünü kullanarak belirtirsiniz. Her hizmet türü örneğine, adlandırılmış uygulamasının URI 'sinin kapsamında bir URI adı atanır. Örneğin, "MyNamedApp" adlı uygulama içinde bir "MyDatabase" hizmeti oluşturursanız, URI şöyle görünür: `"fabric:/MyNamedApp/MyDatabase"` . Adlandırılmış bir uygulama içinde birkaç adlandırılmış hizmet oluşturabilirsiniz. Her bir adlandırılmış hizmet kendi bölüm şemasına, örneğine veya çoğaltma sayısına sahip olabilir.

**Kod paketi**: hizmet türünün yürütülebilir dosyalarını, genellikle exe/dll dosyalarını içeren bir disk dizini. Kod paketi dizinindeki dosyalara hizmet türünün dosyası tarafından başvurulur `ServiceManifest.xml` . Adlandırılmış bir hizmet oluşturduğunuzda, kod paketi, adlandırılmış hizmeti çalıştırmak için seçilen düğüme veya düğümlere kopyalanır. Ardından kod çalışmaya başlar. İki tür kod paketi yürütülebilir dosya vardır:

* **Konuk yürütülebilir dosyaları**: olarak çalışan yürütülebilir dosyalar, ana bilgisayar işletim sistemidir (Windows veya Linux). Bu yürütülebilir dosyalar, hiçbir Service Fabric çalışma zamanı dosyasına bağlantı vermez veya referans vermez, bu nedenle hiçbir Service Fabric programlama modeli kullanmaz. Bu yürütülebilir dosyalar, uç nokta keşfi için adlandırma hizmeti gibi bazı Service Fabric özelliklerini kullanamaz. Konuk yürütülebilir dosyaları her hizmet örneğine özgü yük ölçümlerini bildirebilirler.
* **Hizmet ana bilgisayarı yürütülebilir** dosyaları: Service Fabric çalışma zamanı dosyalarına bağlanarak Service Fabric programlama modellerini kullanan yürütülebilir dosyalar Service Fabric özellikleri etkinleştirir. Örneğin, adlandırılmış bir hizmet örneği uç noktaları Service Fabric Adlandırma Hizmeti kaydedebilir ve ayrıca yük ölçümlerini rapor edebilir.

**Veri paketi**: hizmet türünün statik, Salt okunabilir veri dosyalarını, genellikle fotoğraf, ses ve video dosyalarını içeren bir disk dizini. Veri paketi dizinindeki dosyalara hizmet türünün dosyası tarafından başvurulur `ServiceManifest.xml` . Adlandırılmış bir hizmet oluşturduğunuzda, veri paketi, adlandırılmış hizmeti çalıştırmak için seçilen düğüme veya düğümlere kopyalanır. Kod çalışmaya başlar ve artık veri dosyalarına erişebilir.

**Yapılandırma paketi**: hizmet türünün statik, salt okunurdur yapılandırma dosyalarını ve genellikle metin dosyalarını içeren bir disk dizini. Yapılandırma paketi dizinindeki dosyalara hizmet türünün dosyası tarafından başvurulur `ServiceManifest.xml` . Adlandırılmış bir hizmet oluşturduğunuzda, yapılandırma paketindeki dosyalar, adlandırılmış hizmeti çalıştırmak için seçilen bir veya daha fazla düğüme kopyalanır. Ardından kod çalışmaya başlar ve artık yapılandırma dosyalarına erişebilir.

**Kapsayıcılar**: varsayılan olarak, Service Fabric Hizmetleri işlem olarak dağıtır ve etkinleştirir. Service Fabric Ayrıca, kapsayıcı görüntülerinde Hizmetleri dağıtabilir. Kapsayıcılar, temel işletim sistemini uygulamalardan soyutlayan bir sanallaştırma teknolojisidir. Bir uygulama ve çalışma zamanı, bağımlılıkları ve sistem kitaplıkları bir kapsayıcı içinde çalışır. Kapsayıcı, kapsayıcının işletim sistemi yapılarının yalıtılmış görünümüne tam ve özel erişimi vardır. Service Fabric, Linux üzerinde Windows Server kapsayıcıları ve Docker Kapsayıcıları destekler. Daha fazla bilgi için [Service Fabric ve kapsayıcıları](service-fabric-containers-overview.md)okuyun.

**Bölüm şeması**: adlandırılmış bir hizmet oluşturduğunuzda, bir bölüm düzeni belirtirsiniz. Büyük miktarlı hizmetler, verileri kümenin düğümleri genelinde yayarak verileri bölümler arasında böler. Verileri bölümler arasında bölerek, adlandırılmış hizmetinizin durumu ölçeklendirebilir. Bir bölüm içinde, durum bilgisi olmayan adlandırılmış hizmetler örneklere sahiptir, ancak durum bilgisiz adlı hizmetler çoğaltmalarla Genellikle, durum bilgisi olmayan adlandırılmış hizmetlerin iç durumu olmadığından yalnızca bir bölüm vardır. Bölüm örnekleri kullanılabilirlik sağlar. Bir örnek başarısız olursa, diğer örnekler normal şekilde çalışmaya devam eder ve ardından Service Fabric yeni bir örnek oluşturur. Durum bilgisi olan adlandırılmış hizmetler, çoğaltmalarda durumlarını korur ve durum eşitlenmiş olarak tutulduğundan, her bölümün kendi çoğaltma kümesi vardır. Çoğaltma başarısız olduğunda, Service Fabric var olan çoğaltmalardan yeni bir çoğaltma oluşturur.

Daha fazla bilgi için [bölüm Service Fabric güvenilir hizmetler](service-fabric-concepts-partitioning.md) makalesini okuyun.

## <a name="system-services"></a>Sistem Hizmetleri

Service Fabric platform özelliklerini sağlayan her kümede oluşturulan sistem hizmetleri vardır.

**Adlandırma hizmeti**: her Service Fabric kümesi, hizmet adlarını kümedeki bir konuma çözümleyen bir adlandırma hizmeti sahiptir. Küme için bir İnternet etki alanı adı sistemi (DNS) gibi hizmet adlarını ve özelliklerini yönetirsiniz. İstemciler, bir hizmet adını ve konumunu çözümlemek için Adlandırma Hizmeti kullanarak kümedeki herhangi bir düğümle güvenli bir şekilde iletişim kurar. Uygulamalar küme içinde taşınır. Örneğin, bunun nedeni hatalardan, kaynak dengelemesinden veya kümenin yeniden boyutlandırılması olabilir. Geçerli ağ konumunu çözümlemek için hizmet ve istemciler geliştirebilirsiniz. İstemciler, çalışmakta olan gerçek makine IP adresini ve bağlantı noktasını elde eder.

Adlandırma Hizmeti ile birlikte çalışan istemci ve hizmet iletişim API 'Leri hakkında daha fazla bilgi için [hizmetlerle Iletişim kurun](service-fabric-connect-and-communicate-with-services.md) makalesini okuyun.

**Görüntü deposu hizmeti**: her Service Fabric kümesi dağıtılan, sürümlü uygulama paketlerinin tutulduğu bir görüntü deposu hizmetine sahiptir. Bir uygulama paketini Görüntü Deposu kopyalayın ve ardından bu uygulama paketinin içine dahil olan uygulama türünü kaydedin. Uygulama türü sağlandıktan sonra, bundan sonra bir adlandırılmış uygulama oluşturursunuz. Tüm adlandırılmış uygulamalar silindikten sonra, Görüntü Deposu hizmetinden bir uygulama türünün kaydını kaldırabilirsiniz.

Görüntü Deposu hizmeti hakkında daha fazla bilgi için [ımabtoreconnectionstring ayarını](service-fabric-image-store-connection-string.md) okuyun.

Uygulamaları Görüntü Deposu hizmetine dağıtma hakkında daha fazla bilgi için [uygulama dağıtma](service-fabric-deploy-remove-applications.md) makalesini okuyun.

**Yük Devretme Yöneticisi hizmeti**: her Service Fabric kümesi, aşağıdaki eylemlerden sorumlu bir yük devretme Yöneticisi hizmetine sahiptir:

 - Yüksek kullanılabilirlik ve hizmet tutarlılığı ile ilgili işlevler gerçekleştirir.
 - Uygulama ve küme yükseltmelerini düzenler.
 - Diğer sistem bileşenleriyle etkileşime girer.

**Onarım Yöneticisi hizmeti**: Bu, güvenli, automatable ve saydam bir şekilde bir kümede onarım eylemlerinin gerçekleştirilmesini sağlayan isteğe bağlı bir sistem hizmetidir. Onarım Yöneticisi, ' de kullanılır:

   - [Gümüş ve altın dayanıklılık](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) Azure Service Fabric kümelerinde Azure bakım onarımları gerçekleştirme.
   - [Düzeltme Eki düzenleme uygulaması](service-fabric-patch-orchestration-application.md) için onarım eylemlerini yürütme

## <a name="deployment-and-application-models"></a>Dağıtım ve uygulama modelleri

Hizmetlerinizi dağıtmak için, bunların nasıl çalıştırılacağını açıklamanız gerekir. Service Fabric üç farklı dağıtım modelini destekler:

### <a name="resource-model-preview"></a>Kaynak modeli (Önizleme)

Service Fabric kaynaklar Service Fabric için tek tek dağıtılabilecek her şeydir; uygulamalar, hizmetler, ağlar ve birimler dahil. Kaynaklar bir küme uç noktasına dağıtılabilecek bir JSON dosyası kullanılarak tanımlanır.  Service Fabric ağ için, Azure kaynak modeli şeması kullanılır. Bir YAML dosya şeması, tanım dosyalarını daha kolay yazmak için de kullanılabilir. Kaynaklar Service Fabric her yerde dağıtılabilir. Kaynak modeli, Service Fabric uygulamalarınızı tanımlamanın en kolay yoludur. Ana odağı Kapsayıcılı hizmetlerin basit dağıtım ve yönetimine dayanır. Daha fazla bilgi edinmek için [Service Fabric kaynak modeline giriş](../service-fabric-mesh/service-fabric-mesh-service-fabric-resources.md)makalesini okuyun.

### <a name="native-model"></a>Yerel model

Yerel uygulama modeli, uygulamalarınızı Service Fabric için tam alt düzey erişim sağlar. Uygulamalar ve hizmetler, XML bildirim dosyalarında kayıtlı türler olarak tanımlanır.

Yerel model, C# ve Java 'daki Service Fabric çalışma zamanı API 'Lerine ve küme yönetim API 'Lerine erişim sağlayan Reliable Services ve Reliable Actors çerçeveleri destekler. Yerel model ayrıca rastgele kapsayıcıları ve yürütülebilir dosyaları destekler. Yerel model [Service Fabric kafes ortamında](../service-fabric-mesh/service-fabric-mesh-overview.md)desteklenmez.

**Reliable Services**: durum bilgisiz ve durum bilgisi olan hizmetler oluşturmak IÇIN bir API. Durum bilgisi olan hizmetler, durumlarını sözlük veya kuyruk gibi güvenilir koleksiyonlar halinde depolar. Ayrıca, Web API 'SI ve Windows Communication Foundation (WCF) gibi çeşitli iletişim yığınlarını da takabilirsiniz.

**Reliable Actors**: sanal aktör programlama modeli aracılığıyla durum bilgisiz ve durum bilgisi olan nesneleri derlemek IÇIN bir API. Bu model, çok sayıda bağımsız hesaplama veya durum birimi olduğunda faydalıdır. Bu model, bir çift tabanlı iş parçacığı modeli kullanır; bu nedenle, tek bir aktör tüm giden istekleri tamamlanana kadar diğer gelen istekleri işleyemediği için, diğer aktörlerin veya hizmetlere çağrı yapan kodun önüne geçmek en iyisidir.

Ayrıca, mevcut uygulamalarınızı Service Fabric de çalıştırabilirsiniz:

**Kapsayıcılar**: Service Fabric, windows Server 2016 ' de Linux ve Windows Server kapsayıcılarındaki Docker Kapsayıcıları dağıtımını destekler ve Hyper-V yalıtım modu desteğiyle birlikte. Service Fabric [uygulama modelinde](service-fabric-application-model.md)bir kapsayıcı, birden fazla hizmet çoğaltmalarının yerleştirildiği bir uygulama konağını temsil eder. Service Fabric herhangi bir kapsayıcıyı çalıştırabilir ve senaryo, mevcut bir uygulamayı bir kapsayıcı içinde paketettiğiniz Konuk yürütülebilir senaryosuna benzerdir. Ayrıca, [kapsayıcılar içinde Service Fabric hizmetleri de çalıştırabilirsiniz](service-fabric-services-inside-containers.md) .

**Konuk yürütülebilir dosyaları**: Azure Service Fabric hizmet olarak Node.js, Python, Java veya C++ gibi herhangi bir tür kodu çalıştırabilirsiniz. Service Fabric, durum bilgisi olmayan hizmetler olarak kabul edilen konuk yürütülebilir dosyaları olarak bu hizmet türlerine başvurur. Bir Service Fabric kümesinde Konuk yürütülebilir dosyası çalıştırmanın avantajları arasında yüksek kullanılabilirlik, sistem durumu izleme, uygulama yaşam döngüsü yönetimi, yüksek yoğunluklu ve bulunabilirliği bulunur.

Daha fazla bilgi için, [hizmetiniz için bir programlama modeli seçin](service-fabric-choose-framework.md) makalesini okuyun.

### <a name="docker-compose"></a>Docker Compose 

[Docker Compose](https://docs.docker.com/compose/) Docker projesinin bir parçasıdır. Service Fabric [, Docker Compose modelini kullanarak uygulama dağıtmaya](service-fabric-docker-compose.md)yönelik sınırlı destek sağlar.

## <a name="environments"></a>Ortamlar

Service Fabric, birçok farklı hizmet ve ürünün temel aldığı açık kaynaklı bir platform teknolojisidir. Microsoft aşağıdaki seçenekleri sağlar:

 - **Azure Service Fabric ağı**: Microsoft Azure Service Fabric uygulamaları çalıştırmak için tam olarak yönetilen bir hizmet.
 - **Azure Service Fabric**: Azure 'da barındırılan Service Fabric kümesi sunumu. Service Fabric kümelerinin yükseltme ve yapılandırma yönetimiyle birlikte Service Fabric ve Azure altyapısı arasında tümleştirme sağlar.
 - **Tek başına Service Fabric**: [Service Fabric kümelerini her yerde](./service-fabric-deploy-anywhere.md) (Şirket içinde veya herhangi bir bulut sağlayıcısında) dağıtmak için bir yükleme ve yapılandırma araçları kümesi. Azure tarafından yönetilmiyor.
 - **Service Fabric geliştirme kümesi**: Service Fabric uygulamaların geliştirilmesi için Windows, Linux veya Mac 'te yerel bir geliştirme deneyimi sağlar.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Ortam, çerçeve ve dağıtım modeli destek matrisi

Farklı ortamların çerçeveler ve dağıtım modelleri için farklı düzeylerde destek vardır. Aşağıdaki tabloda desteklenen Framework ve dağıtım modeli birleşimleri açıklanmaktadır.

| Uygulama türü | Açıklanan | Azure Service Fabric ağı | Azure Service Fabric kümeleri (herhangi bir işletim sistemi)| Yerel küme | Tek başına küme |
|---|---|---|---|---|---|
| Service Fabric kafes uygulamaları | Kaynak modeli (YAML & JSON) | Desteklenir |Desteklenmez | Windows-desteklenen, Linux ve Mac-desteklenmez | Windows-desteklenmiyor |
|Yerel uygulamalar Service Fabric | Yerel uygulama modeli (XML) | Desteklenmiyor| Desteklenir|Desteklenir|Windows-destekleniyor|

Aşağıdaki tabloda, Service Fabric karşı farklı uygulama modelleri ve araçları açıklanmaktadır.

| Uygulama türü | Açıklanan | Visual Studio | Eclipse | SFCTL | AZ CLı | PowerShell|
|---|---|---|---|---|---|---|
| Service Fabric kafes uygulamaları | Kaynak modeli (YAML & JSON) | VS 2017 |Desteklenmez |Desteklenmez | Yalnızca desteklenen ağ ortamı | Desteklenmiyor|
|Yerel uygulamalar Service Fabric | Yerel uygulama modeli (XML) | VS 2017 ve VS 2015| Desteklenir|Desteklenir|Desteklenir|Desteklenir|

## <a name="next-steps"></a>Sonraki adımlar

Service Fabric hakkında daha fazla bilgi edinmek için:

* [Service Fabric’e Genel Bakış](service-fabric-overview.md)
* [Uygulamaları oluşturmak için neden mikro hizmetler yaklaşımı öneriliyor?](service-fabric-overview-microservices.md)
* [Uygulama senaryoları](service-fabric-application-scenarios.md)

Service Fabric ağ hakkında daha fazla bilgi edinmek için:

* [Service Fabric ağı 'na genel bakış](../service-fabric-mesh/service-fabric-mesh-overview.md)
