---
title: Sanal ağda Azure Databricks çalışma alanı oluşturma
description: Bu makalede Azure Databricks sanal ağınıza nasıl dağıtabileceğiniz açıklanır.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 69afe2aab3c10707f7160d727b970ad73d59a952
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791551"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-a-virtual-network"></a>Hızlı başlangıç: Sanal ağda Azure Databricks çalışma alanı oluşturma

Bu hızlı başlangıçta, bir sanal ağda Azure Databricks çalışma alanının nasıl oluşturulacağı gösterilmektedir. Ayrıca, bu çalışma alanı içinde bir Apache Spark kümesi oluşturacaksınız.

Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure Portal](https://portal.azure.com/)’ında oturum açın.

> [!Note]
> Bu öğretici **Azure Ücretsiz deneme aboneliği**kullanılarak gerçekleştirilemez.
> Ücretsiz hesabınız varsa, profilinize gidin ve aboneliğinizi **Kullandıkça Öde**ile değiştirin. Daha fazla bilgi için bkz. [Ücretsiz Azure hesabı](https://azure.microsoft.com/free/). Ardından, [harcama limitini kaldırın](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)ve bölgenizdeki vCPU 'lar için [bir kota artışı isteyin](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) . Azure Databricks çalışma alanınızı oluşturduğunuzda, çalışma alanına 14 gün boyunca ücretsiz Premium Azure Databricks DBUs erişimi sağlamak için **deneme (Premium-14 gün ücretsiz DBUs)** fiyatlandırma katmanını seçebilirsiniz.

## <a name="create-a-virtual-network"></a>Sanal ağ oluşturun

1. Azure portal > **ağ** > **sanal ağ**' **a kaynak oluştur** ' u seçin.

2. **Sanal ağ oluştur**altında aşağıdaki ayarları uygulayın: 

    |Ayar|Önerilen değer|Açıklama|
    |-------|---------------|-----------|
    |Adı|databricks-hızlı başlangıç|Sanal ağınız için bir ad seçin.|
    |Adres alanı|10.1.0.0/16|CıDR gösteriminde sanal ağın adres aralığı.|
    |Abonelik|\<Aboneliğiniz\>|Kullanmak istediğiniz Azure aboneliğini seçin.|
    |Kaynak grubu|databricks-hızlı başlangıç|**Yeni oluştur** ' u seçin ve hesabınız için yeni bir kaynak grubu adı girin.|
    |Konum|\<Kullanıcılarınıza en yakın bölgeyi seçin\>|Sanal ağınızı barındırabileceğiniz bir coğrafi konum seçin. Kullanıcılarınıza en yakın konumu kullanın.|
    |Alt ağ adı|default|Sanal ağınızdaki varsayılan alt ağ için bir ad seçin.|
    |Alt Ağ Adresi aralığı|10.1.0.0/24|CIDR gösteriminde alt ağın adres aralığı. Sanal ağın adres alanı tarafından içerilmelidir. Kullanımda olan bir alt ağın adres aralığı düzenlenemiyor.|

    ![Azure portal üzerinde sanal ağ oluşturma](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Dağıtım tamamlandıktan sonra sanal ağınıza gidin ve **Ayarlar**altında **Adres alanı** ' nı seçin. *Ek adres aralığı Ekle*' nin kutusunda `10.179.0.0/16` ekleyin ve **Kaydet**' i seçin.

    ![Azure sanal ağ adres alanı](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks çalışma alanı oluşturma

1. Azure portal **kaynak oluştur** > **Analytics** > **databricks**' i seçin.

2. **Azure Databricks hizmeti**altında aşağıdaki ayarları uygulayın:

    |Ayar|Önerilen değer|Açıklama|
    |-------|---------------|-----------|
    |Çalışma alanı adı|databricks-hızlı başlangıç|Azure Databricks çalışma alanınız için bir ad seçin.|
    |Abonelik|\<Aboneliğiniz\>|Kullanmak istediğiniz Azure aboneliğini seçin.|
    |Kaynak grubu|databricks-hızlı başlangıç|Sanal ağ için kullandığınız kaynak grubunu seçin.|
    |Konum|\<Kullanıcılarınıza en yakın bölgeyi seçin\>|Sanal ağınızla aynı konumu seçin.|
    |Fiyatlandırma Katmanı|Standart veya Premium arasından seçim yapın.|Fiyatlandırma katmanları hakkında daha fazla bilgi için bkz. [Databricks fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/databricks/).|
    |Sanal ağınızda Azure Databricks çalışma alanı dağıtma|Yes|Bu ayar, sanal ağınıza bir Azure Databricks çalışma alanı dağıtmanıza olanak tanır.|
    |Sanal Ağ|databricks-hızlı başlangıç|Önceki bölümde oluşturduğunuz sanal ağı seçin.|
    |Ortak alt ağ adı|Genel-alt ağ|Varsayılan genel alt ağ adını kullanın.|
    |Ortak alt ağ CıDR aralığı|10.179.64.0/18|Bu alt ağ için CıDR aralığı/18 ile/26 arasında olmalıdır.|
    |Özel alt ağ adı|Özel-alt ağ|Varsayılan özel alt ağ adını kullanın.|
    |Özel alt ağ CıDR aralığı|10.179.0.0/18|Bu alt ağ için CıDR aralığı/18 ile/26 arasında olmalıdır.|

    ![Azure portal Azure Databricks çalışma alanı oluşturma](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. Dağıtım tamamlandıktan sonra Azure Databricks kaynağına gidin. Sanal ağ eşlemesinin devre dışı olduğuna dikkat edin. Ayrıca, Genel Bakış sayfasındaki kaynak grubu ve yönetilen kaynak grubu ' na de dikkat edin. 

    ![Azure portal Azure Databricks genel bakış](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    Yönetilen kaynak grubu, depolama hesabı (DBFS), çalışan-SG (ağ güvenlik grubu), çalışanlar-VNET (sanal ağ) fiziksel konumunu içerir. Ayrıca sanal makinelerin, diskin, IP adresinin ve ağ arabiriminin oluşturulacağı konumdur. Bu kaynak grubu varsayılan olarak kilitlidir; Ancak, sanal ağda bir küme başlatıldığında, yönetilen kaynak grubundaki ve "Hub" sanal ağı 'ndaki çalışanlar-VNET arasında bir ağ arabirimi oluşturulur.

    ![Azure Databricks yönetilen kaynak grubu](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

## <a name="create-a-cluster"></a>Küme oluşturma

> [!NOTE]
> Azure Databricks kümesini oluşturmak için ücretsiz hesap oluşturmak istiyorsanız kümeyi oluşturmadan önce profilinize gidin ve aboneliğini **kullandıkça öde** modeline geçirin. Daha fazla bilgi için bkz. [Ücretsiz Azure hesabı](https://azure.microsoft.com/free/).

1. Azure Databricks hizmetinize dönün ve **genel bakış** sayfasında **çalışma alanını Başlat** ' ı seçin.

2. **Kümeler** >  **+ küme oluştur**' u seçin. Ardından, *databricks-QuickStart-Cluster*gibi bir küme adı oluşturun ve kalan varsayılan ayarları kabul edin. **Küme oluştur**' u seçin.

    ![Azure Databricks kümesi oluşturma](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. Küme çalışmaya başladıktan sonra Azure portal yönetilen kaynak grubuna geri dönün. Yeni sanal makineler, diskler, IP adresi ve ağ arabirimlerine dikkat edin. IP adresleriyle ortak ve özel alt ağların her birinde bir ağ arabirimi oluşturulur.  

    ![Küme oluşturulduktan sonra yönetilen kaynak grubu Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Azure Databricks çalışma alanınıza dönün ve oluşturduğunuz kümeyi seçin. Ardından **Spark Kullanıcı arabirimi** sayfasındaki **yürüticileri** sekmesine gidin. Sürücü ve yürüticilere ait adreslerin özel alt ağ aralığında olduğunu unutmayın. Bu örnekte, sürücü 10.179.0.6 ve yürüticileri 10.179.0.4 ve 10.179.0.5 'dir. IP adresleriniz farklı olabilir.

    ![Azure Databricks Spark UI yürüticileri](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Makaleyi tamamladıktan sonra kümeyi sonlandırabilirsiniz. Bunu yapmak için Azure Databricks çalışma alanında sol bölmedeki **Kümeler**’i seçin. Sonlandırmak istediğiniz küme için imleci **Eylemler** sütunu altındaki üç noktanın üzerine taşıyın ve **Sonlandır** simgesini seçin. Bu, kümeyi sonlandırır.

Bu kümeyi el ile sonlandıramazsanız, kümeyi oluştururken **\_sonra Sonlandır** onay kutusunu\_, sonra da Durdur seçeneğini belirlediyseniz, otomatik olarak durdurulur. Böyle bir durumda, belirtilen süre boyunca etkin olmaması durumunda küme otomatik olarak durdurulur.

Kümeyi yeniden kullanmak istemiyorsanız, Azure portal oluşturduğunuz kaynak grubunu silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir sanal ağa dağıttığınız Azure Databricks bir Spark kümesi oluşturdunuz. Bir Azure Databricks Not defterinden JDBC kullanarak sanal ağda SQL Server Linux Docker kapsayıcısını sorgulama hakkında bilgi edinmek için sonraki makaleye ilerleyin.  

> [!div class="nextstepaction"]
>[Bir Azure Databricks Not defterinden bir sanal ağda SQL Server Linux Docker kapsayıcısını sorgulama](vnet-injection-sql-server.md)
