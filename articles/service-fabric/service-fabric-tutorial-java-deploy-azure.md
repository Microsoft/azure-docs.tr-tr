---
title: Azure 'da bir Service Fabric kümesine Java uygulaması dağıtma
description: Bu öğreticide, Java Service Fabric uygulamasının bir Azure Service Fabric kümesine nasıl dağıtılacağını öğreneceksiniz.
ms.topic: tutorial
ms.date: 02/26/2018
ms.custom: mvc, devx-track-java, devx-track-azurecli
ms.openlocfilehash: 48cb7df49fb918ebd30883edc573cc63b6e35e4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789405"
---
# <a name="tutorial-deploy-a-java-application-to-a-service-fabric-cluster-in-azure"></a>Öğretici: Azure’da bir Service Fabric kümesine Java uygulamasını dağıtma

Bir serinin üçüncü kısmı olan bu öğreticide bir Service Fabric uygulamasının Azure’da bir kümeye nasıl dağıtılacağı gösterilir.

Serinin üçüncü bölümünde şunları öğrenirsiniz:

> [!div class="checklist"]
> * Azure’da güvenli bir Linux kümesi oluşturma
> * Kümeye bir uygulama dağıtma

Bu öğretici dizisinde şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * [Java Service Fabric Güvenilir Hizmetler uygulaması derleme](service-fabric-tutorial-create-java-app.md)
> * [Yerel kümede uygulamayı dağıtma ve uygulamanın hatasını ayıklama](service-fabric-tutorial-debug-log-local-cluster.md)
> * Azure kümesine uygulama dağıtma
> * [Uygulama için izleme ve tanılamayı ayarlama](service-fabric-tutorial-java-elk.md)
> * [CI/CD ayarlama](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce:

* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun
* [Azure CLI'yi yükleme](/cli/azure/install-azure-cli)
* [Mac](service-fabric-get-started-mac.md) veya [Linux](service-fabric-get-started-linux.md) için Service Fabric SDK’yı yükleyin
* [Python 3’ü yükleme](https://wiki.python.org/moin/BeginnersGuide/Download)

## <a name="create-a-service-fabric-cluster-in-azure"></a>Azure’da Service Fabric kümesi oluşturma

Aşağıdaki adımlar, bir Service Fabric kümesinde uygulamanızı dağıtmak için gerekli kaynakları oluşturur. Ayrıca ELK (Elasticsearch, Logstash, Kibana) yığını kullanılarak çözümünüzün durumunu izlemek için gerekli kaynaklar ayarlanır. Özellikle [Event Hubs](https://azure.microsoft.com/services/event-hubs/), Service Fabric’teki günlükler için havuz olarak kullanılır. Service Fabric kümesinden Logstash örneğinize günlükleri gönderecek şekilde yapılandırılmıştır.

1. Bir terminali açın ve Azure’da kaynakları oluşturmak için şablonları ve gerekli yardımcı betikleri içeren aşağıdaki paketi indirin

    ```bash
    git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
    ```

2. Azure hesabınızda oturum açma

    ```azurecli
    az login
    ```

3. Kaynakları oluşturmak için kullanmak istediğiniz Azure aboneliğinizi ayarlama

    ```azurecli
    az account set --subscription [SUBSCRIPTION-ID]
    ```

4. *service-fabric-java-quickstart/AzureCluster* klasöründen, Anahtar Kasası’nda bir küme sertifikası oluşturmak için aşağıdaki komutu çalıştırın. Bu sertifika, Service Fabric kümenizin güvenliğini sağlamak için kullanılır. Bölgeyi (Service Fabric kümenizle aynı olmalıdır), anahtar kasası kaynak grubu adını, anahtar kasası adını, sertifika parolasını ve küme DNS adını sağlayın.

    ```bash
    ./new-service-fabric-cluster-certificate.sh [REGION] [KEY-VAULT-RESOURCE-GROUP] [KEY-VAULT-NAME] [CERTIFICATE-PASSWORD] [CLUSTER-DNS-NAME-FOR-CERTIFICATE]

    Example: ./new-service-fabric-cluster-certificate.sh 'westus' 'testkeyvaultrg' 'testkeyvault' '<password>' 'testservicefabric.westus.cloudapp.azure.com'
    ```

    Önceki komut, daha sonra kullanmak üzere not edilmesi gereken aşağıdaki bilgileri döndürür.

    ```output
    Source Vault Resource Id: /subscriptions/<subscription_id>/resourceGroups/testkeyvaultrg/providers/Microsoft.KeyVault/vaults/<name>
    Certificate URL: https://<name>.vault.azure.net/secrets/<cluster-dns-name-for-certificate>/<guid>
    Certificate Thumbprint: <THUMBPRINT>
    ```

5. Günlüklerinizi depolayan depolama hesabı için bir kaynak grubu oluşturma

    ```azurecli
    az group create --location [REGION] --name [RESOURCE-GROUP-NAME]

    Example: az group create --location westus --name teststorageaccountrg
    ```

6. Üretilecek günlükleri depolamak için kullanılacak bir depolama hesabı oluşturma

    ```azurecli
    az storage account create -g [RESOURCE-GROUP-NAME] -l [REGION] --name [STORAGE-ACCOUNT-NAME] --kind Storage

    Example: az storage account create -g teststorageaccountrg -l westus --name teststorageaccount --kind Storage
    ```

7. [Azure Portal](https://portal.azure.com)’a erişin ve Depolama hesabınızın **Paylaşılan Erişim İmzası** sekmesine gidin. Aşağıdaki adımları izleyerek SAS belirtecini oluşturun.

    ![Depolama için SAS oluşturma](./media/service-fabric-tutorial-java-deploy-azure/storagesas.png)

8. Hesap SAS URL’sini kopyalayın ve Service Fabric kümenizi oluştururken kullanmak üzere bir kenara koyun. Aşağıdaki URL’ye benzer:

    ```output
    ?sv=2017-04-17&ss=bfqt&srt=sco&sp=rwdlacup&se=2018-01-31T03:24:04Z&st=2018-01-30T19:24:04Z&spr=https,http&sig=IrkO1bVQCHcaKaTiJ5gilLSC5Wxtghu%2FJAeeY5HR%2BPU%3D
    ```

9. Olay Hub’ı kaynaklarını içeren bir kaynak grubu oluşturun. Event Hubs, Service Fabric’ten ELK kaynaklarını çalıştıran sunucuya ileti göndermek için kullanılır.

    ```azurecli
    az group create --location [REGION] --name [RESOURCE-GROUP-NAME]

    Example: az group create --location westus --name testeventhubsrg
    ```

10. Aşağıdaki komutu kullanarak bir Event Hubs kaynağı oluşturun. namespaceName, eventHubName, consumerGroupName, sendAuthorizationRule ve receiveAuthorizationRule ayrıntılarını girmek için istemleri izleyin.

    ```azurecli
    az deployment group create -g [RESOURCE-GROUP-NAME] --template-file eventhubsdeploy.json

    Example:
    az deployment group create -g testeventhubsrg --template-file eventhubsdeploy.json
    Please provide string value for 'namespaceName' (? for help): testeventhubnamespace
    Please provide string value for 'eventHubName' (? for help): testeventhub
    Please provide string value for 'consumerGroupName' (? for help): testeventhubconsumergroup
    Please provide string value for 'sendAuthorizationRuleName' (? for help): sender
    Please provide string value for 'receiveAuthorizationRuleName' (? for help): receiver
    ```

    Önceki komutun JSON çıkışında **çıkış** alanının içeriklerini kopyalayın. Service Fabric kümesi oluşturulduğunda gönderen bilgileri kullanılır. Alıcı adı ve anahtar, sonraki öğreticide Logstash hizmeti, Olay Hub’ından ileti alacak şekilde yapılandırıldığında kullanılmak üzere kaydedilmelidir. Aşağıdaki blob, örnek bir JSON çıkışıdır:

    ```json
    "outputs": {
        "receiver Key": {
            "type": "String",
            "value": "[KEY]"
        },
        "receiver Name": {
            "type": "String",
            "value": "receiver"
        },
        "sender Key": {
            "type": "String",
            "value": "[KEY]"
        },
        "sender Name": {
            "type": "String",
            "value": "sender"
        }
    }
    ```

11. *eventhubssastoken.py* betiğini çalıştırarak, oluşturduğunuz EventHubs kaynağı için SAS URL’sini oluşturun. Bu SAS URL’si, Event Hubs’a günlükler göndermek için Service Fabric kümesi tarafından kullanılır. Sonuç olarak, URL’yi oluşturmak için **gönderen** ilkesi kullanılır. Betik, aşağıdaki adımda kullanılan Event Hubs kaynağı için SAS URL’sini döndürür:

    ```python
    python3 eventhubssastoken.py 'testeventhubs' 'testeventhubs' 'sender' '[PRIMARY-KEY]'
    ```

    Döndürülen JSON’da **sr** alanının değerini kopyalayın. **sr** alanı değeri, EventHubs için SAS belirtecidir. Aşağıdaki URL, **sr** alanının bir örneğidir:

    ```output
    https%3A%2F%testeventhub.servicebus.windows.net%testeventhub&sig=7AlFYnbvEm%2Bat8ALi54JqHU4i6imoFxkjKHS0zI8z8I%3D&se=1517354876&skn=sender
    ```

    EventHubs için SAS URL 'niz yapıyı izler: `https://<namespacename>.servicebus.windows.net/<eventhubsname>?sr=<sastoken>` . Örneğin, `https://testeventhubnamespace.servicebus.windows.net/testeventhub?sr=https%3A%2F%testeventhub.servicebus.windows.net%testeventhub&sig=7AlFYnbvEm%2Bat8ALi54JqHU4i6imoFxkjKHS0zI8z8I%3D&se=1517354876&skn=sender`

12. sfdeploy.parameters.jsdosya *üzerinde* açın ve yukarıdaki adımlardan aşağıdaki içerikleri değiştirin. [SAS-URL-STORAGE-ACCOUNT] 8. adımda belirtilmiştir. [SAS-URL-EVENT-HUBS] 11. adımda belirtilmiştir.

    ```json
    "applicationDiagnosticsStorageAccountName": {
        "value": "teststorageaccount"
    },
    "applicationDiagnosticsStorageAccountSasToken": {
        "value": "[SAS-URL-STORAGE-ACCOUNT]"
    },
    "loggingEventHubSAS": {
        "value": "[SAS-URL-EVENT-HUBS]"
    }
    ```

13. **sfdeploy.parameters.json** dosyasını açın. Aşağıdaki parametreleri değiştirin ve dosyayı kaydedin.
    - **clusterName**. Yalnızca küçük harf ve sayısal değer kullanın.
    - **adminUserName** (boş olmayan bir değer)
    - **adminPassword** (boş olmayan bir değer)

14. Service Fabric kümenizi oluşturmak için aşağıdaki komutu çalıştırın

    ```azurecli
    az sf cluster create --location 'westus' --resource-group 'testlinux' --template-file sfdeploy.json --parameter-file sfdeploy.parameters.json --secret-identifier <certificate_url_from_step4>
    ```

## <a name="deploy-your-application-to-the-cluster"></a>Kümeye uygulamanızı dağıtma

1. Uygulamanızı dağıtmadan önce, *Voting/VotingApplication/ApplicationManifest.xml* dosyasına aşağıdaki kod parçacığını eklemeniz gerekir. **X509FindValue** alanı, **Azure’da Service Fabric kümesi oluşturma** bölümünün 4. Adımından döndürülen küçük resimdir. Bu kod parçacığı, **ApplicationManifest** alanının (kök alan) altında iç içe yerleştirilir.

    ```xml
    <Certificates>
          <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[CERTIFICATE-THUMBPRINT]" />
    </Certificates>
    ```

2. Bu kümede uygulamanızı dağıtmak için SFCTL kullanarak kümeyle bağlantı kurmanız gerekir. SFCTL, kümeye bağlanmak için ortak ve özel anahtarın her ikisine de sahip bir PEM dosyası gerektirir. Ortak ve özel anahtarın ikisine de sahip bir PEM dosyası oluşturmak için aşağıdaki komutu çalıştırın. 

    ```bash
    openssl pkcs12 -in <clustername>.<region>.cloudapp.azure.com.pfx -out sfctlconnection.pem -nodes -passin pass:<password>
    ```

3. Kümeye bağlanmak için aşağıdaki komutu çalıştırın.

    ```bash
    sfctl cluster select --endpoint https://<clustername>.<region>.cloudapp.azure.com:19080 --pem sfctlconnection.pem --no-verify
    ```

4. Uygulamanızı dağıtmak için *Oylama/Betikler* klasörüne gidin ve **install.sh** betiğini çalıştırın.

    ```bash
    ./install.sh
    ```

5. Service Fabric Explorer’a erişmek için sık kullandığınız tarayıcıyı açıp `https://testlinuxcluster.westus.cloudapp.azure.com:19080` yazın. Bu uç noktaya bağlanmak için kullanmak istediğiniz sertifika deposundan sertifikayı seçin. Linux makinesi kullanıyorsanız, Service Fabric Explorer’ı görüntülemek için *new-service-fabric-cluster-certificate.sh* betiği tarafından oluşturulan sertifikaların Chrome’a içeri aktarılması gerekir. Mac kullanıyorsanız, Anahtarlığınıza PFX dosyasını yüklemeniz gerekir. Uygulamanızın kümeye yüklendiğini görürsünüz.

    ![SFX Java Azure](./media/service-fabric-tutorial-java-deploy-azure/sfxjavaonazure.png)

6. Uygulamanıza erişmek için `https://testlinuxcluster.westus.cloudapp.azure.com:8080` yazın

    ![Oylama Uygulaması Java Azure](./media/service-fabric-tutorial-java-deploy-azure/votingappjavaazure.png)

7. Uygulamanızı kümeden kaldırmak için **Betikler** klasöründeki *uninstall.sh* betiğini çalıştırın

    ```bash
    ./uninstall.sh
    ```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Azure’da güvenli bir Linux kümesi oluşturma
> * ELK ile izleme için gerekli kaynakları oluşturma

Sonraki öğreticiye ilerleyin:
> [!div class="nextstepaction"]
> [İzleme ve Tanılamayı Ayarlama](service-fabric-tutorial-java-elk.md)
