---
title: Sanal ağda Azure yay bulut erişimi uygulaması
description: Sanal ağdaki bir Azure yay bulutu 'nda uygulamaya erişin.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/11/2020
ms.custom: devx-track-java
ms.openlocfilehash: 37c8b4bc186c217ecb27638f5f50297102345de7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878818"
---
# <a name="access-your-application-in-a-private-network"></a>Uygulamanıza özel bir ağda erişin

Bu belgede özel bir ağda uygulamanız için bir uç noktaya nasıl erişebileceğiniz açıklanır.  Erişim sağlamak için aboneliğinizde bir **Azure özel DNS bölgesi** oluşturmanız gerekir ve özel tam etki alanı adını (FQDN) IP adresine çevirin/çözümleyin.

Azure yay bulut hizmeti örneğindeki uygulamalar için **uç nokta ata** sanal ağınıza dağıtıldığında, uç nokta özel bir FQDN 'dir. Etki alanına yalnızca özel ağdan erişilebilir. Uygulamalar ve hizmetler uygulama uç noktasını kullanır. Bunlar, [Uygulamaları ve dağıtımları görüntüleme](spring-cloud-howto-staging-environment.md#view-apps-and-deployments)bölümünde açıklanan **Test uç noktasını** içerirler. [Azure yay bulutu uygulama günlüklerinin gerçek](spring-cloud-howto-log-streaming.md)zamanlı olarak akışını da tarif eden **günlük akışı**, yalnızca özel ağ içinde de kullanılabilir.

## <a name="create-a-private-dns-zone"></a>Özel bir DNS bölgesi oluşturma

Aşağıdaki yordam, özel ağdaki bir uygulama için özel bir DNS bölgesi oluşturur.

1. Azure portalını açın. Üst arama kutusundan **özel DNS bölgelerini** arayın ve sonuçtan **özel DNS bölge** ' yi seçin.

2. **Özel DNS bölgeleri** sayfasında **+ Ekle**' yi seçin.

3. **Özel DNS bölgesi oluştur** sayfasında formu doldurun. Bölgenin **adı** olarak **<span>Private.azuremicroservices.io</span>** girin.

4. **Gözden geçir + Oluştur**’u seçin.

5. **Oluştur**’u seçin.

Bölgenin oluşturulması birkaç dakika sürebilir.

## <a name="link-the-virtual-network"></a>Sanal ağı bağlama

Özel DNS bölgesini sanal ağa bağlamak için bir sanal ağ bağlantısı oluşturmanız gerekir.

1. Yukarıda oluşturulan özel DNS bölgesi kaynağını seçin: **<span>Private.azuremicroservices.io</span>** 

2. Sol bölmede **sanal ağ bağlantıları**' nı seçin.

3. **Add (Ekle)** seçeneğini belirleyin.

4. **Bağlantı adı** için **Azure-Spring-Cloud-DNS-link** ' i girin.

5. **Sanal ağ** için, Azure [sanal ağınızda (VNet ekleme) Azure yay bulutu 'nı dağıtma](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)bölümünde açıklandığı gibi oluşturduğunuz sanal ağı seçin.

    ![Sanal ağ bağlantısı ekle](media/spring-cloud-access-app-vnet/add-virtual-network-link.png)

6. **Tamam**'a tıklayın.

## <a name="create-dns-record"></a>DNS kaydı oluştur

DNS 'i çevirmek/çözümlemek üzere özel DNS bölgesini kullanmak için, bölgede bir "A" tür kaydı oluşturmanız gerekir.

1. Azure [sanal ağınızda (VNet ekleme) Azure yay bulutu 'Nı dağıtma](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)bölümünde açıklandığı gibi oluşturduğunuz sanal ağ kaynağını seçin.

2. **Bağlı cihazlar** arama kutusuna *Kubernetes-Internal* yazın.

3. Filtrelenmiş sonuç ' de, hizmet örneğinin hizmet çalışma zamanı **alt ağına** bağlı **aygıtı** bulun ve **IP adresini** kopyalayın. Bu örnekte, IP adresi *10.1.0.7*' dir.

    [![DNS kaydı ](media/spring-cloud-access-app-vnet/create-dns-record.png) Oluştur](media/spring-cloud-access-app-vnet/create-dns-record.png)

Veya IP 'yi aşağıdaki az CLı komutunu kullanarak getirebilirsiniz:

```azurecli
SPRING_CLOUD_RG= # Resource group name of your Azure Spring Cloud service instance
SPRING_CLOUD= # Name of your Azure Spring Cloud service instance

SERVICE_RUNTIME_RG=`az spring-cloud show -g $SPRING_CLOUD_RG -n $SPRING_CLOUD --query \
"properties.networkProfile.serviceRuntimeNetworkResourceGroup" -o tsv`

IP_ADDRESS=`az network lb frontend-ip list --lb-name kubernetes-internal -g \
$SERVICE_RUNTIME_RG --query "[0].privateIpAddress" -o tsv`
```

4. Yukarıda oluşturulan özel DNS bölge kaynağını seçin: **<span>Private.azuremicroservices.io</span>**.

5. **+ Kayıt kümesi** seçeneğini belirleyin.

6. **Kayıt kümesi Ekle**' de, bu bilgileri girin veya seçin:

    |Ayar     |Değer                                                                      |
    |------------|---------------------------------------------------------------------------|
    |Ad        |Girmesini *\**                                                                 |
    |Tür        |**Bir** seçin                                                               |
    |TTL         |*1* girin                                                                  |
    |TTL birimi    |**Saatleri** seçin                                                           |
    |IP Adresi  |Adım 3 ' te kopyalanmış IP adresini girin. Örnekte, *10.1.0.7* girin.    |

    Ardından **Tamam**’ı seçin.

    ![Özel DNS bölge kaydı ekle](media/spring-cloud-access-app-vnet/private-dns-zone-add-record.png)

## <a name="assign-private-fqdn-for-your-application"></a>Uygulamanız için özel FQDN atama

[Mikro hizmet uygulamaları oluşturma ve dağıtma](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)bölümündeki yordamı tamamladıktan sonra uygulamanız IÇIN özel FQDN atayabilirsiniz.

1. Sanal ağınızda dağıtılan Azure yay bulut hizmeti örneğini seçin ve sol taraftaki menüden **uygulamalar** sekmesini açın.

2. **Genel bakış** sayfasını göstermek için uygulamayı seçin.

3. Uygulamanıza özel bir FQDN atamak için **uç nokta ata** ' yı seçin. Bu işlem birkaç dakika sürebilir.

    ![Özel uç nokta ata](media/spring-cloud-access-app-vnet/assign-private-endpoint.png)

4. Atanan özel FQDN ( **URL** etiketli) artık kullanılabilir. Yalnızca özel ağ içinden erişilebilir, ancak Internet üzerinde değil.

## <a name="access-application-private-fqdn"></a>Uygulamaya özel FQDN erişimi

Atamadan sonra, özel ağdaki uygulamanızın özel FQDN 'sine erişebilirsiniz. Örneğin, aynı sanal ağda veya eşlenmiş bir sanal ağda ve bu sıçrama kutusu makinesinde özel FQDN 'ye erişilebilir bir atlama kutusu makinesi oluşturabilirsiniz.

![VNET 'teki özel uç noktaya erişin](media/spring-cloud-access-app-vnet/access-private-endpoint.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Uygulamaları Internet 'Te kullanıma sunma-Application Gateway ve Azure Güvenlik duvarını kullanma](spring-cloud-expose-apps-gateway-azure-firewall.md)

## <a name="see-also"></a>Ayrıca bkz.

- [VNET 'te Azure Spring Cloud sorunlarını giderme](spring-cloud-troubleshooting-vnet.md)
- [VNET 'te Azure Spring Cloud çalıştırmaya yönelik müşteri sorumlulukları](spring-cloud-vnet-customer-responsibilities.md)