---
title: Azure Spring Cloud 'ı bir sanal ağda dağıtma
description: Azure yay bulutu 'nı bir sanal ağda (VNet ekleme) dağıtın.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 07/21/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: b0ea5728618c7b69403fcc4c0a3575b70fac6038
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482611"
---
# <a name="deploy-azure-spring-cloud-in-a-virtual-network"></a>Azure Spring Cloud 'ı bir sanal ağda dağıtma

**Bu makale şu şekilde geçerlidir:** âœ "ï ̧ Java âœ" ï ̧ C #

Bu öğreticide, sanal ağınıza bir Azure Spring Cloud örneğinin nasıl dağıtılacağı açıklanmaktadır. Bu dağıtım bazen VNet ekleme olarak adlandırılır.

Dağıtım şunları sunar:

* Kurumsal networkâ € ‹ 'teki Internetâ € ‹ 'tan Azure Spring Cloud Apps ve hizmet çalışma zamanının yalıtımı.
* Azure, diğer sanal ağlardaki â € ‹ şirket içi veri merkezleri â € ‹ veya Azure Hizmetleri olan sistemlerle bulut etkileşimini ister.
* Müşterilerin Azure yay bulutu için gelen ve giden ağ iletişimlerini denetlemesine olanak sağlar.

> [!Note]
> Azure Sanal ağınızı, yalnızca yeni bir Azure yay bulut hizmeti örneği oluşturduğunuzda seçebilirsiniz. Azure yay bulutu oluşturulduktan sonra başka bir sanal ağ kullanmak için geçiş yapılamaz.

## <a name="prerequisites"></a>Önkoşullar

Azure yay bulut kaynak sağlayıcısı **Microsoft. AppPlatform** ve **Microsoft. Containerservice** [' i Azure Portal kaynak sağlayıcısını kaydetme](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) BÖLÜMÜNDEKI yönergelere göre veya aşağıdaki Azure CLI komutunu çalıştırarak kaydedin:

```azurecli
az provider register --namespace Microsoft.AppPlatform
az provider register --namespace Microsoft.ContainerService
```

## <a name="virtual-network-requirements"></a>Sanal ağ gereksinimleri

Azure yay bulutu örneğinizi dağıttığınız sanal ağ, aşağıdaki gereksinimleri karşılamalıdır:

* **Konum**: sanal ağın Azure Spring Cloud örneğiyle aynı konumda bulunması gerekir.
* **Abonelik**: sanal ağın Azure yay bulutu örneğiyle aynı abonelikte olması gerekir.
* **Alt ağlar**: sanal ağ, bir Azure yay bulutu örneğine adanmış iki alt ağ içermelidir:

    * Hizmet çalışma zamanı için bir tane.
    * Bir tane, Spring Boot mikro hizmet uygulamalarınız için.
    * Bu alt ağlar ve bir Azure yay bulutu örneği arasında bire bir ilişki vardır. Dağıttığınız her hizmet örneği için yeni bir alt ağ kullanın. Her alt ağ yalnızca tek bir hizmet örneği içerebilir.
* **Adres alanı**: CIDR, hem hizmet çalışma zamanı alt ağı hem de Spring Boot mikro hizmet uygulamaları alt ağı için */28* ' i engeller.
* **Yol tablosu**: varsayılan olarak alt ağlarda ilişkili mevcut yol tablolarının olması gerekmez. [Kendi yol tablonuzu getirebilirsiniz](#bring-your-own-route-table).

Aşağıdaki yordamlarda, sanal ağın Azure Spring Cloud örneğini içerecek şekilde kurulumu açıklanır.

## <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

Azure yay bulutu örneğini barındıracak bir sanal ağınız zaten varsa 1, 2 ve 3. adımları atlayın. Sanal ağ için alt ağları hazırlamak üzere 4. adımdan başlayabilirsiniz.

1. Azure portalı menüsünde **Kaynak oluştur**'u seçin. Azure Marketi 'nden **ağ**  >  **sanal ağı**' nı seçin.

1. **Sanal ağ oluştur** iletişim kutusunda aşağıdaki bilgileri girin veya seçin:

    |Ayar          |Değer                                             |
    |-----------------|--------------------------------------------------|
    |Abonelik     |Aboneliğinizi seçin.                         |
    |Kaynak grubu   |Kaynak grubunuzu seçin veya yeni bir tane oluşturun.  |
    |Name             |**Azure-Spring-Cloud-VNET** girin.                 |
    |Konum         |**Doğu ABD**’yi seçin.                               |

1. **İleri ' yi seçin: IP adresleri**.

1. IPv4 adres alanı için **10.1.0.0/16** girin.

1. **Alt ağ ekle**' yi seçin. Sonra, **alt ağ adı** için **hizmet-çalışma zamanı-alt ağını** girip **alt ağ adres aralığı** için **10.1.0.0/28** girin. Ardından **Ekle**'yi seçin.

1. **Alt ağ ekle** ' yi yeniden seçin ve sonra **alt ağ adı** ve **alt ağ adres aralığını** girin. Örneğin, **Apps-subnet** ve **10.1.1.0/28** yazın. Ardından **Ekle**'yi seçin.

1. **Gözden geçir ve oluştur**’u seçin. Rest 'i varsayılan olarak bırakın ve **Oluştur**' u seçin.

## <a name="grant-service-permission-to-the-virtual-network"></a>Sanal ağ için hizmet izni verme
Azure yay bulutu, daha fazla dağıtım ve bakım için sanal ağ üzerinde adanmış ve dinamik hizmet sorumlusu sağlamak üzere sanal ağınıza **sahip** izni gerektirir.

Daha önce oluşturduğunuz sanal ağı **Azure-Spring-Cloud-VNET** ' i seçin.

1. **Erişim denetimi (IAM)** öğesini seçin ve ardından   >  **Rol Ekle ataması** Ekle ' yi seçin.

    ![Erişim denetimi ekranını gösteren ekran görüntüsü.](./media/spring-cloud-v-net-injection/access-control.png)

1. **Rol ataması Ekle** iletişim kutusunda aşağıdaki bilgileri girin veya seçin:

    |Ayar  |Değer                                             |
    |---------|--------------------------------------------------|
    |Rol     |**Sahip** seçin.                                 |
    |Şunu seçin:   |**Azure Spring Cloud kaynak sağlayıcısı**'nı girin.   |

    Ardından **Azure yay bulut kaynak sağlayıcısı**' nı seçin ve **Kaydet**' i seçin.

    ![Azure yay bulut kaynak sağlayıcısını seçmeyi gösteren ekran görüntüsü.](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

Aşağıdaki Azure CLı komutunu çalıştırarak da bu adımı yapabilirsiniz:

```azurecli
VIRTUAL_NETWORK_RESOURCE_ID=`az network vnet show \
    --name ${NAME_OF_VIRTUAL_NETWORK} \
    --resource-group ${RESOURCE_GROUP_OF_VIRTUAL_NETWORK} \
    --query "id" \
    --output tsv`

az role assignment create \
    --role "Owner" \
    --scope ${VIRTUAL_NETWORK_RESOURCE_ID} \
    --assignee e8de9221-a19c-4c81-b814-fd37c6caf9d2
```

## <a name="deploy-an-azure-spring-cloud-instance"></a>Azure yay bulutu örneğini dağıtma

Sanal ağda bir Azure yay bulutu örneği dağıtmak için:

1. [Azure portalını](https://portal.azure.com) açın.

1. Üst arama kutusunda **Azure Spring Cloud**' ı arayın. Sonuçtan **Azure yay bulutu** ' nı seçin.

1. **Azure yay bulutu** sayfasında **+ Ekle**' yi seçin.

1. Azure yay bulutu **Oluştur** sayfasında formu doldurun.

1. Sanal ağla aynı kaynak grubunu ve bölgeyi seçin.

1. **Hizmet Ayrıntıları** altındaki **ad** için **Azure-Spring-Cloud-VNET**' i seçin.

1. **Ağ** sekmesini seçin ve aşağıdaki değerleri seçin:

    |Ayar                                |Değer                                             |
    |---------------------------------------|--------------------------------------------------|
    |Kendi sanal ağınıza dağıtın     |**Evet**’i seçin.                                   |
    |Sanal ağ                        |**Azure-Spring-Cloud-VNET**' i seçin.               |
    |Hizmet çalışma zamanı alt ağı                 |**Hizmet-çalışma zamanı-alt ağ**' ı seçin.                |
    |Spring Boot mikro hizmet uygulamaları alt ağı   |**Uygulamalar-alt ağ**' ı seçin.                           |

    ![Azure yay bulutu Oluştur sayfasındaki ağ sekmesini gösteren ekran görüntüsü.](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. **Gözden geçir ve Oluştur '** u seçin.

1. Belirtimlerinizi doğrulayıp **Oluştur**' u seçin.

    ![Belirtimlerin doğrulandığının gösterildiği ekran görüntüsü.](./media/spring-cloud-v-net-injection/verify-specifications.png)

Dağıtımdan sonra, Azure Spring Cloud örneğinin ağ kaynaklarını barındırmak için aboneliğinizde iki ek kaynak grubu oluşturulur. **Giriş**' e gidin ve ardından üstteki menü öğelerinden **kaynak grupları** ' nı seçerek aşağıdaki yeni kaynak gruplarını bulun.

**AP-svc-RT_ {Service Instance Name} _ {Service Instance Region}** olarak adlandırılan kaynak grubu, hizmet örneğinin hizmet çalışma zamanına ait ağ kaynaklarını içerir.

  ![Hizmet çalışma zamanını gösteren ekran görüntüsü.](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

**AP-App_ {Service Instance Name} _ {Service Instance Region}** olarak adlandırılan kaynak grubu, hizmet örneğinin Spring Boot mikro hizmet uygulamalarınızın ağ kaynaklarını içerir.

  ![Uygulamalar kaynak grubunu gösteren ekran görüntüsü.](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Bu ağ kaynakları, önceki görüntüde oluşturulan sanal ağınıza bağlanır.

  ![Bağlı cihazlara sahip sanal ağı gösteren ekran görüntüsü.](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > Kaynak grupları, Azure yay bulut hizmeti tarafından tam olarak yönetilir. İçindeki herhangi bir *kaynağı el ile silmeyin veya değiştirmeyin.*

## <a name="using-smaller-subnet-ranges"></a>Küçük alt ağ aralıklarını kullanma

Bu tabloda, Azure Spring Cloud 'ın daha küçük bir alt ağ aralığı kullanımını desteklediği en fazla uygulama örneği sayısı gösterilmektedir.

| Uygulama alt ağı CıDR | Toplam IP sayısı | Kullanılabilir IP 'Ler | En fazla uygulama örnekleri                                        |
| --------------- | --------- | ------------- | ------------------------------------------------------------ |
| /28             | 16        | 8             | <p> 1 çekirdekli uygulama: 96 <br/> 2 çekirdek içeren uygulama: 48<br/>  3 çekirdekle uygulama: 32 <br/> 4 çekirdek içeren uygulama: 24 </p> |
| /27             | 32        | 24            | <p> 1 çekirdekli uygulama: 228<br/> 2 çekirdek içeren uygulama: 144<br/>  3 çekirdekle uygulama: 96 <br/>  4 çekirdek içeren uygulama: 72</p> |
| /26             | 64        | 56            | <p> 1 çekirdekli uygulama: 500<br/> 2 çekirdek içeren uygulama: 336<br/>  3 çekirdekle uygulama: 224<br/>  4 çekirdek içeren uygulama: 168</p> |
| /25             | 128       | 120           | <p> 1 çekirdekli uygulama: 500<br> 2 çekirdek içeren uygulama: 500<br>  3 çekirdekle uygulama: 480<br>  4 çekirdek içeren uygulama: 360</p> |
| /24             | 256       | 248           | <p> 1 çekirdekli uygulama: 500<br/> 2 çekirdek içeren uygulama: 500<br/>  3 çekirdekle uygulama: 500<br/>  4 çekirdek içeren uygulama: 500</p> |

Alt ağlar için, beş IP adresi Azure tarafından ayrılmıştır ve Azure Spring Cloud için en az dört adres gereklidir. En az dokuz IP adresi gereklidir, bu nedenle/29 ve/30 işlem dışı.

Bir hizmet çalışma zamanı alt ağı için en küçük boyut/28 ' dir. Bu boyut, uygulama örneklerinin sayısı üzerinde bir pul içermez.

## <a name="bring-your-own-route-table"></a>Kendi yol tablonuzu getir

Azure yay bulutu, mevcut alt ağların ve yol tablolarının kullanımını destekler.

Özel alt ağlarınız rota tabloları içermiyorsa, Azure yay bulutu bunları her alt ağın her biri için oluşturur ve örnek yaşam döngüsü boyunca bunlara kurallar ekler. Özel alt ağlarınız rota tabloları içeriyorsa, Azure yay bulutu, örnek işlemleri sırasında mevcut yol tablolarını onaylar ve işlemler için uygun olarak/güncelleştirmeleri ve/veya kuralları ekler.

> [!Warning] 
> Özel kurallar özel yol tablolarına eklenebilir ve güncelleştirilir. Ancak, kurallar Azure Spring Cloud tarafından eklenir ve bunların güncellenmesi veya kaldırılması gerekir. 0.0.0.0/0 gibi kurallar her zaman belirli bir yol tablosunda bulunmalı ve bir NVA veya diğer çıkış ağ geçidi gibi internet ağ geçidinizin hedefine eşlenir. Yalnızca özel kurallarınız değiştirilirken kuralları güncelleştirirken dikkatli olun.


### <a name="route-table-requirements"></a>Rota tablosu gereksinimleri

Özel VNET 'nizin ilişkilendirildiği yol tablolarının aşağıdaki gereksinimleri karşılaması gerekir:

* Azure Route tablolarınızı yalnızca yeni bir Azure yay bulut hizmeti örneği oluşturduğunuzda sanal ağınız ile ilişkilendirebilirsiniz. Azure yay bulutu oluşturulduktan sonra başka bir yol tablosu kullanmak için geçiş yapılamaz.
* Hem mikro hizmet uygulama alt ağı hem de hizmet çalışma zamanı alt ağı farklı rota tabloları ile ilişkilendirmeli veya ikisi de değildir.
* Örnek oluşturulduktan önce izinler atanmalıdır. Azure *Spring Cloud Owner* iznini yol tablolarınıza verdiğinizden emin olun.
* İlişkili yol tablosu kaynağı, küme oluşturulduktan sonra güncelleştirilemez. Yol tablosu kaynağı güncelleştirilemediğinden, yönlendirme tablosunda özel kurallar değiştirilebilir.
* Olası çakışan yönlendirme kuralları nedeniyle birden çok örneğe sahip bir yol tablosunu yeniden kullanamazsınız.

## <a name="next-steps"></a>Sonraki adımlar

[VNet 'iniz üzerinde Azure Spring Cloud 'a uygulama dağıtma](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>Ayrıca bkz.

- [VNET 'te Azure Spring Cloud sorunlarını giderme](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [VNET 'te Azure Spring Cloud çalıştırmaya yönelik müşteri sorumlulukları](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)
