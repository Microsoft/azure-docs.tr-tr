---
title: 'Hızlı başlangıç: Azure Service Fabric bir Java uygulaması oluşturma'
description: Bu hızlı başlangıçta, bir Service Fabric güvenilir hizmetler örnek uygulaması kullanarak Azure için Java uygulaması oluşturursunuz.
ms.date: 01/29/2019
ms.topic: quickstart
ms.custom:
- mvc
- devcenter
- seo-java-august2019
- seo-java-september2019
- devx-track-java
- mode-api
ms.openlocfilehash: 199fe459ec8fd58d05b4946dda4291c46a4881c3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530903"
---
# <a name="quickstart--deploy-a-java-app-to-azure-service-fabric-on-linux"></a>Hızlı başlangıç: Linux üzerinde Azure Service Fabric Java uygulaması dağıtma

Bu hızlı başlangıçta, bir Linux geliştirici makinesinde tutulma IDE 'yi kullanarak Azure Service Fabric bir Java uygulaması dağıtırsınız. Bitirdiğinizde, oylama sonuçlarını kümedeki durum bilgisi içeren arka uç hizmetine kaydeden bir Java web ön ucuna sahip oylama uygulaması sağlanır.

Azure Service Fabric; mikro hizmetleri ve kapsayıcıları dağıtmayı ve yönetmeyi sağlayan bir dağıtılmış sistemler platformudur.

## <a name="prerequisites"></a>Önkoşullar

- [Java ortamı](./service-fabric-get-started-linux.md#set-up-java-development) ve [yeumman](./service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables)
- Service Fabric için [tutulma Neon (4.6) +](https://www.eclipse.org/downloads/packages/) ve [Tutulma eklentisi](./service-fabric-get-started-linux.md#install-the-eclipse-plug-in-optional)
- [Service Fabric SDK ve komut satırı arabirimi (CLı)](./service-fabric-get-started-linux.md#installation-methods)
- [Git](https://git-scm.com/downloads)

## <a name="download-the-sample"></a>Örneği indirme

Komut penceresinde, örnek uygulama deposunu yerel makinenize kopyalamak için aşağıdaki komutu çalıştırın.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>Uygulamayı yerel olarak çalıştırma

1. Aşağıdaki komutu çalıştırarak yerel kümenizi başlatın:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Yerel kümenin başlatılması biraz zaman alabilir. Kümenin sorunsuz çalıştığından emin olmak için, `http://localhost:19080` adresinden Service Fabric Explorer’a erişin. Sorunsuz beş düğüm, yerel kümenin çalıştığını belirtir.

    ![Azure Service Fabric Explorer sağlıklı düğümleri gösterir](./media/service-fabric-quickstart-java/service-fabric-explorer-healthy-nodes.png)

2. Eclipse’i açın.
3. **Dosya**  >  **içeri aktarma**  >  **Gradle**  >  **mevcut Gradle projesini** seçin ve Sihirbazı izleyin.
4. **Dizin** ' i seçin ve GitHub ' dan kopyaladığınız **Service-Fabric-Java-QuickStart** klasöründen **Oylama** dizinini seçin. **Son**'u seçin.

    ![Gradle projesini tutulma 'ya aktar](./media/service-fabric-quickstart-java/eclipse-import-gradle-project.png)

5. Artık, Package Explorer for Eclipse’de `Voting` projesine sahipsiniz.
6. Projeye sağ tıklayın ve **Service Fabric** açılan menüsünde **uygulamayı Yayımla** ' yı seçin. Hedef profil olarak **Publishprofiles/Local.js** seçin ve **Yayımla**' yı seçin.

    ![Azure Service Fabric yerel JSON yayımlama](./media/service-fabric-quickstart-java/service-fabric-publish-local-json.png)

7. Sık kullandığınız web tarayıcısını açın ve`http://localhost:8080` adresine giderek uygulamaya erişin.

    ![Azure Service Fabric yerel ana bilgisayarı](./media/service-fabric-quickstart-java/service-fabric-local-host.png)

Şimdi bir dizi oylama seçeneği ekleyebilir ve oyları almaya başlayabilirsiniz. Uygulama çalıştırılır ve ayrı bir veritabanına gerek kalmadan tüm verileri Service Fabric kümenizde depolar.

![Azure Service Fabric oylama örneği](./media/service-fabric-quickstart-java/service-fabric-voting-sample.png)

## <a name="scale-applications-and-services-in-a-cluster"></a>Bir kümedeki uygulamaları ve hizmetleri ölçeklendirme

Hizmet yükündeki bir değişikliği karşılamak için kümedeki hizmetler kolayca ölçeklendirilebilir. Kümede çalıştırılan örnek sayısını değiştirerek bir hizmeti ölçeklendirebilirsiniz. Hizmetlerinizi ölçeklendirmenin birçok yolu vardır. Örneğin, Service Fabric CLı () ' den betikleri veya komutları kullanabilirsiniz `sfctl` . Aşağıdaki adımlarda Service Fabric Explorer kullanılmaktadır.

Service Fabric Explorer, tüm Service Fabric kümelerinde çalışır ve kümenin HTTP yönetim bağlantı noktasına (19080) göz atarak bir tarayıcıdan erişilebilir. Örneğin, `http://localhost:19080`.

Web ön uç hizmetini ölçeklendirmek için aşağıdakileri yapın:

1. Service Fabric Explorer kümenizdeki açın. Örneğin, `https://localhost:19080`.
2. TreeView 'daki **Fabric:/oylama/VotingWeb** düğümünün yanındaki üç nokta (**...**) simgesini seçin ve **hizmeti Ölçeklendir**' i seçin.

    ![Azure Service Fabric bir hizmeti ölçeklendirin](./media/service-fabric-quickstart-java/service-fabric-scale-service.png)

    Şimdi web ön uç hizmetindeki örnek sayısını ölçeklendirebilirsiniz.

3. Sayıyı **2** olarak değiştirin ve **hizmeti Ölçeklendir**' i seçin.
4. Ağaç görünümünde **Fabric:/oylama/VotingWeb** düğümünü seçin ve bölüm DÜĞÜMÜNÜ (GUID ile gösterilir) genişletin.

    ![Azure Service Fabric ölçeklenmiş hizmet](./media/service-fabric-quickstart-java/service-fabric-explorer-service-scaled.png)

    Hizmette üç örnek olduğunu ve ağaç görünümünde örneklerin çalıştığı düğümleri görebilirsiniz.

Bu basit yönetim görevi sayesinde ön uç hizmetinin kullanıcı yükünü işlemek için kullanabileceği kaynakları iki katına çıkarmış oldunuz. Bir hizmetin güvenilir bir şekilde çalışması için birden fazla örneğe ihtiyaç duymadığınızı anlamanız önemlidir. Bir hizmet başarısız olursa Service Fabric, kümede yeni bir hizmet örneği çalışmasını sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta şunları öğrendiniz:

* Service Fabric Java uygulamalarınız için Eclipse’ı kullanma
* Java uygulamalarını yerel kümenize dağıtma
* Birden çok düğüm arasında uygulamanın ölçeğini genişletme

Service Fabric’te Java uygulamalarıyla çalışma hakkında daha fazla bilgi için Java uygulamaları öğreticisiyle devam edin.

> [!div class="nextstepaction"]
> [Java uygulaması dağıtma](./service-fabric-tutorial-create-java-app.md)
