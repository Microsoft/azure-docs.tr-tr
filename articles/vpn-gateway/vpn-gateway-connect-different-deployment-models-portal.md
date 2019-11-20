---
title: "Klasik sanal ağları Azure Resource Manager VNET 'lere bağlama: Portal | Microsoft Docs"
description: VPN Gateway ve portalı kullanarak klasik VNET 'leri Kaynak Yöneticisi sanal ağlara bağlama adımları
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: cherylmc
ms.openlocfilehash: 722907328fe17c4116f4f8d948e081f9582ca712
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266570"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Portalı kullanarak farklı dağıtım modellerindeki sanal ağları bağlama

Bu makalede, farklı dağıtım modellerinde bulunan kaynakların birbirleriyle iletişim kurmasına izin vermek için klasik sanal ağları Kaynak Yöneticisi sanal ağlara nasıl bağlayabilmeniz gösterilmektedir. Bu makaledeki adımlar öncelikle Azure portal kullanır, ancak bu yapılandırmayı, bu listeden makaleyi seçerek PowerShell kullanarak da oluşturabilirsiniz.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Klasik sanal ağı bir Kaynak Yöneticisi VNet 'e bağlamak, VNet 'i şirket içi site konumuna bağlamaya benzer. Her iki bağlantı türü de IPsec/IKE kullanarak güvenli bir tünel sunmak üzere bir VPN ağ geçidi kullanır. Farklı aboneliklerde ve farklı bölgelerde bulunan sanal ağlar arasında bir bağlantı oluşturabilirsiniz. Ayrıca, yapılandırılmış oldukları ağ geçidi dinamik veya rota tabanlı olduğu sürece, zaten şirket içi ağlarda bağlantıları olan VNET 'leri de bağlayabilirsiniz. Sanal ağlar arası bağlantılar hakkında daha fazla bilgi için bu makalenin sonunda yer alan [Sanal ağlar arası bağlantılar hakkında SSS](#faq) bölümünü inceleyin. 

Zaten bir sanal ağ geçidinizin yoksa ve bir tane oluşturmak istemiyorsanız VNet eşleme kullanarak VNet 'iniz bağlamayı göz önünde bulundurmanız gerekebilir. VNet eşlemesi VPN ağ geçidini kullanmaz. Daha fazla bilgi için bkz. [VNet eşlemesi](../virtual-network/virtual-network-peering-overview.md).

### <a name="before"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Bu adımlarda her iki VNET de zaten oluşturulmuş olduğu varsayılır. Bu makaleyi bir alıştırma olarak kullanıyorsanız ve sanal ağlar yoksa, bunları oluşturmanıza yardımcı olacak adımlarda bağlantılar vardır.
* Sanal ağlar için adres aralıklarının birbirleriyle çakışmayacak veya ağ geçitlerinin bağlı olabileceği diğer bağlantılar için aralıklardan hiçbiriyle çakışmadığından emin olun.
* Hem Kaynak Yöneticisi hem de hizmet yönetimi (klasik) için en son PowerShell cmdlet 'lerini yükler. Bu makalede hem Azure portal hem de PowerShell kullanırız. Klasik VNet 'ten Kaynak Yöneticisi VNet 'e bağlantı oluşturmak için PowerShell gereklidir. Daha fazla bilgi için bkz. [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/overview). 

### <a name="values"></a>Örnek ayarlar

Bu değerleri kullanarak bir test ortamı oluşturabilir veya bu makaledeki örnekleri daha iyi anlamak için bunlara bakabilirsiniz.

**Klasik VNet**

VNet adı = ClassicVNet <br>
Adres alanı = 10.0.0.0/24 <br>
Alt ağ adı = alt ağ-1 <br>
Alt ağ adres aralığı = 10.0.0.0/27 <br>
Abonelik = kullanmak istediğiniz abonelik <br>
Kaynak grubu = ClassicRG <br>
Konum = Batı ABD <br>
GatewaySubnet = 10.0.0.32/28 <br>
Yerel site = RMVNetLocal <br>

**Kaynak Yöneticisi VNet**

VNet adı = RMVNet <br>
Adres alanı = 192.168.0.0/16 <br>
Kaynak grubu = RG1 <br>
Konum = Doğu ABD <br>
Alt ağ adı = alt ağ-1 <br>
Adres aralığı = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Sanal ağ geçidi adı = RMGateway <br>
Ağ Geçidi türü = VPN <br>
VPN türü = rota tabanlı <br>
SKU = VpnGw1 <br>
Konum = Doğu ABD <br>
Sanal ağ = RMVNet <br> (VPN ağ geçidini bu VNet ile ilişkilendir) İlk IP yapılandırması = rmgwpıp <br> (ağ geçidi genel IP adresi) Yerel ağ geçidi = ClassicVNetLocal <br>
Bağlantı adı = RMtoClassic

### <a name="connectoverview"></a>Bağlantıya genel bakış

Bu yapılandırma için, sanal ağlar arasında bir IPSec/ıKE VPN tüneli üzerinden bir VPN Ağ Geçidi bağlantısı oluşturursunuz. VNet aralıklarından hiçbirinin birbirleriyle veya bağlandıkları yerel ağlarla çakışdıklarından emin olun.

Aşağıdaki tabloda, örnek VNET 'lerin ve yerel sitelerin nasıl tanımlandığı hakkında bir örnek gösterilmektedir:

| Sanal Ağ | Adres Alanı | Bölge | Yerel ağ sitesine bağlanır |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |Batı ABD | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |East US |ClassicVNetLocal (10.0.0.0/24) |

## <a name="classicvnet"></a>Bölüm 1-klasik VNet ayarlarını yapılandırma

Bu bölümde, klasik VNet, yerel ağ (yerel site) ve sanal ağ geçidi oluşturursunuz. Ekran görüntüleri örnek olarak verilmiştir. Değerleri kendi değerlerinizle değiştirdiğinizden emin olun veya [örnek](#values) değerleri kullanın.

### 1. <a name="classicvnet"></a>Klasik VNet oluşturma

Klasik bir VNet 'iniz yoksa ve bu adımları bir alıştırma olarak çalıştırıyorsanız, [Bu makaleyi](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) ve yukarıdaki [örnek](#values) ayarlar değerlerini kullanarak bir sanal ağ oluşturabilirsiniz.

VPN ağ geçidi olan bir VNet 'iniz zaten varsa, ağ geçidinin dinamik olduğunu doğrulayın. Statik ise, [yerel siteyi yapılandırmaya](#local)devam etmeden önce VPN ağ geçidini silmeniz gerekir.

1. [Azure Portal](https://ms.portal.azure.com)'ı açın ve Azure hesabınızla oturum açın.
2. **+ Kaynak oluştur ' a** tıklayarak ' yeni ' sayfasını açın.
3. ' Market 'te ara ' alanına ' sanal ağ ' yazın. Bunun yerine ağ-> sanal ağı ' nı seçerseniz, klasik VNet oluşturma seçeneğini kullanamazsınız.
4. Döndürülen listeden ' sanal ağ ' öğesini bulun ve sanal ağ sayfasını açmak için tıklayın. 
5. Sanal ağ sayfasında, klasik VNet oluşturmak için ' klasik ' seçeneğini belirleyin. Burada varsayılan değer alırsanız bunun yerine bir Kaynak Yöneticisi VNet ile birlikte bir sanal ağa sahip olursunuz.

### 2. <a name="local"></a>Yerel siteyi yapılandırma

1. **Tüm kaynaklara** gidin ve listedeki **classicvnet** 'i bulun.
2. **Genel bakış** sayfasının **VPN bağlantıları** **bölümünde ağ geçidi** ' ne tıklayarak Ağ Geçidi oluşturun.
  ![VPN ağ geçidi yapılandırma](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "VPN ağ geçidi yapılandırma")
3. **Yenı VPN bağlantısı** sayfasında, **bağlantı türü**için **siteden siteye**' yı seçin.
4. **Yerel site**için **gerekli ayarları Yapılandır**' a tıklayın. Bu, **yerel site** sayfasını açar.
5. **Yerel site** sayfasında, Kaynak Yöneticisi VNET 'e başvuracak bir ad oluşturun. Örneğin, ' RMVNetLocal '.
6. Kaynak Yöneticisi VNet için VPN ağ geçidinin zaten ortak bir IP adresi varsa **VPN ağ GEÇIDI IP adresi** alanının değerini kullanın. Bu adımları bir alıştırma olarak yapıyorsanız veya Kaynak Yöneticisi VNet 'iniz için henüz bir sanal ağ geçidi yoksa, bir yer tutucu IP adresi oluşturabilirsiniz. Yer tutucu IP adresinin geçerli bir biçim kullandığından emin olun. Daha sonra, yer tutucu IP adresini Kaynak Yöneticisi sanal ağ geçidinin genel IP adresiyle değiştirirsiniz.
7. **Istemci adres alanı**Için kaynak yöneticisi VNET için sanal ağ IP adresi alanlarının [değerlerini](#connectoverview) kullanın. Bu ayar Kaynak Yöneticisi sanal ağına yönlendirmek üzere adres alanlarını belirtmek için kullanılır. Örnekte, RMVNet için adres aralığı olan 192.168.0.0/16 kullanırız.
8. Değerleri kaydetmek ve **yenı VPN bağlantısı** sayfasına dönmek için **Tamam** ' ı tıklatın.

### <a name="classicgw"></a>3. Sanal ağ geçidini oluşturma

1. **Yenı VPN bağlantısı** sayfasında, **ağ geçidini hemen oluştur** onay kutusunu seçin.
2. **İsteğe bağlı ağ geçidi yapılandırması**’na tıklayarak **Ağ geçidi yapılandırması** sayfasını açın.

   ![Ağ geçidi yapılandırma sayfasını aç](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "Ağ geçidi yapılandırma sayfasını aç")
3. Alt ağ **-gerekli ayarları Yapılandır** ' a tıklayarak **alt ağ ekle** sayfasını açın. **Ad** , gerekli değerle zaten yapılandırılmış: **Gatewaysubnet**.
4. **Adres aralığı** , ağ geçidi alt ağının aralığını ifade eder. /29 adres aralığı (3 Adres) ile bir ağ geçidi alt ağı oluşturabilseniz de, daha fazla IP adresi içeren bir ağ geçidi alt ağı oluşturmanız önerilir. Bu, daha fazla kullanılabilir IP adresi gerektirebilecek gelecek yapılandırmalara uyum sağlayacak. Mümkünse/27 veya/28 kullanın. Bu adımları bir alıştırma olarak kullanıyorsanız, [örnek değerlere](#values)başvurabilirsiniz. Bu örnekte, ' 10.0.0.32/28 ' kullanılır. Ağ geçidi alt ağını oluşturmak için **Tamam** ' ı tıklatın.
5. **Ağ geçidi yapılandırma** sayfasında, **Boyut** ağ geçidi SKU 'suna başvurur. VPN ağ geçidiniz için ağ geçidi SKU 'sunu seçin.
6. **Yönlendirme türünün** **dinamik**olduğunu doğrulayın ve ardından **Tamam** ' a tıklayarak **yeni VPN bağlantısı** sayfasına dönün.
7. **Yenı VPN bağlantısı** SAYFASıNDA, VPN ağ geçidinizi oluşturmaya başlamak için **Tamam** ' ı tıklatın. Bir VPN ağ geçidinin oluşturulması 45 dakika sürebilir.

### <a name="ip"></a>4. Sanal ağ geçidi genel IP adresini kopyala

Sanal ağ geçidi oluşturulduktan sonra, ağ geçidi IP adresini görüntüleyebilirsiniz. 

1. Klasik sanal ağınıza gidin ve **Genel Bakış ' a**tıklayın.
2. VPN bağlantıları sayfasını açmak için **VPN bağlantıları** ' na tıklayın. VPN bağlantıları sayfasında, genel IP adresini görüntüleyebilirsiniz. Bu, sanal ağ geçidinize atanan genel IP adresidir. IP adresini bir yere getirin. Kaynak Yöneticisi yerel ağ geçidi yapılandırma ayarlarınızla çalışırken sonraki adımlarda kullanırsınız. 
3. Ağ Geçidi bağlantılarınızın durumunu görüntüleyebilirsiniz. Oluşturduğunuz yerel ağ sitesi ' bağlanıyor ' olarak listelendiğine dikkat edin. Bağlantınız oluşturulduktan sonra durum değişir. Durumu görüntülemeyi tamamladığınızda, bu sayfayı kapatabilirsiniz.

## <a name="rmvnet"></a>Bölüm 2-Kaynak Yöneticisi VNet ayarlarını yapılandırma

Bu bölümde, Kaynak Yöneticisi VNet 'iniz için sanal ağ geçidi ve yerel ağ geçidi oluşturursunuz. Ekran görüntüleri örnek olarak verilmiştir. Değerleri kendi değerlerinizle değiştirdiğinizden emin olun veya [örnek](#values) değerleri kullanın.

### <a name="1-create-a-virtual-network"></a>1. Sanal ağ oluşturma

**Örnek değerler:**

* VNet adı = RMVNet <br>
* Adres alanı = 192.168.0.0/16 <br>
* Kaynak grubu = RG1 <br>
* Konum = Doğu ABD <br>
* Alt ağ adı = alt ağ-1 <br>
* Adres aralığı = 192.168.1.0/24 <br>

Kaynak Yöneticisi VNet 'iniz yoksa ve bu adımları bir alıştırma olarak çalıştırıyorsanız, örnek değerleri kullanarak [sanal ağ oluşturma](../virtual-network/quick-create-portal.md)bölümündeki adımlarla bir sanal ağ oluşturun.

### <a name="creategw"></a>2. Sanal ağ geçidi oluşturma

Bu adımda sanal ağınız için sanal ağ geçidi oluşturacaksınız. Bir ağ geçidinin oluşturulması, seçili ağ geçidi SKU’suna bağlı olarak 45 dakika veya daha uzun sürebilir.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

**Örnek değerler:**

* Sanal ağ geçidi adı = RMGateway <br>
* Ağ Geçidi türü = VPN <br>
* VPN türü = rota tabanlı <br>
* SKU = VpnGw1 <br>
* Konum = Doğu ABD <br>
* Sanal ağ = RMVNet <br>
* GatewaySubnet = 192.168.0.0/26 <br>
* İlk IP yapılandırması = rmgwpıp <br>

[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="createlng"></a>3. Yerel ağ geçidi oluşturma

**Örnek değerler:** Yerel ağ geçidi = ClassicVNetLocal

| Sanal Ağ | Adres Alanı | Bölge | Yerel ağ sitesine bağlanır |Ağ Geçidi genel IP adresi|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |Batı ABD | RMVNetLocal (192.168.0.0/16) |Classıvnet ağ geçidine atanan genel IP adresi|
| RMVNet | (192.168.0.0/16) |East US |ClassicVNetLocal (10.0.0.0/24) |RMVNet ağ geçidine atanan genel IP adresi.|

Yerel ağ geçidi, klasik VNet ve sanal ağ geçidi ile ilişkili adres aralığını ve genel IP adresini belirtir. Bu adımları bir alıştırma olarak yapıyorsanız örnek değerlerine bakın.

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="modifylng"></a>Bölüm 3-klasik VNet yerel site ayarlarını değiştirme

Bu bölümde, yerel site ayarlarını belirtirken kullandığınız yer tutucu IP adresini Kaynak Yöneticisi VPN ağ geçidi IP adresiyle değiştirirsiniz. Bu bölüm, klasik (SM) PowerShell cmdlet 'lerini kullanır.

1. Azure portal, klasik sanal ağa gidin.
2. Sanal ağınızın sayfasında **Genel Bakış ' a**tıklayın.
3. **VPN bağlantıları** bölümünde, grafikte yerel sitenizin adına tıklayın.

   ![VPN-bağlantılar](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "VPN bağlantıları")
4. **Siteden sıteye VPN bağlantıları** sayfasında, sitenin adına tıklayın.

   ![Site adı](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "Yerel site adı")
5. Yerel sitenizin bağlantı sayfasında **, yerel site sayfasını açmak** için yerel sitenin adına tıklayın.

   ![Açık yerel site](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "Yerel siteyi aç")
6. **Yerel site** sayfasında **VPN gateway IP adresini** Kaynak Yöneticisi ağ geçidinin IP adresi ile değiştirin.

   ![Ağ geçidi-IP adresi](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "Ağ GEÇIDI IP adresi")
7. IP adresini güncelleştirmek için **Tamam** ' ı tıklatın.

## <a name="RMtoclassic"></a>Bölüm 4-Klasik bağlantı Kaynak Yöneticisi oluşturma

Bu adımlarda, Azure portal kullanarak Kaynak Yöneticisi VNet 'ten klasik VNet 'e bağlantıyı yapılandırırsınız.

1. **Tüm kaynaklar**' da, yerel ağ geçidini bulun. Örneğimizde, yerel ağ geçidi **Classicvnetlocal**' dir.
2. **Yapılandırma** ' ya tıklayın ve IP adresi değerinin klasik VNET için VPN ağ geçidi olduğunu doğrulayın. Gerekirse güncelleştirin ve ardından **Kaydet**' e tıklayın. Sayfayı kapatın.
3. **Tüm kaynaklar**' da yerel ağ geçidi ' ne tıklayın.
4. Bağlantılar sayfasını açmak için **Bağlantılar** ' a tıklayın.
5. **Bağlantılar** sayfasında bağlantı eklemek için tıklayın **+** .
6. **Bağlantı ekle** sayfasında bağlantıyı adlandırın. Örneğin, ' RMtoClassic '.
7. **Siteden siteye** bu sayfada zaten seçili.
8. Bu siteyle ilişkilendirmek istediğiniz sanal ağ geçidini seçin.
9. **Paylaşılan anahtar**oluşturun. Bu anahtar, klasik VNet 'ten Kaynak Yöneticisi VNet 'e oluşturduğunuz bağlantıda de kullanılır. Anahtarı oluşturabilir veya bir tane oluşturabilirsiniz. Bizim örneğimizde, ' abc123 ' kullanıyoruz, ancak daha karmaşık bir şey kullanmanız (ve olması gerekir).
10. Bağlantıyı oluşturmak için **Tamam** ' ı tıklatın.

## <a name="classictoRM"></a>5. bölüm-Kaynak Yöneticisi için klasik bir bağlantı oluşturun

Bu adımlarda, klasik VNet 'ten Kaynak Yöneticisi VNet 'e bağlantıyı yapılandırırsınız. Bu adımlar PowerShell gerektirir. Portalda bu bağlantıyı oluşturamazsınız. Hem klasik (SM) hem de Kaynak Yöneticisi (RM) PowerShell cmdlet 'lerini indirdiğinizden ve yüklediğinizden emin olun.

### <a name="1-connect-to-your-azure-account"></a>1. Azure hesabınıza bağlanma

PowerShell konsolunu yükseltilmiş haklarla açın ve Azure hesabınızda oturum açın. Oturum açtıktan sonra, hesap ayarlarınız Azure PowerShell için kullanılabilir olacak şekilde indirilir. Aşağıdaki cmdlet, Kaynak Yöneticisi dağıtım modeli için Azure hesabınızın oturum açma kimlik bilgilerini ister:

```powershell
Connect-AzAccount
```

Azure aboneliklerinizin bir listesini alın.

```powershell
Get-AzSubscription
```

Birden fazla aboneliğiniz varsa, kullanmak istediğiniz aboneliği belirtin.

```powershell
Select-AzSubscription -SubscriptionName "Name of subscription"
```

Ardından, klasik PowerShell cmdlet 'lerini (hizmet yönetimi) kullanmak için oturum açın. Klasik dağıtım modeli için Azure hesabınızı eklemek üzere aşağıdaki komutu kullanın:

```powershell
Add-AzureAccount
```

Aboneliklerinizin bir listesini alın. Bu adım, Azure modülünün yüklemesine bağlı olarak hizmet yönetimi cmdlet 'leri eklenirken gerekli olabilir.

```powershell
Get-AzureSubscription
```

Birden fazla aboneliğiniz varsa, kullanmak istediğiniz aboneliği belirtin.

```powershell
Select-AzureSubscription -SubscriptionName "Name of subscription"
```

### <a name="2-view-the-network-configuration-file-values"></a>2. Ağ yapılandırma dosyası değerlerini görüntüleme

Azure portal bir sanal ağ oluşturduğunuzda, Azure 'un kullandığı tam ad Azure portal görünmez. Örneğin, Azure portal ' ClassicVNet ' adında görünen bir sanal ağın, ağ yapılandırma dosyasında çok daha uzun bir adı olabilir. Ad şöyle görünebilir: ' ClassicRG ClassicVNet ' grubunu gruplayın. Bu adımlarda, ağ yapılandırma dosyasını indirir ve değerlerini görüntüleyebilirsiniz.

Bilgisayarınızda bir dizin oluşturun ve sonra ağ yapılandırma dosyasını dizine aktarın. Bu örnekte, ağ yapılandırma dosyası C:\AzureNet dizinine aktarılır.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Dosyayı bir metin düzenleyici ile açın ve klasik sanal ağınızın adını görüntüleyin. PowerShell cmdlet 'lerinizi çalıştırırken ağ yapılandırma dosyasındaki adları kullanın.

- VNet adları **Virtualnetworksite Name =** olarak listelenir.
- Site adları **Localnetworksite Name =** olarak listelenir

### <a name="3-create-the-connection"></a>3. Bağlantı oluşturma

Paylaşılan anahtarı ayarlayın ve klasik VNet 'ten Kaynak Yöneticisi VNet 'e bağlantıyı oluşturun. Portalı kullanarak paylaşılan anahtarı ayarlayamazsınız. PowerShell cmdlet 'lerinin klasik sürümünü kullanarak oturum açarken bu adımları çalıştırdığınızdan emin olun. Bunu yapmak için **Add-AzureAccount**kullanın. Aksi takdirde, '-AzureVNetGatewayKey ' öğesini ayarlayameyeceksiniz.

- Bu örnekte, **-vor** , ağ yapılandırma dosyanızda bulunan klasik VNET 'in adıdır. 
- **-Localnetworksitename** , yerel site için belirttiğiniz addır ve ağ yapılandırma dosyanızda bulunur.
- **-Sharedkey** , oluşturduğunuz ve belirlediğiniz bir değerdir. Bu örnekte, *abc123*kullandık, ancak daha karmaşık bir şeyler oluşturabilirsiniz. Burada belirttiğiniz değerin, klasik bağlantıda Kaynak Yöneticisi oluştururken belirttiğiniz değerin aynı olması gerekir.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

## <a name="verify"></a>Bölüm 6-bağlantılarınızı doğrulayın

Azure portal veya PowerShell 'i kullanarak bağlantılarınızı doğrulayabilirsiniz. Doğrulanırken, bağlantı oluşturulduğu sırada bir dakika beklemeniz gerekebilir. Bağlantı başarılı olduğunda, bağlantı durumu ' bağlanıyor ' iken ' Connected ' olarak değişir.

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Klasik sanal ağınızdan Kaynak Yöneticisi VNet 'e bağlantıyı doğrulamak için

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Kaynak Yöneticisi VNet 'ten klasik VNet 'e bağlantıyı doğrulamak için

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>Sanal ağlar arası bağlantılar hakkında SSS

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
