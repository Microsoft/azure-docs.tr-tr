---
title: HDInsight üzerinde Kafka ile Azure Kubernetes hizmetini kullanma
description: Azure Kubernetes Service 'te (AKS) barındırılan kapsayıcı görüntülerinden HDInsight üzerinde Kafka kullanmayı öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 31eefbad8e8d7cb626d87d53690388d09b85257e
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122653"
---
# <a name="use-azure-kubernetes-service-with-apache-kafka-on-hdinsight"></a>HDInsight üzerinde Apache Kafka Azure Kubernetes hizmetini kullanma

HDInsight kümesinde [Apache Kafka](https://kafka.apache.org/) Azure Kubernetes Service (aks) kullanmayı öğrenin. Bu belgedeki adımlarda, Kafka ile bağlantıyı doğrulamak için AKS 'te barındırılan bir Node. js uygulaması kullanılır. Bu uygulama, Kafka ile iletişim kurmak için [Kafka-node](https://www.npmjs.com/package/kafka-node) paketini kullanır. Tarayıcı istemcisi ile AKS 'de barındırılan arka uç arasındaki olay odaklı mesajlaşma için [Socket.io](https://socket.io/) kullanır.

[Apache Kafka](https://kafka.apache.org), gerçek zamanlı akış verisi işlem hatları ve uygulamaları oluşturmak için kullanılabilen, açık kaynak dağıtılmış akış platformudur. Azure Kubernetes hizmeti, barındırılan Kubernetes ortamınızı yönetir ve Kapsayıcılı uygulamaların dağıtılmasını hızlı ve kolay hale getirir. Azure sanal ağını kullanarak iki hizmeti bağlayabilirsiniz.

> [!NOTE]  
> Bu belgenin odağı, Azure Kubernetes hizmetinin HDInsight üzerinde Kafka ile iletişim kurmasını sağlamak için gerekli olan adımlara sahiptir. Örneğin kendisi, yapılandırmanın çalıştığını göstermek için yalnızca temel bir Kafka istemcidir.

## <a name="prerequisites"></a>Önkoşullar

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* Bir Azure aboneliği

Bu belgede, aşağıdaki Azure hizmetlerini oluşturma ve kullanma hakkında bilgi sahibi olduğunuz varsayılmaktadır:

* HDInsight üzerinde Kafka
* Azure Kubernetes Service
* Azure Sanal Ağları

Bu belge Ayrıca, [Azure Kubernetes hizmet öğreticisi](../../aks/tutorial-kubernetes-prepare-app.md)aracılığıyla bir cede olduğunu varsaymaktadır. Bu makale bir kapsayıcı hizmeti oluşturur, bir Kubernetes kümesi, bir kapsayıcı kayıt defteri oluşturur ve `kubectl` yardımcı programı yapılandırır.

## <a name="architecture"></a>Mimari

### <a name="network-topology"></a>Ağ topolojisi

Hem HDInsight hem de AKS, işlem kaynakları için kapsayıcı olarak bir Azure sanal ağı kullanır. HDInsight ve AKS arasında iletişimi etkinleştirmek için ağları arasında iletişimi etkinleştirmeniz gerekir. Bu belgedeki adımlar, ağlarla sanal ağ eşlemesi kullanır. VPN gibi diğer bağlantıların de çalışması gerekir. Eşleme hakkında daha fazla bilgi için bkz. [sanal ağ eşleme](../../virtual-network/virtual-network-peering-overview.md) belgesi.

Aşağıdaki diyagramda bu belgede kullanılan ağ topolojisi gösterilmektedir:

![Tek bir sanal ağda HDInsight, diğer bir deyişle, eşleme kullanarak](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]  
> Ad çözümlemesi, eşlenen ağlar arasında etkin değildir, bu nedenle IP adresleme kullanılır. Varsayılan olarak, HDInsight üzerinde Kafka, istemciler bağlandığında IP adresleri yerine ana bilgisayar adlarını döndürecek şekilde yapılandırılmıştır. Bu belgedeki adımlar, Kafka yerine IP tanıtımı kullanacak şekilde değiştirilir.

## <a name="create-an-azure-kubernetes-service-aks"></a>Azure Kubernetes hizmeti (AKS) oluşturma

Zaten bir AKS kümeniz yoksa, bir tane oluşturma hakkında bilgi edinmek için aşağıdaki belgelerden birini kullanın:

* [Azure Kubernetes hizmeti (AKS) kümesi dağıtma-Portal](../../aks/kubernetes-walkthrough-portal.md)
* [Azure Kubernetes hizmeti (AKS) kümesi dağıtma-CLı](../../aks/kubernetes-walkthrough.md)

> [!NOTE]  
> AKS yükleme sırasında bir sanal ağ oluşturur. Bu ağ, sonraki bölümde HDInsight için oluşturulmuş bir ile eşlenmez.

## <a name="configure-virtual-network-peering"></a>Sanal Ağ eşlemesini yapılandırma

1. [Azure Portal](https://portal.azure.com), __kaynak grupları__' nı seçin ve ardından aks kümeniz için sanal ağı içeren kaynak grubunu bulun. Kaynak grubu adı `MC_<resourcegroup>_<akscluster>_<location>`. `resourcegroup` Ve`akscluster` girişleri, içinde kümeyi oluşturduğunuz kaynak grubunun adı ve kümenin adıdır. , `location` Kümenin oluşturulduğu konumdur.

2. Kaynak grubunda, __sanal ağ__ kaynağını seçin.

3. __Adres alanını__seçin. Listelenen adres alanını dikkate alın.

4. HDInsight için bir sanal ağ oluşturmak istiyorsanız __+ kaynak__, __ağ__ve ardından __sanal ağ__oluştur ' u seçin.

    > [!IMPORTANT]  
    > Yeni sanal ağın değerlerini girerken, AKS küme ağı tarafından kullanılan ile örtüşmeyen bir adres alanı kullanmanız gerekir.

    AKS kümesi için kullandığınız sanal ağ için aynı __konumu__ kullanın.

    Sonraki adıma geçmeden önce sanal ağ oluşturuluncaya kadar bekleyin.

5. HDInsight ağı ile AKS küme ağı arasındaki eşlemeyi yapılandırmak için sanal __ağı seçin ve__ardından eşlemeler ' i seçin. __+ Ekle__ ' yi seçin ve formu doldurmak için aşağıdaki değerleri kullanın:

   * __Ad__: Bu eşleme yapılandırması için benzersiz bir ad girin.
   * __Sanal ağ__: **Aks kümesi**için sanal ağı seçmek üzere bu alanı kullanın.

     Tüm diğer alanları varsayılan değerde bırakın, sonra eşlemeyi yapılandırmak için __Tamam__ ' ı seçin.

6. AKS küme ağı ve HDInsight ağı arasındaki eşlemeyi yapılandırmak için __aks kümesi sanal ağını__ __seçin ve__ardından eşlemeler ' i seçin. __+ Ekle__ ' yi seçin ve formu doldurmak için aşağıdaki değerleri kullanın:

   * __Ad__: Bu eşleme yapılandırması için benzersiz bir ad girin.
   * __Sanal ağ__: __HDInsight kümesi__için sanal ağı seçmek üzere bu alanı kullanın.

     Tüm diğer alanları varsayılan değerde bırakın, sonra eşlemeyi yapılandırmak için __Tamam__ ' ı seçin.

## <a name="install-apache-kafka-on-hdinsight"></a>HDInsight üzerinde Apache Kafka yüklemesi

HDInsight kümesinde Kafka oluştururken, daha önce HDInsight için oluşturulan sanal ağa katılmanız gerekir. Kafka kümesi oluşturma hakkında daha fazla bilgi için [Apache Kafka kümesi oluşturma](apache-kafka-get-started.md) belgesi bölümüne bakın.

> [!IMPORTANT]  
> Kümeyi oluştururken, HDInsight için oluşturduğunuz sanal ağa katmak için __Gelişmiş ayarları__ kullanmanız gerekir.

## <a name="configure-apache-kafka-ip-advertising"></a>Apache Kafka IP tanıtımı yapılandırma

Kafka 'yi etki alanı adları yerine IP adreslerini tanıtmak üzere yapılandırmak için aşağıdaki adımları kullanın:

1. Bir Web tarayıcısı kullanarak adresine gidin https://CLUSTERNAME.azurehdinsight.net. __Clustername__ öğesini HDInsight kümesindeki Kafka adıyla değiştirin.

    İstendiğinde, küme için HTTPS Kullanıcı adı ve parolasını kullanın. Küme için ambarı Web Kullanıcı arabirimi görüntülenir.

2. Kafka hakkındaki bilgileri görüntülemek için soldaki listeden __Kafka__ ' yi seçin.

    ![Kafka vurgulanmış hizmet listesi](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. Kafka yapılandırmasını görüntülemek için üst ortadaki __config__ 'ler ' i seçin.

    ![Apache ambarı Hizmetleri Yapılandırması](./media/apache-kafka-azure-container-services/select-kafka-config1.png)

4. __Kafka-env__ yapılandırmasını bulmak için sağ üst köşedeki `kafka-env` __filtre__ alanına girin.

    ![Kafka-env için Kafka yapılandırması](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

5. IP adreslerini tanıtmak üzere Kafka yapılandırmak için, __Kafka-env-Template__ alanının altına aşağıdaki metni ekleyin:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Kafka tarafından dinlediği arabirimi yapılandırmak için sağ üst köşedeki `listeners` __filtre__ alanına girin.

7. Kafka 'i tüm ağ arabirimlerini dinlemek üzere yapılandırmak için, __dinleyiciler__ alanındaki değeri olarak `PLAINTEXT://0.0.0.0:9092`değiştirin.

8. Yapılandırma değişikliklerini kaydetmek için __Kaydet__ düğmesini kullanın. Değişiklikleri açıklayan bir kısa mesaj girin. Değişiklikler kaydedildikten sonra __Tamam ' ı__ seçin.

    ![Apache ambarı kaydetme yapılandırması](./media/apache-kafka-azure-container-services/save-configuration-button.png)

9. Kafka yeniden başlatırken hata oluşmasını engellemek için, __hizmet eylemleri__ düğmesini kullanın ve __bakım modunu aç__' ı seçin. Bu işlemi gerçekleştirmek için Tamam ' ı seçin.

    ![Bakım vurgulanmış şekilde hizmet eylemleri](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. Kafka 'i yeniden başlatmak için __Yeniden Başlat__ düğmesini kullanın ve __etkilenen tümünü yeniden Başlat__' ı seçin. Yeniden başlatmayı onaylayın ve sonra işlem tamamlandıktan sonra __Tamam__ düğmesini kullanın.

    ![Yeniden Başlat düğmesi tüm etkilenen vurgulanmış vurgulanmış](./media/apache-kafka-azure-container-services/restart-required-button.png)

11. Bakım modunu devre dışı bırakmak için, __hizmet eylemleri__ düğmesini kullanın ve __bakım modunu__Kapat ' ı seçin. Bu işlemi gerçekleştirmek için **Tamam ' ı** seçin.

## <a name="test-the-configuration"></a>Yapılandırmayı test etme

Bu noktada, Kafka ve Azure Kubernetes hizmeti, eşlenmiş sanal ağlarla iletişim kurmaktadır. Bu bağlantıyı test etmek için aşağıdaki adımları kullanın:

1. Test uygulaması tarafından kullanılan bir Kafka konu başlığı oluşturun. Kafka konu başlıkları oluşturma hakkında daha fazla bilgi için [Apache Kafka kümesi oluşturma](apache-kafka-get-started.md) belgesi bölümüne bakın.

2. Örnek uygulamayı konumundan [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test)indirin.

3. `index.js` Dosyayı düzenleyin ve aşağıdaki satırları değiştirin:

    * `var topic = 'mytopic'`: Bu `mytopic` uygulama tarafından kullanılan Kafka konusunun adıyla değiştirin.
    * `var brokerHost = '176.16.0.13:9092`: Kümeniz `176.16.0.13` için aracı konaklarından birinin iç IP adresi ile değiştirin.

        Kümedeki aracı ana bilgisayarlarının (workernodes) iç IP adresini bulmak için, bkz. [Apache ambarı REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-internal-ip-address-of-cluster-nodes) belgesi. Etki alanı adının başladığı `wn`girdilerden birinin IP adresini seçin.

4. `src` Dizindeki bir komut satırından, dağıtım için bir görüntü oluşturmak üzere bağımlılıkları yükleyip Docker kullanın:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]  
    > Bu uygulama için gereken paketler depoya iade edilmiş olduğundan, bunları yüklemek için `npm` yardımcı programı kullanmanıza gerek yoktur.

5. Azure Container Registry (ACR) oturumunuzu açın ve loginServer adını bulun:

    ```bash
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]  
    > Azure Container Registry adınızı bilmiyorsanız veya Azure Kubernetes hizmeti ile çalışmak üzere Azure CLı 'yı kullanma konusunda bilgi sahibi değilseniz, [aks öğreticileri](../../aks/tutorial-kubernetes-prepare-app.md)' ne bakın.

6. Yerel `kafka-aks-test` görüntüyü ACR 'nizin loginserver ile etiketleyin. Görüntü sürümünü `:v1` belirtmek için de sonuna ekleyin:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Görüntüyü kayıt defterine gönderin:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```

    Bu işlemin tamamlanabilmesi birkaç dakika sürer.

8. Kubernetes bildirim dosyasını (`kafka-aks-test.yaml`) düzenleyin ve 4. adımda alınan ACR loginserver adıyla değiştirin. `microsoft`

9. Bildirimden uygulama ayarlarını dağıtmak için aşağıdaki komutu kullanın:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Uygulamanın izlemek `EXTERNAL-IP` için aşağıdaki komutu kullanın:

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    Dış IP adresi atandıktan sonra, sihirbazdan çıkmak için __CTRL + C__ tuşlarını kullanın

11. Bir Web tarayıcısı açın ve hizmetin dış IP adresini girin. Aşağıdaki görüntüye benzer bir sayfaya ulaşmanız gerekir:

    ![Web sayfası görüntüsünü Apache Kafka test etme](./media/apache-kafka-azure-container-services/test-web-page-image1.png)

12. Alana metin girin ve ardından __Gönder__ düğmesini seçin. Veriler Kafka adresine gönderilir. Sonra uygulamadaki Kafka tüketicisi iletiyi okur ve __Kafka bölümündeki iletilere__ ekler.

    > [!WARNING]  
    > Bir iletinin birden çok kopyasını alabilirsiniz. Bu sorun genellikle tarayıcınızı yapılandırdıktan sonra yenilediğinizde veya uygulamaya birden çok tarayıcı bağlantısı açtığınızda oluşur.

## <a name="next-steps"></a>Sonraki adımlar

HDInsight’ta Apache Kafka kullanma hakkında bilgi almak için aşağıdaki bağlantıları kullanın:

* [HDInsight üzerinde Apache Kafka kullanmaya başlama](apache-kafka-get-started.md)

* [HDInsight üzerinde Apache Kafka çoğaltmasını oluşturmak için MirrorMaker 'ı kullanma](apache-kafka-mirroring.md)

* [HDInsight üzerinde Apache Kafka ile Apache Storm kullanma](../hdinsight-apache-storm-with-kafka.md)

* [HDInsight üzerinde Apache Kafka ile Apache Spark kullanma](../hdinsight-apache-spark-with-kafka.md)

* [Azure sanal ağı üzerinden Apache Kafka bağlanma](apache-kafka-connect-vpn-gateway.md)
