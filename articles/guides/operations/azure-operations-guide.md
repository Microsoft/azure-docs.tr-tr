---
title: Azure BT operatörleri için Başlarken Kılavuzu | Microsoft Docs
description: Azure It işleçleri için Başlarken Kılavuzu
author: RicksterCDN
ms.author: rclaus
tags: azure-resource-manager
ms.service: azure
ms.topic: overview
ms.workload: infrastructure
ms.date: 08/24/2018
ms.openlocfilehash: 9b23e3b69b4970e765ea1cd5af690cf074bad774
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693442"
---
# <a name="get-started-for-azure-it-operators"></a>Azure It işleçleri için kullanmaya başlayın

Bu kılavuzda, bir Microsoft Azure altyapısının dağıtımıyla ve yönetimiyle ilgili temel kavramlar tanıtılmıştır. Bulut bilgi işlem veya Azure 'un kendisi için yeni başladıysanız, bu kılavuzda kavramlar, dağıtım ve yönetim ayrıntılarının hızlı bir şekilde başlamanıza yardımcı olur. Bu kılavuzun pek çok bölümü, sanal makine dağıtma gibi bir işlemi tartışır ve daha ayrıntılı teknik ayrıntı için bir bağlantı sağlar.

## <a name="cloud-computing-overview"></a>Bulut bilgi işlem genel bakış

Bulut bilgi işlem, geleneksel şirket içi veri merkezine modern bir alternatif sağlar. Genel bulut satıcıları, tüm bilgi işlem altyapısını ve temel alınan yönetim yazılımını sağlar ve yönetir. Bu satıcılar çok çeşitli bulut hizmetleri sağlar. Bu durumda bir bulut hizmeti, bir sanal makine, bir Web sunucusu veya bulut tarafından barındırılan bir veritabanı altyapısı olabilir. Bir bulut sağlayıcısı müşterisi olarak, bu bulut hizmetlerini gerekli bir şekilde kiralayın. Bunu yaparken, büyük miktarda donanım bakım masrafına bir operasyonel gider olarak dönüştürürsünüz. Bulut hizmeti de şu avantajları sağlar:

- Büyük işlem ortamlarının hızlı dağıtımı

- Artık gerekli olmayan sistemlerin hızlı bir şekilde ayırmayı kaldırma

- Geleneksel olarak karmaşık sistemlerin yük dengeleyiciler gibi kolay dağıtımı

- Gerektiğinde esnek işlem kapasitesi veya ölçek sağlama olanağı

- Daha düşük maliyetli bilgi işlem ortamları

- Web tabanlı portal veya programlı Otomasyon ile herhangi bir yerden erişim

- Çoğu işlem ve uygulama ihtiyaçlarını karşılamak için bulut tabanlı hizmetler

Şirket içi altyapıyla, dağıtılan donanım ve yazılımlar üzerinde tam denetime sahip olursunuz. Tarihsel olarak, bu, ölçeklendirmeye odaklanarak donanım tedarik kararlarına yol açmıştır. En yüksek performans ihtiyaçlarını karşılamak için daha fazla çekirdeğe sahip bir sunucu satın alma örneğidir. Ne yazık ki, bu altyapı isteğe bağlı bir pencere dışında kullanılıyor olabilir. Azure ile yalnızca ihtiyacınız olan altyapıyı dağıtabilir ve istediğiniz zaman bu altyapıyı ayarlayabilir veya azaltabilirsiniz. Bu, bir performans gereksinimini karşılamak için ek işlem düğümlerinin dağıtımına göre ölçeklendirmeye odaklanmaya yol açar. Bulut hizmetlerinin ölçeklendirilmesi, pahalı donanıma kadar ölçeklendirmeye kıyasla daha düşük maliyetli bir işlemdir.

Microsoft, dünyanın dört bir yanındaki çeşitli Azure veri merkezlerini daha planlı olarak dağıtmıştır. Ayrıca, Microsoft, Çin ve Almanya gibi bölgelerde egeign bulutlarını de artmaktadır. Veri merkezlerini yalnızca en büyük küresel kuruluşlar bu şekilde dağıtabilir. bu nedenle Azure kullanmak, tüm boyuttaki kuruluşların hizmetlerini müşterilerine yakın bir şekilde dağıtmasını kolaylaştırır.

Küçük işletmeler için, Azure düşük maliyetli bir giriş noktasına izin verir ve bu sayede işlem artışları için hızlı bir istek olarak ölçeklendirme olanağı sağlar. Bu, altyapıda büyük bir ön ön sermaye yatırımını önler ve gerektiğinde sistemleri mimarın ve yeniden mimarın üzerinde ayarlama esnekliği sağlar. Bulut bilgi işlemin kullanımı, başlangıç artışını hızlı ve başarısız-hızlı bir modelle uyumlu hale sığdırır.

Kullanılabilir Azure bölgeleri hakkında daha fazla bilgi için bkz. [Azure bölgeleri](https://azure.microsoft.com/regions/).

### <a name="cloud-computing-model"></a>Bulut bilgi işlem modeli

Azure, müşterilere sunulan hizmet kategorilerini temel alan bir bulut bilgi işlem modeli kullanır. Üç hizmet kategorisi hizmet olarak altyapı (IaaS), hizmet olarak platform (PaaS) ve hizmet olarak yazılım (SaaS) içerir. Satıcılar, bu kategorilerin her birinde bilgi işlem yığınındaki bileşenlerin bir kısmını veya tamamını paylaşır. Bulut bilgi işlem kategorilerinin her birine göz atalım.
![Cloud yığın karşılaştırmayı hesaplama ](./media/cloud-computing-comparison.png)

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: hizmet olarak altyapı

Bir IaaS Bulut satıcısı çalışır ve bilgisayar sanallaştırmayı etkinleştirmek için tüm fiziksel işlem kaynaklarını ve gerekli yazılımları yönetir. Bu hizmetin bir müşterisi, sanal makineleri bu barındırılan veri merkezlerinde dağıtır. Sanal makineler, site dışı bir veri merkezinde yer alıyor olsa da, IaaS tüketicisi temeldeki altyapıyı bulut satıcısına bırakarak işletim sisteminin yapılandırması ve yönetimi üzerinde denetime sahiptir.

Azure, sanal makineler, sanal makine ölçek kümeleri ve ilgili ağ altyapısı gibi çeşitli IaaS çözümleri içerir. Sanal makineler, başlangıçta Hizmetleri Azure 'a geçirmeye yönelik popüler bir seçimdir çünkü bir "kaldırma ve kaydırma" geçiş modeli sağlar. Bir VM 'yi, veri merkezinizde Hizmetleri çalıştırmakta olan altyapı gibi yapılandırabilir ve ardından yazılımınızı yeni VM 'ye geçirebilirsiniz. Diğer hizmetlere veya depolamaya yönelik URL 'Ler gibi yapılandırma güncelleştirmeleri yapmanız gerekebilir, ancak pek çok uygulamayı bu şekilde geçirebilirsiniz.

Sanal Makine Ölçek Kümeleri, Azure sanal makinelerinin üzerine kurulmuştur ve özdeş VM 'lerin kümelerini dağıtmanın kolay bir yolunu sağlar. Sanal makine ölçek kümeleri de otomatik ölçeklendirmeyi destekler, böylece yeni VM 'Ler gerektiğinde otomatik olarak dağıtılacaktır. Bu, sanal makine ölçeğini Azure Service Fabric ve Azure Container Service gibi daha üst düzey mikro hizmet işlem kümelerini barındırmak için ideal bir platform ayarlar.

#### <a name="paas-platform-as-a-service"></a>PaaS: hizmet olarak platform

PaaS ile uygulamanızı, bulut hizmeti satıcısının sağladığı bir ortama dağıtırsınız. Satıcı, uygulama geliştirme ve veri yönetimine odaklanabilmeniz için tüm altyapı yönetimini yapar.

Azure, Azure App Service ve Azure Cloud Services (Web ve çalışan rolleri) Web Apps özelliği de dahil olmak üzere çeşitli PaaS bilgi işlem teklifleri sunar. Her iki durumda da geliştiriciler, uygulamayı destekleyen NSU ve cıvatların her şeyi bilmeden uygulamasını dağıtmanın birden çok yolu vardır. Geliştiricilerin sanal makineler (VM) oluşturması, her birinde oturum açması için Uzak Masaüstü Protokolü (RDP) kullanması veya uygulamayı yüklemesi gerekmez. Bu kişiler yalnızca bir düğmeye (veya buna yakın) ulaşarak, Microsoft tarafından sunulan araçların VM 'Leri sağlamasını ve uygulamayı onlara dağıtıp yüklemesini sağlar.

#### <a name="saas-software-as-a-service"></a>SaaS: hizmet olarak yazılım

SaaS, merkezi olarak barındırılan ve yönetilen bir yazılımdır. Genellikle çok kiracılı bir mimariye dayalıdır — uygulamanın tek bir sürümü tüm müşteriler için kullanılır. Tüm konumlarda en iyi performansı sağlamak için birden çok örneğe ölçeklendirilebilir. SaaS yazılımı genellikle aylık veya yıllık bir abonelik üzerinden lisanslanır. SaaS yazılım satıcıları yazılım yığınının tüm bileşenlerinden sorumludur, böylece tüm yönettiğiniz hizmetler sağlanmış olur.

Microsoft Office 365, SaaS teklifiyle iyi bir örnektir. Aboneler aylık veya yıllık abonelik ücreti ödeyerek Microsoft Exchange, Microsoft OneDrive ve Microsoft Office paketinin geri kalanını bir hizmet olarak alırlar. Aboneler her zaman en son sürümü alır ve Exchange sunucusu sizin için yönetilir. Her yıl Office 'i yükleme ve yükseltme ile karşılaştırıldığında, bu daha pahalıdır ve daha az çaba gerektirir.

## <a name="azure-services"></a>Azure hizmetleri

Azure, bulut bilgi işlem platformunda çok sayıda hizmet sunar. Bu hizmetler aşağıdakileri içerir.

### <a name="compute-services"></a>İşlem hizmetleri

Uygulama iş yükünü barındırmak ve çalıştırmak için hizmetler:

- Azure sanal makineler — hem Linux hem de Windows

- Uygulama Hizmetleri (Web Apps, Mobile Apps, Logic Apps, API Apps ve Işlev uygulamaları)

- Azure Batch (büyük ölçekli paralel ve toplu işlem işleri için)

- Azure Service Fabric

- Azure Kapsayıcı Hizmeti

### <a name="data-services"></a>Veri hizmetleri

Verileri depolama ve yönetme Hizmetleri:

- Azure depolama (Azure blob, kuyruk, tablo ve dosya hizmetlerinden oluşur)

- Azure SQL Veritabanı

- Azure Cosmos DB

- Microsoft Azure StorSimple

- Redis için Azure Cache

### <a name="application-services"></a>Uygulama hizmetleri

Oluşturma ve işletim uygulamaları için hizmetler:

- Azure Active Directory (Azure AD)

- Dağıtılmış sistemleri bağlamak için Azure Service Bus

- Büyük verileri işlemek için Azure HDInsight

- Azure Zamanlayıcı

- Azure Media Services

### <a name="network-services"></a>Ağ Hizmetleri

Hem Azure hem de Azure ile şirket içi veri merkezleri arasında ağ hizmetleri için hizmetler:

- Azure Sanal Ağı

- Azure ExpressRoute

- Azure tarafından sağlanmış DNS

- Azure Traffic Manager

- Azure Content Delivery Network

Azure hizmetleri hakkında ayrıntılı belgeler için bkz. [Azure hizmet belgeleri](https://docs.microsoft.com/azure).

## <a name="azure-key-concepts"></a>Azure Anahtar kavramları

### <a name="datacenters-and-regions"></a>Veri merkezleri ve bölgeler

Azure, dünyanın dört bir yanındaki birçok bölgede genel olarak kullanılabilen küresel bir bulut platformudur. Azure 'da bir hizmet, uygulama veya VM sağladığınızda bir bölge seçmeniz istenir. Seçilen bölge, uygulamanızın çalıştığı belirli bir veri merkezini temsil eder. Daha fazla bilgi için bkz. [Azure bölgeleri](https://azure.microsoft.com/regions/).

Azure kullanmanın avantajlarından biri, uygulamalarınızı dünyanın dört bir yanındaki çeşitli veri merkezlerine dağıtabileceğiniz bir avantajdır. Seçtiğiniz bölge, uygulamanızın performansını etkileyebilir. Ağ isteklerindeki gecikmeyi azaltmak için müşterilerinizin en yakınına daha yakın bir bölge seçmek en iyisidir. Ayrıca, belirli ülkelerde/bölgelerde uygulamanızı dağıtmaya yönelik yasal gereksinimleri karşılamak için bir bölge seçebilirsiniz.

### <a name="azure-portal"></a>Azure portalı

Azure portal, Azure kaynakları ve hizmetleri oluşturmak, yönetmek ve kaldırmak için kullanılabilen Web tabanlı bir uygulamadır. Azure portal [Portal.Azure.com](https://portal.azure.com)adresinde bulunur. Bu, Azure kaynaklarını yönetmeye yönelik özelleştirilebilir bir Pano ve araç içerir. Ayrıca faturalandırma ve abonelik bilgileri de sağlar. Daha fazla bilgi için bkz. [Microsoft Azure Portal genel bakış](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) ve [Azure kaynaklarını Portal üzerinden yönetme](https://docs.microsoft.com/azure/azure-portal/resource-group-portal).

### <a name="resources"></a>Kaynaklar

Azure kaynakları, bir Azure aboneliğine dağıtılan bireysel işlem, ağ, veri veya uygulama barındırma hizmetlerdir. Bazı ortak kaynaklar sanal makineler, depolama hesapları veya SQL veritabanlarıdır. Azure hizmetleri genellikle ilgili birkaç Azure kaynaklarından oluşur. Örneğin, bir Azure sanal makinesi bir VM, depolama hesabı, ağ bağdaştırıcısı ve genel IP adresi içerebilir. Bu kaynaklar tek tek veya bir grup olarak oluşturulabilir, yönetilebilir ve silinebilir. Azure kaynakları, bu kılavuzun ilerleyen kısımlarında daha ayrıntılı bir şekilde ele alınmıştır.

### <a name="resource-groups"></a>Kaynak grupları

Azure Kaynak grubu, bir Azure çözümü için ilgili kaynakları tutan bir kapsayıcıdır. Kaynak grubu, çözüme yönelik tüm kaynakları veya yalnızca Grup olarak yönetmek istediğiniz kaynakları içerebilir. Azure Kaynak grupları, bu kılavuzun ilerleyen kısımlarında daha ayrıntılı bir şekilde ele alınmıştır.

### <a name="resource-manager-templates"></a>Resource Manager şablonları

Azure Resource Manager şablonu, bir kaynak grubuna dağıtılacak bir veya daha fazla kaynağı tanımlayan bir JavaScript Nesne Gösterimi (JSON) dosyasıdır. Dağıtılan kaynaklar arasındaki bağımlılıkları da tanımlar. Kaynak Yöneticisi şablonlar bu kılavuzun ilerleyen kısımlarında daha ayrıntılı bir şekilde ele alınmıştır.

### <a name="automation"></a>Otomasyon

Azure portal kullanarak kaynak oluşturmaya, yönetmeye ve silmeye ek olarak, bu etkinlikleri PowerShell veya Azure komut satırı arabirimi (CLı) kullanarak otomatik hale getirebilirsiniz.

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell, Azure 'un yönetilmesi için cmdlet 'leri sağlayan bir modüller kümesidir. Azure hizmetlerini oluşturmak, yönetmek ve kaldırmak için cmdlet 'lerini kullanabilirsiniz. Cmdlet 'ler tutarlı, yinelenebilir ve uygulamalı dağıtımlar elde etmenize yardımcı olabilir. Daha fazla bilgi için bkz. [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/install-Az-ps).

#### <a name="azure-command-line-interface"></a>Azure komut satırı arabirimi

Azure komut satırı arabirimi, komut satırından Azure kaynaklarını oluşturmak, yönetmek ve kaldırmak için kullanabileceğiniz bir araçtır. Azure CLı, Linux, Mac OS X ve Windows için kullanılabilir. Daha fazla bilgi ve teknik ayrıntılar için bkz. [Azure CLI 'Yi yüklemeyi](/cli/azure/install-azure-cli).

#### <a name="rest-apis"></a>REST API'leri

Azure, Azure portal Kullanıcı arabirimini destekleyen bir REST API kümesi üzerine kurulmuştur. Bu REST API 'lerinin çoğu, bir Internet etkin cihazdan Azure kaynaklarınızı ve uygulamalarınızı programlı bir şekilde sağlamanıza ve yönetmenize olanak sağlamak için de desteklenir. Daha fazla bilgi için bkz. [Azure Rest SDK başvurusu](https://docs.microsoft.com/rest/api/index).

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

Yöneticiler, Azure Cloud Shell adlı tarayıcıda erişilebilen bir deneyim aracılığıyla Azure PowerShell ve Azure CLı 'ye erişebilir. Bu etkileşimli arabirim, Linux ve Windows yöneticilerinin, bash veya PowerShell gibi tercih ettiğiniz komut satırı arabirimini kullanması için esnek bir araç sağlar. Azure Cloud Shell, Portal üzerinden veya [Shell.Azure.com](https://shell.azure.com)adresinde tek başına bir Web arabirimi olarak veya başka bir dizi erişim noktasından erişebilir. Daha fazla bilgi için bkz. [Azure Cloud Shell Genel Bakış](https://docs.microsoft.com/azure/cloud-shell/overview).

## <a name="azure-subscriptions"></a>Azure abonelikleri

Abonelik, Azure hesabına bağlı Azure hizmetlerinin mantıksal gruplandırmasıdır. Tek bir Azure hesabı, birden çok abonelik içerebilir. Azure hizmetleri için faturalandırma, abonelik başına esasına göre yapılır. Azure aboneliklerinde, abonelik üzerinde tam denetim sahibi olan ve abonelik içindeki tüm hizmetler üzerinde denetim sahibi olan bir hizmet yöneticisi olan bir hesap yöneticisi vardır. Klasik abonelik yöneticileri hakkında daha fazla bilgi için bkz. [Azure abonelik yöneticileri ekleme veya değiştirme](../../billing/billing-add-change-azure-subscription-administrator.md). Yöneticilere ek olarak, bireysel hesaplara [rol tabanlı erişim denetimi (RBAC)](../../role-based-access-control/overview.md)kullanılarak Azure kaynakları için ayrıntılı denetim verilebilir.

### <a name="select-and-enable-an-azure-subscription"></a>Azure aboneliğini seçme ve etkinleştirme

Azure hizmetleriyle çalışabilmeniz için önce bir aboneliğiniz olması gerekir. Birkaç abonelik türü var.

**Ücretsiz hesaplar**: ücretsiz bir hesaba kaydolma bağlantısı [Azure Web sitesinde](https://azure.microsoft.com/)yer alır. Böylece, Azure 'da kaynakların herhangi bir birleşimini denemek için 30 günlük kurs üzerinden kredi elde edersiniz. Kredi tutarınızı aşarsanız hesabınız askıya alınır. Deneme süresi sonunda, hizmetleriniz kullanımdan çıkarıldı ve artık çalışmayacak. Dilediğiniz zaman bir Kullandıkça Öde aboneliğine yükseltebilirsiniz.

**MSDN abonelikleri**: bir MSDN aboneliğiniz varsa, her ay Azure kredisi için belirli bir miktar alırsınız. Örneğin, bir MSDN aboneliğine sahip bir Microsoft Visual Studio Enterprise varsa Azure kredisi ' nda ayda \$150 alırsınız.

Kredi miktarını aşarsanız, hizmetiniz bir sonraki ay başlamadan devre dışı bırakılır. Harcama limitini kapatabilir ve ek maliyetler için kullanılacak kredi kartını ekleyebilirsiniz. Bu maliyetlerin bazıları MSDN hesapları için indirimli. Örneğin, Windows Server çalıştıran VM 'Ler için Linux fiyatını ödeyin ve Microsoft SQL Server gibi Microsoft sunucuları için ek ücret alınmaz. Bu, MSDN hesaplarını geliştirme ve test senaryoları için ideal hale getirir.

**BizSpark hesapları**: Microsoft BizSpark programı, başlatmalar için birçok avantaj sağlar. Bu avantajlardan biri, beş adede kadar MSDN hesabına yönelik geliştirme ve test ortamları için tüm Microsoft yazılımlarına erişim sağlar. Bu beş MSDN hesabının her biri için Azure kredisi $150 alır ve sanal makineler gibi birçok Azure hizmeti için indirimli ücret ödersiniz.

**Kullandıkça Öde**: bu abonelikle, hesaba kredi kartı veya banka kartı ekleyerek kullandığınız kadar ödeyin. Bir kurumunuzda faturalandırma için de onay alabilirsiniz.

**Kurumsal Anlaşmalar**: kurumsal anlaşma sayesinde, bir sonraki yılda Azure 'da belirli sayıda hizmeti kullanmaya ve bu miktarı önceden ödemeniz gerekir. Yaptığınız taahhüt yıl içinde tüketilecektir. Taahhüt tutarını aşarsanız fazla kullanım süresini büyük bir süre içinde ödeyebilirsiniz. Taahhüt miktarına bağlı olarak, Azure 'daki hizmetlere ilişkin bir indirim elde edersiniz.

### <a name="grant-administrative-access-to-an-azure-subscription"></a>Azure aboneliğine yönetici erişimi verme

RBAC 'de izin atamak için kullanabileceğiniz çeşitli yerleşik roller vardır. Bir kullanıcıyı bir Azure aboneliğinin Yöneticisi yapmak için, abonelik kapsamında bu rolü [sahip](../../role-based-access-control/built-in-roles.md#owner) rolüne atayın. Sahip rolü, kullanıcıya, başkalarına erişim yetkisi devretme hakkı da dahil olmak üzere abonelikteki tüm kaynaklara tam erişim izni verir.

Daha fazla bilgi için bkz. [RBAC ve Azure portalını kullanarak erişimi yönetme](../../role-based-access-control/role-assignments-portal.md).

### <a name="view-billing-information-in-the-azure-portal"></a>Azure portal fatura bilgilerini görüntüleme

Azure kullanmanın önemli bir bileşeni faturalandırma bilgilerini görüntüleyebilme olanağıdır. Azure portal Azure Faturalandırma bilgileri hakkında ayrıntılı bilgiler sağlar.

Daha fazla bilgi için bkz. [Azure Faturalandırma faturanızı ve günlük kullanım verilerinizi indirme](../../billing/billing-download-azure-invoice-daily-usage-date.md).

### <a name="get-billing-information-from-billing-apis"></a>Faturalandırma API 'Lerinden fatura bilgilerini alın

Portalda faturalandırmayı görüntülemenin yanı sıra, Azure Faturalandırma REST API 'Leri aracılığıyla bir betik veya program kullanarak faturalandırma bilgilerine erişebilirsiniz:

- Kullanım verilerinizi almak için Azure kullanım API 'sini kullanabilirsiniz. İlgili Azure kaynaklarını etiketleyerek faturalandırma kullanım bilgilerine ince ayar yapabilirsiniz. Örneğin, bir kaynak grubundaki kaynakların her birini bir departman adı veya proje adı ile etiketleyebilir ve ardından bu bir etiket için maliyetleri özel olarak izleyebilirsiniz.

- Tüm kullanılabilir kaynakları, bu kaynakların her biri hakkındaki meta verileri ve fiyatlandırma bilgilerini listelemek için Azure fiyat kartı API 'sini kullanabilirsiniz.

Daha fazla bilgi için bkz. [Microsoft Azure kaynak kullanımınızla ilgili içgörüler edinin](../../billing/billing-usage-rate-card-overview.md).

### <a name="forecast-cost-with-the-pricing-calculator"></a>Fiyatlandırma hesaplayıcısı ile tahmin maliyeti

Azure 'daki her bir hizmetin fiyatlandırması farklıdır. Birçok Azure hizmeti temel, standart ve Premium katmanları sağlar. Genellikle, her katmanın birkaç fiyat ve performans düzeyi vardır. [Çevrimiçi Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/calculator)' ı kullanarak fiyatlandırma tahminleri oluşturabilirsiniz. Hesaplayıcı, maliyeti tek bir kaynakta veya kaynak grubunda tahmin etmek için esneklik içerir.

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager, Azure kaynakları için dağıtım, yönetim ve kuruluş mekanizmasından sorumludur. Kaynak Yöneticisi kullanarak, birçok ayrı kaynağı bir kaynak grubunda birlikte yerleştirebilirsiniz.

Kaynak Yöneticisi ayrıca, ilgili kaynakların özelleştirilebilir dağıtımına ve yapılandırılmasına izin veren dağıtım özelliklerini de içerir. Örneğin, Kaynak Yöneticisi kullanarak birden çok sanal makineden, yük dengeleyiciden ve SQL veritabanından tek bir birim olarak oluşan bir uygulamayı dağıtabilirsiniz. Bu dağıtımları bir Kaynak Yöneticisi şablonu kullanarak geliştirirsiniz.

Resource Manager çeşitli avantajlar sunar:

- Çözümünüzdeki tüm kaynakları ayrı ayrı ele almak yerine bunları grup halinde dağıtabilir, yönetebilir ve izleyebilirsiniz.

- Çözümünüzü geliştirme yaşam döngüsü boyunca sürekli olarak dağıtabilir ve kaynaklarınızın tutarlı bir durumda dağıtıldığından emin olabilirsiniz.

- Altyapınızı betikler yerine bildirim temelli şablonları kullanarak yönetebilirsiniz.

- Doğru sırayla dağıtılmalarını sağlamak için kaynaklarınız arasındaki bağımlılıkları tanımlayabilirsiniz.

- RBAC, yönetim platformuyla yerel olarak tümleştirildiği için, kaynak grubunuzdaki tüm hizmetlere erişim denetimi uygulayabilirsiniz.

- Aboneliğinizdeki tüm kaynakları mantıksal olarak düzenlemek için kaynaklara Etiketler uygulayabilirsiniz.

- Aynı etiketi paylaşan bir kaynak grubu için maliyetleri görüntüleyerek kuruluşunuzun faturalandırmasını açıklığa kavuşturun.

### <a name="tips-for-creating-resource-groups"></a>Kaynak grupları oluşturmaya yönelik ipuçları

Kaynak gruplarınız hakkında kararlar verirken, bu ipuçlarını göz önünde bulundurun:

- Bir kaynak grubundaki tüm kaynaklar aynı yaşam döngüsüne sahip olmalıdır.

- Bir kaynağı tek seferde yalnızca bir gruba atayabilirsiniz.

- Kaynak grubundan istediğiniz zaman bir kaynak ekleyebilir veya kaldırabilirsiniz. Her kaynak bir kaynak grubuna ait olmalıdır. Bu nedenle bir kaynağı bir gruptan kaldırırsanız, başka bir gruba eklemeniz gerekir.

- Birçok kaynak türünü, istediğiniz zaman farklı bir kaynak grubuna taşıyabilirsiniz.

- Bir kaynak grubundaki kaynaklar farklı bölgelerde olabilir.

- Bir kaynak grubunu, içindeki kaynakların erişimini denetlemek için kullanabilirsiniz.

### <a name="building-resource-manager-templates"></a>Kaynak Yöneticisi şablonları oluşturma

Kaynak Yöneticisi şablonlar, tek bir kaynak grubuna dağıtılacak kaynakları ve kaynak konfigürasyonları bildirimli olarak tanımlar. Daha fazla betik oluşturma veya el ile yapılandırma gerekmeden karmaşık dağıtımları düzenlemek için Kaynak Yöneticisi şablonlarını kullanabilirsiniz. Bir şablon geliştirdikten sonra, aynı sonuca sahip her seferinde birden çok kez dağıtabilirsiniz.

Bir Kaynak Yöneticisi şablonu dört bölümden oluşur:

- **Parametreler**: Bunlar dağıtıma yönelik girişlerdir. Parametre değerleri bir insan veya otomatik işlem tarafından sağlanarak yapılabilir. Örnek bir parametre, bir Windows sanal makinesi için Yönetici Kullanıcı adı ve parola olabilir. Parametre değerleri, belirtildiklerinde dağıtım boyunca kullanılır.

- **Değişkenler**: Bunlar dağıtım genelinde kullanılan değerleri tutmak için kullanılır. Parametrelerin aksine, dağıtım zamanında bir değişken değeri sağlanmaz. Bunun yerine, sabit kodlanmış veya dinamik olarak oluşturulur.

- **Kaynaklar**: şablonun bu bölümü, sanal makineler, depolama hesapları ve sanal ağlar gibi dağıtılacak kaynakları tanımlar.

- **Çıkış**: bir dağıtım tamamlandıktan sonra, Kaynak Yöneticisi dinamik olarak oluşturulan bağlantı dizeleri gibi verileri döndürebilir.

Aşağıdaki mekanizmalar Dağıtım Otomasyonu için kullanılabilir:

- **İşlevler**: Kaynak Yöneticisi şablonlarda birkaç işlevi kullanabilirsiniz. Bunlar, bir dizeyi küçük harfe dönüştürme, tanımlı bir kaynağın birden çok örneğini dağıtma ve hedef kaynak grubunu dinamik olarak döndürme gibi işlemleri içerir. Kaynak Yöneticisi işlevler dinamik dağıtımlar oluşturmaya yardımcı olur.

- **Kaynak bağımlılıkları**: birden çok kaynak dağıttığınızda, bazı kaynakların diğerlerine bağımlılığı olur. Dağıtımı kolaylaştırmak için, bağımlı kaynakların diğerlerinden önce dağıtılması için bir bağımlılık bildirimi kullanabilirsiniz.

- **Şablon bağlama**: bir kaynak yöneticisi şablonu içinden başka bir şablona bağlantı oluşturabilirsiniz. Bu, dağıtıma ayrışmaya hedeflenen ve amaca özgü bir şablon kümesine izin verir.

Herhangi bir metin düzenleyicisinde Kaynak Yöneticisi şablonlar oluşturabilirsiniz. Ancak, Visual Studio için Azure SDK, size yardımcı olacak araçlar içerir. Visual Studio 'yu kullanarak, bir sihirbaz aracılığıyla şablona kaynak ekleyebilir, sonra şablonu doğrudan Visual Studio içinden dağıtabilir ve hatalarını ayıklayabilirsiniz. Daha fazla bilgi için bkz. [Azure Resource Manager şablonları yazma](../../resource-group-authoring-templates.md).

Son olarak, varolan kaynak gruplarını Azure portal yeniden kullanılabilir bir şablona dönüştürebilirsiniz. Bu, var olan bir kaynak grubunun dağıtılabilir bir şablonunu oluşturmak veya yalnızca temeldeki JSON 'u incelemek istiyorsanız yararlı olabilir. Bir kaynak grubunu dışarı aktarmak için, kaynak grubunun ayarlarından **Otomasyon betiği** düğmesini seçin.

## <a name="security-of-azure-resources-rbac"></a>Azure kaynaklarının güvenliği (RBAC)

Belirli bir kapsamdaki Kullanıcı hesaplarına işletimsel erişim izni verebilirsiniz: abonelik, kaynak grubu veya tek kaynak. Bu, bir kaynak kümesini bir sanal makine ve tüm ilgili kaynaklar gibi bir kaynak grubuna dağıtabileceğiniz ve belirli bir kullanıcı veya gruba izin verdiğiniz anlamına gelir. Bu yaklaşım, erişimi yalnızca hedef kaynak grubuna ait kaynaklarla sınırlandırır. Ayrıca, sanal makine veya sanal ağ gibi tek bir kaynağa erişim izni verebilirsiniz.

Erişim vermek için kullanıcıya veya kullanıcı grubuna bir rol atarsınız. Önceden tanımlanmış birçok rol vardır. Kendi özel rollerinizi de tanımlayabilirsiniz.

[Azure 'da birkaç örnek yerleşik rol](../../role-based-access-control/built-in-roles.md)aşağıda verilmiştir:

- **Sahip**: Bu role sahip bir Kullanıcı, erişim dahil her şeyi yönetebilir.

- **Okuyucu**: Bu role sahip bir Kullanıcı, tüm türlerin kaynaklarını okuyabilir (gizli dizileri hariç) ancak değişiklik yapamaz.

- **Sanal makine katılımcısı**: Bu role sahip bir Kullanıcı sanal makineleri yönetebilir, ancak bağlı oldukları sanal ağı veya VHD dosyasının bulunduğu depolama hesabını yönetemez.

- **SQL DB katılımcısı**: Bu role sahip BIR kullanıcı SQL veritabanlarını yönetebilir, ancak güvenlikle ilgili ilkelerini yönetemez.

- **SQL Güvenlik Yöneticisi**: Bu role sahip bir Kullanıcı, SQL Server ve veritabanlarının güvenlikle ilgili ilkelerini yönetebilir.

- **Depolama hesabı katılımcısı**: Bu role sahip bir Kullanıcı, depolama hesaplarını yönetebilir ancak depolama hesaplarına erişimi yönetemez.

Daha fazla bilgi için bkz. [RBAC ve Azure portalını kullanarak erişimi yönetme](../../role-based-access-control/role-assignments-portal.md).

## <a name="azure-virtual-machines"></a>Azure Sanal Makineler

Azure sanal makineleri, Azure 'daki merkezi IaaS hizmetlerinden biridir. Azure sanal makineleri, bir Microsoft Azure veri merkezinde Windows veya Linux sanal makinelerinin dağıtımını destekler. Azure sanal makineler ile, VM yapılandırması üzerinde toplam denetiminiz vardır ve tüm yazılım yükleme, yapılandırma ve bakımın sorumluluğundadır.

Bir Azure VM 'yi dağıttığınızda Azure Marketi 'nden bir görüntü seçebilir veya size Genelleştirilmiş görüntü sağlayabilirsiniz. Bu görüntü, işletim sistemini ve ilk yapılandırmayı uygulamak için kullanılır. Dağıtım sırasında Kaynak Yöneticisi, bilgisayar adı, yönetici kimlik bilgileri ve ağ yapılandırması atama gibi bazı yapılandırma ayarlarını işleyecek. Yazılım yükleme, virüsten koruma yapılandırması ve izleme çözümleri gibi yapılandırmaları otomatik hale getirmek için Azure sanal makine uzantıları ' nı kullanabilirsiniz.

Sanal makineleri birçok farklı boyutta oluşturabilirsiniz. Sanal makinenin boyutu, işlem, bellek ve depolama kapasitesi gibi kaynak ayırmayı belirler. Bazı durumlarda, RDMA özellikli ağ bağdaştırıcıları ve SSD diskleri gibi belirli özellikler yalnızca belirli VM boyutları ile kullanılabilir. VM boyutlarının ve yeteneklerin tüm listesi için, bkz. [Windows](../../virtual-machines/windows/sizes.md) ve [Linux](../../virtual-machines/linux/sizes.md)için "Azure 'Da sanal makineler için Boyutlar".

### <a name="use-cases"></a>Uygulama alanları

Azure sanal makineleri yapılandırma üzerinde tamamen denetim sağladığından, PaaS modeline sığmayan çok çeşitli sunucu iş yükleri için idealdir. Veritabanı sunucuları (SQL Server, Oracle veya MongoDB), Windows Server Active Directory, Microsoft SharePoint ve çok daha fazlası gibi sunucu iş yükleri Microsoft Azure platformunda çalıştırılabilir. İsterseniz, çok sayıda yeniden yapılandırma olmadan şirket içi bir veri merkezindeki bu tür iş yüklerini bir veya daha fazla Azure bölgesine taşıyabilirsiniz.

### <a name="deployment-of-virtual-machines"></a>Sanal makinelerin dağıtımı

Azure portal kullanarak, Azure PowerShell modülüyle Otomasyon kullanarak veya platformlar arası CLı ile Otomasyon kullanarak Azure sanal makineleri dağıtabilirsiniz.

#### <a name="portal"></a>Portal

Azure portal kullanarak sanal makine dağıtmak için yalnızca etkin bir Azure aboneliği ve bir Web tarayıcısına erişim gerekir. Değişen yapılandırmalara sahip birçok farklı işletim sistemi görüntüsü seçebilirsiniz. Tüm depolama ve ağ gereksinimleri dağıtım sırasında yapılandırılır. Daha fazla bilgi için bkz. [Windows](../../virtual-machines/windows/quick-create-portal.md) ve [Linux](../../virtual-machines/linux/quick-create-portal.md)için "Azure Portal sanal makine oluşturma".

Bir sanal makineyi Azure portal dağıtmanın yanı sıra, portaldan bir Azure Resource Manager şablonu dağıtabilirsiniz. Bu işlem, şablonda tanımlandığı şekilde tüm kaynakları dağıtır ve yapılandırır. Daha fazla bilgi için bkz. [Kaynak Yöneticisi şablonları ve Azure Portal ile kaynak dağıtma](../../azure-resource-manager/resource-group-template-deploy-portal.md).

#### <a name="powershell"></a>PowerShell

PowerShell kullanarak bir Azure sanal makinesini dağıtmak, depolama ve ağ dahil olmak üzere tüm ilgili sanal makine kaynaklarının dağıtım otomasyonunu sağlar. Daha fazla bilgi için bkz. [Kaynak Yöneticisi ve PowerShell kullanarak WINDOWS VM oluşturma](../../virtual-machines/windows/quick-create-powershell.md).

Azure işlem kaynaklarını tek tek dağıtmanın yanı sıra, bir Azure Resource Manager şablonu dağıtmak için Azure PowerShell modülünü de kullanabilirsiniz. Daha fazla bilgi için bkz. [Kaynak Yöneticisi şablonları ve Azure PowerShell ile kaynak dağıtma](../../azure-resource-manager/resource-group-template-deploy.md).

#### <a name="command-line-interface-cli"></a>Komut satırı arabirimi (CLı)

PowerShell modülünde olduğu gibi, Azure komut satırı arabirimi de Dağıtım Otomasyonu sağlar ve Windows, OS X veya Linux sistemlerinde kullanılabilir. Azure CLı **VM hızlı oluşturma** komutunu kullanırken, tüm ilgili sanal makine kaynakları (depolama ve ağ dahil) ve sanal makine dağıtılır. Daha fazla bilgi için bkz. [CLI kullanarak Azure 'Da LINUX VM oluşturma](../../virtual-machines/linux/quick-create-cli.md).

Benzer şekilde, bir Azure Resource Manager şablonu dağıtmak için Azure CLı 'yi de kullanabilirsiniz. Daha fazla bilgi için bkz. [Kaynak Yöneticisi şablonları ve Azure CLI ile kaynak dağıtma](../../azure-resource-manager/resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-virtual-machines"></a>Sanal makineler için erişim ve güvenlik

Bir sanal makineye Internet 'ten erişmek için ilişkili ağ arabirimi veya varsa yük dengeleyici, genel bir IP adresiyle yapılandırılmalıdır. Genel IP adresi, sanal makine veya yük dengeleyiciye çözülecek bir DNS adı içerir. Daha fazla bilgi için bkz. [Azure 'Da IP adresleri](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).

Ağ güvenlik grubu (NSG) kaynağını kullanarak, genel IP adresi üzerinden sanal makineye erişimi yönetirsiniz. Bir NSG güvenlik duvarı gibi davranır ve tanımlı bir bağlantı noktası kümesindeki ağ arabirimi veya alt ağ genelinde trafiğe izin verir veya reddeder. Örneğin, bir Azure VM ile uzak masaüstü oturumu oluşturmak için NSG bağlantı noktası 3389 üzerinde gelen trafiğe izin verecek şekilde yapılandırmanız gerekir. Daha fazla bilgi için bkz. [Azure Portal kullanarak Azure 'da BIR VM 'ye bağlantı noktaları açma](../../virtual-machines/windows/nsg-quickstart-portal.md).

Son olarak, herhangi bir bilgisayar sisteminin yönetiminde olduğu gibi, güvenlik kimlik bilgilerini ve yazılım güvenlik duvarlarını kullanarak işletim sisteminde bir Azure sanal makinesi için güvenlik sağlamanız gerekir.

## <a name="azure-storage"></a>Azure Depolama

Azure depolama, dayanıklı, ölçeklenebilir ve yedekli depolama sağlayan, Microsoft tarafından yönetilen bir hizmettir. Herhangi bir kaynak dağıtım yöntemi kullanarak bir Azure Depolama hesabını kaynak olarak herhangi bir kaynak grubuna ekleyebilirsiniz. Azure dört depolama türü içerir: BLOB depolama, dosya depolama, tablo depolama ve kuyruk depolama. Bir depolama hesabı dağıtıldığında, genel amaçlı ve BLOB depolama olmak üzere iki hesap türü vardır. Genel amaçlı bir depolama hesabı, dört depolama türüne erişmenizi sağlar. BLOB depolama hesapları, genel amaçlı hesaplara benzer, ancak sık ve soğuk erişim katmanları içeren özel blob 'lar içerir. BLOB depolama hakkında daha fazla bilgi için bkz. [Azure Blob depolama](../../storage/blobs/storage-blob-storage-tiers.md).

Azure depolama hesapları farklı artıklık düzeyleriyle yapılandırılabilir:

- **Yerel olarak yedekli depolama** , yazma başarılı bir şekilde kabul etmeden önce tüm verilerin üç kopyasının zaman uyumlu olarak yapılmasını sağlayarak yüksek kullanılabilirlik sağlar. Bu kopyalar tek bir bölgedeki tek bir tesisde depolanır. Çoğaltmalar ayrı hata etki alanları ve yükseltme etki alanlarında bulunur. Bu, verilerinizi barındıran bir depolama düğümü başarısız olsa veya güncelleştirilmesini çevrimdışı duruma getirildiğinde bile verilerin kullanılabildiği anlamına gelir.

- **Coğrafi olarak yedekli depolama** , yüksek kullanılabilirlik için birincil bölgedeki verilerin üç zaman uyumlu kopyasını yapar ve ardından olağanüstü durum kurtarma için eşlenmiş bir bölgede üç kopya zaman uyumsuz olarak yapılır.

- **Okuma Erişimli Coğrafi olarak yedekli depolama** , coğrafi olarak yedekli depolama ve ikincil bölgedeki verileri okuma imkanına sahiptir. Bu özellik kısmi olağanüstü durum kurtarma için uygun hale getirir. Birincil bölgeyle ilgili bir sorun varsa, uygulamanızı eşleştirilmiş bölgeye salt okuma erişimi olacak şekilde değiştirebilirsiniz.

### <a name="use-cases"></a>Uygulama alanları

Her depolama türünün farklı bir kullanım durumu vardır.

#### <a name="blob-storage"></a>Blob depolaması

Sözcük *blobu* , *ikili büyük nesnenin*kısaltması olur. Blob 'lar, bilgisayarınızda depoladığınız gibi yapılandırılmamış dosyalardır. Blob Storage belge, medya dosyası veya uygulama yükleyici gibi her tür metin veya ikili veri depolayabilir. Blob Storage aynı zamanda nesne depolama olarak adlandırılır. Azure Blob depolama, Azure sanal makineler veri disklerini de barındırır.

Azure depolama üç tür blob destekler:

- **Blok Blobları** , normal dosyaları 195 GB 'a kadar (4 MB × 50.000 blok) tutmak için kullanılır. Blok Blobları için birincil kullanım örneği, medya dosyaları veya Web siteleri için görüntü dosyaları gibi baştan sona okunan dosyaların depolanması olur. Bunlar blok Blobları olarak adlandırılır çünkü 64 MB 'tan büyük dosyalar küçük bloklar olarak karşıya yüklenmelidir. Bu bloklar daha sonra son bloba birleştirilir (veya kaydedilir).

- **Sayfa Blobları** , boyutu 1 TB 'ye kadar olan rastgele erişim dosyalarını tutmak için kullanılır. Sayfa Blobları, Azure 'da IaaS işlem hizmeti olan Azure sanal makineleri için dayanıklı diskler sağlayan VHD 'ler için genellikle yedekleme depolama alanı olarak kullanılır. Bunlar, 512 baytlık sayfalara rastgele okuma/yazma erişimi sağladığından sayfa Blobları olarak adlandırılmaktadır.

- **Ekleme Blobları** blok Blobları gibi bloklardan oluşur, ancak ekleme işlemleri için en iyi duruma getirilir. Bunlar, bir veya daha fazla kaynaktan aynı bloba bilgi günlüğe kaydetmek için sık kullanılır. Örneğin, birden çok VM 'de çalışan bir uygulama için izleme günlüğü 'nün tümünü aynı ekleme blobuna yazabilirsiniz. Tek bir ekleme blobu 195 GB 'a kadar olabilir.

Daha fazla bilgi için bkz. [.NET kullanarak Azure Blob depolamayı kullanmaya başlama](../../storage/blobs/storage-dotnet-how-to-use-blobs.md).

#### <a name="file-storage"></a>Dosya depolama

Azure dosya depolama, standart sunucu Ileti bloğu (SMB) protokolünü kullanarak bulutta dosya paylaşımları sunan bir hizmettir. Hizmet hem SMB 2,1 hem de SMB 3,0 'yi destekler. Azure dosya depolama ile, dosya paylaşımlarına yönelik uygulamaları, maliyetli yeniden yazar olmadan hızlı bir şekilde Azure 'a geçirebilirsiniz. Azure sanal makinelerinde, bulut hizmetlerinde veya şirket içi istemcilerde çalışan uygulamalar, buluta bir dosya paylaşımının bağlanabilir. Bu, bir masaüstü uygulamasının tipik bir SMB payını taktığından benzerdir. Ardından herhangi sayıda uygulama bileşeni eş zamanlı olarak File Storage paylaşımını bağlayıp buna erişim sağlayabilir.

Bir dosya depolama payı standart bir SMB dosya paylaşımında olduğundan, Azure 'da çalışan uygulamalar dosya sistemi g/ç API 'Leri aracılığıyla paylaşımdaki verilere erişebilir. Bu nedenle, geliştiriciler mevcut uygulamaları geçirmek için mevcut kodlarını ve yeteneklerini kullanabilir. BT uzmanları, Azure uygulamalarının yönetiminin bir parçası olarak dosya depolama paylaşımlarını oluşturmak, bağlamak ve yönetmek için PowerShell cmdlet 'lerini kullanabilir.

Daha fazla bilgi için bkz. [Windows 'Da Azure dosya depolama ile çalışmaya başlama](../../storage/files/storage-how-to-use-files-windows.md) veya [Linux ile Azure dosya depolama 'yı kullanma](../../storage/files/storage-how-to-use-files-linux.md).

#### <a name="table-storage"></a>Tablo depolaması

Azure Table Storage, bulutta yapılandırılmış NoSQL verileri depolayan bir hizmettir. Tablo depolama, şema daha az bir tasarıma sahip bir anahtar/öznitelik deposudur. Tablo Depolaması şemadan daha az olduğundan, uygulamanızın ihtiyaçları geliştikçe verilerinizi kolayca uyarlayabilirsiniz. Her türlü uygulama için verilere erişim hızlı ve uygun maliyetlidir. Table Storage, benzer hacimdeki veriler için geleneksel SQL’e oranla çok daha düşük maliyetlidir.

Web uygulamaları için kullanıcı verileri, adres defterleri, cihaz bilgileri ve hizmetiniz için gerekli olan tüm diğer meta veri türleri gibi esnek veri kümelerini depolamak üzere Table Storage’ı kullanabilirsiniz. Bir tabloda herhangi bir sayıda varlığı saklayabilirsiniz. Depolama hesabı, depolama hesabının kapasite sınırına kadar herhangi bir sayıda tablo içerebilir.

Daha fazla bilgi için bkz. [Azure Tablo Depolamayı kullanmaya başlama](../../cosmos-db/table-storage-how-to-use-dotnet.md).

#### <a name="queue-storage"></a>Kuyruk depolama

Azure Queue depolama birimi, uygulama bileşenleri arasında bulut mesajlaşma özelliği sağlar. Uygulamaları ölçeklendirmek için tasarlarken, uygulama bileşenleri genellikle birbirinden bağımsız olarak ölçeklendirilebilecek şekilde ayrılır. Kuyruk depolama bulutta, masaüstünde, şirket içi sunucuda veya mobil bir cihazda çalışan uygulama bileşenleri arasındaki iletişim için zaman uyumsuz mesajlaşma sunar. Kuyruk depolama zaman uyumsuz görevlerin yönetilmesini ve süreç iş akışlarının oluşturulmasını da destekler.

Daha fazla bilgi için bkz. [Azure kuyruk depolamayı kullanmaya başlama](../../storage/queues/storage-dotnet-how-to-use-queues.md).

### <a name="deploying-a-storage-account"></a>Depolama hesabı dağıtma

Depolama hesabı dağıtmaya yönelik çeşitli seçenekler vardır.

#### <a name="portal"></a>Portal

Azure portal kullanarak depolama hesabı dağıtmak için yalnızca etkin bir Azure aboneliği ve bir Web tarayıcısına erişim gerekir. Yeni veya var olan bir kaynak grubuna yeni bir depolama hesabı dağıtabilirsiniz. Depolama hesabını oluşturduktan sonra, portalı kullanarak bir blob kapsayıcısı veya dosya paylaşma oluşturabilirsiniz. Programlı bir şekilde tablo ve kuyruk depolama varlıkları oluşturabilirsiniz. Daha fazla bilgi için bkz. [Depolama hesabı oluşturma](../../storage/common/storage-quickstart-create-account.md).

Azure portal bir depolama hesabı dağıtmaya ek olarak portaldan bir Azure Resource Manager şablonu dağıtabilirsiniz. Bu, herhangi bir depolama hesabı da dahil olmak üzere şablonda tanımlanan tüm kaynakları dağıtır ve yapılandırır. Daha fazla bilgi için bkz. [Kaynak Yöneticisi şablonları ve Azure Portal ile kaynak dağıtma](../../azure-resource-manager/resource-group-template-deploy-portal.md).

#### <a name="powershell"></a>PowerShell

PowerShell kullanarak bir Azure depolama hesabı dağıtmak, depolama hesabının tüm dağıtım otomasyonunu sağlar. Daha fazla bilgi için bkz. [Azure depolama ile Azure PowerShell kullanma](../../storage/common/storage-powershell-guide-full.md).

Azure kaynaklarını tek tek dağıtmanın yanı sıra, bir Azure Resource Manager şablonu dağıtmak için Azure PowerShell modülünü kullanabilirsiniz. Daha fazla bilgi için bkz. [Kaynak Yöneticisi şablonları ve Azure PowerShell ile kaynak dağıtma](../../azure-resource-manager/resource-group-template-deploy.md).

#### <a name="command-line-interface-cli"></a>Komut satırı arabirimi (CLı)

PowerShell modülünde olduğu gibi, Azure komut satırı arabirimi de Dağıtım Otomasyonu sağlar ve Windows, OS X veya Linux sistemlerinde kullanılabilir. Depolama hesabı oluşturmak için Azure CLı **depolama hesabı oluştur** komutunu kullanabilirsiniz. Daha fazla bilgi için Azure [depolama ile Azure CLI kullanma](../../storage/common/storage-azure-cli.md) konusuna bakın.

Benzer şekilde, bir Azure Resource Manager şablonu dağıtmak için Azure CLı 'yi de kullanabilirsiniz. Daha fazla bilgi için bkz. [Kaynak Yöneticisi şablonları ve Azure CLI ile kaynak dağıtma](../../resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-azure-storage"></a>Azure depolama için erişim ve güvenlik

Azure depolama 'nın Azure portal, VM oluşturma ve işlem sırasında ve depolama istemci kitaplıklarından farklı yollarla erişilir.

#### <a name="virtual-machine-disks"></a>Sanal makine diskleri

Bir sanal makine dağıttığınızda, sanal makine işletim sistemi diskini ve tüm ek veri disklerini tutmak için bir depolama hesabı da oluşturmanız gerekir. Var olan bir depolama hesabını seçebilir veya yeni bir depolama hesabı oluşturabilirsiniz. Blob 'un en büyük boyutu 1.024 GB olduğundan, tek bir VM diskinin en fazla 1.023 GB boyutunda olması gerekir. Daha büyük bir veri diski yapılandırmak için, sanal makineye birden çok veri diski sunabilir ve bunları tek bir mantıksal disk olarak birlikte havuzda oluşturabilirsiniz. Daha fazla bilgi için bkz. [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) ve [Linux](../../virtual-machines/linux/tutorial-manage-disks.md)Için "Azure disklerini yönetme".

#### <a name="storage-tools"></a>Depolama araçları

Azure depolama hesaplarına, Visual Studio Cloud Explorer gibi birçok farklı depolama araştırması aracılığıyla erişilebilir. Bu araçlar, depolama hesaplarına ve verilerine göz atmanıza olanak sağlar. Daha fazla bilgi ve kullanılabilir depolama gezginleri listesi için bkz. [Azure Storage istemci araçları](../../storage/common/storage-explorers.md).

#### <a name="storage-api"></a>Depolama API 'SI

Depolama kaynaklarına, HTTP/HTTPS istekleri yapan herhangi bir dil tarafından erişilebilir. Ayrıca, Azure Storage birkaç popüler dilde programlama kitaplıkları sunar. Bu kitaplıklar zaman uyumlu ve zaman uyumsuz çağrı, işlemlerin toplu işi, özel durum yönetimi ve otomatik yeniden denemeler gibi ayrıntıları işleyerek Azure depolama ile çalışmayı basitleştirir. Daha fazla bilgi için bkz. [Azure Storage service REST API başvurusu](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

#### <a name="storage-access-keys"></a>Depolama erişim tuşları

Her depolama hesabının, birincil ve ikincil olmak üzere iki kimlik doğrulama anahtarı vardır. , Depolama erişim işlemleri için kullanılabilir. Bu depolama anahtarları, bir depolama hesabının güvenliğini sağlamaya yardımcı olmak için kullanılır ve verilere programlı bir şekilde erişmek için gereklidir. Güvenliği geliştirmek için anahtarların zaman zaman geçişine izin veren iki anahtar vardır. Hesap adıyla birlikte, depolama hesabındaki herhangi bir veriye sınırsız erişime izin verdiğinden anahtarların güvenli tutulması önemlidir.

#### <a name="shared-access-signatures"></a>Paylaşılan erişim imzaları

Kullanıcıların depolama kaynaklarınıza denetimli erişimi olması gerekiyorsa, paylaşılan erişim imzası oluşturabilirsiniz. Paylaşılan erişim imzası, bir depolama kaynağına temsilci erişimi sağlayan bir URL 'ye eklenebileceği bir belirteçtir. Belirtece sahip olan herkes, geçerli olduğu süre boyunca gösterdiği izinlerle işaret eden kaynağa erişebilir olabilir. Daha fazla bilgi için bkz. [paylaşılan erişim Imzalarını kullanma](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="azure-virtual-network"></a>Azure Sanal Ağı

Sanal makineler arasındaki iletişimleri desteklemek için sanal ağlar gereklidir. Alt ağları, özel IP adresini, DNS ayarlarını, güvenlik filtrelemeyi ve yük dengelemeyi tanımlayabilirsiniz. Azure farklı kullanımlar durumlarını destekler: yalnızca bulut ağları veya karma sanal ağlar.

### <a name="cloud-only-virtual-networks"></a>Yalnızca bulut sanal ağları

Azure sanal ağı, varsayılan olarak yalnızca Azure 'da depolanan kaynaklarla erişilebilir. Aynı sanal ağa bağlı kaynaklar birbirleriyle iletişim kurabilir. Sanal makineyi Internet üzerinden erişilebilir hale getirmek için sanal makine ağ arabirimlerini ve yük dengeleyicileri genel bir IP adresi ile ilişkilendirebilirsiniz. Ağ güvenlik grubu kullanarak genel kullanıma sunulan kaynaklara güvenli erişim sağlanmasına yardımcı olabilirsiniz.

![2 katmanlı bir Web uygulaması için Azure sanal ağı](https://docs.microsoft.com/azure/load-balancer/media/load-balancer-internal-overview/ic744147.png)

### <a name="hybrid-virtual-networks"></a>Karma sanal ağlar

ExpressRoute veya siteden siteye VPN bağlantısı kullanarak şirket içi bir ağı bir Azure sanal ağına bağlayabilirsiniz. Bu yapılandırmada, Azure sanal ağı aslında şirket içi ağınızın bulut tabanlı uzantısıdır.

Azure sanal ağı şirket içi ağınıza bağlı olduğundan, şirket içi sanal ağlar, kuruluşunuzun kullandığı adres alanının benzersiz bir bölümünü kullanmalıdır. Farklı şirket konumlarına belirli bir IP alt ağına atanabileceği şekilde, Azure, ağınızı genişletmenizi sağlayan başka bir konum haline gelir.
Sanal ağ dağıtmak için çeşitli seçenekler vardır.

- [Portal](../..//virtual-network/quick-create-portal.md)

- [PowerShell](../../virtual-network/quick-create-powershell.md)

- [Komut satırı arabirimi (CLı)](../../virtual-network/quick-create-cli.md)

- Azure Resource Manager şablonları

> **Ne zaman kullanılır**: Azure 'da VM 'lerle çalışırken sanal ağlarla çalışacaksınız. Bu, VM 'lerinizi, şirket içi veri merkezlerine benzer şekilde herkese açık ve özel alt ağlarda parçalara ayırma olanağı sağlar.
> 
> **Kullanmaya başlayın**: Azure Portal kullanarak Azure sanal ağını dağıtmak, yalnızca etkin bir Azure aboneliği gerektirir ve bir Web tarayıcısına erişim sağlar. Yeni veya var olan bir kaynak grubuna yeni bir sanal ağ dağıtabilirsiniz. Portaldan yeni bir sanal makine oluştururken, var olan bir sanal ağı seçebilir veya yeni bir ağ oluşturabilirsiniz. Kullanmaya başlayın ve [Azure Portal kullanarak bir sanal ağ oluşturun](../../virtual-network/quick-create-portal.md).

### <a name="access-and-security-for-virtual-networks"></a>Sanal ağlar için erişim ve güvenlik

Bir ağ güvenlik grubu kullanarak Azure sanal ağlarının güvenliğini sağlamaya yardımcı olabilirsiniz. NSG 'ler bir sanal ağdaki VM örneklerinizin ağ trafiğine izin veren veya reddeden erişim denetim listesi (ACL) kurallarının bir listesini içerir. NSG 'leri alt ağlar veya alt ağ içindeki tek bir VM örneğiyle ilişkilendirebilirsiniz. Bir NSG 'yi bir alt ağ ile ilişkilendirdiğinizde, ACL kuralları bu alt ağdaki tüm sanal makine örneklerine uygulanır. Ayrıca, bir NSG 'yi bu VM ile doğrudan ilişkilendirerek, tek bir VM 'ye giden trafiği daha da kısıtlayabilirsiniz. Daha fazla bilgi için bkz. [Ağ güvenlik grupları ile ağ trafiğini filtreleme](../../virtual-network/security-overview.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Windows VM oluşturma](../../virtual-machines/windows/quick-create-portal.md)
- [Linux VM oluşturma](../../virtual-machines/linux/quick-create-portal.md)
