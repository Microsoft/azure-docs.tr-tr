---
title: 'Sanal ağlar arasında bağlantı oluşturma: klasik: Azure portal'
description: PowerShell ve Azure portal kullanarak Azure sanal ağlarını birbirine bağlayın.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 0d81e0474d898ffee7f128c0bcea61f077c3d758
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103229"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>VNet 'ten VNet 'e bağlantı yapılandırma (klasik)

Bu makale, sanal ağlar arasında bir VPN Gateway bağlantısı oluşturmanıza yardımcı olur. Sanal ağlar aynı ya da farklı bölgelerde ve aynı ya da farklı aboneliklerde bulunuyor olabilirler.

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png" alt-text="Klasik VNet 'ten VNet 'e mimariyi gösteren diyagram":::

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Bu makaledeki adımlar, klasik dağıtım modeli ve Azure portal için geçerlidir. Ayrıca aşağıdaki listeden farklı bir seçenek belirtip farklı bir dağıtım aracı veya dağıtım modeli kullanarak da bu yapılandırmayı oluşturabilirsiniz:

> [!div class="op_single_selector"]
> * [Klasik](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Resource Manager](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [VNET 'leri farklı dağıtım modellerinde bağlama](vpn-gateway-connect-different-deployment-models-portal.md)
>
>

## <a name="about-vnet-to-vnet-connections"></a>Sanal Ağdan Sanal Ağa bağlantıları hakkında

VPN ağ geçidi kullanarak, bir sanal ağı klasik dağıtım modelinde başka bir sanal ağa (VNet-VNet) bağlamak, sanal bir ağı şirket içi site konumuna bağlamaya benzer. Her iki bağlantı türü de IPsec/IKE kullanarak güvenli bir tünel sunmak üzere bir VPN ağ geçidi kullanır.

Bağlandığınız sanal ağlar farklı aboneliklerde ve farklı bölgelerde olabilir. VNet 'ten VNet 'e iletişimi çok siteli yapılandırmalara sahip bir şekilde birleştirebilirsiniz. Bu özellik şirket içi ve şirket dışı bağlantıyla ağ içi bağlantıyı birleştiren ağ topolojileri kurabilmenize olanak sağlar.

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png" alt-text="Bağlantıları gösteren diyagram":::

### <a name="why-connect-virtual-networks"></a><a name="why"></a>Sanal ağları neden bağlamalıyız?

Sanal ağları aşağıdaki sebeplerden dolayı bağlamak isteyebilirsiniz:

* **Çapraz bölge coğrafi artıklığı ve coğrafi-durum**

  * Kendi coğrafi çoğaltma veya eşitlemenizi, güvenli bağlantıyla İnternet’te uç noktalara gitmeden ayarlayabilirsiniz.
  * Azure Load Balancer ve Microsoft ya da üçüncü taraf kümeleme teknolojisi sayesinde, birden fazla Azure bölgesinde coğrafi yedeklilik ile yüksek oranda kullanılabilir iş yükü ayarlayabilirsiniz. Buna önemli bir örnek olarak SQL Always On ile birden fazla Azure bölgesine yayılan Kullanılabilirlik Grupları’nı birlikte kurmak verilebilir.
* **Güçlü yalıtım sınırı olan bölgesel çok katmanlı uygulamalar**

  * Aynı bölge içinde çok katmanlı uygulamaları, güçlü yalıtımlı ve güvenli katmanlı iletişimlerle birbirine bağlı birden fazla VNET ile birlikte ayarlayabilirsiniz.
* **Azure 'da çapraz abonelik, şirket içi iletişim**

  * Birden çok Azure aboneliğiniz varsa, sanal ağlar arasında güvenli bir şekilde farklı aboneliklerden iş yüklerini birbirine bağlayabilirsiniz.
  * Kuruluşlar veya hizmet sağlayıcıları için Azure 'da güvenli VPN teknolojisiyle platformlar arası iletişimi etkinleştirebilirsiniz.

Sanal ağlar arası bağlantılar hakkında daha fazla bilgi için bu makalenin sonunda yer alan [Sanal ağdan sanal ağa dikkat edilecek noktalar](#faq) bölümünü inceleyin.

## <a name="prerequisites"></a>Önkoşullar

Çoğu adım için portalı kullanıyoruz, ancak sanal ağlar arasındaki bağlantıları oluşturmak için PowerShell kullanmanız gerekir. Portalda paylaşılan anahtarı belirtmenin bir yolu olmadığından Azure portal kullanarak bağlantı oluşturamazsınız. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="planning"></a><a name="planning"></a>Planlama

Sanal ağlarınızı yapılandırmak için kullanacağınız aralıklara karar vermeniz önemlidir. Bu yapılandırma için, VNet aralıklarından hiçbirinin birbirleriyle veya bağlandıkları yerel ağlarla çakışdıklarından emin olmanız gerekir.

### <a name="vnets"></a><a name="vnet"></a>Sanal ağlar

Bu alıştırma için aşağıdaki örnek değerleri kullanırız:

**TestVNet1 için değerler**

Ad: TestVNet1<br>
Adres alanı: 10.11.0.0/16, 10.12.0.0/16 (isteğe bağlı)<br>
Alt ağ adı: varsayılan<br>
Alt ağ adres aralığı: 10.11.0.0/24<br>
Kaynak grubu: ClassicRG<br>
Location: East US<br>
GatewaySubnet: 10.11.1.0/27

**TestVNet4 için değerler**

Ad: TestVNet4<br>
Adres alanı: 10.41.0.0/16, 10.42.0.0/16 (isteğe bağlı)<br>
Alt ağ adı: varsayılan<br>
Alt ağ adres aralığı: 10.41.0.0/24<br>
Kaynak grubu: ClassicRG<br>
Location: West US<br>
GatewaySubnet: 10.41.1.0/27

### <a name="connections"></a><a name="plan"></a>Bağlantılar

Aşağıdaki tabloda, sanal ağlarınızı nasıl bağlayacaksınız hakkında bir örnek gösterilmektedir. Aralıkları yalnızca bir kılavuz olarak kullanın. Sanal ağlarınız için aralıkları yazın. Sonraki adımlar için bu bilgilere ihtiyacınız vardır.

Bu örnekte, TestVNet1 ' VNet4Local ' adında oluşturduğunuz bir yerel ağ sitesine bağlanır. VNet4Local ayarları, TestVNet4 için adres ön eklerini içerir.
Her VNet için yerel site diğer VNet 'dir. Yapılandırma için aşağıdaki örnek değerler kullanılır:

**Örnek**

| Sanal Ağ | Adres Alanı | Konum | Yerel ağ sitesine bağlanır |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Doğu ABD |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Batı ABD |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="create-virtual-networks"></a><a name="vnetvalues"></a>Sanal ağlar oluşturma

Bu adımda, TestVNet1 ve TestVNet4 olmak üzere iki klasik sanal ağ oluşturacaksınız. Bu makaleyi bir alıştırma olarak kullanıyorsanız, [örnek değerleri](#vnet)kullanın.

**Sanal ağlarınızı oluştururken aşağıdaki ayarları aklınızda bulundurun:**

* **Sanal ağ adres alanları** – sanal ağ adres alanları sayfasında, sanal ağınız için kullanmak istediğiniz adres aralığını belirtin. Bunlar, bu sanal ağa dağıttığınız VM 'lere ve diğer rol örneklerine atanacak dinamik IP adresleridir.<br>Seçtiğiniz adres alanları, bu VNet 'in bağlanacağı diğer sanal ağlar veya şirket içi konumların herhangi biri için adres alanları ile çakışamaz.

* **Konum** – bir sanal ağ oluşturduğunuzda, bunu bir Azure konumuyla (bölge) ilişkilendirirsiniz. Örneğin, sanal ağınıza dağıtılan VM 'lerinin fiziksel olarak Batı ABD ' de bulunmasını istiyorsanız, bu konumu seçin. Oluşturduktan sonra sanal ağınızla ilişkili konumu değiştiremezsiniz.

**Sanal ağlarınızı oluşturduktan sonra aşağıdaki ayarları ekleyebilirsiniz:**

* **Adres alanı** – bu yapılandırma için ek adres alanı gerekli değildir, ancak sanal ağı oluşturduktan sonra ek adres alanı ekleyebilirsiniz.

* **Alt ağlar** – bu yapılandırma için ek alt ağlar gerekli değildir, ancak VM 'lerinizi diğer rol örneklerinizden ayrı bir alt ağda kullanmak isteyebilirsiniz.

* **DNS sunucuları** : DNS sunucusu adını ve IP adresini girin. Bu ayarla bir DNS sunucusu oluşturulmaz. Söz konusu ayar, bu sanal ağa ilişkin ad çözümlemesi için kullanmak istediğiniz DNS sunucularını belirtmenize olanak sağlar.

### <a name="to-create-a-classic-virtual-network"></a>Klasik bir sanal ağ oluşturmak için

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="configure-sites-and-gateways"></a><a name="localsite"></a>Siteleri ve ağ geçitlerini yapılandırma

Azure, sanal ağlar arasında trafiğin nasıl yönlendirileceğini anlamak için her yerel ağ sitesinde belirtilen ayarları kullanır. Her VNet, trafiği yönlendirmek istediğiniz ilgili yerel ağı işaret etmelidir. Her yerel ağ sitesine başvurmak için kullanmak istediğiniz adı belirlersiniz. Açıklayıcı bir açıklama kullanmak en iyisidir.

Örneğin, TestVNet1 ' VNet4Local ' adında oluşturduğunuz bir yerel ağ sitesine bağlanır. VNet4Local ayarları, TestVNet4 için adres ön eklerini içerir.

Her VNet için yerel site diğer VNet 'in bulunduğu göz önünde bulundurun.

| Sanal Ağ | Adres Alanı | Konum | Yerel ağ sitesine bağlanır |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Doğu ABD |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Batı ABD |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

### <a name="to-configure-a-site"></a><a name="site"></a>Bir siteyi yapılandırmak için

Yerel site genellikle şirket içi konumunuzu ifade eder. Bağlantı oluşturacağınız VPN cihazının IP adresini ve VPN ağ geçidi üzerinden VPN cihazına yönlendirilecek IP aralıklarını içerir.

1. VNet 'iniz sayfasında, **Ayarlar**' ın altında, **siteden siteye bağlantılar**' ı seçin.
1. Siteden siteye bağlantılar sayfasında **+ Ekle**' yi seçin.
1. **VPN bağlantısı ve ağ geçidi yapılandırma** sayfasında, **bağlantı türü** için **siteden siteye** seçili bırakın.

   * **VPN ağ geçidi IP adresi:** Şirket içi ağınızdaki VPN cihazının genel IP adresidir. Bu alıştırmada, başka bir site için VPN ağ geçidinin IP adresine sahip olmadığınızdan bir kukla adres koyabilirsiniz. Örneğin, 5.4.3.2. Daha sonra, diğer VNet için ağ geçidini yapılandırdıktan sonra bu değeri ayarlayabilirsiniz.

   * **Istemci adres alanı:** Bu ağ geçidi aracılığıyla diğer VNet 'e yönlendirilmesini istediğiniz IP adresi aralıklarını listeleyin. Birden fazla adres alanı aralığı ekleyebilirsiniz. Burada belirttiğiniz aralıkların, sanal ağınızın bağlandığı diğer ağlarla ve sanal ağın kendi adres aralıklarıyla çakışmadığından emin olun.
1. Sayfanın en altında, gözden geçir + oluştur ' u seçmeyin. Bunun yerine, Ileri ' yi seçin **: ağ geçidi>**.

### <a name="to-configure-a-virtual-network-gateway"></a><a name="sku"></a>Bir sanal ağ geçidini yapılandırmak için

1. **Ağ geçidi** sayfasında, aşağıdaki değerleri seçin:

   * **Boyut:** Bu, sanal ağ geçidinizi oluşturmak için kullandığınız ağ geçidi SKU 'sudur. Klasik VPN ağ geçitleri eski ağ geçidi SKU'larını kullanır. Eski ağ geçidi SKU'ları hakkında daha fazla bilgi için bkz. [Sanal ağ geçidi SKU'ları (eski SKU’lar) ile çalışma](vpn-gateway-about-skus-legacy.md). Bu alıştırma için **Standart** seçeneğini belirleyebilirsiniz.

   * **Yönlendirme türü:** Ağ geçidinizin yönlendirme türünü seçin. Bu seçenek VPN türü olarak da bilinir. Ağ geçidini bir türden diğerine dönüştüremediği için doğru türü seçmeniz önemlidir. VPN cihazınız seçtiğiniz yönlendirme türü ile uyumlu olmalıdır. Yönlendirme türü hakkında daha fazla bilgi için bkz. [VPN Gateway ayarları hakkında](vpn-gateway-about-vpn-gateway-settings.md#vpntype). 'RouteBased' ve 'PolicyBased' VPN türlerine başvuran makaleler görebilirsiniz. 'Dinamik' seçeneği 'RouteBased', 'Statik' seçeneği ise 'PolicyBased' değerine karşılık gelir. Bu yapılandırma için **dinamik**' i seçin.

   * **Ağ geçidi alt ağı:** Belirttiğiniz ağ geçidi alt ağının boyutu, oluşturmak istediğiniz VPN ağ geçidi yapılandırmasına bağlıdır. /29 kadar küçük bir ağ geçidi alt ağı oluşturmak mümkün olsa da /27 veya /28 değerini kullanmanız önerilir. Bu değer, daha fazla adres içeren daha büyük bir alt ağ oluşturur. Daha büyük bir ağ geçidi alt ağı kullanmak, olası gelecek yapılandırmaları barındırmak için yeterli IP adresi bulunmasını sağlar.

1. Ayarlarınızı doğrulamak için sayfanın altındaki **gözden geçir + oluştur** ' u seçin. Dağıtılacak **Oluştur** ' u seçin. Seçtiğiniz ağ geçidi SKU 'suna bağlı olarak, sanal ağ geçidinin oluşturulması 45 dakika sürebilir.
1. Bu ağ geçidi oluştururken sonraki adıma devam edebilirsiniz.

### <a name="configure-testvnet4-settings"></a>TestVNet4 ayarlarını yapılandırma

TestVNet4 yapılandırmak için [bir site ve ağ geçidi oluşturma](#localsite) adımlarını yineleyin ve gerekirse değerleri değiştirin. Bunu bir alıştırma olarak yapıyorsanız [örnek değerleri](#planning)kullanın.

## <a name="update-local-sites"></a><a name="updatelocal"></a>Yerel siteleri Güncelleştir

Sanal ağ geçitleriniz her iki VNET için de oluşturulduktan sonra, **VPN Gateway IP adresi** için yerel site özelliklerini ayarlamanız gerekir.

|VNet adı|Bağlı site|Ağ geçidi IP adresi|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|TestVNet4 için VPN Gateway IP adresi|
|TestVNet4|VNet1Local|TestVNet1 için VPN Gateway IP adresi|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>1. Bölüm-sanal ağ geçidi genel IP adresini al

1. **Kaynak grubuna** gidip sanal ağı seçerek VNET 'e gidin.
1. Sanal ağınızın sayfasında, sağdaki **temel** bileşenler bölmesinde, **ağ geçidi IP adresini** bulun ve panoya kopyalayın.

### <a name="part-2---modify-the-local-site-properties"></a>Bölüm 2-yerel site özelliklerini değiştirme

1. Siteden siteye bağlantılar bölümünde bağlantıyı seçin. Örneğin, SiteVNet4.
1. Siteden siteye bağlantı için **Özellikler** sayfasında, **yerel siteyi Düzenle**' yi seçin.
1. **VPN ağ GEÇIDI IP adresi** alanında, önceki bölümde kopyaladığınız VPN Gateway IP adresini yapıştırın.
1. **Tamam**’ı seçin.
1. Alan, sistemde güncelleştirilir. Bu yöntemi, bu siteye yönlendirmek istediğiniz ek IP adresini eklemek için de kullanabilirsiniz.

### <a name="part-3---repeat-steps-for-the-other-vnet"></a>3. kısım-diğer VNet için adımları yineleyin

TestVNet4 için adımları yineleyin.

## <a name="retrieve-configuration-values"></a><a name="getvalues"></a>Yapılandırma değerlerini al

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-connections"></a><a name="createconnections"></a>Bağlantı oluşturma

Tüm önceki adımlar tamamlandığında, IPSec/ıKE önceden paylaşılan anahtarlarını ayarlayabilir ve bağlantıyı oluşturabilirsiniz. Bu adım kümesi PowerShell kullanır. Paylaşılan anahtar portalda belirtilemediğinden, klasik dağıtım modeline yönelik VNet-VNet bağlantıları Azure portal yapılandırılamaz.

Örneklerde, paylaşılan anahtarın tam olarak aynı olduğuna dikkat edin. Paylaşılan anahtarın her zaman eşleşmesi gerekir. Bu örneklerdeki değerleri sanal ağlar ve yerel ağ siteleriniz için tam adlarla değiştirdiğinizden emin olun.

1. TestVNet1 - TestVNet4 bağlantısını oluşturun. Değerleri değiştirdiğinizden emin olun.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
   -LocalNetworkSiteName 'value for _VNet4Local' -SharedKey A1b2C3D4
   ```
2. TestVNet4 - TestVNet1 bağlantısı oluşturun.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
   -LocalNetworkSiteName 'value for _VNet1Local' -SharedKey A1b2C3D4
   ```
3. Bağlantıların başlatılmasını bekleyin. Ağ Geçidi başlatıldıktan sonra durum ' başarılı ' olur.

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="faq-and-considerations"></a><a name="faq"></a>SSS ve önemli noktalar

Bu noktalar, klasik sanal ağlar ve klasik sanal ağ geçitleri için geçerlidir.

* Sanal ağlar aynı veya farklı aboneliklerde olabilir.
* Sanal ağlar aynı ya da farklı Azure bölgelerinde (konumlarında) bulunabilir.
* Bir bulut hizmeti veya yük dengeleme uç noktası, birbirine bağlı olsalar dahi sanal ağlar arasında yayılamaz.
* Birden çok sanal ağı birbirine bağlamak için herhangi bir VPN cihazı gerekmez.
* VNet 'ten VNet 'e Azure sanal ağlarını bağlamayı destekler. Bir sanal ağa dağıtılan sanal makinelerin veya bulut hizmetlerinin bağlanmasını desteklemez.
* VNet 'ten VNet 'e dinamik yönlendirme ağ geçitleri gerekir. Azure statik yönlendirme ağ geçitleri desteklenmez.
* Sanal ağ bağlantısı, çoklu site VPN’leri ile eşzamanlı olarak kullanılabilir. Diğer sanal ağlara veya şirket içi sitelere bağlanan bir sanal ağ VPN ağ geçidi için en fazla 10 VPN tüneli vardır.
* Sanal ağlar ve şirket içi yerel ağ sitelerinin adres alanları çakışmamalıdır. Çakışan adres alanları, sanal ağların oluşturulmasına veya netcfg yapılandırma dosyalarının başarısız olmasına neden olur.
* Bir çift sanal ağ arasındaki yedekli tüneller desteklenmez.
* VNet için P2S VPN 'Ler de dahil olmak üzere tüm VPN tünelleri, VPN Gateway için kullanılabilir bant genişliğini ve Azure 'da aynı VPN ağ geçidi çalışma süresi SLA 'sını paylaşır.
* VNet-VNet trafiği Azure omurgası üzerinden dolaşır.

## <a name="next-steps"></a>Sonraki adımlar

Bağlantılarınızı doğrulayın. Bkz. [VPN Gateway bağlantısını doğrulama](vpn-gateway-verify-connection-resource-manager.md).
