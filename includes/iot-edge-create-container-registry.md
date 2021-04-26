---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/30/2019
ms.author: kgremban
ms.openlocfilehash: 8cae7ac2d3b961120cb9100f5072dc141769afff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95561515"
---
## <a name="create-a-container-registry"></a>Kapsayıcı kayıt defteri oluşturma

Bu öğreticide, Azure IoT araçları uzantısını kullanarak bir modül oluşturup dosyalardan bir **kapsayıcı görüntüsü** oluşturursunuz. Ardından bu görüntüyü, görüntülerinizin depolandığı ve yönetildiği **kayıt defterine** gönderirsiniz. Son olarak, görüntünüzü IoT Edge cihazınızda çalıştırmak üzere kayıt defterinizden dağıtırsınız.

Kapsayıcı görüntülerinizi tutmak için Docker ile uyumlu herhangi bir kayıt defteri kullanabilirsiniz. İki popüler Docker kayıt defteri hizmeti [Azure Container Registry](../articles/container-registry/index.yml) ve [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Bu öğreticide Azure Container Registry kullanılır.

Zaten bir kapsayıcı kayıt defteriniz yoksa, Azure 'da yeni bir tane oluşturmak için aşağıdaki adımları izleyin:

1. [Azure portalında](https://portal.azure.com), **Kaynak oluştur** > **Kapsayıcılar** > **Container Registry**'yi seçin.

2. Kapsayıcı kayıt defterinizi oluşturmak için aşağıdaki değerleri girin:

   | Alan | Değer |
   | ----- | ----- |
   | Abonelik | Açılan listeden bir abonelik seçin. |
   | Kaynak grubu | IoT Edge hızlı başlangıçlarında ve öğreticilerinde oluşturduğunuz tüm test kaynakları için aynı kaynak grubunu kullanmanızı öneririz. Örneğin, **IoTEdgeResources**. |
   | Kayıt defteri adı | Benzersiz bir ad girin. |
   | Konum | Size yakın bir konum seçin. |
   | SKU | **Temel**'i seçin. |

3. **Oluştur**’u seçin.

4. Kapsayıcı kayıt defteriniz oluşturulduktan sonra, bu dosyaya gidin ve sol bölmeden **Ayarlar**' ın altında bulunan menüden **erişim tuşları** ' nı seçin. 

5. Yönetici kullanıcının kapsayıcı kayıt defteriniz için **Kullanıcı adını** ve **parolayı** görüntülemesini etkinleştirmek üzere tıklayın.

6. **Oturum açma sunucusu**, **Kullanıcı adı** ve **parola** değerlerini kopyalayın ve uygun bir yere kaydedin. Bu değerleri, kapsayıcı kayıt defterine erişim sağlamak için bu öğreticide kullanın.

   ![Kapsayıcı kayıt defteri için oturum açma sunucusu, Kullanıcı adı ve parola kopyalama](./media/iot-edge-create-container-registry/registry-access-key.png)