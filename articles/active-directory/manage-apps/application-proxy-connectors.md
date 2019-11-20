---
title: Azure AD uygulama ara sunucusu bağlayıcıları anlama | Microsoft Docs
description: Azure AD uygulama ara sunucusu bağlayıcıları ile ilgili temel bilgileri kapsar.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: c4666a9d084f6fc12cd68b69d5c71cafc3de0439
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807791"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Azure AD uygulama ara sunucusu bağlayıcıları anlama

Bağlayıcılar, hangi Azure AD uygulama proxy'si olası yaptıran şeydir. Bunlar, basit, kolay dağıtım ve Bakım ve son derece güçlü. Bu makalede nasıl çalıştıkları hangi bağlayıcıları olan ve dağıtımınızın iyileştirilmesine yönelik bazı öneriler ele alınmaktadır.

## <a name="what-is-an-application-proxy-connector"></a>Uygulama Ara sunucusu bağlayıcısını nedir?

Bağlayıcılar, şirket içi yaslanın ve uygulama proxy'si Hizmeti giden bağlantı kolaylaştırmak basit aracılardır. Bağlayıcılar, arka uç uygulaması erişimi olan bir Windows Server üzerinde yüklenmelidir. Bağlayıcılar, belirli uygulamalara trafiği işleme her grubuyla bağlayıcı gruplar halinde düzenleyebilirsiniz.

## <a name="requirements-and-deployment"></a>Gereksinimler ve dağıtım

Uygulama proxy'si başarıyla dağıtmak için en az bir bağlayıcı gerekir, ancak iki veya daha fazla dayanıklılık için daha fazla öneririz. Bağlayıcı'yı Windows Server 2012 R2 çalıştıran bir makineye yüklemek veya üzeri. Yayımladığınız şirket içi uygulamalar ve uygulama proxy'si hizmeti ile iletişim kurmak bağlayıcıyı gerekir.

### <a name="windows-server"></a>Windows server
Windows Server 2012 R2 çalıştıran bir sunucuya ihtiyacınız veya daha sonra uygulama ara sunucusu Bağlayıcısı'nı yükleyebilirsiniz. Azure'da uygulama ara sunucusu hizmetlerini ve yayımlamakta şirket içi uygulamalara bağlanmak sunucunun gerekir.

Windows server TLS 1.2 uygulama ara sunucusu bağlayıcısını yüklemeden önce etkin olmalıdır. Sunucuda TLS 1.2 etkinleştirmek için:

1. Aşağıdaki kayıt defteri anahtarlarını ayarlayın:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Sunucuyu yeniden başlatın

Bağlayıcı sunucusu için ağ gereksinimleri hakkında daha fazla bilgi için bkz: [uygulaması Ara sunucusu ile çalışmaya başlama ve bir bağlayıcı yükleme](application-proxy-add-on-premises-application.md).

## <a name="maintenance"></a>Bakım

Bağlayıcılar ve hizmetin tüm yüksek kullanılabilirlik görevlerini ilgileniriz. Bunlar eklenebilir veya dinamik olarak kaldırılmış. Yeni bir istek geldiğinde her zaman, şu anda kullanılabilen bağlayıcıların birine yönlendirilir. Bir bağlayıcıyı geçici olarak kullanılamıyor, giden trafiğin yanıt vermiyor.

Bağlayıcılar, durum bilgisiz olduğundan ve hiçbir yapılandırma verilerini makine üzerinde sahip. Yalnızca bunlar depolamak verileri, hizmet ve kimlik doğrulama sertifikasını bağlamak için ayarlarıdır. Hizmete bağlandıklarında tüm gerekli yapılandırma verilerini çekme ve her birkaç dakika yenileyin.

Bağlayıcılar ayrıca olup bulmak için sunucuyu yoklamak connector'ın daha yeni bir sürümü. Bağlayıcılar, bulunması durumunda, kendilerini güncelleştirin.

Olay günlüğü ve performans sayaçlarını kullanarak, üzerinde çalıştıkları makineden bağlayıcılarınızı izleyebilirsiniz. Ya da Azure portal'ın uygulama proxy'si sayfasından durumlarını görüntüleyebilirsiniz:

![Örnek: Azure AD uygulama ara sunucusu bağlayıcıları](./media/application-proxy-connectors/app-proxy-connectors.png)

Kullanılmayan bağlayıcılar el ile silmeniz gerekmez. Bir bağlayıcı çalışırken hizmete bağlandığında etkin kalır. Kullanılmayan bağlayıcılar olarak etiketlenmiş _etkin olmayan_ ve kaldıktan sonra 10 gün kaldırılır. Ancak, bir bağlayıcıyı kaldırmak isterseniz hem bağlayıcı hizmetini hem de Updater hizmetini sunucudan kaldırın. Hizmeti tam olarak kaldırmak için bilgisayarınızı yeniden başlatın.

## <a name="automatic-updates"></a>Otomatik güncelleştirmeler

Azure AD, dağıttığınız tüm bağlayıcıları otomatik güncelleştirmeler sağlar. Application Proxy Connector Updater hizmeti çalışıyor olduğu sürece, bağlayıcılarınızı otomatik olarak güncelleştirilir. Connector Updater hizmeti sunucunuzda görmüyorsanız, istediğiniz [Bağlayıcınızı yeniden](application-proxy-add-on-premises-application.md) güncelleştirmeleri almak için.

Bir otomatik güncelleştirme için Bağlayıcınızı gelmesini beklemek istemiyorsanız, bunu el ile yükseltme yapabilirsiniz. Git [Bağlayıcısı indirme sayfası](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) bulunduğu ve select Bağlayıcınızı olduğu sunucu üzerindeki **indirme**. Bu işlem yerel bağlayıcı için bir yükseltme başlatan.

Birden çok Bağlayıcılarla kiracılar için Otomatik Güncelleştirmeler ortamınızdaki kesinti süresini önlemek için her grupta bir anda tek bir bağlayıcıyı hedefleyin.

Bağlayıcınızı güncelleştirir, kapalı kalma süresi karşılaşabilirsiniz:
  
- Yalnızca tek bir bağlayıcıyı sahip ikinci bir bağlayıcı öneririz ve [bir bağlayıcı grubu oluşturma](application-proxy-connector-groups.md). Kapalı kalma süresini önlemek ve daha yüksek kullanılabilirlik sağlayın.  
- Güncelleştirme başladığında bir bağlayıcı ortasında bir işlem oluştu. İlk işlem kayıp olsa da, tarayıcınız otomatik olarak işlemi yeniden denemesinin veya sayfanızı yenileyebilirsiniz. İstek gönderilir, trafik için yedek bir bağlayıcı yönlendirilir.

Daha önce yayımlanmış sürümleri ve bunlar değişiklikler hakkındaki bilgileri görmek için eklemek için bkz: [uygulama ara sunucusu - sürüm yayımlama geçmişi](application-proxy-release-version-history.md).

## <a name="creating-connector-groups"></a>Bağlayıcı grupları oluşturma

Bağlayıcı grupları belirli uygulamalar sunmak için özel bağlayıcılar atamanızı sağlar. Bağlayıcılar sayısı gruplamak ve her bir uygulama bir gruba atayın.

Bağlayıcı grupları büyük dağıtımları yönetmeyi kolaylaştırır. Bunlar ayrıca gecikme kiracılar için yalnızca yerel uygulamalar sunmak için konum temelli bağlayıcı grupları oluşturabildiğinden, farklı bölgelerde barındırılan uygulamalar geliştirin.

Bağlayıcı grupları hakkında daha fazla bilgi için bkz: [ayrı ağlar ve konumları bağlayıcı grupları kullanarak uygulama yayımlama](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>Kapasite planlaması

Beklenen trafik hacmini işlemeye yetecek bağlayıcılar arasında yeterli kapasite planladığınızdan emin olmak önemlidir. Her bir bağlayıcı grubu yüksek kullanılabilirlik ve ölçek sağlamak için en az iki bağlayıcı olduğunu öneririz. Herhangi bir noktada bir makine hizmet gerektiği durumlarda üç bağlayıcılar olması idealdir.

Genel, sahip olduğunuz daha fazla kullanıcı, daha büyük içinde bir makine gerekir. Aşağıda farklı makineler işleyebilir beklenen gecikme süresi ve birimi bir özetini veren bir tablodur. Tüm beklenen işlem başına ikinci (TPS üzerinde) temel kullanım bu yana bir kullanıcı tarafından desenleri değişir ve yük tahmin kullanılamaz yerine unutmayın. Ayrıca yanıtları ve arka uç uygulama yanıt süresini boyutuna bağlı olarak bazı farklılıklar da olacaktır - büyük yanıt boyutu ve daha yavaş yanıt süresi daha düşük bir maksimum TPS neden olur. Dağıtılmış yük makinelerdeki her zaman geniş bir arabellek sağlar, böylece ek makineler sahip öneririz. Ek kapasite, yüksek kullanılabilirlik ve dayanıklılığı sahip olmanızı sağlar.

|Çekirdek|RAM|Gecikme süresi (MS) bekleniyordu-P99|En fazla TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|

\* Bu makineyi, bazı önerilen ayarlar .NET ötesinde varsayılan bağlantı sınırları artırmak için özel bir ayarı kullanılır. Kiracınız için değiştirildi. Bu sınırı almak için destek ile irtibat kurmadan önce varsayılan ayarlarla bir test çalıştırmanızı öneririz.

> [!NOTE]
> Kadar en fazla TPS 4, 8 ve 16 çekirdekli makinelerde arasındaki farkı yoktur. Bunlar arasındaki temel fark, beklenen gecikme ' dir.  

## <a name="security-and-networking"></a>Güvenlik ve ağ özellikleri

Bağlayıcılar için uygulama proxy'si hizmeti istekleri göndermesine izin veren ağ her yerden yüklenebilir. Önemli olan, ayrıca bağlayıcıyı çalıştıran bilgisayarın uygulamalarınıza erişim sahip olur. Bağlayıcılar, Kurumsal ağınızın içinde veya bulutta çalışan bir sanal makine üzerinde yükleyebilirsiniz. Bağlayıcılar bir sivil bölge (DMZ) olarak da bilinen bir çevre ağ içinde çalıştırabilirsiniz ancak ağınızın güvenliğini korumak için tüm trafik gidendir çünkü gerekli değildir.

Bağlayıcılar, yalnızca giden istekler gönderin. Giden trafik uygulama proxy'si hizmeti ve yayımlanan uygulamalara gönderilir. Oturum kurulduktan sonra iki yolu vardır ve trafik akışları için gelen bağlantı noktalarını açmanız gerekmez. Ayrıca, güvenlik duvarları üzerinden gelen erişimi yapılandırmak gerekmez.

Giden güvenlik duvarı kurallarını yapılandırma hakkında daha fazla bilgi için bkz. [iş mevcut şirket içi proxy sunucuları](application-proxy-configure-connectors-with-proxy-servers.md).

## <a name="performance-and-scalability"></a>Performans ve ölçeklenebilirlik

Uygulama proxy'si hizmeti için ölçek saydamdır, ancak ölçek bağlayıcıların bir faktördür. Yoğun trafiği işlemek için yeterli bağlayıcılar olması gerekir. Bağlayıcılar, durum bilgisiz olduğundan, bunlar sayısı kullanıcı veya oturum tarafından etkilenmez. Bunun yerine, istek sayısı ve bunların yükü boyutu için yanıt. Standart web trafiği ile ortalama bir makine saniyede birkaç bin istekleri işleyebilir. Belirli kapasite tam makine özelliklerine bağlıdır.

Bağlayıcı performansı CPU ve ağ ile ilişkilidir. Ağ Azure'da hızlı bağlantı uygulamalara ve çevrimiçi hizmete almak önemli olmakla birlikte bir CPU performans SSL şifreleme ve şifre çözme, için gereklidir.

Buna karşılık, Bağlayıcılarla ilgili sorun bellektir. Çevrimiçi hizmet işleme çoğunu ve kimliği doğrulanmamış tüm trafiği üstlenir. Bulutta yapılabilir her şey bulutta yapılır.

Bağlayıcısı veya makine kullanılamaz hale herhangi bir nedenle, başka bir bağlayıcı grubunda gidip trafik başlar. Bu da neden olan birden fazla bağlayıcıyı öneririz dayanıklılıktır.

Performansını etkileyen başka bir ağ dahil olmak üzere bu bağlayıcıları arasında kalitesini etkendir:

- **Çevrimiçi hizmet**: Azure'daki uygulama proxy'si hizmeti yavaş veya Yüksek gecikmeli bağlantılar bağlayıcı performansı etkiler. En iyi performans için kuruluşunuz Azure Express Route ile bağlanın. Aksi takdirde, ağ ekibiniz Azure bağlantıları mümkün olduğunca verimli bir şekilde işlendiğinden emin olmak gerekir.
- **Arka uç uygulamaları**: Bazı durumlarda, bağlayıcı ve yavaş veya bağlantıları engelle arka uç uygulamaları arasında ek bir proxy vardır. Bu senaryoyla ilgili sorunları gidermek için bağlayıcı sunucusundan bir tarayıcı açın ve uygulamaya erişmeyi deneyin. Bağlayıcılar Azure'da çalıştırdığınız ancak şirket içi uygulamaları, ne kullanıcılarınızın beklediği deneyimi olmayabilir.
- **Etki alanı denetleyicileri**: Bağlayıcılar, çoklu oturum Kerberos kısıtlanmış temsil kullanarak açma (SSO) gerçekleştirirseniz arka ucuna istek göndermeden önce etki alanı denetleyicileriyle iletişim. Kerberos biletlerinin bir önbellek bağlayıcılara sahiptir, ancak meşgul bir ortamda, etki alanı denetleyicilerinin yanıtlama performansını etkileyebilir. Bu sorun, Azure'da çalıştırmak, ancak şirket içi etki alanı denetleyicileriyle iletişim bağlayıcıların daha yaygındır.

Ağınızın en iyi duruma getirme hakkında daha fazla bilgi için bkz. [Azure Active Directory Uygulama proxy'si kullanılırken ağ topolojisi hakkında önemli noktalar](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Etki alanına katılma

Bağlayıcılar değil etki alanına katılmış bir makinede çalıştırabilirsiniz. Ancak, tümleşik Windows kimlik doğrulaması (IWA) kullanan uygulamalar için çoklu oturum açma (SSO) istiyorsanız, etki alanına katılmış bir makine gerekir. Bu durumda, bağlayıcı makineler gerçekleştirebileceği bir etki alanına katılması gerekir [Kerberos](https://web.mit.edu/kerberos) kısıtlanmış temsil yayımlanan uygulamalar kullanıcılar adına.

Bağlayıcılar ayrıca etki alanı ya da kısmi bir güvene sahip ormanlara veya salt okunur etki alanı denetleyicilerine katılabilir.

## <a name="connector-deployments-on-hardened-environments"></a>Sağlamlaştırılmış ortamlarda bağlayıcı dağıtımları

Genellikle, bağlayıcı dağıtım oldukça basittir ve özel bir yapılandırma gerektirir. Ancak, ele alınması gereken benzersiz bazı koşullar vardır:

- Giden trafiği sınırlamak kuruluşların gerekir [gerekli bağlantı noktalarını açma](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).
- FIPS uyumlu makineler oluşturmak ve bir sertifika deposu bağlayıcı işlemlere izin verecek şekilde yapılandırmalarını değiştirmek için gerekli olabilir.
- Tüm gerekli bağlantı noktaları ve IP'ler erişmek için her iki bağlayıcıyı hizmetin etkin olduğundan emin olmak ağ istekleri işlemleri temel alan ortamlarında kilitleme kuruluşların sahip.
- Bazı durumlarda, giden iletme proxy'leri karşılıklı sertifika kimlik doğrulaması bölün ve iletişimin başarısız olmasına neden.

## <a name="connector-authentication"></a>Bağlayıcı kimlik doğrulaması

Güvenli bir hizmet sağlamak için doğru hizmet kimlik doğrulaması bağlayıcılar gerekir ve hizmet bağlayıcı kimlik doğrulaması gerekir. Bu kimlik doğrulaması yapılır istemci ve sunucu sertifikaları kullandığınızda bağlayıcıları bağlantıyı başlatırsınız. Bu şekilde, bağlayıcı makinesinde yönetici kullanıcı adı ve parola depolanmaz.

Uygulama proxy'si hizmeti için kullanılan sertifikaları özgüdür. İlk kayıt sırasında oluşturulan ve otomatik olarak bağlayıcılar tarafından her birkaç ay yenilenir.

Bir bağlayıcı birkaç ay boyunca hizmetine bağlı değilse, sertifikaları güncel olmayabilir. Bu durumda, kaldırın ve bağlayıcıyı tetikleyici kayıt için yeniden yükleyin. Aşağıdaki PowerShell komutlarını çalıştırabilirsiniz:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

## <a name="under-the-hood"></a>Başlık altında

Çoğu Windows olay günlükleri de dahil olmak üzere aynı yönetim araçlarını, sahip oldukları için bağlayıcılar Windows Server Web Uygulama Proxy üzerinde temel alır

![Olay günlükleri Olay Görüntüleyicisi'ni ile yönetme](./media/application-proxy-connectors/event-view-window.png)

ve Windows performans sayaçları.

![Bağlayıcı ile Performans İzleyicisi sayaçları ekleyin](./media/application-proxy-connectors/performance-monitor.png)

Hem yönetim hem de oturum takılabilecek günlükleri. Yönetici günlükler anahtar olayları ve bunların hatalarını içerir. Oturum günlükleri tüm işlemleri ve bunların işleme ayrıntılarını içerir.

Günlükleri görmek için açık Olay Görüntüleyicisi'ne gidin. **görünümü** menü ve etkinleştirme **Göster Analitik ve hata ayıklama günlüklerini**. Ardından, bunları olayları toplamaya başlamak etkinleştirin. Bağlayıcılar daha yeni bir sürümü üzerinde dayalı olarak bu günlükleri Web uygulaması Ara sunucusu Windows Server 2012 R2'de görünmez.

Hizmetler penceresini hizmeti durumunu inceleyebilirsiniz. Bağlayıcı iki Windows hizmetten oluşur: Gerçek bağlayıcı ve güncelleştirici. Her ikisi de her zaman çalıştırmalısınız.

 ![Örnek: Azure AD Hizmetleri yerel gösteren penceresi Hizmetleri](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Ayrı ağlarda ve konumları bağlayıcı grupları kullanarak uygulama yayımlama](application-proxy-connector-groups.md)
- [Mevcut şirket içi proxy sunucuları ile çalışma](application-proxy-configure-connectors-with-proxy-servers.md)
- [Uygulama Ara sunucusu ve bağlayıcı hatalarını giderme](application-proxy-troubleshoot.md)
- [Azure AD uygulama ara sunucusu Bağlayıcısı sessiz yükleme](application-proxy-register-connector-powershell.md)
