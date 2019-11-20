---
title: Linux üzerinde App Service SSS-Azure | Microsoft Docs
description: Linux SSS üzerinde Azure App Service.
keywords: Azure App Service, Web uygulaması, SSS, Linux, Oss, kapsayıcılar için Web App, çok Kapsayıcılı, çok kapsayıcı
services: app-service
documentationCenter: ''
author: msangapu-msft
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/30/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: fa7b6a02ba287c7f51284a28ce41b2291317f99c
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066897"
---
# <a name="azure-app-service-on-linux-faq"></a>Linux 'ta Azure App Service SSS

Linux üzerinde App Service yayınlanmasıyla birlikte özellik ekleme ve platformumuza geliştirmeler yapma konusunda çalışıyoruz. Bu makalede, müşterilerimizin son zamanlarda sorduğu sorulara yanıtlar verilmektedir.

Sorunuz varsa, bu makaleye yorum yapın.

## <a name="built-in-images"></a>Yerleşik görüntüler

**Platformun sağladığı yerleşik Docker kapsayıcılarını çatala eklemek istiyorum. Bu dosyaları nerede bulabilirim?**

Tüm Docker dosyalarını [GitHub](https://github.com/azure-app-service)üzerinde bulabilirsiniz. [Docker Hub](https://hub.docker.com/u/appsvc/)'Daki tüm Docker kapsayıcılarını bulabilirsiniz.

<a id="#startup-file"></a>

**Çalışma zamanı yığınını yapılandırdığımda, başlangıç dosyası bölümü için beklenen değerler nelerdir?**

| Yığın           | Beklenen değer                                                                         |
|-----------------|----------------------------------------------------------------------------------------|
| Java &AMP;         | JAR uygulamanızı başlatma komutu (örneğin, `java -jar my-app.jar --server.port=80`) |
| Tomcat, Yavaya | gerekli yapılandırmaların gerçekleştirileceği bir betiğin konumu (örneğin, `/home/site/deployments/tools/startup_script.sh`)          |
| Node.js         | PM2 yapılandırma dosyası veya betik dosyanız                                |
| .Net Core       | derlenmiş DLL adı olarak`dotnet <myapp>.dll`                                 |
| Ruby            | uygulamanızı başlatmak istediğiniz Ruby betiği                     |

Bu komutlar veya betikler, yerleşik Docker kapsayıcısı başlatıldıktan sonra, ancak uygulama kodunuz başlatılmadan önce yürütülür.

## <a name="management"></a>Yönetim

**Azure portal yeniden Başlat düğmesine bastığımda ne olur?**

Bu eylem bir Docker yeniden başlatması ile aynıdır.

**Uygulama kapsayıcısı sanal makinesine (VM) bağlanmak için Secure Shell (SSH) kullanabilir miyim?**

Evet, bunu kaynak denetimi yönetimi (SCM) sitesi aracılığıyla yapabilirsiniz.

> [!NOTE]
> SSH, SFTP veya Visual Studio Code (Node.js apps canlı hata ayıklaması için) kullanarak doğrudan yerel geliştirme makinenizden de uygulama kapsayıcısına bağlanabilirsiniz. Daha fazla bilgi için bkz. [Linux üzerinde App Service’te uzaktan hata ayıklama ve SSH](https://azure.github.io/AppService/2018/05/07/New-SSH-Experience-and-Remote-Debugging-for-Linux-Web-Apps.html).
>

**Bir SDK veya Azure Resource Manager şablonuyla bir Linux App Service planı nasıl oluşturabilirim?**

App Service 'in **ayrılmış** alanını *doğru*olarak ayarlamanız gerekir.

## <a name="continuous-integration-and-deployment"></a>Sürekli tümleştirme ve dağıtım

**Docker Hub 'ında görüntüyü güncelleştirdikten sonra Web Uygulamam hala eski bir Docker kapsayıcı görüntüsü kullanıyor. Özel kapsayıcıların sürekli tümleştirmesini ve dağıtımını destekliyor musunuz?**

Evet, [kapsayıcılar için Web App sürekli dağıtımı](./app-service-linux-ci-cd.md)izleyerek Azure Container Registry veya DockerHub için sürekli tümleştirme/dağıtım ayarlamak için. Özel kayıt defterleri için, Web uygulamanızı durdurup başlatarak kapsayıcıyı yenileyebilirsiniz. İsterseniz, Kapsayıcınızın yenilenmesini zorlamak için bir kukla uygulama ayarı değiştirebilir veya ekleyebilirsiniz.

**Hazırlama ortamlarını destekliyor musunuz?**

Evet.

**Web uygulamamı dağıtmak için *WebDeploy/MSDeploy* kullanabilir miyim?**

Evet, `WEBSITE_WEBDEPLOY_USE_SCM` *yanlış*olarak çağrılan bir uygulama ayarı ayarlamanız gerekir.

**Linux Web uygulaması kullanılırken uygulamamın git dağıtımı başarısız oluyor. Sorunu geçici olarak nasıl çözebilirim?**

Git dağıtımı Linux Web uygulamanıza başarısız olursa, uygulama kodunuzu dağıtmak için aşağıdaki seçeneklerden birini belirleyin:

- Sürekli teslim (Önizleme) özelliğini kullanın: Azure sürekli teslimi kullanabilmeniz için, uygulamanızın kaynak kodunu bir Azure DevOps git deposunda veya GitHub deposunda saklayabilirsiniz. Daha fazla bilgi için bkz. [Linux Web uygulaması Için sürekli teslimi yapılandırma](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/).

- [ZIP DAĞıTıM API](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)'sini kullanın: Bu API 'yi kullanarak [Web uygulamanıza SSH ekleyin](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support) ve kodunuzu dağıtmak istediğiniz klasöre gidin. Aşağıdaki kodu çalıştırın:

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   `curl` Komutun bulunamadığını belirten bir hata alırsanız, önceki `curl` komutu çalıştırmadan önce kullanarak `apt-get install curl` kıvrımlı yüklediğinizden emin olun.

## <a name="language-support"></a>Dil desteği

**Node. js uygulamamda Web yuvalarını, tüm özel ayarları veya ayarlanacak konfigürasyonları kullanmak istiyorum?**

Evet, sunucu `perMessageDeflate` tarafı Node. js kodunuzda devre dışı bırakın. Örneğin, socket.io kullanıyorsanız aşağıdaki kodu kullanın:

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**Derlenmemiş .NET Core uygulamalarını destekliyor musunuz?**

Evet.

**Oluşturucu, PHP uygulamaları için bir bağımlılık Yöneticisi olarak destekliyoruz mi?**

Evet, bir git dağıtımı sırasında kudu, bir PHP uygulaması dağıttığınızı algılamamalıdır (bir Oluşturucu. Lock dosyası için teşekkürler) ve kudu daha sonra bir besteci yüklemesi tetikleyecektir.

## <a name="custom-containers"></a>Özel kapsayıcılar

**Kendi özel kapsayıcınızı kullanıyorum. Platformun bir SMB `/home/` dizinini dizine bağlamak istiyorum.**

`/home/` Ayar belirtilmemişse veya true olarak ayarlanırsa, Dizin ölçek örnekleri arasında paylaşılır ve yazılan dosyalar yeniden başlatmalar arasında kalır. `WEBSITES_ENABLE_APP_SERVICE_STORAGE` Açıkça `WEBSITES_ENABLE_APP_SERVICE_STORAGE` *false* olarak ayarlandığında bağlama devre dışı bırakılır.

**Özel kapsayıcımın başlaması uzun sürer ve platformun başlaması bitmeden önce kapsayıcıyı yeniden başlatır.**

Platformun kapsayıcınızı yeniden başlatmadan önce bekleyeceği süreyi yapılandırabilirsiniz. Bunu yapmak için, `WEBSITES_CONTAINER_START_TIME_LIMIT` uygulama ayarını istediğiniz değere ayarlayın. Varsayılan değer 230 saniyedir ve en yüksek değer 1800 saniyedir.

**Özel kayıt defteri sunucu URL 'sinin biçimi nedir?**

`http://` Veya`https://`dahil olmak üzere tam kayıt defteri URL 'sini sağlayın.

**Özel kayıt defteri seçeneğinde görüntü adının biçimi nedir?**

Özel kayıt defteri URL 'SI de dahil olmak üzere tam görüntü adını ekleyin (örneğin, myacr.azurecr.io/dotnet:latest). Özel bir bağlantı noktası kullanan görüntü adları [Portal üzerinden girilemez](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Ayarlamak `docker-custom-image-name` [için `az` komut satırı aracını](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set)kullanın.

**Özel kapsayıcı Görüntümdeki birden fazla bağlantı noktasını kullanıma alabilir miyim?**

Birden fazla bağlantı noktasının sunulmasını desteklemiyoruz.

**Kendi depolama alanım 'ı getirebilir miyim?**

Evet, [kendi depolama alanınızı getir](https://docs.microsoft.com/azure/app-service/containers/how-to-serve-content-from-azure-storage) önizleme aşamasındadır.

**Özel kapsayıcımın dosya sistemine ya da SCM sitesinden çalışan işlemlerine neden gözatamıyorum?**

SCM sitesi ayrı bir kapsayıcıda çalışır. Dosya sistemini veya uygulama kapsayıcısının çalışan süreçlerini kontrol edebilirsiniz.

**Özel kapsayıcım bağlantı noktası 80 dışında bir bağlantı noktasını dinler. Uygulamamı istekleri bu bağlantı noktasına yönlendirmek için nasıl yapılandırabilirim?**

Otomatik bağlantı noktası algılıyoruz. Ayrıca, *WEBSITES_PORT* adlı bir uygulama ayarı da belirtebilir ve bu değere beklenen bağlantı noktası numarasının değerini verebilirsiniz. Daha önce, Platform, *bağlantı noktası* uygulama ayarını kullandı. Bu uygulama ayarını kullanımdan kaldırmayı ve özel olarak *WEBSITES_PORT* kullanmayı planlıyoruz.

**Özel kapsayıcımda HTTPS uygulamam gerekiyor mu?**

Hayır, platform paylaşılan ön uçlarında HTTPS sonlandırmasını işler.

## <a name="multi-container-with-docker-compose"></a>Docker Compose ile çok Kapsayıcılı

**Nasıl yaparım? Azure Container Registry (ACR), çok Kapsayıcılı ile kullanılacak şekilde yapılandırma**

ACR 'yi çok Kapsayıcılı kullanmak için, **tüm kapsayıcı görüntülerinin** aynı ACR kayıt defteri sunucusunda barındırılması gerekir. Aynı kayıt defteri sunucusunda olduktan sonra, uygulama ayarları oluşturmanız ve ardından Docker Compose yapılandırma dosyasını ACR görüntü adını içerecek şekilde güncelleştirmeniz gerekecektir.

Aşağıdaki uygulama ayarlarını oluşturun:

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL (tam URL, Ex: `https://<server-name>.azurecr.io`)
- DOCKER_REGISTRY_SERVER_PASSWORD (ACR ayarlarında yönetici erişimini etkinleştir)

Yapılandırma dosyası içinde, ACR yansımanıza aşağıdaki örnekte olduğu gibi başvurun:

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**Nasıl yaparım? internet 'e hangi kapsayıcının erişilebilir olduğunu biliyor musunuz?**

- Erişim için yalnızca bir kapsayıcı açılabilir
- Yalnızca bağlantı noktası 80 ve 8080 erişilebilir (açığa çıkarılan bağlantı noktaları)

Öncelik sırasına göre hangi kapsayıcının erişilebilir olduğunu belirlemek için kurallar aşağıda verilmiştir:

- Kapsayıcı adına `WEBSITES_WEB_CONTAINER_NAME` ayarlanan uygulama ayarı
- 80 veya 8080 numaralı bağlantı noktasını tanımlamak için ilk kapsayıcı
- Yukarıdakilerin hiçbiri true ise, dosyada tanımlanan ilk kapsayıcı erişilebilir olur (gösterilir)

## <a name="pricing-and-sla"></a>Fiyatlandırma ve SLA

**Artık hizmet genel kullanıma sunulduğunda fiyatlandırma nedir?**

Uygulamanızın çalıştırıldığı saat sayısı için normal Azure App Service fiyatlandırmaya ücretlendirilirsiniz.

## <a name="other-questions"></a>Diğer sorular

**Uygulama ayarları adlarında desteklenen karakterler nelerdir?**

Uygulama ayarları için yalnızca harfler (A-Z, a-z), rakamlar (0-9) ve alt çizgi karakterini (_) kullanabilirsiniz.

**Yeni özellikleri nereden isteyebilirim?**

[Web Apps geri bildirim forumundan](https://aka.ms/webapps-uservoice)fikir gönderebilirsiniz. Fikriniz başlığına "[Linux]" ekleyin.

## <a name="next-steps"></a>Sonraki adımlar

- [Linux üzerinde Azure App Service nedir?](app-service-linux-intro.md)
- [Azure App Service ortamlarında hazırlık ayarlama](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Kapsayıcılar için Web App ile sürekli dağıtım](./app-service-linux-ci-cd.md)
