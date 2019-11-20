---
title: 'Hızlı başlangıç: Azure Service Fabric bir Java uygulaması oluşturma'
description: Bu hızlı başlangıçta, Service Fabric güvenilir hizmetler örnek uygulaması kullanarak Azure için bir Java uygulaması oluşturacaksınız.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/29/2019
ms.author: suhuruli
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 4ef0db5f72f5849942bb043261f1166cf7c046b1
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703288"
---
# <a name="quickstart--deploy-a-java-app-to-azure-service-fabric-on-linux"></a>Hızlı başlangıç: Linux üzerinde Azure Service Fabric Java uygulaması dağıtma

Bu hızlı başlangıçta, bir Linux geliştirici makinesinde tutulma IDE 'yi kullanarak Azure Service Fabric ilk Java uygulamanızı nasıl dağıtacağınız gösterilmektedir. İşiniz bittiğinde, oylama sonuçlarını kümedeki durum bilgisi içeren arka uç hizmetine kaydeden bir Java Web ön ucuna sahip bir oylama uygulamanız vardır.

Azure Service Fabric, mikro hizmetler ve kapsayıcılar dağıtmak ve yönetmek için dağıtılmış bir sistem platformudur.

![Azure Service Fabric oylama örneği](./media/service-fabric-quickstart-java/service-fabric-voting-sample.png)

Bu hızlı başlangıçta şunları nasıl yapacağınızı öğreneceksiniz:

* Service Fabric Java uygulamalarınız için bir araç olarak tutulma kullanın
* Uygulamayı yerel kümenize dağıtma
* Birden çok düğümde uygulamanın ölçeğini genişletme

## <a name="prerequisites"></a>Prerequisites

Bu hızlı başlangıcı gerçekleştirmek için:

1. [Service Fabric SDK & Service Fabric komut satırı arabirimini (CLı) yükler](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [Git 'i yükler](https://git-scm.com/)
3. [Çakışan Küreler yüklemeleri](https://www.eclipse.org/downloads/)
4. [Java ortamını ayarlama](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development), tutulma eklentisini yüklemek için isteğe bağlı adımları izlediğinizden emin olun

## <a name="download-the-sample"></a>Örneği indirin

Komut penceresinde, örnek uygulama deposunu yerel makinenize kopyalamak için aşağıdaki komutu çalıştırın.

```git
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>Uygulamayı yerel olarak çalıştırma

1. Aşağıdaki komutu çalıştırarak yerel kümenizi başlatın:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Yerel kümenin başlatılması biraz zaman alır. Kümenin tam olarak olduğunu doğrulamak için Service Fabric Explorer **http://localhost:19080** ' e erişin. Sağlıklı olan beş düğüm, yerel kümenin çalışır durumda olduğunu gösterir.

    ![Azure Service Fabric Explorer sağlıklı düğümleri gösterir](./media/service-fabric-quickstart-java/service-fabric-explorer-healthy-nodes.png)

2. Tutulma 'yi açın.
3. **Dosya** > **içeri aktarma** > **Gradle** > **var olan Gradle Project** ' i seçin ve Sihirbazı izleyin.
4. **Dizin** ' i seçin ve GitHub 'dan kopyaladığınız @no__t 2 klasöründen `Voting` dizinini seçin. **Son**' u seçin.

    ![Gradle projesini tutulma 'ya aktar](./media/service-fabric-quickstart-java/eclipse-import-gradle-project.png)

5. Artık `Voting` projesi, çakışan küreler için paket Gezgini 'nde vardır.
6. Projeye sağ tıklayın ve **Service Fabric** açılan menüsünde **uygulamayı Yayımla** ' yı seçin. **Publishprofiles/Local. JSON** öğesini hedef profil olarak seçin ve **Yayımla**' yı seçin.

    ![Azure Service Fabric yerel JSON yayımlama](./media/service-fabric-quickstart-java/service-fabric-publish-local-json.png)

7. En sevdiğiniz web tarayıcınızı açın ve `http://localhost:8080` ' a erişerek uygulamaya erişin.

    ![Azure Service Fabric yerel ana bilgisayarı](./media/service-fabric-quickstart-java/service-fabric-local-host.png)

Artık bir oy verme seçenekleri kümesi ekleyebilir ve oyları almaya başlayabilirsiniz. Uygulama, ayrı bir veritabanına gerek duymadan Service Fabric kümenizdeki tüm verileri çalıştırır ve depolar.

## <a name="scale-applications-and-services-in-a-cluster"></a>Bir kümedeki uygulamaları ve Hizmetleri ölçeklendirme

Hizmetler, hizmetlerde yükün bir değişikliğe uyum sağlamak için bir küme genelinde ölçeklendirilebilir. Kümede çalışan örneklerin sayısını değiştirerek bir hizmeti ölçeklendirmeniz gerekir. Hizmetlerinizi ölçeklendirmenin birçok yolu vardır; Örneğin, Service Fabric CLı (sfctl) içindeki betikleri veya komutları kullanabilirsiniz. Aşağıdaki adımlar Service Fabric Explorer kullanır.

Service Fabric Explorer, tüm Service Fabric kümelerinde çalışır ve kümenin HTTP yönetim bağlantı noktasına (19080) göz atarak bir tarayıcıdan erişilebilir. Örneğin, `http://localhost:19080`.

Web ön uç hizmetini ölçeklendirmek için aşağıdakileri yapın:

1. Service Fabric Explorer kümenizdeki açın (örneğin, `https://localhost:19080`).
2. TreeView 'daki **Fabric:/oylama/VotingWeb** düğümünün yanındaki üç nokta ( **...** ) simgesini seçin ve **hizmeti Ölçeklendir**' i seçin.

    ![Azure Service Fabric bir hizmeti ölçeklendirin](./media/service-fabric-quickstart-java/service-fabric-scale-service.png)

    Artık Web ön uç hizmetinin örnek sayısını ölçeklendirmeye seçebilirsiniz.

3. Sayıyı **2** olarak değiştirin ve **hizmeti Ölçeklendir**' i seçin.
4. Ağaç görünümünde **Fabric:/oylama/VotingWeb** düğümünü seçin ve bölüm DÜĞÜMÜNÜ (GUID ile gösterilir) genişletin.

    ![Azure Service Fabric ölçeklenmiş hizmet](./media/service-fabric-quickstart-java/service-fabric-explorer-service-scaled.png)

    Artık hizmetin iki örneği olduğunu görebilir ve ağaç görünümünde örneklerin üzerinde çalıştığı düğümleri görebilirsiniz.

Bu basit yönetim göreviyle, ön uç hizmetinin Kullanıcı yükünü işlemesi için kullanılabilir kaynakları iki katına çıkarmış olursunuz. Güvenilir bir şekilde çalışması için bir hizmetin birden fazla örneğinin gerekli olmadığı anlaşılması önemlidir. Bir hizmet başarısız olursa, Service Fabric yeni bir hizmet örneğinin kümede çalıştığından emin olur.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta şunları öğrendiniz:

* Service Fabric Java uygulamalarınız için bir araç olarak tutulma kullanın
* Java uygulamalarını yerel kümenize dağıtma
* Birden çok düğümde uygulamanın ölçeğini genişletme

Service Fabric 'de Java uygulamalarıyla çalışma hakkında daha fazla bilgi edinmek için Java uygulamalarına yönelik öğreticiye geçin.

> [!div class="nextstepaction"]
> [Java uygulaması dağıtma](./service-fabric-tutorial-create-java-app.md)
