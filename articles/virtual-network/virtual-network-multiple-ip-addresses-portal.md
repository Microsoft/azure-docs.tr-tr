---
title: Azure sanal makineler için birden çok IP adresi-Portal | Microsoft Docs
description: Azure portal kullanarak bir sanal makineye birden çok IP adresi atamayı öğrenin | Kaynak Yöneticisi.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: allensu
ms.openlocfilehash: 050d3ac23562e7822d186a16675d03c1b9dc670b
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739215"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Azure portal kullanarak sanal makinelere birden çok IP adresi atama

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> Bu makalede, Azure portal kullanılarak Azure Resource Manager dağıtım modeli aracılığıyla bir sanal makinenin (VM) nasıl oluşturulacağı açıklanmaktadır. Klasik dağıtım modeliyle oluşturulan kaynaklara birden çok IP adresi atanamaz. Azure dağıtım modelleri hakkında daha fazla bilgi edinmek için [dağıtım modellerini anlama](../azure-resource-manager/management/deployment-models.md) makalesini okuyun.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Birden çok IP adresi ile VM oluşturma

Birden çok IP adresi veya statik bir özel IP adresi olan bir VM oluşturmak istiyorsanız, PowerShell veya Azure CLı kullanarak oluşturmanız gerekir. Nasıl yapılacağını öğrenmek için, bu makalenin en üstündeki PowerShell veya CLı seçeneklerine tıklayın. Tek bir dinamik özel IP adresi ve (isteğe bağlı olarak) tek bir genel IP adresi olan bir VM oluşturabilirsiniz. [WINDOWS VM oluşturma](../virtual-machines/windows/quick-create-portal.md) veya [Linux VM oluşturma](../virtual-machines/linux/quick-create-portal.md) makalelerinde bulunan adımları izleyerek portalı kullanın. VM 'yi oluşturduktan sonra, bu makalenin [VM 'ye IP adresleri ekleme](#add) bölümündeki adımları IZLEYEREK, IP adresi türünü dinamik iken statik olarak değiştirebilir ve portalı kullanarak ek IP adresleri ekleyebilirsiniz.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>VM 'ye IP adresleri ekleme

Aşağıdaki adımları tamamlayarak bir Azure ağ arabirimine özel ve genel IP adresleri ekleyebilirsiniz. Aşağıdaki bölümlerde yer alan örneklerde, [senaryosunda](#scenario)AÇıKLANAN üç IP yapılandırmasına sahıp bir VM zaten var, ancak bu gerekli değildir.

### <a name="core-steps"></a><a name="coreadd"></a>Temel adımlar

1. Azure portal gidin https://portal.azure.com ve gerekirse oturum açın.
2. Portalda, **daha fazla hizmet** ' e tıklayın > filtre kutusuna *sanal makineler* yazın ve ardından **sanal makineler**' e tıklayın.
3. **Sanal makineler** BÖLMESINDE, IP adreslerini eklemek istediğiniz VM 'ye tıklayın. **Ağ** sekmesine gidin. sayfada **ağ arabirimi** ' ne tıklayın. Aşağıdaki resimde gösterildiği gibi: 


    ![VM’ye genel IP adresi ekleme](./media/virtual-network-multiple-ip-addresses-portal/figure200319.png)
4. **Ağ arabirimi** bölmesinde, **IP yapılandırmalarına** tıklayın.

5. Seçtiğiniz NIC için görüntülenen bölmede, **IP yapılandırması**' na tıklayın. **Ekle**' ye tıklayın, eklemek istediğiniz IP adresi türüne bağlı olarak, aşağıdaki bölümlerden birindeki adımları tamamlayın ve ardından **Tamam**' a tıklayın. 

### <a name="add-a-private-ip-address"></a>Özel IP adresi ekle

Yeni bir özel IP adresi eklemek için aşağıdaki adımları izleyin:

1. Bu makalenin [temel adımlar](#coreadd) bölümündeki ADıMLARı tamamlayarak VM ağ arabiriminin **IP konfigürasyonları** bölümünde olduğunuzdan emin olun.  Varsayılan olarak gösterilen alt ağı (10.0.0.0/24 gibi) gözden geçirin.
2. **Ekle**'ye tıklayın. Görüntülenen **IP Yapılandırması Ekle** bölmesinde, son sekizli için yeni bir sayı seçerek yeni bir *statik* özel IP adresi ile *IPCONFIG-4* adlı bir IP yapılandırması oluşturun ve ardından **Tamam**' a tıklayın.  (10.0.0.0/24 alt ağı için örnek bir IP *kısmına 10.0.0.7* olacaktır.)

    > [!NOTE]
    > Statik bir IP adresi eklerken, NIC 'nin bağlı olduğu alt ağda kullanılmamış, geçerli bir adres belirtmeniz gerekir. Seçtiğiniz adres yoksa, Portal IP adresi için bir X görüntüler ve farklı bir tane seçmeniz gerekir.

3. Tamam ' a tıkladığınızda, bölmesi kapanır ve yeni IP yapılandırmasının listelendiğini görürsünüz. **IP Yapılandırması Ekle** bölmesini kapatmak için **Tamam** ' ı tıklatın.
4. Ek IP yapılandırması eklemek için **Ekle** ' ye TıKLAYABILIR veya IP adresleri ekleme işleminin tamamlanabilmesi için tüm açık kanları kapatabilirsiniz.
5. Bu makalenin [BIR VM işletim SISTEMINE IP adresleri ekleme](#os-config) bölümündeki ADıMLARı tamamlayarak VM işletim SISTEMINE özel IP adreslerini ekleyin.

### <a name="add-a-public-ip-address"></a>Genel IP adresi ekleme

Genel IP adresi kaynağı yeni bir IP yapılandırmasına veya var olan bir IP yapılandırmasına ilişkilendirerek genel bir IP adresi eklenir.

> [!NOTE]
> Genel IP adreslerinin nominal bir ücreti vardır. IP adresi fiyatlandırması hakkında daha fazla bilgi edinmek için [IP adresi fiyatlandırma](https://azure.microsoft.com/pricing/details/ip-addresses) sayfasını okuyun. Bir abonelikte kullanılabilecek genel IP adresi sayısı için bir sınır vardır. Sınırlar hakkında daha fazla bilgi için [Azure limitleri](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) makalesini okuyun.
> 

### <a name="create-a-public-ip-address-resource"></a><a name="create-public-ip"></a>Genel IP adresi kaynağı oluşturma

Genel IP adresi, genel IP adresi kaynağı için bir ayardır. IP yapılandırması ile ilişkilendirmek istediğiniz bir IP yapılandırması ile ilişkili olmayan bir genel IP adresi kaynağınız varsa, aşağıdaki adımları atlayın ve gerekli olan bölümlerden birindeki adımları tamamlayın. Kullanılabilir bir genel IP adresi kaynağınız yoksa, bir tane oluşturmak için aşağıdaki adımları izleyin:

1. Azure portal gidin https://portal.azure.com ve gerekirse oturum açın.
3. Portalda **kaynak**  >  **ağı**  >  **genel IP adresi** oluştur ' a tıklayın.
4. Görüntülenen **genel IP adresi oluştur** bölmesinde, bir **ad** girin, bir **IP adresi atama** türü, **abonelik**, **kaynak grubu** ve **konum** seçin ve ardından aşağıdaki resimde gösterildiği gibi **Oluştur**' a tıklayın:

    ![Genel IP adresi kaynağı oluşturma](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Genel IP adresi kaynağını bir IP yapılandırmasıyla ilişkilendirmek için aşağıdaki bölümlerden birindeki adımları tamamlayın.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Genel IP adresi kaynağını yeni bir IP yapılandırmasıyla ilişkilendir

1. Bu makalenin [temel adımlar](#coreadd) bölümündeki adımları uygulayın.
2. **Ekle**'ye tıklayın. Görüntülenen **IP Yapılandırması Ekle** bölmesinde *Ipconfig-4* adlı bir IP yapılandırması oluşturun. **Genel IP adresini** etkinleştirin ve görüntülenen **ortak IP adresini seçin** bölmesinden mevcut, kullanılabilir bir genel IP adresi kaynağı seçin.

    Genel IP adresi kaynağını seçtikten sonra, **Tamam** ' a tıklayın ve bölmesi kapanır. Mevcut bir genel IP adresiniz yoksa, bu makalenin [genel IP adresi kaynağı oluşturma](#create-public-ip) bölümündeki adımları tamamlayarak bir tane oluşturabilirsiniz. 

3. Yeni IP yapılandırmasını gözden geçirin. Özel bir IP adresi açıkça atanmamış olsa da, tüm IP yapılandırmalarının özel bir IP adresi olması gerektiğinden, IP yapılandırmasına otomatik olarak bir tane atanır.
4. Ek IP yapılandırması eklemek için **Ekle** ' ye TıKLAYABILIR veya IP adresleri ekleme işleminin tamamlanabilmesi için tüm açık kanları kapatabilirsiniz.
5. Bu makalenin [BIR VM işletim SISTEMINE IP adresleri Ekle](#os-config) bölümünde işletim sisteminizin ADıMLARıNı tamamlayarak VM işletim SISTEMINE özel IP adresini ekleyin. Genel IP adresini işletim sistemine eklemeyin.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>Genel IP adresi kaynağını mevcut bir IP yapılandırmasıyla ilişkilendir

1. Bu makalenin [temel adımlar](#coreadd) bölümündeki adımları uygulayın.
2. Genel IP adresi kaynağını eklemek istediğiniz IP yapılandırmasına tıklayın.
3. Görüntülenen IPConfig bölmesinde **IP adresi**' ne tıklayın.
4. Görüntülenen **ortak IP adresi seçin** bölmesinde genel bir IP adresi seçin.
5. **Kaydet** ' e tıklayın ve bölmeler kapanır. Mevcut bir genel IP adresiniz yoksa, bu makalenin [genel IP adresi kaynağı oluşturma](#create-public-ip) bölümündeki adımları tamamlayarak bir tane oluşturabilirsiniz.
3. Yeni IP yapılandırmasını gözden geçirin.
4. Ek IP yapılandırması eklemek için **Ekle** ' ye TıKLAYABILIR veya IP adresleri ekleme işleminin tamamlanabilmesi için tüm açık kanları kapatabilirsiniz. Genel IP adresini işletim sistemine eklemeyin.

> [!NOTE]
> IP adresi yapılandırmasını değiştirdikten sonra değişikliklerin VM 'de etkili olması için VM 'yi yeniden başlatmanız gerekir.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
