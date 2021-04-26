---
title: Hızlı başlangıç-Azure portal sanal makine ölçek kümesi oluşturma
description: Hızlı bir şekilde sanal makine oluşturmayı öğrenerek dağıtımlarınızla çalışmaya başlayın Azure portal.
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.date: 06/30/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 4ed0932e82751a5ded4bfc9d234bfcd16fa5b9e0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935456"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-in-the-azure-portal"></a>Hızlı Başlangıç: Azure portalında sanal makine ölçek kümesi oluşturma

Bir sanal makine ölçek kümesi, otomatik ölçeklendirme sanal makineleri kümesini dağıtmanıza ve yönetmenize olanak tanır. Ölçek kümesi içindeki sanal makine sayısını el ile ölçeklendirebilir veya CPU, bellek talebi ya da ağ trafiği gibi kaynak kullanımını temel alan otomatik ölçeklendirme kuralları tanımlayabilirsiniz. Azure Load Balancer daha sonra ölçek kümesindeki sanal makine örneklerine trafiği dağıtır. Bu hızlı başlangıçta, Azure portalında sanal makine ölçek kümesi oluşturacaksınız.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.


## <a name="log-in-to-azure"></a>Azure'da oturum açma
https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-a-load-balancer"></a>Yük dengeleyici oluşturma

Azure [yük dengeleyici](../load-balancer/load-balancer-overview.md) , gelen trafiği sağlıklı sanal makine örnekleri arasında dağıtır. 

İlk olarak, portalı kullanarak bir ortak Standart Load Balancer oluşturun. Oluşturduğunuz ad ve genel IP adresi, yük dengeleyicinin ön ucu olarak otomatik olarak yapılandırılır.

1. Arama kutusuna **yük dengeleyici** yazın. Arama sonuçlarında **Market** ' ın altında **yük dengeleyici**' yi seçin.
1. **Yük dengeleyici oluştur** sayfasının **temel bilgiler** sekmesinde, aşağıdaki bilgileri girin veya seçin:

    | Ayar                 | Değer   |
    | ---| ---|
    | Abonelik  | Aboneliğinizi seçin.    |    
    | Kaynak grubu | **Yeni oluştur** ' u seçin ve metin kutusuna *Myvmssresourcegroup* yazın.|
    | Name           | *myLoadBalancer*         |
    | Region         | **Doğu ABD**’yi seçin.       |
    | Tür          | **Genel**’i seçin.       |
    | SKU           | **Standart**' ı seçin.       |
    | Genel IP adresi | **Yeni oluştur**’u seçin. |
    | Genel IP adresi adı  | *Mypıp*   |
    | Atama| Statik |
    | Kullanılabilirlik alanı | **Bölge yedekli** seçeneğini belirleyin. |

1. İşiniz bittiğinde **gözden geçir + oluştur** ' u seçin. 
1. Doğrulama başarılı olduktan sonra **Oluştur**' u seçin. 

![Yük dengeleyici oluşturma](./media/virtual-machine-scale-sets-create-portal/load-balancer.png)

## <a name="create-virtual-machine-scale-set"></a>Sanal makine ölçek kümesi oluşturma
RHEL, CentOS, Ubuntu veya SLES gibi bir Linux görüntüsü ya da Windows Server görüntüsü ile ölçek kümesi dağıtabilirsiniz.

1. Arama kutusuna **Ölçek kümesi** yazın. Sonuçlarda, **Market** altında **Sanal Makine Ölçek Kümeleri**' ni seçin. Sanal makine ölçek **kümeleri** Oluştur sayfasında **Oluştur** ' u seçerek **sanal makine ölçek kümesi oluşturma** sayfasını açarsınız. 
1. **Temel bilgiler** sekmesinde, **proje ayrıntıları**' nın altında, doğru aboneliğin seçildiğinden emin olun ve kaynak grubu listesinden *myvmssresourcegroup* ' yi seçin. 
1. Ölçek kümesi için ad olarak *myScaleSet* yazın.
1. **Bölge**' de, bulunduğunuz alana yakın bir bölge seçin.
1. **Görüntü** için bir market görüntüsü seçin. Bu örnekte, *Ubuntu Server 18,04 LTS*' yi seçtik.
1. İstediğiniz kullanıcı adını girin ve tercih ettiğiniz kimlik doğrulaması türünü seçin.
   - **Parola** en az 12 karakter uzunluğunda olmalı ve şu dört karmaşıklık gereksiniminden üçünü karşılamalıdır: bir küçük harf karakter, bir büyük harf karakter, bir sayı ve bir özel karakter. Daha fazla bilgi için [kullanıcı adı ve parola gereksinimlerine](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) bakın.
   - Bir Linux OS disk görüntüsü seçerseniz bunun yerine **SSH genel anahtarını** seçebilirsiniz. Yalnızca genel anahtarınızı sağlayın (örn. *~/.ssh/id_rsa.pub*). [SSH anahtarları oluşturmak ve kullanmak](../virtual-machines/linux/mac-create-ssh-keys.md) için portaldan Azure Cloud Shell’i kullanabilirsiniz.
   
    :::image type="content" source="./media/virtual-machine-scale-sets-create-portal/quick-create-scale-set.png" alt-text="Görüntü, Azure portal ölçek kümeleri için oluşturma seçeneklerini gösterir.":::

1. Diğer sayfaları taşımak için **İleri ' yi** seçin. 
1. **Örnek** ve **disk** sayfaları için varsayılan değerleri bırakın.
1. **Ağ** sayfasında, **Yük Dengeleme** altında, ölçek kümesi örneklerini bir yük dengeleyicinin arkasına koymak için **Evet** ' i seçin. 
1. **Yük dengeleme seçenekleri**' nde **Azure yük dengeleyici**' ni seçin.
1. **Yük dengeleyici seçin**' de, daha önce oluşturduğunuz *myloadbalancer* ' ı seçin.
1. **Bir arka uç havuzu seçin** Için **Yeni oluştur**' u seçin, *Mybackendpool* yazın ve **Oluştur**' u seçin.
1. İşiniz bittiğinde, **gözden geçir + oluştur**' u seçin. 
1. Doğrulama başarılı olduktan sonra, ölçek kümesini dağıtmak için **Oluştur** ' u seçin.


## <a name="clean-up-resources"></a>Kaynakları temizleme
Artık gerekli olmadığında kaynak grubunu, ölçek kümesini ve tüm ilgili kaynakları silin. Bunu yapmak için ölçek kümesi için kaynak grubunu seçin ve **Sil**' i seçin.


## <a name="next-steps"></a>Sonraki adımlar
Bu hızlı başlangıçta, Azure portalında temel bir ölçek kümesi oluşturdunuz. Daha fazla bilgi edinmek için Azure sanal makine ölçek kümelerinin nasıl oluşturulacağı ve yönetileceğine ilişkin öğreticiyle devam edin.

> [!div class="nextstepaction"]
> [Azure sanal makine ölçek kümeleri oluşturma ve yönetme](tutorial-create-and-manage-powershell.md)
