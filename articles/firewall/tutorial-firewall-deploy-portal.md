---
title: "Öğretici: Azure portal kullanarak Azure Güvenlik Duvarı 'Nı dağıtma & yapılandırma"
description: Bu öğreticide Azure portalı kullanarak Azure Güvenlik Duvarı'nı dağıtmayı ve yapılandırmayı öğreneceksiniz.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: ac9eaea70de67e67ef690c280b30bae22517ac21
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228002"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Öğretici: Azure portalı kullanarak Azure Güvenlik Duvarı'nı dağıtma ve yapılandırma

Giden ağ erişimini denetleme, genel ağ güvenlik planının önemli bir parçasıdır. Örneğin, erişimi Web sitelerine kısıtlamak isteyebilirsiniz. Ya da, erişilebilen giden IP adreslerini ve bağlantı noktalarını sınırlamak isteyebilirsiniz.

Azure Güvenlik Duvarı, Azure alt ağından giden ağ erişimini denetlemenin bir yoludur. Azure Güvenlik Duvarı ile şunları yapılandırabilirsiniz:

* Bir alt ağdan erişilebilen tam etki alanı adlarını (FQDN) tanımlayan uygulama kuralları.
* Kaynak adres, protokol, hedef bağlantı noktası ve hedef adresini tanımlayan ağ kuralları.

Ağ trafiğinizi güvenlik duvarından alt ağın varsayılan ağ geçidi olarak yönlendirdiğinizde ağ trafiği yapılandırılan güvenlik duvarı kurallarına tabi tutulur.

Bu öğreticide, kolay dağıtım için iki alt ağa sahip Basitleştirilmiş tek bir sanal ağ oluşturacaksınız.

Üretim dağıtımları için, güvenlik duvarının kendi VNet 'inde bulunduğu bir [hub ve bağlı bileşen modeli](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) önerilir. İş yükü sunucuları, bir veya daha fazla alt ağ ile aynı bölgedeki eşlenmiş VNET 'lerde bulunur.

* **AzureFirewallSubnet** - güvenlik duvarı bu alt ağdadır.
* **Workload-SN**: İş yükü sunucusu bu alt ağda yer alır. Bu alt ağın ağ trafiği güvenlik duvarından geçer.

![Öğretici ağı altyapısı](media/tutorial-firewall-deploy-portal/tutorial-network.png)

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Test amaçlı ağ ortamı oluşturma
> * Güvenlik duvarı dağıtma
> * Varsayılan rota oluşturma
> * Www.google.com erişimine izin vermek için bir uygulama kuralı yapılandırma
> * Dış DNS sunucularına erişime izin vermek için ağ kuralı yapılandırma
> * Uzak masaüstünün test sunucusuna bağlanmasına izin vermek için bir NAT kuralı yapılandırma
> * Güvenlik duvarını test etme

Tercih ederseniz, bu öğreticiyi [Azure PowerShell](deploy-ps.md) kullanarak tamamlayabilirsiniz.

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="set-up-the-network"></a>Ağı ayarlama

İlk olarak güvenlik duvarını dağıtmak için gerekli olan kaynakları içerecek bir kaynak grubu oluşturun. Ardından VNet, alt ağlar ve test sunucusu oluşturun.

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Kaynak grubu, bu öğreticideki tüm kaynakları içerir.

1. [https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.
2. Azure portal menüsünde, **kaynak grupları** ' nı seçin veya herhangi bir sayfadan *kaynak gruplarını* arayıp seçin. Ardından **Ekle**'yi seçin.
4. **Abonelik** bölümünde aboneliğinizi seçin.
1. **Kaynak grubu adı** için *Test-FW-RG* yazın.
1. **Kaynak grubu konumu** bölümünde bir konum seçin. Oluşturduğunuz diğer tüm kaynaklar aynı konumda olmalıdır.
1. **Gözden geçir ve oluştur**’u seçin.
1. **Oluştur**’u seçin.

### <a name="create-a-vnet"></a>Sanal ağ oluşturma

Bu VNet 'in üç alt ağı olacaktır.

> [!NOTE]
> AzureFirewallSubnet alt ağının boyutu/26 ' dır. Alt ağ boyutu hakkında daha fazla bilgi için bkz. [Azure Güvenlik DUVARı SSS](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

1. Azure portal menüsünde veya **Giriş** sayfasında, **Kaynak oluştur**’u seçin.
1. **Ağ**  >  **sanal ağını** seçin.
1. **Oluştur**’u seçin.
1. **Abonelik** bölümünde aboneliğinizi seçin.
1. **Kaynak grubu** için **Test-FW-RG** öğesini seçin.
1. **Ad** alanına **Test-FW-VN** yazın.
1. **Bölge** için, daha önce kullandığınız konumu seçin.
1. **İleri ' yi seçin: IP adresleri**.
1. **IPv4 adres alanı** için **10.0.0.0/16** yazın.
1. **Alt ağ** altında **varsayılan**' ı seçin.
1. **Alt ağ adı** türü için **AzureFirewallSubnet**. Güvenlik duvarı bu alt ağda yer alacaktır ve alt ağ adının **mutlaka** AzureFirewallSubnet olması gerekir.
1. **Adres aralığı** için **10.0.1.0/26** yazın.
1. **Kaydet**’i seçin.

   Sonra, iş yükü sunucusu için bir alt ağ oluşturun.

1. **Alt ağ ekle**' yi seçin.
4. **Alt ağ adı** Için **Iş yükü-sn** yazın.
5. **Alt ağ adres aralığı** için **10.0.2.0/24** yazın.
6. **Add (Ekle)** seçeneğini belirleyin.
7. **Gözden geçir ve oluştur**’u seçin.
8. **Oluştur**’u seçin.

### <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

Şimdi iş yükü sanal makinesini oluşturun ve **Iş yükü-sn** alt ağına yerleştirin.

1. Azure portal menüsünde veya **Giriş** sayfasında, **Kaynak oluştur**’u seçin.
2. **Windows Server 2016 Datacenter** öğesini seçin.
4. Sanal makine için şu değerleri girin:

   |Ayar  |Değer  |
   |---------|---------|
   |Kaynak grubu     |**Test-ILT-RG**|
   |Sanal makine adı     |**SRV-Iş**|
   |Bölge     |Öncekiyle aynı|
   |Görüntü|Windows Server 2016 Datacenter|
   |Yönetici Kullanıcı adı     |Kullanıcı adı yazın|
   |Parola     |Parola yazın|

4. **Gelen bağlantı noktası kuralları**, **Genel gelen bağlantı noktaları** altında **hiçbiri**' ni seçin.
6. Diğer varsayılanları kabul edin ve **İleri ' yi seçin: diskler**.
7. Disk varsayılanlarını kabul edin ve **İleri ' yi seçin: ağ**.
8. Sanal ağ için **Test-FW-VN** ' nin seçildiğinden ve alt ağın **Iş yükü-sn** olduğundan emin olun.
9. **Genel IP** için **hiçbiri**' ni seçin.
11. Diğer varsayılanları kabul edin ve **İleri: yönetim**' i seçin.
12. Önyükleme tanılamayı devre dışı bırakmak için **devre dışı bırak** seçeneğini belirleyin Diğer varsayılanları kabul edin ve **gözden geçir + oluştur**' u seçin.
13. Özet sayfasında ayarları gözden geçirin ve ardından **Oluştur**' u seçin.

## <a name="deploy-the-firewall"></a>Güvenlik duvarını dağıtma

Güvenlik duvarını sanal ağa dağıtın.

1. Azure portal menüsünde veya **Giriş** sayfasında, **Kaynak oluştur**’u seçin.
2. Arama kutusuna **güvenlik duvarı** yazın ve **ENTER** tuşuna basın.
3. **Güvenlik duvarı** ' nı seçip **Oluştur**' u seçin.
4. **Güvenlik duvarı oluştur** sayfasında aşağıdaki ayarları kullanarak güvenlik duvarını yapılandırın:

   |Ayar  |Değer  |
   |---------|---------|
   |Abonelik     |\<your subscription\>|
   |Kaynak grubu     |**Test-ILT-RG** |
   |Name     |**Test-FW01**|
   |Bölge     |Önceden kullandığınız konumu seçin|
   |Güvenlik Duvarı yönetimi|**Bu güvenlik duvarını yönetmek için güvenlik duvarı kuralları (klasik) kullanın**|
   |Bir sanal ağ seçin     |**Mevcut olanı kullan**: **Test-FW-vn**|
   |Genel IP adresi     |**Yeni Ekle**<br>**Ad**:  **FW-PIP**|

5. Diğer varsayılan değerleri kabul edin ve ardından **gözden geçir + oluştur**' u seçin.
6. Özeti gözden geçirin ve ardından güvenlik duvarını oluşturmak için **Oluştur** ' u seçin.

   Dağıtma işlemi birkaç dakika sürebilir.
7. Dağıtım tamamlandıktan sonra, **Test-FW-RG** kaynak grubuna gidin ve **Test-FW01** güvenlik duvarını seçin.
8. Güvenlik Duvarı özel ve genel IP adreslerini aklınızda edin. Bu adresleri daha sonra kullanacaksınız.

## <a name="create-a-default-route"></a>Varsayılan rota oluşturma

**Workload-SN** alt ağında varsayılan giden rotayı güvenlik duvarından geçecek şekilde yapılandırın.

1. Azure portal menüsünde **tüm hizmetler** ' i seçin veya herhangi bir sayfadan *tüm hizmetler* ' i arayın ve seçin.
2. **Ağ** altında, **Rota tabloları**' nı seçin.
3. **Add (Ekle)** seçeneğini belirleyin.
5. **Abonelik** bölümünde aboneliğinizi seçin.
6. **Kaynak grubu** için **Test-FW-RG** öğesini seçin.
7. **Bölge** için, daha önce kullandığınız konumu seçin.
4. **Ad** alanına **Firewall-route** yazın.
1. **Gözden geçir ve oluştur**’u seçin.
1. **Oluştur**’u seçin.

Dağıtım tamamlandıktan sonra **Kaynağa Git**' i seçin.

1. Güvenlik Duvarı-yönlendirme sayfasında, **alt ağlar** ' ı seçin ve ardından **ilişkilendir**' i seçin.
1. **Sanal ağ**  >  **sınaması-İlt-vn** öğesini seçin.
1. **Alt ağ** Için **Iş yükü-sn**' ı seçin. Bu rota için yalnızca **Iş yükü-sn** alt ağını seçtiğinizden emin olun, aksi takdirde güvenlik duvarınız doğru çalışmaz.

13. **Tamam**’ı seçin.
14. **Rotalar** ' ı seçin ve ardından **Ekle**' yi seçin.
15. **Rota adı** için, **FW-DG** yazın.
16. **Adres ön eki** alanına **0.0.0.0/0** yazın.
17. **Sonraki atlama türü** için **Sanal gereç**'i seçin.

    Azure Güvenlik Duvarı, normalde yönetilen bir hizmettir ancak bu durumda sanal gereç kullanılabilir.
18. **Sonraki atlama adresi** alanına önceden not ettiğiniz güvenlik duvarı özel IP adresini yazın.
19. **Tamam**’ı seçin.

## <a name="configure-an-application-rule"></a>Uygulama kuralı yapılandırma

Bu, için giden erişime izin veren uygulama kuralıdır `www.google.com` .

1. **Test-FW-RG** öğesini açın ve **Test-FW01** güvenlik duvarını seçin.
2. **Test-FW01** sayfasında, **Ayarlar**' ın altında Kurallar ' ı **(klasik)** seçin.
3. **Uygulama kuralı koleksiyonu** sekmesini seçin.
4. **Uygulama kuralı koleksiyonu Ekle**' yi seçin.
5. **Ad** alanına **App-Coll01** yazın.
6. **Öncelik** alanına **200** yazın.
7. **Eylem** alanında **İzin ver**'i seçin.
8. **Kurallar** altında, **ad** için **hedef FQDN 'ler** yazın, **izin ver-Google** yazın.
9. **Kaynak türü** için **IP adresi**' ni seçin.
10. **Kaynak** için **10.0.2.0/24** yazın.
11. **Protokol:bağlantı noktası** alanına **http, https** yazın.
12. **Hedef FQDN 'ler** için şunu yazın **`www.google.com`**
13. **Add (Ekle)** seçeneğini belirleyin.

Azure Güvenlik Duvarı'nda varsayılan olarak izin verilen altyapı FQDN'leri için yerleşik bir kural koleksiyonu bulunur. Bu FQDN'ler platforma özgüdür ve başka amaçlarla kullanılamaz. Daha fazla bilgi için bkz. [Altyapı FQDN'leri](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Ağ kuralını yapılandırma

Bu, bağlantı noktası 53’deki (DNS) iki IP adresine giden erişime izin veren ağ kuralıdır.

1. **Ağ kuralı koleksiyonu** sekmesini seçin.
2. **Ağ kuralı koleksiyonu Ekle**' yi seçin.
3. **Ad** alanına **Net-Coll01** yazın.
4. **Öncelik** alanına **200** yazın.
5. **Eylem** alanında **İzin ver**'i seçin.
6. **Kurallar** altında, **ad** için **IP adresleri**, Type **-DNS** yazın.
7. **Protokol** alanında **UDP**'yi seçin.
9. **Kaynak türü** için **IP adresi**' ni seçin.
1. **Kaynak** için **10.0.2.0/24** yazın.
2. **Hedef türü** için **IP adresi** seçin.
3. **Hedef adres** için **209.244.0.3, 209.244.0.4** yazın

   Bunlar, CenturyLink tarafından çalıştırılan genel DNS sunucularıdır.
1. **Hedef Bağlantı Noktaları** için **53** yazın.
2. **Add (Ekle)** seçeneğini belirleyin.

## <a name="configure-a-dnat-rule"></a>DNAT kuralını yapılandırma

Bu kural, güvenlik duvarı üzerinden uzak bir masaüstünü Srv-Work sanal makinesine bağlamanıza olanak tanır.

1. **NAT kural koleksiyonu** sekmesini seçin.
2. **NAT kuralı koleksiyonu Ekle**' yi seçin.
3. **Ad** için **RDP** yazın.
4. **Öncelik** alanına **200** yazın.
5. **Kurallar**' ın altında, **ad** için **RDP-NAT** yazın.
6. **Protokol** alanında **TCP**'yi seçin.
7. **Kaynak türü** için **IP adresi**' ni seçin.
8. **Kaynak** için yazın **\*** .
9. **Hedef adres** için güvenlik DUVARı genel IP adresini yazın.
10. **Hedef bağlantı noktaları** için **3389** yazın.
11. **Çevrilen adres** Için, **SRV-iş** özel IP adresini yazın.
12. **Çevrilmiş bağlantı noktası** için **3389** yazın.
13. **Add (Ekle)** seçeneğini belirleyin.


### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>**Srv-Work** ağ arabiriminin birincil ve ikincil DNS adresini değiştirme

Bu öğreticide sınama amacıyla sunucunun birincil ve ikincil DNS adreslerini yapılandırın. Bu genel bir Azure Güvenlik Duvarı gereksinimi değildir.

1. Azure portal menüsünde, **kaynak grupları** ' nı seçin veya herhangi bir sayfadan *kaynak gruplarını* arayıp seçin. **Test-FW-RG** kaynak grubunu seçin.
2. **SRV-iş** sanal makinesi için ağ arabirimini seçin.
3. **Ayarlar** altında, **DNS sunucuları**' nı seçin.
4. **DNS sunucuları** altında **özel**' i seçin.
5. **DNS sunucusu ekle** metin kutusuna **209.244.0.3**, sonraki metin kutusuna da **209.244.0.4** yazın.
6. **Kaydet**’i seçin.
7. **Srv-Work** sanal makinesini yeniden başlatın.

## <a name="test-the-firewall"></a>Güvenlik duvarını test etme

Şimdi, güvenlik duvarını test edin ve beklendiği gibi çalıştığını doğrulayın.

1. Uzak bir masaüstünü güvenlik duvarı genel IP adresine bağlayın ve **SRV-Work** sanal makinesinde oturum açın. 
3. Internet Explorer'ı açın ve `https://www.google.com` adresine gidin.
4.   >  Internet Explorer güvenlik uyarıları 'nda Tamam **Kapat** ' ı seçin.

   Google giriş sayfasını görmeniz gerekir.

5. `https://www.microsoft.com` adresine gidin.

   Güvenlik duvarının engellemesi gerekir.

Artık Güvenlik Duvarı kurallarının çalıştığını doğruladınız:

* İzin verilen bir FQDN'ye göz atabilir ancak diğerlerine göz atamazsınız.
* Yapılandırılmış dış DNS sunucusunu kullanarak DNS adlarını çözümleyebilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Güvenlik duvarı kaynaklarını bir sonraki öğretici için tutabilirsiniz veya artık gerekli değilse **Test-FW-RG** kaynak grubunu silerek güvenlik duvarıyla ilgili tüm kaynakları silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: Azure Güvenlik Duvarı günlüklerini izleme](./firewall-diagnostics.md)