---
title: Uygulamalar için tanılama günlüğünü etkinleştirme-Azure App Service
description: Tanılama günlüğünü etkinleştirme ve uygulamanıza izleme ekleme ve Azure tarafından günlüğe kaydedilen bilgilere erişme hakkında bilgi edinin.
services: app-service
author: cephalin
manager: gwallace
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/17/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 21600036302050aeea3e2ea989d86e18b208c087
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73958028"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Azure App Service uygulamalar için tanılama günlüğünü etkinleştirme
## <a name="overview"></a>Genel Bakış
Azure, [App Service bir uygulamada](overview.md)hata ayıklamaya yardımcı olmak için yerleşik tanılama sağlar. Bu makalede, tanılama günlüğünü etkinleştirmeyi ve uygulamanıza nasıl araç ekleneceğini ve Azure tarafından günlüğe kaydedilen bilgilere nasıl erişebileceğinizi öğreneceksiniz.

Bu makalede tanılama günlükleri ile çalışmak için [Azure Portal](https://portal.azure.com) ve Azure CLI kullanılmaktadır. Visual Studio kullanarak tanılama günlükleri ile çalışma hakkında daha fazla bilgi için bkz. [Visual Studio 'Da Azure sorunlarını giderme](troubleshoot-dotnet-visual-studio.md).

> [!NOTE]
> Bu makaledeki günlüğe kaydetme yönergelerine ek olarak, Azure Izleme ile yeni ve tümleşik günlüğe kaydetme özelliği de mevcuttur. Bu özellikten daha fazla bilgi edinmek [için günlükleri Azure Izleyicisine gönder (Önizleme) bölümüne bakın](#send-logs-to-azure-monitor-preview) . 
>
>

|Tür|Platform|Konum|Açıklama|
|-|-|-|-|
| Uygulama günlüğüne kaydetme | Windows, Linux | App Service dosya sistemi ve/veya Azure depolama Blobları | Uygulama kodunuz tarafından oluşturulan iletileri günlüğe kaydeder. İletiler seçtiğiniz Web çerçevesi tarafından veya Dilinizdeki standart günlük modelini kullanarak doğrudan uygulama kodunuzda oluşturulabilir. Her ileti şu kategorilerden birine atanır: **kritik**, **hata**, **Uyarı**, **bilgi**, **hata ayıklama**ve **izleme**. Uygulama günlüğünü etkinleştirdiğinizde önem düzeyini ayarlayarak günlüğün ne kadar ayrıntılı olmasını istediğinizi seçebilirsiniz.|
| Web sunucusu günlüğü| Windows | App Service dosya sistemi veya Azure depolama Blobları| [W3C Genişletilmiş günlük dosyası biçimindeki](/windows/desktop/Http/w3c-logging)ham http istek verileri. Her günlük iletisi HTTP yöntemi, kaynak URI, istemci IP, istemci bağlantı noktası, Kullanıcı Aracısı, yanıt kodu vb. gibi verileri içerir. |
| Ayrıntılı hata günlüğü | Windows | App Service dosya sistemi | İstemci tarayıcısına gönderilen *. htm* hata sayfalarının kopyaları. Güvenlik nedenleriyle, ayrıntılı hata sayfaları üretimde istemcilere gönderilmemelidir, ancak App Service HTTP kodu 400 veya üzerini içeren bir uygulama hatası oluştuğunda hata sayfasını kaydedebilirler. Sayfa, sunucunun neden hata kodunu döndürdüğünü belirlemede yardımcı olabilecek bilgiler içerebilir. |
| Başarısız istek izleme | Windows | App Service dosya sistemi | İsteği işlemek için kullanılan IIS bileşenlerinin izlenmesi ve her bileşende geçen süre dahil olmak üzere, başarısız istekler hakkında ayrıntılı izleme bilgileri. Site performansını artırmak veya belirli bir HTTP hatasını yalıtmak istiyorsanız yararlı olur. XML günlük dosyasını içeren her başarısız istek için bir klasör oluşturulur ve günlük dosyasını ile görüntülemek için XSL stil sayfası. |
| Dağıtım günlüğü | Windows, Linux | App Service dosya sistemi | Bir uygulamaya içerik yayımladığınızda günlüğe kaydedilir. Dağıtım günlüğü otomatik olarak gerçekleşir ve dağıtım günlüğü için yapılandırılabilir bir ayar yoktur. Dağıtımın neden başarısız olduğunu belirlemenize yardımcı olur. Örneğin, [özel bir dağıtım betiği](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)kullanıyorsanız, betiğin neden başarısız olduğunu anlamak için dağıtım günlüğünü kullanabilirsiniz. |

> [!NOTE]
> App Service, uygulamanızda sorun gidermenize yardımcı olmak için adanmış, etkileşimli bir tanılama aracı sağlar. Daha fazla bilgi için bkz. [Azure App Service tanılamayı genel bakış](overview-diagnostics.md).
>
> Ayrıca, [Azure izleyici](../azure-monitor/app/azure-web-apps.md)gibi uygulamanızın günlüğe kaydetme ve izleme yeteneklerini geliştirmek Için diğer Azure hizmetlerini de kullanabilirsiniz.
>

## <a name="enable-application-logging-windows"></a>Uygulama günlüğünü etkinleştir (Windows)

[Azure Portal](https://portal.azure.com)Windows uygulamaları için uygulama günlüğünü etkinleştirmek üzere uygulamanıza gidin ve **App Service Günlükler**' i seçin.

**Uygulama günlüğü (dosya sistemi)** veya **uygulama günlüğü (blob)** ya da her ikisi için **Açık** seçeneğini belirleyin. 

**Dosya sistemi** seçeneği geçici hata ayıklama amaçlarıyla yapılır ve 12 saat içinde kendisini kapatır. **BLOB** seçeneği uzun süreli günlüğe kaydetme içindir ve günlükleri yazmak için bir BLOB depolama kapsayıcısı gerekir.  **BLOB** seçeneği Ayrıca günlük iletilerinde (`InstanceId`) kaynak sanal makıne örneğinin kimliği, Iş parçacığı kimliği (`Tid`) ve daha ayrıntılı bir zaman damgası ([`EventTickCount`](https://docs.microsoft.com/dotnet/api/system.datetime.ticks)) gibi ek bilgiler içerir.

> [!NOTE]
> Şu anda yalnızca .NET uygulama günlükleri blob depolamaya yazılabilir. Java, PHP, Node. js, Python uygulama günlükleri yalnızca App Service dosya sisteminde depolanabilir (günlükleri dış depolamaya yazmak için kod değişiklikleri olmadan).
>
> Ayrıca, [depolama hesabınızın erişim anahtarlarını](../storage/common/storage-create-storage-account.md)yeniden oluşturursanız, ilgili günlük yapılandırmasını, güncelleştirilmiş erişim anahtarlarını kullanacak şekilde sıfırlamanız gerekir. Bunu yapmak için:
>
> 1. **Yapılandır** sekmesinde ilgili günlük özelliğini **kapalı**olarak ayarlayın. Ayarınızı kaydedin.
> 2. Depolama hesabı blobuna yeniden günlük kaydını etkinleştirin. Ayarınızı kaydedin.
>
>

Günlüğe kaydedilecek ayrıntı düzeyini veya **düzeyi**seçin. Aşağıdaki tabloda her düzeyde bulunan günlük kategorileri gösterilmektedir:

| Düzey | Dahil edilen Kategoriler |
|-|-|
|**Devre dışı** | None |
|**Hata:** | Hata, kritik |
|**Uyarı** | Uyarı, hata, kritik|
|**Bilgi** | Bilgi, uyarı, hata, kritik|
|**Seçeneini** | Trace, Debug, Info, uyarı, hata, kritik (tüm kategoriler) |

İşiniz bittiğinde **Kaydet**' i seçin.

## <a name="enable-application-logging-linuxcontainer"></a>Uygulama günlüğünü etkinleştir (Linux/container)

[Azure Portal](https://portal.azure.com)Linux uygulamaları veya özel kapsayıcı uygulamaları için uygulama günlüğünü etkinleştirmek üzere uygulamanıza gidin ve **App Service Günlükler**' i seçin.

**Uygulama günlüğü**' nde **dosya sistemi**' ni seçin.

**Kota (MB)** alanında uygulama günlükleri için disk kotasını belirtin. **Bekletme süresi (gün)** alanında günlüklerin saklanacağı gün sayısını ayarlayın.

İşiniz bittiğinde **Kaydet**' i seçin.

## <a name="enable-web-server-logging"></a>Web sunucusu günlüğünü etkinleştir

[Azure Portal](https://portal.azure.com)Windows uygulamaları için Web sunucusu günlüğünü etkinleştirmek üzere uygulamanıza gidin ve **App Service Günlükler**' i seçin.

**Web sunucusu günlüğü**için günlükleri blob depolamada depolamak üzere **depolama** ' yı veya App Service dosya sisteminde günlükleri depolamak için **dosya sistemi** ' ni seçin. 

**Bekletme süresi (gün)** alanında günlüklerin saklanacağı gün sayısını ayarlayın.

> [!NOTE]
> [Depolama hesabınızın erişim anahtarlarını](../storage/common/storage-create-storage-account.md)yeniden oluşturursanız, güncelleştirilmiş anahtarları kullanmak için ilgili günlük yapılandırmasını sıfırlamanız gerekir. Bunu yapmak için:
>
> 1. **Yapılandır** sekmesinde ilgili günlük özelliğini **kapalı**olarak ayarlayın. Ayarınızı kaydedin.
> 2. Depolama hesabı blobuna yeniden günlük kaydını etkinleştirin. Ayarınızı kaydedin.
>
>

İşiniz bittiğinde **Kaydet**' i seçin.

## <a name="log-detailed-errors"></a>Ayrıntılı hataları günlüğe kaydet

[Azure Portal](https://portal.azure.com)Windows uygulamaları için hata sayfasını veya başarısız istek izlemeyi kaydetmek için, uygulamanıza gidin ve **App Service Günlükler**' i seçin.

**Ayrıntılı hata günlüğü** veya **başarısız Istek izleme**altında **Açık**' ı seçin ve ardından **Kaydet**' i seçin.

Her iki günlük türü de App Service dosya sisteminde depolanır. 50 ' e kadar hata (dosya/klasör) korunur. HTML dosyaları sayısı 50 ' i aşarsa, en eski 26 hata otomatik olarak silinir.

## <a name="add-log-messages-in-code"></a>Koda günlük iletileri ekleme

Uygulama kodunuzda günlük iletilerini uygulama günlüklerine göndermek için her zamanki günlük tesislerini kullanırsınız. Örneğin:

- ASP.NET uygulamaları, uygulama tanılama günlüğüne bilgileri günlüğe kaydetmek için [System. Diagnostics. Trace](/dotnet/api/system.diagnostics.trace) sınıfını kullanabilir. Örneğin:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- Varsayılan olarak ASP.NET Core, [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) günlük sağlayıcısını kullanır. Daha fazla bilgi için bkz. [Azure 'da günlüğe kaydetme ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/).

## <a name="stream-logs"></a>Akış günlükleri

Günlükleri gerçek zamanlı olarak akışa almadan önce istediğiniz günlük türünü etkinleştirin. */LogFiles* dizininde (d:/Home/LogFiles) depolanan. txt,. log veya. htm ile biten dosyalara yazılan bilgiler App Service tarafından akışla kaydedilir.

> [!NOTE]
> Bazı günlük arabelleği türleri günlük dosyasına yazılır, bu da akıştaki sıra olayları oluşmasına neden olabilir. Örneğin, bir Kullanıcı bir sayfayı ziyaret ettiğinde oluşan bir uygulama günlüğü girişi, sayfa isteği için karşılık gelen HTTP günlük girişinden önce akışta görüntülenebilir.
>

### <a name="in-azure-portal"></a>Azure portal

[Azure Portal](https://portal.azure.com)günlükleri akışa almak için uygulamanıza gidin ve **günlük akışı**' nı seçin. 

### <a name="in-cloud-shell"></a>Cloud Shell

[Cloud Shell](../cloud-shell/overview.md)' de canlı günlükleri akışa almak için aşağıdaki komutu kullanın:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

Hatalar gibi belirli olayları filtrelemek için **--Filter** parametresini kullanın. Örneğin:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --filter Error
```
HTTP gibi belirli günlük türlerini filtrelemek için **--Path** parametresini kullanın. Örneğin:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --path http
```

### <a name="in-local-terminal"></a>Yerel terminalde

Günlükleri yerel konsolda akışa almak için [Azure CLI 'yı yükleyip](https://docs.microsoft.com/cli/azure/install-azure-cli) [hesabınızda oturum açın](https://docs.microsoft.com/cli/azure/authenticate-azure-cli). Oturum açtıktan sonra, [Cloud Shell için yönergeler](#in-cloud-shell) izlenir

## <a name="access-log-files"></a>Günlük dosyalarına erişin

Azure Storage bloblarını bir günlük türü için yapılandırırsanız, Azure depolama ile birlikte çalışarak bir istemci aracına ihtiyacınız vardır. Daha fazla bilgi için bkz. [Azure Storage Istemci araçları](../storage/common/storage-explorers.md).

App Service dosya sisteminde depolanan Günlükler için en kolay yol, ZIP dosyasını şu adresten tarayıcıda indirmeniz:

- Linux/kapsayıcı uygulamaları: `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Windows uygulamaları: `https://<app-name>.scm.azurewebsites.net/api/dump`

Linux/kapsayıcı uygulamaları için, ZIP dosyası hem Docker konağının hem de Docker kapsayıcısının konsol çıkış günlüklerini içerir. Ölçekli bir uygulama için, ZIP dosyası her örnek için bir günlük kümesi içerir. App Service dosya sisteminde, bu günlük dosyaları */Home/LogFiles* dizininin içeriğidir.

Windows uygulamaları için, ZIP dosyası App Service dosya sistemindeki *D:\home\logfiles* dizininin içeriğini içerir. Aşağıdaki yapıya sahiptir:

| Günlük türü | Dizin | Açıklama |
|-|-|-|
| **Uygulama günlükleri** |*/LogFiles/Application/* | Bir veya daha fazla metin dosyası içeriyor. Günlük iletilerinin biçimi kullandığınız günlük sağlayıcısına bağlıdır. |
| **Başarısız Istek Izlemeleri** | */LogFiles/W3SVC # # # # # # # # #/* | XML dosyalarını ve bir XSL dosyasını içerir. Biçimli XML dosyalarını tarayıcıda görüntüleyebilirsiniz. |
| **Ayrıntılı hata günlükleri** | */LogFiles/DetailedErrors/* | HTM hata dosyalarını içerir. HTM dosyalarını tarayıcıda görüntüleyebilirsiniz.<br/>Başarısız istek izlemelerini görüntülemenin bir başka yolu da portalda uygulama sayfanıza gitmenin bir yoludur. Sol menüden **Tanıla ve sorunları çöz**' ü seçin, sonra **başarısız istek izleme günlüklerini**arayın ve ardından istediğiniz izlemeye gözatıp görüntülemek için simgeye tıklayın. |
| **Web sunucusu günlükleri** | */LogFiles/http/RawLogs/* | [W3C Genişletilmiş günlük dosyası biçimi](/windows/desktop/Http/w3c-logging)kullanılarak biçimlendirilen metin dosyalarını içerir. Bu bilgiler, bir metin düzenleyici veya [günlük ayrıştırıcısı](https://go.microsoft.com/fwlink/?LinkId=246619)gibi bir yardımcı program kullanılarak okunabilir.<br/>App Service, `s-computername`, `s-ip`veya `cs-version` alanlarını desteklemez. |
| **Dağıtım günlükleri** | */LogFiles/git/* ve */Deployments/* | Ayrıca, iç dağıtım işlemlerine ve git dağıtımları günlüklerine göre oluşturulan günlükleri içerir. |

## <a name="send-logs-to-azure-monitor-preview"></a>Günlükleri Azure Izleyici 'ye gönderme (Önizleme)

Yeni [Azure izleyici tümleştirmesiyle](https://aka.ms/appsvcblog-azmon), günlükleri depolama hesaplarına, Event Hubs ve Log Analytics göndermek Için [Tanılama ayarları (Önizleme) oluşturabilirsiniz](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) . 

> [!div class="mx-imgBorder"]
> ![Tanılama ayarları (Önizleme)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>Desteklenen günlük türleri

Aşağıdaki tabloda desteklenen günlük türleri ve açıklamaları gösterilmektedir: 

| Günlük türü | Windows desteği | Linux desteği | Açıklama |
|-|-|-|
| AppServiceConsoleLogs | TBA dili | Yes | Standart çıkış ve standart hata |
| AppServiceHTTPLogs | Yes | Yes | Web sunucusu günlükleri |
| AppServiceEnvironmentPlatformLogs | Yes | Yes | App Service Ortamı: ölçekleme, yapılandırma değişiklikleri ve durum günlükleri|
| AppServiceAuditLogs | Yes | Yes | FTP ve kudu aracılığıyla oturum açma etkinliği |
| AppServiceFileAuditLogs | TBA dili | TBA dili | FTP ve kudu aracılığıyla dosya değişiklikleri |
| AppServiceAppLogs | TBA dili | Java & & Tomcat | Uygulama günlükleri |

## <a name="nextsteps"></a> Sonraki adımlar
* [Azure Izleyici ile günlük sorgulama](../azure-monitor/log-query/log-query-overview.md)
* [Azure App Service Izleme](web-sites-monitor.md)
* [Visual Studio 'da Azure App Service sorunlarını giderme](troubleshoot-dotnet-visual-studio.md)
* [HDInsight 'ta uygulama günlüklerini çözümleme](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
