---
title: Sanal ağlar için Azure IoT cihaz sağlama hizmeti (DPS) desteği
description: Azure IoT cihaz sağlama hizmeti (DPS) ile sanal ağlar bağlantı modelini kullanma
services: iot-hub
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: wesmc
ms.openlocfilehash: f1409a931195d236b2729e629e4603c606137593
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94959790"
---
# <a name="azure-iot-hub-device-provisioning-service-dps-support-for-virtual-networks"></a>Sanal ağlar için Azure IoT Hub cihaz sağlama hizmeti (DPS) desteği

Bu makalede, DPS kullanan IoT Hub 'ları ile IoT cihazları sağlamak için sanal ağ (VNET) bağlantı deseninin tanıtılmaktadır. Bu model, müşterilerin sahip olduğu bir Azure sanal ağı içindeki cihazlar, DPS ve IoT Hub 'ı arasında özel bağlantı sağlar. 

DPS bir sanal ağ ile yapılandırıldığı Çoğu senaryoda, IoT Hub aynı VNET 'te de yapılandırılır. Sanal ağ desteği ve IoT Hub 'Ları yapılandırması hakkında daha ayrıntılı bilgi için bkz. [IoT Hub Virtual Network Support](../iot-hub/virtual-network-support.md).



## <a name="introduction"></a>Giriş

Varsayılan olarak, DPS ana bilgisayar adları, Internet üzerinden genel olarak yönlendirilebilir bir IP adresi olan genel bir uç nokta ile eşlenir. Bu genel uç nokta tüm müşteriler tarafından görülebilir. Genel uç noktaya erişim, geniş alan ağları ve şirket içi ağları üzerinde IoT cihazları tarafından denenmelidir.

Birçok nedenden dolayı müşteriler, DPS gibi Azure kaynaklarıyla bağlantıyı kısıtlamak isteyebilir. Bu nedenlerden bazıları şunlardır:

* Genel Internet üzerinden bağlantı pozlamasını engelleyin. IoT Hub ve DPS kaynaklarınız için ağ düzeyi yalıtımı aracılığıyla ek güvenlik katmanları sunarak pozlama azaltılabilir

* Verilerinizin ve trafiğinizin doğrudan Azure omurga ağına aktarılmasını sağlamak için şirket içi ağ varlıklarınızdan özel bir bağlantı deneyimini etkinleştirme.

* Gizli şirket içi ağlarda gerçekleştirilen ayıklanma saldırılarını önleyin. 

* [Özel uç noktaları](../private-link/private-endpoint-overview.md)kullanan Azure genelindeki Azure genelinde bağlantı desenleri aşağıda verilmiştir.

Bağlantıları kısıtlamak için yaygın yaklaşımlar, [DPS IP filtresi kurallarını](./iot-dps-ip-filtering.md) ve sanal ağı (VNet) [Özel uç noktalarla](../private-link/private-endpoint-overview.md)kapsar. Bu makalenin amacı, Özel uç noktalar kullanılarak DPS için VNET yaklaşımını betimmaktır. 

Şirket içi ağlarda çalışan cihazlar, Azure 'daki bir VNET 'e bağlanmak ve özel uç noktalar aracılığıyla DPS kaynaklarına erişmek için [sanal özel ağ (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) veya [ExpressRoute](https://azure.microsoft.com/services/expressroute/) özel eşlemesini kullanabilir. 

Özel bir uç nokta, bir Azure kaynağına erişilebilen, müşterinin sahip olduğu VNET içinde ayrılan özel bir IP adresidir. DPS kaynağınız için özel bir uç nokta sunarak, VNET 'iniz içinde çalışan cihazların, genel uç noktaya trafiğe izin vermeden DPS kaynağınız tarafından sağlanmasını istemesi için izin verebilirsiniz.


## <a name="prerequisites"></a>Önkoşullar

Devam etmeden önce aşağıdaki önkoşulların karşılandığından emin olun:

* DPS kaynağınız zaten oluşturulmuş ve IoT Hub 'larıyla bağlantılı. Yeni bir DPS kaynağı ayarlama hakkında yönergeler için, bkz [. IoT Hub cihaz sağlama hizmetini Azure Portal Ile ayarlama](./quick-setup-auto-provision.md)

* Özel uç noktanın oluşturulacağı bir alt ağa sahip bir Azure VNET sağladınız. Daha fazla bilgi için bkz. [Azure CLI kullanarak sanal ağ oluşturma](../virtual-network/quick-create-cli.md).

* Şirket içi ağların içinde çalışan cihazlarda [sanal özel ağ (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) veya [ExpressRoute](https://azure.microsoft.com/services/expressroute/) özel eşlemesini Azure VNET 'iniz ile ayarlayın.

## <a name="private-endpoint-limitations"></a>Özel uç nokta sınırlamaları

Özel uç noktaları kullanırken DPS için aşağıdaki geçerli sınırlamaları göz önünde edin:

* DPS kaynağı ve bağlı hub farklı bulutlarda olduğunda özel uç noktalar DPS ile çalışmaz. Örneğin, [Azure Kamu ve küresel Azure](../azure-government/documentation-government-welcome.md).

* Şu anda, Azure işlevi VNET ve özel uç noktalara kilitlendiğinde, DPS için [Azure işlevleri ile özel ayırma ilkeleri](how-to-use-custom-allocation-policies.md) çalışmayacaktır. 

* Geçerli DPS VNET desteği yalnızca DPS 'e veri girişi için geçerlidir. DPS 'ten IoT Hub giden trafik olan veri çıkışı, adanmış VNET yerine iç hizmetten hizmete bir mekanizma kullanır. DPS ve IoT Hub arasında tam VNET tabanlı çıkış kilidi desteği şu anda kullanılamıyor.

* En düşük gecikme süresine sahip bir cihazı IoT Hub 'ına atamak için en düşük gecikme süresi ayırma ilkesi kullanılır. Bu ayırma ilkesi, bir sanal ağ ortamında güvenilir değil. 

## <a name="set-up-a-private-endpoint"></a>Özel uç nokta ayarlama

Özel bir uç nokta ayarlamak için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/), DPS kaynağını açın ve **ağ** sekmesine tıklayın. **Özel uç nokta bağlantıları** ve **+ Özel uç nokta** öğesine tıklayın.

    ![DPS için yeni bir özel uç nokta ekleyin](./media/virtual-network-support/networking-tab-add-private-endpoint.png)

2. _Özel uç nokta temelleri oluştur_ sayfasında, aşağıdaki tabloda belirtilen bilgileri girin.

    ![Özel uç noktalar oluşturma temelleri](./media/virtual-network-support/create-private-endpoint-basics.png)

    | Alan | Değer |
    | :---- | :-----|
    | **Abonelik** | Özel uç noktayı içerecek şekilde istediğiniz Azure aboneliğini seçin.  |
    | **Kaynak grubu** | Özel uç noktayı içerecek bir kaynak grubu seçin veya oluşturun |
    | **Ad**       | Özel uç noktanız için bir ad girin |
    | **Bölge**     | Seçilen bölge, VNET 'i içeren bölgeyle aynı olmalıdır, ancak DPS kaynağıyla aynı olmak zorunda değildir. |

    **İleri: kaynak** ' a tıklayarak özel uç noktanın işaret edecek kaynağı yapılandırın.

3. _Özel uç nokta kaynağı oluştur_ sayfasında, aşağıdaki tabloda belirtilen bilgileri girin.

    ![Özel uç nokta kaynağı oluştur](./media/virtual-network-support/create-private-endpoint-resource.png)

    | Alan | Değer |
    | :---- | :-----|
    | **Abonelik**        | Özel uç noktanızın işaret edecek olan DPS kaynağını içeren Azure aboneliğini seçin.  |
    | **Kaynak türü**       | **Microsoft. Devices/ProvisioningServices** öğesini seçin. |
    | **Kaynak**            | Özel uç noktanın eşolacağı DPS kaynağını seçin. |
    | **Hedef alt kaynak** | **Iotdps** öğesini seçin. |

    > [!TIP]
    > Bu makaledeki [özel bir uç nokta iste](#request-a-private-endpoint) bölümünde **kaynak kimliğine veya diğer ada göre Azure kaynağına bağlanma** hakkında bilgi sağlanır.


    Ileri ' ye tıklayın **:** sanal ağı özel uç nokta için yapılandırma.

4. Özel uç _nokta yapılandırması oluştur_ sayfasında, içinde özel uç nokta oluşturmak için Sanal ağınızı ve alt ağınızı seçin.
 
    **İleri: Etiketler**' e tıklayın ve isteğe bağlı olarak kaynağınız için Etiketler sağlayın.

    ![Özel uç noktayı Yapılandır](./media/virtual-network-support/create-private-endpoint-configuration.png)

6. Özel uç nokta kaynağınızı oluşturmak için **gözden geçir + oluştur** ' a tıklayıp **Oluştur** ' a tıklayın.


## <a name="request-a-private-endpoint"></a>Özel uç nokta isteme

Bir DPS kaynağına, kaynak KIMLIĞIYLE özel bir uç nokta isteyebilirsiniz. Bu isteği yapmak için kaynak sahibine kaynak KIMLIĞI sağlamanız gerekir. 

1. Kaynak KIMLIĞI, aşağıda gösterildiği gibi, DPS kaynağı için özellikler sekmesine sağlanır.

    ![DPS Özellikler sekmesi](./media/virtual-network-support/dps-properties.png)

    > [!CAUTION]
    > Kaynak KIMLIĞININ abonelik KIMLIĞINI içerdiğini unutmayın. 

2. Kaynak KIMLIĞI ' ne sahip olduktan sonra, Özel uç nokta _kaynağı oluşturma_ sayfasında adım 3 ' te [Özel uç nokta ayarlama](#set-up-a-private-endpoint) bölümünde yukarıdaki adımları izleyin. **Kaynak kimliğine veya diğer ada göre Azure kaynağına bağlan** ' a tıklayın ve aşağıdaki tabloya bilgileri girin. 

    | Alan | Değer |
    | :---- | :-----|
    | **Kaynak KIMLIĞI veya diğer ad** | DPS kaynağı için kaynak KIMLIĞINI girin. |
    | **Hedef alt kaynak** | **Iotdps** girin |
    | **İstek iletisi** | DPS kaynak sahibi için bir istek iletisi girin.<br>Örneğin, <br>`Please approve this new private endpoint`<br>`for IoT devices in site 23 to access this DPS instance`  |

    Ileri ' ye tıklayın **:** sanal ağı özel uç nokta için yapılandırma.

3. _Özel uç nokta yapılandırması oluştur_ sayfasında, içinde özel uç nokta oluşturmak için sanal ağı ve alt ağı seçin.
 
    **İleri: Etiketler**' e tıklayın ve isteğe bağlı olarak kaynağınız için Etiketler sağlayın.

4. Özel uç nokta isteğinizi oluşturmak için **gözden geçir + oluştur** ' a tıklayın ve ardından **oluşturun** .

5. DPS sahibi, DPS Ağ sekmesinde özel **uç nokta bağlantıları** listesinde özel uç nokta isteğini görür. Bu sayfada, sahibi aşağıda gösterildiği gibi özel uç nokta isteğini **onaylayabilir** veya **reddedebilir** .

    ![DPS onayı](./media/virtual-network-support/approve-dps-private-endpoint.png)


## <a name="pricing-private-endpoints"></a>Fiyatlandırma Özel uç noktaları

Fiyatlandırma ayrıntıları için bkz. [Azure özel bağlantı fiyatlandırması](https://azure.microsoft.com/pricing/details/private-link).



## <a name="next-steps"></a>Sonraki adımlar

DPS güvenlik özellikleri hakkında daha fazla bilgi edinmek için aşağıdaki bağlantıları kullanın:

* [Güvenlik](./concepts-service.md#attestation-mechanism)
* [TLS 1,2 desteği](tls-support.md)