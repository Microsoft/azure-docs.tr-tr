---
title: Application Gateway ve Azure Güvenlik Duvarı 'Nı kullanarak uygulamaları Internet 'Te kullanıma sunma
description: Application Gateway ve Azure Güvenlik Duvarı kullanarak uygulamaları Internet 'Te kullanıma sunma
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: 6c22d1bae4f1d116aa52846880498c7c2a425174
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878797"
---
# <a name="expose-applications-to-the-internet-using-application-gateway-and-azure-firewall"></a>Application Gateway ve Azure Güvenlik Duvarı 'Nı kullanarak uygulamaları Internet 'Te kullanıma sunma

Bu belgede Application Gateway ve Azure Güvenlik Duvarı kullanılarak uygulamaların Internet 'Te nasıl kullanılacağı açıklanmaktadır. Sanal ağınızda bir Azure yay bulut hizmeti örneği dağıtıldığında, hizmet örneğindeki uygulamalara yalnızca özel ağdan erişilebilir. Uygulamaları Internet üzerinde erişilebilir hale getirmek için **azure Application Gateway** ve isteğe bağlı olarak **Azure Güvenlik Duvarı** ile tümleştirmeniz gerekir.

## <a name="prerequisites"></a>Önkoşullar

- [Azure CLI sürüm 2.0.4 veya üzeri](/cli/azure/install-azure-cli).

## <a name="define-variables"></a>Değişkenleri tanımlama

Azure [sanal ağ (VNet ekleme) Içinde Azure Spring Cloud 'ı dağıtma](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)bölümünde yönlendirilmek üzere oluşturduğunuz kaynak grubu ve sanal ağ için değişkenleri tanımlayın. Değerleri gerçek ortamınıza göre özelleştirin.

```
SUBSCRIPTION='subscription-id'
RESOURCE_GROUP='my-resource-group'
LOCATION='eastus'
SPRING_APP_PRIVATE_FQDN='my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io'
VIRTUAL_NETWORK_NAME='azure-spring-cloud-vnet'
APPLICATION_GATEWAY_SUBNET_NAME='app-gw-subnet'
APPLICATION_GATEWAY_SUBNET_CIDR='10.1.2.0/24'
```

## <a name="login-to-azure"></a>Azure'da oturum açma

Azure CLı 'da oturum açın ve etkin aboneliğinizi seçin.

```
az login
az account set --subscription ${SUBSCRIPTION}
```

## <a name="create-network-resources"></a>Ağ kaynakları oluşturma

Oluşturulacak **azure Application Gateway** , Azure yay bulut hizmeti örneği ile aynı sanal ağa veya eşlenmiş sanal ağa katılacak. İlk olarak, kullanarak sanal ağdaki Application Gateway için yeni bir alt ağ oluşturun `az network vnet subnet create` ve ayrıca kullanarak Application Gateway ön ucu olarak genel BIR IP adresi oluşturun `az network public-ip create` .

```
APPLICATION_GATEWAY_PUBLIC_IP_NAME='app-gw-public-ip'
az network vnet subnet create \
    --name ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --address-prefix ${APPLICATION_GATEWAY_SUBNET_CIDR}
az network public-ip create \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --allocation-method Static \
    --sku Standard
```

## <a name="create-application-gateway"></a>Uygulama ağ geçidi oluşturma

Kullanarak bir uygulama ağ geçidi oluşturun `az network application-gateway create` ve uygulamanızın özel tam etki alanı adını (FQDN) arka uç havuzunda sunucu olarak belirtin. Ardından, öğesini kullanarak, `az network application-gateway http-settings update` arka uç havuzundan ana bilgisayar adını kullanmak üzere http ayarını güncelleştirin.

```
APPLICATION_GATEWAY_NAME='my-app-gw'
az network application-gateway create \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --capacity 2 \
    --sku Standard_v2 \
    --http-settings-cookie-based-affinity Enabled \
    --http-settings-port 443 \
    --http-settings-protocol Https \
    --public-ip-address ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --subnet ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --servers ${SPRING_APP_PRIVATE_FQDN}
az network application-gateway http-settings update \
    --gateway-name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --name appGatewayBackendHttpSettings \
    --host-name-from-backend-pool true
```

Azure 'un uygulama ağ geçidini oluşturması 30 dakika kadar sürebilir. Oluşturulduktan sonra, kullanarak arka uç durumunu kontrol edin `az network application-gateway show-backend-health` .  Bu, Application Gateway 'in uygulamanıza özel FQDN aracılığıyla ulaşmasını denetler.

```
az network application-gateway show-backend-health \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP}
```

Çıktı, arka uç havuzunun sağlıklı durumunu gösterir.

```
{
  "backendAddressPools": [
    {
      "backendHttpSettingsCollection": [
        {
          "servers": [
            {
              "address": "my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io",
              "health": "Healthy",
              "healthProbeLog": "Success. Received 200 status code",
              "ipConfiguration": null
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="access-your-application-using-the-frontend-public-ip-of-the-application-gateway"></a>Application Gateway 'in ön uç genel IP 'sini kullanarak uygulamanıza erişin

Kullanarak uygulama ağ geçidinin genel IP adresini alın `az network public-ip show` .

```
az network public-ip show \
    --resource-group ${RESOURCE_GROUP} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --query [ipAddress] \
    --output tsv
```

Genel IP adresini kopyalayıp tarayıcınızın adres çubuğuna yapıştırın.

  ![Genel IP 'de uygulama](media/spring-cloud-expose-apps-gateway-az-firewall/app-gateway-public-ip.png)

## <a name="see-also"></a>Ayrıca bkz.

- [VNET 'te Azure Spring Cloud sorunlarını giderme](spring-cloud-troubleshooting-vnet.md)
- [VNET 'te Azure Spring Cloud çalıştırmaya yönelik müşteri sorumlulukları](spring-cloud-vnet-customer-responsibilities.md)