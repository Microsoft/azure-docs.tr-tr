---
title: F5 dağıtım kılavuzu ile Azure AD güvenli karma erişimi | Microsoft Docs
description: Güvenli karma erişim için Azure IaaS 'de F5 BIG-IP Virtual Edition (VE) VM dağıtma öğreticisi
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 139009c55573e1e115a22069671f66e93695a635
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783332"
---
# <a name="tutorial-to-deploy-f5-big-ip-virtual-edition-vm-in-azure-iaas-for-secure-hybrid-access"></a>Güvenli karma erişim için Azure IaaS 'de F5 BIG-IP Virtual Edition VM dağıtma öğreticisi

Bu öğretici, Azure IaaS 'de büyük IP Vitürel sürümlerini (VE) dağıtmanın bitiş süreci boyunca size yol gösterir. Bu öğreticinin sonunda şunları yapmanız gerekir:

- Güvenli hibrit erişimi (SHA) kavramını modellemeye yönelik tam olarak hazırlanmış büyük IP sanal makinesi (VM)

- Yeni büyük IP sistem güncelleştirmelerini ve düzeltmeleri test etmek için kullanılan hazırlama örneği

## <a name="prerequisites"></a>Önkoşullar

Önceki F5 büyük IP deneyimi veya bilgi gerekli değildir, ancak [F5 büyük IP terminolojisini](https://www.f5.com/services/resources/glossary)alıştırarak kendiniz öneririz. SHA için Azure 'da büyük IP dağıtımı şunları gerektirir:

- Ücretli bir Azure aboneliği veya 12 aylık ücretsiz [deneme aboneliği](https://azure.microsoft.com/free/).

- Aşağıdaki F5 büyük IP lisans SKU 'larından herhangi biri

  - F5 BIG-IP® En Iyi demeti

  - F5 BIG-IP Access Policy Manager™ (APM) tek başına lisansı

  - F5 BIG-IP Access Policy Manager™ (APM) eklenti lisansı var olan bir büyük IP F5 BIG-IP® yerel Traffic Manager™ (LTM)
  
  - 90 günlük büyük-IP tam özellik [deneme lisansı](https://www.f5.com/trial/big-ip-trial.php).

- Web uygulamalarını Güvenli Yuva Katmanı (SSL) üzerinden yayımlamak için bir joker karakter veya konu alternatif adı (SAN) sertifikası. [Şifrelemem](https://letsencrypt.org/) , sınama için ücretsiz 90 günlük sertifika sağlar.

- BIG-IPs yönetim arabirimini güvenli hale getirmek için bir SSL sertifikası. Web uygulamalarını yayımlamak için kullanılan bir sertifika, konusu büyük IP 'nin tam etki alanı adına (FQDN) karşılık geliyorsa kullanılabilir. Örneğin, bir konu *. contoso.com ile tanımlanan bir joker karakter sertifikası için uygun olacaktır `https://big-ip-vm.contoso.com:8443`

VM dağıtımı ve temel sistem yapılandırmaları yaklaşık 30 dakika sürer. bu noktada büyük IP platformunuzun [burada](f5-aad-integration.md)listelenen SHA senaryolarından herhangi birini uygulamaya hazır olacağı anlamına gelir.

Bu öğreticide, senaryoları test etmek için büyük IP 'nin Active Directory (AD) ortamı içeren bir Azure Kaynak grubuna dağıtılacağını kabul edilir. Ortam, bir etki alanı denetleyicisi (DC) ve Web ana bilgisayar (IIS) VM 'lerinden oluşmalıdır. Bu sunucuların büyük IP VM 'ye ait diğer konumlarda olması de, büyük IP 'nin, belirli bir senaryoyu desteklemek için gereken her bir rol için bir görüş alanına sahip olmasını sağlamak da da Tamam. BÜYÜK IP sanal makinesinin bir VPN bağlantısı üzerinden başka bir ortama bağlandığı senaryolar da desteklenir.

Test için burada bahsedilen öğeler yoksa, bu [betiği](https://github.com/Rainier-MSFT/Cloud_Identity_Lab)kullanarak ad etki alanı ortamının tamamını Azure 'a dağıtabilirsiniz. Örnek test uygulamalarının bir koleksiyonu, bu [komut dosyası içeren Otomasyon](https://github.com/jeevanbisht/DemoSuite)kullanılarak programlı BIR şekilde IIS Web ana bilgisayarına da dağıtılabilir.

>[!NOTE]
>[Azure Portal](https://portal.azure.com/#home) sürekli olarak gelişiyor, bu nedenle bu öğreticideki bazı adımlar Azure Portal gözlemlenen gerçek düzenden farklı olabilir.

## <a name="azure-deployment"></a>Azure dağıtımı

BÜYÜK IP, farklı topolojilerde dağıtılabilir. Bu kılavuz, tek bir ağ arabirimi (NIC) dağıtımına odaklanır. Ancak, büyük IP dağıtımınız yüksek kullanılabilirlik, ağ ayrımı veya 1 GB 'den daha fazla aktarım için birden çok ağ arabirimi gerektiriyorsa, F5's önceden derlenmiş [Azure Resource Manager (ARM) şablonları](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_multiNIC.html)kullanmayı düşünün.

[Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps)'NDEN büyük IP 'yi dağıtmak için aşağıdaki görevleri doldurun.

1. [Azure Portal](https://portal.azure.com/#home) , VM oluşturma izinlerine sahip olan bir hesapla oturum açın. Örneğin, katkıda bulunan

2. En üstteki şerit arama kutusuna **Market** yazın ve ardından **ENTER** tuşuna basarak

3. Market filtresine F5 yazın ve ardından **ENTER** **tuşuna** basın

4. Üstteki şeritten **+ Ekle** ' yi seçin ve ardından **ENTER** tuşuna basarak Market filtresine **F5** yazın.

5. **F5 Big-IP Virtual Edition 'ı seçin (KLG)**  >  **bir yazılım planı seçin**  >  **F5 Big-IP ve-All (KLG, 2 önyükleme konumu)**

6. **Oluştur**’u seçin.

![Görüntüde, yazılım planı seçme adımları gösterilir](./media/f5ve-deployment-plan/software-plan.png)

7. **Temel bilgiler** menüsünde ilerleyin ve aşağıdaki ayarları kullanın

 |  Proje ayrıntıları     |  Değer     |
 |:-------|:--------|
 |Abonelik|BÜYÜK IP VM dağıtımı için hedef abonelik|
 |Kaynak grubu | Mevcut Azure Kaynak grubu büyük IP sanal makinesi içine dağıtılacak veya bir tane oluşturacak. DC ve IIS sanal makinelerinizin aynı kaynak grubu olmalıdır|
 | **Örnek ayrıntıları**|  |
 |VM Adı| Örnek büyük-IP-VM |
 |Region | BÜYÜK IP-VM için hedef Azure coğrafi |
 |Kullanılabilirlik seçenekleri| Yalnızca üretimde VM kullanılıyorsa etkinleştirin|
 |Görüntü| F5 BIG-IP VE-ALL (KLG, 2 önyükleme konumu)|
 |Azure Spot örneği| Hayır ancak uygunsa etkinleştirmek için ücretsiz |
 |Boyut| Minimum belirtim 2 vCPU ve 8 GB bellek olmalıdır|
 |**Yönetici hesabı**|  |
 |Kimlik doğrulaması türü|Şimdilik parolayı seçin. Daha sonra bir anahtar çiftine geçiş yapabilirsiniz |
 |Kullanıcı adı|Yönetim arabirimlerine erişim için büyük IP yerel hesabı olarak oluşturulacak kimlik. Kullanıcı adı büyük/küçük harfe duyarlıdır.|
 |Parola|Güçlü bir parolayla yönetici erişimini güvenli hale getirme|
 |**Gelen bağlantı noktası kuralları**|  |
 |Genel gelen bağlantı noktaları|Yok|

8. **İleri ' yi seçin:** tüm varsayılanları bırakarak diskler, **İleri ' yi seçin: ağ**.

9. **Ağ** menüsünde, bu ayarları doldurun.

 |Ağ arabirimi|      Değer |
 |:--------------|:----------------|
 |Sanal ağ|DC ve IIS VM 'Ler tarafından kullanılan aynı Azure VNet 'i veya bir tane oluşturun|
 |Alt ağ| DC ve IIS sanal makinelerinize ait Azure iç alt ağı aynı veya bir tane oluşturun|
 |Genel IP |  Yok|
 |NIC ağ güvenlik grubu| Önceki adımlarda seçtiğiniz Azure alt ağı zaten bir ağ güvenlik grubu (NSG) ile ilişkilendirilmişse hiçbiri ' ni seçin; Aksi takdirde temel seçin|
 |Ağı hızlandırma| Kapalı |
 |**Yük dengeleme**|     |
 |Yük Dengeleme VM 'si| No|

10. **İleri: yönetim** ' i seçin ve bu ayarları doldurun.

 |İzleme|    Değer |
 |:---------|:-----|
 |Ayrıntılı izleme| Kapalı|
 |Önyükleme tanılamaları|Özel depolama hesabıyla etkinleştirin. , Azure portal seri konsol seçeneği aracılığıyla büyük IP Secure Shell (SSH) arabirimine bağlanmaya izin verir. Kullanılabilir herhangi bir Azure Depolama hesabını seçin|
 |**Kimlik**|  |
 |Sistem tarafından atanan yönetilen kimlik|Kapalı|
 |Azure Active Directory|BÜYÜK-IP Şu anda bu seçeneği desteklemiyor|
 |**Otomatik kapatma**|    |
 |Otomatik kapanışı etkinleştir| Sınama yapıyorsanız, büyük IP-VM 'yi her gün kapatılacak şekilde ayarlamayı düşünün|

11. **İleri ' yi seçin: Gelişmiş** tüm varsayılan değerleri bırakın ve **İleri ' yi seçin: Etiketler**.

12. **İleri ' yi seçin:** dağıtım başlatma için **Oluştur** ' u seçmeden önce büyük IP-VM yapılandırmalarından gözden geçirin ve oluşturun.

13. BÜYÜK IP sanal makinesinin tamamen dağıtılması süresi genellikle 5 dakikadır. Tamamlandığında, **Kaynağa Git**' i seçmeyin, Azure Portal sol taraftaki menüsünü genişletin ve yenı büyük IP-VM 'nize gitmek için **kaynak grupları** ' nı seçin. VM oluşturma başarısız olursa, **geri** ve **İleri**' yi seçin.

## <a name="network-configuration"></a>Ağ yapılandırması

BÜYÜK IP sanal makinesi ilk kez önyüklendiğinde, NIC 'si, bağlandığı Azure alt ağının dinamik ana bilgisayar Yapılandırma Protokolü (DHCP) hizmeti tarafından verilen **birincil** özel IP ile sağlanır. Bu IP, ile iletişim kurmak için büyük IP 'nin trafik yönetimi Işletim sistemi (TMO 'lar) tarafından kullanılacaktır:

- Diğer konaklarla ve hizmetlerle iletişim kurma

- Genel internet 'e giden erişim

- BIG-IPs Web config ve SSH yönetim arabirimlerine gelen erişim

Bu yönetim arabirimlerinden birini Internet 'e göstermek, BIG-IPs saldırı yüzeyini artırarak BIG-IPs birincil IP 'nin dağıtım sırasında genel bir IP ile sağlanamamasından dolayı olur. Bunun yerine, hizmetleri yayımlamak için ikincil bir iç IP ve ilişkili ortak IP sağlanır.
VM genel IP ve özel IP arasında bu 1 ile 1 arasında eşleme, dış trafiğin bir VM 'ye ulaşmasını sağlar. Ancak, bir güvenlik duvarıyla aynı şekilde trafiğe izin vermek için bir Azure NSG kuralı da gerekir.

Diyagramda bir büyük IP ve Azure 'da, genel işlemler ve yönetim için birincil IP ile yapılandırılan ve hizmetleri yayımlamak için ayrı bir sanal sunucu IP 'si ile yapılandırılmış tek bir NIC dağıtımı gösterilmektedir.
Bu düzenlemede, bir NSG kuralı, uzak trafiğin `intranet.contoso.com` , büyük IP sanal sunucusuna iletilmeden önce yayımlanmış hizmetin genel IP 'ye yönlendirilmesini sağlar.

![Görüntüde tek NIC dağıtımı varsayılan olarak gösterilmektedir ](./media/f5ve-deployment-plan/single-nic-deployment.png) , Azure VM 'lerine verilen özel ve genel IP 'ler her zaman dinamik olduğundan, BIR VM 'nin her yeniden başlatması büyük olasılıkla değişir. BIG-IPs yönetim IP 'sini statik olarak değiştirerek ve hizmetleri yayımlamak için kullanılan ikincil IP 'Lerde aynı olduğundan, öngörülemeyen bağlantı sorunlarından kaçının.

1. Büyük IP sanal makinenizin menüsünden **Ayarlar**  >  **ağ iletişimi** ' ne gidin

2. Ağ görünümünde, **ağ arabiriminin** sağından bağlantıyı seçin

![Görüntüde ağ yapılandırması gösterilir](./media/f5ve-deployment-plan/network-config.png)

>[!NOTE]
>VM adları, dağıtım sırasında rastgele oluşturulur.

3. Sol bölmede **IP yapılandırması** ' nı ve ardından **ipconfig1** Row ' ı seçin.

4. **IP atama** seçeneğini statik olarak ayarlayın ve gerekırse, büyük IP VM 'LERININ birincil IP adresini değiştirin. Sonra **Kaydet** ' i seçin ve ipconfig1 menüsünü kapatın

>[!NOTE]
>Bu birincil IP 'yi, büyük IP-VM ' i bağlamak ve yönetmek için kullanacaksınız.

5. Üst şeritte **+ Ekle** ' yi seçin ve IKINCIL özel IP için bir ad sağlayın, örneğin, ipconfig2

6. Özel IP adresi ayarlarının **ayırma** seçeneğini **statik** olarak ayarlayın. Bir sonraki daha yüksek veya daha düşük ardışık IP sağlamak, işlerin düzenli tutulmasına yardımcı olur.

7. **Ilişkilendirilecek** genel IP adresini belirleyin ve **Oluştur** ' u seçin

8. Yeni genel IP adresi için bir ad sağlayın, örneğin, büyük IP-VM_ipconfig2_Public

9. İstenirse, **SKU 'Yu** standart olarak ayarlayın

10. İstenirse **katmanı** **Global** olarak ayarlayın ve

11. **Atama** seçeneğini **statik** olarak ayarlayın, ardından iki kez **Tamam** ' ı seçin.

BÜYÜK IP-VM 'niz artık şu şekilde ayarlamaya hazırdır:

- **Birincil özel IP**: Web yapılandırma yardımcı programı ve SSH aracılığıyla büyük IP-VM yönetimi için ayrılmıştır. Bu, büyük IP sistemi tarafından, yayımlanan arka uç hizmetlerine bağlanmak için **kendi kendi IP 'si** olarak kullanılacaktır. Ayrıca, NTP, AD ve LDAP gibi dış hizmetlere bağlanır.

- **İkincil özel IP**: yayımlanmış bir hizmete gelen istekleri dınlemek IÇIN büyük IP APM sanal sunucusu oluşturulurken kullanılır.

- **Genel IP**: IKINCIL özel IP ile ilişkili, ortak internet 'ten gelen istemci trafiğinin yayımlanan hizmet (ler) IÇIN büyük IP sanal sunucusuna ulaşmasını sağlar

Örnek, VM ortak ve özel IP 'Ler arasındaki 1 ile 1 arasındaki ilişkiyi gösterir. Azure VM NIC 'sinde yalnızca bir birincil IP olabilir ve ek olarak oluşturulan tüm IP 'Ler her zaman ikincil olarak adlandırılır.

>[!NOTE]
>BÜYÜK IP hizmetlerini yayımlamak için ikincil IP eşlemelerine ihtiyacınız olacak.

![Bu görüntüde tüm ikincil IP 'Ler gösteriliyor](./media/f5ve-deployment-plan/secondary-ips.png)

BÜYÜK IP **erişimi destekli yapılandırmayı** kullanarak SHA uygulamak IÇIN, büyük IP APM aracılığıyla yayımlamayı planladığınız her ek hizmet için ek özel ve genel IP çiftleri oluşturmak üzere 5-11 arasındaki adımları yineleyin. Ayrıca, BIG-IPs **Gelişmiş yapılandırma** kullanılarak hizmetler yayımlandığında de aynı yaklaşım kullanılabilir. Ancak, bu senaryoda, [sunucu adı göstergesi (SNı)](https://support.f5.com/csp/#/article/K13452) yapılandırması kullanarak genel IP fazla kafaları önleme seçeneğiniz vardır. Bu yapılandırmada, büyük bir IP sanal sunucusu aldığı tüm istemci trafiğini kabul eder ve hedefin hedefine yönlendirirsiniz.

## <a name="dns-configuration"></a>DNS yapılandırması

İstemcilerin yayınlanmış SHA hizmetlerini büyük IP-VM 'nizin genel IP 'lerinize çözümlemesi için DNS 'nin yapılandırılmış olması önemlidir.

Aşağıdaki adımlarda, SHA hizmetleriniz için kullanılan genel etki alanının DNS bölgesi Azure 'da yönetilir. Ancak, konum belirleyici kayıt oluşturmaya yönelik aynı DNS ilkeleri, DNS bölgenizin nerede yönetildiği konusunda da geçerlidir.

1. Zaten açık değilse, portalın sol menüsünü genişletin ve **kaynak grupları** SEÇENEĞI aracılığıyla büyük IP-VM 'nize gidin

2. Büyük IP sanal makinenizin menüsünden **Ayarlar**  >  **ağ iletişimi** ' ne gidin

3. BÜYÜK IP-VM 'Ler ağ görünümünde, açılan IP yapılandırması listesinden ilk ikincil IP 'yi seçin ve **NIC ortak IP 'si** için bağlantıyı seçin

![NIC genel IP 'sini gösteren ekran görüntüsü](./media/f5ve-deployment-plan/networking.png)

4. Sol bölmedeki **Ayarlar** bölümünün altında, SEÇILI ikincil IP IÇIN genel IP ve DNS özellikleri menüsünü açmak üzere **yapılandırma** ' yı seçin.

5. Diğer ad kaydı ' nı seçin ve **oluşturun** ve açılan listeden **DNS** bölgenizi seçin. Bir DNS bölgesi yoksa, Azure dışında yönetiliyor olabilir veya Azure AD 'de doğrulamış olduğunuz etki alanı soneki için bir tane oluşturabilirsiniz.

6. İlk DNS diğer ad kaydını oluşturmak için aşağıdaki ayrıntıları kullanın:

 | Alan | Değer |
 |:-------|:-----------|
 |Abonelik| BÜYÜK IP-VM ile aynı abonelik|
 |DNS bölgesi| Yayınlanan Web siteleriniz tarafından kullanılan doğrulanmış etki alanı soneki için yetkili DNS bölgesi, örneğin, www.contoso.com |
 |Name | Belirttiğiniz ana bilgisayar adı, seçili ikincil IP ile ilişkili genel IP 'ye çözümlenir. IP eşlemelerinde doğru DNS 'yi tanımladığınızdan emin olun. Bkz. ağ yapılandırması 'ndaki son görüntü, örneğin, intranet.contoso.com > 13.77.148.215|
 | TTL | 1 |
 |TTL birimleri | Saat |

7. Bu ayarları genel DNS 'e kaydetmek için Azure için **Oluştur** ' u seçin.

8. Genel IP menüsünü kapatmadan önce **DNS adı etiketini (isteğe bağlı)** bırakın ve **Kaydet** ' i seçin.

9. BÜYÜK IP 'nin Kılavuzlu yapılandırması kullanılarak yayımlamayı planladığınız her hizmet için ek DNS kayıtları oluşturmak üzere 1 ile 6 arasındaki adımları yineleyin.

  DNS kayıtları varken, oluşturulan bir kaydın tüm genel genel DNS sunucularında başarıyla yayıldığından emin olmak için [DNS denetleyicisi](https://dnschecker.org/) gibi çevrimiçi araçlardan herhangi birini kullanabilirsiniz.

  DNS etki alanınızı [GoDaddy](https://www.godaddy.com/)gibi bir dış sağlayıcı kullanarak yönetiyorsanız, kendi DNS yönetim olanağını kullanarak kayıt oluşturmanız gerekecektir.

>[!NOTE]
>Ayrıca, test ve sık sık DNS kayıtlarını değiştirirken BILGISAYARıN yerel ana bilgisayarları dosyasını da kullanabilirsiniz. Bir Windows BILGISAYARDAKI localhosts dosyasına klavyeden Win + R tuşlarına basılarak ve Çalıştır kutusuna Word **sürücüleri** gönderilirken erişilebilir. Yalnızca bir localhost kaydının yalnızca yerel bılgısayar için DNS çözümlemesi sağlayacaksınız, diğer istemcileri değil.

## <a name="client-traffic"></a>İstemci trafiği

Varsayılan olarak, Azure sanal ağları ve ilişkili alt ağlar Internet trafiği alamayacak özel ağlardır. BÜYÜK IP sanal makinenizin NIC 'i, dağıtım sırasında belirttiğiniz NSG 'ye eklenmelidir. Dış Web trafiğinin büyük-IP-VM ' y e ulaşması için, genel İnternet üzerinden 443 (HTTPS) ve 80 (HTTP) bağlantı noktalarına izin vermek üzere bir gelen NSG kuralı tanımlamanız gerekir.

1. BÜYÜK IP VM 'sinin ana **genel bakış** menüsünde **ağ** ' ı seçin.

2. Aşağıdaki NSG kural özelliklerini girmek için gelen kuralı **Ekle** ' yi seçin:

 |     Alan   |   Değer        |
 |:------------|:------------|
 |Kaynak| Herhangi bir|
 |Kaynak bağlantı noktası aralıkları| *|
 |Hedef IP adresleri|Tüm büyük IP-VM ikincil özel IP 'lerinin virgülle ayrılmış listesi|
 |Hedef bağlantı noktaları| 80,443|
 |Protokol| TCP |
 |Eylem| İzin Ver|
 |Öncelik|100-4096 arasındaki en düşük kullanılabilir değer|
 |Name | Açıklayıcı bir ad, örneğin: `BIG-IP-VM_Web_Services_80_443`|

3. Değişiklikleri uygulamak için **Ekle** ' yi seçin ve **ağ** menüsünü kapatın.

Bundan sonra herhangi bir konumdan gelen HTTP ve HTTPS trafiğinin, büyük IP VM 'lerinin tüm ikincil arabirimlerinize erişmesine izin verilir. Bağlantı noktası 80 ' e izin verilmesi, büyük IP APM 'nin kullanıcıları HTTP 'den HTTPS 'ye otomatik olarak yönlendirmesine izin vermek için yararlıdır. Bu kural, her gerektiğinde hedef IP 'Leri eklemek veya kaldırmak için düzenlenebilir.

## <a name="manage-big-ip"></a>BÜYÜK IP 'yi yönetme

BÜYÜK bir IP sistemi, Web yapılandırma kullanıcı arabirimi aracılığıyla yönetilir ve bu, aşağıdaki önerilen yöntemlerden birini kullanarak erişilebilir:

- BÜYÜK IP 'nin iç ağı içindeki bir makineden

- BÜYÜK IP-VM 'nin iç ağına bağlı bir VPN istemcisinden

- [Azure AD uygulama ara sunucusu](./application-proxy-add-on-premises-application.md) aracılığıyla yayımlandı

Kalan yapılandırmalara geçebilmeniz için en uygun yönteme karar vermeniz gerekir. Gerekirse, büyük IP 'nin birincil IP 'sini genel bir IP ile yapılandırarak doğrudan internet 'ten Web config 'e bağlanabilirsiniz. Daha sonra bu birincil IP 'ye 8443 trafiğe izin vermek için bir NSG kuralı ekleyin. Kaynağı kendi güvenilen IP 'niz ile kısıtladığınızdan emin olun, aksi takdirde herkes bağlanabilir.

Daha sonra, büyük IP sanal makinesinin Web yapılandırmasına bağlanabildiğinizi ve VM dağıtımı sırasında belirtilen kimlik bilgileriyle oturum açmayı doğrulayabilirsiniz:

- Kendi iç ağındaki bir VM 'den veya VPN aracılığıyla bağlanıyorsanız, BIG-IPs birincil IP ve Web config bağlantı noktasına doğrudan bağlanın. Örneğin, `https://<BIG-IP-VM_Primary_IP:8443`. Tarayıcınız bağlantının güvensiz olduğunu sorar, ancak büyük IP yapılandırılana kadar istemi yoksayabilirsiniz. Tarayıcı erişimi engelleyip insists, önbelleğini temizleyin ve yeniden deneyin.

- Web yapılandırmasını uygulama proxy 'Si aracılığıyla yayımladıysanız, bağlantı noktasını eklemeden Web yapılandırmasına dışarıdan erişmek için tanımlanan URL 'yi (örneğin,) kullanın `https://big-ip-vm.contoso.com` . İç URL, Web yapılandırma bağlantı noktası kullanılarak tanımlanmalıdır; Örneğin, `https://big-ip-vm.contoso.com:8443` 

BÜYÜK bir IP sistemi, genellikle komut satırı (CLı) görevleri ve kök düzeyinde erişim için kullanılan kendi temel SSH ortamı aracılığıyla da yönetilebilir. CLı 'ya bağlanmak için çeşitli seçenekler mevcuttur, örneğin:

- [Azure savunma hizmeti](../../bastion/bastion-overview.md): herhangi bir konumdan bir sanal ağ içindeki HERHANGI bir VM 'ye hızlı ve güvenli bağlantılara izin verir

- JıT yaklaşımı aracılığıyla PuTTY gibi bir SSH istemcisi aracılığıyla doğrudan bağlanın

- Seri konsol: portaldaki VM 'Ler menüsündeki destek ve sorun giderme bölümünün en altında sunulur. Dosya aktarımlarını desteklemez.

- Web yapılandırmasında olduğu gibi, büyük IP 'nin birincil IP 'sini ortak IP ile yapılandırarak ve SSH trafiğine izin veren bir NSG kuralı ekleyerek doğrudan Internet 'ten CLı 'ye bağlanabilirsiniz. Yine, bu yöntemi kullanıyorsanız kaynağı kendi güvenilir IP 'niz ile kısıtladığınızdan emin olun.  

## <a name="big-ip-license"></a>BÜYÜK IP Lisansı

Yayımlama hizmetleri ve SHA için yapılandırılanmadan önce, büyük IP sistemi etkinleştirilmelidir ve APM modülü ile sağlanmalıdır.

1. Web config 'e yeniden oturum açın ve **Genel Özellikler** sayfasında **Etkinleştir** ' i seçin.

2. **Temel kayıt anahtarı** alanına F5 tarafından sunulan büyük/küçük harfe duyarlı anahtarı girin

3. **Etkinleştirme yöntemini** **Otomatik** olarak ayarlayın ve **ILERI**' yi seçin, büyük IP lisansı doğrular ve Son Kullanıcı Lisans Sözleşmesi 'ni (EULA) görüntüler.

4. **Devam**' ı seçmeden önce **kabul et** ' i seçin ve etkinleştirmenin tamamlanmasını bekleyin.

5. Yeniden oturum açın ve Lisans Özeti sayfasının en altında **İleri**' yi seçin. BÜYÜK IP artık SHA için gereken çeşitli özellikleri sağlayan modüllerin bir listesini görüntüler. Bunu görmüyorsanız, ana sekmeden **sistem**  >  **kaynağı sağlama** bölümüne gidin.

6. Erişim Ilkesi için sağlama sütununu denetleme (APM)

![Görüntüde erişim sağlama gösterilmektedir](./media/f5ve-deployment-plan/access-provisioning.png)

7. **Gönder** ' i seçin ve uyarıyı kabul edin

8. Hasta olun ve büyük IP 'nin yeni özellikleri aydınlatmayı tamamlamasını bekleyin. Tam olarak başlatılmadan birkaç kez geçiş gösterebilir. 

9. Hazırlık ' i seçin ve **hakkında** sekmesinden **kurulum yardımcı programını çalıştır** ' **ı seçin** .

>[!IMPORTANT]
>F5 lisansları, tek bir büyük IP VE örnek tarafından herhangi bir zamanda tüketilmek üzere kısıtlanmıştır. Bir lisansı bir örnekten diğerine geçirmeyi düşünmenin nedenleri olabilir. bunu yaparsanız, kullanımdan kaldırmadan önce etkin örnekte deneme lisansınızı [iptal](https://support.f5.com/csp/article/K41458656) ettiğinizden emin olun, aksi takdirde lisans kalıcı olarak kaybedilir.

## <a name="provision-big-ip"></a>BÜYÜK IP sağlama

BIG-IPs Web config 'e ve sunucudan yönetim trafiğinin güvenliğini sağlamak Paramount. Web yapılandırma kanalının güvenliğinin aşılmasına yardımcı olmak için bir cihaz yönetim sertifikası yapılandırın.

1. Sol gezinti çubuğundan **sistem**  >  **sertifika yönetimi**  >  **trafiği sertifika yönetimi**  >  **SSL sertifika listesi**  >  **içeri aktarma** ' ya gidin.

2. **Içeri aktarma türü** açılan listesinden **PKCS 12 (IIS)** öğesini seçin ve **Dosya**' yı seçin. Sonraki birkaç adımda büyük IP-VM ' y i kapsayacak bir konu adı veya SAN içeren bir SSL Web sertifikası bulun

3. Sertifikanın parolasını girip **Içeri aktar** ' ı seçin.

4. Sol gezinti çubuğundan **sistem**  >  **platformu** ' na gidin.

5. BÜYÜK IP-VM ' y i tam bir ana bilgisayar adı ve kendi ortamı için saat dilimi ile, ardından **Update** ile yapılandırın

![Görüntüde genel özellikler gösterilir](./media/f5ve-deployment-plan/general-properties.png)

6. Sol gezinti çubuğundan **sistem**  >  **yapılandırma**  >  **cihaz**  >  **NTP** öğesine gidin

7. Güvenilir bir NTP kaynağı belirtin ve **Ekle**' yi ve ardından **Güncelleştir**' i seçin. Örneğin, `time.windows.com`

Şimdi, önceki adımlarda belirtilen BIG-IPs FQDN 'sini birincil özel IP 'sine çözümlemek için bir DNS kaydına ihtiyacınız vardır. Ortamın iç DNS 'ye veya büyük IP 'nin Web yapılandırmasına bağlanmak için kullanılacak bir BILGISAYARıN localhost dosyasına bir kayıt eklenmelidir. Her iki durumda da, Web yapılandırmasına doğrudan bağlandığınızda tarayıcı uyarısı artık görünmez. Yani, uygulama proxy 'Si ya da başka bir ters proxy aracılığıyla değil.

## <a name="ssl-profile"></a>SSL profili

Ters bir ara sunucu olarak, büyük bir IP sistemi basit bir iletme hizmeti olarak veya saydam bir proxy olarak da bilinen bir tam proxy veya istemciler ve sunucular arasındaki değişimler için etkin bir şekilde hareket edebilir.
Tam bir ara sunucu iki ayrı bağlantı oluşturur: bir ön uç TCP istemci bağlantısıyla birlikte, ortadaki bir boşluk ile bağlanmış bir arka uç TCP sunucu bağlantısı. İstemciler, **sanal sunucu** olarak da bilinen bir uçta proxy dinleyicisine bağlanır ve proxy, arka uç sunucusuna ayrı, bağımsız bir bağlantı kurar. Her iki tarafta da çift yönlü olur.
Bu tam proxy modunda, F5 BIG-IP sistemi trafiği inceliyor ve bu nedenle isteklerle ve yanıtlarla etkileşim kurmak için de mümkündür. Yük Dengeleme ve Web performansı iyileştirmesi gibi belirli işlevlerin yanı sıra uygulama katmanı güvenliği, Web hızlandırma, sayfa yönlendirme ve güvenli uzaktan erişim gibi daha gelişmiş trafik yönetimi hizmetleri bu işlevselliğe bağımlıdır.
SSL tabanlı Hizmetleri yayımlarken, istemciler ve arka uç hizmetleri arasındaki trafiğin şifresini çözme ve şifreleme işlemi büyük IP SSL profilleri tarafından işlenir.

İki tür profil vardır:

- **ISTEMCI SSL**: Iç Hizmetleri SSL ile YAYıMLAMAK IÇIN büyük IP sistemi ayarlamanın en yaygın yolu, BIR istemci SSL profili oluşturmaktır. Istemci SSL profiliyle, büyük bir IP sistemi, gelen istemci isteklerinin bir aşağı akış hizmetine göndermeden önce şifrelerini çözebilirler. Ardından, giden arka uç yanıtlarını istemcilere göndermeden önce şifreler.

- **Sunucu SSL**: https için yapılandırılmış arka uç hizmetleri IÇIN, büyük IP 'Yi BIR sunucu SSL profili kullanacak şekilde yapılandırabilirsiniz. Sunucu SSL profiliyle, büyük IP, istemci isteğini hedef arka uç hizmetine göndermeden önce yeniden şifreler. Sunucu şifreli bir yanıt döndürdüğünde, büyük IP sistemi yanıtı, istemciye göndermeden önce, yapılandırılmış Istemci SSL profili aracılığıyla çözer ve yeniden şifreler.

Hem Istemci hem de sunucu SSL profillerinin sağlanması, büyük IP 'nin tüm SHA senaryolarına önceden yapılandırılmış ve hazırlanacaktır.

1. Sol gezinti çubuğundan **sistem**  >  **sertifika yönetimi**  >  **trafiği sertifika yönetimi**  >  **SSL sertifika listesi**  >  **içeri aktarma** ' ya gidin.

2. **Içeri aktarma türü** açılan listesinden **PKCS 12 (IIS)** seçeneğini belirleyin.

3. İçeri aktarılan sertifika için bir ad sağlayın, örneğin  `ContosoWilcardCert`

4. Web 'e gitmek için **Dosya Seç** ' i seçin. ilgili adı, yayımlanmış hizmetler için kullanmayı planladığınız etki alanı sonekine karşılık gelir.

5. İçeri aktarılan sertifikanın **parolasını** girip **içeri aktar** ' ı seçin.

6. Sol gezinti çubuğundan **Yerel trafik**  >  **profilleri**  >  **SSL**  >  **istemcisi** ' ne gidin ve **Oluştur** ' u seçin.

7. **Yeni ISTEMCI SSL profili** sayfasında, yenı istemci SSL profili için benzersiz bir kolay ad belirtin ve üst profilin **Clientssl** olarak ayarlandığından emin olun

![Görüntüde büyük-IP güncelleştirmesi gösteriliyor](./media/f5ve-deployment-plan/client-ssl.png)

8. **Sertifika anahtar zinciri** satırındaki en sağdaki onay kutusunu seçin ve **Ekle** ' yi seçin.

9. Üç açılan listeden, bir parola olmadan içeri aktardığınız joker karakter sertifikasını seçin ve ardından tamamlandı **Ekle**' yi seçin  >  .

![Görüntüde büyük-IP contoso joker karakteri güncelleştirmesi gösterilmektedir](./media/f5ve-deployment-plan/contoso-wildcard.png)

10. Bir **SSL sunucu sertifikası profili** oluşturmak için 6-9 arası adımları tekrarlayın. Üst şeritte **SSL**  >  **sunucu**  >  **Oluştur**' u seçin.

11. **Yeni sunucu SSL profili** sayfasında, yenı sunucu SSL profili için benzersiz bir kolay ad belirtin ve üst profilin **serverssl** olarak ayarlandığından emin olun

12. Sertifika ve anahtar satırları için en sağdaki onay kutusunu seçin ve açılan listeden içeri aktarılan sertifikanızı ve ardından **tamamlandı**' yı seçin.

![Görüntüde büyük IP sunucu tüm profillerinin güncelleştirilmesi gösterilmektedir](./media/f5ve-deployment-plan/server-ssl-profile.png)

>[!NOTE]
>Bir SSL sertifikası temin etmezseniz, tümleşik otomatik olarak imzalanan büyük IP sunucusu ve istemci SSL sertifikalarını kullanabilirsiniz. Yalnızca tarayıcıda bir sertifika hatası görürsünüz.

SHA için büyük IP hazırlama konusunda son bir adım, yayımlamanın kaynakları ve ayrıca SSO için kullandığı dizin hizmetini bulabilmesini sağlamaktır. BÜYÜK bir IP 'nin yerel/.../ana bilgisayar dosyasıyla başlayarak iki ad çözümleme kaynağı vardır veya bir kayıt bulunamazsa büyük IP sistemi, ile yapılandırılan DNS hizmetini kullanır. Hosts dosyası yöntemi, bir FQDN kullanan APM düğümleri ve havuzlar için geçerlidir.

1. Web yapılandırmasında, **sistem**  >  **yapılandırması**  >  **cihaz**  >  **DNS** ' ye gidin

2. **DNS arama sunucusu listesinde**, ortamların DNS sunucusunun IP adresini girin

3. Güncelleştirme **Ekle**'yi seçin  >  

Ayrı ve isteğe bağlı bir adım olarak, yerel büyük IP hesaplarını yönetmek yerine ad ile büyük IP olmaktadır kimlik doğrulaması yapmak için bir [LDAP yapılandırması](https://somoit.net/f5-big-ip/authentication-using-active-directory) düşünebilirsiniz.

## <a name="update-big-ip"></a>BÜYÜK IP 'yi Güncelleştir

BÜYÜK IP sanal makinenizin, [senaryo tabanlı](f5-aad-integration.md)kılavuzda ele alınan tüm yeni işlevsellikten sonra açılması için güncelleştirilmeleri gerekir. System TMOS sürümünün fare işaretçisini ana sayfanın sol üst kısmında BIG-IPs ana bilgisayar adının üzerine getirdiğinizde emin olabilirsiniz. V15. x ve üzeri, bilgiler kişilerden [F5 Download](https://downloads.f5.com/esd/productlines.jsp)'tan çalışıyor olmanız önerilir. Ana sistem IŞLETIM sistemini (TMO 'lar) güncelleştirmek için [Kılavuzu](https://support.f5.com/csp/article/K34745165) kullanın.

Ana TMO 'lar güncelleştirilemediğinde, bu adımları kullanarak en az destekli yapılandırmayı yükseltmeyi göz önünde bulundurun.

1. Büyük IP Web yapılandırmasındaki ana sekmeden,   >  **Kılavuzlu yapılandırmaya** erişme sayfasına gidin

2. **Kılavuzlu yapılandırma** sayfasında, **Kılavuzlu yapılandırmayı Yükselt** ' i seçin.

![Görüntüde büyük IP 'nin nasıl güncelleştirilmesi gösterilmektedir](./media/f5ve-deployment-plan/update-big-ip.png)

3. **Yükseltme destekli yapılandırma** iletişim kutusunda, indirilen kullanım örneği paketini karşıya yüklemek için dosya ' yı **seçin** ve **karşıya yükle ve yükle** ' yi seçin.

4. Yükseltme tamamlandığında **devam**' ı seçin.

## <a name="backup-big-ip"></a>Yedekleme büyük-IP

BÜYÜK IP sistemi artık tam olarak sağlandığından, yapılandırmasının tam yedeklemesini yapmanızı öneririz:

1. **Sistem**  >  **arşivleri**  >  **Oluştur** 'a git

2. Benzersiz bir **dosya adı** belirtin ve parolayı parolayla **şifrelemeyi** etkinleştirin

3. **Özel anahtarlar** seçeneğini ayrıca CIHAZ ve SSL sertifikalarını yedeklemek için **dahil** etmek üzere ayarla

4. **Tamamlandı** ' ı seçin ve işlemin tamamlanmasını bekleyin

5. Yedekleme sonucu durumu sağlayan bir işlem durumu görüntülenir. **Tamam**'ı seçin

6. Yedekleme bağlantısını seçip **İndir**' i seçerek Kullanıcı yapılandırma KÜMESI (UCS) arşivini yerel olarak kaydedin.

İsteğe bağlı bir adım olarak, [Azure anlık görüntülerini](../../virtual-machines/windows/snapshot-copy-managed-disk.md)kullanarak tüm sistem diskinin bir yedeğini alabilirsiniz. bu sayede, Web yapılandırma yedeğinin, tmos sürümleri arasında test için bir acil durum sağlayacağından ya da yeni bir sisteme geri dönme anlamına gelir.

```PowerShell
# Install modules
Install-module Az
Install-module AzureVMSnapshots

# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Create Snapshot
New-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>'

#List Snapshots
#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>'

#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot -RemoveOriginalDisk 

```

## <a name="restore-big-ip"></a>BÜYÜK IP 'yi geri yükleme

BÜYÜK IP 'nin geri yüklenmesi, yedeklemeye benzer bir yordam izler ve ayrıca büyük IP VM 'Ler arasında config 'Leri geçirmek için de kullanılabilir. Desteklenen yükseltme yollarındaki ayrıntılar, bir yedek içeri aktarılmadan önce gözlenmelidir.

1. **Sistem**  >  **arşivleri** 'ne git

2. Geri yüklemek istediğiniz bir yedeğin bağlantısını seçin ya da listede olmayan daha önce kaydedilen bir UCS arşivine gitmek için **karşıya yükle** düğmesini seçin

3. Yedeklemenin parolasını girin ve **geri yükle 'yi** seçin

```PowerShell
# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Restore Snapshot
Get-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot

```

>[!NOTE]
>Yazma sırasında, AzVmSnapshot cmdlet 'i tarihe göre en son anlık görüntüyü geri yüklemeyle sınırlandırılmıştır. Anlık görüntüler, sanal makinenin kaynak grubunun kökünde saklanır. Anlık görüntülerin geri yüklenmesi bir Azure VM 'yi yeniden başlatdığının farkında olun.

## <a name="additional-resources"></a>Ek kaynaklar

-   [Azure 'da büyük IP VE parolayı sıfırlama](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html)
    -   [Portalı kullanmadan parolayı sıfırlama](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html#reset-the-password-without-using-the-portal)

-   [BÜYÜK IP VE yönetim için kullanılan NIC 'ı değiştirme](https://clouddocs.f5.com/cloud/public/v1/shared/change_mgmt_nic.html)

-   [Tek bir NIC yapılandırmasındaki rotalar hakkında](https://clouddocs.f5.com/cloud/public/v1/shared/routes.html)

-   [Microsoft Azure: waagent](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_waagent.html)

## <a name="next-steps"></a>Sonraki adımlar

Bir [dağıtım senaryosu](f5-aad-integration.md) seçin ve uygulamanızı başlatın.
