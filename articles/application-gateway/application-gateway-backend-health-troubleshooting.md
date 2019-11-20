---
title: Azure Application Gateway arka uç sistem durumu sorunlarını giderme
description: Azure Application Gateway için arka uç sistem durumu sorunlarını nasıl giderebileceğinizi açıklar
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: 71e1f8be2af5556d86996175e8a1ddbccc9c7de1
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001673"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Application Gateway arka uç sistem durumu sorunlarını giderme
==================================================

<a name="overview"></a>Genel Bakış
--------

Varsayılan olarak Azure Application Gateway, arka uç sunucularını inceleyerek sistem durumunu denetler ve istekleri sunmaya hazır olup olmadığını kontrol eder. Kullanıcılar ayrıca ana bilgisayar adından, araştırılan yoldan ve durum kodlarından sağlıklı olarak kabul edileceği özel yoklamalar oluşturabilir. Her durumda, arka uç sunucusu başarıyla yanıt vermezse, Application Gateway sunucuyu sağlıksız olarak işaretler ve istekleri sunucuya iletmeyi sonlandırır. Sunucu başarıyla yanıt vermeye başladıktan sonra Application Gateway istekleri iletmeyi sürdürür.

### <a name="how-to-check-backend-health"></a>Arka uç durumunu denetleme

Arka uç havuzunuzun durumunu denetlemek için Azure portal **arka uç sistem durumu** sayfasını kullanabilirsiniz. Ya da [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0), [CLI](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)veya [REST API](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth)kullanabilirsiniz.

Bu yöntemlerin herhangi biri tarafından alınan durum aşağıdakilerden biri olabilir:

- Sorunsuz

- Sağlıksız

- Bilinmiyor

Bir sunucunun arka uç sistem durumu sağlıklı ise, Application Gateway istekleri bu sunucuya iletmeyeceği anlamına gelir. Ancak arka uç havuzundaki tüm sunucuların arka uç durumu sağlıksız veya bilinmiyorsa, uygulamalara erişmeye çalıştığınızda sorunlarla karşılaşabilirsiniz. Bu makalede, gösterilen hataların her biri için belirtiler, neden ve çözüm açıklanmaktadır.

<a name="backend-health-status-unhealthy"></a>Arka uç sistem durumu: sağlıksız
-------------------------------

Arka uç sistem durumu sağlıksız ise, Portal görünümü aşağıdaki ekran görüntüsüne benzer:

![Application Gateway arka uç durumu-sağlıksız](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Ya da bir Azure PowerShell, CLı veya Azure REST API sorgusu kullanıyorsanız, aşağıdakine benzer bir yanıt alırsınız:
```azurepowershell
PS C:\Users\testuser\> Get-AzApplicationGatewayBackendHealth -Name "appgw1" -ResourceGroupName "rgOne"
BackendAddressPools :
{Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayBackendHealthPool}
BackendAddressPoolsText : [
{
                              "BackendAddressPool": {
                                "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appgw1/b
                          ackendAddressPools/appGatewayBackendPool"
                              },
                              "BackendHttpSettingsCollection": [
                                {
                                  "BackendHttpSettings": {
                                    "TrustedRootCertificates": [],
                                    "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appg
                          w1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
                                  },
                                  "Servers": [
                                    {
                                      "Address": "10.0.0.5",
                                      "Health": "Healthy"
                                    },
                                    {
                                      "Address": "10.0.0.6",
                                      "Health": "Unhealthy"
                                    }
                                  ]
                                }
                              ]
                            }
                        ]
```
Bir arka uç havuzundaki tüm sunucular için sağlıklı olmayan bir arka uç sunucu durumu aldıktan sonra, istekler sunuculara iletilmez ve Application Gateway istekte bulunan istemciye bir "502 hatalı Ağ Geçidi" hatası döndürür. Bu sorunu gidermek için, **arka uç sistem durumu** sekmesindeki **Ayrıntılar** sütununu kontrol edin.

**Ayrıntılar** sütununda görünen ileti, sorun hakkında daha ayrıntılı öngörüler sağlar ve bunlar temelinde sorun gidermeye başlayabilirsiniz.

> [!NOTE]
> Varsayılan araştırma isteği, \<protocol @ no__t-1://127.0.0.1: \<Port @ no__t-3/biçiminde gönderilir. Örneğin, 80 numaralı bağlantı noktasında http araştırması için http://127.0.0.1:80. Yalnızca 200 ile 399 arasındaki HTTP durum kodları sağlıklı olarak değerlendirilir. Protokol ve hedef bağlantı noktası HTTP ayarlarından devralınır. Application Gateway farklı bir protokol, ana bilgisayar adı veya yol üzerinde araştırma yapmak ve farklı bir durum kodunu sağlıklı olarak tanımak istiyorsanız, özel bir araştırma yapılandırın ve bunu HTTP ayarları ile ilişkilendirin.

<a name="error-messages"></a>Hata iletileri
------------------------
#### <a name="backend-server-timeout"></a>Arka uç sunucu zaman aşımı

**İleti:** Arka uçta uygulama ağ geçidine yanıt vermek için geçen süre @ no__t-1s sistem durumu araştırması, araştırma ayarındaki zaman aşımı eşiğinden daha fazla.

**Neden:** Application Gateway, arka uç sunucusuna bir HTTP (S) araştırma isteği gönderdikten sonra, yapılandırılan bir süre için arka uç sunucusundan bir yanıt bekler. Arka uç sunucusu yapılandırılan süre içinde yanıt vermiyorsa (zaman aşımı değeri), yapılandırılan zaman aşımı süresi içinde yeniden yanıt vermemeye başlamasına kadar sağlıksız olarak işaretlenir.

**Çözüm:** Arka uç sunucusunun veya uygulamanın yapılandırılan zaman aşımı süresi içinde neden yanıt vermediğini denetleyin ve ayrıca uygulama bağımlılıklarını denetleyin. Örneğin, veritabanında yanıt olarak bir gecikme tetikleyebilen herhangi bir sorun olup olmadığını denetleyin. Uygulamanın davranışının farkındaysanız ve yalnızca zaman aşımı değerinden sonra yanıt vermesi gerekiyorsa, özel araştırma ayarlarından zaman aşımı değerini artırın. Zaman aşımı değerini değiştirmek için özel bir araştırmanız olması gerekir. Özel bir araştırmanın nasıl yapılandırılacağı hakkında daha fazla bilgi için [Belgeler sayfasına bakın](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

Zaman aşımı değerini artırmak için aşağıdaki adımları izleyin:

1.  Arka uç sunucusuna doğrudan erişin ve sunucunun bu sayfada yanıt vermesi için geçen süreyi denetleyin. Geliştirici araçlarını kullanan bir tarayıcı da dahil olmak üzere arka uç sunucusuna erişmek için herhangi bir aracı kullanabilirsiniz.

1.  Uygulamanın yanıt vermesi için geçen süreyi iletişime ettikten sonra, **sistem durumu araştırmaları** sekmesini seçin ve ardından http ayarlarınızla ilişkili araştırmayı seçin.

1.  Uygulama yanıt süresinden daha büyük bir zaman aşımı değerini saniye cinsinden girin.

1.  Özel araştırma ayarlarını kaydedin ve arka uç durumunun şimdi sağlıklı olarak görünüp gösterilmediğini denetleyin.

#### <a name="dns-resolution-error"></a>DNS çözümleme hatası

**İleti:** Application Gateway bu arka uç için bir araştırma oluşturamadı. Bu genellikle arka ucun FQDN 'SI doğru girildiyse oluşur. 

**Neden:** Arka uç havuzu IP adresi/FQDN veya App Service türünde ise, Application Gateway etki alanı adı sistemi (DNS) ile girilen FQDN 'nin IP adresine (özel veya Azure varsayılan) çözülür ve HTTP ayarlarında belirtilen TCP bağlantı noktasındaki sunucuya bağlanmaya çalışır. Ancak bu ileti görüntüleniyorsa Application Gateway, girilen FQDN 'nin IP adresini başarıyla çözümleyemediğini önerir.

**Çözünürlüğüne**

1.  Arka uç havuzunda girilen FQDN 'nin doğru olduğundan ve ortak bir etki alanı olduğundan emin olun ve ardından yerel makinenizden çözümlemeyi deneyin.

1.  IP adresini çözümleyebiliyorsanız, sanal ağda DNS yapılandırmasında bir sorun olabilir.

1.  Sanal ağın özel bir DNS sunucusu ile yapılandırılıp yapılandırılmadığını denetleyin. Varsa, DNS sunucusunu, belirtilen FQDN 'nin IP adresine neden çözemediğini denetleyin.

1.  Azure varsayılan DNS kullanıyorsanız, uygun bir kayıt veya CNAME kaydı eşlemesinin tamamlanıp tamamlanmadığını öğrenmek için etki alanı adı kayıt şirketinize danışın.

1.  Etki alanı özel veya iç ise, aynı sanal ağ içindeki bir VM 'den çözümlemeyi deneyin. Sorunu çözebiliyorsanız Application Gateway yeniden başlatın ve yeniden denetleyin. Application Gateway yeniden başlatmak için, bu bağlı kaynaklarda açıklanan PowerShell komutlarını kullanarak [durdurmanız](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0) ve [başlatmanız](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0) gerekir.

#### <a name="tcp-connect-error"></a>TCP bağlantı hatası

**İleti:** Application Gateway arka uca bağlanamadı.
Lütfen arka ucun araştırma için kullanılan bağlantı noktasında yanıt verdiğini kontrol edin.
Ayrıca, herhangi bir NSG/UDR/güvenlik duvarının IP 'ye ve bu arka ucun bağlantı noktasına erişimi engelleyip engellemediğini denetleyin

**Neden:** DNS çözümleme aşamasından sonra, Application Gateway HTTP ayarlarında yapılandırılan TCP bağlantı noktasındaki arka uç sunucusuna bağlanmaya çalışır. Application Gateway belirtilen bağlantı noktasında TCP oturumu kuramazsa, araştırma Bu iletiyle sağlıksız olarak işaretlenir.

**Çözüm:** Bu hatayı alırsanız, şu adımları izleyin:

1.  Tarayıcı veya PowerShell kullanarak HTTP ayarlarında bahsedilen bağlantı noktasında arka uç sunucusuna bağlanıp bağlanamaıp bağlanamayacağını denetleyin. Örneğin, şu komutu çalıştırın: `Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  Belirtilen bağlantı noktası istenen bağlantı noktası değilse, arka uç sunucusuna bağlanmak için Application Gateway doğru bağlantı noktası numarasını girin

1.  Bağlantı noktasında yerel makinenizden de bağlanamadıysanız:

    a.  Arka uç sunucusunun ağ bağdaştırıcısının ve alt ağının ağ güvenlik grubu (NSG) ayarlarını ve yapılandırılmış bağlantı noktasına gelen bağlantılara izin verilip verilmediğini denetleyin. Bu değillerse, bağlantılara izin vermek için yeni bir kural oluşturun. NSG kuralları oluşturmayı öğrenmek için [Belgeler sayfasına bakın](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules).

    b.  Application Gateway alt ağın NSG ayarlarının giden genel ve özel trafiğe izin verip vermediği ve bir bağlantının yapılabilmesi için denetleyin. NSG kuralları oluşturma hakkında daha fazla bilgi edinmek için, adım 3A ' da sunulan belge sayfasını kontrol edin.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Tüm yönlendirme bozuklukları için Application Gateway ve arka uç sunucusunun alt ağının Kullanıcı tanımlı yollar (UDR) ayarlarını kontrol edin. UDR 'nin trafiği arka uç alt ağından uzağa yönlendirmediğinden emin olun. Örneğin, Azure ExpressRoute ve/veya VPN aracılığıyla Application Gateway alt ağına tanıtılan ağ sanal gereçlerine veya varsayılan yollara yönelik yolları denetleyin.

    d.  Bir ağ bağdaştırıcısının geçerli yollarını ve kurallarını denetlemek için aşağıdaki PowerShell komutlarını kullanabilirsiniz:
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  NSG veya UDR ile ilgili herhangi bir sorun bulamazsanız, istemcilerin yapılandırılmış bağlantı noktalarında bir TCP oturumu kurmasını engelleyen uygulamayla ilgili sorunlar için arka uç sunucunuzu kontrol edin. Denetlenecek birkaç şey:

    a.  Bir komut istemi açın (Win + R-\> cmd), `netstat` girin ve ENTER ' u seçin.

    b.  Sunucunun yapılandırılmış bağlantı noktasında dinleme yapıp yapmadığını denetleyin. Örnek:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  Yapılandırılmış bağlantı noktasında dinleme yapmadıysa, Web sunucusu ayarlarınızı kontrol edin. Örneğin: IIS 'de site bağlamaları, NGıNX içindeki sunucu bloğu ve Apache 'de sanal ana bilgisayar.

    d.  Bağlantı noktasına gelen trafiğe izin verildiğinden emin olmak için işletim sistemi güvenlik duvarı ayarlarınızı denetleyin.

#### <a name="http-status-code-mismatch"></a>HTTP durum kodu uyumsuzluğu

**İleti:** Arka uç @ no__t-1s HTTP yanıtının durum kodu araştırma ayarıyla eşleşmedi. Beklenen: {HTTPStatusCode0} alındı: {HTTPStatusCode1}.

**Neden:** TCP bağlantısı kurulduktan ve bir SSL el sıkışması yapıldıktan sonra (SSL etkinse), Application Gateway arka uç sunucusuna bir HTTP GET isteği olarak araştırmaya gönderilir. Daha önce açıklandığı gibi, varsayılan araştırma \<protocol @ no__t-1://127.0.0.1: \<Port @ no__t-3/olur ve 200 ile 399 arasındaki yanıt durum kodlarını sağlıklı olarak değerlendirir. Sunucu başka bir durum kodu döndürürse, Bu iletiyle sağlıksız olarak işaretlenir.

**Çözüm:** Arka uç sunucusunun yanıt koduna bağlı olarak, aşağıdaki adımları gerçekleştirebilirsiniz. Yaygın durum kodlarından bazıları aşağıda listelenmiştir:

| **Hatayla** | **Eylemler** |
| --- | --- |
| Araştırma durum kodu uyumsuzluğu: 401 alındı | Arka uç sunucusunun kimlik doğrulaması gerektirip gerektirmediğini denetleyin. Application Gateway araştırmaları bu noktada kimlik doğrulaması için kimlik bilgilerini geçiremiyor. Araştırma durum kodu eşleşmesi veya araştırmasının sunucu kimlik doğrulaması gerektirmeyen bir yola göre \"HTTP 401 @ no__t-1 ' e izin verin. | |
| Araştırma durum kodu uyumsuzluğu: 403 alındı | Erişim yasak. Arka uç sunucusunda yola erişime izin verilip verilmediğini denetleyin. | |
| Araştırma durum kodu uyumsuzluğu: 404 alındı | Sayfa bulunamadı. Ana bilgisayar adı yolunun arka uç sunucusunda erişilebilir olup olmadığını denetleyin. Ana bilgisayar adı veya yol parametresini erişilebilir bir değer olarak değiştirin. | |
| Araştırma durum kodu uyumsuzluğu: 405 alındı | Application Gateway için araştırma istekleri HTTP GET yöntemini kullanır. Sunucunuzun bu yöntemin izin verip içermediğini denetleyin. | |
| Araştırma durum kodu uyumsuzluğu: 500 alındı | İç sunucu hatası. Arka uç sunucusunun sistem durumunu ve hizmetlerin çalışıp çalışmadığını denetleyin. | |
| Araştırma durum kodu uyumsuzluğu: 503 alındı | Hizmet kullanılamıyor. Arka uç sunucusunun sistem durumunu ve hizmetlerin çalışıp çalışmadığını denetleyin. | |

Ya da yanıtın meşru olduğunu düşünüyorsanız ve Application Gateway diğer durum kodlarını sağlıklı olarak kabul etmek istiyorsanız, özel bir araştırma oluşturabilirsiniz. Bu yaklaşım, arka uç Web sitesinin kimlik doğrulaması gereken durumlarda yararlıdır. Araştırma istekleri herhangi bir kullanıcı kimlik bilgisi taşımadığından, bunlar başarısız olur ve arka uç sunucusu tarafından bir HTTP 401 durum kodu döndürülür.

Özel bir araştırma oluşturmak için [aşağıdaki adımları](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)izleyin.

#### <a name="http-response-body-mismatch"></a>HTTP yanıt gövdesi uyumsuzluğu

**İleti:** Arka uç @ no__t-1s HTTP yanıtının gövdesi araştırma ayarıyla eşleşmedi. Alınan yanıt gövdesi {String} içermiyor.

**Neden:** Özel bir araştırma oluşturduğunuzda, yanıt gövdesinden bir dizeyi eşleştirerek arka uç sunucusunu sağlıklı olarak işaretleme seçeneğiniz vardır. Örneğin, Application Gateway eşleşecek bir dize olarak "yetkisiz" kabul edecek şekilde yapılandırabilirsiniz. Araştırma isteği için arka uç sunucu yanıtı, dizeyi **yetkisiz**Içeriyorsa, sağlıklı olarak işaretlenir. Aksi takdirde, Bu iletiyle sağlıksız olarak işaretlenir.

**Çözüm:** Bu sorunu çözmek için şu adımları izleyin:

1.  Arka uç sunucusuna veya araştırma yolundaki bir istemci makineden yerel olarak erişin ve yanıt gövdesini denetleyin.

1.  Application Gateway özel araştırma yapılandırmasındaki yanıt gövdesinin yapılandırılandıklarınızı eşleştirdiğini doğrulayın.

1.  Eşleşiyorlarsa, araştırma yapılandırmasını, kabul edilecek doğru dize değerine sahip olacak şekilde değiştirin.

[Application Gateway araştırma eşleştirmesi](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)hakkında daha fazla bilgi edinin.

#### <a name="backend-server-certificate-invalid-ca"></a>Arka uç sunucu sertifikası geçersiz CA

**İleti:** Arka uç tarafından kullanılan sunucu sertifikası iyi bilinen bir sertifika yetkilisi (CA) tarafından imzalanmadı. Arka uç tarafından kullanılan sunucu sertifikasının kök sertifikasını karşıya yükleyerek Application Gateway arka ucunu beyaz listeye ekleyin.

**Neden:** Application Gateway v2 ile uçtan uca SSL, sunucunun sağlıklı bir şekilde çıkarılması için arka uç sunucusunun sertifikasının doğrulanmasını gerektirir.
SSL sertifikasının güvenilir olması için, arka uç sunucusunun bu sertifikasının Application Gateway güvenilen deposunda bulunan bir CA tarafından verilmesi gerekir. Sertifika, güvenilir bir CA tarafından verilmediyse (örneğin, otomatik olarak imzalanan bir sertifika kullanılmışsa), kullanıcıların sertifikayı verenin sertifikasını Application Gateway yüklemesi gerekir.

**Çözüm:** Güvenilen kök sertifikayı dışarı ve Application Gateway yüklemek için aşağıdaki adımları izleyin. (Bu adımlar Windows istemcileri içindir.)

1.  Uygulamanızın barındırıldığı makinede oturum açın.

1.  Win + R ' yi seçin veya **Başlat** düğmesine sağ tıklayıp **Çalıştır**' ı seçin.

1.  @No__t-0 girin ve ENTER ' u seçin. Ayrıca, **Başlangıç** menüsünde Sertifika Yöneticisi 'ni de arayabilirsiniz.

1.  Genellikle `\Certificates - Current User\\Personal\\Certificates\` ' da sertifikayı bulun ve açın.

1.  Kök sertifikayı seçin ve ardından **sertifikayı görüntüle**' yi seçin.

1.  Sertifika Özellikleri ' nde **Ayrıntılar** sekmesini seçin.

1.  **Ayrıntılar** sekmesinde **Dosyaya Kopyala** seçeneğini belirleyin ve dosyayı Base-64 ile kodlanmış X. 509.440 (. CER) biçiminde.

1.  Azure portal Application Gateway HTTP **ayarları** sayfasını açın.

1. HTTP ayarlarını açın, **sertifika ekle**' yi seçin ve az önce kaydettiğiniz sertifika dosyasını bulun.

1. HTTP ayarlarını kaydetmek için **Kaydet** ' i seçin.

Alternatif olarak, sunucuya doğrudan erişerek (Application Gateway atlayarak) kök sertifikayı tarayıcıdan dışarı aktarabilir ve kök sertifikayı tarayıcıdan dışarı aktarabilirsiniz.

Güvenilen kök sertifikaların Application Gateway nasıl ayıklanıp yüklenemediği hakkında daha fazla bilgi için bkz. [Güvenilen kök sertifikayı dışarı aktarma (v2 SKU 'su için)](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).

#### <a name="trusted-root-certificate-mismatch"></a>Güvenilen kök sertifika uyumsuzluğu

**İleti:** Arka uç tarafından kullanılan sunucu sertifikasının kök sertifikası, uygulama ağ geçidine eklenmiş olan güvenilen kök sertifika ile eşleşmiyor. Arka ucun beyaz listeye doğru kök sertifikayı eklemediğinizden emin olun

**Neden:** Application Gateway v2 ile uçtan uca SSL, sunucunun sağlıklı bir şekilde çıkarılması için arka uç sunucusunun sertifikasının doğrulanmasını gerektirir.
SSL sertifikasının güvenilir olması için, arka uç sunucu sertifikasının, Application Gateway güvenilen deposunda bulunan bir CA tarafından verilmesi gerekir. Sertifika, güvenilir bir CA (örneğin, kendinden imzalı bir sertifika kullanıldıysa) tarafından verilmediyse, kullanıcıların sertifikayı verenin sertifikasını Application Gateway yüklemesi gerekir.

HTTP ayarlarına Application Gateway yüklenen sertifika, arka uç sunucu sertifikasının kök sertifikasıyla eşleşmelidir.

**Çözüm:** Bu hata iletisini alırsanız, Application Gateway yüklenen sertifika ile arka uç sunucusuna yüklenmiş olan sertifika arasında bir uyumsuzluk vardır.

Application Gateway, doğru güvenilen kök sertifikayı karşıya yüklemek için yukarıdaki yöntemdeki 1-11 adımlarını izleyin.

Güvenilen kök sertifikaların Application Gateway nasıl ayıklanıp yüklenemediği hakkında daha fazla bilgi için bkz. [Güvenilen kök sertifikayı dışarı aktarma (v2 SKU 'su için)](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).
> [!NOTE]
> Bu hata, arka uç sunucusu, TLS el sıkışması sırasında kök > ara (varsa) > yaprak dahil olmak üzere tüm sertifika zincirini değişmediğinde meydana gelebilir. Doğrulamak için herhangi bir istemciden OpenSSL komutlarını kullanabilir ve Application Gateway araştırmakta yapılandırılan ayarları kullanarak arka uç sunucusuna bağlanabilirsiniz.

Örnek:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Çıktı, döndürülmekte olan sertifikanın tüm zincirini göstermezse, sertifikayı Kök sertifika da dahil olmak üzere tüm zincirle yeniden dışarı aktarın. Bu sertifikayı arka uç sunucunuzda yapılandırın. 

```
  CONNECTED(00000188)\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=20:unable to get local issuer certificate\
  verify return:1\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=21:unable to verify the first certificate\
  verify return:1\
  \-\-\-\
  Certificate chain\
   0 s:/OU=Domain Control Validated/CN=*.example.com\
     i:/C=US/ST=Arizona/L=Scottsdale/O=GoDaddy.com, Inc./OU=http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority - G2\
  \-----BEGIN CERTIFICATE-----\
  xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
  \-----END CERTIFICATE-----
```

#### <a name="backend-certificate-invalid-common-name-cn"></a>Arka uç sertifikası geçersiz ortak adı (CN)

**İleti:** Arka uç sertifikasının ortak adı (CN), araştırmanın ana bilgisayar üstbilgisiyle eşleşmiyor.

**Neden:** Application Gateway, arka uç HTTP ayarlarında belirtilen ana bilgisayar adının, arka uç sunucusunun SSL sertifikası tarafından sunulan CN ile eşleşip eşleşmediğini denetler. Bu, Standard_v2 ve WAF_v2 SKU davranışıdır. Standart ve WAF SKU 'nun Sunucu Adı Belirtme (SNı), arka uç havuzu adresinde FQDN olarak ayarlanır.

V2 SKU 'sunda, varsayılan bir yoklama varsa (özel araştırma yapılandırılmamışsa ve ilişkili değilse), SNı HTTP ayarlarında belirtilen ana bilgisayar adından ayarlanır. Veya, arka uç adres havuzunun geçerli bir FQDN içerdiği HTTP ayarlarında "arka uç adresinden ana bilgisayar adı Seç" bölümünde bahsedildiğinde, bu ayar geçerli olur.

HTTP ayarlarıyla ilişkili özel bir araştırma varsa, SNı özel araştırma yapılandırmasında belirtilen ana bilgisayar adından ayarlanacaktır. Ya da özel **Yoklamede arka uç http ayarlarından bir ana bilgisayar** adı seçilirse, SNI http ayarlarında belirtilen ana bilgisayar adından ayarlanır.

HTTP ayarlarında **arka uç adresinden seçim ana bilgisayar adı** ayarlandıysa, arka uç adres havuzunun GEÇERLI bir FQDN içermesi gerekir.

Bu hata iletisini alırsanız, arka uç sertifikasının CN 'si özel araştırmakta veya HTTP ayarlarında yapılandırılan ana bilgisayar adıyla eşleşmez ( **arka uca http ayarlarından seçim ana bilgisayar** adı seçiliyse). Varsayılan bir araştırma kullanıyorsanız, ana bilgisayar adı **127.0.0.1**olarak ayarlanır. Bu istenen bir değer değilse, özel bir araştırma oluşturmanız ve bunu HTTP ayarlarıyla ilişkilendirmeniz gerekir.

**Çözüm:**

Sorunu çözmek için aşağıdaki adımları izleyin.

Windows için:

1.  Uygulamanızın barındırıldığı makinede oturum açın.

1.  Win + R ' yi seçin veya **Başlat** düğmesine sağ tıklayıp **Çalıştır**' ı seçin.

1.  **Certmgr. msc** girin ve ENTER ' u seçin. Ayrıca, **Başlangıç** menüsünde Sertifika Yöneticisi 'ni de arayabilirsiniz.

1.  Sertifikayı bulun (genellikle `\Certificates - Current User\\Personal\\Certificates`) ve sertifikayı açın.

1.  **Ayrıntılar** sekmesinde, sertifika **konusunu**kontrol edin.

1.  Ayrıntılardan sertifikanın CN 'sini doğrulayın ve özel araştırmanın ana bilgisayar adı alanına veya HTTP ayarları ' na ( **arka uç http ayarlarından seçim ana bilgisayar** adı seçiliyse) yazın. Bu, Web siteniz için istenen konak adı değilse, bu etki alanı için bir sertifika almanız veya özel araştırmasına veya HTTP ayarı yapılandırmasına doğru ana bilgisayar adını girmeniz gerekir.

OpenSSL kullanan Linux için:

1.  OpenSSL 'de şu komutu çalıştırın:
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  Görüntülenözellikler ' de, sertifikanın CN 'sini bulun ve http ayarlarının ana bilgisayar adı alanına aynısını girin. Bu, Web siteniz için istenen konak adı değilse, bu etki alanı için bir sertifika almanız veya özel araştırmasına veya HTTP ayarı yapılandırmasına doğru ana bilgisayar adını girmeniz gerekir.

#### <a name="backend-certificate-is-invalid"></a>Arka uç sertifikası geçersiz

**İleti:** Arka uç sertifikası geçersiz. Geçerli tarih, @ no__t-1 ve \" ' den geçerli @no__t, sertifikadaki @ no__t-3 tarih aralığı içinde değil.

**Neden:** Her sertifika bir geçerlilik aralığıyla birlikte gelir ve sunucunun SSL sertifikası geçerli olmadığı takdirde HTTPS bağlantısı güvende olmaz. Geçerli veriler, **öğesinden geçerli** ve **geçerli** bir aralık dahilinde olmalıdır. Aksi takdirde, sertifika geçersiz olarak kabul edilir ve Application Gateway arka uç sunucusunu sağlıksız olarak işaretleyen bir güvenlik sorunu oluşturur.

**Çözüm:** SSL sertifikanızın süresi dolmuşsa, sertifikayı satıcınızla yenileyin ve sunucu ayarlarını yeni sertifikayla güncelleştirin. Otomatik olarak imzalanan bir sertifika ise, geçerli bir sertifika oluşturmanız ve kök sertifikayı Application Gateway HTTP ayarlarına yüklemeniz gerekir. Bunu yapmak için şu adımları uygulayın:

1.  Application Gateway HTTP ayarlarınızı portalda açın.

1.  Süresi biten sertifikayı içeren ayarı seçin, **sertifika ekle**' yi seçin ve yeni sertifika dosyasını açın.

1.  Sertifikanın yanındaki **Sil** simgesini kullanarak eski sertifikayı kaldırın ve ardından **Kaydet**' i seçin.

#### <a name="certificate-verification-failed"></a>Sertifika doğrulaması başarısız oldu

**İleti:** Arka uç sertifikasının geçerliliği doğrulanamadı. Nedenini öğrenmek için, {errorCode} hata koduyla ilişkili ileti için SSL tanılamayı aç ' ı işaretleyin

**Neden:** Application Gateway sertifikanın geçerliliğini doğrulayamadığı zaman bu hata oluşur.

**Çözüm:** Bu sorunu çözmek için, sunucunuzdaki sertifikanın düzgün bir şekilde oluşturulduğunu doğrulayın. Örneğin, sertifikayı ve özelliklerini doğrulamak için [OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) 'yi kullanabilir ve sonra SERTIFIKAYı Application Gateway http ayarlarına yeniden yüklemeyi deneyebilirsiniz.

<a name="backend-health-status-unknown"></a>Arka uç sistem durumu: bilinmiyor
-------------------------------
Arka uç sistem durumu bilinmiyor olarak gösteriliyorsa, Portal görünümü aşağıdaki ekran görüntüsüne benzer olacaktır:

![Application Gateway arka uç durumu-bilinmiyor](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Bu davranış, aşağıdakilerden biri veya birkaçı nedeniyle oluşabilir:

1.  Application Gateway alt ağındaki NSG, "Internet" üzerinden 65503-65534 (v1 SKU) veya 65200-65535 (v2 SKU) bağlantı noktalarına gelen erişimi engelliyor.
1.  Application Gateway alt ağındaki UDR, varsayılan yol (0.0.0.0/0) olarak ayarlanır ve sonraki atlama "Internet" olarak belirtilmez.
1.  Varsayılan yol, BGP üzerinden sanal ağa yönelik bir ExpressRoute/VPN bağlantısı tarafından tanıtılabilir.
1.  Özel DNS sunucusu, ortak etki alanı adlarını giderebilecek bir sanal ağ üzerinde yapılandırılır.
1.  Application Gateway sağlıksız bir durumda.

**Çözüm:**

1.  NSG 'nizin **Internet**'ten 65503-65534 (v1 SKU) veya 65200-65535 (v2 SKU) bağlantı noktalarına erişimi engelleyip engellemediğini denetleyin:

    a.  Application Gateway **genel bakış** sekmesinde **sanal ağ/alt ağ** bağlantısını seçin.

    b.  Sanal ağınızın **alt ağlar** sekmesinde Application Gateway dağıtıldığı alt ağı seçin.

    c.  Herhangi bir NSG 'nin yapılandırılıp yapılandırılmadığını denetleyin.

    d.  Bir NSG yapılandırılmışsa, **arama** sekmesinde veya **tüm kaynaklar**' da bu NSG kaynağını arayın.

    e.  **Gelen kuralları** bölümünde, v1 SKU 'su veya 65200-65535 v2 SKU 'su için 65503-65534 hedef bağlantı noktası aralığına, **herhangi bir** veya **Internet**olarak **kaynak** kümesiyle izin veren bir gelen kuralı ekleyin.

    f.  **Kaydet** ' i seçin ve arka ucunu sağlıklı olarak görüntüleyebildiğinizi doğrulayın. Alternatif olarak, bunu [PowerShell/CLI](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)aracılığıyla yapabilirsiniz.

1.  UDR 'nizin bir sonraki atlama ile **Internet**olarak ayarlanmamış bir varsayılan yolu (0.0.0.0/0) olup olmadığını denetleyin:
    
    a.  Alt ağını öğrenmek için 1a ve 1b adımlarını izleyin.

    b.  Yapılandırılmış herhangi bir UDR olup olmadığını kontrol edin. Varsa, arama çubuğunda veya **tüm kaynaklar**altında kaynağı arayın.

    c.  Sonraki atlama ile **Internet**olarak ayarlanmamış varsayılan yolların (0.0.0.0/0) olup olmadığını denetleyin. Ayar **Sanal Gereç** ya da **sanal ağ geçidi**ise, sanal gerecinizin veya şirket içi cihazın paketi değiştirmeden paketi Internet hedefine doğru şekilde yönlendirebileceğine emin olmanız gerekir.

    d.  Aksi halde, bir sonraki atlamayı **Internet**olarak değiştirin, **Kaydet**' i seçin ve arka uç durumunu doğrulayın.

1.  BGP üzerinden sanal ağa ExpressRoute/VPN bağlantısı tarafından tanıtılan varsayılan yol:

    a.  BGP üzerinden sanal ağa yönelik bir ExpressRoute/VPN bağlantınız varsa ve varsayılan bir yol tanıtıyorsa, paketin değişiklik yapılmadan internet hedefine geri yönlendirildiğinden emin olmanız gerekir. Application Gateway portalında **bağlantı sorunlarını giderme** seçeneğini kullanarak doğrulayabilirsiniz.

    b.  1\.1.1.1 gibi internet yönlendirilebilir IP adresi olarak hedefi el ile seçin. Hedef bağlantı noktasını bir şey olarak ayarlayın ve bağlantıyı doğrulayın.

    c.  Sonraki atlama sanal ağ geçidi ise, ExpressRoute veya VPN üzerinden tanıtılan bir varsayılan yol olabilir.

1.  Sanal ağda yapılandırılmış özel bir DNS sunucusu varsa, sunucunun (veya sunucularının) ortak etki alanlarını çözümleyebildiğini doğrulayın. Application Gateway, OCSP sunucuları gibi dış etki alanlarına ulaşabilmesi veya sertifikanın iptal durumunu denetlemesi gerektiği senaryolarda ortak etki alanı adı çözümlemesi gerekebilir.

1.  Application Gateway sağlıklı ve çalışır durumda olduğunu doğrulamak için, portalda **kaynak durumu** seçeneğine gidin ve durumun **sağlıklı**olduğunu doğrulayın. **Sağlıksız** veya **düşürülmüş** bir durum görürseniz [desteğe başvurun](https://azure.microsoft.com/support/options/).

<a name="next-steps"></a>Sonraki adımlar
----------

[Tanılama ve günlüğe kaydetme Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)hakkında daha fazla bilgi edinin.
