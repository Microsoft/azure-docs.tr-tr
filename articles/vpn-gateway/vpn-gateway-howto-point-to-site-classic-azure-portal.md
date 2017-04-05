---
title: "Noktadan Siteye bağlantısı kullanarak bir bilgisayarı Azure sanal ağına bağlama: Azure portal: klasik | Microsoft Docs"
description: "Azure portalı ile Noktadan Siteye bir VPN Gateway bağlantısı oluşturarak klasik Azure Sanal Ağınıza güvenli bir şekilde bağlanın."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: bc542cfbca3a27aec98b75e2b7ed05472419c3a7
ms.lasthandoff: 04/03/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal-classic"></a>Azure portalını kullanarak bir sanal ağa Noktadan Siteye bağlantı yapılandırma (klasik)
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Klasik - Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>
>

Noktadan Siteye (P2S) yapılandırması, ayrı bir istemci bilgisayardan bir sanal ağa yönelik güvenli bağlantı oluşturmanıza olanak sağlar. P2S, SSTP (Güvenli Yuva Tünel Protokolü) aracılığıyla gerçekleşen bir VPN bağlantısıdır. Sanal ağınıza uzak bir konumdan (örneğin, evden veya bir konferanstan) bağlanmak istediğinizde ya da sanal bir ağa bağlanması gereken yalnızca birkaç istemciniz bulunduğunda Noktadan Siteye bağlantıları kullanışlıdır. P2S bağlantılarının bir VPN cihazına veya genel kullanıma yönelik bir IP adresine gerek yoktur. VPN bağlantısını istemci bilgisayardan kurarsınız.

Bu makalede Azure portalı kullanılarak klasik dağıtım modelinde Noktadan Siteye bağlantı ile sanal ağ oluşturma işlemi adım adım açıklanmaktadır. Noktadan Siteye bağlantılar hakkında daha fazla bilgi edinmek için bu makalenin sonunda yer alan [Noktadan Siteye hakkında SSS](#faq) bölümünü inceleyin.


### <a name="deployment-models-and-methods-for-p2s-connections"></a>P2S bağlantıları için dağıtım modelleri ve yöntemleri
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Aşağıdaki tabloda, P2S yapılandırmaları için iki dağıtım modeli ve kullanılabilir dağıtım yöntemleri gösterilmektedir. Yapılandırma adımlarını içeren bir makale olduğunda, bu tablodan makaleye yönelik doğrudan bağlantı oluştururuz.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Temel iş akışı
![Noktadan Siteye diyagramı](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

Aşağıdaki bölümlerde, sanal ağa yönelik güvenli bir Noktadan Siteye bağlantı oluşturma işlemi adım adım açıklanmaktadır.

1. Sanal ağ ve VPN Gateway oluşturma
2. İstemci sertifikaları oluşturma
3. .cer dosyasını karşıya yükleme
4. VPN istemcisi yapılandırma paketini oluşturma
5. İstemci bilgisayarı yapılandırma
6. Azure'a Bağlanma

### <a name="example-settings"></a>Örnek ayarlar
Aşağıdaki örnek ayarları kullanabilirsiniz:

* **Ad: VNet1**
* **Adres alanı: 192.168.0.0/16**<br>Bu örnekte yalnızca bir adres alanı kullanılmaktadır. Sanal ağınıza ait birden fazla adres alanı olabilir.
* **Alt ağ adı: FrontEnd**
* **Alt ağ adres aralığı: 192.168.1.0/24**
* **Abonelik:** Birden fazla aboneliğiniz varsa doğru aboneliği kullandığınızdan emin olun.
* **Kaynak Grubu: TestRG**
* **Konum: Doğu ABD**
* **Bağlantı türü: Noktadan siteye**
* **İstemci Adres Alanı: 172.16.201.0/24**. Sanal ağa bu Noktadan Siteye bağlantıyı kullanarak bağlanan VPN istemcileri belirtilen havuzdan bir IP adresi alır.
* **GatewaySubnet: 192.168.200.0/24**. Ağ geçidi alt ağı 'GatewaySubnet' adını kullanmalıdır.
* **Boyut:** Kullanmak istediğiniz ağ geçidi SKU’sunu seçin.
* **Yönlendirme Türü: Dinamik**



## <a name="vnetvpn"></a>1. Bölüm - Sanal ağ ve VPN ağ geçidi oluşturma

Başlamadan önce, bir Azure aboneliğiniz olduğunu doğrulayın. Henüz Azure aboneliğiniz yoksa [MSDN abonelik avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial) için kaydolabilirsiniz.
### <a name="createvnet"></a>1. Kısım - Sanal ağ oluşturma
Sanal ağınız yoksa bir sanal ağ oluşturun. Ekran görüntüleri örnek olarak verilmiştir. Değerlerin kendinizinkilerle değiştirildiğinden emin olun. Azure portalını kullanarak sanal ağ oluşturmak için şu adımları uygulayın:

1. Tarayıcıdan [Azure portalına](http://portal.azure.com) gidin ve gerekiyorsa Azure hesabınızda oturum açın.
2. **Yeni**’ye tıklayın. **Markette ara** alanına 'Sanal Ağ' yazın. Döndürülen listeden **Sanal Ağ**’ı bulun ve tıklayarak **Sanal Ağ** dikey penceresini açın.

    ![Sanal ağ ara dikey penceresi](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvnetportal700.png)
3. Sanal Ağ dikey penceresinin altı yakınlarında, **Bir dağıtım modeli seçin** listesinden **Klasik**’i seçip **Oluştur**’a tıklayın.

    ![Dağıtım modeli seçme](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png)
4. **Sanal ağ oluştur** dikey penceresinde sanal ağ ayarlarını yapılandırın. Bu dikey pencerede, ilk adres alanınızı ve tek alt ağ adres aralığınızı eklersiniz. Sanal ağ oluşturma işlemini tamamladıktan sonra geri dönüp ek alt ağları ve adres alanlarını ekleyin.

    ![Sanal ağ oluştur dikey penceresi](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)
5. **Abonelik** alanında doğru bir giriş olduğunu doğrulayın. Açılan listeyi kullanarak abonelikleri değiştirebilirsiniz.
6. **Kaynak grubu**’na tıklayın, ya varolan bir kaynak grubunu seçin ya da yeni kaynak grubunuz için bir ad yazarak yeni bir tane oluşturun. Yeni bir grup oluşturuyorsanız, planlanan yapılandırma değerlerinize göre kaynak grubunu adlandırın. Kaynak grupları hakkında daha fazla bilgi için [Azure Resource Manager’a Genel Bakış](../azure-resource-manager/resource-group-overview.md#resource-groups)’ı ziyaret edin.
7. Ardından, sanal ağınız için **Konum** ayarlarını seçin. Bu Sanal ağa dağıttığınız kaynakların nerede olacağını konum belirler.
8. Sanal ağınızı panoda kolay bulmak istiyorsanız **Panoya sabitle**’yi seçin ve ardından **Oluştur**’a tıklayın.

    ![Panoya sabitle](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png)
9. Oluştur’a tıkladıktan sonra, panonuzda sanal ağınızın ilerleme durumunu yansıtacak bir kutucuk göreceksiniz. Sanal ağ oluşturulurken kutucuk değişir.

    ![Sanal ağ kutucuğu oluşturma](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png)
10. Sanal ağınızı oluşturduktan sonra ad çözünürlüğünü işlemek için bir DNS sunucusunun IP adresini ekleyebilirsiniz. Sanal ağınızın ayarlarını açın, DNS sunucularına tıklayın ve kullanmak istediğiniz DNS sunucusunun IP adresini ekleyin. Bu ayarla yeni bir DNS sunucusu oluşturulmaz. Kaynaklarınızın iletişim kurabileceği bir DNS sunucusu eklediğinizden emin olun.

Sanal ağınız oluşturulduğunda, klasik Azure portalının ağlar sayfasındaki **Durum** seçeneğinin altında **Oluşturuldu** ifadesinin yer aldığını görürsünüz.

### <a name="gateway"></a>2. Kısım: Ağ geçidi alt ağı ve dinamik yönlendirme ağ geçidi oluşturma
Bu adımda bir ağ geçidi alt ağı ve dinamik yönlendirme ağ geçidi oluşturacaksınız. Klasik dağıtım modeli için Azure portalında aynı ağ geçidi alt ağı ve ağ geçidi, aynı yapılandırma pencerelerinden oluşturulabilir.

1. Portalda, ağ geçidi oluşturmak istediğiniz sanal ağa gidin.
2. Sanal ağınızın dikey penceresindeki **Genel Bakış** dikey penceresinin VPN bağlantıları bölümünde **Ağ Geçidi**’ne tıklayın.

    ![Ağ geçidi oluşturmak için tıklayın](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. **Yeni VPN Bağlantısı** dikey penceresinde **Noktadan siteye** öğesini seçin.

    ![Noktadan Siteye bağlantı türü](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. **İstemci Adres Alanı** için IP adresi aralığını ekleyin. Bu aralık, VPN istemcilerinin bağlanırken bir IP adresi alacağı aralıktır. Otomatik olarak doldurulan aralığı silin ve kendi aralığınızı ekleyin.

    ![İstemci adres alanı](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. **Ağ geçidini hemen oluştur** onay kutusunu seçin.

    ![Ağ geçidini hemen oluşturma](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png)
6. **İsteğe bağlı ağ geçidi yapılandırması**’na tıklayarak **Ağ geçidi yapılandırması** dikey penceresini açın.

    ![İsteğe bağlı ağ geçidi yapılandırması'na tıklayın](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)
7. **Alt Ağ Yapılandırma zorunlu ayarları**’na tıklayarak **ağ geçidi alt ağı** ekleyin. /29 kadar küçük bir ağ geçidi alt ağı oluşturmak mümkün olsa da en az /28 veya /27’yi seçerek daha fazla adres içeren büyük bir alt ağ oluşturmanızı öneririz. Bu, gelecekte isteyebileceğiniz ek yapılandırmaları da içerecek yeteri kadar adres sağlayacaktır.

   > [!IMPORTANT]
   > Ağ geçidi alt ağlarıyla çalışırken, ağ güvenlik grubunu (NSG) ağ geçidi alt ağıyla ilişkilendirmekten kaçının. Ağ güvenlik grubunun bu alt ağ ile ilişkilendirilmesi, VPN Gateway’inizin beklendiği gibi çalışmayı durdurmasına neden olabilir. Ağ güvenlik grupları hakkında daha fazla bilgi için bkz. [Ağ güvenlik grubu nedir?](../virtual-network/virtual-networks-nsg.md)
   >
   >

    ![GatewaySubnet ekleme](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
8. Ağ geçidi **Boyutu** seçin. Bu seçenek, sanal ağ geçidinizi oluşturmak için kullanacağınız ağ geçidi SKU’sudur. Portalda Varsayılan SKU, **Temel**’dir. Ağ geçidi SKU’ları hakkında bilgi için bkz. [VPN Gateway Ayarları Hakkında](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

    ![ağ geçidi boyutu](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
9. Ağ geçidiniz için **Yönlendirme Türü** seçin. P2S yapılandırmaları bir **Dinamik** yönlendirme türü gerektirir. Bu dikey pencereyi yapılandırmayı bitirdiğinizde **Tamam**’a tıklayın.

    ![Yönlendirme türünü yapılandırma](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)
10. **Yeni VPN Bağlantısı** dikey penceresinde, sanal ağ geçidinizi oluşturmaya başlamak için dikey pencerenin en altındaki **Tamam**’a tıklayın. Bu işlemin tamamlanması 45 dakika sürebilir.

## <a name="generatecerts"></a>2. Bölüm - Sertifika oluşturma
Noktadan Siteye VPN’lerde VPN istemcilerinin kimlik doğrulamasını yapmak için Azure tarafından sertifikalar kullanılır. Kök sertifikayı oluşturduktan sonra, ortak sertifika verilerini (özel anahtarı değil) Base-64 olarak kodlanmış X.509 .cer dosyası olarak dışarı aktarırsınız. Ardından kök sertifikanın genel sertifika verilerini Azure’a yüklersiniz.

Noktadan Siteye bağlantı kullanarak bir sanal ağa bağlanan her istemci bilgisayarda bir istemci sertifikası yüklü olmalıdır. İstemci sertifikası kök sertifikadan oluşturulur ve her bir istemci bilgisayara yüklenir. Geçerli bir istemci sertifikası yüklü değilse ve istemci sanal ağa bağlanmaya çalışırsa, kimlik doğrulaması başarısız olur.

### <a name="cer"></a>1. Kısım: Kök sertifikaya ilişkin ortak anahtarı (.cer) alma

####<a name="enterprise-certificate"></a>Kurumsal sertifika
 
Bir kuruluş çözümü kullanıyorsanız var olan sertifika zincirinizi kullanabilirsiniz. Kullanmak istediğiniz kök sertifika için .cer dosyasını alın.

####<a name="self-signed-root-certificate"></a>Otomatik olarak imzalanan kök sertifika

Kurumsal bir sertifika çözümü kullanmıyorsanız, otomatik olarak imzalanan bir sertifika oluşturmanız gerekir. P2S kimlik doğrulaması için gerekli alanları içeren bir otomatik olarak imzalanan sertifika oluşturmak için PowerShell kullanabilirsiniz. [PowerShell kullanarak Noktadan Siteye bağlantıları için otomatik olarak imzalanan bir sertifika oluşturma](vpn-gateway-certificates-point-to-site.md) bölümü, otomatik olarak imzalanan bir kök sertifika oluşturma konusunda size adım adım yol gösterir.

> [!NOTE]
> Daha önce, otomatik olarak imzalanan kök sertifikalar oluşturmak ve Noktadan Siteye bağlantılar için istemci sertifikaları oluşturmak için makecert metodu öneriliyordu. Artık bu sertifikaları oluşturmak için PowerShell kullanabilirsiniz. PowerShell kullanmanın bir avantajı SHA-2 sertifikaları oluşturma özelliğidir. Gerekli değerler için bkz. [PowerShell kullanarak Noktadan Siteye bağlantıları için otomatik olarak imzalanan bir sertifika oluşturma](vpn-gateway-certificates-point-to-site.md).
>
>

#### <a name="to-export-the-public-key-for-a-self-signed-root-certificate"></a>Otomatik olarak imzalanan bir kök sertifika için ortak anahtarı dışarı aktarmak için

Noktadan Siteye bağlantılar, ortak anahtarın (.cer) Azure’a yüklenmesini gerektirir. Aşağıdaki adımlar otomatik olarak imzalanan kök sertifikanız için .cer dosyasını dışarı aktarmanıza yardımcı olur.

1. Sertifikadan bir .cer dosyası almak için, **certmgr.msc** öğesini açın. Otomatik olarak imzalanan kök sertifikayı bulun (genellikle 'Certificates - Current User\Personal\Certificates' konumundadır) ve sağ tıklayın. **Tüm Görevler**’e tıklayın ve ardından **Dışarı Aktar**’a tıklayın. **Sertifika Dışarı Aktarma Sihirbazı** açılır.
2. Sihirbazda, **İleri**’ye tıklayın. **Hayır, özel anahtarı dışarı aktarma**’yı seçin ve **İleri**’ye tıklayın.
3. **Dışarı Aktarma Dosyası Biçimi** sayfasında **Base-64 ile kodlanmış X.509 (.CER)** seçeneğini belirleyin ve **İleri**’ye tıklayın. 
4. **Dışarı Aktarılan Dosya** sayfasında **Gözat**'a tıklayarak sertifika için dışarı aktarma konumunu seçin. **Dosya adı** alanına, sertifika dosyası için bir ad girin. Ardından **İleri**'ye tıklayın.
5. Sertifikayı dışarı aktarmak için **Son**'a tıklayın. **Dışarı aktarma başarılı** ifadesini görürsünüz. **Tamam**’a tıklayarak sihirbazı kapatın.

### <a name="genclientcert"></a>2. Kısım: İstemci sertifikası oluşturma

Bağlanacak her istemci için benzersiz bir sertifika oluşturabileceğiniz gibi, birden çok istemcide aynı sertifikayı da kullanabilirsiniz. Benzersiz istemci sertifikaları oluşturmanın avantajı, gerektiğinde tek bir sertifikayı iptal edebiliyor olmanızdır. Herkesin aynı istemci sertifikasını kullandığı bir durumda bir istemcinin sertifikasını iptal etmeniz gerektiğinde, kimlik doğrulaması için söz konusu sertifikayı kullanan tüm istemciler için yeni sertifikalar oluşturmanız ve yüklemeniz gerekir.

####<a name="enterprise-certificate"></a>Kurumsal sertifika
- Kurumsal bir sertifika çözümü kullanıyorsanız, 'etkialaniadi\kullaniciadi' biçimini kullanmak yerine, yaygın olarak kullanılan 'name@yourdomain.com' ad değer biçimiyle bir istemci sertifikası oluşturun.
- Verdiğiniz istemci sertifikasının, kullanım listesindeki ilk öğe olarak Akıllı Kart Oturumu, vb. yerine ‘İstemci Kimlik Doğrulaması’na sahip ‘Kullanıcı’ sertifikası şablonunu temel alarak hazırlandığından emin olun. İstemci sertifikasına sağ tıklayıp **Ayrıntılar > Gelişmiş Anahtar Kullanımı**’nı görüntüleyerek sertifikayı denetleyebilirsiniz.

####<a name="self-signed-root-certificate"></a>Otomatik olarak imzalanan kök sertifika 
Otomatik olarak imzalanan bir kök sertifika kullanıyorsanız, Noktadan Siteye bağlantılar ile uyumlu bir istemci sertifikası oluşturmak için [PowerShell kullanarak istemci sertifikası oluşturma](vpn-gateway-certificates-point-to-site.md#clientcert) bölümüne bakın.

### <a name="exportclientcert"></a>3. Kısım - İstemci sertifikasını dışarı aktarma
[PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert) yönergelerini kullanarak bir otomatik olarak imzalanan kök sertifikadan bir istemci sertifikası oluşturursanız sertifika, sertifikayı oluşturmak için kullandığınız bilgisayara otomatik olarak yüklenir. İstemci sertifikasını başka bir istemci bilgisayara yüklemek istiyorsanız, sertifikayı dışarı aktarmanız gerekir.

1. İstemci sertifikasını dışarı aktarmak için, **certmgr.msc** öğesini açın. Dışarı aktarmak istediğiniz istemci sertifikasına sağ tıklayın, **tüm görevler**’e ve ardından **dışarı aktar**’a tıklayın. **Sertifika Dışarı Aktarma Sihirbazı** açılır.
2. Sihirbazda **İleri**’ye tıklayın, **Evet, özel anahtarı dışarı aktar**’ı seçin ve **İleri**’ye tıklayın.
3. **Dışarı Aktarma Dosyası Biçimi** sayfasında, varsayılan ayarları seçili bırakın. **Mümkünse sertifika yolundaki tüm sertifikaları ekle** seçeneğinin işaretli olduğundan emin olun. Ardından **İleri**'ye tıklayın.
4. **Güvenlik** sayfasında, özel anahtarı korumanız gerekir. Bir parola kullanmayı seçerseniz, bu sertifika için ayarladığınız parolayı kaydettiğinizden ya da unutmayacağınızdan emin olun. Ardından **İleri**'ye tıklayın.
5. **Dışarı Aktarılan Dosya** sayfasında **Gözat**'a tıklayarak sertifika için dışarı aktarma konumunu seçin. **Dosya adı** alanına, sertifika dosyası için bir ad girin. Ardından **İleri**'ye tıklayın.
6. Sertifikayı dışarı aktarmak için **Son**'a tıklayın.

## <a name="upload"></a>3. Bölüm - Kök sertifika .cer dosyasını karşıya yükleme
Ağ geçidi oluşturulduktan sonra güvenilen bir kök sertifika için .cer dosyasını Azure’a yükleyebilirsiniz. 20 adede kadar kök sertifikasının dosyasını yükleyebilirsiniz. Kök sertifikanın özel anahtarını Azure'a yüklemezsiniz. .cer dosyası yüklendikten sonra Azure, sanal ağa bağlanan istemcilerin kimliğini doğrulamak için bu anahtarı kullanır.

1. Sanal ağınıza ait dikey pencerenin **VPN bağlantıları** bölümünde **istemciler** grafiğine tıklayarak **Noktadan siteye VPN bağlantısı** dikey penceresini açın.

    ![İstemciler](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. **Noktadan siteye bağlantı** dikey penceresinde **Sertifikaları yönet**’e tıklayarak **Sertifikalar** dikey penceresini açın.<br>

    ![Sertifikalar dikey penceresi](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. **Sertifikalar** dikey penceresinde **Karşıya Yükle**’ye tıklayarak **Sertifikayı karşıya yükle** dikey penceresini açın.<br>

    ![Sertifikaları karşıya yükle dikey penceresi](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)<br>
4. .cer dosyasına göz atmak için klasör grafiğine tıklayın. Dosyayı seçin ve ardından **Tamam**’a tıklayın. **Sertifikalar** dikey penceresine yüklenen sertifikayı görmek için sayfayı yenileyin.

    ![Sertifikayı karşıya yükleme](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)<br>

## <a name="vpnclientconfig"></a>Bölüm 4 - VPN istemcisi yapılandırma paketini oluşturma
Sanal ağa bağlanmak için bir VPN istemcisi de yapılandırmanız gerekir. İstemci bilgisayarın bağlanması için bir istemci sertifikasına ve uygun VPN istemcisi yapılandırma paketine gerek vardır.

VPN istemci paketi, Windows'da yerleşik bulunan VPN istemci yazılımını yapılandırmaya yönelik yapılandırma bilgileri içerir. Paket, ek yazılım yüklemez. Ayarlar bağlanmak istediğiniz sanal ağa özeldir. Desteklenen istemci işletim sistemlerinin listesi için bu makalenin sonundaki [Noktadan Siteye bağlantılar hakkında SSS](#faq) bölümüne bakın.

### <a name="to-generate-the-vpn-client-configuration-package"></a>VPN istemcisi yapılandırma paketini oluşturmak için
1. Azure portalında, sanal ağınızın **Genel Bakış** dikey penceresindeki **VPN bağlantıları** menüsünde istemci grafiğine tıklayarak **Noktadan siteye VPN bağlantısı** dikey penceresini açın.
2. **Noktadan siteye VPN bağlantısı** dikey penceresinin üst kısmında, yükleneceği istemci işletim sistemine karşılık gelen indirme paketini seçin:

   * 64 bit istemciler için **VPN İstemcisi (64 bit)** seçeneğini belirleyin.
   * 32 bit istemciler için **VPN İstemcisi (32 bit)** seçeneğini belirleyin.

     ![VPN istemcisi yapılandırma paketini indirme](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)<br>
3. Azure’un sanal ağ için VPN istemcisi yapılandırma paketini oluşturduğuna ilişkin bir ileti görürsünüz. Birkaç dakika sonra paket oluşturulur ve yerel bilgisayarınızda paketin indirildiğine yönelik bir ileti görürsünüz. Yapılandırma paketi dosyasını kaydedin. Bu dosyayı, P2S kullanarak sanal ağa bağlanacak her istemci bilgisayara yükleyeceksiniz.

## <a name="clientconfiguration"></a>5. Bölüm - İstemci bilgisayarı yapılandırma
### <a name="part-1-install-an-exported-client-certificate"></a>1. Kısım: Dışarı aktarılan bir istemci sertifikasını yükleme

İstemci sertifikalarını oluşturmak için kullandığınız bilgisayardan farklı bir istemci bilgisayarda bir P2S bağlantı oluşturmak istiyorsanız, bir istemci sertifikası yüklemeniz gerekir. Bir istemci sertifikası yüklenirken, istemci sertifikası dışarı aktarılırken oluşturulan parola gerekir.

1. *.pfx* dosyasını bulun ve istemci bilgisayara kopyalayın. İstemci bilgisayarda *.pfx* dosyasına çift tıklayarak yükleme işlemini gerçekleştirin. **Depolama Konumu**’nu **Geçerli Kullanıcı** olarak bırakın ve **İleri**’ye tıklayın.
2. İçeri aktarılacak **Dosya** sayfasında herhangi bir değişiklik yapmayın. **İleri**'ye tıklayın.
3. **Özel anahtar koruma** sayfasında, bir sertifika kullandıysanız sertifika için parolayı girin veya sertifikayı yükleyen güvenlik sorumlusunun doğru olduğunu onaylayın ve **İleri**’ye tıklayın.
4. **Sertifika Deposu** sayfasında, varsayılan konumu bırakın ve **İleri**’ye tıklayın.
5. **Son**'a tıklayın. Sertifika yüklemesi için **Güvenlik Uyarısı**’nda, **Evet**’e tıklayın. Sertifikayı siz oluşturduğunuz için güvenle ‘Evet’ seçeneğine tıklayabilirsiniz. Sertifika başarıyla içeri aktarılır.

### <a name="part-2-install-the-vpn-client-configuration-package"></a>2. Kısım: VPN istemcisi yapılandırma paketini yükleme
Sürümünün istemci mimarisiyle eşleşmesi şartıyla, her istemci bilgisayarda aynı VPN istemcisi yapılandırma paketini kullanabilirsiniz.

1. Yapılandırma dosyasını, sanal ağınıza bağlamak istediğiniz bilgisayara yerel olarak kopyalayın. 
2. Paketi istemci bilgisayara yüklemek için .exe dosyasına çift tıklayın. Yapılandırma paketi sizin tarafınızdan oluşturulduğu için imzalanmamıştır. Bu, bir uyarı görebileceğiniz anlamına gelir. Bir Windows SmartScreen açılır penceresi görürseniz **Daha fazla bilgi**’ye (solda) ve ardından **Yine de çalıştır**’a tıklayarak paketi yükleyin.
3. İstemci bilgisayarda **Ağ Ayarları**’na gidin ve **VPN** öğesine tıklayın. Bağlantının listelendiğini görürsünüz. Bu listede bağlantı kurulacak sanal ağın adı gösterilir ve liste aşağıdaki gibi görünür:

    ![VPN istemcisi](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vpn.png)

## <a name="connect"></a>6. Bölüm - Azure'a bağlanma
### <a name="connect-to-your-vnet"></a>Sanal ağınıza bağlanma
1. İstemci bilgisayarda sanal ağınıza bağlanmak için VPN bağlantılarında gezinin ve oluşturduğunuz VPN bağlantısını bulun. Bu VPN bağlantısı sanal ağınızla aynı ada sahiptir. **Bağlan**'a tıklayın. Sertifika kullanımına ilişkin bir açılır ileti görüntülenebilir. Böyle bir durumla karşılaşırsanız yükseltilmiş ayrıcalıkları kullanmak için **Devam**'a tıklayın.
2. **Bağlantı** durum sayfasında **Bağlan**'a tıklayarak bağlantıyı başlatın. Bir **Sertifika Seç** ekranı çıkarsa, gösterilen istemci sertifikasının bağlanmak için kullanmak istediğiniz sertifika olduğunu doğrulayın. Başka bir sertifika gösteriliyorsa, açılan liste okunu kullanarak doğru sertifikayı seçin ve **Tamam**’a tıklayın.

    ![VPN istemci bağlantısı](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png)
3. Artık bağlantı kurabilirsiniz.

    ![Kurulan bağlantı](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png)

> [!NOTE]
> Bir Kuruluş Sertifika Yetkilisi çözümü kullanarak verilen bir sertifika kullanıyor ve kimlik doğrulama sorunu yaşıyorsanız, istemci sertifikasındaki kimlik doğrulama sırasını denetleyin. Kimlik doğrulama listesinin sırasını istemci sertifikasına çift tıklayıp **Ayrıntılar > Gelişmiş Anahtar Kullanımı**’na giderek denetleyebilirsiniz. Listede ilk öğe olarak ‘İstemci Kimlik Doğrulaması’nın göründüğünden emin olun. Aksi takdirde, listedeki ilk öğe olarak İstemci Kimlik Doğrulaması’nı içeren Kullanıcı şablonunu temel alarak oluşturulmuş bir istemci sertifikası vermeniz gerekir. 
>
>

### <a name="verify-the-vpn-connection"></a>VPN bağlantısını doğrulama
1. VPN bağlantınızın etkin olduğunu doğrulamak için, yükseltilmiş bir komut istemi açın ve *ipconfig/all* komutunu çalıştırın.
2. Sonuçlara bakın. Aldığınız IP adresinin, sanal ağınızı oluştururken belirlediğiniz Noktadan Siteye bağlantı adres aralığı içerisinden bir adres olduğuna dikkat edin. Sonuçlar aşağıdakine benzer olmalıdır:

Örnek:

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

## <a name="add"></a>Güvenilen kök sertifika ekleme veya kaldırma

Azure’da güvenilen kök sertifikayı ekleyebilir veya kaldırabilirsiniz. Güvenilen kök sertifikayı kaldırdığınızda, kök sertifikadan oluşturulan istemci sertifikaları artık Noktadan Siteye bağlantı aracılığıyla Azure'a bağlanamaz. İstemciler, bağlanabilmek için Azure'da güvenilen bir sertifikadan oluşturan yeni bir istemci sertifikası yüklemelidir.

### <a name="to-add-a-trusted-root-certificate"></a>Güvenilen kök sertifika ekleme

Azure'a en fazla 20 güvenilen kök sertifika .cer dosyası ekleyebilirsiniz. Yönergeler için bkz. [3. Bölüm - Kök sertifika .cer dosyasını karşıya yükleme](#upload).

### <a name="to-remove-a-trusted-root-certificate"></a>Güvenilen kök sertifikayı kaldırmak için


1. Sanal ağınıza ait dikey pencerenin **VPN bağlantıları** bölümünde **istemciler** grafiğine tıklayarak **Noktadan siteye VPN bağlantısı** dikey penceresini açın.

    ![İstemciler](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. **Noktadan siteye bağlantı** dikey penceresinde **Sertifikaları yönet**’e tıklayarak **Sertifikalar** dikey penceresini açın.<br>

    ![Sertifikalar dikey penceresi](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. **Sertifikalar** dikey penceresinde, kaldırmak istediğiniz sertifikanın yanındaki üç noktaya tıklayın ve ardından **Sil**’e tıklayın.

     ![Kök sertifikayı sil](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)<br>


## <a name="revokeclient"></a>İstemci sertifikasını iptal etme
İstemci sertifikalarını iptal edebilirsiniz. Sertifika iptal listesi sayesinde, ayrı istemci sertifikalarına göre Noktadan Siteye bağlantıyı seçmeli olarak reddedebilirsiniz. Bu, güvenilen kök sertifikayı kaldırma işleminden farklıdır. Azure’dan güvenilen kök sertifika .cer dosyasını kaldırırsanız iptal edilen kök sertifika tarafından oluşturulan/imzalanan tüm istemci sertifikaları reddedilir. Kök sertifika yerine istemci sertifikasını iptal etmek, kök sertifikadan oluşturulan diğer sertifikaların Noktadan Siteye bağlantı için kimlik doğrulaması amacıyla kullanılmaya devam edilmesine olanak sağlar.

Genellikle ekip ve kuruluş düzeylerinde erişimi yönetmek için kök sertifika kullanılırken ayrı kullanıcılar üzerinde ayrıntılı erişim denetimi için iptal edilen istemci sertifikaları kullanılır.

### <a name="to-revoke-a-client-certificate"></a>İstemci sertifikasını iptal etme

Parmak izini iptal listesine ekleyerek bir istemci sertifikasını iptal edebilirsiniz.

1. İstemci sertifikasının parmak izini alın. Daha fazla bilgi için bkz. [Nasıl yapılır: Bir Sertifikanın Parmak İzini Alma](https://msdn.microsoft.com/library/ms734695.aspx).
2. Bilgileri bir metin düzenleyicisine kopyalayın ve sürekli bir dize haline getirmek için tüm boşlukları kaldırın.
3. **'klasik sanal ağ adı' > Noktadan siteye VPN bağlantısı > Sertifikalar** dikey penceresine gidin ve ardından İptal listesi dikey penceresini açmak için **İptal listesi**’ne tıklayın. 
4. **İptal listesi** dikey penceresinde, **İptal listesine sertifika ekle** dikey penceresini açmak için **+Sertifika ekle**’ye tıklayın.
5. **İptal listesine sertifika ekle** dikey penceresinde, sertifika parmak izini boşluk içermeyen sürekli bir metin satırı olarak yapıştırın. Dikey pencerenin alt kısmındaki **Tamam**’a tıklayın.
6. Güncelleştirme tamamlandıktan sonra sertifika artık bağlanmak için kullanılamaz. Bu sertifikayı kullanarak bağlanmaya çalışan istemciler sertifikanın artık geçerli olmadığını belirten bir ileti alır.


## <a name="faq"></a>Noktadan Siteye hakkında SSS

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Sonraki adımlar
Bağlantınız tamamlandıktan sonra sanal ağlarınıza sanal makineler ekleyebilirsiniz. Daha fazla bilgi için bkz. [Sanal Makineler](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Ağ ve sanal makineler hakkında daha fazla bilgi edinmek için, bkz. [Azure ve Linux VM ağına genel bakış](../virtual-machines/linux/azure-vm-network-overview.md).

