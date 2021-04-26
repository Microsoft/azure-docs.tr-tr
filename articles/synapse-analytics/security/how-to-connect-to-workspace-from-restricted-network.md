---
title: Kısıtlanmış bir ağdan Azure SYNAPSE Analytics Studio 'daki çalışma alanı kaynaklarına bağlanma
description: Bu makalede, kısıtlı bir ağdan çalışma alanı kaynaklarınıza nasıl bağlanacaksınız
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: de7c5dba5a4868b7a8fdb390f974134cfaef7395
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384529"
---
# <a name="connect-to-workspace-resources-from-a-restricted-network"></a>Kısıtlanmış bir ağdan çalışma alanı kaynaklarına bağlanma

Kuruluşunuzun kısıtlanmış ağını yöneten bir BT Yöneticisi olduğunuzu varsayalım. Bu kısıtlı ağ içindeki bir iş istasyonu ile Azure SYNAPSE Analytics Studio arasındaki ağ bağlantısını etkinleştirmek istiyorsunuz. Bu makalede nasıl yapılacağı gösterilir.

## <a name="prerequisites"></a>Önkoşullar

* **Azure aboneliği**: Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.
* **Azure SYNAPSE Analytics çalışma alanı**: Azure SYNAPSE Analytics 'ten bir tane oluşturabilirsiniz. 4. adımda çalışma alanı adının olması gerekir.
* **Kısıtlı ağ**: BT Yöneticisi, kuruluş için kısıtlı ağı korur ve ağ ilkesini yapılandırma iznine sahiptir. Adım 3 ' te sanal ağ adı ve alt ağı gerekir.


## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>1. Adım: kısıtlanmış ağa ağ giden güvenlik kuralları ekleme

Dört hizmet etiketine sahip dört ağ giden güvenlik kuralı eklemeniz gerekir. 
* AzureResourceManager
* Azurefrontkapısı. ön uç
* AzureActiveDirectory
* AzureMonitor (Bu kural türü isteğe bağlıdır. Yalnızca verileri Microsoft ile paylaşmak istediğinizde ekleyin.)

Aşağıdaki ekran görüntüsünde Azure Resource Manager giden kuralı için Ayrıntılar gösterilmektedir.

![Hizmet etiketi ayrıntılarının Azure Resource Manager ekran görüntüsü.](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)

Diğer üç kuralı oluştururken, **hedef hizmet etiketinin** değerini **Azurefrontkapısı. ön uç**, **AzureActiveDirectory** veya **AzureMonitor** ile değiştirin.

Daha fazla bilgi için bkz. [hizmet etiketlerine genel bakış](../../virtual-network/service-tags-overview.md).

## <a name="step-2-create-private-link-hubs"></a>2. Adım: özel bağlantı hub 'ları oluşturma

Sonra, Azure portal özel bağlantı hub 'ları oluşturun. Portalda bunu bulmak için *Azure SYNAPSE Analytics (özel bağlantı hub 'ları)* araması yapın ve ardından bu bilgileri oluşturmak için gerekli bilgileri girin. 

![SYNAPSE özel bağlantı hub 'ı oluşturma ekranının ekran görüntüsü.](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-a-private-endpoint-for-your-synapse-studio"></a>3. Adım: SYNAPSE Studio için özel bir uç nokta oluşturma

Azure SYNAPSE Analytics Studio 'ya erişmek için Azure portal özel bir uç nokta oluşturmanız gerekir. Portalda bunu bulmak için *özel bağlantı* aratın. **Özel bağlantı merkezinde** **Özel uç nokta oluştur**' u seçin ve ardından oluşturmak için gerekli bilgileri girin. 

> [!Note]
> **Bölge** değerinin, Azure SYNAPSE Analytics çalışma alanınızın bulunduğu bir değerle aynı olduğundan emin olun.

![Özel uç nokta oluşturma temelleri sekmesinin ekran görüntüsü.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

**Kaynak** sekmesinde, adım 2 ' de oluşturduğunuz özel bağlantı merkezini seçin.

![Özel uç nokta oluşturma, kaynak sekmesi ekran görüntüsü.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

**Yapılandırma** sekmesinde: 
* **Sanal ağ** için, kısıtlı sanal ağ adını seçin.
* **Alt ağ** için, kısıtlı sanal ağın alt ağını seçin. 
* **Özel DNS bölgesi Ile tümleştirme** için **Evet**' i seçin.

![Özel uç nokta oluşturma, yapılandırma sekmesinin ekran görüntüsü.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

Özel bağlantı uç noktası oluşturulduktan sonra, Azure SYNAPSE Analytics Studio için Web aracının oturum açma sayfasına erişebilirsiniz. Ancak, çalışma alanınızın içindeki kaynaklara henüz erişemeyeceksiniz. Bunun için bir sonraki adımı gerçekleştirmeniz gerekir.

## <a name="step-4-create-private-endpoints-for-your-workspace-resource"></a>4. Adım: çalışma alanı kaynağınız için özel uç noktalar oluşturma

Azure SYNAPSE Analytics Studio çalışma alanı kaynağınızın içindeki kaynaklara erişmek için aşağıdakileri oluşturmanız gerekir:

- **Hedef alt kaynak** türü **dev** olan en az bir özel bağlantı uç noktası.
- Hangi çalışma alanındaki kaynaklara bağlı olarak, **SQL** veya **sqlondemand** türlerine sahip iki diğer isteğe bağlı özel bağlantı uç noktası.

Bunların oluşturulması, önceki adımda bitiş noktasını oluşturma ile benzerdir.  

**Kaynak** sekmesinde:

* **Kaynak türü** için **Microsoft. SYNAPSE/çalışma alanları**' nı seçin.
* **Kaynak** için, daha önce oluşturduğunuz çalışma alanı adını seçin.
* **Hedef alt kaynak** için uç nokta türünü seçin:
  * **SQL, SQL** havuzunda SQL sorgu yürütme içindir.
  * **Sqlondemand** , SQL yerleşik sorgu yürütme içindir.
  * **Geliştirme** , Azure SYNAPSE Analytics Studio çalışma alanları içindeki diğer her şeye erişmeye yöneliktir. Bu türün en az bir özel bağlantı uç noktası oluşturmanız gerekir.

![Özel uç nokta, kaynak sekmesi, çalışma alanı oluşturma ekran görüntüsü.](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)


## <a name="step-5-create-private-endpoints-for-workspace-linked-storage"></a>5. Adım: çalışma alanı bağlantılı depolama için özel uç noktalar oluşturma

Azure SYNAPSE Analytics Studio çalışma alanındaki Depolama Gezgini ile bağlantılı depolamaya erişmek için bir özel uç nokta oluşturmanız gerekir. Bunun adımları 3. adımdan benzerdir. 

**Kaynak** sekmesinde:
* **Kaynak türü** için **Microsoft. SYNAPSE/storageaccounts**' ı seçin.
* **Kaynak** için, daha önce oluşturduğunuz depolama hesabı adını seçin.
* **Hedef alt kaynak** için uç nokta türünü seçin:
  * **BLOB** , Azure Blob depolama içindir.
  * **DFS** , Azure Data Lake Storage 2. içindir.

![Özel uç nokta, kaynak sekmesi, depolama alanı oluşturma ekran görüntüsü.](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-storage.png)

Artık bağlı depolama kaynağına erişebilirsiniz. Sanal ağınızda, Azure SYNAPSE Analytics Studio çalışma alanınızda, bağlantılı depolama kaynağına erişmek için Depolama Gezgini ' ni kullanabilirsiniz.

Aşağıdaki ekran görüntüsünde gösterildiği gibi, çalışma alanınız için yönetilen bir sanal ağı etkinleştirebilirsiniz:

![Yönetilen sanal ağı etkinleştir seçeneği vurgulanmış şekilde, Create SYNAPSE Workspace 'in ekran görüntüsü.](./media/how-to-connect-to-workspace-from-restricted-network/ws-network-config.png)

Not defterinizin belirli bir depolama hesabı altındaki bağlı depolama kaynaklarına erişmesini istiyorsanız, Azure SYNAPSE Analytics Studio ' nun altına yönetilen özel uç noktaları ekleyin. Depolama hesabı adı, not defterinizin erişmesi gereken bir ad olmalıdır. Daha fazla bilgi için bkz. [veri kaynağınıza yönetilen özel uç nokta oluşturma](./how-to-create-managed-private-endpoints.md).

Bu uç noktayı oluşturduktan sonra, onay durumu **bekleyen** durumunu gösterir. Azure portal bu depolama hesabının **Özel uç nokta bağlantıları** sekmesinde Bu depolama hesabının sahibinden onay isteyin. Onaylandıktan sonra, Not defteriniz bu depolama hesabı altındaki bağlı depolama kaynaklarına erişebilir.

Şimdi, All kümesi. Azure SYNAPSE Analytics Studio çalışma alanı kaynağınızın erişimine erişebilirsiniz.

## <a name="appendix-dns-registration-for-private-endpoint"></a>Ek: özel uç nokta için DNS kaydı

Özel uç nokta oluşturma sırasında aşağıda ekran görüntüsü olarak "özel DNS bölgesi ile tümleştirin" etkinleştirilmemişse, Özel uç noktalarınızın her biri için "**özel DNS bölgesi**" oluşturmanız gerekir.
![SYNAPSE özel DNS bölgesi 1 oluşturma ekranının ekran görüntüsü.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-1.png)

Portalda **özel DNS bölgeyi** bulmak için *özel DNS bölgeyi* arayın. **Özel DNS bölgesinde**, gerekli bilgileri oluşturmak için aşağıdaki bilgileri girin.

* **Ad** için, özel DNS bölgesine özel bir uç nokta için adanmış adı aşağıdaki gibi girin:
  * **`privatelink.azuresynapse.net`** , Azure SYNAPSE Analytics Studio Gateway 'e erişmenin özel uç noktasıdır. Adım 3 ' te bu tür özel uç nokta oluşturma bölümüne bakın.
  * **`privatelink.sql.azuresynapse.net`** , SQL havuzunda ve yerleşik havuzda SQL sorgu yürütmesinin bu tür özel uç noktasıdır. 4. adımda uç nokta oluşturma bölümüne bakın.
  * **`privatelink.dev.azuresynapse.net`** , Azure SYNAPSE Analytics Studio çalışma alanları içindeki diğer her şeye erişmek için bu tür özel uç nokta içindir. 4. adımda bu tür özel uç nokta oluşturma bölümüne bakın.
  * **`privatelink.dfs.core.windows.net`** , çalışma alanına bağlı Azure Data Lake Storage 2. erişmek için özel uç nokta içindir. 5. adımda bu tür özel uç nokta oluşturma bölümüne bakın.
  * **`privatelink.blob.core.windows.net`** , çalışma alanına bağlı Azure Blob depolama alanına erişmek için özel uç nokta içindir. 5. adımda bu tür özel uç nokta oluşturma bölümüne bakın.

![SYNAPSE özel DNS bölge 2 oluştur ekran görüntüsü.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-2.png)

**Özel DNS bölge** oluşturulduktan sonra, oluşturulan özel DNS bölgesini girin ve sanal ağınıza bağlantıyı eklemek için **sanal ağ bağlantılarını** seçin. 

![SYNAPSE özel DNS bölgesi oluşturma 3 ekran görüntüsü.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-3.png)

Zorunlu alanları aşağıda gösterildiği gibi girin:
* **Bağlantı adı** için bağlantı adı ' nı girin.
* **Sanal ağ** için Sanal ağınızı seçin.

![SYNAPSE özel DNS bölgesi oluşturma 4 ekran görüntüsü.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-4.png)

Sanal ağ bağlantısı eklendikten sonra, daha önce oluşturduğunuz **özel DNS BÖLGESINE** DNS kayıt kümesini eklemeniz gerekir.

* **Ad** için, farklı özel uç nokta için ayrılmış ad dizelerini girin: 
  * **Web** , Azure SYNAPSE Analytics Studio 'ya erişmenin özel uç noktasıdır.
  * "***Yourçalışmaalanıadı***" SQL havuzundaki SQL sorgu yürütmesinin özel uç noktası ve ayrıca Azure SYNAPSE Analytics Studio çalışma alanları içindeki diğer her şeye erişmenin özel uç noktasıdır.
  * "***Yourçalışmaalanıadı *-OnDemand**", yerleşik havuzda SQL sorgu yürütmesinin özel uç noktasıdır.
* **Tür** **için yalnızca DNS** kayıt türü ' nü seçin. 
* **IP adresi** için, her özel uç noktanın KARŞıLıK gelen IP adresini girin. **Ağ ARABIRIMINDEKI** IP adresini özel uç noktanıza genel bakış ' dan edinebilirsiniz.

![SYNAPSE özel DNS bölgesi 5 oluşturma ekranının ekran görüntüsü.](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-5.png)


## <a name="next-steps"></a>Sonraki adımlar

[Yönetilen çalışma alanı sanal ağı](./synapse-workspace-managed-vnet.md)hakkında daha fazla bilgi edinin.

[Yönetilen özel uç noktalar](./synapse-workspace-managed-private-endpoints.md)hakkında daha fazla bilgi edinin.
