Azure bulut çözümlerinin altyapısını, yazılım dağıtımlarının çevik olarak paketlenmesine ve kaynakların fiziksel donanımlara göre daha iyi birleştirilmesine imkan tanıyan sanal makineler (fiziksel bilgisayar donanımı öykünmesi) oluşturur. [Docker](https://www.docker.com) kapsayıcıları ve docker ekosistemi sayesinde dağıtılmış yazılımları geliştirme, teslim etme ve yönetme olanaklarınız önemli ölçüde arttı. Bir kapsayıcıdaki uygulama kodu, konak sanal makineden ve aynı sanal makinedeki diğer kapsayıcılardan yalıtılır. Bu yalıtım sayesinde daha fazla geliştirme ve dağıtım çevikliğine sahip olursunuz.

Azure aşağıdaki Docker değerlerini sunar:

* İçinde bulunduğunuz duruma uygun Docker konakları oluşturmak için [birçok](../articles/virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) [farklı](../articles/virtual-machines/linux/dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) yol
* [Azure Container Service](https://azure.microsoft.com/documentation/services/container-service/), **marathon** ve **swarm** gibi düzenleyicileri kullanarak kapsayıcı konaklarından oluşan kümeler oluşturur.
* Karmaşık dağıtılmış uygulamaların dağıtımını ve güncelleştirilmesini basitleştirmek için [Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md) ve [kaynak grubu şablonları](../articles/resource-group-authoring-templates.md)
* Hem özel hem de açık kaynaklı bir çok farklı yapılandırma yönetim aracı ile tümleştirme

Üstelik, Azure’da program aracılığıyla sanal makineler ve Linux kapsayıcıları oluşturabileceğinizden, sanal makine ve kapsayıcı *düzenleme* araçlarını kullanarak Sanal Makine (VM) grupları oluşturabilir ve hem Linux kapsayıcıları hem de artık [Windows Kapsayıcıları](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview) içinde uygulama dağıtabilirsiniz.

Bu makale yalnızca bu kavramlara yönelik üst düzey bir açıklama yapmakla kalmaz ve Azure’da kapsayıcı ve küme kullanımıyla ilgili daha fazla bilgi, öğreticiler ve ürünler için birçok bağlantı içerir. Bunların hepsini biliyorsanız ve yalnızca bağlantıları görmek istiyorsanız [kapsayıcılarla çalışmaya yönelik araçlar](#tools-for-working-with-azure-vms-and-containers) bölümüne gidin.

## <a name="the-difference-between-virtual-machines-and-containers"></a>Sanal makineler ile kapsayıcılar arasındaki fark
Sanal makineler, bir [hiper yönetici](http://en.wikipedia.org/wiki/Hypervisor) tarafından sağlanan yalıtılmış bir donanım sanallaştırma ortamı içinde çalışır. Azure’da, [Sanal Makineler](https://azure.microsoft.com/services/virtual-machines/) hizmeti tüm bu işlemleri sizin yerinize halleder: Sizin yapmanız gereken, işletim sistemini seçmek ve özel VM görüntüsü yapılandırarak (veya bir özel VM görüntüsünü karşıya yükleyerek) Sanal Makineleri oluşturmaktır. Sanal Makineler zaman içinde kabul görmüş, “çok badireler atlatmış” bir teknolojidir ve içerdikleri işletim sistemi ve uygulamaları yönetmeye yönelik birçok araç vardır.  Bir VM’deki uygulamalar konak işletim sisteminden gizlenir. Bir VM üzerindeki uygulama veya kullanıcı açısından bakıldığında, VM bağımsız bir fiziksel bilgisayar olarak görünür.

[Linux kapsayıcıları](http://en.wikipedia.org/wiki/LXC) ve docker araçları kullanılarak oluşturulan ve barındırılan kapsayıcılar, yalıtım sağlamak için bir hiper yönetici kullanmaz. Kapsayıcılar ile, kapsayıcı konağı Linux çekirdeğinin işlem ve dosya sistemi yalıtma özelliklerini kullanarak bunları kapsayıcı, uygulamaları, bazı çekirdek özellikleri ve kendi yalıtılmış dosya sisteminin kullanımına sunar. Bir kapsayıcı içinde çalışmakta olan uygulama açısından bakıldığında, kapsayıcı benzersiz bir işletim sistemi örneği olarak görünür. Kapsanan bir uygulama, kapsayıcısı dışındaki işlemleri veya diğer kaynakları göremez.

Bir Docker kapsayıcısında, bir VM’dekinden çok daha az kaynak kullanılır. Docker kapsayıcıları, Docker konağının çekirdeğini paylaşmayan bir uygulama yalıtım ve yürütme modeli kullanır. Kapsayıcı tüm işletim sistemini içermediği için daha düşük bir disk kullanım alanına sahiptir. Bir VM’ye göre başlangıç süresi ve gerekli disk alanı çok daha düşüktür.
Windows Kapsayıcıları, Windows’da çalışan uygulamalar için Linux kapsayıcılarıyla aynı avantajları sunar. Windows Kapsayıcıları Docker görüntü biçimini ve Docker API’yi destekler, ancak PowerShell kullanılarak da yönetilebilir. Windows Kapsayıcılarıyla iki kapsayıcı çalışma zamanı kullanılabilir: Windows Server Kapsayıcıları ve Hyper-V Kapsayıcıları. Hyper-V Kapsayıcıları, her kapsayıcıyı maksimum düzeyde iyileştirilmiş bir sanal makinede barındırarak fazladan bir yalıtım katmanı sağlar. Windows Kapsayıcıları hakkında daha fazla bilgi edinmek için bkz. [Windows Kapsayıcıları hakkında](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview). Azure’da Windows Kapsayıcılarını kullanmaya başlamak için [Azure Container Service kümesi dağıtmayı](/articles/container-service/container-service-deployment.md) öğrenin.

## <a name="what-are-containers-good-for"></a>Kapsayıcılar hangi alanlarda kullanıma uygundur?

Kapsayıcılar şu iyileştirmeleri yapabilir:

* Hızlı uygulama kodu geliştirilip yaygın olarak paylaşılabilir
* Bir uygulamanın test edildiği hız ve güvenirlik
* Bir uygulamanın dağıtıldığı hız ve güvenirlik

Kapsayıcılar, bir kapsayıcı konağı (işletim sistemi) ile Azure üzerinde, yani bir Azure Sanal Makinesinde yürütülür. Kapsayıcılar konusunda çoktan tatmin olmuş olsanız bile kapsayıcıları barındıran bir sanal makine altyapısına olan ihtiyacınız ortadan kalkmaz; ancak bu konudaki avantajınız, kapsayıcıların sanal makine ayrımı yapmamasıdır (tabii ki kapsayıcının yürütme ortamı olarak Linux mu yoksa Windows mu istediği gibi ayrıntılar önemlidir).


## <a name="what-are-containers-good-for"></a>Kapsayıcılar hangi alanlarda kullanıma uygundur?
Kapsayıcılar birçok şey için uygun olsa da&mdash;[Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) ve [Azure Service Fabric](../articles/service-fabric/service-fabric-overview.md)&mdash; için de geçerli olduğu gibi tek hizmetli, mikro hizmet odaklı dağıtılmış uygulamalar oluşturulmasını teşvik ederler. Bu modelde, uygulama tasarımı büyük ve daha sıkı şekilde bağlı bileşenler yerine daha küçük, birleştirilebilen parçaları temel alır.

Bu durum, özellikle de dilediğiniz zaman ve yerde sanal makine kiralayabileceğiniz Azure gibi genel bulut ortamları için geçerlidir. Yalnızca yalıtım, hızlı dağıtım ve düzenleme araçlarına sahip olmakla kalmaz, daha verimli uygulama altyapısı kararları alabilirsiniz.

Örneğin, yüksek oranda kullanılabilir ve dağıtılmış bir uygulama için büyük boyutlu 9 Azure sanal makinesinden bir dağıtımınız var diyelim. Bu uygulamanın bileşenleri kapsayıcılarda dağıtılabilirse yalnızca 4 sanal makine kullanmanın yanı sıra yedeklilik ve yük dengeleme için uygulama bileşenlerinizi 20 kapsayıcıda dağıtma olanağına sahip olabilirsiniz.

Doğal olarak bu yalnızca bir örnek, ancak senaryonuzda bunu yapabiliyorsanız daha fazla Azure sanal makinesi yerine daha fazla kapsayıcı ile ani kullanım artışlarına uyum sağlayabilir ve geriye kalan toplam CPU yükünü öncekine oranla çok daha verimli bir şekilde kullanabilirsiniz.

Ayrıca, mikro hizmet yaklaşımına uygun olmayan birçok senaryo olduğundan, mikro hizmetlerin ve kapsayıcıların işinize yarayıp yaramayacağını en iyi siz bilirsiniz.

### <a name="container-benefits-for-developers"></a>Geliştiriciler için kapsayıcı avantajları
Genel olarak kapsayıcı teknolojisinin bir ilerleme olduğunu görmek kolay olsa da ayrıntılarda daha özel avantajların saklı olduğu gözden kaçmamalıdır. Docker kapsayıcıları örneğini ele alalım. Bu konu başlığında Docker’ı ayrıntılı olarak ele almayacağız (Docker’ın hikayesini merak ediyorsanız [Docker nedir?”](https://www.docker.com/whatisdocker/) makalesini okuyun veya [vikipedi](http://wikipedia.org/wiki/Docker_%28software%29)’ye bakın), ancak Docker ve sunduğu ekosistem, hem geliştiriciler hem de BT uzmanları için büyük avantajlar sağlıyor.

Docker kapsayıcıları her şeyden önce Linux ve Windows kapsayıcılarını kullanmayı kolaylaştırdığından, geliştiriciler Docker kapsayıcılarına çok çabuk ısınıyor:

* Dağıtılması kolay olan sabit bir görüntü oluşturmak için basit, artımlı komutları kullanabiliyor ve bir docker dosyası kullanarak bu görüntülerin derlenmesini otomatikleştirebiliyorlar
* Bu görüntüleri basit, [git](https://git-scm.com/) tarzı gönderme ve çekme komutlarıyla [genel](https://registry.hub.docker.com/) veya [özel docker kayıt defterleriyle](../articles/virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kolayca paylaşabiliyorlar
* Bilgisayarlar yerine yalıtılmış uygulama bileşenlerini göz önünde bulundurabiliyorlar
* Docker kapsayıcılarını ve farklı temel görüntüleri anlayan çok sayıda aracı kullanabiliyorlar

### <a name="container-benefits-for-operations-and-it-professionals"></a>İşlemler ve BT uzmanları için kapsayıcı avantajları
BT ve işlemlerden sorumlu profesyoneller, kapsayıcılar ile sanal makinelerin birleşiminden avantaj sağlıyor.

* Kapsanan hizmetler sanal makine konak yürütme ortamından yalıtılır
* Kapsanan kod doğrulanabilir şekilde özdeştir
* Kapsanan hizmetler başlatılabilir, durdurulabilir ve geliştirme, test ve üretim ortamları arasında hızla taşınabilir

Bunun gibi özellikler (dahası da var), kaynakları (saf işleme gücü dahil) yalnızca ticari olarak ayakta kalması değil müşteri memnuniyetini artırması ve erişim ağını genişletmesi gereken görevlere uydurma görevini üstlenen profesyonel bilgi teknolojisi kurumlarının yer aldığı köklü işletmeleri heyecanlandırıyor. Küçük işletmeler, ISV’ler ve yeni işletmeler için de tam olarak aynı gereksinim vardır, ancak farklı şekilde açıklanabilir.

## <a name="what-are-virtual-machines-good-for"></a>Sanal makineler hangi kullanım alanları için uygundur?
Sanal makineler, bulut bilgi işlemin omurgasını oluşturur ve bu durum değişmez. Sanal makineler daha yavaş başlatılıyor, daha büyük bir disk alanı gerektiriyor ve mikro hizmet mimarisiyle doğrudan eşleşmiyor olsa da önemli avantajlar sağlar:

1. Varsayılan olarak konak bilgisayar için çok daha dayanıklı varsayılan güvenlik korumalarına sahiptir
2. Önde gelen tüm işletim sistemlerini ve uygulama yapılandırmalarını destekler
3. Komut ve denetim için uzun süredir geliştirilmekte olan bir araç ekosistemleri var
4. Konak kapsayıcılarına yürütme ortamı sağlar

Kapsanmış uygulamalar da yapacakları çağrılara bağlı olarak belirli bir işletim sistemi ve CPU türü gerektirdiğinden, son madde önemlidir. Sanal makineler dağıtmak istediğiniz uygulamaları içerdiğinden, kapsayıcıları sanal makinelere yüklediğinizi ve kapsayıcıların sanal makinelerin ya da işletim sistemlerinin yerini almaya yönelik olmadığını unutmamanız önemlidir.

## <a name="high-level-feature-comparison-of-vms-and-containers"></a>Sanal makineler ile kapsayıcıların üst düzey özellik karşılaştırması
Aşağıdaki tabloda, sanal makinelerle Linux kapsayıcıları arasında (pek ek çalışma yapılmadığında) bulunan özellik farkları çok yüksek bir düzeyde açıklanmıştır. Kendi uygulama gereksinimlerinize bağlı olarak bazı özelliklerin ilginizi daha çok veya daha az çekebileceğini ve tüm yazılımlarda olduğu gibi ek çalışma yapmanın özellikle de güvenlik alanında daha fazla özellik desteği sağladığını unutmayın.

| Özellik | VM'ler | Kapsayıcılar |
|:--- | --- | --- |
| "Varsayılan" güvenlik desteği |büyük ölçüde |daha düşük bir ölçüde |
| Diskte bellek gerekir |Tam işletim sistemi ve uygulamalar |Yalnızca uygulama gereksinimleri |
| Başlatma için harcanan süre |Önemli Ölçüde Daha Uzun: İşletim sisteminin başlatılması ve uygulamanın yüklenmesi |Önemli ölçüde daha kısa: Çekirdek zaten çalışır durumda olduğundan yalnızca uygulamanın başlatılması gerekir |
| Taşınabilirlik |Uygun Hazırlıkla Taşınabilir |Görüntü biçimi içinde taşınabilir; genellikle daha küçüktür |
| Görüntü Otomasyonu |İşletim sistemine ve uygulamalara bağlı olarak büyük oranda değişiklik gösterir |[Docker kayıt defteri](https://registry.hub.docker.com/); diğerleri |

## <a name="creating-and-managing-groups-of-vms-and-containers"></a>Sanal makine ve kapsayıcı gruplarının oluşturulması ve yönetilmesi
Bu noktada bir mimar, geliştirici ya da BT işlemleri uzmanı "Bunların HEPSİNİ otomatikleştirebilirim; bu GERÇEKTEN Hizmet Olarak Veri Merkezi!" diye düşünüyor olabilir.

Haklısınız, öyle kullanılabilir. Ayrıca, muhtemelen çoğunu zaten kullandığınız, hem Azure sanal makine gruplarını yönetebilen hem de betik (çoğunlukla [Windows için CustomScriptingExtension](https://msdn.microsoft.com/library/azure/dn781373.aspx) veya [Linux için CustomScriptingExtension](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/) ile) kullanarak özel kod ekleyebilen birçok sistem var. Azure dağıtımlarınızı PowerShell ya da Azure CLI betiklerini kullanarak otomatikleştirebilirsiniz (bunu daha önce yapmış da olabilirsiniz).

Daha sonra, bu özellikler genellikle uygun ölçekte sanal makine oluşturma işlemlerinin ve bunlara yönelik yapılandırmanın otomatikleştirilmesi için [Puppet](https://puppetlabs.com/) ve [Chef](https://www.chef.io/) gibi araçlara geçirilir. (Aşağıda [bu araçları Azure ile kullanma](#tools-for-working-with-containers) konusunda birkaç bağlantı verilmiştir.)

### <a name="azure-resource-group-templates"></a>Azure kaynak grubu şablonları
Daha yakın bir tarihte Azure tarafından [Azure kaynak yönetimi](../articles/resource-manager-deployment-model.md) REST API’si ve bunu daha kolay kullanabilmenizi sağlayan güncelleştirilmiş PowerShell ve Azure CLI araçları yayınlandı. Azure kaynak yönetimi API’sini içeren [Azure Resource Manager şablonlarını](../articles/resource-group-authoring-templates.md) aşağıdakilerle kullanarak bütün bir uygulama topolojisini dağıtabilir, değiştirebilir ve yeniden dağıtabilirsiniz:

* [şablonları kullanan Azure portalı](https://github.com/Azure/azure-quickstart-templates)&mdash;ipucu: "DeployToAzure" düğmesini kullanın
* [Azure CLI](../articles/virtual-machines/linux/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure PowerShell modülleri](../articles/virtual-machines/linux/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="deployment-and-management-of-entire-groups-of-azure-vms-and-containers"></a>Azure sanal makine ve kapsayıcı gruplarının bütünlüklü olarak dağıtımı ve yönetimi
Sanal makine gruplarını bütünlüklü olarak dağıtabilen ve bunlara otomatikleştirilebilen bir grup olarak Docker’ı (veya diğer Linux kapsayıcı konak sistemleri) yükleyebilen çeşitli popüler sistemler mevcuttur. Doğrudan bağlantılar için aşağıdaki [kapsayıcılar ve araçlar](#containers-and-vm-technologies) bölümüne bakın. Bu işlemleri daha geniş veya daha dar kapsamlı olarak gerçekleştirebilen çeşitli sistemler mevcuttur ve bu listede hepsine yer verilmemiştir. Bunlar, becerilerinize ve senaryolarınıza bağlı olarak sizin için kullanışlı olabilir veya olmayabilir.

Docker kendi sanal makine oluşturma araçları ([docker-machine](../articles/virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) kümesine ve yük dengeleyen bir docker kapsayıcı kümesi yönetim aracına ([swarm](../articles/virtual-machines/virtual-machines-linux-docker-swarm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) sahiptir. Ayrıca, [Azure Docker Sanal Makine Uzantısı](https://github.com/Azure/azure-docker-extension/blob/master/README.md), birden çok kapsayıcıya yapılandırılmış uygulama kapsayıcıları dağıtabilen [`docker-compose`](https://docs.docker.com/compose/) için varsayılan olarak destek sağlar.

Ayrıca, [Mesosphere tarafından sunulan Data Center Operating System (DCOS)](http://docs.mesosphere.com/install/azurecluster/) ürününü deneyebilirsiniz. DCOS, veri merkezinizi tek bir hizmet olarak görmenize imkan tanıyan açık kaynaklı [mesos](http://mesos.apache.org/) "dağıtılmış sistemler çekirdeği"ni temel alır. DCOS’de [Spark](http://spark.apache.org/) ve [Kafka](http://kafka.apache.org/) (ve diğerleri) gibi çeşitli önemli sistemler ve [Marathon](https://mesosphere.github.io/marathon/) (kapsayıcı denetleme sistemi) ve [Chronos](https://mesos.github.io/chronos/) (dağıtılmış zamanlayıcı) gibi yerleşik hizmetlere yönelik yerleşik paketler vardır. Mesos sistemi Twitter, AirBnb ve diğer web ölçeğindeki işletmelerden alınan derslerden türetilmiştir. Düzenleme altyapısı olarak **swarm**’ı da kullanabilirsiniz.

Ayrıca, Google’dan alınan derslerden türetilen, sanal makine ve kapsayıcı grubu yönetimi için açık kaynaklı bir sistem olan [kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/) de kullanılabilir. [Ağ desteği sağlamak için kubernetes’i weave ile kullanma](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave) olanağınız bile vardır.

[Deis](http://deis.io/overview/), uygulamalarınızı kendi sunucularınızda dağıtmayı ve yönetmeyi kolaylaştıran açık kaynaklı bir "Hizmet Olarak Platform" (PaaS) çözümüdür. Docker ve CoreOS’u temel alan Deis, Heroku’dan ilham alan bir iş akışıyla hafif bir PaaS sunar. Azure’da kolayca [3 Düğümlü bir Azure sanal makine grubu oluşturup Deis’i yükleyebilir](../articles/virtual-machines/linux/deis-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ardından [bir Hello World Go uygulaması yükleyebilirsiniz](../articles/virtual-machines/linux/deis-cluster.md#deploy-and-scale-a-hello-world-application).

Kapladığı alan, Docker desteği ve kendine ait [rkt](https://github.com/coreos/rkt) adlı kapsayıcı sistemiyle iyileştirilmiş bir Linux dağıtımı olan [CoreOS](https://coreos.com/os/docs/latest/booting-on-azure.html), [fleet](https://coreos.com/using-coreos/clustering/) adlı bir kapsayıcı grubu yönetim aracına da sahiptir.

Bir başka popüler Linux dağıtımı olan Ubuntu Docker’ı çok iyi desteklemekle birlikte [Linux (LXC stili) kümelerini](https://help.ubuntu.com/lts/serverguide/lxc.html) de destekler.

## <a name="tools-for-working-with-azure-vms-and-containers"></a>Azure sanal makineleri ve kapsayıcıları ile çalışmaya yönelik araçlar
Kapsayıcılarla ve Azure sanal makineleriyle çalışılırken çeşitli araçlar kullanılır. Bu bölümde kapsayıcılar, gruplar ve daha genel olarak bunlarla birlikte kullanılan yapılandırma ve düzenleme araçları ile ilgili en kullanışlı veya önemli kavramların ve araçların yalnızca bazıları listelenmiştir.

> [!NOTE]
> Bu alan baş döndürücü bir hızda değiştiğinden, bu konuyu ve konudaki bağlantıları güncel tutmak için elimizden geleni yapmamıza rağmen bunu başaramayabiliriz. Bilgilerinizin güncel kalması için ilginizi çeken konularda mutlaka araştırma yapın!
>
>

### <a name="containers-and-vm-technologies"></a>Kapsayıcılar ve sanal makine teknolojileri
Bazı Linux kapsayıcısı teknolojileri:

* [Docker](https://www.docker.com)
* [LXC](https://linuxcontainers.org/)
* [CoreOS ve rkt](https://github.com/coreos/rkt)
* [Open Container Project](http://opencontainers.org/)
* [RancherOS](http://rancher.com/rancher-os/)

Windows Kapsayıcısı bağlantıları:

* [Windows Kapsayıcıları](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)

Visual Studio Docker bağlantıları:

* [Docker için Visual Studio 2015 RC Araçları - Önizleme](https://visualstudiogallery.msdn.microsoft.com/6f638067-027d-4817-bcc7-aa94163338f0)

Docker araçları:

* [Docker daemon](https://docs.docker.com/installation/#installation)
* Docker istemcileri
  * [Chocolatey’de Windows Docker İstemcisi](https://chocolatey.org/packages/docker)
  * [Docker yükleme yönergeleri](https://docs.docker.com/installation/#installation)

Microsoft Azure’da Docker:

* [Azure’da Linux için Docker Sanal Makine Uzantısı](../articles/virtual-machines/linux/dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure Docker Sanal Makine Uzantısı Kullanıcı Kılavuzu](https://github.com/Azure/azure-docker-extension/blob/master/README.md)
* [Docker Sanal Makine Uzantısını Azure Komut Satırı Arabirimi (Azure CLI) ile kullanma](../articles/virtual-machines/linux/classic/cli-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Docker Sanal Makine Uzantısını Azure portalından kullanma](../articles/virtual-machines/linux/classic/portal-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Azure’da docker-machine kullanma](../articles/virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure’da swarm ile docker'ı kullanma](../articles/virtual-machines/virtual-machines-linux-docker-swarm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure’da Docker ve Compose Kullanmaya Başlama](../articles/virtual-machines/linux/docker-compose-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure kaynak grubu şablonu kullanarak Azure’da hızlıca Docker konağı oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)
* [Kapsanan uygulamalar için yerleşik `compose`](https://github.com/Azure/azure-docker-extension#11-public-configuration-keys) desteği
* [Azure’da Docker özel kayıt defteri uygulama](../articles/virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Linux dağıtımları ve Azure örnekleri:

* [CoreOS](https://coreos.com/os/docs/latest/booting-on-azure.html)

Yapılandırma, küme yönetimi ve kapsayıcı düzenleme:

* [CoreOS üzerinde Fleet](https://coreos.com/using-coreos/clustering/)
* Deis

  * [3 Düğümlü bir Azure sanal makine grubu oluşturma, Deis’i yükleme ve bir Hello World Go uygulaması başlatma](../articles/virtual-machines/linux/deis-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Kubernetes

  * [CoreOS ve Weave ile otomatikleştirilmiş Kubernetes küme dağıtımı konusunda bilmeniz gereken her şey](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
  * [Kubernetes Visualizer](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)
* [Mesos](http://mesos.apache.org/)

  * [Mesosphere tarafından sunulan Data Center Operating System (DCOS)](http://beta-docs.mesosphere.com/install/azurecluster/)
* [Jenkins](https://jenkins-ci.org/) ve [Hudson](http://hudson-ci.org/)

  * [Blog: Azure için Jenkins Bağımlı Eklentisi](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
  * [GitHub deposu: Azure için Jenkins Depolama Eklentisi](https://github.com/jenkinsci/windows-azure-storage-plugin)
  * [Üçüncü Taraf: Azure için Hudson Bağımlı Eklentisi](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
  * [Üçüncü Taraf: Azure için Hudson Depolama Eklentisi](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
* [Azure Otomasyonu](https://azure.microsoft.com/services/automation/)

  * [Video: Azure Otomasyonunu Linux Sanal Makineleri ile Kullanma](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
* Linux için PowerShell DSC

  * [Blog: Linux için Powershell DSC gerçekleştirme](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
  * [GitHub: Docker İstemcisi DSC](https://github.com/anweiss/DockerClientDSC)

## <a name="next-steps"></a>Sonraki adımlar
[Docker](https://www.docker.com) ve [Windows Kapsayıcıları](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)’nı inceleyin.

<!--Anchors-->
[microservices]: http://martinfowler.com/articles/microservices.html
[microservice]: http://martinfowler.com/articles/microservices.html
<!--Image references-->
