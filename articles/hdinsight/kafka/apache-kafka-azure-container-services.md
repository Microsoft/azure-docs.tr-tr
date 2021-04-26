---
title: HDInsight üzerinde Kafka ile Azure Kubernetes hizmetini kullanma
description: Azure Kubernetes Service 'te (AKS) barındırılan kapsayıcı görüntülerinden HDInsight üzerinde Kafka kullanmayı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 2216eb5893b77761f4d31c5819d152ceeb985abc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869659"
---
# <a name="use-azure-kubernetes-service-with-apache-kafka-on-hdinsight"></a>HDInsight üzerinde Apache Kafka Azure Kubernetes hizmetini kullanma

HDInsight kümesinde [Apache Kafka](https://kafka.apache.org/) Azure Kubernetes Service (aks) kullanmayı öğrenin. Bu belgedeki adımlar, Kafka ile bağlantıyı doğrulamak için AKS 'te barındırılan Node.js bir uygulama kullanır. Bu uygulama, Kafka ile iletişim kurmak için [Kafka-node](https://www.npmjs.com/package/kafka-node) paketini kullanır. Tarayıcı istemcisi ile AKS 'de barındırılan arka uç arasındaki olay odaklı mesajlaşma için [Socket.io](https://socket.io/) kullanır.

[Apache Kafka](https://kafka.apache.org), gerçek zamanlı akış verisi işlem hatları ve uygulamaları oluşturmak için kullanılabilen, açık kaynak dağıtılmış akış platformudur. Azure Kubernetes hizmeti, barındırılan Kubernetes ortamınızı yönetir ve Kapsayıcılı uygulamaların dağıtılmasını hızlı ve kolay hale getirir. Azure sanal ağını kullanarak iki hizmeti bağlayabilirsiniz.

> [!NOTE]  
> Bu belgenin odağı, Azure Kubernetes hizmetinin HDInsight üzerinde Kafka ile iletişim kurmasını sağlamak için gerekli olan adımlara sahiptir. Örneğin kendisi, yapılandırmanın çalıştığını göstermek için yalnızca temel bir Kafka istemcidir.

## <a name="prerequisites"></a>Önkoşullar

* [Azure CLI](/cli/azure/install-azure-cli)
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

:::image type="content" source="./media/apache-kafka-azure-container-services/kafka-aks-architecture.png" alt-text="Tek bir sanal ağda HDInsight, diğer bir deyişle, eşleme kullanarak" border="false":::

> [!IMPORTANT]  
> Ad çözümlemesi, eşlenen ağlar arasında etkin değildir, bu nedenle IP adresleme kullanılır. Varsayılan olarak, HDInsight üzerinde Kafka, istemciler bağlandığında IP adresleri yerine ana bilgisayar adlarını döndürecek şekilde yapılandırılmıştır. Bu belgedeki adımlar, Kafka yerine IP tanıtımı kullanacak şekilde değiştirilir.

## <a name="create-an-azure-kubernetes-service-aks"></a>Azure Kubernetes hizmeti (AKS) oluşturma

Zaten bir AKS kümeniz yoksa, bir tane oluşturma hakkında bilgi edinmek için aşağıdaki belgelerden birini kullanın:

* [Azure Kubernetes hizmeti (AKS) kümesi dağıtma-Portal](../../aks/kubernetes-walkthrough-portal.md)
* [Azure Kubernetes hizmeti (AKS) kümesi dağıtma-CLı](../../aks/kubernetes-walkthrough.md)

> [!IMPORTANT]  
> AKS, yükleme sırasında **ek** bir kaynak grubunda bir sanal ağ oluşturur. Ek kaynak grubu **MC_resourceGroup_AKSclusterName_location** adlandırma kuralına uyar.  
> Bu ağ, sonraki bölümde HDInsight için oluşturulmuş bir ile eşlenmez.

## <a name="configure-virtual-network-peering"></a>Sanal Ağ eşlemesini yapılandırma

### <a name="identify-preliminary-information"></a>Ön bilgileri tanımla

1. [Azure Portal](https://portal.azure.com), aks kümeniz için sanal ağı Içeren ek **kaynak grubunu** bulun.

2. Kaynak grubundan __sanal ağ__ kaynağını seçin. Adı daha sonra kullanmak için not edin.

3. **Ayarlar**’dan __Adres alanı__’nı seçin. Listelenen adres alanını dikkate alın.

### <a name="create-virtual-network"></a>Sanal ağ oluşturma

1. HDInsight için bir sanal ağ oluşturmak için __+ kaynak__  >  __ağ__  >  __sanal ağı__ oluşturma bölümüne gidin.

1. Belirli özellikler için aşağıdaki yönergeleri kullanarak ağı oluşturun:

    |Özellik | Değer |
    |---|---|
    |Adres alanı|AKS küme ağı tarafından kullanılan ile örtüşmeyen bir adres alanı kullanmanız gerekir.|
    |Konum|AKS kümesi için kullandığınız sanal ağ için aynı __konumu__ kullanın.|

1. Sonraki adıma geçmeden önce sanal ağ oluşturuluncaya kadar bekleyin.

### <a name="configure-peering"></a>Eşlemeyi yapılandırma

1. HDInsight ağı ile AKS küme ağı arasındaki eşlemeyi yapılandırmak için sanal __ağı seçin ve__ ardından eşlemeler ' i seçin.

1. __+ Ekle__ ' yi seçin ve formu doldurmak için aşağıdaki değerleri kullanın:

    |Özellik |Değer |
    |---|---|
    |\<this VN>Uzak sanal ağa eşleme adı|Bu eşleme yapılandırması için benzersiz bir ad girin.|
    |Sanal ağ|**aks kümesi** için sanal ağı seçin.|
    |Eşleme adı \<AKS VN>\<this VN>|Benzersiz bir ad girin.|

    Tüm diğer alanları varsayılan değerde bırakın, sonra eşlemeyi yapılandırmak için __Tamam__ ' ı seçin.

## <a name="create-apache-kafka-cluster-on-hdinsight"></a>HDInsight üzerinde Apache Kafka kümesi oluşturma

HDInsight kümesinde Kafka oluştururken, daha önce HDInsight için oluşturulan sanal ağa katılmanız gerekir. Kafka kümesi oluşturma hakkında daha fazla bilgi için [Apache Kafka kümesi oluşturma](apache-kafka-get-started.md) belgesi bölümüne bakın.

## <a name="configure-apache-kafka-ip-advertising"></a>Apache Kafka IP tanıtımı yapılandırma

Kafka 'yi etki alanı adları yerine IP adreslerini tanıtmak üzere yapılandırmak için aşağıdaki adımları kullanın:

1. Web tarayıcısını kullanarak `https://CLUSTERNAME.azurehdinsight.net` adresine gidin. CLUSTERNAME öğesini HDInsight kümesindeki Kafka adıyla değiştirin.

    İstendiğinde, küme için HTTPS Kullanıcı adı ve parolasını kullanın. Küme için ambarı Web Kullanıcı arabirimi görüntülenir.

2. Kafka hakkındaki bilgileri görüntülemek için soldaki listeden __Kafka__ ' yi seçin.

    :::image type="content" source="./media/apache-kafka-azure-container-services/select-kafka-service.png" alt-text="Kafka vurgulanmış hizmet listesi" border="true":::

3. Kafka yapılandırmasını görüntülemek için üst ortadaki __config__ 'ler ' i seçin.

    :::image type="content" source="./media/apache-kafka-azure-container-services/select-kafka-config1.png" alt-text="Apache ambarı Hizmetleri Yapılandırması" border="true":::

4. __Kafka-env__ yapılandırmasını bulmak için `kafka-env` sağ üst köşedeki __filtre__ alanına girin.

    :::image type="content" source="./media/apache-kafka-azure-container-services/search-for-kafka-env.png" alt-text="Kafka-env için Kafka yapılandırması" border="true":::

5. IP adreslerini tanıtmak üzere Kafka yapılandırmak için, __Kafka-env-Template__ alanının altına aşağıdaki metni ekleyin:

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Kafka tarafından dinlediği arabirimi yapılandırmak için `listeners` sağ üst köşedeki __filtre__ alanına girin.

7. Kafka 'i tüm ağ arabirimlerini dinlemek üzere yapılandırmak için, __dinleyiciler__ alanındaki değeri olarak değiştirin `PLAINTEXT://0.0.0.0:9092` .

8. Yapılandırma değişikliklerini kaydetmek için __Kaydet__ düğmesini kullanın. Değişiklikleri açıklayan bir kısa mesaj girin. Değişiklikler kaydedildikten sonra __Tamam ' ı__ seçin.

    :::image type="content" source="./media/apache-kafka-azure-container-services/save-configuration-button.png" alt-text="Apache ambarı kaydetme yapılandırması" border="true":::

9. Kafka yeniden başlatırken hata oluşmasını engellemek için, __hizmet eylemleri__ düğmesini kullanın ve __bakım modunu aç__' ı seçin. Bu işlemi gerçekleştirmek için Tamam ' ı seçin.

    :::image type="content" source="./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png" alt-text="Bakım vurgulanmış şekilde hizmet eylemleri" border="true":::

10. Kafka 'i yeniden başlatmak için __Yeniden Başlat__ düğmesini kullanın ve __etkilenen tümünü yeniden Başlat__' ı seçin. Yeniden başlatmayı onaylayın ve sonra işlem tamamlandıktan sonra __Tamam__ düğmesini kullanın.

    :::image type="content" source="./media/apache-kafka-azure-container-services/restart-required-button.png" alt-text="Yeniden Başlat düğmesi tüm etkilenen vurgulanmış vurgulanmış" border="true":::

11. Bakım modunu devre dışı bırakmak için, __hizmet eylemleri__ düğmesini kullanın ve __bakım modunu__ Kapat ' ı seçin. Bu işlemi gerçekleştirmek için **Tamam ' ı** seçin.

## <a name="test-the-configuration"></a>Yapılandırmayı test edin

Bu noktada, Kafka ve Azure Kubernetes hizmeti, eşlenmiş sanal ağlarla iletişim kurmaktadır. Bu bağlantıyı test etmek için aşağıdaki adımları kullanın:

1. Test uygulaması tarafından kullanılan bir Kafka konu başlığı oluşturun. Kafka konu başlıkları oluşturma hakkında daha fazla bilgi için [Apache Kafka kümesi oluşturma](apache-kafka-get-started.md) belgesi bölümüne bakın.

2. Örnek uygulamayı konumundan indirin [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test) .

3. Dosyayı düzenleyin `index.js` ve aşağıdaki satırları değiştirin:

    * `var topic = 'mytopic'`: `mytopic` Bu uygulama tarafından kullanılan Kafka konusunun adıyla değiştirin.
    * `var brokerHost = '176.16.0.13:9092`: `176.16.0.13` Kümeniz için aracı konaklarından birinin Iç IP adresi ile değiştirin.

        Kümedeki aracı ana bilgisayarlarının (workernodes) iç IP adresini bulmak için, bkz. [Apache ambarı REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-internal-ip-address-of-cluster-nodes) belgesi. Etki alanı adının başladığı girdilerden birinin IP adresini seçin `wn` .

4. Dizindeki bir komut satırından `src` , dağıtım için bir görüntü oluşturmak üzere bağımlılıkları yükleyip Docker kullanın:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]  
    > Bu uygulama için gereken paketler depoya iade edilmiş olduğundan, `npm` bunları yüklemek için yardımcı programı kullanmanıza gerek yoktur.

5. Azure Container Registry (ACR) oturumunuzu açın ve loginServer adını bulun:

    ```azurecli
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]  
    > Azure Container Registry adınızı bilmiyorsanız veya Azure Kubernetes hizmeti ile çalışmak üzere Azure CLı 'yı kullanma konusunda bilgi sahibi değilseniz, [aks öğreticileri](../../aks/tutorial-kubernetes-prepare-app.md)' ne bakın.

6. Yerel `kafka-aks-test` görüntüyü ACR 'Nizin loginServer ile etiketleyin. `:v1`Görüntü sürümünü belirtmek için de sonuna ekleyin:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Görüntüyü kayıt defterine gönderin:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```

    Bu işlemin tamamlanabilmesi birkaç dakika sürer.

8. Kubernetes bildirim dosyasını () düzenleyin `kafka-aks-test.yaml` ve `microsoft` 4. adımda alınan ACR loginserver adıyla değiştirin.

9. Bildirimden uygulama ayarlarını dağıtmak için aşağıdaki komutu kullanın:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Uygulamanın izlemek için aşağıdaki komutu kullanın `EXTERNAL-IP` :

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    Dış IP adresi atandıktan sonra, sihirbazdan çıkmak için __CTRL + C__ tuşlarını kullanın

11. Bir Web tarayıcısı açın ve hizmetin dış IP adresini girin. Aşağıdaki görüntüye benzer bir sayfaya ulaşmanız gerekir:

    :::image type="content" source="./media/apache-kafka-azure-container-services/test-web-page-image1.png" alt-text="Web sayfası görüntüsünü Apache Kafka test etme" border="true":::

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
