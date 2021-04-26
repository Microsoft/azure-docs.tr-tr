---
title: 'P2S kullanarak bir bilgisayarı sanal ağa bağlama: sertifika kimlik doğrulaması: Azure portal klasik'
titleSuffix: Azure VPN Gateway
description: Azure portal kullanarak klasik bir noktadan siteye VPN Gateway bağlantısı oluşturun.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 42b0945de55775f55f20cefdeb547cb5d6492c06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94657083"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>Sertifika kimlik doğrulaması kullanarak noktadan siteye bağlantı yapılandırma (klasik)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Bu makalede, Noktadan siteye bağlantı ile sanal ağ oluşturma konusu gösterilmektedir. Azure portal kullanarak bu VNet 'i klasik dağıtım modeliyle oluşturursunuz. Bu yapılandırma, bağlanan istemcinin kimliğini doğrulamak için otomatik olarak imzalanan veya CA tarafından verilen sertifikaları kullanır. Aşağıdaki makalelerde açıklanan seçenekleri kullanarak bu yapılandırmayı farklı bir dağıtım aracı veya modeliyle de oluşturabilirsiniz:

> [!div class="op_single_selector"]
> * [Azure portalı](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Azure portal (klasik)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

Tek bir istemci bilgisayarından sanal ağınıza güvenli bir bağlantı oluşturmak için Noktadan siteye (P2S) VPN ağ geçidi kullanırsınız. Sanal ağınıza uzak bir konumdan bağlanmak istediğinizde Noktadan siteye VPN bağlantıları yararlı olur. Bir sanal ağa bağlanması gereken yalnızca birkaç istemciniz olduğunda, P2S VPN, siteden siteye VPN yerine kullanmak üzere yararlı bir çözümdür. P2S VPN bağlantısı, istemci bilgisayardan başlatılmasıyla oluşturulur.

> [!IMPORTANT]
> Klasik dağıtım modeli yalnızca Windows VPN istemcilerini destekler ve SSL tabanlı VPN protokolü olan Güvenli Yuva Tünel Protokolünü (SSTP) kullanır. Windows dışı VPN istemcilerini desteklemek için, sanal ortamınızı Kaynak Yöneticisi dağıtım modeliyle oluşturmanız gerekir. Resource Manager dağıtım modeli SSTP’ye ek olarak IKEv2 VPN desteği sunar. Daha fazla bilgi için bkz. [P2S bağlantıları hakkında](point-to-site-about.md).
>
>

![Noktadan Siteye diyagramı](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="settings-and-requirements"></a>Ayarlar ve gereksinimler

### <a name="requirements"></a>Gereksinimler

Noktadan siteye sertifika kimlik doğrulama bağlantıları aşağıdaki öğeleri gerektirir. Bu makalede, bunları oluşturmanıza yardımcı olacak adımlar vardır.

* Bir Dinamik VPN ağ geçidi.
* Azure’a yüklenmiş bir kök sertifikanın ortak anahtarı (.cer dosyası). Bu anahtar, güvenilen bir sertifika olarak kabul edilir ve kimlik doğrulaması için kullanılır.
* Kök sertifikadan oluşturulmuş ve bağlanacak her bir istemci bilgisayara yüklenmiş istemci sertifikası. Bu sertifika, istemci kimlik doğrulaması için kullanılır.
* Bir VPN istemcisi yapılandırma paketi oluşturulmalı ve bağlanan her istemci bilgisayara yüklenmelidir. İstemci yapılandırma paketi, işletim sisteminde zaten bulunan yerel VPN istemcisini sanal ağa bağlanmak için gerekli bilgilerle yapılandırır.

Noktadan siteye bağlantılar için bir VPN cihazı veya şirket içi genel kullanıma yönelik bir IP adresi gerekmez. VPN bağlantısı SSTP (Güvenli Yuva Tünel Protokolü) üzerinden oluşturulur. Sunucu tarafında 1.0, 1.1 ve 1.2 SSTP sürümlerini destekliyoruz. Kullanılacak sürüm, istemci tarafından belirlenir. Windows 8.1 ve sonraki sürümlerinde, SSTP'de varsayılan olarak 1.2 kullanılır.

Daha fazla bilgi için bkz. [Noktadan siteye bağlantılar](point-to-site-about.md) ve [SSS](#faq)hakkında.

### <a name="example-settings"></a>Örnek ayarlar

Aşağıdaki değerleri kullanarak bir test ortamı oluşturun veya bu makaledeki örnekleri daha iyi anlamak için bu değerlere başvurun:

* **Kaynak grubu:** TestRG
* **VNet Adı:** VNet1
* **Adres alanı:** 192.168.0.0/16 <br>Bu örnekte yalnızca bir adres alanı kullanılmaktadır. Sanal ağınıza ait birden fazla adres alanı olabilir.
* **Alt ağ adı:** Uçta
* **Alt ağ adres aralığı:** 192.168.1.0/24
* **Gatewaysubnet:** 10.11.255.0/27
* **Bölge:** (US) Doğu ABD
* **İstemci adres alanı:** 172.16.201.0/24 <br> Sanal ağa bu noktadan siteye bağlantıyı kullanarak bağlanan VPN istemcileri belirtilen havuzdan bir IP adresi alır.
* **Bağlantı türü**: **Noktadan siteye** seçeneğini belirleyin.
* **Gatewaysubnet adres aralığı (CIDR bloğu):** 192.168.200.0/24

Başlamadan önce, bir Azure aboneliğiniz olduğunu doğrulayın. Henüz Azure aboneliğiniz yoksa [MSDN abonelik avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial) için kaydolabilirsiniz.

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Sanal ağ oluşturma

Zaten bir sanal ağınız varsa, ayarların VPN ağ geçidi tasarımınızla uyumlu olduğunu doğrulayın. Diğer ağlarla çakışabilecek herhangi bir alt ağ olup olmadığına özellikle dikkat edin.

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="create-a-vpn-gateway"></a><a name="gateway"></a>VPN ağ geçidi oluşturma

1. Oluşturduğunuz VNet 'e gidin.
1. VNet sayfasında, ayarlar altında **ağ geçidi**' ni seçin. **Ağ geçidi** sayfasında, sanal ağınızın ağ geçidini görüntüleyebilirsiniz. Bu sanal ağ henüz bir ağ geçidine sahip değil. **Bağlantı ve ağ geçidi eklemek için buraya tıklayacağını** belirten nota tıklayın.
1. **VPN bağlantısı ve ağ geçidi yapılandırma** sayfasında, aşağıdaki ayarları seçin:

   * Bağlantı türü: Noktadan siteye
   * İstemci adres alanı: VPN istemcilerinin bağlanırken bir IP adresi alacağı IP adresi aralığını ekleyin. ' Den bağlandığınız şirket içi konum ile veya bağlandığınız VNet ile örtüşmeyen özel bir IP adresi aralığı kullanın.
1. **Şu anda bir ağ geçidini yapılandırma** onay kutusunu işaretsiz bırakın. Bir ağ geçidi oluşturacağız.
1. Sayfanın alt kısmındaki Ileri ' yi seçin **: ağ geçidi >**.
1. **Ağ geçidi** sekmesinde, aşağıdaki değerleri seçin:

   * **Boyut:** Boyut, sanal ağ geçidinizin ağ geçidi SKU 'SU olur. Azure portal **varsayılan SKU varsayılandır.** Ağ Geçidi SKU 'Ları hakkında daha fazla bilgi için bkz. [VPN Gateway ayarları hakkında](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
   * **Yönlendirme türü:** Noktadan siteye yapılandırma için **dinamik** ' i seçmeniz gerekir. Statik yönlendirme çalışmayacak.
   * **Ağ geçidi alt ağı:** Bu alan zaten tekrar doldurulmuştur. Adı değiştiremezsiniz. PowerShell 'i veya başka bir yöntemi kullanarak adı değiştirmeye çalışırsanız, ağ geçidi düzgün çalışmaz.
   * **Adres aralığı (CIDR bloğu):** /29 kadar küçük bir ağ geçidi alt ağı oluşturmak mümkün olsa da, en az/28 veya/27 ' yi seçerek daha fazla adres içeren daha büyük bir alt ağ oluşturmanızı öneririz. Bunun yapılması, gelecekte isteyebileceğiniz olası ek yapılandırmalara uyum sağlamak için yeterli adrese izin verir. Ağ geçidi alt ağlarıyla çalışırken, ağ güvenlik grubunu (NSG) ağ geçidi alt ağıyla ilişkilendirmekten kaçının. Bir ağ güvenlik grubunun bu alt ağ ile ilişkilendirilmesi, VPN ağ geçidinizin beklendiği gibi çalışmamasına neden olabilir.
1. Ayarlarınızı doğrulamak için **gözden geçir + oluştur** ' u seçin.
1. Doğrulama başarılı olduktan sonra **Oluştur**' u seçin. Bir VPN ağ geçidinin tamamlanması, seçtiğiniz ağ geçidi SKU 'suna bağlı olarak 45 dakika sürebilir.

## <a name="create-certificates"></a><a name="generatecerts"></a>Sertifika oluşturma

Azure, Noktadan siteye VPN 'Lerde VPN istemcilerinin kimliğini doğrulamak için sertifikaları kullanır. Kök sertifikanın ortak anahtar bilgilerini Azure'a yükleyin. Ortak anahtar daha sonra *güvenilir* olarak değerlendirilir. İstemci sertifikalarının güvenilir kök sertifikadan oluşturulması ve ardından Certificates-Current User\person\certificates sertifika deposundaki her bir istemci bilgisayara yüklenmesi gerekir. Sertifika, VNet 'e bağlanırken istemcinin kimliğini doğrulamak için kullanılır. 

Otomatik olarak imzalanan sertifikalar kullanıyorsanız, bunların belirli parametreler kullanılarak oluşturulması gerekir. [PowerShell ve Windows 10](vpn-gateway-certificates-point-to-site.md)veya [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)yönergelerini kullanarak otomatik olarak imzalanan bir sertifika oluşturabilirsiniz. Otomatik olarak imzalanan kök sertifikaları kullanırken ve otomatik olarak imzalanan kök sertifikadan istemci sertifikaları oluştururken bu talimatlardaki adımları izlemeniz önemlidir. Aksi halde, oluşturduğunuz sertifikalar P2S bağlantılarıyla uyumlu olmaz ve bağlantı hatası alırsınız.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>Kök sertifika için ortak anahtarı (. cer) alma

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>İstemci sertifikası oluşturma

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>Kök sertifika .cer dosyasını karşıya yükleme

Ağ Geçidi oluşturulduktan sonra, güvenilen kök sertifikanın. cer dosyasını (ortak anahtar bilgilerini içerir) Azure sunucusuna yükleyin. Kök sertifika için özel anahtarı karşıya yüklemeyin. Sertifikayı karşıya yükledikten sonra Azure, güvenilir kök sertifikadan oluşturulmuş bir istemci sertifikası yüklemiş olan istemcilerin kimliğini doğrulamak için bunu kullanır. Gerekirse, daha sonra ek Güvenilen kök sertifika dosyalarını (en fazla 20) karşıya yükleyebilirsiniz.

1. Oluşturduğunuz sanal ağa gidin.
1. **Ayarlar**' ın altında, **Noktadan siteye bağlantılar**' ı seçin.
1. **Sertifikayı Yönet**' i seçin.
1. **Karşıya Yükle**’yi seçin.
1. **Sertifikayı karşıya yükle** bölmesinde, klasör simgesini seçin ve karşıya yüklemek istediğiniz sertifikaya gidin.
1. **Karşıya Yükle**’yi seçin.
1. Sertifika başarıyla karşıya yüklendikten sonra, sertifikayı Yönet sayfasında görüntüleyebilirsiniz. Karşıya yüklediğiniz sertifikayı görüntülemek için **Yenile** ' yi seçmeniz gerekebilir.

## <a name="configure-the-client"></a>İstemciyi yapılandırma

Noktadan siteye VPN kullanarak bir sanal ağa bağlanmak için, her istemcinin yerel Windows VPN istemcisini yapılandırmak üzere bir paket yüklemesi gerekir. Yapılandırma paketi, sanal ağa bağlanmak için gereken ayarlarla yerel Windows VPN istemcisini yapılandırır.

Sürümünün istemci mimarisiyle eşleşmesi şartıyla, her istemci bilgisayarda aynı VPN istemcisi yapılandırma paketini kullanabilirsiniz. Desteklenen istemci işletim sistemlerinin listesi için bkz. [Noktadan siteye bağlantılar](point-to-site-about.md) ve [SSS](#faq)hakkında.

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>VPN istemcisi yapılandırma paketi oluşturma ve yüklemeyi

1. VNet 'iniz için **Noktadan siteye bağlantı** ayarlarına gidin.
1. Sayfanın üst kısmında, yükleneceği istemci işletim sistemine karşılık gelen indirme paketini seçin:

   * 64 bit istemciler için **VPN istemcisi (64 bit)** seçeneğini belirleyin.
   * 32 bit istemciler için **VPN istemcisi (32 bit)** seçeneğini belirleyin.

1. Azure, istemcinin gerektirdiği belirli ayarlarla bir paket oluşturur. VNet veya ağ geçidinde her değişiklik yaptığınızda, yeni bir istemci yapılandırma paketi indirip istemci bilgisayarlarınıza yüklemeniz gerekir.
1. Paket oluşturulduktan sonra **İndir**' i seçin.
1. İstemci yapılandırma paketini istemci bilgisayarınıza yükler. Yüklerken, Windows 'un BILGISAYARıNıZı koruduğu bir SmartScreen açılır penceresi görürseniz **daha fazla bilgi**' yi ve **yine de Çalıştır**' ı seçin. Paketi ayrıca diğer istemci bilgisayarlara yüklemek üzere kaydedebilirsiniz.

### <a name="install-a-client-certificate"></a>İstemci sertifikası yükler

Bu alıştırmada, istemci sertifikasını oluşturduğunuzda bilgisayarınıza otomatik olarak yüklenir. İstemci sertifikalarını oluşturmak için kullanılandan farklı bir istemci bilgisayardan bir P2S bağlantısı oluşturmak için, bu bilgisayara oluşturulan istemci sertifikasını yüklemelisiniz.

İstemci sertifikası yüklediğinizde, istemci sertifikası aktarıldığında oluşturulan parolanın olması gerekir. Genellikle, sertifikayı yalnızca çift tıklayarak yükleyebilirsiniz. Daha fazla bilgi için bkz. [bir içe aktarılmış istemci sertifikası yüklemeyi](vpn-gateway-certificates-point-to-site.md#install).

## <a name="connect-to-your-vnet"></a>Sanal ağınıza bağlanma

>[!NOTE]
>Bağlanmakta olduğunuz istemci bilgisayarında Yönetici haklarına sahip olmanız gerekir.
>

1. İstemci bilgisayarda VPN ayarları ' na gidin.
1. Oluşturduğunuz VPN 'yi seçin. Örnek ayarları kullandıysanız, bağlantı **Grup TestRG VNet1** olarak etiketlenir.
1. **Bağlan**’ı seçin.
1. Windows Azure sanal ağ kutusunda **Bağlan**' ı seçin. Sertifika hakkında bir açılır ileti görünürse, yükseltilmiş ayrıcalıkları kullanmak için **devam** ' ı ve yapılandırma değişikliklerini kabul etmek için **Evet** ' i seçin.
1. Bağlantınız başarılı olursa, **bağlı** bir bildirim görürsünüz.

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>VPN bağlantısını doğrulama

1. VPN bağlantınızın etkin olduğunu doğrulayın. İstemci bilgisayarınızda yükseltilmiş bir komut istemi açın ve **ipconfig/all** komutunu çalıştırın.
1. Sonuçlara bakın. Aldığınız IP adresinin, sanal ağınızı oluştururken belirlediğiniz Noktadan Siteye bağlantı adres aralığı içerisinden bir adres olduğuna dikkat edin. Sonuçlar şu örneğe benzer olmalıdır:

   ```
    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a>Sanal makineye bağlanma

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="to-add-or-remove-trusted-root-certificates"></a>Güvenilen kök sertifika ekleme veya kaldırma

Azure’da güvenilen kök sertifikayı ekleyebilir veya kaldırabilirsiniz. Bir kök sertifikayı kaldırdığınızda, o kökten oluşturulmuş bir sertifikaya sahip istemciler artık kimlik doğrulaması yapabilir ve bağlanamaz. Bu istemcilerin kimlik doğrulaması ve yeniden bağlanması için, Azure tarafından güvenilen bir kök sertifikadan oluşturulan yeni bir istemci sertifikası yüklemelisiniz.

### <a name="add-a-trusted-root-certificate"></a>Güvenilen kök sertifika ekleme

En fazla 20 güvenilen kök sertifika. cer dosyasını Azure 'a, ilk güvenilen kök sertifikayı eklemek için kullandığınız aynı işlemi kullanarak ekleyebilirsiniz.

### <a name="remove-a-trusted-root-certificate"></a>Güvenilen kök sertifikayı kaldırma

1. VNet 'iniz için sayfanın **Noktadan siteye bağlantılar** bölümünde **sertifikayı Yönet**' i seçin.
1. Kaldırmak istediğiniz sertifikanın yanındaki üç noktayı seçin ve **Sil**' i seçin.

## <a name="to-revoke-a-client-certificate"></a>İstemci sertifikasını iptal etme

Gerekirse, bir istemci sertifikasını iptal edebilirsiniz. Sertifika iptal listesi sayesinde, ayrı istemci sertifikalarına göre Noktadan Siteye bağlantıyı seçmeli olarak reddedebilirsiniz. Bu yöntem, güvenilen kök sertifikayı kaldırma işleminden farklıdır. Azure’dan güvenilen kök sertifika .cer dosyasını kaldırırsanız iptal edilen kök sertifika tarafından oluşturulan/imzalanan tüm istemci sertifikaları reddedilir. Kök sertifika yerine istemci sertifikasını iptal etmek, kök sertifikadan oluşturulan diğer sertifikaların Noktadan Siteye bağlantı için kimlik doğrulaması amacıyla kullanılmaya devam edilmesine olanak sağlar.

Genellikle ekip ve kuruluş düzeylerinde erişimi yönetmek için kök sertifika kullanılırken ayrı kullanıcılar üzerinde ayrıntılı erişim denetimi için iptal edilen istemci sertifikaları kullanılır.

Parmak izini iptal listesine ekleyerek bir istemci sertifikasını iptal edebilirsiniz.

1. İstemci sertifikasının parmak izini alın. Daha fazla bilgi için bkz. [Nasıl yapılır: Bir Sertifikanın Parmak İzini Alma](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate).
1. Bilgileri bir metin düzenleyicisine kopyalayın ve alanlarını sürekli bir dize olacak şekilde kaldırın.
1. **Noktadan sıteye VPN bağlantısı**' na gidin ve **sertifikayı Yönet**' i seçin.
1. İptal **listesi ' ni seçerek** **iptal listesi** sayfasını açın.
1. **Parmak izi**' nde, sertifika parmak izini boşluk olmadan sürekli bir metin satırı olarak yapıştırın.
1. Parmak izini sertifika iptal listesine (CRL) eklemek için **+ listeye ekle** ' yi seçin.

Güncelleştirme tamamlandıktan sonra sertifika artık bağlanmak için kullanılamaz. Bu sertifikayı kullanarak bağlanmaya çalışır olan istemciler, sertifikanın artık geçerli olmadığını belirten bir ileti alır.

## <a name="faq"></a><a name="faq"></a>SSS

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

* Bağlantınız tamamlandıktan sonra sanal ağlarınıza sanal makineler ekleyebilirsiniz. Daha fazla bilgi için bkz. [Sanal Makineler](../index.yml).

* Ağ ve Linux sanal makineleri hakkında daha fazla bilgi edinmek için bkz. [Azure ve LINUX VM ağına genel bakış](../virtual-machines/network-overview.md).

* P2S sorun giderme bilgileri için [Azure noktadan siteye bağlantıları sorununu giderme](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).