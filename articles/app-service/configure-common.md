---
title: Portalda Uygulama Yapılandırma-Azure App Service
description: Azure portal bir App Service uygulamasının ortak ayarlarını yapılandırmayı öğrenin.
keywords: Azure App Service, Web uygulaması, uygulama ayarları, ortam değişkenleri
services: app-service\web
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c22f88487fd8b34d48d3012c706bb0415760b21e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470946"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Azure portal App Service uygulama yapılandırma

Bu konuda, [Azure Portalı]kullanarak Web Apps, mobil arka uç veya API uygulaması için genel ayarların nasıl yapılandırılacağı açıklanmaktadır.

## <a name="configure-app-settings"></a>Uygulama ayarlarını yapılandırma

App Service, uygulama ayarları, uygulama koduna ortam değişkenleri olarak geçirilen değişkenlerdir. Linux uygulamaları ve özel kapsayıcılar için App Service, kapsayıcıda ortam değişkenini ayarlamak için `--env` bayrağını kullanarak uygulama ayarlarını kapsayıcıya geçirir.

[Azure Portalı]uygulamanızın yönetim sayfasına gidin. Uygulamanın sol menüsünde **yapılandırma** > **uygulama ayarları**' na tıklayın.

![Uygulama ayarları](./media/configure-common/open-ui.png)

ASP.NET ve ASP.NET Core geliştiricileri için, App Service uygulama ayarlarının ayarlanması, *Web. config* veya *appSettings. JSON*içinde `<appSettings>` ayarlama gibidir, ancak App Service içindeki değerler *Web. config* veya *appSettings. JSON içinde olanları geçersiz kılar* . *Web. config* veya *appSettings. JSON*içinde geliştirme ayarlarını (örneğin, yerel MySQL parolası) tutabilir, ancak üretim gizli dizileri (örneğin, Azure MySQL veritabanı parolası) App Service güvenlidir. Aynı kod, yerel olarak hata ayıkladığınızda geliştirme ayarlarınızı kullanır ve Azure 'a dağıtıldığında üretim sırlarınızı kullanır.

Benzer şekilde, diğer dil yığınları, çalışma zamanında ortam değişkenleri olarak uygulama ayarlarını alır. Dil yığınına özgü adımlar için bkz.:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Özel kapsayıcılar](containers/configure-custom-container.md#configure-environment-variables)

Uygulama ayarları her zaman (REST-REST) depolandığında şifrelenir.

> [!NOTE]
> Uygulama ayarları, [Key Vault başvuruları](app-service-key-vault-references.md)kullanılarak [Key Vault](/azure/key-vault/) de çözülebilir.

### <a name="show-hidden-values"></a>Gizli değerleri göster

Varsayılan olarak, uygulama ayarları için değerler portalda güvenlik için gizlidir. Bir uygulama ayarının gizli değerini görmek için, bu ayarın **değer** alanına tıklayın. Tüm uygulama ayarlarının değerlerini görmek için **değeri göster** düğmesine tıklayın.

### <a name="add-or-edit"></a>Ekle veya Düzenle

Yeni bir uygulama ayarı eklemek için **Yeni uygulama ayarı**' na tıklayın. İletişim kutusunda, [ayarı geçerli yuvaya](deploy-staging-slots.md#which-settings-are-swapped)ekleyebilirsiniz.

Bir ayarı düzenlemek için sağ taraftaki **Düzenle** düğmesine tıklayın.

İşiniz bittiğinde **Güncelleştir**' e tıklayın. **Yapılandırma** sayfasında geri **Kaydet** ' e tıklamasını unutmayın.

> [!NOTE]
> Varsayılan bir Linux kapsayıcısında veya özel bir Linux kapsayıcısında, `ApplicationInsights:InstrumentationKey` gibi uygulama ayarı adındaki iç içe geçmiş tüm JSON anahtar yapısının, anahtar adı için `ApplicationInsights__InstrumentationKey` olarak App Service yapılandırılması gerekir. Diğer bir deyişle, tüm `:` `__` (çift alt çizgi) ile değiştirilmelidir.
>

### <a name="edit-in-bulk"></a>Toplu olarak Düzenle

Uygulama ayarlarını toplu olarak eklemek veya düzenlemek için **Gelişmiş Düzenle** düğmesine tıklayın. İşiniz bittiğinde **Güncelleştir**' e tıklayın. **Yapılandırma** sayfasında geri **Kaydet** ' e tıklamasını unutmayın.

Uygulama ayarları aşağıdaki JSON biçimlendirmesine sahiptir:

```json
[
  {
    "name": "<key-1>",
    "value": "<value-1>",
    "slotSetting": false
  },
  {
    "name": "<key-2>",
    "value": "<value-2>",
    "slotSetting": false
  },
  ...
]
```

## <a name="configure-connection-strings"></a>Bağlantı dizelerini yapılandırma

[Azure Portalı], uygulamanın yönetim sayfasına gidin. Uygulamanın sol menüsünde **yapılandırma** > **uygulama ayarları**' na tıklayın.

![Uygulama ayarları](./media/configure-common/open-ui.png)

ASP.NET ve ASP.NET Core geliştiricileri için, App Service bağlantı dizelerinin, *Web. config*'de `<connectionStrings>` ayarlama gibidir, ancak App Service ' de ayarladığınız değerler *Web. config*'de olanları geçersiz kılar. *Web. config* ve üretim gizli dizileri (ÖRNEĞIN, SQL veritabanı kimlik bilgileri) ' nde güvenli App Service için geliştirme ayarlarını (örneğin, veritabanı dosyası) koruyabilirsiniz. Aynı kod, yerel olarak hata ayıkladığınızda geliştirme ayarlarınızı kullanır ve Azure 'a dağıtıldığında üretim sırlarınızı kullanır.

Diğer dil yığınları için bunun yerine [uygulama ayarlarının](#configure-app-settings) kullanılması daha iyidir, çünkü bağlantı dizeleri değerlere erişmek için değişken anahtarlarında özel biçimlendirme gerektirir. Ancak bir özel durum aşağıda verilmiştir: uygulamanızda bağlantı dizelerini yapılandırırsanız, belirli Azure veritabanı türleri uygulamayla birlikte yedeklenir. Daha fazla bilgi için bkz. [nelerin yedeklenebileceği](manage-backup.md#what-gets-backed-up). Bu otomatik yedeklemeye ihtiyacınız yoksa uygulama ayarları ' nı kullanın.

Çalışma zamanında, bağlantı dizeleri aşağıdaki bağlantı türleri ön eki olan ortam değişkenleri olarak kullanılabilir:

* SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* SQL veritabanı: `SQLAZURECONNSTR_`
* Özel: `CUSTOMCONNSTR_`

Örneğin, *connectionstring1* adlı bir MySQL bağlantı dizesi `MYSQLCONNSTR_connectionString1`ortam değişkeni olarak erişilebilir. Dil yığınına özgü adımlar için bkz.:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Özel kapsayıcılar](containers/configure-custom-container.md#configure-environment-variables)

Bağlantı dizeleri her zaman (REST-REST) depolandığında şifrelenir.

> [!NOTE]
> Bağlantı dizeleri, [Key Vault başvuruları](app-service-key-vault-references.md)kullanılarak [Key Vault](/azure/key-vault/) de çözülebilir.

### <a name="show-hidden-values"></a>Gizli değerleri göster

Varsayılan olarak, bağlantı dizeleri için değerler portalda güvenlik için gizlenir. Bir bağlantı dizesinin gizli değerini görmek için bu dizenin **değer** alanına tıklamanız yeterlidir. Tüm bağlantı dizelerinin değerlerini görmek için **değeri göster** düğmesine tıklayın.

### <a name="add-or-edit"></a>Ekle veya Düzenle

Yeni bağlantı dizesi eklemek için **Yeni bağlantı dizesi**' ne tıklayın. İletişim kutusunda [bağlantı dizesini geçerli yuvaya](deploy-staging-slots.md#which-settings-are-swapped)ekleyebilirsiniz.

Bir ayarı düzenlemek için sağ taraftaki **Düzenle** düğmesine tıklayın.

İşiniz bittiğinde **Güncelleştir**' e tıklayın. **Yapılandırma** sayfasında geri **Kaydet** ' e tıklamasını unutmayın.

### <a name="edit-in-bulk"></a>Toplu olarak Düzenle

Bağlantı dizelerini toplu olarak eklemek veya düzenlemek için **Gelişmiş Düzenle** düğmesine tıklayın. İşiniz bittiğinde **Güncelleştir**' e tıklayın. **Yapılandırma** sayfasında geri **Kaydet** ' e tıklamasını unutmayın.

Bağlantı dizeleri aşağıdaki JSON biçimlendirmesine sahiptir:

```json
[
  {
    "name": "name-1",
    "value": "conn-string-1",
    "type": "SQLServer",
    "slotSetting": false
  },
  {
    "name": "name-2",
    "value": "conn-string-2",
    "type": "PostgreSQL",
    "slotSetting": false
  },
  ...
]
```

<a name="platform"></a>
<a name="alwayson"></a>

## <a name="configure-general-settings"></a>Genel ayarları yapılandırma

[Azure Portalı], uygulamanın yönetim sayfasına gidin. Uygulamanın sol menüsünde **yapılandırma** > **uygulama ayarları**' na tıklayın.

![Genel ayarlar](./media/configure-common/open-general.png)

Burada, uygulama için bazı ortak ayarları yapılandırabilirsiniz. Bazı ayarlar, [daha yüksek fiyatlandırma katmanlarına kadar ölçeklendirmenizi](manage-scale-up.md)gerektirir.

- **Yığın ayarları**: DIL ve SDK sürümleri dahil olmak üzere uygulamayı çalıştırmak için yazılım yığını. Linux uygulamaları ve özel kapsayıcı uygulamaları için isteğe bağlı bir başlangıç komutu veya dosyası da ayarlayabilirsiniz.
- **Platform ayarları**: barındırma platformu için aşağıdakiler de dahil olmak üzere ayarları yapılandırmanıza olanak sağlar:
    - **Bit genişliği: 32**-bit veya 64 bit.
    - **WebSocket protokolü**: örneğin, [ASP.net signalr] veya [Socket.io](https://socket.io/)için.
    - **Always on**: hiçbir trafik olmadığında bile uygulamanın yüklü kalmasını sağlayın. Sürekli Web Işleri veya bir CRON ifadesi kullanılarak tetiklenen Web Işleri için gereklidir.
    - **Yönetilen işlem hattı sürümü**: IIS [ardışık düzen modu]. Daha eski bir IIS sürümü gerektiren eski bir uygulamanız varsa, bunu **Klasik** olarak ayarlayın.
    - **Http sürümü**: [https/2](https://wikipedia.org/wiki/HTTP/2) protokolü desteğini etkinleştirmek için **2,0** olarak ayarlayın.
    > [!NOTE]
    > Modern tarayıcıların çoğu yalnızca TLS üzerinden HTTP/2 protokolünü destekler, şifrelenmemiş trafik HTTP/1.1 kullanmaya devam eder. İstemci tarayıcılarının uygulamanıza HTTP/2 ile bağlanmasını sağlamak için, [özel DNS adınızı Azure App Service BIR SSL bağlamasıyla güvenli hale](configure-ssl-bindings.md)getirin.
    - **ARR benzeşimi**: çok örnekli bir dağıtımda, istemcinin oturum süresince aynı örneğe yönlendirildiğinden emin olun. Durum bilgisi olmayan uygulamalar için bu seçeneği **kapalı** olarak ayarlayabilirsiniz.
- **Hata ayıklama**: [ASP.net](troubleshoot-dotnet-visual-studio.md#remotedebug), [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)veya [Node. js](containers/configure-language-nodejs.md#debug-remotely) uygulamaları için uzaktan hata ayıklamayı etkinleştirin. Bu seçenek, 48 saat sonra otomatik olarak devre dışı bırakır.
- **Gelen istemci sertifikaları**: [karşılıklı kimlik doğrulamasında](app-service-web-configure-tls-mutual-auth.md)istemci sertifikaları gerektir.

## <a name="configure-default-documents"></a>Varsayılan belgeleri yapılandırma

Bu ayar yalnızca Windows uygulamaları içindir.

[Azure Portalı], uygulamanın yönetim sayfasına gidin. Uygulamanın sol menüsünde **yapılandırma** > **Varsayılan belgeler**' e tıklayın.

![Genel ayarlar](./media/configure-common/open-documents.png)

Varsayılan belge, bir Web sitesinin kök URL 'sinde görüntülenen Web sayfasıdır. Listedeki ilk eşleşen dosya kullanılır. Yeni bir varsayılan belge eklemek için **Yeni belge**' ye tıklayın. **Kaydet**' e tıklamasını unutmayın.

Uygulama, statik içerik sunmak yerine URL 'ye bağlı olarak yönlendiren modüller kullanıyorsa, varsayılan belgelere gerek yoktur.

## <a name="configure-path-mappings"></a>Yol eşlemelerini yapılandırma

[Azure Portalı], uygulamanın yönetim sayfasına gidin. Uygulamanın sol menüsünde, **yapılandırma** > **yol eşlemeleri**' ne tıklayın.

![Genel ayarlar](./media/configure-common/open-path.png)

**Yol eşlemeleri** sayfası, işletim sistemi türüne göre farklı şeyler gösterir.

### <a name="windows-apps-uncontainerized"></a>Windows uygulamaları (Kapsayıcılı olmayan)

Windows uygulamaları için IIS İşleyici eşlemelerini ve sanal uygulamaları ve dizinleri özelleştirebilirsiniz.

İşleyici eşlemeleri belirli dosya uzantılarına yönelik istekleri işlemek için özel betik işlemcileri eklemenize olanak sağlar. Özel bir işleyici eklemek için **yeni işleyici**' ye tıklayın. İşleyiciyi şu şekilde yapılandırın:

- **Uzantı**. İşlemek istediğiniz *\*. php* veya *Handler. FCGI*gibi dosya uzantısı.
- **Betik işlemcisi**. Betik işlemcisinin size mutlak yolu. Dosya uzantısıyla eşleşen dosya istekleri betik işlemcisi tarafından işlenir. Uygulamanızın kök dizinine başvurmak için `D:\home\site\wwwroot` yolunu kullanın.
- **Bağımsız değişkenler**. Betik işlemcisi için isteğe bağlı komut satırı bağımsız değişkenleri.

Her uygulama, kodunuzun varsayılan olarak dağıtıldığı `D:\home\site\wwwroot`eşlenen varsayılan kök yoluna (`/`) sahiptir. Uygulama kökleriniz farklı bir klasörlerde varsa veya deponuzda birden fazla uygulama varsa, sanal uygulamaları ve dizinleri buradan düzenleyebilir veya ekleyebilirsiniz. **Yeni sanal uygulama veya dizin**' e tıklayın.

Sanal uygulamaları ve dizinleri yapılandırmak için, her bir sanal dizini ve ilgili fiziksel yolunu Web sitesi köküne (`D:\home`) göre belirtin. İsteğe bağlı olarak, bir sanal dizini bir uygulama olarak işaretlemek için **uygulama** onay kutusunu seçebilirsiniz.

### <a name="containerized-apps"></a>Kapsayıcılı uygulamalar

[Kapsayıcılı uygulamanız için özel depolama ekleyebilirsiniz](containers/how-to-serve-content-from-azure-storage.md). Kapsayıcılı uygulamalar tüm Linux uygulamalarını ve Ayrıca App Service çalışan Windows ve Linux özel kapsayıcılarını içerir. **Yeni Azure Storage bağlama** ' ya tıklayın ve özel depolama alanınızı aşağıdaki şekilde yapılandırın:

- **Ad**: görünen ad.
- **Yapılandırma seçenekleri**: **temel** veya **Gelişmiş**.
- **Depolama hesapları**: istediğiniz kapsayıcıya sahip depolama hesabı.
- **Depolama türü**: **Azure Blob 'Ları** veya **Azure dosyaları**.
  > [!NOTE]
  > Windows kapsayıcı uygulamaları yalnızca Azure dosyalarını destekler.
- **Depolama kapsayıcısı**: temel yapılandırma için istediğiniz kapsayıcı.
- **Paylaşma adı**: Gelişmiş yapılandırma için dosya paylaşımının adı.
- **Erişim anahtarı**: Gelişmiş yapılandırma için erişim anahtarı.
- **Bağlama yolu**: özel depolamayı bağlamak için kapsayıcınızda mutlak yol.

Daha fazla bilgi için bkz. [Linux üzerinde App Service Azure Storage 'tan içerik](containers/how-to-serve-content-from-azure-storage.md)sunma.

## <a name="configure-language-stack-settings"></a>Dil yığını ayarlarını yapılandırma

Linux uygulamaları için bkz.:

- [ASP.NET Core](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [PHP](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [Ruby](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Özel kapsayıcıları yapılandırma

Bkz. [Azure App Service için özel bir Linux kapsayıcısı yapılandırma](containers/configure-custom-container.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure App Service'te özel etki alanı adını yapılandırma]
- [Azure App Service’te hazırlık ortamları ayarlama]
- [Azure App Service 'de SSL bağlaması ile özel bir DNS adının güvenliğini sağlama](configure-ssl-bindings.md)
- [Tanılama günlüklerini etkinleştir](troubleshoot-diagnostic-logs.md)
- [Azure App Service bir uygulamayı ölçeklendirme]
- [Azure App Service temel bilgileri izleme]
- [ApplicationHost. xdt ile applicationHost. config ayarlarını değiştirme](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure Portalı]: https://portal.azure.com/
[Azure App Service'te özel etki alanı adını yapılandırma]: ./app-service-web-tutorial-custom-domain.md
[Azure App Service’te hazırlık ortamları ayarlama]: ./deploy-staging-slots.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Azure App Service temel bilgileri izleme]: ./web-sites-monitor.md
[ardışık düzen modu]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Azure App Service bir uygulamayı ölçeklendirme]: ./manage-scale-up.md
