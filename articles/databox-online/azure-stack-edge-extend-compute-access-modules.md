---
title: İşlem ağını Azure Stack Edge Pro 'da modüllere erişim için yönetme | Microsoft Docs
description: Azure Stack Edge Pro 'daki işlem ağının bir dış IP aracılığıyla modüllere erişim için nasıl genişletileceğini açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 19c92deb58ac51aa882e7123b9a90aa3eae627d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90894121"
---
# <a name="enable-compute-network-on-your-azure-stack-edge-pro"></a>Azure Stack Edge Pro 'unuzda işlem ağını etkinleştirin

Bu makalede, Azure Stack Edge Pro 'da çalışan modüllerin cihazda etkin olan işlem ağına nasıl erişebileceği açıklanır.

Ağı yapılandırmak için aşağıdaki adımları uygulayın:

- İşlem için Azure Stack Edge Pro cihazınızda bir ağ arabirimini etkinleştirme
- Azure Stack Edge Pro 'unuzda işlem ağına erişmek için bir modül ekleyin
- Modülün etkin ağ arabirimine erişebileceğini doğrulayın

Bu öğreticide, senaryoyu göstermek için bir Web sunucusu uygulama modülünü kullanacaksınız.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce şunları yapmanız gerekir:

- Cihaz kurulumu ile bir Azure Stack Edge Pro cihazı tamamlandı.
- Öğreticiye göre **işlem adımını yapılandırmayı** tamamladınız: cihazınızda [Azure Stack Edge Pro ile veri dönüştürme](azure-stack-edge-deploy-configure-compute-advanced.md#configure-compute) . Cihazınızın ilişkili bir IoT Hub kaynağına, IoT cihazına ve bir IoT Edge cihazına sahip olması gerekir.

## <a name="enable-network-interface-for-compute"></a>İşlem için ağ arabirimini etkinleştir

Cihazınızda çalışan modüllerle bir dış ağ aracılığıyla erişmek için, cihazınızdaki bir ağ arabirimine bir IP adresi atamanız gerekir. Bu işlem ayarlarını, yerel Web kullanıcı arabiriminden yönetebilirsiniz.

İşlem ayarlarını yapılandırmak için yerel Web Kullanıcı arabiriminde aşağıdaki adımları uygulayın.

1. Yerel Web Kullanıcı arabiriminde **yapılandırma > işlem ayarları**' na gidin.  

2. Cihazda çalıştıracağınız bir işlem modülüne bağlanmak için kullanmak istediğiniz ağ arabirimini **etkinleştirin** .

    - Statik IP adresleri kullanılıyorsa, ağ arabirimi için bir IP adresi girin.
    - DHCP kullanılıyorsa, IP adresleri otomatik olarak atanır. Bu örnek DHCP kullanır.

    ![İşlem ayarlarını etkinleştir 1](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. Ayarları uygulamak için **Uygula** ' yı seçin. DHCP kullanılıyorsa ağ arabirimine atanan IP adresini bir yere unutmayın.

    ![İşlem ayarlarını etkinleştir](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Web sunucusu uygulama modülünü Ekle

Azure Stack Edge Pro cihazınıza bir Web sunucusu uygulama modülü eklemek için aşağıdaki adımları uygulayın.

1. Azure Stack Edge Pro aygıtınızla ilişkili IoT Hub kaynağına gidin ve **IoT Edge cihaz**' ı seçin.
2. Azure Stack Edge Pro cihazınıza ilişkin IoT Edge cihazını seçin. **Cihaz ayrıntıları**' nın **Modül ayarla**' yı seçin. **Modül Ekle**' de **+ Ekle** ' yi seçin ve ardından **IoT Edge modülü**' nü seçin.
3. **IoT Edge özel modüller** dikey penceresinde:

    1. Web sunucusu uygulaması modülünüzün dağıtmak istediğiniz bir **ad** belirtin.
    2. Modül görüntünüz için bir **görüntü URI 'si** sağlayın. Belirtilen ad ve etiketlerle eşleşen bir modül alındı. Bu durumda, `nginx:stable` genel [Docker deposundan](https://hub.docker.com/_/nginx/)kararlı bir NGINX görüntüsü (kalıcı olarak etiketlenir) çeker.
    3. **Kapsayıcı oluşturma seçeneklerinde** aşağıdaki örnek kodu yapıştırın:  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        Bu yapılandırma, TCP bağlantı noktası 8080 ' de (varsayılan Web sunucusu bağlantı noktası 80 olan) *http* üzerinden Işlem ağı IP 'sini kullanarak modüle erişmenizi sağlar.

        ![IoT Edge özel modül dikey penceresinde bağlantı noktası bilgilerini belirtin](media/azure-stack-edge-extend-compute-access-modules/module-information.png)

    4. **Kaydet**’i seçin.

## <a name="verify-module-access"></a>Modül erişimini doğrula

1. Modülün başarıyla dağıtıldığını ve çalıştığını doğrulayın. **Cihaz ayrıntıları** sayfasındaki **modüller** sekmesinde, modülün çalışma zamanı durumu **çalışıyor** olmalıdır.  
2. Web sunucusu uygulama modülüne bağlanın. Bir tarayıcı penceresi açın ve şunu yazın:

    `http://<compute-network-IP-address>:8080`

    Web sunucusu uygulamasının çalıştığını görmeniz gerekir.

    ![Belirtilen bağlantı noktası üzerinden modülle bağlantıyı doğrula](media/azure-stack-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure portal aracılığıyla kullanıcıları yönetme](azure-stack-edge-manage-users.md) hakkında bilgi edinin.
