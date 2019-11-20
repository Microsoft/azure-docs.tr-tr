---
title: Azure sanal makineler - Portal için birden çok IP adresi | Microsoft Docs
description: Azure portalını kullanarak bir sanal makineye birden çok IP adresi atama hakkında bilgi edinin | Resource Manager.
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: 3a8cae97-3bed-430d-91b3-274696d91e34
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: anavin
ms.openlocfilehash: fcb9cc817128bce9498312d3fb016761b6382634
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612748"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Azure portalını kullanarak sanal makineler için birden çok IP adresi atama

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> Bu makalede, Azure portalını kullanarak Azure Resource Manager dağıtım modeli sanal makine (VM) oluşturma açıklanmaktadır. Birden çok IP adresi Klasik dağıtım modeliyle oluşturulan kaynaklara atanamaz. Azure dağıtım modelleri hakkında daha fazla bilgi edinmek için [dağıtım modellerini anlama](../resource-manager-deployment-model.md) makalesi.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Birden çok IP adresi ile VM oluşturma

Birden çok IP adresi veya bir statik özel IP adresi ile bir VM oluşturmak istiyorsanız, PowerShell veya Azure CLI kullanarak oluşturmanız gerekir. Bilgi edinmek için nasıl, bu makalenin üst kısmındaki PowerShell veya CLI Seçenekleri'ni tıklatın. Tek bir dinamik özel IP adresi ve (isteğe bağlı) tek bir genel IP adresi ile bir VM oluşturabilirsiniz. Portalı'ndaki adımları izleyerek kullanmak [Windows VM oluşturma](../virtual-machines/virtual-machines-windows-hero-tutorial.md) veya [bir Linux VM oluşturma](../virtual-machines/linux/quick-create-portal.md) makaleler. VM oluşturduktan sonra IP adresi türü dinamik olan statik olarak değiştirin ve aşağıdaki adımlarda portalı kullanarak ek IP adreslerini ekleyin [ekleme IP adresleri için bir VM](#add) bu makalenin.

## <a name="add"></a>Bir VM'ye IP adresleri ekleme

Aşağıdaki adımları izleyerek, özel ve genel IP adresleri için bir Azure ağ arabirimi ekleyebilirsiniz. Aşağıdaki bölümlerde örneklerde, bir VM içinde açıklanan üç IP yapılandırmaları ile sahip olduğunuz varsayılmaktadır [senaryo](#scenario), ancak gerekli değildir.

### <a name="coreadd"></a>Çekirdek adımları

1. Adresinden Azure portalında Gözat https://portal.azure.com ve oturum açın, gerekirse.
2. Portalında **diğer hizmetler** > türü *sanal makineler* 'a tıklayın ve filtre kutusuna **sanal makineler**.
3. İçinde **sanal makineler** bölmesinde, IP eklemek istediğiniz VM adreslerinin tıklayın. Tıklayın **ağ arabirimleri** bölmesi görünür ve ardından ağ arabirimi IP eklemek istediğiniz sanal makineyi adresleri. Aşağıdaki resimde gösterilen örnekte, NIC adlı *Mynıc* adlı VM'den *myVM* seçilir:

    ![Ağ arabirimi](./media/virtual-network-multiple-ip-addresses-portal/figure1.png)

4. Seçtiğiniz NIC için görüntülenen bölmesinde **IP yapılandırmaları**.

Eklemek istediğiniz IP adresi türüne göre aşağıdaki bölümlerde birindeki adımları tamamlayın.

### <a name="add-a-private-ip-address"></a>**Özel bir IP adresi Ekle**

Yeni bir özel IP adresi eklemek için aşağıdaki adımları tamamlayın:

1. Bölümündeki adımları tamamlamanız [çekirdek adımları](#coreadd) bu makalenin.
2.           **Ekle**'yi tıklatın. İçinde **ekleme IP yapılandırması** görüntülenen bölmesi adlı IP yapılandırması oluşturun *IPConfig 4* ile *10.0.0.7* olarak bir *statik* Özel IP adresini'a tıklayın **Tamam**.

    > [!NOTE]
    > Statik bir IP adresi eklerken, NIC'nin bağlı olduğu alt ağdaki kullanılmayan, geçerli bir adresi belirtmeniz gerekir. Seçtiğiniz adresi kullanılabilir değilse, portal IP adresi için bir X görüntüler ve farklı bir seçmeniz gerekir.

3. Tamam'a tıkladığınızda, bölmeyi kapatır ve yeni IP yapılandırması listede görürsünüz. Tıklayın **Tamam** kapatmak için **ekleme IP yapılandırması** bölmesi.
4. Tıklayabilirsiniz **Ekle** ek IP yapılandırmaları ekleyin ya da IP adresleri ekleme işlemini sonlandırmak için açık olan tüm dikey pencereleri kapatın.
5. İçindeki adımları tamamlayarak VM işletim sistemine özel IP adresleri ekleme [ekleme IP adresleri için bir VM işletim sistemi](#os-config) bu makalenin.

### <a name="add-a-public-ip-address"></a>Genel IP adresi ekleme

Genel bir IP adresi, yeni bir IP yapılandırması veya mevcut bir IP yapılandırması için genel bir IP adresi kaynağı ilişkilendirerek eklenir.

> [!NOTE]
> Genel IP adreslerinin nominal bir ücreti vardır. IP adresi fiyatlandırması hakkında daha fazla bilgi edinmek için [IP adresi fiyatlandırması](https://azure.microsoft.com/pricing/details/ip-addresses) sayfası. Bir abonelikte kullanılan genel IP adresleri sayısına bir sınır yoktur. Sınırlar hakkında daha fazla bilgi için [Azure limitleri](../azure-subscription-service-limits.md#networking-limits) makalesini okuyun.
> 

### <a name="create-public-ip"></a>Genel bir IP adresi kaynağı oluşturun

Genel bir IP adresi, bir genel IP adresi kaynağı için bir ayardır. Şu anda ilişkili bir IP yapılandırmasına ilişkilendirmek istediğiniz bir IP yapılandırmasına değil genel bir IP adresi kaynağı varsa, aşağıdaki adımları atlayın ve gereksinim duyduğunuz kadar aşağıdaki bölümlerde birindeki adımları tamamlayın. Kullanılabilir bir genel IP adresi kaynağına sahip değilseniz, oluşturmak için aşağıdaki adımları tamamlayın:

1. Adresinden Azure portalında Gözat https://portal.azure.com ve oturum açın, gerekirse.
3. Portalında **kaynak Oluştur** > **ağ** > **genel IP adresi**.
4. İçinde **genel IP adresi oluşturma** görüntülenen bölmesi girin bir **adı**seçin bir **IP adresi ataması** türü, bir **abonelik**, **Kaynak grubu**ve **konumu**, ardından **Oluştur**, aşağıdaki resimde gösterildiği gibi:

    ![Genel bir IP adresi kaynağı oluşturun](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Bir IP yapılandırması için genel IP adresi kaynağı ilişkilendirmek için aşağıdaki bölümlerde birindeki adımları tamamlayın.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Genel IP adresi kaynağı yeni bir IP yapılandırmasını ilişkilendirin

1. Bölümündeki adımları tamamlamanız [çekirdek adımları](#coreadd) bu makalenin.
2.           **Ekle**'yi tıklatın. İçinde **ekleme IP yapılandırması** görüntülenen bölmesi adlı IP yapılandırması oluşturun *IPConfig 4*. Etkinleştirme **genel IP adresi** bir var olan ve kullanılabilir genel IP adresi kaynağına nden seçip **genel IP adresi seçin** bölmesi görünür.

    Genel IP adresi kaynağı seçtikten sonra tıklayın **Tamam** ve bölmeyi kapatır. Var olan bir genel IP adresi yoksa, bir adımları tamamlayarak oluşturabilirsiniz [genel bir IP adresi kaynağı oluşturun](#create-public-ip) bu makalenin. 

3. Yeni IP yapılandırmasını gözden geçirin. Özel bir IP adresi açıkça atanmış durumda olsa da, tüm IP yapılandırmaları özel bir IP adresi olması gerektiğinden bir otomatik olarak IP yapılandırması için atandı.
4. Tıklayabilirsiniz **Ekle** ek IP yapılandırmaları ekleyin ya da IP adresleri ekleme işlemini sonlandırmak için açık olan tüm dikey pencereleri kapatın.
5. İşletim sisteminiz için adımları tamamlayarak VM işletim sistemine özel IP adresini ekleyin [ekleme IP adresleri için bir VM işletim sistemi](#os-config) bu makalenin. İşletim sistemi için genel IP adresini eklemeyin.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>Genel IP adresi kaynağı var olan bir IP yapılandırmasını ilişkilendirin

1. Bölümündeki adımları tamamlamanız [çekirdek adımları](#coreadd) bu makalenin.
2. Genel IP adresi kaynağına eklemek istediğiniz IP Yapılandırması'nı tıklatın.
3. Görüntülenen IPConfig bölmesinden **IP adresi**.
4. İçinde **genel IP adresi seçin** görüntülenen bölmesinde bir genel IP adresi seçin.
5. Tıklayın **Kaydet** bölmeleri kapatın. Var olan bir genel IP adresi yoksa, bir adımları tamamlayarak oluşturabilirsiniz [genel bir IP adresi kaynağı oluşturun](#create-public-ip) bu makalenin.
3. Yeni IP yapılandırmasını gözden geçirin.
4. Tıklayabilirsiniz **Ekle** ek IP yapılandırmaları ekleyin ya da IP adresleri ekleme işlemini sonlandırmak için açık olan tüm dikey pencereleri kapatın. İşletim sistemi için genel IP adresini eklemeyin.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
