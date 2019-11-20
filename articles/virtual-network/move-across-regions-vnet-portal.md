---
title: Azure portal kullanarak bir Azure sanal ağını başka bir Azure bölgesine taşıyın.
description: Bir Azure sanal ağını bir Azure bölgesinden diğerine, Kaynak Yöneticisi şablonu ve Azure portal kullanarak taşıyın.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: d6f417e53e7d7a1a242a0c0dc56c2356f78f5344
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828960"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-the-azure-portal"></a>Azure portal kullanarak bir Azure sanal ağını başka bir bölgeye taşıma

Mevcut bir Azure sanal ağını bir bölgeden diğerine taşımaya yönelik çeşitli senaryolar vardır. Örneğin, var olan sanal ağınız ile test ve kullanılabilirlik için aynı yapılandırmaya sahip bir sanal ağ oluşturmak isteyebilirsiniz. Ya da bir üretim sanal ağını olağanüstü durum kurtarma planlamanızın bir parçası olarak başka bir bölgeye taşımak isteyebilirsiniz.

Sanal ağı başka bir bölgeye taşımayı gerçekleştirmek için bir Azure Resource Manager şablonu kullanabilirsiniz. Bunu, sanal ağı bir şablona vererek, parametreleri hedef bölgeyle eşleşecek şekilde değiştirerek ve sonra şablonu yeni bölgeye dağıtarak yapabilirsiniz. Kaynak Yöneticisi şablonları hakkında daha fazla bilgi için bkz. [hızlı başlangıç: Azure Portal kullanarak Azure Resource Manager şablonları oluşturma ve dağıtma](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Önkoşullar

- Sanal ağınızın, taşımak istediğiniz Azure bölgesinde olduğundan emin olun.

- Bir sanal ağı dışa aktarmak ve başka bir bölgede bir sanal ağ oluşturmak üzere bir şablon dağıtmak için, ağ katılımcısı rolüne veya daha üst bir sürüme sahip olmanız gerekir.

- Sanal ağ eşlemeleri yeniden oluşturulmayacak ve şablonda hala mevcutsa başarısız olur. Şablonu dışarı aktarmadan önce tüm sanal ağ eşlerini kaldırmanız gerekir. Daha sonra sanal ağ taşıdıktan sonra yeniden kurabilirsiniz.

- Kaynak ağ düzeni ve şu anda kullanmakta olduğunuz tüm kaynakları belirler. Bu düzen, yük dengeleyiciler, ağ güvenlik grupları (NSG 'Ler) ve genel IP 'Leri içerir ancak bunlarla sınırlı değildir.

- Azure aboneliğinizin hedef bölgede sanal ağlar oluşturmanıza izin verdiğini doğrulayın. Gerekli kotayı etkinleştirmek için desteğe başvurun.

- Aboneliğinizin bu işleme yönelik sanal ağların eklenmesini desteklemek için yeterli kaynağa sahip olduğundan emin olun. Daha fazla bilgi için bkz. [Azure aboneliği ile hizmet limitleri, kotalar ve kısıtlamalar](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits).


## <a name="prepare-for-the-move"></a>Taşıma için hazırlanma
Bu bölümde, Kaynak Yöneticisi şablonu kullanarak taşıma için sanal ağı hazırlarsınız. Daha sonra Azure portal kullanarak sanal ağı hedef bölgeye taşıyabilirsiniz.

Sanal ağı dışa aktarmak ve Azure portal kullanarak hedef sanal ağı dağıtmak için aşağıdakileri yapın:

1. [Azure Portal](https://portal.azure.com)oturum açın ve **kaynak grupları**' nı seçin.
1. Kaynak sanal ağı içeren kaynak grubunu bulun ve ardından seçin.
1. @No__t **ayarları**seçin-1**şablonu dışarı aktar**.
1. **Şablonu dışarı aktar** bölmesinde **Dağıt**' ı seçin.
1. Çevrimiçi Düzenleyicinizde *Parameters. JSON* dosyasını açmak için **şablon** > **parametreleri Düzenle**' yi seçin.
1. Sanal ağ adının parametresini düzenlemek için, **Parametreler**altındaki **değer** özelliğini değiştirin:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```

1. Düzenleyicide, düzenleyicideki kaynak sanal ağ adı değerini hedef sanal ağ için istediğiniz bir adla değiştirin. Adı tırnak işaretleri içine aldığınızdan emin olun.

1. Düzenleyicide **Kaydet** ' i seçin.

1. Çevrimiçi düzenleyicide *Template. JSON* dosyasını açmak için **şablon** > **Şablonu Düzenle**' yi seçin.

1. Çevrimiçi düzenleyicide, sanal ağın taşınacağı hedef bölgeyi düzenlemek için **kaynaklar**altındaki **Location** özelliğini değiştirin:

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```

1. Bölge konum kodlarını almak için bkz. [Azure konumları](https://azure.microsoft.com/global-infrastructure/locations/). Bölge için kod, boşluk olmadan (örneğin, **Orta ABD** = **merkezileştirme**) bölge adıdır.

1. Seçim Gereksinimlerinize bağlı olarak, şablondaki diğer parametreleri de değiştirebilirsiniz:

    * **Adres alanı**: dosyayı kaydetmeden önce, **kaynak** > **Adresalanı** bölümünü değiştirerek ve **addresspredüzeltmelerinin** özelliğini değiştirerek sanal ağın adres alanını değiştirebilirsiniz:

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },

        ```

    * **Alt ağ**: şablonun **alt ağlar** bölümünü değiştirerek alt ağ adını ve alt ağ adres alanını değiştirebilir veya ekleyebilirsiniz. **Ad** özelliğini değiştirerek alt ağın adını değiştirebilirsiniz. Ve **Addresspredüzeltmesini** özelliğini değiştirerek alt ağ adres alanını değiştirebilirsiniz:

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

        *Template. JSON* dosyasındaki adres önekini değiştirmek için, bu dosyayı iki yerde düzenleyin: önceki bölümdeki kodda ve aşağıdaki kodun **tür** bölümünde. Aşağıdaki kodda **Addresspredüzeltmesini** özelliğini, önceki bölümdeki koddaki **addresspredüzeltmesini** özelliğiyle eşleşecek şekilde değiştirin.

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

1. Çevrimiçi düzenleyicide **Kaydet**' i seçin.

1. Hedef sanal ağın dağıtılacağı aboneliği seçmek için **temel** > **aboneliği**' ni seçin.

1. Hedef sanal ağın dağıtılacağı kaynak grubunu seçmek için **temel** > **kaynak grubu**' nu seçin. 

    Hedef sanal ağ için yeni bir kaynak grubu oluşturmanız gerekiyorsa, **Yeni oluştur**' u seçin. Adın, var olan sanal ağdaki kaynak kaynak grubu adıyla aynı olmadığından emin olun.

1. **Temel** > **konumunun** , sanal ağın dağıtılmasını istediğiniz hedef konuma ayarlandığını doğrulayın.

1. **Ayarlar**' ın altında, ad ' ın daha önce parametreler düzenleyicisinde girdiğiniz adla eşleştiğini doğrulayın.

1. **Hüküm ve koşullar** onay kutusunu seçin.

1. Hedef sanal ağı dağıtmak için **satın al**' ı seçin.

## <a name="delete-the-target-virtual-network"></a>Hedef sanal ağı silme

Hedef sanal ağı atmak için, hedef sanal ağı içeren kaynak grubunu silersiniz. Bunu yapmak için:
1. Azure portal panosunda kaynak grubunu seçin.
1. **Genel bakış** bölmesinin üst kısmında **Sil**' i seçin.

## <a name="clean-up"></a>Temizleme

Değişiklikleri uygulamak ve sanal ağ taşımayı tamamlamak için kaynak sanal ağını veya kaynak grubunu silersiniz. Bunu yapmak için:
1. Azure portal panosunda, sanal ağ veya kaynak grubunu seçin.
1. Her bölmenin üst kısmında **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir Azure sanal ağını Azure portal kullanarak bir bölgeden diğerine taşımış sonra gereksiz kaynak kaynaklarını temizlülüsiniz. Azure 'da bölgeler ve olağanüstü durum kurtarma arasında kaynakları taşıma hakkında daha fazla bilgi edinmek için bkz.:


- [Kaynakları yeni kaynak grubuna veya aboneliğe taşıma](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure sanal makinelerini başka bir bölgeye taşıma](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
