---
title: 'Hızlı başlangıç: Azure portal kullanarak rota sunucusu oluşturma ve yapılandırma'
description: Bu hızlı başlangıçta, Azure portal kullanarak bir rota sunucusu oluşturma ve yapılandırma hakkında bilgi edineceksiniz.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/03/2021
ms.author: duau
ms.openlocfilehash: f76c48af4f5ebc8013daad457f9973cf7792c7c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548010"
---
# <a name="quickstart-create-and-configure-route-server-using-the-azure-portal"></a>Hızlı başlangıç: Azure portal kullanarak rota sunucusu oluşturma ve yapılandırma

Bu makale, Azure yol sunucusunu Azure portal kullanarak sanal ağınızdaki bir ağ sanal gereci (NVA) ile eşler arası yapılandırmanıza yardımcı olur. Azure yol sunucusu, NVA 'dan yolları öğrenmekte ve sanal ağdaki sanal makinelerde bunları programalacak. Azure yol sunucusu, NVA 'ya sanal ağ yollarını da duyuracaktır. Daha fazla bilgi için [Azure Route sunucusu](overview.md)' nu okuyun.

> [!IMPORTANT]
> Azure yol sunucusu (Önizleme) Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Azure Route sunucusu için hizmet sınırlarını](route-server-faq.md#limitations)gözden geçirin.

## <a name="create-a-route-server"></a>Rota sunucusu oluşturma

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Azure hesabınızda oturum açın ve aboneliğinizi seçin.

Bir tarayıcıdan [Azure portalına](https://portal.azure.com) gidin ve Azure hesabınızla oturum açın.

### <a name="create-a-route-server"></a>Rota sunucusu oluşturma

1. https://aka.ms/routeserver öğesine gidin.

1. **+ Yeni rota sunucusu oluştur**' u seçin.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-landing-page.png" alt-text="Rota sunucusu giriş sayfasının ekran görüntüsü."::: 

1. **Yol sunucusu oluşturma** sayfasında, gerekli bilgileri girin veya seçin.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/create-route-server-page.png" alt-text="Yol sunucusu oluşturma sayfasının ekran görüntüsü.":::     

    | Ayarlar | Değer |
    |----------|-------|
    | Abonelik | Yönlendirme sunucusunu dağıtmak için kullanmak istediğiniz Azure aboneliğini seçin. |
    | Kaynak grubu | İçindeki rota sunucusunu oluşturmak için bir kaynak grubu seçin. Mevcut bir kaynak grubunuz yoksa yeni bir tane oluşturabilirsiniz. |
    | Name | Rota sunucusu için bir ad girin. |
    | Region | Yol sunucusunun oluşturulacağı bölgeyi seçin. Açılan kutuda sanal ağı görmek için daha önce oluşturduğunuz sanal ağla aynı bölgeyi seçin. |
    | Sanal Ağ | Yol sunucusunun oluşturulacağı sanal ağı seçin. Yeni bir sanal ağ oluşturabilir veya var olan bir sanal ağ kullanabilirsiniz. Var olan bir sanal ağı kullanıyorsanız, mevcut sanal ağın en az bir/27 alt ağı için yol sunucusu alt ağ gereksinimini karşılayacak yeterli alana sahip olduğundan emin olun. Sanal ağınızı açılan listeden görmüyorsanız, doğru kaynak grubunu veya bölgeyi seçtiğinizden emin olun. |
    | Alt ağ | Bir sanal ağ oluşturduktan veya seçtikten sonra alt ağ alanı görüntülenir. Bu alt ağ yalnızca rota sunucusu için ayrılmıştır. **Alt ağ yapılandırmasını Yönet** ' i seçin ve Azure rota sunucusu alt ağını oluşturun. **+ Alt ağ** ' ı seçin ve aşağıdaki yönergeleri kullanarak bir alt ağ oluşturun:</br><br>-Alt ağ, *Routeserversubnet* olarak adlandırılmalıdır.</br><br>-Alt ağ en az/27 veya daha büyük olmalıdır.</br> |

1. **Gözden geçir + oluştur**' u seçin, Özeti gözden geçirin ve ardından **Oluştur**' u seçin. 

    > [!NOTE]
    > Yol sunucusunun dağıtımı yaklaşık 20 dakika sürer.

## <a name="set-up-peering-with-nva"></a>NVA ile eşlemeyi ayarlama

Bu bölüm, NVA ile BGP eşlemesini yapılandırmanıza yardımcı olur.

1. Azure portal [yol sunucusuna](https://aka.ms/routeserver) gidin ve yapılandırmak Istediğiniz rota sunucusunu seçin.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/select-route-server.png" alt-text="Rota sunucusu listesinin ekran görüntüsü."::: 

1. Sol gezinti panelinde *Ayarlar* ' ın altında bulunan **Peers** ' ı seçin. Ardından yeni bir eş eklemek için **+ Ekle** ' yi seçin.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/peers-landing-page.png" alt-text="Eş giriş sayfasının ekran görüntüsü."::: 

1. NVA eşbilgileriniz hakkında aşağıdaki bilgileri girin.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/add-peer-page.png" alt-text="Eş ekleme sayfasının ekran görüntüsü.":::

    | Ayarlar | Değer |
    |----------|-------|
    | Ad | Yönlendirme sunucunuz ve NVA arasında eşleme için bir ad verin. |
    | ASN |  NVA 'nizin otonom sistem numarası 'nı (ASN) girin. |
    | IPv4 adresi | Yol sunucusunun BGP oluşturmak için iletişim kurması için kullanacağı NVA 'nin IP adresini girin. |

1. Bu eşi eklemek için **Ekle** ' yi seçin.

## <a name="complete-the-configuration-on-the-nva"></a>NVA üzerinde yapılandırmayı doldurun

Bir BGP oturumu oluşturmak için NVA 'daki yapılandırmayı tamamlayabilmeniz üzere Azure Route sunucusunun eşi IP 'Leri ve ASN 'sine ihtiyacınız vardır. Bu bilgileri, yönlendirme sunucunuzdaki genel bakış sayfasından edinebilirsiniz.

:::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-overview.png" alt-text="Yönlendirme sunucusuna Genel Bakış sayfasının ekran görüntüsü.":::

## <a name="configure-route-exchange"></a>Rota değişimini yapılandırma

Bir ExpressRoute Gateway ve/veya VPN Gateway varsa ve yönlendirme sunucusu ile yolların değiş tokuşmasını istiyorsanız, yönlendirme değişimi 'ni etkinleştirebilirsiniz.

1. Azure portal [yol sunucusuna](https://aka.ms/routeserver) gidin ve yapılandırmak Istediğiniz rota sunucusunu seçin.

1. Sol gezinti panelinde *Ayarlar* ' ın altında **yapılandırma** ' yı seçin.

1. **Dal dalı** ayarı için **Etkinleştir** ' i seçin ve ardından **Kaydet**' i seçin.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/enable-route-exchange.png" alt-text="Yol değişimini etkinleştirme ekran görüntüsü.":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Azure Route sunucusuna artık ihtiyacınız yoksa, yol sunucusunun sağlamasını kaldırmak için genel bakış sayfasından **Sil** ' i seçin.

:::image type="content" source="./media/quickstart-configure-route-server-portal/delete-route-server.png" alt-text="Yol sunucusunun nasıl silineceği ekran görüntüsü.":::

## <a name="next-steps"></a>Sonraki adımlar

Azure Route sunucusu oluşturduktan sonra Azure Route sunucusunun ExpressRoute ve VPN ağ geçitleri ile nasıl etkileşime gireceğini öğrenin: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute ve Azure VPN desteği](expressroute-vpn-support.md)
