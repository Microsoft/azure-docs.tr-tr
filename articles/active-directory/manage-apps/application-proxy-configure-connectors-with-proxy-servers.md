---
title: Mevcut şirket içi proxy sunucularıyla ve Azure AD ile çalışma | Microsoft Docs
description: Mevcut şirket içi proxy sunucularıyla çalışmayı ele alır.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: d305f3354e7b1af6d43f31f0dd5fe9f54ef3e66f
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242287"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Mevcut şirket içi proxy sunucularıyla çalışma

Bu makalede, Azure Active Directory (Azure AD) uygulama proxy bağlayıcılarının giden proxy sunucularıyla çalışacak şekilde nasıl yapılandırılacağı açıklanmaktadır. Mevcut proxy 'leri olan ağ ortamları olan müşterilere yöneliktir.

Şu ana dağıtım senaryolarına bakarak başlayacağız:

* Bağlayıcıları şirket içi giden proxy 'larınızı atlayacak şekilde yapılandırın.
* Bağlayıcıları Azure AD Uygulama Ara Sunucusu erişmek için bir giden proxy kullanacak şekilde yapılandırın.

Bağlayıcıların nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Azure AD uygulama ara sunucusu bağlayıcıları anlama](application-proxy-connectors.md).

## <a name="bypass-outbound-proxies"></a>Giden proxy 'leri atla

Bağlayıcılar, giden istekleri yapan temel işletim sistemi bileşenlerine sahiptir. Bu bileşenler, Web proxy otomatik bulma (WPAD) kullanarak ağ üzerindeki bir proxy sunucusunu otomatik olarak bulmayı dener.

İşletim sistemi bileşenleri, WPAD. domainsuffix için bir DNS araması gerçekleştirerek bir ara sunucu bulmaya çalışır. Arama DNS 'de çözümlenirse, WPAD. dat için IP adresine bir HTTP isteği yapılır. Bu istek, ortamınızda ara sunucu yapılandırma betiği haline gelir. Bağlayıcı, giden bir ara sunucu seçmek için bu betiği kullanır. Bununla birlikte, ara sunucuda gereken ek yapılandırma ayarları nedeniyle bağlayıcı trafiği yine de gidemeyebilir.

Bağlayıcıyı, Azure hizmetlerine doğrudan bağlantı kullandığından emin olmak için şirket içi ara sunucusunu atlayacak şekilde yapılandırabilirsiniz. Bu yaklaşım, Ağ ilkeniz izin verdiği sürece, devam etmek için daha az bir yapılandırmaya sahip olduğunuz anlamına gelir.

Bağlayıcı için giden proxy kullanımını devre dışı bırakmak için, C:\Program Files\Microsoft AAD uygulama proxy Connector\ApplicationProxyConnectorService.exe.config dosyasını düzenleyin ve bu kod örneğinde gösterilen *System.net* bölümünü ekleyin:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>
    <defaultProxy enabled="false"></defaultProxy>
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Bağlayıcı Güncelleştiricisi hizmetinin proxy 'yi de atlayacak emin olmak için, ApplicationProxyConnectorUpdaterService. exe. config dosyasında benzer bir değişiklik yapın. Bu dosya C:\Program Files\Microsoft AAD App proxy Bağlayıcı Güncelleştiricisi konumundadır.

Varsayılan. config dosyalarına döndürmeniz gerekiyorsa, özgün dosyaların kopyalarını aldığınızdan emin olun.

## <a name="use-the-outbound-proxy-server"></a>Giden proxy sunucusunu kullanma

Bazı ortamlar, tüm giden trafiğin özel durum olmadan giden bir proxy üzerinden gitmesini gerektirir. Sonuç olarak, proxy 'yi atlamak bir seçenek değildir.

Bağlayıcı trafiğini, aşağıdaki diyagramda gösterildiği gibi giden proxy üzerinden gitmek üzere yapılandırabilirsiniz:

 ![Bağlayıcı trafiğini, giden bir ara sunucudan Azure 'a gidecek şekilde yapılandırma AD Uygulama Ara Sunucusu](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

Yalnızca giden trafiğe sahip olmanın bir sonucu olarak, güvenlik duvarlarınız aracılığıyla gelen erişimi yapılandırmanız gerekmez.

> [!NOTE]
> Uygulama proxy 'Si, diğer proxy 'lerde kimlik doğrulamasını desteklemez. Bağlayıcı/Güncelleştirici ağ hizmeti hesaplarının, kimlik doğrulaması için istemeden proxy 'ye bağlanabiliyor olması gerekir.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>1\. Adım: bağlayıcıyı ve ilgili hizmetleri giden ara sunucu üzerinden gidilecek şekilde yapılandırma

WPAD, ortamda etkinse ve uygun şekilde yapılandırıldıysa, bağlayıcı giden proxy sunucusunu otomatik olarak bulur ve kullanmayı dener. Ancak, bağlayıcıyı bir giden proxy üzerinden gitmek üzere açıkça yapılandırabilirsiniz.

Bunu yapmak için, C:\Program Files\Microsoft AAD uygulama proxy Connector\ApplicationProxyConnectorService.exe.config dosyasını düzenleyin ve bu kod örneğinde gösterilen *System.net* bölümünü ekleyin. *ProxyServer: 8080* ' i yerel ara sunucu ADıNıZı veya IP adresinizi ve dinlediği bağlantı noktasını yansıtacak şekilde değiştirin. Bir IP adresi kullanıyor olsanız bile, değerin http://öneki olmalıdır.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Daha sonra, Bağlayıcı Güncelleştiricisi hizmetini, C:\Program Files\Microsoft AAD uygulama proxy Bağlayıcısı Updater\ApplicationProxyConnectorUpdaterService.exe.config dosyasında benzer bir değişiklik yaparak proxy 'yi kullanacak şekilde yapılandırın.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>2\. Adım: bağlayıcıya ve ilgili hizmetlerden gelen trafiğin akmasını sağlamak için proxy 'yi yapılandırma

Giden ara sunucuda dikkate alınması gereken dört önemli nokta vardır:

* Proxy giden kuralları
* Proxy kimlik doğrulaması
* Proxy bağlantı noktaları
* SSL incelemesi

#### <a name="proxy-outbound-rules"></a>Proxy giden kuralları

Aşağıdaki URL 'Lere erişime izin ver:

| URL | Nasıl kullanılır? |
| --- | --- |
| \*. msappproxy.net<br>\*.servicebus.windows.net | Bağlayıcı ile uygulama proxy 'Si bulut hizmeti arasındaki iletişim |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Azure, sertifikaları doğrulamak için bu URL 'Leri kullanır |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>*. microsoftonline.com<br>* . microsoftonline-p.com<br>*. msauth.net<br>* . msauthimages.net<br>*. msecnd.net<br>* . msftauth.net<br>*. msftauthimages.net<br>* . phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net | Bağlayıcı, kayıt işlemi sırasında bu URL 'Leri kullanır. |

Güvenlik duvarınız veya proxy 'niz DNS izin verilenler listelerini yapılandırmanıza izin veriyorsa, \*. msappproxy.net ve \*. servicebus.windows.net bağlantılarına izin verebilirsiniz. Aksi takdirde, [Azure veri MERKEZI IP aralıklarına](https://www.microsoft.com/download/details.aspx?id=41653)erişime izin vermeniz gerekir. IP aralıkları her hafta güncellenir.

FQDN ile bağlantıya izin veremiyorum ve bunun yerine IP aralıklarını belirtmeniz gerekiyorsa, şu seçenekleri kullanın:

* Bağlayıcının tüm hedeflere giden erişimine izin verin.
* Bağlayıcının tüm [Azure veri MERKEZI IP aralıklarına](https://www.microsoft.com//download/details.aspx?id=41653)giden erişimine izin verin. Azure veri merkezi IP aralıkları listesini kullanmayla ilgili zorluk, haftalık olarak güncelleştirilir. Erişim kurallarınızın uygun şekilde güncelleştirildiğinden emin olmak için bir işlem yerine getirmeniz gerekir. Yalnızca IP adreslerinin bir alt kümesini kullanmak yapılandırmanızın kesintiye neden olabilir.

#### <a name="proxy-authentication"></a>Proxy kimlik doğrulaması

Proxy kimlik doğrulaması şu anda desteklenmiyor. Geçerli önerimiz, bağlayıcının Internet hedeflerine anonim erişimine izin vermimize yönelik.

#### <a name="proxy-ports"></a>Proxy bağlantı noktaları

Bağlayıcı, CONNECT metodunu kullanarak giden SSL tabanlı bağlantılar oluşturur. Bu yöntem, aslında giden proxy üzerinden bir tünel kurar. Proxy sunucusunu, 443 ve 80 bağlantı noktalarına tünel sağlamak üzere yapılandırın.

> [!NOTE]
> Service Bus, HTTPS üzerinden çalıştırıldığında 443 numaralı bağlantı noktasını kullanır. Bununla birlikte, varsayılan olarak, Service Bus doğrudan bağlantı başarısız olursa, doğrudan TCP bağlantılarını dener ve yalnızca HTTPS 'ye geri döner.

#### <a name="ssl-inspection"></a>SSL incelemesi

Bağlayıcı trafiği için sorun oluşmasına neden olduğundan bağlayıcı trafiği için SSL incelemesi kullanmayın. Bağlayıcı, uygulama proxy 'Si hizmetinde kimlik doğrulaması yapmak için bir sertifika kullanır ve bu sertifika SSL incelemesi sırasında kaybolabilir.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Bağlayıcı ara sunucu sorunlarını ve hizmet bağlantı sorunlarını giderme

Artık ara sunucu üzerinden akan tüm trafiği görmeniz gerekir. Sorun yaşıyorsanız, aşağıdaki sorun giderme bilgileri yardımcı olmalıdır.

Bağlayıcı bağlantı sorunlarını belirlemek ve sorunlarını gidermek için en iyi yol, bağlayıcı hizmetini başlatırken ağ yakalama işleminin sürme yoludur. Ağ izlemelerini yakalama ve filtreleme hakkında bazı hızlı ipuçları aşağıda verilmiştir.

Seçtiğiniz izleme aracını kullanabilirsiniz. Bu makalenin amaçları doğrultusunda, Microsoft Message Analyzer 'ı kullandık. [Microsoft 'tan indirebilirsiniz](https://www.microsoft.com/download/details.aspx?id=44226).

Aşağıdaki örnekler Ileti çözümleyici 'ye özgüdür, ancak ilkeler herhangi bir analiz aracına uygulanabilir.

### <a name="take-a-capture-of-connector-traffic"></a>Bağlayıcı trafiği yakalama

İlk sorun giderme için aşağıdaki adımları gerçekleştirin:

1. Services. msc ' den Azure AD Uygulama Ara Sunucusu Bağlayıcısı hizmetini durdurun.

   ![Services. msc ' de Azure AD Uygulama Ara Sunucusu Bağlayıcısı hizmeti](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. Ileti Çözümleyicisi 'ni yönetici olarak çalıştırın.
1. **Yerel Izlemeyi Başlat**' ı seçin.
1. Azure AD Uygulama Ara Sunucusu Bağlayıcısı hizmetini başlatın.
1. Ağ yakalamayı durdurun.

   ![Ekran görüntüsü ağ yakalamayı Durdur düğmesini gösterir](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Bağlayıcı trafiğinin giden proxy 'leri atladığını denetle

Uygulama proxy bağlayıcınızı proxy sunucularını atlayacak ve doğrudan uygulama ara sunucusu hizmetine bağlanacak şekilde yapılandırdıysanız, başarısız olan TCP bağlantı girişimleri için ağ yakalamaya bakmak isteyebilirsiniz.

Bu denemeleri belirlemek için Ileti Çözümleyicisi filtresini kullanın. Filtre kutusuna `property.TCPSynRetransmit` girin ve **Uygula**' yı seçin.

Bir SYN paketi, TCP bağlantısı kurmak için gönderilen ilk pakettir. Bu paket bir yanıt döndürmezse, SYN işlemi yeniden deneyin. Yeniden iletilen tüm sistem 'leri görmek için önceki filtreyi kullanabilirsiniz. Ardından, bu sistem 'lerin bağlayıcıyla ilgili herhangi bir trafiğe karşılık geldiğini kontrol edebilirsiniz.

Bağlayıcının Azure hizmetlerine doğrudan bağlantı yapmasını bekleliyorsanız, bağlantı noktası 443 ' deki Synyeniden yönlendirme yanıtları bir ağ veya güvenlik duvarı sorununuz olduğunu belirtir.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Bağlayıcı trafiğinin giden proxy 'leri kullanıp kullanmadığını denetleyin

Uygulama proxy Bağlayıcısı trafiğinizi proxy sunucular üzerinden gezinmek üzere yapılandırdıysanız, proxy 'nize yönelik başarısız HTTPS bağlantılarını aramak istersiniz.

Bu bağlantı girişimleri için ağ yakalamayı filtrelemek için, Ileti Çözümleyicisi filtresine `(https.Request or https.Response) and tcp.port==8080` girin ve 8080 öğesini proxy hizmeti bağlantı noktasıyla değiştirin. Filtre sonuçlarını görmek için **Uygula** ' yı seçin.

Yukarıdaki filtrede yalnızca HTTPs istekleri ve proxy bağlantı noktasından/kaynağından gelen yanıtlar gösterilmektedir. Ara sunucu ile iletişimi gösteren bağlanma isteklerini arıyorsunuz. Başarılı olduğunda, HTTP Tamam (200) yanıtını alırsınız.

407 veya 502 gibi başka yanıt kodları görürseniz, bu, proxy 'nin kimlik doğrulama gerektirmesinin veya başka bir nedenden dolayı trafiğe izin vermemesinin gerektiği anlamına gelir. Bu noktada, proxy sunucusu destek ekibinize katılımını yapabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD Uygulama Ara Sunucusu bağlayıcılarını anlama](application-proxy-connectors.md)
* Bağlayıcı bağlantı sorunlarıyla ilgili sorun yaşıyorsanız sorunuzu [Azure Active Directory forumundan](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) isteyin veya destek ekibimiz ile bir bilet oluşturun.
