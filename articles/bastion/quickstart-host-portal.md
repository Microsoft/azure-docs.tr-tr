---
title: "Hızlı başlangıç: Azure savunma yapılandırma ve özel IP adresi ve tarayıcı aracılığıyla bir VM 'ye bağlanma"
titleSuffix: Azure Bastion
description: Bu hızlı başlangıç makalesinde bir sanal makineden Azure savunma ana bilgisayarı oluşturmayı ve özel IP adresi aracılığıyla tarayıcınızla güvenli bir şekilde sanal makineye nasıl bağlanacağınızı öğrenin.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 02/18/2021
ms.author: cherylmc
ms.openlocfilehash: 53f09eed89f9667611ed4d5e0268c889609d560a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553575"
---
# <a name="quickstart-connect-to-a-vm-securely-through-a-browser-via-private-ip-address"></a>Hızlı başlangıç: özel IP adresi aracılığıyla bir tarayıcı aracılığıyla güvenli bir şekilde VM 'ye bağlanma

Azure portal ve Azure savunma kullanarak tarayıcınız aracılığıyla bir sanal makineye (VM) bağlanabilirsiniz. Bu hızlı başlangıç makalesinde, Azure savunma 'yı VM ayarlarınıza göre yapılandırma ve ardından Portal üzerinden sanal makinenize bağlama işlemi gösterilmektedir. VM 'nin genel IP adresi, istemci yazılımı, Aracısı veya özel bir yapılandırmaya sahip olması gerekmez. Hizmet sağlandıktan sonra, RDP/SSH deneyimi aynı sanal ağdaki tüm sanal makineler için kullanılabilir. Azure savunma hakkında daha fazla bilgi için bkz. [Azure](bastion-overview.md)savunma nedir?.

## <a name="prerequisites"></a><a name="prereq"></a>Kaynakları

* Etkin aboneliği olan bir Azure hesabı. Yoksa, [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Savunma kullanarak bir VM 'ye tarayıcı aracılığıyla bağlanabilme için, Azure portal oturum açabiliyor olmanız gerekir.

* Bir sanal ağdaki Windows sanal makinesi. VM yoksa [hızlı başlangıç: VM oluşturma](../virtual-machines/windows/quick-create-portal.md)' yı kullanarak bir tane oluşturun.

  * Örnek değerlere ihtiyacınız varsa, bkz. belirtilen [örnek değerleri](#values).
  * Zaten bir sanal ağınız varsa, Sanal ağınızı oluştururken ağ sekmesinden seçtiğinizden emin olun.
  * Zaten bir sanal ağınız yoksa, VM 'nizi oluşturduğunuz sırada bir tane oluşturabilirsiniz.
  * Azure savunma aracılığıyla bağlanabilmek için bu VM 'nin genel IP adresine sahip olmanız gerekmez.

* Gerekli VM rolleri:
  * Sanal makinede okuyucu rolü.
  * Sanal makinenin özel IP 'si ile NIC 'de okuyucu rolü.
  
* Gerekli VM bağlantı noktaları:
  * Gelen bağlantı noktaları: RDP (3389)

 >[!NOTE]
 >Azure Özel DNS bölgeleriyle Azure savunma kullanımı Şu anda desteklenmiyor. Başlamadan önce lütfen savunma kaynağınızı dağıtmayı planladığınız sanal ağın özel bir DNS bölgesine bağlı olmadığından emin olun.
 >

### <a name="example-values"></a><a name="values"></a>Örnek değerler

Bu yapılandırmayı oluştururken aşağıdaki örnek değerleri kullanabilir veya kendi kodunuzu kullanabilirsiniz.

**Temel VNet ve VM değerleri:**

|**Ad** | **Değer** |
| --- | --- |
| Sanal makine| TestVM |
| Kaynak grubu | TestRG1 |
| Bölge | Doğu ABD |
| Sanal ağ | VNet1 |
| Adres alanı | 10.1.0.0/16 |
| Alt ağlar | Ön uç: 10.1.0.0/24 |

**Azure savunma değerleri:**

|**Ad** | **Değer** |
| --- | --- |
| Name | VNet1-savunma |
| + Alt ağ adı | AzureBastionSubnet |
| AzureBastionSubnet adresleri | VNet adres alanınızda/27 alt ağ maskesiyle bir alt ağ. Örneğin, 10.1.1.0/27.  |
| Genel IP adresi |  Yeni oluştur |
| Genel IP adresi adı | VNet1-IP  |
| Genel IP adresi SKU 'SU |  Standart  |
| Atama  | Statik |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Savunma Konağı oluşturma

Bir savunma konağını yapılandırmanın birkaç farklı yolu vardır. Aşağıdaki adımlarda, doğrudan sanal makinenizde Azure portal bir savunma ana bilgisayarı oluşturacaksınız. Bir VM 'den bir konak oluşturduğunuzda, sanal makinenize ve/veya sanal ağınıza karşılık gelen farklı ayarlar otomatik olarak doldurulur.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Bağlanmak istediğiniz VM 'ye gidin ve ardından **Bağlan**' ı seçin.

   :::image type="content" source="./media/quickstart-host-portal/vm-connect.png" alt-text="Sanal makine ayarlarının ekran görüntüsü." lightbox="./media/quickstart-host-portal/vm-connect.png":::
1. Açılan menüden, savunma ' **yı seçin.**

   :::image type="content" source="./media/quickstart-host-portal/bastion.png" alt-text="Savunma açılan listesinin ekran görüntüsü." lightbox="./media/quickstart-host-portal/bastion.png":::
1. **Testvm 'de | Bağlan sayfasında**, **kullan**' ı seçin.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="Kullanım ekranının ekran görüntüsü.":::

1. Azure savunma **kullanarak bağlan** sayfasında, değerleri yapılandırın.

   * **1. Adım:** , Savunma konağını doğrudan sanal bilgisayarınızdan oluşturduğunuz için değerler önceden doldurulur.

   * **2. Adım:** Adres alanı, önerilen bir adres alanı ile önceden doldurulur. AzureBastionSubnet adres alanı/27 veya daha büyük (/26,/25 vb.) olmalıdır.

   :::image type="content" source="./media/quickstart-host-portal/create-subnet.png" alt-text="Savunma alt ağını oluşturma ekran görüntüsü.":::

1. AzureBastionSubnet oluşturmak için **alt ağ oluştur** ' a tıklayın.
1. Alt ağ oluşturulduktan sonra sayfa **Adım 3**' e otomatik olarak ilerler. 3. adım için aşağıdaki değerleri kullanın:

   * **Ad:** Savunma konağını adlandırın.
   * **Genel IP adresi:****Yeni oluştur**'u seçin.
   * **Genel IP adresi adı:** Genel IP adresi kaynağının adı.
   * **Genel IP adresi SKU 'su:** **Standart** olarak önceden yapılandırılmış
   * **Atama:** **Statik** olarak önceden yapılandırılmış. Azure savunma için dinamik bir atama kullanamazsınız.
   * **Kaynak grubu**: VM ile aynı kaynak grubu.

   :::image type="content" source="./media/quickstart-host-portal/create-bastion.png" alt-text="3. adım ekran görüntüsü.":::
1. Değerleri tamamladıktan sonra **Varsayılanları kullanarak Azure savunma oluştur**' u seçin. Azure ayarlarınızı doğrular ve ardından Konağı oluşturur. Ana bilgisayar ve kaynakları, oluşturulması ve dağıtılması yaklaşık 5 dakika sürer.

## <a name="connect"></a><a name="connect"></a>Bağlanabilmeniz

Sanal ağa bağlantı dağıtıldıktan sonra ekran bağlan sayfasına dönüşür.

1. Sanal makineniz için Kullanıcı adını ve parolayı yazın. Ardından **Bağlan**' ı seçin.

   :::image type="content" source="./media/quickstart-host-portal/connect.png" alt-text="Ekran görüntüsü Azure savunma kullanarak bağlan iletişim kutusunu gösterir.":::
1. Bu sanal makineyle RDP bağlantısı, 443 numaralı bağlantı noktasını ve savunma hizmetini kullanarak doğrudan Azure portal (HTML5 üzerinden) açılır.

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="RDP bağlantısı":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sanal ağ ve sanal makineler kullanarak işiniz bittiğinde, kaynak grubunu ve içerdiği tüm kaynakları silin:

1. Portalın üst kısmındaki **arama** kutusuna kaynak grubunuzun adını girip arama sonuçlarından seçin.

1. **Kaynak grubunu sil**'i seçin.

1. Kaynak **grubunun adını yazıp** **Sil**' i seçerek kaynak grubunuzu girin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, sanal ağınız için bir savunma ana bilgisayarı oluşturdunuz ve ardından savunma aracılığıyla güvenli bir şekilde sanal makineye bağlanırsınız. Daha sonra, bir sanal makine ölçek kümesine bağlanmak istiyorsanız aşağıdaki adımla devam edebilirsiniz.

> [!div class="nextstepaction"]
> [Azure savunma kullanarak bir sanal makine ölçek kümesine bağlanma](bastion-connect-vm-scale-set.md)
