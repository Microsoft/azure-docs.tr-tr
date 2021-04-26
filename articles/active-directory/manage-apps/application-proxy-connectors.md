---
title: Azure AD Uygulama Ara Sunucusu bağlayıcıları anlama | Microsoft Docs
description: Azure AD Uygulama Ara Sunucusu bağlayıcıları hakkında bilgi edinin.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b54e3cb3b4ce7eb8f541755df75e8d6a22eb7c2
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575318"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Azure AD Uygulama Ara Sunucusu bağlayıcılarını anlama

Bağlayıcılar Azure AD Uygulama Ara Sunucusu mümkün hale getirir. Bunlar basit, kolayca dağıtılır ve korunur ve süper güçlü bir işlemdir. Bu makalede bağlayıcılar ne olduğunu, nasıl çalıştığını ve dağıtımınızı iyileştirmek için bazı öneriler açıklanmaktadır.

## <a name="what-is-an-application-proxy-connector"></a>Uygulama proxy Bağlayıcısı nedir?

Bağlayıcılar şirket içinde oturlan ve uygulama ara sunucusu hizmetine giden bağlantıyı kolaylaştıran hafif aracılardır. Bağlayıcıların, arka uç uygulamasına erişimi olan bir Windows Server 'a yüklenmesi gerekir. Bağlayıcıları bağlayıcı grupları halinde düzenleyerek, her grup belirli bir uygulama için trafiği idare edebilirsiniz. Uygulama ara sunucusu üzerinde daha fazla bilgi ve uygulama proxy mimarisinin diagrammatik gösterimi için bkz. [uzak kullanıcılar için şirket içi uygulamalar yayımlamak üzere Azure AD uygulama ara sunucusu kullanma](what-is-application-proxy.md#application-proxy-connectors)

## <a name="requirements-and-deployment"></a>Gereksinimler ve dağıtım

Uygulama proxy 'Sini başarıyla dağıtmak için en az bir Bağlayıcınız olması gerekir, ancak daha fazla esneklik için iki veya daha fazla önerilir. Bağlayıcıyı Windows Server 2012 R2 veya üstünü çalıştıran bir makineye yükler. Bağlayıcının, uygulama proxy hizmeti ve yayımladığınız şirket içi uygulamalarla iletişim kurması gerekir.

### <a name="windows-server"></a>Windows sunucusu
Uygulama proxy bağlayıcısını yükleyebileceğiniz Windows Server 2012 R2 veya üstünü çalıştıran bir sunucuya ihtiyacınız vardır. Sunucunun Azure 'daki uygulama proxy hizmetlerine ve yayımlamakta olduğunuz şirket içi uygulamalara bağlanması gerekir.

Uygulama proxy bağlayıcısını yüklemeden önce Windows Server 'ın TLS 1,2 özelliğinin etkinleştirilmesi gerekir. Sunucuda TLS 1,2 ' i etkinleştirmek için:

1. Aşağıdaki kayıt defteri anahtarlarını ayarlayın:

    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Sunucuyu yeniden başlatın

Bağlayıcı sunucusu için ağ gereksinimleri hakkında daha fazla bilgi için bkz. [uygulama proxy 'si ile çalışmaya başlama ve bağlayıcı yüklemeyi](application-proxy-add-on-premises-application.md).

## <a name="maintenance"></a>Bakım

Bağlayıcılar ve hizmet, tüm yüksek oranda kullanılabilir görevleri ele alır. Dinamik olarak eklenebilir veya kaldırılabilirler. Her yeni istek geldiğinde, şu anda kullanılabilir olan bağlayıcılardan birine yönlendirilir. Bir bağlayıcı geçici olarak kullanılamıyorsa, bu trafiğe yanıt vermez.

Bağlayıcılar durum bilgisiz ve makinede yapılandırma verisi yok. Yalnızca depotıkları veriler, hizmeti ve kimlik doğrulama sertifikasını bağlamak için ayarlar. Hizmete bağlandıklarında, gerekli tüm yapılandırma verilerini çeker ve her birkaç dakikada bir yeniler.

Bağlayıcılar Ayrıca, bağlayıcının daha yeni bir sürümü olup olmadığını öğrenmek için sunucuyu yoklayın. Bir tane bulunursa, bağlayıcılar günceldir.

Üzerinde çalıştıkları makineden, olay günlüğü ve performans sayaçlarını kullanarak bağlayıcılarınızı izleyebilirsiniz. Ya da Azure portal uygulama proxy 'Si sayfasından durumlarını görüntüleyebilirsiniz:

![Örnek: Azure AD Uygulama Ara Sunucusu bağlayıcıları](./media/application-proxy-connectors/app-proxy-connectors.png)

Kullanılmayan bağlayıcıları el ile silmeniz gerekmez. Bir bağlayıcı çalışırken, hizmete bağlanırken etkin kalır. Kullanılmayan bağlayıcılar, _etkin değil_ olarak etiketlenebilir ve 10 gün etkin olmama durumunda kaldırılır. Bir bağlayıcıyı kaldırmak istiyorsanız, hem bağlayıcı hizmetini hem de Güncelleştirici hizmetini sunucudan kaldırın. Hizmeti tam olarak kaldırmak için bilgisayarınızı yeniden başlatın.

## <a name="automatic-updates"></a>Otomatik güncelleştirmeler

Azure AD, dağıttığınız tüm bağlayıcılar için otomatik güncelleştirmeler sağlar. Uygulama proxy 'Si Bağlayıcı Güncelleştiricisi hizmeti çalıştığı sürece, bağlayıcılar [en son ana bağlayıcı sürümüyle otomatik olarak güncellidir](application-proxy-faq.yml#why-is-my-connector-still-using-an-older-version-and-not-auto-upgraded-to-latest-version-) . Sunucunuzda Bağlayıcı Güncelleştiricisi hizmetini görmüyorsanız, tüm güncelleştirmeleri almak için [bağlayıcınızı yeniden yüklemeniz](application-proxy-add-on-premises-application.md) gerekir.

Otomatik güncelleştirme 'nin bağlayıcınıza gelmesini beklemek istemiyorsanız, el ile yükseltme yapabilirsiniz. Bağlayıcının bulunduğu sunucudaki [bağlayıcı indirme sayfasına](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) gidin ve **İndir**' i seçin. Bu işlem, yerel bağlayıcı için yükseltmeyi devre dışı bırakır.

Birden çok bağlayıcı içeren kiracılar için, otomatik güncelleştirmeler ortamınızda kapalı kalma süresini engellemek için her bir gruptaki her seferinde bir bağlayıcıyı hedefleyin.

Şu durumlarda Bağlayıcınız güncelleştirildiğinde kapalı kalma süresine karşılaşabilirsiniz:
  
- İkinci bir bağlayıcı yüklemenizi ve [bir bağlayıcı grubu oluşturmanızı](application-proxy-connector-groups.md)öneririz. Bu, kapalı kalma süresini ortadan kaldırmak ve daha yüksek kullanılabilirlik sağlar.  
- Bir bağlayıcı, güncelleştirme başladığında bir işlemin ortasındaysa. İlk işlem kaybolsa da, tarayıcınız işlemi otomatik olarak yeniden deneyecek veya sayfanızı yenileyebilmelisiniz. İstek yeniden gönderilir, trafik bir yedekleme bağlayıcısına yönlendirilir.

Önceden yayınlanan sürümler ve içerdikleri değişiklikler hakkında bilgi almak için bkz. [uygulama proxy sürümü yayın geçmişi](application-proxy-release-version-history.md).

## <a name="creating-connector-groups"></a>Bağlayıcı grupları oluşturma

Bağlayıcı grupları belirli uygulamalara olanak tanımak için belirli bağlayıcılar atamanızı sağlar. Bir dizi bağlayıcıyı birlikte gruplandırabilir ve sonra her uygulamayı bir gruba atayabilirsiniz.

Bağlayıcı grupları büyük dağıtımları yönetmeyi kolaylaştırır. Ayrıca, yalnızca yerel uygulamalara sunmak üzere konum tabanlı bağlayıcı grupları oluşturabileceğiniz için farklı bölgelerde barındırılan uygulamalara sahip kiracılar için gecikme süresini de geliştirir.

Bağlayıcı grupları hakkında daha fazla bilgi edinmek için bkz. [bağlayıcı gruplarını kullanarak ayrı ağlarda ve konumlarda uygulama yayımlama](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>Kapasite planlaması

Beklenen trafik birimini işlemek için bağlayıcılar arasında yeterli kapasiteye sahip olduğunuzdan emin olmak önemlidir. Yüksek kullanılabilirlik ve ölçek sağlamak için her bir bağlayıcı grubunda en az iki bağlayıcı olmasını öneririz. Her zaman bir makineye hizmet etmeniz gerekebilmeniz için üç bağlayıcı olması en iyi durumdur.

Genellikle, ne kadar çok kullanıcı varsa, ihtiyacınız olacak bir makine daha büyük olur. Aşağıda birimin ana hattını sağlayan bir tablo ve farklı makinelerin işleyebileceği beklenen gecikme süresi verilmiştir. Bu, kullanım desenleri farklılık gösterdiğinden ve yükü tahmin etmek için kullanılamadığından, Kullanıcı tarafından değil, bir saniyede beklenen Işlem (TPS) temel alır. Ayrıca, yanıtların boyutuna ve arka uç uygulama yanıt süresi ve daha yavaş yanıt süreleriyle ilgili bazı farklılıklar da daha düşük olan en büyük TPS 'ye neden olur. Ayrıca, makineler genelinde dağıtılmış yükün her zaman geniş bir arabellek sağladığından, ek makineler de yapmanızı öneririz. Ekstra kapasite yüksek kullanılabilirlik ve dayanıklılık sahibi olmanızı sağlayacaktır.

|Çekirdekler|RAM|Beklenen gecikme süresi (MS)-P99|En fazla TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200 *|

\* Bu makine, .NET tarafından önerilen ayarların ötesinde bazı varsayılan bağlantı sınırlarını yükseltmek için özel bir ayar kullandı. Kiracınız için bu sınırın değiştirilmesini sağlamak üzere desteğe başvurmadan önce varsayılan ayarlarla bir test çalıştırmayı öneririz.

> [!NOTE]
> 4, 8 ve 16 çekirdek makineler arasındaki en fazla TPS 'de çok fark yoktur. Bunlar arasındaki temel fark beklenen gecikmedir.
>
> Bu tablo, üzerinde yüklü olduğu makinenin türüne bağlı olarak bir bağlayıcının beklenen performansına de odaklanır. Bu, uygulama proxy 'Si hizmetinin azaltma limitlarından ayrıdır, bkz. [hizmet limitleri ve kısıtlamaları](../enterprise-users/directory-service-limits-restrictions.md).

## <a name="security-and-networking"></a>Güvenlik ve ağ

Bağlayıcılar, ağ üzerinde her yere yüklenebilir ve bu sayede istekleri uygulama proxy 'Si hizmetine gönderebilirler. Ne önemli olan, bağlayıcıyı çalıştıran bilgisayarın da uygulamalarınıza erişimi vardır. Şirket ağınızın içine veya bulutta çalışan bir sanal makineye bağlayıcılar yükleyebilirsiniz. Bağlayıcılar, sivilleştirilmiş bölge (DMZ) olarak da bilinen bir çevre ağı içinde çalışabilir, ancak ağınızın güvenliği güvende kalacak şekilde tüm trafik giden bir çıkış olduğundan bu gerekli değildir.

Bağlayıcılar yalnızca giden istekleri gönderir. Giden trafik, uygulama proxy 'Si hizmetine ve yayımlanan uygulamalara gönderilir. Trafik bir oturum kurulduktan sonra iki şekilde akışa alındığından gelen bağlantı noktalarını açmanız gerekmez. Ayrıca, güvenlik duvarlarınız aracılığıyla gelen erişimi yapılandırmanız gerekmez.

Giden güvenlik duvarı kurallarını yapılandırma hakkında daha fazla bilgi için bkz. [mevcut şirket içi proxy sunucularıyla çalışma](application-proxy-configure-connectors-with-proxy-servers.md).

## <a name="performance-and-scalability"></a>Performans ve ölçeklenebilirlik

Uygulama proxy 'Si hizmetinin ölçeği saydam, ancak ölçek bağlayıcılar için bir faktördür. Yoğun trafiği işlemek için yeterli sayıda bağlayıcı olması gerekir. Bağlayıcılar durum bilgisiz olduğundan Kullanıcı veya oturum sayısından etkilenmemektedir. Bunun yerine, istek sayısına ve bunların yük boyutuna yanıt verir. Standart Web trafiğiyle ortalama bir makine saniyede birkaç bin isteği işleyebilir. Belirli kapasite, tam makine özelliklerine bağlıdır.

Bağlayıcı performansı, CPU ve ağ ile bağlanır. TLS şifreleme ve şifre çözme için CPU performansı gerekir. ağ, Azure 'daki uygulamalara ve çevrimiçi hizmete hızlı bağlantı sağlamak için önemlidir.

Buna karşılık, bellek, bağlayıcılar için bir sorundan daha küçüktür. Çevrimiçi hizmet, işlemenin ve tüm kimliği doğrulanmamış trafiğin çoğunu üstlenir. Bulutta yapılabilecek her şey bulutta yapılır.

Bağlayıcının veya makinenin kullanılamaz hale gelmesi herhangi bir nedenden dolayı trafik gruptaki başka bir bağlayıcıya devam eder. Bu esneklik Ayrıca birden çok bağlayıcı kullanmanızı öneriyoruz.

Performansı etkileyen bir başka faktör de şunlar dahil olmak üzere bağlayıcılar arasındaki ağ kalitesi olur:

- **Çevrimiçi hizmet**: Azure 'Da uygulama proxy hizmeti için yavaş veya yüksek gecikmeli bağlantılar, bağlayıcı performansını etkiler. En iyi performansı elde etmek için, kuruluşunuzun Express Route ile Azure 'a bağlanmasını sağlar. Aksi takdirde, ağ takımınızın Azure bağlantılarının mümkün olduğunca verimli bir şekilde işlendiğinden emin olun.
- **Arka uç uygulamaları**: bazı durumlarda bağlayıcı ve bağlantıları yavaşlatabilecek ya da engelleyebilen arka uç uygulamaları arasında ek proxy 'ler vardır. Bu senaryoda sorun gidermek için bağlayıcı sunucusundan bir tarayıcı açın ve uygulamaya erişmeyi deneyin. Bağlayıcıları Azure 'da çalıştırırsanız, ancak uygulamalar Şirket içi ise, deneyim kullanıcılarınızın beklediği şekilde çalışmayabilir.
- **Etki alanı denetleyicileri**: bağlayıcılar, Kerberos kısıtlanmış temsilciyi kullanarak çoklu oturum açma (SSO) gerçekleştirdiklerinde, isteği arka uca göndermeden önce etki alanı denetleyicileriyle iletişim kurabilirler. Bağlayıcılar Kerberos biletleri önbelleğine sahiptir, ancak meşgul bir ortamda, etki alanı denetleyicilerinin yanıt verme hızı performansı etkileyebilir. Bu sorun, Azure 'da çalışan ancak şirket içi etki alanı denetleyicileri ile iletişim kuran bağlayıcılar için daha yaygın bir sorundur.

Ağınızı en iyi duruma getirme hakkında daha fazla bilgi için, [Azure Active Directory uygulama ara sunucusu kullanırken ağ topolojisi konularına](application-proxy-network-topology.md)bakın.

## <a name="domain-joining"></a>Etki alanına katılma

Bağlayıcılar, etki alanına katılmamış bir makinede çalıştırılabilir. Ancak, tümleşik Windows kimlik doğrulaması (ıWA) kullanan uygulamalar için çoklu oturum açma (SSO) istiyorsanız, etki alanına katılmış bir makineye ihtiyacınız vardır. Bu durumda, bağlayıcı makineleri yayımlanan uygulamalar için kullanıcılar adına [Kerberos](https://web.mit.edu/kerberos) kısıtlı temsili gerçekleştirebilen bir etki alanına katılmalıdır.

Bağlayıcılar, kısmi güven veya salt okuma etki alanı denetleyicileri olan ormanlarda bulunan etki alanlarına da katılabilir.

## <a name="connector-deployments-on-hardened-environments"></a>Sağlamlaştırılmış ortamlarda bağlayıcı dağıtımları

Genellikle, bağlayıcı dağıtımı basittir ve özel yapılandırma gerektirmez. Ancak göz önünde bulundurmanız gereken bazı benzersiz koşullar vardır:

- Giden trafiği sınırlayan kuruluşların [gereken bağlantı noktalarını açması](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)gerekir.
- FIPS uyumlu makineler, bağlayıcı işlemlerinin bir sertifika oluşturmasına ve depolamasına izin verecek şekilde yapılandırmalarını değiştirmek için gerekebilir.
- Ağ isteklerini veren işlemlere bağlı olarak ortamlarını kilitleyen kuruluşlar, her iki bağlayıcı hizmetinin gerekli tüm bağlantı noktalarına ve IP 'Lere erişmek için etkinleştirildiğinden emin olmak zorundadır.
- Bazı durumlarda, giden iletme proxy 'leri iki yönlü sertifika kimlik doğrulamasını bozabilir ve iletişimin başarısız olmasına neden olabilir.

## <a name="connector-authentication"></a>Bağlayıcı kimlik doğrulaması

Güvenli bir hizmet sağlamak için bağlayıcılar hizmete doğru kimlik doğrulaması yapmak ve hizmetin bağlayıcıya doğru kimlik doğrulaması yapması gerekir. Bu kimlik doğrulaması, bağlayıcılar bağlantıyı başlatabileceğinden istemci ve sunucu sertifikaları kullanılarak yapılır. Bu şekilde yöneticinin Kullanıcı adı ve parolası bağlayıcı makinede depolanmaz.

Kullanılan sertifikalar, uygulama proxy 'Si hizmetine özgüdür. Bunlar ilk kayıt sırasında oluşturulur ve her iki ayda bir bağlayıcı tarafından otomatik olarak yenilenir.

İlk başarılı sertifika yenilemesinin ardından Azure AD Uygulama Ara Sunucusu bağlayıcı hizmeti 'nin (ağ hizmeti) yerel makine deposundan eski sertifikayı kaldırma izni yoktur. Sertifikanın süresi dolmuşsa veya hizmet tarafından artık kullanılmıyorsa, güvenli bir şekilde silebilirsiniz.

Sertifika yenilemeyle ilgili sorunlardan kaçınmak için, bağlayıcıdan [belgelenen hedeflere](./application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) doğru olan ağ iletişiminin etkinleştirildiğinden emin olun.

Bir bağlayıcı birkaç ay için hizmete bağlı değilse, sertifikaları güncel olmayabilir. Bu durumda, kayıt tetiklemek için bağlayıcıyı kaldırın ve yeniden yükleyin. Aşağıdaki PowerShell komutlarını çalıştırabilirsiniz:

```
Import-module AppProxyPSModule
Register-AppProxyConnector -EnvironmentName "AzureCloud"
```

Kamu için kullanın `-EnvironmentName "AzureUSGovernment"` . Daha ayrıntılı bilgi için bkz. [Azure Kamu Bulutu Için aracı 'Nı yükler](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud).

Sertifikayı doğrulama ve sorun giderme hakkında daha fazla bilgi edinmek için bkz. [uygulama proxy 'si güven sertifikası Için makine ve arka uç bileşenleri desteğini doğrulayın](application-proxy-connector-installation-problem.md#verify-machine-and-backend-components-support-for-application-proxy-trust-certificate).

## <a name="under-the-hood"></a>Başlık altında

Bağlayıcılar Windows Server Web uygulaması ara sunucusu 'nu temel alır, bu nedenle Windows olay günlükleri de dahil olmak üzere aynı yönetim araçlarının çoğuna sahip olurlar

![Olay günlüklerini Olay Görüntüleyicisi yönetme](./media/application-proxy-connectors/event-view-window.png)

ve Windows performans sayaçları.

![Performans Izleyicisi ile bağlayıcıya sayaç ekleme](./media/application-proxy-connectors/performance-monitor.png)

Bağlayıcılar **yönetici** ve **oturum** günlüklerine sahiptir. **Yönetici** günlüğü, anahtar olayları ve bunların hatalarını içerir. **Oturum** günlüğü tüm işlemleri ve bunların işleme ayrıntılarını içerir.

Günlükleri görmek için **Olay Görüntüleyicisi** açın ve **uygulamalar ve hizmetler Günlükler**  >  **Microsoft**  >  **aadapplicationproxy**  >  **Bağlayıcısı**' na gidin. **Oturum** günlüğünün görünür olması Için, **Görünüm** menüsünde **analitik ve hata ayıklama günlüklerini göster**' i seçin. **Oturum** günlüğü genellikle sorun giderme için kullanılır ve varsayılan olarak devre dışıdır. Olayları toplamaya başlamasını ve artık gerekli olmadığında devre dışı bırakmayı etkinleştirir.

Hizmetin durumunu Hizmetler penceresinde inceleyebilirsiniz. Bağlayıcı iki Windows hizmetinden oluşur: gerçek bağlayıcı ve Güncelleştirici. Her ikisinin de her ikisi de her zaman çalıştırması gerekir.

 ![Örnek: Azure AD hizmetleri yerel ' i gösteren hizmetler penceresi](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Bağlayıcı gruplarını kullanarak ayrı ağlarda ve konumlarda uygulama yayımlama](application-proxy-connector-groups.md)
- [Mevcut şirket içi proxy sunucularıyla çalışma](application-proxy-configure-connectors-with-proxy-servers.md)
- [Uygulama proxy 'Si ve bağlayıcı hatalarında sorun giderme](application-proxy-troubleshoot.md)
- [Azure AD Uygulama Ara Sunucusu bağlayıcısını sessizce yüklemek](application-proxy-register-connector-powershell.md)
