---
title: Hızlı başlangıç-Azure yay bulut yapılandırması sunucusunu ayarlama
description: Uygulama dağıtımı için Azure Spring Cloud config Server 'ın kurulumunu açıklar.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 643d1cd6df3791b2e5ea2118425eecb29dbcdea2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104879371"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>Hızlı başlangıç: Azure yay bulut yapılandırması sunucusunu ayarlama

Azure Spring Cloud config Server, dağıtılmış sistemler için merkezi bir yapılandırma hizmetidir. Şu anda yerel depolama, git ve alt sürümü destekleyen bir takılabilir depo katmanı kullanır. Bu hızlı başlangıçta, bir git deposundan veri almak için yapılandırma sunucusunu ayarlarsınız.

::: zone pivot="programming-language-csharp"

## <a name="prerequisites"></a>Önkoşullar

* Bu serideki önceki hızlı başlangıcı doldurun: [Azure Spring Cloud Service sağla](spring-cloud-quickstart-provision-service-instance.md).

## <a name="azure-spring-cloud-config-server-procedures"></a>Azure yay bulut yapılandırması sunucu yordamları

Aşağıdaki komutu çalıştırarak yapılandırma-sunucunuzu projenin git deposunun konumuyla ayarlayın. `<service instance name>`Daha önce oluşturduğunuz hizmetin adıyla değiştirin. Önceki hızlı başlangıçta ayarladığınız hizmet örneği adı için varsayılan değer bu komutla birlikte çalışmaz.

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples --search-paths steeltoe-sample/config
```

Bu komut yapılandırma sunucusuna, örnek uygulama deposunun [steeltoe-Sample/config](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample/config) klasöründe yapılandırma verilerini bulmasını söyler. Yapılandırma verilerini alacak uygulamanın adı olduğundan `planet-weather-provider` , kullanılacak dosya [Planet-Weather-Provider. yıml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/steeltoe-sample/config/planet-weather-provider.yml)olacaktır.

::: zone-end

::: zone pivot="programming-language-java"
Azure Spring Cloud config Server, dağıtılmış sistemler için merkezi bir yapılandırma hizmetidir. Şu anda yerel depolama, git ve alt sürümü destekleyen bir takılabilir depo katmanı kullanır.  Mikro hizmet uygulamalarını Azure Spring Cloud 'a dağıtmak için yapılandırma sunucusunu ayarlayın.

## <a name="prerequisites"></a>Önkoşullar

* [JDK 8 ' i yükler](/java/azure/jdk/)
* [Azure aboneliğine kaydolma](https://azure.microsoft.com/free/)
* Seçim [Azure CLI sürüm 2.0.67 veya üstünü yükleyip](/cli/azure/install-azure-cli) Azure Spring Cloud uzantısını şu komutla birlikte yüklersiniz: `az extension add --name spring-cloud`
* Seçim [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) ve [oturum açmayı](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in) yükleyip

## <a name="azure-spring-cloud-config-server-procedures"></a>Azure yay bulut yapılandırması sunucu yordamları

#### <a name="portal"></a>[Portal](#tab/Azure-portal)

Aşağıdaki yordam, [Pılem ölçümleri örneğini](spring-cloud-quickstart-sample-app-introduction.md)dağıtmak için Azure Portal kullanarak yapılandırma sunucusunu ayarlar.

1. Hizmete **genel bakış** sayfasına gidin ve **yapılandırma sunucusu**' nu seçin.

2. **Varsayılan depo** bölümünde, **URI** 'yi "" olarak ayarlayın https://github.com/Azure-Samples/piggymetrics-config .

3. **Doğrula**' ya tıklayın.

    ![Yapılandırma sunucusuna git](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

4. Doğrulama tamamlandığında, değişikliklerinizi kaydetmek için **Uygula** ' ya tıklayın.

    ![Yapılandırma sunucusu doğrulanıyor](media/spring-cloud-quickstart-launch-app-portal/validate-complete.png)

5. Yapılandırmanın güncelleştirilmesi birkaç dakika sürebilir.
 
    ![Yapılandırma sunucusu güncelleştiriliyor](media/spring-cloud-quickstart-launch-app-portal/updating-config.png) 

6. Yapılandırma tamamlandığında bir bildirim almanız gerekir.

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Aşağıdaki yordam, [Pılem ölçümleri örneğini](spring-cloud-quickstart-sample-app-introduction.md)dağıtmak üzere yapılandırma sunucusunu ayarlamak IÇIN Azure CLI 'yi kullanır.

Yapılandırma-sunucunuzu proje için git deposunun konumuyla ayarlayın:

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```
---
::: zone-end

> [!TIP]
> Yapılandırma sunucusu için özel bir depo kullanıyorsanız, lütfen [kimlik doğrulamasını ayarlama hakkında öğreticimize](./spring-cloud-howto-config-server.md)başvurun.

## <a name="troubleshooting-of-azure-spring-cloud-config-server"></a>Azure Spring Cloud config Server sorunlarını giderme

Aşağıdaki yordamda, yapılandırma sunucusu ayarlarının nasıl giderileceği açıklanmaktadır.

1. Azure portal, hizmete **genel bakış** sayfasına gidin ve **Günlükler**' i seçin. 
1. **Sorgular** ' ı seçin ve **"hata" veya "özel durum" koşullarını Içeren uygulama günlüklerini görüntüleyin**. 
1. **Çalıştır**'a tıklayın. 
1. Günlüklerde **Java. lang. illegalStateException** hatası bulursanız, bu, Spring Cloud Service 'in yapılandırma sunucusundan özellikleri bulamadığı anlamına gelir.

    [![ASC portalı çalıştırma sorgusu ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-query.png)](media/spring-cloud-quickstart-setup-config-server/setup-config-server-query.png)

1. Hizmete **genel bakış** sayfasına gidin.
1. **Sorunları tanılama ve çözme**’yi seçin. 
1. **Yapılandırma sunucusu** algılayıcısı ' nı seçin.

    [![ASC portalı sorunları ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-diagnose.png) Tanılama](media/spring-cloud-quickstart-setup-config-server/setup-config-server-diagnose.png)

3. **Yapılandırma sunucusu sistem durumu denetimi**' ne tıklayın.

    [![ASC portalı Genie ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-genie.png)](media/spring-cloud-quickstart-setup-config-server/setup-config-server-genie.png)

4. Algılayıcısının daha fazla ayrıntı görmek için **yapılandırma sunucusu durumu** ' na tıklayın.

    [![ASC portalı sistem durumu ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-health-status.png)](media/spring-cloud-quickstart-setup-config-server/setup-config-server-health-status.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, abonelikleriniz varsa ücretlendirmeye devam edecek Azure kaynakları oluşturdunuz. Sonraki hızlı başlangıca devam etmeyi düşünmüyorsanız bkz. [Kaynakları Temizleme](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources). Aksi takdirde, sonraki hızlı başlangıca ilerleyin:

> [!div class="nextstepaction"]
> [Uygulama oluşturma ve dağıtma](spring-cloud-quickstart-deploy-apps.md)