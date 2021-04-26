---
title: 'Öğretici: Azure savunma Konağı oluşturma: Windows VM: Portal'
description: Bu makalede, bir Azure savunma ana bilgisayarı oluşturmayı ve bir Windows VM 'ye bağlanmayı öğreneceksiniz.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: tutorial
ms.date: 02/12/2021
ms.author: cherylmc
ms.openlocfilehash: 3b365e347802824e855797afb8c68e5249bf0adb
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579632"
---
# <a name="tutorial-configure-bastion-and-connect-to-a-windows-vm-through-a-browser"></a>Öğretici: bir tarayıcı aracılığıyla savunma yapılandırma ve Windows VM 'ye bağlanma

Bu öğreticide, Azure savunma ve Azure portal kullanarak tarayıcınız aracılığıyla bir sanal makineye nasıl bağlanabilmeniz gösterilmektedir. Azure portal, sanal ağınıza savunma dağıtırsınız. Savunma 'yı dağıttıktan sonra, Azure portal kullanarak bir VM 'ye özel IP adresi aracılığıyla bağlanırsınız. VM 'nizin genel bir IP adresi veya özel bir yazılıma ihtiyacı yoktur. Hizmet sağlandıktan sonra, RDP/SSH deneyimi aynı sanal ağdaki tüm sanal makineler için kullanılabilir. Azure savunma hakkında daha fazla bilgi için bkz. [Azure](bastion-overview.md)savunma nedir?.

Bu öğreticide aşağıdakilerin nasıl yapılacağını öğreneceksiniz:

> [!div class="checklist"]
> * VNet 'iniz için bir savunma ana bilgisayarı oluşturma
> * Bir Windows sanal makinesine bağlanma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

* Bir sanal ağ.
* Sanal ağdaki bir Windows sanal makinesi.
* Aşağıdaki gerekli roller:
  * Sanal makinede okuyucu rolü.
  * Sanal makinenin özel IP 'si ile NIC 'de okuyucu rolü.
  * Azure savunma kaynağında okuyucu rolü.

* Bağlantı noktaları: Windows VM 'ye bağlanmak Için aşağıdaki bağlantı noktalarının Windows VM 'niz üzerinde açık olması gerekir:
  * Gelen bağlantı noktaları: RDP (3389)

 >[!NOTE]
 >Azure Özel DNS bölgeleriyle Azure savunma kullanımı Şu anda desteklenmiyor. Başlamadan önce lütfen savunma kaynağınızı dağıtmayı planladığınız sanal ağın özel bir DNS bölgesine bağlı olmadığından emin olun.
 >

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Savunma Konağı oluşturma

Bu bölüm, VNet 'iniz üzerinde savunma nesnesi oluşturmanıza yardımcı olur. Bu, VNet 'teki bir VM ile güvenli bir bağlantı oluşturmak için gereklidir.

1. **Giriş** sayfasından **+ kaynak oluştur**' u seçin.
1. **Yeni** sayfada, arama kutusunda, giriş ' i yazın ve ardından arama sonuçlarına ulaşmak için **ENTER** **' u seçin**. Savunma nedeniyle yayımcının Microsoft olduğunu doğrulayın.
1. **Oluştur**’u seçin.
1. Savunma **Oluştur** sayfasında, yeni bir savunma kaynağı yapılandırın.

   :::image type="content" source="./media/tutorial-create-host-portal/bastion-basics.png" alt-text="Güçlendirilmiş erişim konağı oluşturma" lightbox="./media/tutorial-create-host-portal/bastion-basics.png":::

    * **Abonelik**: yeni bir savunma kaynağı oluşturmak için kullanmak istediğiniz Azure aboneliği.
    * **Kaynak grubu**: yeni savunma kaynağının oluşturulacağı Azure Kaynak grubu. Mevcut bir kaynak grubunuz yoksa yeni bir tane oluşturabilirsiniz.
    * **Ad**: yeni savunma kaynağının adı.
    * **Bölge**: kaynağın oluşturulacağı Azure ortak bölgesi.
    * **Sanal ağ**: savunma kaynağının oluşturulacağı sanal ağ. Bu işlem sırasında portalda yeni bir sanal ağ oluşturabilir veya var olan bir sanal ağı kullanabilirsiniz. Var olan bir sanal ağı kullanıyorsanız, var olan sanal ağın savunma alt ağ gereksinimlerine uyum sağlamak için yeterli boş adres alanı olduğundan emin olun. Sanal ağınızı açılan listeden görmüyorsanız, doğru kaynak grubunu seçtiğinizden emin olun.
    * **Alt ağ**: bir sanal ağ oluşturduğunuzda veya seçtiğinizde alt ağ alanı görüntülenir. Sanal ağınızda, yeni savunma konağının dağıtılacağı alt ağ. Alt ağ, savunma ana bilgisayarına tahsis edilir. **Alt ağ yapılandırmasını Yönet** ' i seçin ve Azure savunma alt ağını oluşturun. **+ Alt ağ** ' ı seçin ve aşağıdaki yönergeleri kullanarak bir alt ağ oluşturun:

         * Alt ağ **AzureBastionSubnet** olarak adlandırılmalıdır.
         * Alt ağ en az/27 veya daha büyük olmalıdır.

      Ek alanlar doldurmanız gerekmez. **Tamam** ' ı seçin ve ardından sayfanın üst kısmında, savunma yapılandırması sayfasına dönmek için **bir savunma oluştur** ' u seçin.
    * **Genel IP adresi**: RDP/SSH 'ye erişilecek savunma kaynağının genel IP 'si (443 numaralı bağlantı noktası üzerinden). Yeni bir genel IP oluşturun. Genel IP adresi, oluşturmakta olduğunuz savunma kaynağıyla aynı bölgede olmalıdır. Bu IP adresinin, bağlanmak istediğiniz VM 'lerden herhangi biri ile ilgili hiçbir şey yok. Bu, savunma ana bilgisayar kaynağı için genel IP olur.
    * **Genel IP adresi adı**: genel IP adresi kaynağının adı. Bu öğretici için varsayılan olarak bırakabilirsiniz.
    * **Genel IP adresi SKU 'su**: Bu ayar varsayılan olarak **Standart** olarak doldurulur. Azure savunma yalnızca standart genel IP SKU 'sunu kullanır/destekler.
    * **Atama**: Bu ayar varsayılan olarak **statik** olarak doldurulur.

1. Ayarları belirtmeyi tamamladığınızda, **gözden geçir + oluştur**' u seçin. Bu, değerleri doğrular. Doğrulama başarılı olduktan sonra savunma kaynağını oluşturabilirsiniz.
1. **Oluştur**’u seçin.
1. Dağıtımınızın devam ettiğinden emin olarak bir ileti görürsünüz. Kaynaklar oluşturulduğundan bu sayfada durum görüntülenecektir. Savunma kaynağının oluşturulması ve dağıtılması yaklaşık 5 dakika sürer.

## <a name="connect-to-a-vm"></a>Sanal makineye bağlanma

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam etmeyecekecekseniz, aşağıdaki adımları kullanarak kaynaklarınızı silin:

1. Portalın üst kısmındaki **arama** kutusuna kaynak grubunuzun adını girin. Arama sonuçlarında kaynak grubunuzu gördüğünüzde, onu seçin.
1. **Kaynak grubunu sil**'i seçin.
1. Kaynak grubu **adını yazmak** için kaynak grubunuzun adını girin: ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir savunma ana bilgisayarı oluşturdunuz ve bunu bir sanal ağla ilişkilendirdikten sonra bir Windows VM 'sine bağlanırsınız. Azure savunma alt ağımızı kullanarak ağ güvenlik grupları 'nı kullanmayı tercih edebilirsiniz. Bunu yapmak için bkz.:

> [!div class="nextstepaction"]
> [NSG’ler ile çalışma](bastion-nsg.md)
