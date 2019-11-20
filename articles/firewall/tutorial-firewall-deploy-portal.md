---
title: "Öğretici: Azure portalı kullanarak Azure Güvenlik Duvarı'nı dağıtma ve yapılandırma"
description: Bu öğreticide Azure portalı kullanarak Azure Güvenlik Duvarı'nı dağıtmayı ve yapılandırmayı öğreneceksiniz.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 10/28/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 124a87728a8d201c329b15d94ae7e61a225646ab
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468456"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Öğretici: Azure portalı kullanarak Azure Güvenlik Duvarı'nı dağıtma ve yapılandırma

Giden ağ erişimini denetleme, genel ağ güvenlik planının önemli bir parçasıdır. Örneğin, erişimi Web sitelerine kısıtlamak isteyebilirsiniz. Ya da, erişilebilen giden IP adreslerini ve bağlantı noktalarını sınırlamak isteyebilirsiniz.

Azure Güvenlik Duvarı, Azure alt ağından giden ağ erişimini denetlemenin bir yoludur. Azure Güvenlik Duvarı ile şunları yapılandırabilirsiniz:

* Bir alt ağdan erişilebilen tam etki alanı adlarını (FQDN) tanımlayan uygulama kuralları.
* Kaynak adres, protokol, hedef bağlantı noktası ve hedef adresini tanımlayan ağ kuralları.

Ağ trafiğinizi güvenlik duvarından alt ağın varsayılan ağ geçidi olarak yönlendirdiğinizde ağ trafiği yapılandırılan güvenlik duvarı kurallarına tabi tutulur.

Bu öğreticide kolay dağıtım için üç alt ağa sahip tek bir basitleştirilmiş sanal ağ oluşturursunuz. Üretim dağıtımları için, güvenlik duvarının kendi VNet 'inde bulunduğu bir [hub ve bağlı bileşen modeli](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) önerilir. İş yükü sunucuları, bir veya daha fazla alt ağ ile aynı bölgedeki eşlenmiş VNET 'lerde bulunur.

* **AzureFirewallSubnet** - güvenlik duvarı bu alt ağdadır.
* **Workload-SN**: İş yükü sunucusu bu alt ağda yer alır. Bu alt ağın ağ trafiği güvenlik duvarından geçer.
* **Jump-SN**: "Atlama" sunucusu bu alt ağda yer alır. Atlama sunucusu, Uzak Masaüstü ile bağlanabileceğiniz genel IP adresine sahiptir. Buradan iş yükü sunucusuna bağlanabilirsiniz (başka bir Uzak Masaüstü oturumuyla).

![Öğretici ağı altyapısı](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Test amaçlı ağ ortamı oluşturma
> * Güvenlik duvarı dağıtma
> * Varsayılan rota oluşturma
> * Www.google.com erişimine izin vermek için bir uygulama kuralı yapılandırma
> * Dış DNS sunucularına erişime izin vermek için ağ kuralı yapılandırma
> * Güvenlik duvarını test etme

Tercih ederseniz, bu öğreticiyi [Azure PowerShell](deploy-ps.md) kullanarak tamamlayabilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="set-up-the-network"></a>Ağı ayarlama

İlk olarak güvenlik duvarını dağıtmak için gerekli olan kaynakları içerecek bir kaynak grubu oluşturun. Ardından sanal ağı, alt ağları ve test sunucularını oluşturun.

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Kaynak grubu, bu öğreticideki tüm kaynakları içerir.

1. [https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.
2. Azure portal menüsünde, **kaynak grupları** ' nı seçin veya herhangi bir sayfadan *kaynak gruplarını* arayıp seçin. Ardından **Ekle**'yi seçin.
3. **Kaynak grubu adı**için *Test-FW-RG*yazın.
4. **Abonelik** bölümünde aboneliğinizi seçin.
5. **Kaynak grubu konumu** bölümünde bir konum seçin. Bundan sonra oluşturacağınız tüm kaynakların aynı konumda olması gerekir.
6. **Oluştur**'u seçin.

### <a name="create-a-vnet"></a>Sanal ağ oluşturma

Bu sanal ağda üç alt ağ bulunacaktır.

> [!NOTE]
> AzureFirewallSubnet alt ağının boyutu/26 ' dır. Alt ağ boyutu hakkında daha fazla bilgi için bkz. [Azure Güvenlik DUVARı SSS](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

1. Azure portal menüsünde veya **giriş** sayfasında, **kaynak oluştur**' u seçin.
1. **Sanal ağ** > **ağ oluşturma** ' yı seçin.
1. **Ad** alanına **Test-FW-VN** yazın.
1. **Adres alanı** için **10.0.0.0/16** yazın.
1. **Abonelik** bölümünde aboneliğinizi seçin.
1. **Kaynak grubu**için **Test-FW-RG**öğesini seçin.
1. **Konum** alanında önceden kullandığınız konumu seçin.
1. **Alt ağ** bölümünde **Ad** alanına **AzureFirewallSubnet** yazın. Güvenlik duvarı bu alt ağda yer alacaktır ve alt ağ adının **mutlaka** AzureFirewallSubnet olması gerekir.
1. **Adres aralığı**için **10.0.1.0/26**yazın.
1. Diğer varsayılan ayarları kabul edin ve **Oluştur**' u seçin.

### <a name="create-additional-subnets"></a>Ek alt ağ oluşturma

Bir sonraki adımda atlama sunucusu için alt ağlar ve iş yükü sunucuları için bir alt ağ oluşturun.

1. Azure portal menüsünde, **kaynak grupları** ' nı seçin veya herhangi bir sayfadan *kaynak gruplarını* arayıp seçin. Ardından **Test-FW-RG**öğesini seçin.
2. **Test-FW-VN** sanal ağını seçin.
3. **Alt ağları** > alt **ağ**' ı seçin.
4. **Ad** alanına **Workload-SN** yazın.
5. **Adres aralığı** için **10.0.2.0/24** yazın.
6. **Tamam**’ı seçin.

**Jump-SN** adına ve **10.0.3.0/24** adres aralığına sahip bir alt ağ daha oluşturun.

### <a name="create-virtual-machines"></a>Sanal makineler oluşturma

Şimdi atlama ve iş yükü sanal makinelerini oluşturup uygun alt ağlara yerleştirin.

1. Azure portal menüsünde veya **giriş** sayfasında, **kaynak oluştur**' u seçin.
2. **İşlem**’i ve sonra Öne Çıkanlar listesinde **Windows Server 2016 Datacenter**’ı seçin.
3. Sanal makine için şu değerleri girin:

   |Ayar  |Değer  |
   |---------|---------|
   |Kaynak grubu     |**Test-ILT-RG**|
   |Sanal makine adı     |**SRV-zıplama**|
   |Bölge     |Öncekiyle aynı|
   |Yönetici Kullanıcı adı     |**azureuser**|
   |Parola     |**Azure123456!**|

4. **Gelen bağlantı noktası kuralları**altında, **Genel gelen bağlantı noktaları**Için **Seçili bağlantı noktalarına izin ver**' i seçin
5. **Gelen bağlantı noktaları Seç**için **RDP (3389)** seçeneğini belirleyin.

6. Diğer varsayılanları kabul edin ve **İleri ' yi seçin: diskler**.
7. Disk varsayılanlarını kabul edin ve **İleri ' yi seçin: ağ**.
8. Sanal ağ için **Test-FW-VN** ' nin seçildiğinden ve alt AĞıN **geç sn**olduğundan emin olun.
9. **Genel IP**için, varsayılan yeni genel IP adresi adını (SRV-atıp) kabul edin.
11. Diğer varsayılanları kabul edin ve **İleri: yönetim**' i seçin.
12. Önyükleme tanılamayı devre dışı bırakmak için **Kapat** ' ı seçin. Diğer varsayılanları kabul edin ve **gözden geçir + oluştur**' u seçin.
13. Özet sayfasında ayarları gözden geçirin ve ardından **Oluştur**' u seçin.

**SRV-Work**adlı başka bir sanal makineyi yapılandırmak için aşağıdaki tablodaki bilgileri kullanın. Yapılandırmanın geri kalanı Srv-Jump sanal makinesiyle aynıdır.

|Ayar  |Değer  |
|---------|---------|
|Alt ağ|**İş yükü-SN**|
|Genel IP|**Seçim**|
|Ortak gelen bağlantı noktaları|**Seçim**|

## <a name="deploy-the-firewall"></a>Güvenlik duvarını dağıtma

Güvenlik duvarını sanal ağa dağıtın.

1. Azure portal menüsünde veya **giriş** sayfasında, **kaynak oluştur**' u seçin.
2. Arama kutusuna **güvenlik duvarı** yazın ve **ENTER**tuşuna basın.
3. **Güvenlik duvarı** ' nı seçip **Oluştur**' u seçin.
4. **Güvenlik duvarı oluştur** sayfasında aşağıdaki ayarları kullanarak güvenlik duvarını yapılandırın:

   |Ayar  |Değer  |
   |---------|---------|
   |Abonelik     |\<aboneliğiniz\>|
   |Kaynak grubu     |**Test-ILT-RG** |
   |Ad     |**Test-FW01**|
   |Konum     |Önceden kullandığınız konumu seçin|
   |Bir sanal ağ seçin     |**Mevcut olanı kullan**: **Test-FW-vn**|
   |Genel IP adresi     |**Yeni oluşturun**. Genel IP adresinin türü Standart SKU olmalıdır.|

5. **İncele ve oluştur**’u seçin.
6. Özeti gözden geçirin ve ardından güvenlik duvarını oluşturmak için **Oluştur** ' u seçin.

   Dağıtma işlemi birkaç dakika sürebilir.
7. Dağıtım tamamlandıktan sonra, **Test-FW-RG** kaynak grubuna gidin ve **Test-FW01** güvenlik duvarını seçin.
8. Özel IP adresini not edin. Varsayılan rotayı oluştururken bu adresi kullanacaksınız.

## <a name="create-a-default-route"></a>Varsayılan rota oluşturma

**Workload-SN** alt ağında varsayılan giden rotayı güvenlik duvarından geçecek şekilde yapılandırın.

1. Azure portal menüsünde **tüm hizmetler** ' i seçin veya herhangi bir sayfadan *tüm hizmetler* ' i arayın ve seçin.
2. **Ağ**altında, **Rota tabloları**' nı seçin.
3. **Add (Ekle)** seçeneğini belirleyin.
4. **Ad** alanına **Firewall-route** yazın.
5. **Abonelik** bölümünde aboneliğinizi seçin.
6. **Kaynak grubu**için **Test-FW-RG**öğesini seçin.
7. **Konum** alanında önceden kullandığınız konumu seçin.
8. **Oluştur**'u seçin.
9. **Yenile**' yi seçin ve ardından **güvenlik duvarı-rota** yol tablosunu seçin.
10. **Alt ağları** seçip **ilişkilendir**' i seçin.
11. **Test-FW-VN** > **sanal ağı** seçin.
12. **Alt ağ**Için **Iş yükü-sn**' ı seçin. Bu rota için yalnızca **Iş yükü-sn** alt ağını seçtiğinizden emin olun, aksi takdirde güvenlik duvarınız doğru çalışmaz.

13. **Tamam**’ı seçin.
14. **Rotalar** ' ı seçin ve ardından **Ekle**' yi seçin.
15. **Rota adı**için, **FW-DG**yazın.
16. **Adres ön eki** alanına **0.0.0.0/0** yazın.
17. **Sonraki atlama türü** için **Sanal gereç**'i seçin.

    Azure Güvenlik Duvarı, normalde yönetilen bir hizmettir ancak bu durumda sanal gereç kullanılabilir.
18. **Sonraki atlama adresi** alanına önceden not ettiğiniz güvenlik duvarı özel IP adresini yazın.
19. **Tamam**’ı seçin.

## <a name="configure-an-application-rule"></a>Uygulama kuralı yapılandırma

Bu, www.google.com 'e giden erişime izin veren uygulama kuralıdır.

1. **Test-FW-RG**öğesini açın ve **Test-FW01** güvenlik duvarını seçin.
2. **Test-FW01** sayfasında, **Ayarlar**' ın altında **kurallar**' ı seçin.
3. **Uygulama kuralı koleksiyonu** sekmesini seçin.
4. **Uygulama kuralı koleksiyonu Ekle**' yi seçin.
5. **Ad** alanına **App-Coll01** yazın.
6. **Öncelik** alanına **200** yazın.
7. **Eylem** alanında **İzin ver**'i seçin.
8. **Kurallar**altında, **ad**için **hedef FQDN 'ler**yazın, **izin ver-Google**yazın.
9. **Kaynak Adresler** alanına **10.0.2.0/24** yazın.
10. **Protokol:bağlantı noktası** alanına **http, https** yazın.
11. **Hedef FQDN 'ler**için **www.Google.com** yazın
12. **Add (Ekle)** seçeneğini belirleyin.

Azure Güvenlik Duvarı'nda varsayılan olarak izin verilen altyapı FQDN'leri için yerleşik bir kural koleksiyonu bulunur. Bu FQDN'ler platforma özgüdür ve başka amaçlarla kullanılamaz. Daha fazla bilgi için bkz. [Altyapı FQDN'leri](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Ağ kuralını yapılandırma

Bu, bağlantı noktası 53’deki (DNS) iki IP adresine giden erişime izin veren ağ kuralıdır.

1. **Ağ kuralı koleksiyonu** sekmesini seçin.
2. **Ağ kuralı koleksiyonu Ekle**' yi seçin.
3. **Ad** alanına **Net-Coll01** yazın.
4. **Öncelik** alanına **200** yazın.
5. **Eylem** alanında **İzin ver**'i seçin.
6. **Kurallar**' ın altında, **ad**IÇIN **izin ver-DNS**yazın.
7. **Protokol** alanında **UDP**'yi seçin.
8. **Kaynak Adresler** alanına **10.0.2.0/24** yazın.
9. Hedef adres için **209.244.0.3,209.244.0.4** yazın.

   Bunlar, CenturyLink tarafından çalıştırılan genel DNS sunucularıdır.
1. **Hedef Bağlantı Noktaları** için **53** yazın.
2. **Add (Ekle)** seçeneğini belirleyin.

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>**Srv-Work** ağ arabiriminin birincil ve ikincil DNS adresini değiştirme

Bu öğreticide sınama amacıyla sunucunun birincil ve ikincil DNS adreslerini yapılandırın. Bu genel bir Azure Güvenlik Duvarı gereksinimi değildir.

1. Azure portal menüsünde, **kaynak grupları** ' nı seçin veya herhangi bir sayfadan *kaynak gruplarını* arayıp seçin. **Test-FW-RG** kaynak grubunu seçin.
2. **SRV-iş** sanal makinesi için ağ arabirimini seçin.
3. **Ayarlar**altında, **DNS sunucuları**' nı seçin.
4. **DNS sunucuları**altında **özel**' i seçin.
5. **DNS sunucusu ekle** metin kutusuna **209.244.0.3**, sonraki metin kutusuna da **209.244.0.4** yazın.
6. **Kaydet**’i seçin.
7. **Srv-Work** sanal makinesini yeniden başlatın.

## <a name="test-the-firewall"></a>Güvenlik duvarını test etme

Şimdi, güvenlik duvarını test edin ve beklendiği gibi çalıştığını doğrulayın.

1. Azure portalda **Srv-Work** sanal makinesinin ağ ayarlarını gözden geçirin ve özel IP adresini not edin.
2. Uzak bir masaüstünü **SRV-atma** sanal makinesine bağlayın ve oturum açın. Buradan, **SRV iş** özel IP adresine bir Uzak Masaüstü bağlantısı açın.
3. Internet Explorer'ı açın ve https://www.google.com adresine gidin.
4. Internet Explorer güvenlik uyarıları ' nı **kapatmak** > **Tamam ' ı** seçin.

   Google giriş sayfasını görmeniz gerekir.

5. https://www.microsoft.com adresine gidin.

   Güvenlik duvarının engellemesi gerekir.

Artık Güvenlik Duvarı kurallarının çalıştığını doğruladınız:

* İzin verilen bir FQDN'ye göz atabilir ancak diğerlerine göz atamazsınız.
* Yapılandırılmış dış DNS sunucusunu kullanarak DNS adlarını çözümleyebilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Güvenlik duvarı kaynaklarını bir sonraki öğretici için tutabilirsiniz veya artık gerekli değilse **Test-FW-RG** kaynak grubunu silerek güvenlik duvarıyla ilgili tüm kaynakları silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: Azure Güvenlik Duvarı günlüklerini izleme](./tutorial-diagnostics.md)
