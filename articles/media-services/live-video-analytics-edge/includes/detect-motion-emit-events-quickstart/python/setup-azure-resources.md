---
ms.openlocfilehash: 98a3965160e7ab6c86ba8c6d3a4dfd75af4c6dbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101750693"
---
Bu öğreticide aşağıdaki Azure kaynakları gereklidir:

* IoT Hub
* Depolama hesabı
* Azure Media Services hesabı
* Azure 'da [IoT Edge Runtime](../../../../../iot-edge/how-to-install-iot-edge.md) yüklüyken Linux sanal makinesi

Bu hızlı başlangıç için, Azure aboneliğinizde gerekli kaynakları dağıtmak üzere [canlı video analizi kaynakları kurulum betiğini](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) kullanmanızı öneririz. Bunu yapmak için aşağıdaki adımları izleyin:

1. [Azure Cloud Shell](https://ms.portal.azure.com/#cloudshell/)’i açın.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/quickstarts/cloud-shell.png" alt-text="Cloud Shell":::
1. İlk kez Cloud Shell kullanıyorsanız, bir depolama hesabı ve bir Microsoft Azure dosya paylaşımının oluşturulması için bir abonelik seçmeniz istenir. Cloud Shell oturum bilgileriniz için bir depolama hesabı oluşturmak üzere **depolama oluştur** ' u seçin. Bu depolama hesabı, komut dosyasının Azure Media Services hesabınızla kullanılmak üzere oluşturulacağı hesaptan ayrıdır.
1. Cloud Shell penceresinin sol tarafındaki açılan menüde, ortamınız olarak **Bash** ' i seçin.

    ![Ortam Seçicisi](../../../media/quickstarts/env-selector.png)
1. Aşağıdaki komutu çalıştırın.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    Betiği başarıyla tamamladıktan sonra aboneliğinizdeki tüm gerekli kaynakları görmeniz gerekir. Komut dosyası tarafından toplam 12 kaynak oluşturulur:
    1. **Akış uç noktası** -bu, kayıtlı AMS varlığını yürütmeye yardımcı olur.
    1. **Sanal makine** -bu, Edge cihazınız olarak görev yapacak bir sanal makinedir.
    1. **Disk** -bu, medya ve yapıtları depolamak için sanal makineye bağlı bir depolama disktir.
    1. **Ağ güvenlik grubu** -bu, bir Azure sanal ağındaki Azure kaynaklarından gelen ve giden ağ trafiğini filtrelemek için kullanılır.
    1. **Ağ arabirimi** -bu, bir Azure sanal makinesinin Internet, Azure ve diğer kaynaklarla iletişim kurmasını sağlar.
    1. Savunma **bağlantısı** -bu, tarayıcınızı ve Azure Portal kullanarak sanal makinenize bağlanmanızı sağlar.
    1. **Genel IP adresi** -bu, Azure kaynaklarının Internet ve genel kullanıma yönelik Azure hizmetleriyle iletişim kurmasını sağlar
    1. **Sanal ağ** -bu, sanal makineniz gibi birçok Azure Kaynak türünün, internet ve şirket içi ağlarla güvenli bir şekilde iletişim kurmasını sağlar. [Sanal ağlar](../../../../../virtual-network/virtual-networks-overview.md) hakkında daha fazla bilgi
    1. **IoT Hub** -bu, IoT uygulamanız, IoT Edge modülleriniz ve yönettiği cihazlar arasında çift yönlü iletişim için bir merkezi ileti hub 'ı görevi görür.
    1. **Medya hizmeti hesabı** -bu, Azure 'da medya içeriğini yönetmeye ve akışa yardımcı olur.
    1. **Depolama hesabı** -bir birincil depolama hesabınız olmalıdır ve Media Services hesabınızla Ilişkili birkaç ikincil depolama hesabı olabilir. Daha fazla bilgi için bkz. [Azure Media Services hesapları Ile Azure depolama hesapları](../../../../latest/storage-account-concept.md).
    1. **Kapsayıcı kayıt defteri** -bu, özel Docker kapsayıcı görüntülerinizi ve ilgili yapıtlarınızı depolamaya ve yönetmeye yardımcı olur.
1. Betik tamamlandıktan sonra, klasör yapısını göstermek için süslü ayraçları seçin. *~/CloudDrive/LVA-Sample* dizininde birkaç dosya görürsünüz. Bu hızlı başlangıçta ilgilendiğiniz:

     * ***~/CloudDrive/LVA-Sample/Edge-Deployment/.exe*** -bu dosya Visual Studio Code bir uç cihaza modül dağıtmak için kullandığı özellikleri içerir.
     * ***~/CloudDrive/LVA-Sample/appsetting.json*** -Visual Studio Code, örnek kodu çalıştırmak için bu dosyayı kullanır.
     
    Bir sonraki bölümde Visual Studio Code ' de geliştirme ortamınızı ayarlarken bu dosyalar gerekir. Bunları şimdilik yerel bir dosyaya kopyalamak isteyebilirsiniz.
    
    ![Uygulama ayarları](../../../media/quickstarts/clouddrive.png)

> [!TIP]
> Oluşturulan Azure kaynaklarıyla ilgili sorunlar yaşıyorsanız, bazı sık karşılaşılan sorunları çözmek için lütfen **[sorun giderme kılavuzumuzu](../../../troubleshoot-how-to.md#common-error-resolutions)** görüntüleyin.