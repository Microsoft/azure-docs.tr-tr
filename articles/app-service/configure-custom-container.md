---
title: Özel kapsayıcı yapılandırma
description: Azure App Service bir özel kapsayıcıyı yapılandırmayı öğrenin. Bu makalede en yaygın yapılandırma görevlerine yer verilmiştir.
ms.topic: article
ms.date: 02/23/2021
ms.custom: devx-track-azurepowershell
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 48d2eeec1bdb1b9b4a393b4116092f043716077c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832042"
---
# <a name="configure-a-custom-container-for-azure-app-service"></a>Azure App Service için özel kapsayıcıyı yapılandırma

Bu makalede, Azure App Service üzerinde çalıştırmak için özel bir kapsayıcının nasıl yapılandırılacağı gösterilir.

::: zone pivot="container-windows"

Bu kılavuzda, App Service Windows uygulamalarının kapsayıcılama için temel kavramlar ve yönergeler sağlanmaktadır. Azure App Service hiç kullanmadıysanız, önce [özel kapsayıcı hızlı başlangıç](quickstart-custom-container.md) ve [öğreticiyi](tutorial-custom-container.md) izleyin.

::: zone-end

::: zone pivot="container-linux"

Bu kılavuzda, App Service Linux uygulamalarının kapsayıcılama için temel kavramlar ve yönergeler sağlanmaktadır. Azure App Service hiç kullanmadıysanız, önce [özel kapsayıcı hızlı başlangıç](quickstart-custom-container.md) ve [öğreticiyi](tutorial-custom-container.md) izleyin. Ayrıca [çok kapsayıcılı bir uygulama hızlı](quickstart-multi-container.md) başlangıcı ve [öğretici](tutorial-multi-container-app.md)de mevcuttur.

::: zone-end

::: zone pivot="container-windows"

## <a name="supported-parent-images"></a>Desteklenen üst görüntüler

Özel Windows görüntünüz için, istediğiniz çerçevenin sağ [üst görüntüsünü (temel görüntü)](https://docs.docker.com/develop/develop-images/baseimages/) seçmeniz gerekir:

- .NET Framework uygulamaları dağıtmak için Windows Server Core [uzun süreli bakım kanalı (LTSC)](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) sürümüne göre bir üst görüntü kullanın. 
- .NET Core uygulamaları dağıtmak için Windows Server nano [yarı yıllık bakım kanalı (sac)](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) sürümüne göre bir üst görüntü kullanın. 

Uygulama başlatılırken üst görüntünün indirilmesi zaman alabilir. Ancak Azure App Service önbelleğinde bulunan aşağıdaki üst görüntülerden birini kullanarak başlangıç süresini kısaltabilirsiniz:

- [MCR.Microsoft.com/Windows/ServerCore](https://hub.docker.com/_/microsoft-windows-servercore): 2004
- [MCR.Microsoft.com/Windows/ServerCore](https://hub.docker.com/_/microsoft-windows-servercore): ltsc2019
- [MCR.Microsoft.com/DotNet/Framework/ASPNET](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/): 4,8-windowsservercore-2004
- [MCR.Microsoft.com/DotNet/Framework/ASPNET](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/): 4,8-windowsservercore-ltsc2019
- [MCR.Microsoft.com/DotNet/Core/Runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nanoserver-2004
- [MCR.Microsoft.com/DotNet/Core/Runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nanoserver-1909
- [MCR.Microsoft.com/DotNet/Core/Runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nanoserver-1903
- [MCR.Microsoft.com/DotNet/Core/Runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nanoserver-1809
- [MCR.Microsoft.com/DotNet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nanoserver-2004
- [MCR.Microsoft.com/DotNet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nanoserver-1909
- [MCR.Microsoft.com/DotNet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nanoserver-1903
- [MCR.Microsoft.com/DotNet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nanoserver-1809

::: zone-end

## <a name="change-the-docker-image-of-a-custom-container"></a>Özel kapsayıcının Docker görüntüsünü değiştirme

Mevcut bir özel kapsayıcı uygulamasını geçerli Docker görüntüsünden yeni bir görüntüye değiştirmek için aşağıdaki komutu kullanın:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>Özel bir kayıt defterinden görüntü kullanma

Azure Container Registry gibi özel bir kayıt defterinden görüntü kullanmak için aşağıdaki komutu çalıştırın:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

*\<username>* Ve için *\<password>* , özel kayıt hesabınız için oturum açma kimlik bilgilerini sağlayın.

## <a name="i-dont-see-the-updated-container"></a>Güncelleştirilmiş kapsayıcıyı görmüyorum

Docker kapsayıcı ayarlarınızı yeni bir kapsayıcıya işaret edecek şekilde değiştirirseniz, uygulamanın yeni kapsayıcıdan HTTP isteklerini kullanabilmesi birkaç dakika sürebilir. Yeni kapsayıcı çekilmekte ve başlatıldığında, App Service eski kapsayıcıdan istekleri sunmaya devam eder. Yalnızca yeni kapsayıcı başlatıldığında ve istekleri almaya hazırsanız, istek göndermeye başlamak App Service.

## <a name="how-container-images-are-stored"></a>Kapsayıcı görüntüleri nasıl depolanır

App Service bir özel Docker görüntüsü ilk kez çalıştırdığınızda, App Service `docker pull` ve tüm resim katmanlarını çeker. Bu katmanlar, şirket içi Docker kullanıyor gibi bir diskte depolanır. Uygulama her yeniden başlatıldığında App Service `docker pull` , ancak yalnızca değiştirilmiş katmanları çeker. Değişiklik yoksa, App Service yerel diskte varolan katmanları kullanır.

Uygulama, fiyatlandırma katmanlarının ölçeğini artırma ve azaltma gibi her nedenden dolayı işlem örneklerini değiştirirse App Service tüm katmanları yeniden almalıdır. Daha fazla örnek eklemek için ölçeği ölçeklendirirseniz aynı değer geçerlidir. Uygulama örneklerinin bir ölçeklendirme işlemi olmadan değiştirebildiği nadir durumlar da vardır.

## <a name="configure-port-number"></a>Bağlantı noktası numarasını Yapılandır

Varsayılan olarak App Service, özel kapsayıcının 80 numaralı bağlantı noktasında dinlediğini varsayar. Kapsayıcınız farklı bir bağlantı noktasına dinliyorsa, App Service uygulamanızdaki `WEBSITES_PORT` uygulama ayarını ayarlayın. [Cloud Shell](https://shell.azure.com)aracılığıyla ayarlayabilirsiniz. Bash 'de:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

PowerShell'de:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_PORT"="8000"}
```

App Service şu anda kapsayıcının HTTP istekleri için yalnızca bir bağlantı noktası kullanıma sunmasına izin veriyor. 

## <a name="configure-environment-variables"></a>Ortam değişkenlerini yapılandırma

Özel Kapsayıcınız, dışarıdan sağlanması gereken ortam değişkenlerini kullanabilir. Bunları [Cloud Shell](https://shell.azure.com)aracılığıyla geçirebilirsiniz. Bash 'de:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings DB_HOST="myownserver.mysql.database.azure.com"
```

PowerShell'de:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"DB_HOST"="myownserver.mysql.database.azure.com"}
```

Uygulamanız çalıştırıldığında, App Service uygulama ayarları işleme otomatik olarak ortam değişkenleri olarak eklenir. Kapsayıcı ortam değişkenlerini URL ile doğrulayabilirsiniz `https://<app-name>.scm.azurewebsites.net/Env)` .

Uygulamanız özel bir kayıt defterinden veya Docker Hub 'ından görüntü kullanıyorsa, depoya erişim için kimlik bilgileri ortam değişkenlerine kaydedilir: `DOCKER_REGISTRY_SERVER_URL` , `DOCKER_REGISTRY_SERVER_USERNAME` ve `DOCKER_REGISTRY_SERVER_PASSWORD` . Güvenlik riskleri nedeniyle, bu ayrılmış değişken adlarından hiçbiri uygulamaya gösterilmez.

::: zone pivot="container-windows"
IIS veya .NET Framework (4,0 veya üzeri) tabanlı kapsayıcılar için, `System.ConfigurationManager` App Service tarafından otomatik olarak .NET uygulama ayarları ve bağlantı dizeleri olarak eklenir. Diğer tüm diller veya Framework için, aşağıdaki ilgili öneklerden biriyle işlem için ortam değişkenleri olarak sunulur:

- `APPSETTING_`
- `SQLCONTR_`
- `MYSQLCONTR_`
- `SQLAZURECOSTR_`
- `POSTGRESQLCONTR_`
- `CUSTOMCONNSTR_`

::: zone-end

::: zone pivot="container-linux"

Bu yöntem, hem tek Kapsayıcılı uygulamalar için hem de ortam değişkenlerinin *Docker-Compose. yıml* dosyasında belirtildiği çok Kapsayıcılı uygulamalar için geçerlidir.

::: zone-end

## <a name="use-persistent-shared-storage"></a>Kalıcı paylaşılan depolama kullan

::: zone pivot="container-windows"

Dosyaları yeniden başlatmalar arasında kalıcı hale getirmek ve örnekleri arasında paylaşmak için uygulamanızın dosya sistemindeki *C:\home* dizinini kullanabilirsiniz. `C:\home`Uygulamanızda, kapsayıcı uygulamanızın kalıcı depolamaya erişmesini sağlamak için verilmiştir.

Kalıcı depolama devre dışı bırakıldığında, dizine yazma işlemleri `C:\home` kalıcı olmaz. [Docker ana bilgisayar günlükleri ve kapsayıcı günlükleri](#access-diagnostic-logs) , kapsayıcıya eklenmemiş, varsayılan kalıcı bir paylaşılan depolama alanına kaydedilir. Kalıcı depolama etkinleştirildiğinde, dizine yapılan tüm yazma işlemleri `C:\home` kalıcı hale getirilir ve genişleme uygulamasının tüm örnekleri tarafından erişilebilir ve günlük olarak erişilebiliyor `C:\home\LogFiles` .

::: zone-end

::: zone pivot="container-linux"

Dosyaları yeniden başlatmalar arasında kalıcı hale getirmek ve örnekleri arasında paylaşmak için uygulamanızın dosya sistemindeki */Home* dizinini kullanabilirsiniz. `/home`Uygulamanızda, kapsayıcı uygulamanızın kalıcı depolamaya erişmesini sağlamak için verilmiştir.

Kalıcı depolama devre dışı bırakıldığında, dizine yazma işlemleri, `/home` uygulama yeniden başlatmaları veya birden çok örnek arasında kalıcı olmaz. Tek özel durum `/home/LogFiles` , Docker ve kapsayıcı günlüklerini depolamak için kullanılan dizindir. Kalıcı depolama etkinleştirildiğinde, dizine yapılan tüm yazmaları `/home` kalıcı hale getirilir ve genişleme uygulamasının tüm örnekleri tarafından erişilebilir.

::: zone-end

Varsayılan olarak, kalıcı depolama devre dışıdır ve ayar uygulama ayarları 'nda gösterilmez. Etkinleştirmek için `WEBSITES_ENABLE_APP_SERVICE_STORAGE` [Cloud Shell](https://shell.azure.com)aracılığıyla uygulama ayarını ayarlayın. Bash 'de:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

PowerShell'de:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_ENABLE_APP_SERVICE_STORAGE"=true}
```

> [!NOTE]
> Ayrıca, [kendi kalıcı depolama alanınızı da yapılandırabilirsiniz](configure-connect-to-azure-storage.md).

## <a name="detect-https-session"></a>HTTPS oturumunu Algıla

App Service ön uçlarında TLS/SSL 'yi sonlandırır. Bu, TLS/SSL isteklerinin uygulamanıza hiçbir şekilde hiçbir şekilde hiçbir şekilde yararlanmadığını gösterir. Uygulamanıza gerek yoktur ve bu uygulamaya TLS/SSL desteği uygulamanız gerekmez. 

Ön uçlar Azure veri merkezlerinin içinde bulunur. Uygulamanızla TLS/SSL kullanıyorsanız, Internet üzerindeki trafiğiniz her zaman güvenli bir şekilde şifrelenir.

::: zone pivot="container-windows"

## <a name="customize-aspnet-machine-key-injection"></a>ASP.NET makine anahtarı ekleme işlemini özelleştirin

 Kapsayıcı başlangıcı sırasında otomatik olarak oluşturulan anahtarlar, ASP.NET şifreleme yordamları için makine anahtarları olarak kapsayıcıya eklenir. Şu ortam değişkenlerini arayarak [kapsayıcıda bu anahtarları bulabilirsiniz](#connect-to-the-container) : `MACHINEKEY_Decryption` , `MACHINEKEY_DecryptionKey` , `MACHINEKEY_ValidationKey` , `MACHINEKEY_Validation` . 

Her yeniden başlatmada yeni anahtarlar, uygulamanız kendisine bağımlıysa ASP.NET Forms kimlik doğrulaması ve görünüm durumunu sıfırlayamayabilir. Anahtarların otomatik olarak yeniden oluşturulmasını engellemek için, [App Service uygulama ayarları olarak el ile ayarlayın](#configure-environment-variables). 

## <a name="connect-to-the-container"></a>Kapsayıcıya bağlanma

Uygulamasına giderek tanılama görevleri için Windows kapsayıcıcınıza doğrudan bağlanabilirsiniz `https://<app-name>.scm.azurewebsites.net/DebugConsole` . Şöyle çalışır:

- Hata ayıklama konsolu PowerShell oturumlarını başlatma, kayıt defteri anahtarlarını İnceleme ve kapsayıcı dosya sisteminin tamamına gitme gibi etkileşimli komutları yürütmenize imkan tanır.
- BT, yukarıdaki grafik tarayıcıdan ayrı çalışır ve yalnızca [paylaşılan depolamadaki](#use-persistent-shared-storage)dosyaları gösterir.
- Ölçeği genişletilmiş bir uygulamada, hata ayıklama konsolu kapsayıcı örneklerinden birine bağlanır. Üst menüdeki **örnek** açılır listesinden farklı bir örnek seçebilirsiniz.
- Konsol içinden kapsayıcıda yaptığınız herhangi bir değişiklik, uygulamanız yeniden başlatıldığında (paylaşılan depolamada değişiklikler hariç), Docker görüntüsünün parçası *olmadığından devam etmez* . Kayıt defteri ayarları ve yazılım yükleme gibi değişikliklerinizi kalıcı hale getirmek için, onları Dockerfile ' ın bir parçası haline getirin.

## <a name="access-diagnostic-logs"></a>Tanılama günlüklerine erişim

App Service, Docker konağının yanı sıra kapsayıcının içindeki etkinlikleri günlüğe kaydeder. Docker ana bilgisayarındaki (Platform günlükleri) Günlükler varsayılan olarak gönderilir, ancak kapsayıcı içinden uygulama günlüklerinin veya Web sunucusu günlüklerinin el ile etkinleştirilmesi gerekir. Daha fazla bilgi için bkz. [Uygulama günlüğünü etkinleştirme](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) ve [Web sunucusu günlüğünü etkinleştirme](troubleshoot-diagnostic-logs.md#enable-web-server-logging). 

Docker günlüklerine erişmenin birkaç yolu vardır:

- [Azure portal](#in-azure-portal)
- [Kudu konsolundan](#from-the-kudu-console)
- [Kudu API 'SI ile](#with-the-kudu-api)
- [Günlükleri Azure izleyici 'ye gönderme](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview)

### <a name="in-azure-portal"></a>Azure portal

Docker günlükleri, uygulamanızdaki **kapsayıcı ayarları** sayfasında portalda görüntülenir. Günlükler kesilir, ancak **İndir**' i tıklayarak tüm günlükleri indirebilirsiniz. 

### <a name="from-the-kudu-console"></a>Kudu konsolundan

' A gidin `https://<app-name>.scm.azurewebsites.net/DebugConsole` ve günlük dosyalarını tek tek görmek Için **LogFiles** klasörüne tıklayın. Tüm **LogFiles** dizinini indirmek için Dizin adının solundaki **İndir** simgesine tıklayın. Ayrıca, bu klasöre bir FTP istemcisi kullanarak erişebilirsiniz.

Konsol terminalinde, `C:\home\LogFiles` kalıcı paylaşılan depolama etkin olmadığından klasöre varsayılan olarak erişemezsiniz. Konsol terminalinde bu davranışı etkinleştirmek için [kalıcı paylaşılan depolamayı etkinleştirin](#use-persistent-shared-storage).

Şu anda bir FTP istemcisi kullanarak kullanılmakta olan Docker günlüğünü indirmeye çalışırsanız, bir dosya kilidi nedeniyle bir hata alabilirsiniz.

### <a name="with-the-kudu-api"></a>Kudu API 'SI ile

`https://<app-name>.scm.azurewebsites.net/api/logs/docker`Docker günlüklerinin meta verilerini görmek için doğrudan öğesine gidin. Listelenmiş birden fazla günlük dosyası görebilirsiniz ve bu `href` özellik günlük dosyasını doğrudan indirmenizi sağlar. 

Tüm günlükleri bir ZIP dosyasında bir araya indirmek için erişim `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip` .

## <a name="customize-container-memory"></a>Kapsayıcı belleğini özelleştirme

Varsayılan olarak, Azure App Service dağıtılan tüm Windows kapsayıcıları 1 GB RAM ile sınırlıdır. `WEBSITE_MEMORY_LIMIT_MB` [Cloud Shell](https://shell.azure.com)aracılığıyla uygulama ayarı sağlayarak bu değeri değiştirebilirsiniz. Bash 'de:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_MEMORY_LIMIT_MB=2000
```

PowerShell'de:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_MEMORY_LIMIT_MB"=2000}
```

Değer MB olarak tanımlanır ve konağın toplam fiziksel belleğine eşit ve daha az olmalıdır. Örneğin, 8 GB RAM 'e sahip bir App Service planında, `WEBSITE_MEMORY_LIMIT_MB` tüm uygulamaların birikmeli toplamı 8 GB 'ı aşmamalıdır. Her fiyatlandırma katmanında kullanılabilir bellek miktarı hakkında bilgi, **Premium kapsayıcı (Windows) planı** bölümünde [App Service fiyatlandırmada](https://azure.microsoft.com/pricing/details/app-service/windows/)bulunabilir.

## <a name="customize-the-number-of-compute-cores"></a>İşlem çekirdekleri sayısını özelleştirme

Varsayılan olarak, bir Windows kapsayıcısı seçtiğiniz fiyatlandırma katmanınız için tüm kullanılabilir çekirdeklerle çalışır. Örneğin, hazırlama yuvalarınızın kullandığı çekirdek sayısını azaltmak isteyebilirsiniz. Bir kapsayıcı tarafından kullanılan çekirdek sayısını azaltmak için, `WEBSITE_CPU_CORES_LIMIT` uygulama ayarını tercih edilen çekirdek sayısı olarak ayarlayın. [Cloud Shell](https://shell.azure.com)aracılığıyla ayarlayabilirsiniz. Bash 'de:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --slot staging --settings WEBSITE_CPU_CORES_LIMIT=1
```

PowerShell'de:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_CPU_CORES_LIMIT"=1}
```

> [!NOTE]
> Uygulama ayarının güncelleştirilmesi otomatik yeniden başlatmayı tetikler ve en az kapalı kalma süresine neden olur. Bir üretim uygulaması için, bir hazırlama yuvasına değiştirmeyi düşünün, hazırlama yuvasındaki uygulama ayarını değiştirin ve ardından tekrar üretime dönüştürün.

Kudu konsoluna giderek () ayarlanmış numaranızı doğrulayın `https://<app-name>.scm.azurewebsites.net` ve PowerShell 'i kullanarak aşağıdaki komutları yazın. Her komut bir sayı verir.

```PowerShell
Get-ComputerInfo | ft CsNumberOfLogicalProcessors # Total number of enabled logical processors. Disabled processors are excluded.
Get-ComputerInfo | ft CsNumberOfProcessors # Number of physical processors.
```

İşlemciler birden çok veya hiper iş parçacığı işlemcisi olabilir. Her fiyatlandırma katmanında kaç çekirdeğin kullanılabilir olduğunu, **Premium kapsayıcı (Windows) planı** bölümünde [App Service fiyatlandırmada](https://azure.microsoft.com/pricing/details/app-service/windows/)bulabilirsiniz.

## <a name="customize-health-ping-behavior"></a>Sağlık ping davranışını özelleştirme

App Service kapsayıcı başlatıldığında ve bir HTTP ping işlemine yanıt verdiğinde kapsayıcının başarıyla başlatılacağını kabul eder. Durum ping isteği, üstbilgiyi içerir `User-Agent= "App Service Hyper-V Container Availability Check"` . Kapsayıcı başlatılır ancak belirli bir süre sonra ping komutuna yanıt vermezse App Service, kapsayıcının başlamadığını belirten Docker günlüğünde bir olay günlüğe kaydeder. 

Uygulamanız Kaynak yoğunluklu ise, kapsayıcı HTTP pingine zamanında yanıt vermemeyebilir. HTTP ping işlemleri başarısız olduğunda eylemleri denetlemek için `CONTAINER_AVAILABILITY_CHECK_MODE` uygulama ayarını ayarlayın. [Cloud Shell](https://shell.azure.com)aracılığıyla ayarlayabilirsiniz. Bash 'de:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings CONTAINER_AVAILABILITY_CHECK_MODE="ReportOnly"
```

PowerShell'de:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"CONTAINER_AVAILABILITY_CHECK_MODE"="ReportOnly"}
```

Aşağıdaki tabloda olası değerler gösterilmektedir:

| Değer | Tanımlarını |
| - | - |
| **Onarımı** | Birbirini izleyen üç kullanılabilirlik denetimi sonrasında kapsayıcıyı yeniden başlatın |
| **ReportOnly** | Varsayılan değer. Kapsayıcıyı yeniden başlatmayın ancak üç ardışık kullanılabilirlik denetimi sonrasında kapsayıcının Docker günlüklerinde rapor edin. |
| **Kapalı** | Kullanılabilirliği denetleme. |

## <a name="support-for-group-managed-service-accounts"></a>Grup tarafından yönetilen hizmet hesapları için destek

Grup yönetilen hizmet hesapları (gMSAs) Şu anda App Service Windows kapsayıcılarında desteklenmemektedir.

::: zone-end

::: zone pivot="container-linux"

## <a name="enable-ssh"></a>SSH 'yi etkinleştirme

SSH, kapsayıcı ile istemci arasında güvenli iletişime olanak tanır. Özel bir kapsayıcının SSH 'yi desteklemesi için onu Docker yansımanıza eklemeniz gerekir.

> [!TIP]
> App Service içindeki tüm yerleşik Linux kapsayıcıları, SSH yönergelerini görüntü depolarında ekledi. Nasıl etkinleştirildiğini görmek için [Node.js 10,14 deposu](https://github.com/Azure-App-Service/node/blob/master/10.14) ile aşağıdaki yönergeleri izleyebilirsiniz. Node.js yerleşik görüntüsündeki yapılandırma biraz farklıdır, ancak prensibi.

- Aşağıdaki örnekte olduğu gibi, deponuza [bir sshd_config dosyası](https://man.openbsd.org/sshd_config) ekleyin.

    ```
    Port            2222
    ListenAddress       0.0.0.0
    LoginGraceTime      180
    X11Forwarding       yes
    Ciphers aes128-cbc,3des-cbc,aes256-cbc,aes128-ctr,aes192-ctr,aes256-ctr
    MACs hmac-sha1,hmac-sha1-96
    StrictModes         yes
    SyslogFacility      DAEMON
    PasswordAuthentication  yes
    PermitEmptyPasswords    no
    PermitRootLogin     yes
    Subsystem sftp internal-sftp
    ```

    > [!NOTE]
    > Bu dosya, OpenSSH 'yi yapılandırır ve aşağıdaki öğeleri içermelidir:
    > - `Port` 2222 olarak ayarlanmalıdır.
    > - `Ciphers`, bu listedeki en az bir öğeyi içermelidir: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs`, bu listedeki en az bir öğeyi içermelidir: `hmac-sha1,hmac-sha1-96`.

- Dockerfile dosyanızda aşağıdaki komutları ekleyin:

    ```Dockerfile
    # Install OpenSSH and set the password for root to "Docker!". In this example, "apk add" is the install instruction for an Alpine Linux-based image.
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 

    # Copy the sshd_config file to the /etc/ssh/ directory
    COPY sshd_config /etc/ssh/

    # Open port 2222 for SSH access
    EXPOSE 80 2222
    ```

    Bu yapılandırma kapsayıcıya dış bağlantılara izin vermiyor. Kapsayıcının 2222 numaralı bağlantı noktasına yalnızca özel bir sanal ağın köprü ağı içinde erişilebilir ve Internet 'teki bir saldırgan tarafından erişilebilir değildir.

- Kapsayıcının başlangıç komut dosyasında SSH sunucusunu başlatın.

    ```bash
    /usr/sbin/sshd
    ```

## <a name="access-diagnostic-logs"></a>Tanılama günlüklerine erişim

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="configure-multi-container-apps"></a>Çok Kapsayıcılı uygulamaları yapılandırma

- [Docker Compose 'de kalıcı depolamayı kullanma](#use-persistent-storage-in-docker-compose)
- [Önizleme sınırlamaları](#preview-limitations)
- [Docker Compose seçenekleri](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Docker Compose 'de kalıcı depolamayı kullanma

WordPress gibi çok Kapsayıcılı uygulamalarda kalıcı depolamanın düzgün çalışması gerekir. Bunu etkinleştirmek için, Docker Compose yapılandırmanızın kapsayıcının *dışında* bir depolama konumunu göstermesi gerekir. Kapsayıcının içindeki depolama konumları değişiklikleri uygulamanın yeniden başlatmalarıyla kalıcı tutmaz.

`WEBSITES_ENABLE_APP_SERVICE_STORAGE` [Cloud Shell](https://shell.azure.com)' deki [az WebApp config appSettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) komutunu kullanarak uygulama ayarını ayarlayarak kalıcı depolamayı etkinleştirin.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

*Docker-Compose. yıml* dosyanızda, `volumes` seçeneğini ile eşleyin `${WEBAPP_STORAGE_HOME}` . 

`WEBAPP_STORAGE_HOME`, App Service içinde bulunan ve uygulamanız için kalıcı depolamayla eşlenmiş olan bir ortam değişkenidir. Örnek:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Önizleme sınırlamaları

Çok Kapsayıcılı Şu anda önizleme aşamasındadır. Aşağıdaki App Service platform özellikleri desteklenmez:

- Kimlik doğrulama/yetkilendirme
- Yönetilen kimlikler
- CORS

### <a name="docker-compose-options"></a>Docker Compose seçenekleri

Aşağıdaki listede desteklenen ve desteklenmeyen Docker Compose yapılandırma seçenekleri gösterilmektedir:

#### <a name="supported-options"></a>Desteklenen seçenekler

- command
- entrypoint
- ortam
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>Desteklenmeyen seçenekler

- build (izin verilmez)
- depends_on (yoksayılır)
- networks (yoksayılır)
- secrets (yoksayılır)
- 80 ve 8080 dışındaki bağlantı noktaları (yoksayıldı)

> [!NOTE]
> Açıkça çağrılmayan diğer seçenekler genel önizlemede yok sayılır.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: özel bir kapsayıcı kullanarak Azure App Service özel yazılım geçirme](tutorial-custom-container.md)

::: zone pivot="container-linux"

> [!div class="nextstepaction"]
> [Öğretici: çok Kapsayıcılı WordPress uygulaması](tutorial-multi-container-app.md)

::: zone-end

Ya da bkz. ek kaynaklar:

[Windows/Linux kapsayıcılarındaki sertifikayı yükle](configure-ssl-certificate-in-code.md#load-certificate-in-linuxwindows-containers)
