---
title: Azure DevTest Labs bir Service Fabric kümesiyle ortam oluşturma | Microsoft Docs
description: Kendi içinde Service Fabric kümeyle ortam oluşturmayı ve zamanlamaları kullanarak kümeyi başlatıp durdurmayı öğrenin.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: EMaher
manager: femila
editor: spelluru
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2019
ms.author: enewman
ms.openlocfilehash: 1e192a2b27c9d617e43a56766431a0f40e87a752
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325247"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Azure DevTest Labs içinde kendi içinde Service Fabric kümeyle bir ortam oluşturun
Bu makalede, Azure DevTest Labs içinde kendi içinde Service Fabric kümeyle ortam oluşturma hakkında bilgi sağlanır. 

## <a name="overview"></a>Genel Bakış
DevTest Labs, Azure Kaynak Yönetimi şablonları tarafından tanımlanan, kendi kendine dahil edilen test ortamları oluşturabilir. Bu ortamlar, sanal makineler gibi IaaS kaynaklarını ve Service Fabric gibi PaaS kaynaklarını içerir. DevTest Labs, sanal makineleri denetlemek için komutlar sunarak bir ortamdaki sanal makineleri yönetmenizi sağlar. Bu komutlar bir zamanlamaya göre bir sanal makineyi başlatma veya durdurma olanağı sağlar. Benzer şekilde, DevTest Labs de bir ortamdaki Service Fabric kümelerini yönetmenize yardımcı olabilir. Bir ortamda el ile veya bir zamanlama aracılığıyla Service Fabric kümesi başlatabilir veya durdurabilirsiniz.

## <a name="create-a-service-fabric-cluster"></a>Service Fabric kümesi oluşturma
Service Fabric kümeler, DevTest Labs içindeki ortamlar kullanılarak oluşturulur. Her ortam, bir git deposundaki bir Azure Resource Manager şablonu tarafından tanımlanır. DevTest Labs için [genel git deposu](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) , [Servicefabric-cluster](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) klasöründe bir Service Fabric kümesi oluşturmak için Kaynak Yöneticisi şablonunu içerir. 

1. İlk olarak, aşağıdaki makaledeki yönergeleri kullanarak Azure DevTest Labs bir laboratuvar oluşturun: [Laboratuvar oluşturun](devtest-lab-create-lab.md). **Genel ortamlar** seçeneğinin varsayılan olarak **Açık** olduğunu unutmayın. 
2. Aşağıdaki adımları izleyerek Service Fabric sağlayıcının aboneliğiniz için kayıtlı olduğunu doğrulayın:
    1. Sol gezinti menüsünde **abonelikler** ' i seçin ve **aboneliğinizi** seçin
    2. **Abonelik** sayfasında, Sol menüdeki **Ayarlar** bölümünde **kaynak sağlayıcıları** ' nı seçin. 
    3. **Microsoft. ServiecFabric** kayıtlı değilse **Kaydet**' i seçin. 
3. Laboratuvarınızın **DevTest Lab** sayfasında, araç çubuğunda **+ Ekle** ' yi seçin. 
    
    ![Araç çubuğuna düğme Ekle](./media/create-environment-service-fabric-cluster/add-button.png)
3. **Temel seçin** sayfasında, listeden **Service Fabric laboratuvar kümesi** ' ni seçin. 

    ![Listeden Service Fabric laboratuvar kümesi seçin](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. **Ayarları Yapılandır** sayfasında, aşağıdaki adımları uygulayın: 
    1. Küme **ortamınız**için bir **ad** belirtin. Bu, Azure 'daki Service Fabric kümesinin oluşturulacağı kaynak grubunun adıdır. 
    2. Küme sanal makineleri için **işletim sistemini (OS)** seçin. Varsayılan değer: **Windows**.
    3. Kümenin **Yöneticisi** için bir ad belirtin. 
    4. Yönetici için bir **parola** belirtin. 
    5. **Sertifika**için sertifika bilgilerinizi Base64 olarak kodlanmış bir dize olarak girin. Bir sertifika oluşturmak için aşağıdaki adımları uygulayın:
        1. **Create-ClusterCertificate. ps1** dosyasını [Git deposundan](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster)indirin. Alternatif olarak, depoyu makinenizde klonlayın. 
        2. **PowerShell**’i başlatın. 
        3. Komutunu`.\Create-ClusterCertificate.ps1`kullanarak **ps1** dosyasını çalıştırın. Not defteri 'nde açılan bir metin dosyası, bu sayfadaki sertifikayla ilgili alanları doldurmanız için gereken bilgileri görürsünüz. . 
    6. **Sertifika için parolayı**girin.
    7. Sertifikanızın **parmak izini** belirtin.
    8. **Ayarları Yapılandır** sayfasında **Ekle** ' yi seçin. 

        ![Küme ayarlarını yapılandır](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. Küme oluşturulduktan sonra, önceki adımda belirttiğiniz ortam adına sahip bir kaynak grubu görürsünüz. Genişlettikten sonra Service Fabric kümeyi görürsünüz. Kaynak grubunun durumu **oluşturma**sırasında takılırsa, araç çubuğunda **Yenile** ' yi seçin. **Service Fabric küme** ortamı, Linux veya Windows üzerinde 5 düğümlü 1-NodeType kümesi oluşturur.

    Aşağıdaki örnekte, **hayal fabricclusterrg** , Service Fabric kümesi için özel olarak oluşturulan kaynak grubunun adıdır. Laboratuvar ortamlarının oluşturuldukları kaynak grubu içinde kendi kendine dahil olduğunu unutmamak önemlidir. Bu, ortamı tanımlayan, yalnızca yeni oluşturulan kaynak grubu içindeki kaynaklara veya [Laboratuvar tarafından kullanılmak üzere yapılandırılmış sanal ağlara](devtest-lab-configure-vnet.md)erişebilecek olan şablona yol gösterir. Yukarıdaki bu örnek, aynı kaynak grubundaki tüm gerekli kaynakları oluşturur.

    ![Küme oluşturuldu](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Kümeyi başlatma veya durdurma
Kümeyi DevTest Labs sayfasından veya DevTest Labs tarafından sunulan Service Fabric kümesi sayfasından başlatabilir veya durdurabilirsiniz. 

### <a name="from-the-devtest-lab-page"></a>DevTest Lab sayfasından
Laboratuvar için DevTest Lab sayfasında kümeyi başlatabilir veya durdurabilirsiniz. 

1. Aşağıdaki görüntüde gösterildiği gibi Service Fabric kümesi için **üç nokta (...)** seçin: 

    ![Küme için başlatma ve durdurma komutları](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. Kümeyi **başlatmak** ve **durdurmak** için bağlam menüsünde iki komut görürsünüz. Start komutu kümedeki tüm düğümleri başlatır. Durdur komutu bir kümedeki tüm düğümleri durdurur. Bir küme durdurulduktan sonra, Service Fabric kümesi hazır bir durumda kalır, ancak Başlat komutu laboratuvardaki kümede yeniden oluşturuluncaya kadar hiçbir düğüm kullanılamaz.

    Bir test ortamında Service Fabric kümesi kullanırken dikkat etmeniz gereken bazı noktalar vardır. Düğümler yeniden başlatıldıktan sonra Service Fabric kümesinin tamamen yeniden doldurulması biraz zaman alabilir. Başlangıçtan itibaren verilerin başlatılmasını sağlamak için, verilerin sanal makineye bağlı bir yönetilen diske kaydedilmesi gerekir. Ekli yönetilen disk kullanılırken performans açısından etkileri vardır; bu nedenle yalnızca test ortamları için önerilir. Veri depolama için kullanılan disk yedeklenmez, küme üzerinde durdur komutu verildiğinde veriler kaybolur.

### <a name="from-the-service-fabric-cluster-page"></a>Service Fabric kümesi sayfasından 
Kümeyi başlatmak veya durdurmak için başka bir yol vardır. 

1. DevTest Lab sayfasındaki ağaç görünümünde Service Fabric kümenizi seçin. 

    ![Kümenizi seçin](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. Küme için **Service Fabric kümesi** sayfasında, kümeyi başlatmak veya durdurmak için araç çubuğundaki komutları görürsünüz. 

    ![Service Fabric kümesi sayfasında komutları Başlat veya Durdur](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Otomatik başlatma ve otomatik kapatılma zamanlamasını yapılandırma
Service Fabric kümeler, bir zamanlamaya göre de başlatılabilir veya durdurulabilir. Bu deneyim, laboratuvardaki sanal makinelere yönelik deneyimle benzerdir. Para tasarrufu sağlamak için, varsayılan olarak, bir laboratuvarda oluşturulan her küme otomatik olarak laboratuvar [kapanması ilkesi](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)tarafından tanımlanan saatte kapanır. Kümenin kapatılıp kapatılmayacağını belirterek veya kümenin kapatıldığı saati belirterek geçersiz kılabilirsiniz. 

![Otomatik başlatma ve otomatik kapanmaya yönelik mevcut zamanlamalar](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Otomatik başlatma zamanlamasını kabul et
Başlatma zamanlamasını kabul etmek için aşağıdaki adımları uygulayın:

1. Sol taraftaki menüden **otomatik başlatma** seçeneğini belirleyin
2. **Bu Service Fabric kümesinin otomatik başlatma için zamanlanmasını Izin ver**Için **Açık '** ı seçin. Bu sayfa yalnızca laboratuvar sahibinin kullanıcıların sanal makinelerini veya Service Fabric kümelerini otomatik olarak otomatik olarak açmasına izin verdiği durumlarda etkinleştirilir.
3. Araç çubuğunda **Kaydet**’i seçin. 

    ![Otomatik yıldız sayfası](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Otomatik kapanmaya yönelik ayarları Yapılandır 
Kapatılacak ayarları değiştirmek için aşağıdaki adımları uygulayın:

1. Sol menüde **otomatik olarak kapatmalar** ' yı seçin. 
2. Bu sayfada, **etkin**' i seçerek otomatik kapatmayı **devre** dışı bırakabilirsiniz. 
3. **'** In **etkin**' i seçtiyseniz, şu adımları izleyin:
    1. Kapatılma **zamanını** belirtin.
    2. Saat için saat **dilimini** belirtin. 
    3. DevTest Labs 'in otomatik kapatmadan önce **bildirim** göndermesini isteyip istemediğinizi belirtin. 
    4. Bildirim seçeneği için **Evet** ' i seçtiyseniz, bildirim göndermek Için **Web kancası URL 'sini** ve/veya **e-posta adresini** belirtin. 
    5. Araç çubuğunda **Kaydet**’i seçin.

        ![Otomatik kapatma sayfası](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Service Fabric kümesindeki düğümleri görüntülemek için
Daha önce adımlarda gördüğünüz Service Fabric kümesi sayfası DevTest Labs sayfasına özgüdür. Bu, kümedeki düğümleri göstermez. Küme hakkında daha fazla bilgi görmek için şu adımları izleyin:

1. Laboratuvarınızın **DevTest Lab** sayfasında, **sanal makinelerim** bölümünde ağaç görünümünde **kaynak grubunu** seçin.

    ![Kaynak grubu seçin](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. **Kaynak grubu** sayfasında, kaynak grubundaki tüm kaynakları bir listede görürsünüz. Listeden **Service Fabric kümenizi** seçin. 

    ![Listeden kümenizi seçin](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Kümeniz için **Service Fabric kümesi** sayfasını görürsünüz. Bu, Service Fabric sağladığı sayfasıdır. Düğümler, düğüm türleri vb. gibi kümelerle ilgili tüm bilgileri görürsünüz.

    ![Service Fabric kümesi giriş sayfası](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Sonraki adımlar
Ortamlar hakkındaki ayrıntılar için aşağıdaki makalelere bakın: 

- [Azure Resource Manager şablonları ile çoklu VM ortamları ve PaaS kaynakları oluşturma](devtest-lab-create-environment-from-arm.md)
- [Azure DevTest Labs ortak ortamları yapılandırma ve kullanma](devtest-lab-configure-use-public-environments.md)
