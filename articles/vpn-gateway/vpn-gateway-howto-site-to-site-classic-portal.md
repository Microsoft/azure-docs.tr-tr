---
title: 'Şirket içi ağınızı bir Azure sanal ağına bağlama: Siteden siteye VPN (klasik): Portal | Microsoft Docs'
description: Şirket içi ağınız ile klasik Azure sanal ağı arasında genel İnternet üzerinden bir IPSec bağlantısı oluşturun.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: cherylmc
ms.openlocfilehash: 2e6036c5f29614f2e91278b693c07dc3dc8595f2
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575471"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Azure portalını (klasik) kullanarak Siteden Siteye bağlantı oluşturma


Bu makalede, Azure portalını kullanarak şirket içi ağınızdan VNet’e Siteden Siteye VPN ağ geçidi bağlantısı oluşturma işlemi gösterilir. Bu makaledeki adımlar, klasik dağıtım modeli için geçerlidir ve geçerli dağıtım modeli için geçerli değildir Kaynak Yöneticisi. Ayrıca aşağıdaki listeden farklı bir seçenek belirtip farklı bir dağıtım aracı veya dağıtım modeli kullanarak da bu yapılandırmayı oluşturabilirsiniz:

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure portal (klasik)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

Siteden Siteye VPN ağ geçidi bağlantısı, şirket içi ağınızı bir IPsec/IKE (IKEv1 veya IKEv2) tüneli üzerinden Azure sanal ağına bağlamak için kullanılır. Bu bağlantı türü için, şirket içinde yer alan ve kendisine atanmış dışarıya yönelik bir genel IP adresi atanmış olan bir VPN cihazı gerekir. VPN ağ geçitleri hakkında daha fazla bilgi için bkz. [VPN ağ geçidi hakkında](vpn-gateway-about-vpngateways.md).

![Siteden Siteye şirket içi ve dışı karışık VPN Gateway bağlantısı diyagramı](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before"></a>Başlamadan önce

Yapılandırmaya başlamadan önce aşağıdaki ölçütleri karşıladığınızı doğrulayın:

* Klasik dağıtım modelinde çalışmak istediğinizi doğrulayın. Resource Manager dağıtım modelinde çalışmak istiyorsanız bkz. [Siteden Siteye bağlantı oluşturma (Resource Manager)](vpn-gateway-howto-site-to-site-resource-manager-portal.md). Mümkün olduğunda Resource Manager dağıtım modelini kullanmanız önerilir.
* Uyumlu bir VPN cihazı ve bu cihazı yapılandırabilecek birinin bulunduğundan emin olun. Uyumlu VPN cihazları ve cihaz yapılandırması hakkında daha fazla bilgi için bkz.[VPN Cihazları Hakkında](vpn-gateway-about-vpn-devices.md).
* VPN cihazınız için dışarıya dönük genel bir IPv4 adresi olduğunu doğrulayın.
* Şirket içi ağ yapılandırmanızda bulunan IP adresi aralıklarıyla ilgili fazla bilginiz yoksa size bu ayrıntıları sağlayabilecek biriyle çalışmanız gerekir. Bu yapılandırmayı oluşturduğunuzda, Azure’un şirket içi konumunuza yönlendireceği IP adres aralığı ön eklerini oluşturmanız gerekir. Şirket içi ağınızın alt ağlarından hiçbiri, bağlanmak istediğiniz sanal ağ alt ağlarıyla çakışamaz.
* Şu anda, ortak anahtarı belirtmek ve VPN ağ geçidi bağlantısını oluşturmak için PowerShell gereklidir. Azure Hizmet Yönetimi (SM) PowerShell cmdlet’lerinin en son sürümünü yükleyin. Cmdlet 'leri yüklemek için bkz. [hizmet yönetimi](/powershell/azure/servicemanagement/install-azure-ps). Genel olarak PowerShell yüklemeleri hakkında daha fazla bilgi için bkz. [Azure PowerShell yükleme ve yapılandırma](/powershell/azure/overview). Bu yapılandırma için PowerShell ile çalışırken yönetici olarak işlem yaptığınızdan emin olun.

### <a name="values"></a>Bu alıştırma için örnek yapılandırma değerleri

Bu makaledeki örneklerde aşağıdaki değerler kullanılır. Bu değerleri kullanarak bir test ortamı oluşturabilir veya bu makaledeki örnekleri daha iyi anlamak için bunlara bakabilirsiniz.

* **VNet adı:** TestVNet1
* **Adres Alanı:** 
  * 10.11.0.0/16
  * 10.12.0.0/16 (bu alıştırma için isteğe bağlı)
* **Alt ağlar:**
  * Uçta 10.11.0.0/24
  * Sunucusundan 10.12.0.0/24 (Bu alıştırma için isteğe bağlı)
* **GatewaySubnet** 10.11.255.0/27
* **Kaynak grubu:** TestRG1
* **Konumuna** East US
* **DNS sunucusu:** 10.11.0.3 (Bu alıştırma için isteğe bağlı)
* **Yerel site adı:** Site2
* **İstemci adres alanı:** Şirket içi sitenizde bulunan adres alanı.

## <a name="CreatVNet"></a>1. Sanal ağ oluşturma

S2S bağlantısı için kullanılacak bir sanal ağ oluşturduğunuzda, belirttiğiniz adres alanlarının, bağlanmak istediğiniz yerel siteler için istemci adres alanlarından herhangi biriyle çakışmadığından emin olun. Çakışan alt ağlarınız varsa bağlantınız düzgün şekilde gerçekleşmeyebilir.

* Zaten bir sanal ağınız varsa, ayarların VPN ağ geçidi tasarımınızla uyumlu olduğunu doğrulayın. Diğer ağlarla çakışabilecek herhangi bir alt ağ olup olmadığına özellikle dikkat edin. 

* Sanal ağınız yoksa bir sanal ağ oluşturun. Ekran görüntüleri örnek olarak verilmiştir. Değerlerin kendinizinkilerle değiştirildiğinden emin olun.

### <a name="to-create-a-virtual-network"></a>Sanal ağ oluşturmak için

1. Tarayıcıdan [Azure portalına](https://portal.azure.com) gidin ve gerekiyorsa Azure hesabınızda oturum açın.
2. * *+ Kaynak oluştur ' a*tıklayın. **Markette ara** alanına 'Sanal Ağ' yazın. Döndürülen listeden **Sanal Ağ**’ı bulun ve tıklayarak **Sanal Ağ** sayfasını açın.
3. tıklayın **(klasik olarak değiştirin)** ve ardından **Oluştur**' a tıklayın.
4. **Sanal ağ oluştur (klasik)** sayfasında sanal ağ ayarlarını yapılandırın. Bu sayfada, ilk adres alanınızı ve tek alt ağ adres aralığınızı eklersiniz. Sanal ağ oluşturma işlemini tamamladıktan sonra geri dönüp ek alt ağları ve adres alanlarını ekleyin.

   ![Sanal ağ oluşturma sayfası](./media/vpn-gateway-howto-site-to-site-classic-portal/createvnet.png "Sanal ağ oluşturma sayfası")
5. **Abonelik** alanında doğru bir giriş olduğunu doğrulayın. Açılan listeyi kullanarak abonelikleri değiştirebilirsiniz.
6. **Kaynak grubu**’na tıklayın, mevcut bir kaynak grubunu seçin ya da bir ad yazarak yeni bir tane oluşturun. Kaynak grupları hakkında daha fazla bilgi için [Azure Resource Manager’a Genel Bakış](../azure-resource-manager/resource-group-overview.md#resource-groups)’ı ziyaret edin.
7. Ardından, sanal ağınız için **Konum** ayarlarını seçin. Konum bu sanal ağa dağıttığınız kaynakların nerede olacağını belirler.
8. VNet’inizi oluşturmak için **Oluştur**'a tıklayın.
9. “Oluştur”a tıkladıktan sonra, panoda sanal ağınızın ilerleme durumunu yansıtan bir kutucuk görünür. Sanal ağ oluşturulurken kutucuk değişir.

## <a name="additionaladdress"></a>2. Başka adres alanı ekleme

Sanal ağınızı oluşturduktan sonra başka adres alanı ekleyebilirsiniz. Başka adres alanı eklenmesi S2S yapılandırmasının gerekli bir kısmı değildir, ancak birden fazla adres alanı gerekliyse aşağıdaki adımları kullanın:

1. Portalda sanal ağı bulun.
2. Sanal ağınızın sayfasındaki **Ayarlar** bölümünde **Adres alanı**’na tıklayın.
3. Adres alanı sayfasında **+Ekle**’ye tıklayın ve diğer adres alanını girin.

## <a name="dns"></a>3. DNS sunucusu belirleme

DNS ayarları, S2S yapılandırmasının gerekli bir kısmı değildir, ancak ad çözünürlüğü istiyorsanız DNS gereklidir. Bir değer belirtildiğinde yeni bir DNS sunucusu oluşturulmaz. Belirttiğiniz DNS sunucusu IP adresi, bağlandığınız kaynakların adlarını çözümleyebilen bir DNS sunucusu olmalıdır. Örnek ayarlar için özel bir IP adresi kullandık. Bizim kullandığımız IP adresi muhtemelen DNS sunucunuzun IP adresi değildir. Kendi değerlerinizi kullandığınızdan emin olun.

Sanal ağınızı oluşturduktan sonra ad çözünürlüğünü işlemek için bir DNS sunucusunun IP adresini ekleyebilirsiniz. Sanal ağınızın ayarlarını açın, DNS sunucularına tıklayın ve ad çözünürlüğü kullanmak istediğiniz DNS sunucusunun IP adresini ekleyin.

1. Portalda sanal ağı bulun.
2. Sanal ağınızın sayfasındaki **Ayarlar** bölümünde **DNS sunucuları**’na tıklayın.
3. Bir DNS sunucusu ekleyin.
4. Ayarlarınızı kaydetmek için sayfanın en üstünde yer alan **Kaydet**’e tıklayın.

## <a name="localsite"></a>4. Yerel siteyi yapılandırma

Yerel site genellikle şirket içi konumunuzu ifade eder. Bağlantı oluşturacağınız VPN cihazının IP adresini ve VPN ağ geçidi üzerinden VPN cihazına yönlendirilecek IP aralıklarını içerir.

1. VNet 'iniz sayfasında, **Ayarlar**altında **Diyagram**' a tıklayın.
1. **VPN bağlantıları** sayfasında, mevcut VPN bağlantınız **yok ' a tıklayın. Başlamak için**buraya tıklayın.
1. **Bağlantı türü**için **siteden siteye** seçili olarak bırakın.
4. **Yerel site - Gerekli ayarları yapılandırın**’a tıklayarak **Yerel site** sayfasını açın. Ayarları yapılandırın ve ardından **Tamam**’a tıklayarak ayarları kaydedin.
   - **Ada** Tanımlamanızı kolaylaştırmak için yerel siteniz için bir ad oluşturun.
   - **VPN ağ geçidi IP adresi:** Bu, şirket içi ağınızın VPN cihazının genel IP adresidir. VPN cihazı, IPv4 genel IP adresi gerektirir. Bağlanmak istediğiniz VPN cihazı için geçerli bir genel IP adresi belirtin. Azure tarafından erişilebilmelidir. VPN cihazınızın IP adresini bilmiyorsanız her zaman bir yer tutucu değeri girebilir (geçerli bir genel IP adresi biçiminde olduğu sürece) ve daha sonra değiştirebilirsiniz.
   - **İstemci adres alanı:** Bu ağ geçidi aracılığıyla yerel şirket içi ağa yönlendirilmesini istediğiniz IP adresi aralıklarını listeleyin. Birden fazla adres alanı aralığı ekleyebilirsiniz. Burada belirttiğiniz aralıkların, sanal ağınızın bağlandığı diğer ağlarla ve sanal ağın kendi adres aralıklarıyla çakışmadığından emin olun.

   ![Yerel site](./media/vpn-gateway-howto-site-to-site-classic-portal/localnetworksite.png "Configure local site")

Yerel site sayfasını kapatmak için **Tamam** ' ı tıklatın. **Yenı VPN bağlantısı sayfasını kapatmak için Tamam '** a tıklamayın.

## <a name="gatewaysubnet"></a>5. Ağ geçidi alt ağını yapılandırma

VPN ağ geçidiniz için bir ağ geçidi alt ağı oluşturmanız gerekir. Ağ geçidi alt ağı, VPN ağ geçidi hizmetlerinin kullandığı IP adreslerini içerir.


1. **Yeni VPN Bağlantısı** sayfasında, **Ağ geçidini hemen oluştur** onay kutusunu işaretleyin. 'İsteğe bağlı ağ geçidi yapılandırması' sayfası görüntülenir. Onay kutusunu seçmezseniz, ağ geçidi alt ağını yapılandırmaya yönelik sayfayı görmezsiniz.

   ![Ağ geçidi yapılandırması - Alt ağ, boyut, yönlendirme türü](./media/vpn-gateway-howto-site-to-site-classic-portal/optional.png "Gateway configuration - Subnet, size, routing type")
2. **Ağ geçidi yapılandırması** sayfasını açmak için **İsteğe bağlı ağ geçidi yapılandırması - Alt ağ, boyut ve yönlendirme türü**’ne tıklayın.
3. **Ağ Geçidi Yapılandırması** sayfasında **Alt Ağ - Gerekli ayarları yapılandırın** öğesine tıklayarak **Alt ağ ekle** sayfasını açın. Bu ayarları yapılandırmayı tamamladığınızda **Tamam**' a tıklayın.

   ![Ağ geçidi yapılandırması - ağ geçidi alt ağı](./media/vpn-gateway-howto-site-to-site-classic-portal/subnetrequired.png "Gateway configuration - gateway subnet")
4. **Alt ağ ekle** sayfasında ağ geçidi alt ağını ekleyin. Belirttiğiniz ağ geçidi alt ağının boyutu, oluşturmak istediğiniz VPN ağ geçidi yapılandırmasına bağlıdır. /29 kadar küçük bir ağ geçidi alt ağı oluşturmak mümkün olsa da /27 veya /28 değerini kullanmanız önerilir. Bu değer, daha fazla adres içeren daha büyük bir alt ağ oluşturur. Daha büyük bir ağ geçidi alt ağı kullanmak, olası gelecek yapılandırmaları barındırmak için yeterli IP adresi bulunmasını sağlar.

   ![Ağ geçidi alt ağı ekleme](./media/vpn-gateway-howto-site-to-site-classic-portal/addgwsubnet.png "Add gateway subnet")

## <a name="sku"></a>6. SKU ve VPN türünü belirtme

1. Ağ geçidi **Boyutu** seçin. Bu seçenek, sanal ağ geçidinizi oluşturmak için kullandığınız ağ geçidi SKU’sudur. Klasik VPN ağ geçitleri eski ağ geçidi SKU'larını kullanır. Eski ağ geçidi SKU'ları hakkında daha fazla bilgi için bkz. [Sanal ağ geçidi SKU'ları (eski SKU’lar) ile çalışma](vpn-gateway-about-skus-legacy.md).

   ![SKUL ve VPN türü seçme](./media/vpn-gateway-howto-site-to-site-classic-portal/sku.png "Select SKU and VPN type")
2. Ağ geçidiniz için **Yönlendirme Türü** seçin. Bu seçenek VPN türü olarak da bilinir. Ağ geçidini bir türden diğerine dönüştüremediği için doğru türü seçmeniz önemlidir. VPN cihazınız seçtiğiniz yönlendirme türü ile uyumlu olmalıdır. Yönlendirme türü hakkında daha fazla bilgi için bkz. [VPN Gateway ayarları hakkında](vpn-gateway-about-vpn-gateway-settings.md#vpntype). 'RouteBased' ve 'PolicyBased' VPN türlerine başvuran makaleler görebilirsiniz. 'Dinamik' seçeneği 'RouteBased', 'Statik' seçeneği ise 'PolicyBased' değerine karşılık gelir.
3. Ayarları kaydetmek için **Tamam**’a tıklayın.
4. **Yenı VPN bağlantısı** sayfasında, sanal ağ geçidinizin dağıtmaya başlamak için sayfanın altındaki **Tamam** ' a tıklayın. Seçtiğiniz SKU’ya bağlı olarak, sanal ağ geçidi oluşturma 45 dakikaya kadar sürebilir.

## <a name="vpndevice"></a>7. VPN cihazınızı yapılandırma

Bir şirket içi ağı ile Siteden Siteye bağlantılar için VPN cihazı gerekir. Bu adımda VPN cihazınızı yapılandıracaksınız. VPN cihazınızı yapılandırırken şunlar gerekir:

- Paylaşılan bir anahtar. Siteden Siteye VPN bağlantınızı oluştururken belirttiğiniz paylaşılan anahtarın aynısıdır. Bu örneklerde temel bir paylaşılan anahtar kullanılır. Kullanmak için daha karmaşık bir anahtar oluşturmanız önerilir.
- Sanal ağ geçidinizin Genel IP adresi. Azure Portal, PowerShell veya CLI kullanarak genel IP adresini görüntüleyebilirsiniz.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>8. Bağlantı oluşturma
Bu adımda, paylaşılan anahtarı ayarlayabilir ve bağlantıyı oluşturabilirsiniz. Ayarladığınız anahtar, VPN cihazı yapılandırmasında kullanılan anahtarla aynı olmalıdır.

> [!NOTE]
> Şu anda, bu yapılandırma Azure portalında mevcut değildir. Azure PowerShell cmdlet’lerinin Hizmet Yönetimi (SM) sürümünü kullanmanız gerekir. Bu cmdlet 'leri yükleme hakkında bilgi için [başlamadan önce](#before) bölümüne bakın.
>

### <a name="step-1-connect-to-your-azure-account"></a>1\.Adım Azure hesabınıza bağlanma

PowerShell hizmeti yönetim modülünü kullanarak bu komutları yerel olarak çalıştırmanız gerekir. Hizmet yönetimine geçiş yapmak için şu komutu kullanın:

```powershell
azure config mode asm
```

1. PowerShell konsolunuzu yükseltilmiş haklarla açın ve hesabınıza bağlanın. Bağlanmanıza yardımcı olması için aşağıdaki örneği kullanın:

   ```powershell
   Add-AzureAccount
   ```
2. Hesapla ilişkili abonelikleri kontrol edin.

   ```powershell
   Get-AzureSubscription
   ```
3. Birden fazla aboneliğiniz varsa, kullanmak istediğiniz aboneliği seçin.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```

### <a name="step-2-set-the-shared-key-and-create-the-connection"></a>2\.Adım Paylaşılan anahtarı ayarlama ve bağlantıyı oluşturma

Portalda klasik bir sanal ağ oluşturduğunuzda (PowerShell kullanarak), Azure Kaynak grubu adını kısa ada ekler. Örneğin, Azure 'a göre, bu alıştırma için oluşturduğunuz VNet 'in adı "TestVNet1" değil "Group TestRG1 TestVNet1" dır. PowerShell, portalda görünen kısa adı değil, sanal ağın tam adını gerektirir. Uzun ad portalda görünmez. Aşağıdaki adımlar, sanal ağ adının tam değerlerini almak için ağ yapılandırma dosyasını dışarı aktarmaya yardımcı olur. 

1. Bilgisayarınızda bir dizin oluşturun ve sonra ağ yapılandırma dosyasını dizine aktarın. Bu örnekte, ağ yapılandırma dosyası C:\AzureNet dizinine aktarılır.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Ağ yapılandırma dosyasını bir xml düzenleyicisi ile açın ve 'LocalNetworkSite name' ile 'VirtualNetworkSite name' değerlerini denetleyin. Bu alıştırma için örneği, XML içindeki değerleri yansıtacak şekilde değiştirin. Boşluk içeren bir ad belirtirken, değeri tek tırnak işaretleri içine alın.

3. Paylaşılan anahtarı ayarlayıp bağlantıyı oluşturun. '-SharedKey' sizin oluşturup belirttiğiniz bir değerdir. Örnekte 'abc123' değeri kullanılmıştır, ancak siz daha karmaşık bir değer oluşturabilirsiniz (ve oluşturmalısınız). Önemli olan, burada belirttiğiniz değerin VPN cihazınızı yapılandırırken belirttiğiniz değerle aynı olmasıdır.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
   -LocalNetworkSiteName 'D1BFC9CB_Site2' -SharedKey abc123
   ```
   Bağlantı oluşturulduğunda sonuç şu olur: **Durumlarına Başarılı**oldu.

## <a name="verify"></a>9. Bağlantınızı doğrulama

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

Bağlantı sorunları yaşıyorsanız, sol bölmedeki içindekiler tablosunun **Sorun Giderme** bölümüne bakın.

## <a name="reset"></a>VPN ağ geçidini sıfırlama

Bir veya daha fazla Siteden Siteye VPN tünelinde şirketler arası VPN bağlantısını kaybederseniz bir Azure VPN ağ geçidinin sıfırlanması yararlıdır. Bu durumda şirket içi VPN cihazlarınızın tümü düzgün çalışır, ancak Azure VPN ağ geçitleriyle IPsec tünelleri kuramaz. Adımlar için bkz. [VPN ağ geçidini sıfırlama](vpn-gateway-resetgw-classic.md#resetclassic).

## <a name="changesku"></a>Ağ geçidi SKU'sunu değiştirme

Bir ağ geçidi SKU'sunu değiştirme adımları için bkz. [Ağ geçidi SKU'sunu yeniden boyutlandırma](vpn-gateway-about-SKUS-legacy.md#classicresize).

## <a name="next-steps"></a>Sonraki adımlar

* Bağlantınız tamamlandıktan sonra sanal ağlarınıza sanal makineler ekleyebilirsiniz. Daha fazla bilgi için bkz. [Sanal Makineler](https://docs.microsoft.com/azure/).
* Zorlamalı Tünel Oluşturma hakkında bilgi için bkz. [Zorlamalı Tünel Oluşturma Hakkında](vpn-gateway-about-forced-tunneling.md).
