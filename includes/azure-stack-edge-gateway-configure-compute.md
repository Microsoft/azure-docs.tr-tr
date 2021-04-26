---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/07/2021
ms.author: alkohli
ms.openlocfilehash: c51577882e75facb1d8eb03c7cfab82467c5ec51
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99524965"
---
Azure Stack Edge Pro ortamınızda işlem yapılandırmak için Azure portal aracılığıyla bir IoT Hub kaynağı oluşturacaksınız.

1. Azure Stack Edge kaynağınızın Azure portal, **Genel Bakış ' a** gidin ve **IoT Edge**' yı seçin.

   ![İşlem ile çalışmaya başlama](./media/azure-stack-edge-gateway-configure-compute/configure-compute-1.png)

2. **IoT Edge hizmeti 'Ni etkinleştir** bölümünde **Ekle**' yi seçin.

   ![İşlem yapılandırma](./media/azure-stack-edge-gateway-configure-compute/configure-compute-2.png)

3. **Uç Işlem yapılandırma** dikey penceresinde aşağıdaki bilgileri girin:
   
    |Alan  |Değer  |
    |---------|---------|
    |Abonelik     |IoT Hub kaynağınız için bir abonelik seçin. Azure Stack Edge kaynağı tarafından kullanılan aboneliğin aynısını kullanabilirsiniz.         |
    |Kaynak grubu     |IoT Hub kaynağınız için bir kaynak grubu seçin. Azure Stack Edge kaynağı tarafından kullanılan aynı kaynak grubunu kullanabilirsiniz.         |
    |IoT Hub     | **Yeni** veya **mevcut** seçeneklerinden birini belirleyin. <br> Varsayılan olarak IoT kaynağı oluşturulurken Standart katmanı (S1) kullanılır. Bir ücretsiz katman IoT kaynağı kullanmak için kaynağı oluşturun ve sonra da mevcut kaynağı seçin. <br> Her durumda IoT Hub kaynağı, Azure Stack Edge kaynağıyla aynı aboneliği ve kaynak grubunu kullanır.     |
    |Name     |Varsayılan adı kabul edin veya IoT Hub kaynağınız için bir ad girin.         |

   ![İşlem 2 ile çalışmaya başlama](./media/azure-stack-edge-gateway-configure-compute/configure-compute-3.png)

4. Ayarları tamamladığınızda, **gözden geçir + oluştur**' u seçin. IoT Hub kaynağınız için ayarları gözden geçirin ve **Oluştur**' u seçin.

   Bir IoT Hub kaynağı için kaynak oluşturma birkaç dakika sürer. Kaynak oluşturulduktan sonra **genel bakış** IoT Edge hizmetin çalıştığını gösterir.

   ![İşlem 3 ' ü kullanmaya başlama](./media/azure-stack-edge-gateway-configure-compute/configure-compute-4.png)

5. Edge işlem rolünün yapılandırıldığını onaylamak için **IoT Edge > Özellikler**' e gidin.

   ![İşlem 4 ' ü kullanmaya başlama](./media/azure-stack-edge-gateway-configure-compute/configure-compute-5.png)

   Edge cihazında Edge hesaplama rolü ayarlandığında, iki cihaz oluşturur: bir IoT cihazı ve bir IoT Edge cihaz. Her iki cihaz de IoT Hub kaynağında görüntülenebilir. Bu IoT Edge cihazında aynı zamanda bir IoT Edge çalışma zamanı çalışıyor. Bu noktada, IoT Edge cihazınız için yalnızca Linux platformu kullanılabilir.

Arka planda, sanal makinelerin ve Kubernetes kümesinin oluşturulduğu için işlem yapılandırması 20-30 dakika sürebilir.

Azure portal işlem başarıyla yapılandırıldıktan sonra, bir Kubernetes kümesi ve IoT ad alanıyla ilişkili bir varsayılan kullanıcı (Azure Stack Edge Pro tarafından denetlenen bir sistem ad alanı) mevcuttur.
