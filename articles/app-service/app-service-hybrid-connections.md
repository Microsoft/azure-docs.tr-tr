---
title: Karma bağlantılar
description: Farklı ağlardaki kaynaklara erişmek için Azure App Service ' de karma bağlantıları oluşturma ve kullanma hakkında bilgi edinin.
author: ccompy
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.topic: article
ms.date: 02/05/2020
ms.author: ccompy
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: 1b3fc4a254c1157f2c2336e6360ba7621f31364d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99594240"
---
# <a name="azure-app-service-hybrid-connections"></a>Azure App Service Hibrit Bağlantılar

Karma Bağlantılar hem Azure 'daki bir hizmettir hem de Azure App Service bir özelliktir. Hizmet olarak, App Service ' de kullanılanların ötesinde kullanımı ve yetenekleri vardır. App Service dışında Karma Bağlantılar ve kullanımları hakkında daha fazla bilgi edinmek için bkz. [Azure Relay karma bağlantılar][HCService].

App Service içinde Karma Bağlantılar, 443 numaralı bağlantı noktası üzerinden Azure 'a giden çağrılar yapabilirler herhangi bir ağdaki uygulama kaynaklarına erişmek için kullanılabilir. Karma Bağlantılar, uygulamanızdan bir TCP uç noktasına erişim sağlar ve uygulamanıza erişmek için yeni bir yol etkinleştirmez. App Service için kullanıldığında, her karma bağlantı tek bir TCP ana bilgisayarı ve bağlantı noktası bileşimiyle söz konusu. Bu, uygulamalarınızın bir TCP uç noktası olması şartıyla, herhangi bir işletim sisteminde bulunan kaynaklara erişmesini sağlar. Karma Bağlantılar özelliği, uygulama protokolünün ne olduğunu veya ne erişeceğimizi bilmez veya ilgilenmez. Yalnızca ağ erişimi sağlar.  

## <a name="how-it-works"></a>Nasıl çalışır? ##
Karma Bağlantılar, hem istenen uç noktaya hem de Azure 'a ulaşabulabileceği bir geçiş aracısının dağıtılmasını gerektirir. Karma Bağlantı Yöneticisi (HCM) geçiş aracısı, 443 numaralı bağlantı noktası üzerinden Azure Relay çağırır. Web uygulaması sitesinden App Service altyapısı uygulamanızın adına Azure Relay da bağlanır. Birleştirilmiş bağlantılar aracılığıyla uygulamanız istenen uç noktaya erişebilir. Bağlantı, kimlik doğrulama ve yetkilendirme için güvenlik ve paylaşılan erişim imzası (SAS) anahtarları için TLS 1,2 kullanır.    

![Karma bağlantı üst düzey akış diyagramı][1]

Uygulamanız yapılandırılmış bir karma bağlantı uç noktasıyla eşleşen bir DNS isteği yaptığında, giden TCP trafiği karma bağlantı aracılığıyla yeniden yönlendirilir.  

> [!NOTE]
> Bu, karma bağlantınız için her zaman bir DNS adı kullanmayı denemeniz gerektiği anlamına gelir. Uç nokta bunun yerine bir IP adresi kullanıyorsa, bazı istemci yazılımları DNS araması yapmaz. 
>

### <a name="app-service-hybrid-connection-benefits"></a>Karma bağlantı avantajlarını App Service ###

Karma Bağlantılar özelliği için aşağıdakiler de dahil olmak üzere birçok avantaj vardır:

- Uygulamalar, şirket içi sistemlere ve hizmetlere güvenli bir şekilde erişebilir.
- Bu özellik, internet 'ten erişilebilen bir uç nokta gerektirmez.
- Hızlı ve kolay bir şekilde ayarlanır. Ağ Geçidi gerekli değil
- Her karma bağlantı tek bir konakla eşleşir: bağlantı noktası birleşimi, güvenlik için yararlıdır.
- Normalde güvenlik duvarı delikleri gerektirmez. Bağlantılar, standart Web bağlantı noktaları üzerinden giden tüm çıkışlardır.
- Özelliği ağ düzeyinde olduğundan, uygulamanız tarafından kullanılan dile ve uç nokta tarafından kullanılan teknolojiye belirsiz bir şekilde sahip olur.
- Tek bir uygulamadan birden çok ağda erişim sağlamak için kullanılabilir. 
- Windows Uygulamaları ve Linux uygulamaları için GA 'de desteklenir. Windows kapsayıcı uygulamaları için desteklenmez.

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Karma Bağlantılar ile yapaistemediğiniz şeyler ###

Karma Bağlantılar ile yapaistemediğiniz şeyler şunlardır:

- Bir sürücü bağlayın.
- UDP kullanın.
- FTP pasif modu veya genişletilmiş Pasif mod gibi dinamik bağlantı noktaları kullanan TCP tabanlı hizmetlere erişin.
- UDP 'yi gerektirebileceğinden, LDAP 'yi destekler.
- App Service çalışanına katılabilmeniz için Active Directory destek. 

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Uygulamanıza Karma Bağlantılar ekleme ve oluşturma ##

Karma bağlantı oluşturmak için [Azure Portal][portal] gidin ve uygulamanızı seçin.   >  **Karma bağlantı uç noktalarınızı** ağ oluşturma ' yı seçin. Burada, uygulamanız için yapılandırılmış Karma Bağlantılar görebilirsiniz.  

![Karma bağlantı listesinin ekran görüntüsü][2]

Yeni bir karma bağlantı eklemek için **karma bağlantı ekle**' yi seçin.  Zaten oluşturduğunuz Karma Bağlantılar bir liste görürsünüz. Uygulamanıza bir veya daha fazla uygulama eklemek için istediğiniz olanları seçin ve ardından **Seçili karma bağlantı ekle**' yi seçin.  

![Karma bağlantı portalının ekran görüntüsü][3]

Yeni bir karma bağlantı oluşturmak istiyorsanız **yeni karma bağlantı oluştur**' u seçin. Şunu belirtin: 

- Karma bağlantı adı.
- Uç nokta ana bilgisayar adı.
- Uç nokta bağlantı noktası.
- Kullanmak istediğiniz ad alanı Service Bus.

![Yeni karma bağlantı oluştur iletişim kutusunun ekran görüntüsü][4]

Her karma bağlantı bir Service Bus ad alanına bağlıdır ve her Service Bus ad alanı bir Azure bölgesinde bulunur. Ağ üzerinden oluşan gecikmeyi önlemek için, uygulamanızla aynı bölgede bir Service Bus ad alanı kullanmayı denemek önemlidir.

Karma bağlantınızı uygulamanızdan kaldırmak istiyorsanız, sağ tıklayın ve **bağlantıyı kes**' i seçin.  

Bir karma bağlantı uygulamanıza eklendiğinde, bunu seçerek ayrıntıları görebilirsiniz. 

![Karma bağlantı ayrıntılarının ekran görüntüsü][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Azure Relay portalında karma bağlantı oluşturma ###

Uygulamanızın içinden Portal deneyiminin yanı sıra, Azure Relay Portal içinden Karma Bağlantılar oluşturabilirsiniz. Karma bağlantının App Service tarafından kullanılabilmesi için şunları yapmanız gerekir:

* İstemci yetkilendirmesi gerektir.
* Ana bilgisayar: bağlantı noktası birleşimi değeri olarak adlandırılan, uç nokta adlı bir meta veri öğesi vardır.

## <a name="hybrid-connections-and-app-service-plans"></a>Karma Bağlantılar ve App Service planları ##

App Service Karma Bağlantılar yalnızca temel, standart, Premium ve yalıtılmış fiyatlandırma SKU 'Larında kullanılabilir. Fiyatlandırma planına bağlı sınırlar vardır.  

| Fiyatlandırma planı | Planda kullanılabilir Karma Bağlantılar sayısı |
|----|----|
| Temel | plan başına 5 |
| Standart | plan başına 25 |
| PremiumV2 | Uygulama başına 200 |
| Yalıtılmış | Uygulama başına 200 |

App Service planı Kullanıcı arabirimi, kaç Karma Bağlantılar kullanıldığını ve hangi uygulamalara göre kullanıldığını gösterir.  

![App Service planı özelliklerinin ekran görüntüsü][6]

Ayrıntıları görmek için karma bağlantıyı seçin. Uygulama görünümünde gördüğünüz tüm bilgileri görebilirsiniz. Ayrıca, aynı plandaki diğer birçok uygulamanın bu hibrit bağlantısını kullandığını görebilirsiniz.

App Service planında kullanılabilecek karma bağlantı uç noktası sayısında bir sınır vardır. Ancak kullanılan her karma bağlantı, Bu plandaki herhangi bir sayıda uygulama üzerinde kullanılabilir. Örneğin, bir App Service planındaki beş ayrı uygulama için kullanılan tek bir karma bağlantı, tek bir karma bağlantı olarak sayılır.

### <a name="pricing"></a>Fiyatlandırma ###

App Service plan SKU gereksinimi olmasının yanı sıra, Karma Bağlantılar kullanmanın bir maliyeti de vardır. Karma bağlantı tarafından kullanılan her dinleyici için bir ücretlendirme vardır. Dinleyici Karma Bağlantı Yöneticisi. İki karma bağlantı Yöneticisi tarafından desteklenen beş Karma Bağlantılar varsa, bu 10 dinleyici olur. Daha fazla bilgi için bkz. [Service Bus fiyatlandırması][sbpricing].

## <a name="hybrid-connection-manager"></a>Karma Bağlantı Yöneticisi ##

Karma Bağlantılar özelliği, karma bağlantı uç noktanızı barındıran ağda bir geçiş aracısı gerektirir. Bu geçiş aracısına Karma Bağlantı Yöneticisi (HCM) denir. [Azure Portal][portal]uygulamanızdaki HCM 'yi indirmek için,   >  **karma bağlantı uç noktalarınızı ağ yapılandırma**' yı seçin.  

Bu araç Windows Server 2012 ve üzeri sürümlerde çalışır. HCM bir hizmet olarak çalışır ve 443 numaralı bağlantı noktasında giden Azure Relay bağlanır.  

HCM 'yi yükledikten sonra, aracı için Kullanıcı arabirimini kullanmak üzere HybridConnectionManagerUi.exe çalıştırabilirsiniz. Bu dosya Karma Bağlantı Yöneticisi yükleme dizininde bulunur. Windows 10 ' da, arama kutusunda yalnızca *karma bağlantı Yöneticisi Kullanıcı arabirimi* araması yapabilirsiniz.  

![Karma Bağlantı Yöneticisi ekran görüntüsü][7]

HCM Kullanıcı arabirimini başlattığınızda gördüğünüz ilk şey, HCM 'nin bu örneğiyle yapılandırılan tüm Karma Bağlantılar listeleyen bir tablodur. Herhangi bir değişiklik yapmak istiyorsanız, ilk olarak Azure ile kimlik doğrulaması yapın. 

HCM 'nize bir veya daha fazla Karma Bağlantılar eklemek için:

1. HCM Kullanıcı arabirimini başlatın.
2. **Başka bir karma bağlantı Yapılandır**' ı seçin.
![Yeni Karma Bağlantılar yapılandırma ekran görüntüsü][8]

1. Aboneliklerinizle Karma Bağlantılar sağlamak için Azure hesabınızla oturum açın. HCM bunun ötesinde Azure hesabınızı kullanmaya devam etmez. 
1. Bir abonelik seçin.
1. HCM 'nin geçişine istediğiniz Karma Bağlantılar seçin.
![Karma Bağlantılar ekran görüntüsü][9]

1. **Kaydet**’i seçin.

Artık eklediğiniz Karma Bağlantılar görebilirsiniz. Ayrıca, ayrıntıları görmek için yapılandırılmış karma bağlantıyı seçebilirsiniz.

![Karma bağlantı ayrıntılarının ekran görüntüsü][10]

İle yapılandırıldığı Karma Bağlantılar desteklemek için, HCM şunları gerektirir:

- 443 numaralı bağlantı noktası üzerinden Azure 'a TCP erişimi.
- Karma bağlantı uç noktasına TCP erişimi.
- Uç nokta ana bilgisayarında DNS arama ve Service Bus ad alanı yapma özelliği.

> [!NOTE]
> Azure Relay bağlantı için Web soketlerine bağımlıdır. Bu özellik yalnızca Windows Server 2012 veya üzeri sürümlerde kullanılabilir. Bu nedenle, HCM Windows Server 2012 ' den önceki bir şeye göre desteklenmez.
>

### <a name="redundancy"></a>Yedeklilik ###

Her HCM birden çok Karma Bağlantılar destekleyebilir. Ayrıca, belirli karma bağlantılar birden çok HCMs tarafından desteklenebilir. Varsayılan davranış, trafiği, belirli bir uç nokta için yapılandırılmış HCMs genelinde yönlendirmenize yöneliktir. Karma Bağlantılar ağınızdan yüksek kullanılabilirlik istiyorsanız, ayrı makinelerde birden çok HCMs çalıştırın. Trafiği HCMs 'ye dağıtmak için geçiş hizmeti tarafından kullanılan yük dağıtım algoritması rastgele atamadır. 

### <a name="manually-add-a-hybrid-connection"></a>Karma bağlantı el ile ekleme ###

Aboneliğiniz dışındaki birinin belirli bir karma bağlantı için bir HCM örneği barındırması için, karma bağlantı için ağ geçidi bağlantı dizesini bunlarla paylaşabilirsiniz. Ağ Geçidi bağlantı dizesini [Azure Portal][portal]karma bağlantı özelliklerinde görebilirsiniz. Bu dizeyi kullanmak için HCM 'ye **El Ile gir** ' i seçin ve ağ geçidi bağlantı dizesine yapıştırın.

![Karma bağlantı el ile ekleme][11]

### <a name="upgrade"></a>Yükseltme ###

Sorunları gidermek veya iyileştirmeler sağlamak için Karma Bağlantı Yöneticisi düzenli güncelleştirmeler vardır. Yükseltmeler yayınlandığında, HCM Kullanıcı arabiriminde bir açılan pencere görünür. Yükseltmenin uygulanması değişiklikleri uygular ve HCM 'yi yeniden başlatır. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Programlı olarak uygulamanıza karma bağlantı ekleme ##

Karma Bağlantılar için Azure CLı desteği vardır. Belirtilen komutlar hem uygulama hem de App Service plan düzeyinde çalışır.  Uygulama düzeyi komutları şunlardır:

```azurecli
az webapp hybrid-connection

Group
    az webapp hybrid-connection : Methods that list, add and remove hybrid-connections from webapps.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a hybrid-connection to a webapp.
    list   : List the hybrid-connections on a webapp.
    remove : Remove a hybrid-connection from a webapp.
```

App Service plan komutları, belirli bir karma bağlantının hangi anahtara kullanılacağını ayarlamanıza olanak sağlar. Her karma bağlantıda, birincil ve ikincil olmak üzere iki anahtar kümesi vardır. Aşağıdaki komutlarla birincil veya ikincil anahtarı kullanmayı tercih edebilirsiniz. Bu, anahtarlarınızı düzenli olarak yeniden oluşturmak istediğinizde için anahtarlar yapmanızı sağlar. 

```azurecli
az appservice hybrid-connection --help

Group
    az appservice hybrid-connection : A method that sets the key a hybrid-connection uses.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    set-key : Set the key that all apps in an appservice plan use to connect to the hybrid-
                connections in that appservice plan.
```

## <a name="secure-your-hybrid-connections"></a>Karma Bağlantılar güvenli hale getirin ##

Mevcut bir karma bağlantı, temel Azure Service Bus geçişi üzerinde yeterli izinlere sahip herhangi bir kullanıcı tarafından diğer App Service Web Apps eklenebilir. Bu, başkalarının aynı karma bağlantıyı yeniden kullanmasını engellemeniz gerekiyorsa (örneğin, hedef kaynak yetkisiz erişimi engellemek için ek güvenlik önlemleri olmayan bir hizmettir) Azure Service Bus geçişine erişimi kilitlemeniz gerekir.

Geçişe erişimi olan herkes, `Reader` Azure Portal Web uygulamasına eklemeye çalışırken karma bağlantıyı _görebilir_ , ancak geçiş bağlantısını kurmak için kullanılan bağlantı dizesini alma izinlerine sahip olmadıkları sürece bu bağlantıları _ekleyemeyecektir_ . () Karma bağlantıyı başarıyla eklemek için, `listKeys` () iznine sahip olmaları gerekir `Microsoft.Relay/namespaces/hybridConnections/authorizationRules/listKeys/action` . `Contributor`Bu Izni geçiş üzerinde içeren rol veya başka herhangi bir rol, kullanıcıların karma bağlantıyı kullanmasına ve kendi Web Apps eklemesine izin verir.

## <a name="troubleshooting"></a>Sorun giderme ##

"Bağlı" durumu, en az bir HCM 'nin bu karma bağlantıyla yapılandırıldığı ve Azure 'a ulaşabilme anlamına gelir. Karma bağlantınızın durumu **bağlı** değilse, karma bağlantınız Azure erişimi olan herhangi bir HCM 'de yapılandırılmamıştır. HCM 'niz **bağlı değil** olarak görünüyorsa, denetlenecek birkaç şey vardır:

* Ana bilgisayarınız 443 numaralı bağlantı noktasında Azure 'a giden erişime sahip mi? PowerShell komut *testi-NetConnection hedef-P bağlantı noktasını* kullanarak HCM konağından test edebilirsiniz 
* HCM büyük olasılıkla hatalı durumda mı? ' Azure Karma Bağlantı Yöneticisi hizmeti "yerel hizmetini yeniden başlatmayı deneyin.

Durumunuz **bağlı** ise, uygulamanız bitiş noktanıza ulaşamadıysanız:

* Karma bağlantınızda bir DNS adı kullandığınızdan emin olun. Bir IP adresi kullanırsanız, gerekli istemci DNS araması gerçekleşmeyebilir. Web uygulamanızda çalışan istemci bir DNS araması yapmaz, karma bağlantı çalışır durumda olmayacaktır
* Karma bağlantınızda kullanılan DNS adının HCM ana bilgisayardan çözümleneceğini denetleyin. ' Nin karma bağlantı tanımınızda kullanılan bir tam eşleşme olduğu *nslookup endpointdnsname* kullanarak çözümü denetleyin.
* HCM konağından uç noktaya erişimi test etmek için PowerShell komutu *Test-NetConnection EndpointDNSname-P bağlantı noktasını*  kullanarak, HCM ana bilgisayarınızdan gelen erişimi test edin ve ardından hedef konaktaki ana bilgisayar tabanlı güvenlik duvarları dahil olmak üzere iki ana bilgisayar arasındaki güvenlik duvarlarını kontrol edin.

App Service, **tcme** komut satırı aracı Gelişmiş araçlar (kudu) konsolundan çağrılabilir. Bu araç, bir TCP uç noktasına erişiminiz varsa size söyleyebilir, ancak karma bağlantı uç noktasına erişiminiz varsa size bilgi vermez. Aracı bir karma bağlantı uç noktasına karşı konsolunda kullandığınızda yalnızca bir konak kullandığını onaylanıyor olursunuz: bağlantı noktası birleşimi.  

Uç noktanız için bir komut satırı istemciniz varsa, bağlantıyı uygulama konsolundan test edebilirsiniz. Örneğin, kıvrımlı kullanarak Web sunucusu uç noktalarına erişimi test edebilirsiniz.


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png
[11]: ./media/app-service-hybrid-connections/hybridconn-manual.png
[12]: ./media/app-service-hybrid-connections/hybridconn-bt.png

<!--Links-->
[HCService]: /azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: https://portal.azure.com/
[oldhc]: /azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: https://azure.microsoft.com/pricing/details/service-bus/
[armclient]: https://github.com/projectkudu/ARMClient/
