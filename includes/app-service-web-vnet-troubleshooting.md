---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: cec44bbabdb7d528c30a8d3396b819f2eb3c5386
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95999475"
---
Özelliği kolayca ayarlanabilir, ancak bu deneyim deneyiminizin ücretsiz olacağı anlamına gelmez. İstediğiniz uç noktaya erişmede sorun yaşarsanız, uygulama konsolundan bağlantıyı test etmek için kullanabileceğiniz bazı yardımcı programlar vardır. Kullanabileceğiniz iki konsol vardır. Birisi kudu konsoludur ve diğeri Azure portal konsoludur. Uygulamaınızdan kudu konsoluna ulaşmak için **Araçlar**  >  **kudu**' ye gidin. Ayrıca, Kudo konsoluna [SiteName]. scm. azurewebsites. net adresinden ulaşabilirsiniz. Web sitesi yüklendikten sonra, **hata ayıklama konsolu** sekmesine gidin. Uygulamanızdaki Azure Portal barındırılan konsola ulaşmak için **Araçlar**  >  **konsolu**' na gidin.

#### <a name="tools"></a>Araçlar
Yerel Windows uygulamalarında, **ping**, **nslookup** ve **tracert** araçları güvenlik kısıtlamaları nedeniyle konsolda çalışmaz ( [özel Windows kapsayıcılarında](../articles/app-service/quickstart-custom-container.md)çalışırlar). Void öğesini dolduracak şekilde iki ayrı araç eklenir. DNS işlevselliğini test etmek için **nameresolver.exe** adlı bir araç ekledik. Söz dizimi aşağıdaki gibidir:

```console
nameresolver.exe hostname [optional: DNS Server]
```

Uygulamanızın bağımlı olduğu ana bilgisayar adlarını denetlemek için nameresolver kullanabilirsiniz. Bu şekilde, DNS 'niz ile yapılandırılmış herhangi bir şey varsa veya belki DNS sunucunuza erişiminiz yoksa test edebilirsiniz. WEBSITE_DNS_SERVER ve WEBSITE_DNS_ALT_SERVER ortam değişkenlerine bakarak uygulamanızın konsolunda kullandığı DNS sunucusunu görebilirsiniz.

> [!NOTE]
> nameresolver.exe Şu anda özel Windows kapsayıcılarında çalışmıyor.
>

Bir konak ve bağlantı noktası birleşimine TCP bağlantısını test etmek için bir sonraki aracı kullanabilirsiniz. Bu araca, **tcma** adı verilir ve sözdizimi şöyledir:

```console
tcpping.exe hostname [optional: port]
```

**Tcter** yardımcı programı, belirli bir konağa ve bağlantı noktasına ulaşabilseniz size bildirir. Yalnızca konak ve bağlantı noktası birleşimini dinleyen bir uygulama varsa ve uygulamanızdan belirtilen konağa ve bağlantı noktasına ağ erişimi varsa, bu başarılı olabilir.

#### <a name="debug-access-to-virtual-network-hosted-resources"></a>Sanal ağ üzerinde barındırılan kaynaklara erişimi hata ayıklama
Birçok şey, uygulamanızın belirli bir konağa ve bağlantı noktasına ulaşmasını engelleyebilir. Çoğu zaman bu işlemlerden biridir:

* **Bir güvenlik duvarı bu şekilde yapılır.** Bu şekilde bir güvenlik duvarınız varsa, TCP zaman aşımına uğraymanız gerekir. Bu durumda TCP zaman aşımı 21 saniyedir. Bağlantıyı sınamak için **tcma** aracını kullanın. TCP zaman aşımları, güvenlik duvarlarının ötesinde pek çok şeyin oluşmasına karşın buradan başlayabilir.
* **DNS erişilebilir değil.** DNS sunucusu başına DNS zaman aşımı 3 saniyedir. İki DNS sunucunuz varsa, zaman aşımı 6 saniyedir. DNS çalışıp çalışmadığını görmek için nameresolver kullanın. Sanal ağınızın yapılandırıldığı DNS 'yi kullanmadığından, nslookup 'ı kullanamazsınız. Erişilemezse, DNS erişimi engelleyen bir güvenlik duvarına veya NSG 'ye sahip olabilirsiniz veya bu durumda olabilir.

Bu öğeler sorunlarınızı yanıtlamazsanız, şunun gibi şeyler için göz atın:

**Bölgesel VNet tümleştirmesi**
* Hedef, RFC1918 olmayan bir adres değil ve WEBSITE_VNET_ROUTE_ALL 1 olarak ayarlanmamış.
* Tümleştirme alt ağınızdan çıkış bloke bir NSG var mı?
* Azure ExpressRoute veya VPN üzerinden devam ediyorsanız şirket içi ağ geçidiniz trafiği Azure 'a yeniden yönlendirmek üzere yapılandırılmış mı? Sanal ağınızdaki, şirket içi olmayan uç noktalara ulaşabilseniz, rotalarınızı denetleyin.
* Tümleştirme alt ağında temsili ayarlamak için yeterli izinlere sahip misiniz? Bölgesel VNet tümleştirme yapılandırması sırasında, tümleştirme alt ağınız Microsoft. Web/Servergrupları için temsilci olarak oluşturulur. VNet Integration UI, alt ağı Microsoft. Web/ServerFarm 'e otomatik olarak devreder. Hesabınız temsilci seçmek için yeterli ağ izinlerine sahip değilse, alt ağı devretmek için tümleştirme alt ağlarınızın özniteliklerini ayarlayabilen bir kişiye ihtiyacınız vardır. Tümleştirme alt ağını el ile devretmek için Azure sanal ağ alt ağı Kullanıcı arabirimine gidin ve Microsoft. Web/ServerFarm temsilcisini ayarlayın.

**Ağ Geçidi-gerekli VNet tümleştirmesi**
* , RFC 1918 aralıklarında (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255) Noktadan siteye adres aralığıdır mi?
* Ağ Geçidi portalda mi çalışıyor? Ağ geçidinizin kapalıysa, daha sonra geri getirin.
* Sertifikalar eşitlenmiş olarak gösteriliyor mu veya ağ yapılandırmasının değiştiğini kuşkulu mı?  Sertifikalarınız eşitlenmemiş veya sanal ağ yapılandırmanızda ASPs 'niz ile eşitlenmemiş bir değişikliğin yapıldığından şüphelenirseniz, **eşitleme ağı**' nı seçin.
* VPN üzerinden çalışıyorsanız, trafiği Azure 'a yönlendirmek için şirket içi ağ geçidi yapılandırılmış mı? Sanal ağınızdaki, şirket içi olmayan uç noktalara ulaşabilseniz, rotalarınızı denetleyin.
* Hem site hem de ExpressRoute 'a işaret eden bir birlikte bulunma ağ geçidi kullanmayı deniyor musunuz? Birlikte bulunma ağ geçitleri, VNet tümleştirmesinde desteklenmez.

Belirli bir konağa erişimi engellemeyi görmediğinden hata ayıklama ağ sorunlarını giderme işlemi bir zorluk noktasıdır: bağlantı noktası birleşimi. Bazı nedenler şunlardır:

* Ana bilgisayarınızda, Noktadan siteye IP aralığından uygulama bağlantı noktasına erişimi engelleyen bir güvenlik duvarınız var. Çakışan alt ağlar genellikle genel erişim gerektirir.
* Hedef ana bilgisayarınız çalışmıyor.
* Uygulamanız çalışmıyor.
* Yanlış IP veya ana bilgisayar adı vardı.
* Uygulamanız, beklediğinizden farklı bir bağlantı noktasını dinliyor. Uç nokta konağında "netstat-Aon" öğesini kullanarak, işlem KIMLIĞINIZI dinleme bağlantı noktasıyla eşleştirebilirsiniz.
* Ağ güvenlik gruplarınız, uygulama ana bilgisayarınıza ve bağlantı noktasına Noktadan siteye IP aralığından erişimi engelleyecek şekilde yapılandırılır.

Uygulamanızın gerçekten kullandığı adresi bilemezsiniz. Tümleştirme alt ağında veya Noktadan siteye adres aralığında herhangi bir adres olabilir, bu nedenle tüm adres aralığından erişime izin vermeniz gerekir.

Ek hata ayıklama adımları şunlardır:

* Sanal ağınızdaki bir VM 'ye bağlanın ve kaynak konağınız üzerinde bağlantı noktasını buradan ulaşmaya çalışın. TCP erişimini test etmek için PowerShell komut **testi-NetConnection** komutunu kullanın. Söz dizimi aşağıdaki gibidir:

```powershell
test-netconnection hostname [optional: -Port]
```

* Bir VM üzerinde bir uygulama açın ve **tcup** kullanarak konsolundan konsoldan bu konağa ve bağlantı noktasına erişimi test edin.

#### <a name="on-premises-resources"></a>Şirket içi kaynaklar ####

Uygulamanız Şirket içindeki bir kaynağa ulaşamadıysanız, sanal ağınızdan kaynağa ulaşabilseniz kontrol edin. TCP erişimini denetlemek için **Test-NetConnection** PowerShell komutunu kullanın. VM 'niz şirket içi kaynağına ulaşamadıysa, VPN veya ExpressRoute bağlantınız düzgün şekilde yapılandırılmamış olabilir.

Sanal ağ üzerinde barındırılan VM 'niz şirket içi sisteminize ulaşılırsa ancak uygulamanız bu nedenle, nedeni aşağıdakilerden biri olabilir:

* Rotalarınız, şirket içi ağ geçidinizdeki alt ağlarınız veya Noktadan siteye adres aralıklarıyla yapılandırılmamıştır.
* Ağ güvenlik gruplarınız, Noktadan siteye IP aralığınız için erişimi engelliyor.
* Şirket içi güvenlik duvarları, Noktadan siteye IP aralığından trafiği engelliyor.
* Bölgesel VNet tümleştirme özelliğini kullanarak, RFC 1918 olmayan bir adrese ulaşmaya çalışıyorsunuz.
