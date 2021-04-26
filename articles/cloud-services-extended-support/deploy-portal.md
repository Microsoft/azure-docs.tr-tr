---
title: Azure bulut hizmeti dağıtma (genişletilmiş destek)-Azure portal
description: Azure portal kullanarak bir Azure bulut hizmeti (genişletilmiş destek) dağıtma
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 306294cc654e46dbe8af80b25cb9c709071fad20
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166335"
---
# <a name="deploy-a-azure-cloud-services-extended-support-using-the-azure-portal"></a>Azure portal kullanarak bir Azure Cloud Services (genişletilmiş destek) dağıtma
Bu makalede, bulut hizmeti (genişletilmiş destek) dağıtımı oluşturmak için Azure portal nasıl kullanılacağı açıklanmaktadır. 

## <a name="before-you-begin"></a>Başlamadan önce

Cloud Services (genişletilmiş destek) için [dağıtım önkoşullarını](deploy-prerequisite.md) gözden geçirin ve ilişkili kaynakları oluşturun. 

## <a name="deploy-a-cloud-services-extended-support"></a>Cloud Services Dağıtma (genişletilmiş destek) 
1. [Azure portalda](https://portal.azure.com) oturum açma

2.  Azure portal üst kısmında bulunan arama çubuğunu kullanarak, **Cloud Services (genişletilmiş destek)** öğesini arayın ve seçin.

    :::image type="content" source="media/deploy-portal-1.png" alt-text="Görüntü, Azure portal tüm kaynaklar dikey penceresini gösterir.":::
 
3.  Cloud Services (genişletilmiş destek) bölmesinde **Oluştur**' u seçin. 

    :::image type="content" source="media/deploy-portal-2.png" alt-text="Görüntü, Market 'ten bir bulut hizmeti satın almayı gösterir.":::

4. Cloud Services (genişletilmiş destek) oluşturma penceresi **temel bilgiler** sekmesinde açılır. 
    - Bir abonelik seçin.
    - Bir kaynak grubu seçin veya yeni bir tane oluşturun.
    - Bulut hizmetiniz (genişletilmiş destek) dağıtımı için istenen adı girin.
        - Bulut hizmetinin DNS adı ayrı ve genel IP adresinin DNS adı etiketiyle belirtilir ve yapılandırma sekmesindeki Genel IP bölümünde değiştirilebilir.
    -  Dağıtım yapılacak bölgeyi seçin.

    :::image type="content" source="media/deploy-portal-3.png" alt-text="Görüntü Cloud Services (genişletilmiş destek) giriş dikey penceresini gösterir.":::

5. Bulut hizmeti yapılandırmanızı, paket ve tanım dosyalarınızı ekleyin. Blob depolamadan var olan dosyaları ekleyebilir veya bunları yerel makinenizden karşıya yükleyebilirsiniz. Yerel makinenizden karşıya yükleme yapıyorsanız, bunlar bir depolama hesabında depolanır. 

    :::image type="content" source="media/deploy-portal-4.png" alt-text="Görüntü oluşturma sırasında temel bilgiler sekmesinin karşıya yükleme bölümünü gösterir.":::

6. Tüm alanlar tamamlandığında, ' a gidin ve **yapılandırma** sekmesini doldurun. 
    - Bulut hizmeti ile ilişkilendirilecek bir sanal ağ seçin veya yeni bir tane oluşturun. 
        - Bulut hizmeti (genişletilmiş destek **) dağıtımları bir sanal ağda olmalıdır.** Sanal ağa Ayrıca, bölümü altındaki hizmet yapılandırma (. cscfg) dosyasında da **başvurulmalıdır** `NetworkConfiguration` .
    - Bulut hizmeti ile ilişkilendirilecek mevcut bir genel IP adresi seçin veya yeni bir tane oluşturun.
        - Hizmet tanımı (. csdef) dosyanızda tanımlanmış **IP giriş uç noktalarınız** varsa, bulut hizmetiniz için genel bir IP adresi oluşturulması gerekecektir. 
        - Cloud Services (genişletilmiş destek) yalnızca temel IP adresi SKU 'sunu destekler.
        - Hizmet yapılandırmanız (. cscfg) ayrılmış bir IP adresi içeriyorsa, genel IP 'nin ayırma türü TP **statik** olarak ayarlanmalıdır. 
        - İsteğe bağlı olarak, bulut hizmeti ile ilişkili genel IP adresinin DNS etiketi özelliğini güncelleştirerek bulut hizmeti uç noktanız için bir DNS adı atayın.  
    - Seçim Bulut hizmetini başlatın. Oluşturmadan hemen sonra Başlat ' ı veya hizmeti Başlat ' ı seçin.
    - Bir Key Vault seçin 
        - Hizmet yapılandırma (. cscfg) dosyanızda bir veya daha fazla sertifika belirttiğinizde Key Vault gereklidir. Bir Anahtar Kasası seçtiğinizde, Seçili sertifikaları, parmak izlerini temel alarak hizmet yapılandırma (. cscfg) dosyasından bulmayı deneyeceğiz. Anahtar kasasında herhangi bir sertifika yoksa, şimdi karşıya yükleyebilir ve **Yenile**' ye tıklayabilirsiniz.   

 :::image type="content" source="media/deploy-portal-5.png" alt-text="Görüntü Cloud Services oluştururken Azure portal yapılandırma dikey penceresini gösterir (genişletilmiş destek).":::

7. Tüm alanlar tamamlandıktan sonra, dağıtım yapılandırmanızı doğrulamak ve bulut hizmetinizi (genişletilmiş destek) oluşturmak için **İnceleme ve oluşturma** sekmesine geçin.

## <a name="next-steps"></a>Sonraki adımlar 
- Cloud Services için [sık sorulan soruları](faq.md) gözden geçirin (genişletilmiş destek).
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [şablon](deploy-template.md) veya [Visual Studio](deploy-visual-studio.md)kullanarak bir bulut hizmeti (genişletilmiş destek) dağıtın.
- [Cloud Services (genişletilmiş destek) örnekleri deposunu](https://github.com/Azure-Samples/cloud-services-extended-support) ziyaret edin
