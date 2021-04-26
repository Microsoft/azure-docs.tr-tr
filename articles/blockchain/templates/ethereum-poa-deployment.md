---
title: Azure 'da Ethereum yetki kanıtlama Konsorsiyumu çözüm şablonu dağıtma
description: Azure 'da çok siteli bir konsorsiyumum ağını dağıtmak ve yapılandırmak için Ethereum yetkili bir Konsorsiyumu çözümünü kullanın
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3
ms.openlocfilehash: f4b12a9b3b830fcc4f45cb5e957232fee5a756a3
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078654"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Azure 'da Ethereum yetki kanıtlama Konsorsiyumu çözüm şablonu dağıtma

Çok sayıda Azure ve Ethereum bilgisine sahip çok üyeli bir konsorsiyum yetkilisi olan bir ağ dağıtımı, yapılandırmak ve yönetmek için [Ethereum-Authority Konsorsiyumu önizleme Azure çözüm şablonunu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum) kullanabilirsiniz.

Çözüm şablonu, Microsoft Azure işlem, ağ ve depolama hizmetleri kullanılarak bir blok zinciri ağı kaplama sağlamak için her bir konsorsiyum üyesi tarafından kullanılabilir. Her bir konsorsiyumun ağ izi, bir uygulamanın veya kullanıcının Ethereum işlemlerini göndermek için etkileşime girebileceği yük dengeli bir doğrulayıcı düğümleri kümesinden oluşur.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Bir Azure blok zinciri çözümü seçin

Ethereum yetki kanıtlama Konsorsiyumu çözüm şablonunu kullanmayı seçmeden önce, senaryonuzu, kullanılabilir Azure blok zinciri seçeneklerinin yaygın kullanım durumları ile karşılaştırın.

> [!IMPORTANT]
> Azure çözüm şablonunda Ethereum yerine [Azure blok zinciri hizmetini](../service/overview.md) kullanmayı göz önünde bulundurun. Azure blok zinciri hizmeti, desteklenen bir yönetilen Azure hizmetidir. Eşlik eden Ethereum, topluluk odaklı geliştirme ve bakım 'a geçti. Daha fazla bilgi için bkz. [eşlik Ethereum, OPENETHEREUM DAO 'ya geçme](https://www.parity.io/parity-ethereum-openethereum-dao/).

Seçenek | Hizmet modeli | Yaygın kullanım durumu
-------|---------------|-----------------
Çözüm şablonları | IaaS | Çözüm şablonları, tam olarak yapılandırılmış bir blok zinciri ağ topolojisi sağlamak için kullanabileceğiniz Azure Resource Manager şablonlardır. Şablonlar, belirli bir blok zinciri ağ türü için Microsoft Azure işlem, ağ ve depolama hizmetleri dağıtır ve yapılandırır. Çözüm şablonları, bir hizmet düzeyi sözleşmesi olmadan sağlanır. Destek için [Microsoft Q&soru sayfasını](/answers/topics/azure-blockchain-workbench.html) kullanın.
[Azure Blok Zinciri Hizmeti](../service/overview.md) | PaaS | Azure blok zinciri hizmeti önizlemesi, konsorsiyum blok zinciri ağlarının yönetimini, yönetimini ve yönetimini basitleştirir. PaaS, konsorsiyum yönetimi veya sözleşme ve işlem gizliliği gerektiren çözümler için Azure blok zinciri hizmetini kullanın.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS ve PaaS | Azure blok zinciri çalışma ekranı önizlemesi, iş süreçlerini ve verileri diğer kuruluşlarla paylaşmak üzere blok zinciri uygulamaları oluşturmanıza ve dağıtmanıza yardımcı olmak üzere tasarlanmış bir Azure hizmetleri ve özellikleri koleksiyonudur. Bir blok zinciri çözümünü veya blok zinciri uygulaması kavram kanıtı 'nı prototip için Azure blok zinciri çalışma ekranı 'nı kullanın. Azure Blockchain Workbench hizmet düzeyi anlaşması olmadan sunulur. Destek için [Microsoft Q&soru sayfasını](/answers/topics/azure-blockchain-workbench.html) kullanın.

## <a name="solution-architecture"></a>Çözüm mimarisi

Ethereum çözüm şablonunu kullanarak, tek veya çok bölgeli bir çok üyeli bir adet-üye kanıtı Konsorsiyumu ağını dağıtabilirsiniz.

![dağıtım mimarisi](./media/ethereum-poa-deployment/deployment-architecture.png)

Her bir konsorsiyum üye dağıtımı şunları içerir:

* PoA Doğrulayıcıları çalıştırmaya yönelik sanal makineler
* RPC, eşleme ve idare DApp isteklerini dağıtmaya yönelik Azure Load Balancer
* Doğrulayıcı kimliklerini güvenli hale getirmek için Azure Key Vault
* Kalıcı ağ bilgilerini barındırmak ve kiralamayı koordine etmek için Azure depolama
* Günlük ve performans istatistikleri toplamak için Azure Izleyici
* Özel VNET 'lerde VPN bağlantılarına izin vermek için sanal ağ geçidi (isteğe bağlı)

Varsayılan olarak, RPC ve eşleme uç noktalarına, abonelikler ve bulutlar arasında basitleştirilmiş bağlantı sağlamak için genel IP üzerinden erişilebilir. Uygulama düzeyi erişim denetimleri için, [eşlik 'nın izinleri sözleşmeleri](https://openethereum.github.io/Permissioning.html)' ni kullanabilirsiniz. Platformlar arası bağlantı için VNet ağ geçitlerinden yararlanan VPN arkasında dağıtılan ağlar desteklenir. VPN ve VNet dağıtımları daha karmaşık olduğundan, bir çözümü prototip oluştururken bir genel IP modeliyle başlamak isteyebilirsiniz.

Docker Kapsayıcıları güvenilirlik ve modülarfor için kullanılır. Azure Container Registry, her dağıtımın parçası olarak sürümlü görüntüleri barındırmak ve barındırmak için kullanılır. Kapsayıcı görüntüleri aşağıdakilerden oluşur:

* Orchestrator-kimlikler ve idare sözleşmeleri oluşturur. Kimlikleri bir kimlik deposunda depolar.
* Eşlik istemcisi-kimlik deposundan kimlik kiralamaları. Eşleri bulur ve eşlere bağlanır.
* Etistats Aracısı-RPC aracılığıyla yerel günlükleri ve istatistikleri toplar ve bilgileri Azure Izleyici 'ye iter.
* İdare sözleşmeleri ile etkileşim kurmak için idare DApp-Web arabirimi.

### <a name="validator-nodes"></a>Doğrulayıcı düğümleri

Yetki kanıtlama protokolünde, doğrulayıcı düğümleri geleneksel Miner düğümlerinin yerini alır. Her doğrulayıcının, blok oluşturma işlemine katılmasına izin veren benzersiz bir Ethereum kimliği vardır. Her konsorsiyum üyesi, coğrafi yedeklilik için beş bölge genelinde iki veya daha fazla Doğrulayıcı düğümü sağlayabilir. Doğrulayıcı düğümleri, temel alınan dağıtılmış defter durumunda konsensus 'a gelmesi için diğer Doğrulayıcı düğümleriyle iletişim kurar. Ağa yönelik dengeli bir katılım sağlamak için, her bir konsorsiyumun, ağdaki ilk Üyeden daha fazla Doğrulayıcıları kullanmasını yasaktır. Örneğin, ilk üye üç Doğrulayıcı dağıttığında, her üyenin en fazla üç doğrulayıcısı olabilir.

### <a name="identity-store"></a>Kimlik deposu

Oluşturulan Ethereum kimliklerini güvenli bir şekilde tutan her üyenin aboneliğine bir kimlik deposu dağıtılır. Her Doğrulayıcı için, düzenleme kapsayıcısı bir Ethereum özel anahtarı oluşturur ve Azure Key Vault depolar.

## <a name="deploy-ethereum-consortium-network"></a>Ethereum Consortium ağını dağıtma

Bu kılavuzda, çok taraflı bir Ethereum Konsorsiyumu ağı oluşturduğunuzu varsayalım. Aşağıdaki akış, çok taraflı bir dağıtıma örnektir:

1. Üç üye, her biri MetaMask kullanarak bir Ethereum hesabı oluşturur
1. *A üyesi bir* Ethereum POA dağıtır ve bu kişilerin Ethereum ortak adresini sağlar
1. *A üyesi* , *B üyesine* ve *C üyesine* yönelik Consortium URL 'sini sağlar
1. *Üye B* ve *üye C* dağıtımı, Ethereum POA, Ethereum ortak adresi ve *ÜYESI A*'nın Konsorsiyumu URL 'si
1. *B üyesi B* 'de yönetici olarak *bir oy üyesi*
1. Üye *A* ve *üye B* üye *C* 'yi yönetici olarak Oylar

Sonraki bölümlerde, ağdaki ilk üyenin parmak izini nasıl yapılandıracağınız gösterilmektedir.

### <a name="create-resource"></a>Kaynak oluşturma

[Azure Portal](https://portal.azure.com)sol üst köşedeki **kaynak oluştur** ' u seçin.

**Blok zinciri**  >  **Ethereum yetki kanıtlama Konsorsiyumu (Önizleme)** öğesini seçin.

### <a name="basics"></a>Temel Bilgiler

**Temel bilgiler** altında herhangi bir dağıtım için standart parametrelerin değerlerini belirtin.

![Temel Bilgiler](./media/ethereum-poa-deployment/basic-blade.png)

Parametre | Açıklama | Örnek değer
----------|-------------|--------------
Yeni bir ağ oluşturun veya var olan ağa katılırsanız | Yeni bir konsorsiyum ağı oluşturabilir veya önceden var olan bir konsorsiyum ağına katılabilir. Var olan bir ağa katılmak için ek parametreler gerekir. | Yeni oluştur
E-posta Adresi | Dağıtımınız hakkında bilgi ile dağıtım tamamlandığında bir e-posta bildirimi alırsınız. | Geçerli bir e-posta adresi
VM Kullanıcı adı | Dağıtılan her VM 'nin Yönetici Kullanıcı adı | 1-64 alfasayısal karakter
Kimlik doğrulaması türü | Sanal makinede kimlik doğrulama yöntemi. | Parola
Parola | Dağıtılan sanal makinelerin her biri için yönetici hesabının parolası. Tüm VM 'Ler başlangıçta aynı parolaya sahiptir. Sağlama sonrasında parolayı değiştirebilirsiniz. | 12-72 karakter 
Abonelik | Consortium ağının dağıtılacağı abonelik |
Kaynak Grubu| Consortium ağının dağıtılacağı kaynak grubu. | myResourceGroup
Konum | Kaynak grubu için Azure bölgesi. | Batı ABD 2

**Tamam**’ı seçin.

### <a name="deployment-regions"></a>Dağıtım bölgeleri

*Dağıtım bölgeleri*' nin altında, her biri için bölgelerin ve konumların sayısını belirtin. En fazla beş bölgede dağıtım yapabilirsiniz. İlk bölge, *temel bilgiler* bölümünden kaynak grubu konumuyla eşleşmelidir. Geliştirme veya test ağları için, her üye için tek bir bölge kullanabilirsiniz. Üretim için, yüksek kullanılabilirlik için iki veya daha fazla bölgeye dağıtın.

![dağıtım bölgeleri](./media/ethereum-poa-deployment/deployment-regions.png)

Parametre | Açıklama | Örnek değer
----------|-------------|--------------
Bölge sayısı|Konsorsiyum ağını dağıtmak için bölge sayısı| 2
İlk bölge | Consortium ağını dağıtmaya yönelik ilk bölge | Batı ABD 2
İkinci bölge | Konsorsiyum ağını dağıtmak için ikinci bölge. Bölge sayısı iki veya daha büyükse ek bölgeler görülebilir. | Doğu ABD 2

**Tamam**’ı seçin.

### <a name="network-size-and-performance"></a>Ağ boyutu ve performansı

*Ağ boyutu ve performans* altında, konsorsiyumun ağ boyutu için giriş belirtin. Doğrulayıcı düğümü depolama boyutu, blok zincirinin olası boyutunu belirler. Boyut dağıtımdan sonra değiştirilebilir.

![Ağ boyutu ve performansı](./media/ethereum-poa-deployment/network-size-and-performance.png)

Parametre | Açıklama | Örnek değer
----------|-------------|--------------
Yük dengeli Doğrulayıcı düğümlerinin sayısı | Ağın bir parçası olarak sağlanacak Doğrulayıcı düğümlerinin sayısı. | 2
Doğrulayıcı düğümü depolama performansı | Dağıtılan Doğrulayıcı düğümlerinin her biri için yönetilen disk türü. Fiyatlandırma hakkında ayrıntılı bilgi için bkz. [Depolama fiyatlandırması](https://azure.microsoft.com/pricing/details/managed-disks/) | Standart SSD
Doğrulayıcı düğümü sanal makine boyutu | Doğrulayıcı düğümleri için kullanılan sanal makine boyutu. Fiyatlandırma hakkında ayrıntılı bilgi için bkz. [sanal makine fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) | Standart D2 v3

Sanal makine ve depolama katmanı, ağ performansını etkiler.  Maliyet verimliliği ' nı seçmenize yardımcı olması için aşağıdaki tabloyu kullanın:

Sanal makine SKU 'SU|Depolama katmanı|Fiyat|Aktarım hızı|Gecikme süresi
---|---|---|---|---
F1|Standart SSD|zayıf|zayıf|yüksek
D2_v3|Standart SSD|orta|orta|orta
F16s|Premium SSD|yüksek|yüksek|zayıf

**Tamam**’ı seçin.

### <a name="ethereum-settings"></a>Ethereum ayarları

*Ethereum ayarları* altında, Ethereum ile ilgili yapılandırma ayarlarını belirtin.

![Ethereum ayarları](./media/ethereum-poa-deployment/ethereum-settings.png)

Parametre | Açıklama | Örnek değer
----------|-------------|--------------
Konsorsiyum üye KIMLIĞI | Konsorsiyum ağına katılan her üyeyle ilişkili KIMLIK. Çarpışmadan kaçınmak için IP adresi alanlarını yapılandırmak için kullanılır. Özel ağ için, üye KIMLIĞI aynı ağdaki farklı kuruluşlarda benzersiz olmalıdır.  Aynı kuruluş birden çok bölgeye dağıttığında bile benzersiz bir üye KIMLIĞI gereklidir. Bu parametrenin değerini, çakışma olmadığından emin olmak için diğer birleştirme üyeleriyle paylaşmanız gerektiğinden yapın. Geçerli Aralık 0 ile 255 arasındadır. | 0
Ağ KIMLIĞI | Dağıtmakta olan konsorsiyumum ağının ağ KIMLIĞI. Her bir Ethereum ağının kendi ağ KIMLIĞI vardır ve bu, genel ağın KIMLIĞI olan 1 ' dir. Geçerli Aralık 5 ile 999.999.999 arasındadır | 10101010
Yönetici Ethereum adresi | PoA idaresinde katılım için kullanılan Ethereum hesap adresi. Ethereum adresini oluşturmak için MetaMask kullanabilirsiniz. |
Gelişmiş Seçenekler | Ethereum ayarları için Gelişmiş Seçenekler | Etkinleştir
Genel IP kullanarak dağıtma | Özel VNet seçilirse, ağ bir VNet ağ geçidinin arkasında dağıtılır ve eşleme erişimi kaldırılır. Özel VNet için, bağlantının uyumlu olması için tüm üyelerin VNet Gateway kullanması gerekir. | Genel IP
Blok gaz sınırı | Ağın başlangıç blok gaz sınırı. | 50000000
Yeniden mühürlemek süresi (sn) | Ağ üzerinde işlem olmadığında boş blokların oluşturulma sıklığı. Daha yüksek bir sıklık daha hızlı ve daha fazla depolama maliyetine sahip olacaktır. | 15
İşlem Izin sözleşmesi | Işlem Izinleri sözleşmesi için bayt kodu. Akıllı sözleşme dağıtımını ve yürütmeyi, izin verilen Ethereum hesapları listesine kısıtlar. |

**Tamam**’ı seçin.

### <a name="monitoring"></a>İzleme

İzleme, ağınız için bir günlük kaynağı yapılandırmanıza olanak tanır. İzleme Aracısı, ağ durumu veya hata ayıklama sorunlarını hızla denetleyebilme olanağı sunan, ağınızdaki yararlı ölçümleri ve günlükleri toplar ve bunları yüzey halinde sunar.

![Azure izleyici](./media/ethereum-poa-deployment/azure-monitor.png)

Parametre | Açıklama | Örnek değer
----------|-------------|--------------
İzleme | İzlemeyi etkinleştirme seçeneği | Etkinleştir
Mevcut Azure Izleyici günlüklerine bağlanma | Yeni bir Azure Izleyici günlükleri örneği oluşturma veya var olan bir örneğe katma seçeneği | Yeni oluştur
Konum | Yeni örneğin dağıtıldığı bölge | Doğu ABD
Mevcut Log Analytics çalışma alanı KIMLIĞI (mevcut Azure Izleyici günlüklerine Bağlan = var olanı Birleştir)|Mevcut Azure Izleyici günlükleri örneğinin çalışma alanı KIMLIĞI|NA
Mevcut Log Analytics birincil anahtarı (mevcut Azure Izleyici günlüklerine bağlanma = var olanı Birleştir)|Mevcut Azure Izleyici günlükleri örneğine bağlanmak için kullanılan birincil anahtar|NA

**Tamam**’ı seçin.

### <a name="summary"></a>Özet

Belirtilen girişleri gözden geçirmek ve temel dağıtım öncesi doğrulamayı çalıştırmak için Özet ' e tıklayın. Dağıtılmadan önce şablonu ve parametreleri indirebilirsiniz.

Dağıtılacak **Oluştur** ' u seçin.

Dağıtım VNet ağ geçitleri içeriyorsa, dağıtım 50 45 dakika sürebilir.

## <a name="deployment-output"></a>Dağıtım çıkışı

Dağıtım tamamlandıktan sonra, Azure portal kullanarak gerekli parametrelere erişebilirsiniz.

### <a name="confirmation-email"></a>Onay e-postası

Bir e-posta adresi ([temel bilgiler bölümü](#basics)) sağlarsanız, dağıtım bilgilerini ve bu belgelerin bağlantılarını içeren bir e-posta gönderilir.

![Dağıtım e-postası](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>Portal

Dağıtım başarıyla tamamlandıktan ve tüm kaynaklar sağlandıktan sonra, çıkış parametrelerini kaynak grubunuzda görüntüleyebilirsiniz.

1. Portalda kaynak grubunuza gidin.
1. **> dağıtımlarını genel bakış ' ı** seçin.

    ![Kaynak grubuna genel bakış](./media/ethereum-poa-deployment/resource-group-overview.png)

1. **Microsoft-Azure-blockzinciri. Azure-blockzincirini-Ether-...** dağıtımı ' nı seçin.
1. **Çıktılar** bölümünü seçin.

    ![Dağıtım çıkışları](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>Konsorsiyumun büyümesi

Konsorsiyumu genişletmek için önce fiziksel ağı bağlamanız gerekir. VPN 'nin arkasında dağıtım yapıyorsanız, [sanal ağ geçidini bağlama](#connecting-vnet-gateways) bölümüne, yeni üye dağıtımının bir parçası olarak ağ bağlantısını yapılandırma konusuna bakın. Dağıtımınız tamamlandıktan sonra, bir ağ yöneticisi olmak için [Idare DApp](#governance-dapp) 'yi kullanın.

### <a name="new-member-deployment"></a>Yeni üye dağıtımı

Aşağıdaki bilgileri katılım üyesiyle paylaşabilirsiniz. Bilgiler, dağıtım sonrası e-postanız veya Portal dağıtım çıkışında bulunur.

* Konsorsiyum verileri URL 'SI
* Dağıttığınız düğümlerin sayısı
* VNet ağ geçidi kaynak KIMLIĞI (VPN kullanılıyorsa)

Dağıtım üyesi, ağ iletişim bilgilerini aşağıdaki kılavuzdan dağıtmada aynı Ethereum yetki kanıtlama Konsorsiyumu çözüm şablonunu kullanmalıdır:

* **Varolanı katılmayı** seçin
* Dengeli gösterimi sağlamak için ağdaki diğer üyelerin geri kalanı ile aynı sayıda Doğrulayıcı düğümünü seçin
* Aynı yönetici Ethereum adresini kullan
* *Ethereum ayarlarında* belirtilen *konsorsiyum veri URL 'sini* kullanın
* Ağın geri kalanı VPN 'in arkasındaysa Gelişmiş bölüm altında **özel VNET** ' i seçin.

### <a name="connecting-vnet-gateways"></a>VNet ağ geçitlerini bağlama

Bu bölüm yalnızca özel bir sanal ağ kullanarak dağıttıysanız gereklidir. Genel IP adresleri kullanıyorsanız, bu bölümü atlayabilirsiniz.

Özel ağ için, farklı Üyeler VNet Gateway bağlantıları aracılığıyla bağlanır. Bir üyenin ağa katılabilmesi ve işlem trafiğini görmeniz için, mevcut bir üyenin VPN ağ geçidinde son bir yapılandırmayı bağlantıyı kabul etmesi gerekir. Birleştirme üyesinin Ethereum düğümleri bağlantı kurulana kadar çalışmaz. Tek bir hata noktası olasılığını azaltmak için, konsorsiyumda yedek ağ bağlantıları oluşturun.

Yeni üye dağıtıldıktan sonra, mevcut üye yeni üyeye VNet ağ geçidi bağlantısı ayarlayarak iki yönlü bağlantıyı tamamlamalıdır. Mevcut üye şunları gerektirir:

* Bağlanan üyenin VNet Gateway RESOURCEID. Bkz. [dağıtım çıktısı](#deployment-output).
* Paylaşılan bağlantı anahtarı.

Mevcut üyenin bağlantıyı tamamlaması için aşağıdaki PowerShell betiğini çalıştırması gerekir. Portalda sağ üst gezinti çubuğunda bulunan Azure Cloud Shell kullanabilirsiniz.

![Cloud Shell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

## <a name="governance-dapp"></a>İdare DApp

Yetki kanıtlama kalbi, yönetim açısından idare edilir. Yetki kanıtı, ağın sağlıklı kalmasını sağlamak için izin verilen bir ağ yetkilileri listesine bağlı olduğundan, bu izin listesinde değişiklik yapmak için bir yetkili mekanizma sağlanması önemlidir. Her dağıtım, izin verilen bu listenin zinciri için bir dizi akıllı sözleşme ve Portal ile birlikte sunulur. Önerilen bir değişiklik, konsorsiyum üyelerinden oluşan bir büyük oyuna ulaştığında, değişiklik yapılır. Oylama, yeni yarışma katılımcılarının eklenmesine veya tehlikeye giren bir ağı teşvik eden bir saydam şekilde kaldırılmasına olanak sağlar.

İdare DApp, önceden dağıtılan [akıllı sözleşmeleri](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) ve ağdaki yetkilileri yönetmek için kullanılan bir Web uygulamasını bir kümesidir. Yetkililer, yönetici kimliklerine ve doğrulayıcı düğümlerine bölünmüştür.
Yöneticiler, konsensus katılımını bir doğrulayıcı düğümleri kümesine devretmek için güce sahiptir. Yöneticiler ayrıca diğer yöneticileri ağ içine veya dışına da oylayabilir.

![İdare DApp](./media/ethereum-poa-deployment/governance-dapp.png)

* Merkezi olmayan **idare:** Ağ yetkililerinde yapılan değişiklikler, select yöneticileri tarafından zincir üzerinde oylama aracılığıyla yönetilir.
* **Doğrulayıcı temsili:** Yetkililer, her bir bir dağıtımda ayarlanan Doğrulayıcı düğümlerini yönetebilir.
* **Denetlenebilir değişiklik geçmişi:** Her değişiklik, saydam ve seslebilirlik sağlayan blok zincirinde kaydedilir.

### <a name="getting-started-with-governance"></a>İdare ile çalışmaya başlama

Idare DApp aracılığıyla herhangi bir türde işlem gerçekleştirmek için bir Ethereum cüzdan kullanmanız gerekir. En kolay yaklaşım, [MetaMask](https://metamask.io)gibi bir tarayıcı içi cüzdan kullanmaktır. Bununla birlikte, bu akıllı sözleşmeler ağ üzerinde dağıtıldığından, Idare sözleşmenize yönelik etkileşimlerinizi de otomatik hale getirebilirsiniz.

MetaMask yükledikten sonra, tarayıcıdaki Idare DApp 'ye gidin.  URL 'YI dağıtım çıkışında Azure portal aracılığıyla bulabilirsiniz.  Tarayıcı içi bir cüzdan yüklü değilse herhangi bir eylem gerçekleştiremezsiniz; Ancak, yönetici durumunu görüntüleyebilirsiniz.  

### <a name="becoming-an-admin"></a>Yönetici olma

Ağa dağıtılan ilk üyesiyse, otomatik olarak yönetici olur ve eşlik düğümleriniz doğrulayıcılar olarak listelenir. Ağa katılırsanız, büyük bir yönetici olarak (%50 ' dan fazla) erişmeniz gerekir var olan yönetici kümesi. Yönetici olmaya devam ederseniz, düğümleriniz blok zincirini eşitler ve doğrular; Ancak, blok oluşturma işlemine katılmaz. Bir yönetici olmak üzere oylama işlemini başlatmak için **aday** ' ı seçin ve Ethereum adresinizi ve diğer adınızı girin.

![Aday gösterin](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>Larınızdan

**Adaylar** sekmesinin seçilmesi, geçerli aday Yöneticiler kümesini gösterir.  Bir aday, geçerli yöneticiler tarafından büyük bir oyuna ulaştığında aday bir yöneticiye yükseltilir.  Bir aday üzerinde oy vermek için satırı seçin ve **oyın**' u seçin. Oyunuzu bir oyda değiştirirseniz, adayı seçin ve **rescind oynı** seçin.

![Larınızdan](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>Yöneticiler

**Yöneticiler** sekmesi geçerli yönetici kümesini gösterir ve size bir oy verme özelliği sunar.  Yönetici %50 ' den fazla destek kaybettikten sonra ağ üzerinde yönetici olarak kaldırılır. Yöneticinin sahip olduğu tüm Doğrulayıcı düğümleri, Doğrulayıcı durumunu kaybeder ve ağ üzerinde işlem düğümleri haline gelir. Herhangi bir sayıda nedenden dolayı yönetici kaldırılabilir. Ancak, bir ilkeyi önceden kabul etmek için konsorsiyumun bir üyesi vardır.

![Yöneticiler](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>Metninin

**Doğrulayıcılar** sekmesini seçtiğinizde, örnek için dağıtılan geçerli eşlik düğümleri ve bunların geçerli durumu (düğüm türü) görüntülenir. Bu görünüm geçerli dağıtılmış Consortium üyesini temsil ettiğinden, her bir konsorsiyumun bu listede farklı bir doğrulayıcılar kümesi vardır. Örnek yeni dağıtılırsa ve Doğrulayıcıları eklemediyseniz, **doğrulayıcılar ekleme** seçeneğini alırsınız. Doğrulayıcılar eklemek, bölgesel olarak dengelenmiş bir eşlik düğümleri kümesini otomatik olarak seçer ve bunları Doğrulayıcı kümesine atar. İzin verilen kapasiteden daha fazla düğüm dağıttıysanız, kalan düğümler ağ üzerinde işlem düğümleri haline gelir.

Her doğrulayıcının adresi, Azure 'daki [kimlik deposu](#identity-store) aracılığıyla otomatik olarak atanır.  Bir düğüm kapanıyorsa, kendi kimliğini yeniden oluşturur, böylece dağıtımınızdaki başka bir düğümün yerini alır. Bu işlem, konsensus katılımınızı yüksek oranda kullanılabilir olmasını sağlar.

![Metninin](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>Konsorsiyum adı

Herhangi bir yönetici, konsorsiyumun adını güncelleştirebilir.  Konsorsiyumun adını güncelleştirmek için sol üstteki dişli simgesini seçin.

### <a name="account-menu"></a>Hesap menüsü

Sağ üst köşede, Ethereum hesabınızın diğer adı ve identicon.  Yönetici değilseniz, diğer adınızı güncelleştirebilirsiniz.

![Hesap](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="support-and-feedback"></a>Destek ve geri bildirim<a id="tutorials"></a>

Azure blok zinciri haberleri için Azure blok zinciri [blogu](https://azure.microsoft.com/blog/topics/blockchain/) ' nı ziyaret ederek, blok zinciri hizmeti tekliflerini ve Azure blok zinciri Mühendisliği ekibinin bilgilerini güncel tutun.

Ürün geri bildirimi sağlamak veya yeni özellikler istemek için, [blok zinciri Için Azure geri bildirim Forumu](https://aka.ms/blockchainuservoice)aracılığıyla bir fikir gönderin veya oylayın.

### <a name="community-support"></a>Topluluk desteği

Microsoft mühendisleri ve Azure blok zinciri topluluk uzmanlarıyla birlikte katılın.

* [Microsoft Q&soru sayfası](/answers/topics/azure-blockchain-workbench.html). Blok zinciri şablonları için mühendislik desteği, dağıtım sorunlarıyla sınırlıdır.
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla Azure blok zinciri çözümü için bkz. [Azure blok zinciri belgeleri](../index.yml).
