---
title: Özel kapsayıcıyı Yapılandırma-Azure App Service | Microsoft Docs
description: Node. js uygulamalarını Azure App Service içinde çalışacak şekilde yapılandırmayı öğrenin
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 7290e2b09c316a97bfb88744307e185aef72852a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73668980"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Azure App Service için özel bir Linux kapsayıcısı yapılandırma

Bu makalede, Azure App Service üzerinde çalışacak özel bir Linux kapsayıcısının nasıl yapılandırılacağı gösterilir.

Bu kılavuzda, App Service Linux uygulamalarının kapsayıcılama için temel kavramlar ve yönergeler sağlanmaktadır. Azure App Service hiç kullanmadıysanız, önce [özel kapsayıcı hızlı başlangıç](quickstart-docker-go.md) ve [öğreticiyi](tutorial-custom-docker-image.md) izleyin. Ayrıca [çok kapsayıcılı bir uygulama hızlı](quickstart-multi-container.md) başlangıcı ve [öğretici](tutorial-multi-container-app.md)de mevcuttur.

## <a name="configure-port-number"></a>Bağlantı noktası numarasını Yapılandır

Özel görüntinizdeki Web sunucusu 80 dışında bir bağlantı noktası kullanabilir. Azure 'a özel kapsayıcının kullandığı bağlantı noktasını `WEBSITES_PORT` uygulama ayarını kullanarak söylemiş olursunuz. [Bu öğreticideki Python örneği](https://github.com/Azure-Samples/docker-django-webapp-linux) için GitHub sayfası, `WEBSITES_PORT` olarak _8000_ ayarlamanız gerektiğini gösterir. Bunu, Cloud Shell [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) komutunu çalıştırarak ayarlayabilirsiniz. Örneğin:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Ortam değişkenlerini yapılandırma

Özel Kapsayıcınız, dışarıdan sağlanması gereken ortam değişkenlerini kullanabilir. Bu dosyaları, Cloud Shell [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) komutunu çalıştırarak geçirebilirsiniz. Örneğin:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Bu yöntem, hem tek Kapsayıcılı uygulamalar için hem de ortam değişkenlerinin *Docker-Compose. yıml* dosyasında belirtildiği çok Kapsayıcılı uygulamalar için geçerlidir.

## <a name="use-persistent-shared-storage"></a>Kalıcı paylaşılan depolama kullan

Dosyaları yeniden başlatmalar arasında kalıcı hale getirmek ve örnekleri arasında paylaşmak için uygulamanızın dosya sistemindeki */Home* dizinini kullanabilirsiniz. Uygulamanızdaki `/home`, kapsayıcı uygulamanızın kalıcı depolamaya erişmesini sağlamak için sağlanır.

Kalıcı depolama devre dışı bırakıldığında, `/home` dizinine yazma işlemi, uygulama yeniden başlatmaları veya birden çok örnek arasında kalıcı olmaz. Tek istisna, Docker ve kapsayıcı günlüklerini depolamak için kullanılan `/home/LogFiles` dizinidir. Kalıcı depolama etkinleştirildiğinde, `/home` dizinine yapılan tüm yazmaları kalıcı hale getirilir ve genişleme uygulamasının tüm örnekleri tarafından erişilebilir.

Varsayılan olarak, kalıcı depolama *etkindir* ve ayar uygulama ayarları 'nda gösterilmez. Devre dışı bırakmak için, Cloud Shell [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) komutunu çalıştırarak `WEBSITES_ENABLE_APP_SERVICE_STORAGE` uygulama ayarını belirleyin. Örneğin:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> Ayrıca, [kendi kalıcı depolama alanınızı da yapılandırabilirsiniz](how-to-serve-content-from-azure-storage.md).

## <a name="enable-ssh"></a>SSH 'yi etkinleştirme

SSH, kapsayıcı ile istemci arasında güvenli iletişime olanak tanır. Özel bir kapsayıcının SSH 'yi desteklemesi için onu Dockerfile öğesine eklemeniz gerekir.

> [!TIP]
> Tüm yerleşik Linux kapsayıcıları, görüntü depolarında SSH yönergelerini ekledi. Nasıl etkinleştirildiğini görmek için [Node. js 10,14 deposu](https://github.com/Azure-App-Service/node/blob/master/10.14) ile aşağıdaki yönergeleri izleyebilirsiniz.

- SSH sunucusunu yüklemek ve kök hesabın parolasını `"Docker!"`olarak ayarlamak için [Çalıştır](https://docs.docker.com/engine/reference/builder/#run) yönergesini kullanın. Örneğin, [alp Linux](https://hub.docker.com/_/alpine)tabanlı bir görüntü için aşağıdaki komutlara ihtiyacınız vardır:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Bu yapılandırma kapsayıcıya dış bağlantılara izin vermiyor. SSH yalnızca `https://<app-name>.scm.azurewebsites.net` aracılığıyla kullanılabilir ve yayımlama kimlik bilgileriyle doğrulanır.

- [Bu sshd_config dosyasını](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) görüntü deponuza ekleyin ve [Copy](https://docs.docker.com/engine/reference/builder/#copy) yönergesini kullanarak dosyayı */etc/ssh/* dizinine kopyalayın. *Sshd_config* dosyaları hakkında daha fazla bilgi için bkz. [OpenBSD belgeleri](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > *Sshd_config* dosyası şu öğeleri içermelidir:
    > - `Ciphers`, bu listedeki en az bir öğeyi içermelidir: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs`, bu listedeki en az bir öğeyi içermelidir: `hmac-sha1,hmac-sha1-96`.

- Kapsayıcıda 2222 numaralı bağlantı noktasını açmak için [sergileme](https://docs.docker.com/engine/reference/builder/#expose) yönergesini kullanın. Kök parola bilinse de, 2222 numaralı bağlantı noktasına internet 'ten erişilemez. Yalnızca özel bir sanal ağın köprü ağı içindeki kapsayıcılar tarafından erişilebilir.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- Kapsayıcının başlangıç komut dosyasında SSH sunucusunu başlatın.

    ```bash
    /usr/sbin/sshd
    ```

    Örneğin, varsayılan [Node. js 10,14 KAPSAYıCıSıNıN](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) SSH sunucusunu nasıl başlatadığına bakın.

## <a name="access-diagnostic-logs"></a>Tanılama günlüklerine erişim

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Çok Kapsayıcılı uygulamaları yapılandırma

- [Docker Compose 'de kalıcı depolamayı kullanma](#use-persistent-storage-in-docker-compose)
- [Önizleme sınırlamaları](#preview-limitations)
- [Docker Compose seçenekleri](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Docker Compose 'de kalıcı depolamayı kullanma

WordPress gibi çok Kapsayıcılı uygulamalarda kalıcı depolamanın düzgün çalışması gerekir. Bunu etkinleştirmek için, Docker Compose yapılandırmanızın kapsayıcının *dışında* bir depolama konumunu göstermesi gerekir. Kapsayıcının içindeki depolama konumları değişiklikleri uygulamanın yeniden başlatmalarıyla kalıcı tutmaz.

Cloud Shell ' de [az WebApp config appSettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) komutunu kullanarak `WEBSITES_ENABLE_APP_SERVICE_STORAGE` uygulama ayarını ayarlayarak kalıcı depolamayı etkinleştirin.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

*Docker-Compose. yıml* dosyanızda `volumes` seçeneğini `${WEBAPP_STORAGE_HOME}`eşleyin. 

`WEBAPP_STORAGE_HOME`, App Service içinde bulunan ve uygulamanız için kalıcı depolamayla eşlenmiş olan bir ortam değişkenidir. Örneğin:

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

### <a name="docker-compose-options"></a>Docker Compose seçenekleri

Aşağıdaki listede desteklenen ve desteklenmeyen Docker Compose yapılandırma seçenekleri gösterilmektedir:

#### <a name="supported-options"></a>Desteklenen seçenekler

- command
- entrypoint
- environment
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

## <a name="configure-vnet-integration"></a>VNet tümleştirmesini yapılandırma

VNet tümleştirmeyle özel bir kapsayıcı kullanmak için ek kapsayıcı yapılandırması gerekebilir. Bkz. [uygulamanızı Azure sanal ağıyla tümleştirme](../web-sites-integrate-with-vnet.md).

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: özel kapsayıcı deposundan dağıtma](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Öğretici: çok Kapsayıcılı WordPress uygulaması](tutorial-multi-container-app.md)
