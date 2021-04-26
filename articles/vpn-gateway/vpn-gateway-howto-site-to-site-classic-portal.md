---
title: 'Şirket içi ağınızı bir Azure sanal ağına bağlama: Siteden Siteye VPN (klasik): Portal | Microsoft Docs'
description: Şirket içi ağınız ile klasik Azure sanal ağı arasında genel İnternet üzerinden bir IPSec bağlantısı oluşturun.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 4ad05281f13885327c855a261a3101388f38af83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878062"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Azure portalını (klasik) kullanarak Siteden Siteye bağlantı oluşturma

Bu makalede, Azure portalını kullanarak şirket içi ağınızdan VNet’e Siteden Siteye VPN ağ geçidi bağlantısı oluşturma işlemi gösterilir. Bu makaledeki adımlar, klasik dağıtım modeli için geçerlidir ve geçerli dağıtım modeli için geçerli değildir Kaynak Yöneticisi. Ayrıca aşağıdaki listeden farklı bir seçenek belirtip farklı bir dağıtım aracı veya dağıtım modeli kullanarak da bu yapılandırmayı oluşturabilirsiniz:

> [!div class="op_single_selector"]
> * [Azure portalı](./tutorial-site-to-site-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure portal (klasik)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 

Siteden Siteye VPN ağ geçidi bağlantısı, şirket içi ağınızı bir IPsec/IKE (IKEv1 veya IKEv2) tüneli üzerinden Azure sanal ağına bağlamak için kullanılır. Bu bağlantı türü için, şirket içinde yer alan ve kendisine atanmış dışarıya yönelik bir genel IP adresi atanmış olan bir VPN cihazı gerekir. VPN ağ geçitleri hakkında daha fazla bilgi için bkz. [VPN ağ geçidi hakkında](vpn-gateway-about-vpngateways.md).

![Siteden Siteye şirket içi ve dışı karışık VPN Gateway bağlantısı diyagramı](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>Başlamadan önce

Yapılandırmaya başlamadan önce aşağıdaki ölçütleri karşıladığınızı doğrulayın:

* Klasik dağıtım modelinde çalışmak istediğinizi doğrulayın. Resource Manager dağıtım modelinde çalışmak istiyorsanız bkz. [Siteden Siteye bağlantı oluşturma (Resource Manager)](./tutorial-site-to-site-portal.md). Klasik model eski olduğu için Kaynak Yöneticisi dağıtım modelini kullanmanızı öneririz.
* Uyumlu bir VPN cihazı ve bu cihazı yapılandırabilecek birinin bulunduğundan emin olun. Uyumlu VPN cihazları ve cihaz yapılandırması hakkında daha fazla bilgi için bkz.[VPN Cihazları Hakkında](vpn-gateway-about-vpn-devices.md).
* VPN cihazınız için dışarıya dönük genel bir IPv4 adresi olduğunu doğrulayın.
* Şirket içi ağ yapılandırmanızda bulunan IP adresi aralıklarıyla ilgili fazla bilginiz yoksa size bu ayrıntıları sağlayabilecek biriyle çalışmanız gerekir. Bu yapılandırmayı oluşturduğunuzda, Azure’un şirket içi konumunuza yönlendireceği IP adres aralığı ön eklerini oluşturmanız gerekir. Şirket içi ağınızın alt ağlarından hiçbiri, bağlanmak istediğiniz sanal ağ alt ağlarıyla çakışamaz.
* Paylaşılan anahtarı belirtmek ve VPN ağ geçidi bağlantısını oluşturmak için PowerShell gereklidir. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="sample-configuration-values-for-this-exercise"></a><a name="values"></a>Bu alıştırma için örnek yapılandırma değerleri

Bu makaledeki örneklerde aşağıdaki değerler kullanılır. Bu değerleri kullanarak bir test ortamı oluşturabilir veya bu makaledeki örnekleri daha iyi anlamak için bunlara bakabilirsiniz. Genellikle, adres alanı için IP adresi değerleriyle çalışırken, çakışan adres alanlarını önlemek için, yönlendirmeyi etkileyebilecek ağ yöneticinizle koordine etmek istersiniz. Bu durumda, çalışan bir bağlantı oluşturmak istiyorsanız IP adresi değerlerini kendi değerlerinizle değiştirin.

* **Kaynak Grubu:** TestRG1
* **VNet Name:** TestVNet1
* **Adres alanı:** 10.11.0.0/16
* **Alt ağ adı:** Uçta
* **Alt ağ adres aralığı:** 10.11.0.0/24
* **Gatewaysubnet:** 10.11.255.0/27
* **Bölge:** (US) Doğu ABD
* **Yerel site adı:** Site2
* **İstemci adres alanı:** Şirket içi sitenizde yer alan adres alanı.

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Sanal ağ oluşturma

S2S bağlantısı için kullanılacak bir sanal ağ oluşturduğunuzda, belirttiğiniz adres alanlarının, bağlanmak istediğiniz yerel siteler için istemci adres alanlarından herhangi biriyle çakışmadığından emin olun. Çakışan alt ağlarınız varsa bağlantınız düzgün şekilde gerçekleşmeyebilir.

* Zaten bir sanal ağınız varsa, ayarların VPN ağ geçidi tasarımınızla uyumlu olduğunu doğrulayın. Diğer ağlarla çakışabilecek herhangi bir alt ağ olup olmadığına özellikle dikkat edin.

* Sanal ağınız yoksa bir sanal ağ oluşturun. Ekran görüntüleri örnek olarak verilmiştir. Değerlerin kendinizinkilerle değiştirildiğinden emin olun.

### <a name="to-create-a-virtual-network"></a>Sanal ağ oluşturmak için

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="configure-the-site-and-gateway"></a><a name="localsite"></a>Siteyi ve ağ geçidini yapılandırma

### <a name="to-configure-the-site"></a>Siteyi yapılandırmak için

Yerel site genellikle şirket içi konumunuzu ifade eder. Bağlantı oluşturacağınız VPN cihazının IP adresini ve VPN ağ geçidi üzerinden VPN cihazına yönlendirilecek IP aralıklarını içerir.

1. VNet 'iniz sayfasında, **Ayarlar**' ın altında, **siteden siteye bağlantılar**' ı seçin.
1. Siteden siteye bağlantılar sayfasında **+ Ekle**' yi seçin.
1. **VPN bağlantısı ve ağ geçidi yapılandırma** sayfasında, **bağlantı türü** için **siteden siteye** seçili bırakın. Bu alıştırmada, [örnek değerlerinin](#values) ve kendi değerlerinizin bir birleşimini kullanmanız gerekir.

   * **VPN ağ geçidi IP adresi:** Şirket içi ağınızdaki VPN cihazının genel IP adresidir. VPN cihazı, IPv4 genel IP adresi gerektirir. Bağlanmak istediğiniz VPN cihazı için geçerli bir genel IP adresi belirtin. Azure tarafından erişilebilmelidir. VPN cihazınızın IP adresini bilmiyorsanız her zaman bir yer tutucu değeri girebilir (geçerli bir genel IP adresi biçiminde olduğu sürece) ve daha sonra değiştirebilirsiniz.

   * **İstemci Adres alanı:** Bu ağ geçidi aracılığıyla yerel şirket içi ağınıza yönlendirilmesini istediğiniz IP adres aralıklarını listeleyin. Birden fazla adres alanı aralığı ekleyebilirsiniz. Burada belirttiğiniz aralıkların, sanal ağınızın bağlandığı diğer ağlarla ve sanal ağın kendi adres aralıklarıyla çakışmadığından emin olun.
1. Sayfanın en altında, gözden geçir + oluştur ' u seçmeyin. Bunun yerine, Ileri ' yi seçin **: ağ geçidi>**.

### <a name="to-configure-the-virtual-network-gateway"></a><a name="sku"></a>Sanal ağ geçidini yapılandırmak için

1. **Ağ geçidi** sayfasında, aşağıdaki değerleri seçin:

   * **Boyut:** Bu, sanal ağ geçidinizi oluşturmak için kullandığınız ağ geçidi SKU 'sudur. Klasik VPN ağ geçitleri eski ağ geçidi SKU'larını kullanır. Eski ağ geçidi SKU'ları hakkında daha fazla bilgi için bkz. [Sanal ağ geçidi SKU'ları (eski SKU’lar) ile çalışma](vpn-gateway-about-skus-legacy.md). Bu alıştırma için **Standart** seçeneğini belirleyebilirsiniz.

   * **Yönlendirme türü:** Ağ geçidinizin yönlendirme türünü seçin. Bu seçenek VPN türü olarak da bilinir. Ağ geçidini bir türden diğerine dönüştüremediği için doğru türü seçmeniz önemlidir. VPN cihazınız seçtiğiniz yönlendirme türü ile uyumlu olmalıdır. Yönlendirme türü hakkında daha fazla bilgi için bkz. [VPN Gateway ayarları hakkında](vpn-gateway-about-vpn-gateway-settings.md#vpntype). 'RouteBased' ve 'PolicyBased' VPN türlerine başvuran makaleler görebilirsiniz. 'Dinamik' seçeneği 'RouteBased', 'Statik' seçeneği ise 'PolicyBased' değerine karşılık gelir. Genellikle, Dinamik yönlendirmeyi istersiniz.

   * **Ağ geçidi alt ağı:** Belirttiğiniz ağ geçidi alt ağının boyutu, oluşturmak istediğiniz VPN ağ geçidi yapılandırmasına bağlıdır. /29 kadar küçük bir ağ geçidi alt ağı oluşturmak mümkün olsa da /27 veya /28 değerini kullanmanız önerilir. Bu değer, daha fazla adres içeren daha büyük bir alt ağ oluşturur. Daha büyük bir ağ geçidi alt ağı kullanmak, olası gelecek yapılandırmaları barındırmak için yeterli IP adresi bulunmasını sağlar.

1. Ayarlarınızı doğrulamak için sayfanın altındaki **gözden geçir + oluştur** ' u seçin. Dağıtılacak **Oluştur** ' u seçin. Seçtiğiniz ağ geçidi SKU 'suna bağlı olarak, sanal ağ geçidinin oluşturulması 45 dakika sürebilir.

## <a name="configure-your-vpn-device"></a><a name="vpndevice"></a>VPN cihazınızı yapılandırma

Bir şirket içi ağı ile Siteden Siteye bağlantılar için VPN cihazı gerekir. Bu adımda VPN cihazınızı yapılandıracaksınız. VPN cihazınızı yapılandırırken aşağıdaki değerlere sahip olmanız gerekir:

* Paylaşılan bir anahtar. Siteden Siteye VPN bağlantınızı oluştururken belirttiğiniz paylaşılan anahtarın aynısıdır. Bu örneklerde temel bir paylaşılan anahtar kullanılır. Kullanmak için daha karmaşık bir anahtar oluşturmanız önerilir.
* Sanal ağ geçidinizin Genel IP adresi. Azure Portal, PowerShell veya CLI kullanarak genel IP adresini görüntüleyebilirsiniz.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="retrieve-values"></a><a name="getvalues"></a>Değerleri Al

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-the-connection"></a><a name="CreateConnection"></a>Bağlantıyı oluşturma

> [!NOTE]
> Klasik dağıtım modeli için bu adım Azure portal veya Azure Cloud Shell aracılığıyla kullanılamaz. Azure PowerShell cmdlet 'lerinin hizmet yönetimi (SM) sürümünü masaüstünüzden yerel olarak kullanmanız gerekir.
>

Bu adımda, önceki adımlardan alınan değerleri kullanarak, paylaşılan anahtarı ayarlar ve bağlantıyı oluşturursunuz. Ayarladığınız anahtar, VPN cihazı yapılandırmasında kullanılan anahtarla aynı olmalıdır.

1. Paylaşılan anahtarı ayarlayıp bağlantıyı oluşturun.

   * -VA değerini ve-LocalNetworkSiteName değerini değiştirin. Boşluk içeren bir ad belirtirken, değeri tek tırnak işaretleri içine alın.
   * '-SharedKey ', oluşturduğunuz bir değerdir ve sonra öğesini belirtin. Örnekte, ' abc123 ' kullandık, ancak daha karmaşık bir şeyler oluşturmanız (ve yapmanız gerekir). Önemli olan, burada belirttiğiniz değerin VPN cihazınızı yapılandırırken belirttiğiniz değerle aynı olmasıdır.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
   -LocalNetworkSiteName '6C74F6E6_Site2' -SharedKey abc123
   ```

1. Bağlantı oluşturulduğunda, sonuç şu şekildedir: **Durum: Başarılı**.

## <a name="verify-your-connection"></a><a name="verify"></a>Bağlantınızı doğrulama

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

Bağlantı sorunları yaşıyorsanız, sol bölmedeki içindekiler tablosunun **Sorun Giderme** bölümüne bakın.

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>VPN ağ geçidini sıfırlama

Bir veya daha fazla Siteden Siteye VPN tünelinde şirketler arası VPN bağlantısını kaybederseniz bir Azure VPN ağ geçidinin sıfırlanması yararlıdır. Bu durumda şirket içi VPN cihazlarınızın tümü düzgün çalışır, ancak Azure VPN ağ geçitleriyle IPsec tünelleri kuramaz. Adımlar için bkz. [VPN ağ geçidini sıfırlama](./reset-gateway.md#resetclassic).

## <a name="how-to-change-a-gateway-sku"></a><a name="changesku"></a>Ağ geçidi SKU'sunu değiştirme

Ağ Geçidi SKU 'sunu değiştirme adımları için bkz. [ağ GEÇIDI SKU 'Sunu yeniden boyutlandırma](vpn-gateway-about-SKUS-legacy.md#classicresize).

## <a name="next-steps"></a>Sonraki adımlar

* Bağlantınız tamamlandıktan sonra sanal ağlarınıza sanal makineler ekleyebilirsiniz. Daha fazla bilgi için bkz. [Sanal Makineler](../index.yml).
* Zorlamalı tünel oluşturma hakkında bilgi için bkz. [Zorlamalı tünel oluşturma](vpn-gateway-about-forced-tunneling.md).