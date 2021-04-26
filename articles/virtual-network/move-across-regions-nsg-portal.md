---
title: Azure ağ güvenlik grubu (NSG) Azure portal kullanarak başka bir Azure bölgesine taşıma
description: Azure portal kullanarak Azure ağ güvenlik grubunu bir Azure bölgesinden diğerine taşımak için Azure Resource Manager şablonu kullanın.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 11b0260eb6c1f5edfb246dcf97a43bdd874bf337
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98217286"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>Azure ağ güvenlik grubu (NSG) Azure portal kullanarak başka bir bölgeye taşıma

Mevcut NSG 'lerinizi bir bölgeden diğerine taşımak istediğiniz çeşitli senaryolar vardır. Örneğin, test için aynı yapılandırma ve güvenlik kurallarına sahip bir NSG oluşturmak isteyebilirsiniz. Ayrıca, olağanüstü durum kurtarma planlamasının bir parçası olarak bir NSG 'yi başka bir bölgeye taşımak isteyebilirsiniz.

Azure güvenlik grupları bir bölgeden diğerine taşınamaz. Bununla birlikte, bir NSG 'nin mevcut yapılandırma ve güvenlik kurallarını dışarı aktarmak için bir Azure Resource Manager şablonu kullanabilirsiniz.  Daha sonra, NSG 'yi bir şablona dışarı aktararak, parametreleri hedef bölgeyle eşleşecek şekilde değiştirerek ve sonra şablonu yeni bölgeye dağıtabilmeniz için kaynağı başka bir bölgede aşamalandırın.  Kaynak Yöneticisi ve şablonlar hakkında daha fazla bilgi için bkz. [hızlı başlangıç: Azure Portal kullanarak Azure Resource Manager şablonları oluşturma ve dağıtma](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Önkoşullar

- Azure ağ güvenlik grubunun, taşımak istediğiniz Azure bölgesinde olduğundan emin olun.

- Azure ağ güvenlik grupları bölgeler arasında taşınamaz.  Yeni NSG 'yi hedef bölgedeki kaynaklarla ilişkilendirmeniz gerekir.

- Bir NSG yapılandırmasını dışarı aktarmak ve başka bir bölgede NSG oluşturmak üzere bir şablon dağıtmak için, ağ katılımcısı rolü veya daha yüksek bir sürümü gerekir.

- Kaynak ağ düzeni ve şu anda kullanmakta olduğunuz tüm kaynakları belirler. Bu düzen, yük dengeleyiciler, genel IP 'Ler ve sanal ağlar dahil değildir ancak bunlarla sınırlı değildir.

- Azure aboneliğinizin, kullanılan hedef bölgede NSG 'ler oluşturmanıza izin verdiğini doğrulayın. Gerekli kotayı sağlamak için desteğe başvurun.

- Aboneliğinizin bu işlem için NSG 'lerin eklenmesini desteklemek için yeterli kaynağa sahip olduğundan emin olun.  Bkz. [Azure aboneliği ve hizmet sınırları, kotalar ve kısıtlamalar](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Hazırlama ve taşıma
Aşağıdaki adımlar, yapılandırma ve güvenlik kuralı için ağ güvenlik grubunun nasıl hazırlanacağını bir Kaynak Yöneticisi şablonu kullanarak taşıma ve NSG yapılandırma ve güvenlik kurallarını Portal kullanarak hedef bölgeye taşıma işlemlerinin nasıl yapılacağını göstermektedir.


### <a name="export-the-template-and-deploy-from-the-portal"></a>Şablonu dışarı aktarma ve portaldan dağıtma

1. [Azure Portal](https://portal.azure.com)  >  **kaynak gruplarında** oturum açın.
2. Kaynak NSG 'yi içeren kaynak grubunu bulun ve üzerine tıklayın.
3. > **ayarları**  >  **dışarı aktarma şablonu**' nu seçin.
4. **Şablonu dışarı aktar** dikey penceresinde **Dağıt** ' ı seçin.
5.   >  Çevrimiçi düzenleyicide dosya **parameters.js** açmak için şablon **düzenleme parametreleri** ' ne tıklayın.
6. NSG adının parametresini düzenlemek için, **Parametreler** altındaki **Value** özelliğini değiştirin:

    ```json
            {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
               "value": "<target-nsg-name>"
                }
               }
            }
    ```

7. Düzenleyicideki kaynak NSG değerini hedef NSG için seçtiğiniz bir adla değiştirin. Adı tırnak içine aldığınızdan emin olun.

8.  Düzenleyicide **Kaydet** ' e tıklayın.

9.    >  Çevrimiçi düzenleyicide dosya **template.js** açmak için şablon **düzenleme** şablonu ' na tıklayın.

10. NSG yapılandırma ve güvenlik kurallarının taşınacağı hedef bölgeyi düzenlemek için, çevrimiçi düzenleyicideki **kaynaklar** altındaki **Location** özelliğini değiştirin:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
             }
            }
           ]

    ```

11. Bölge konum kodlarını almak için bkz. [Azure konumları](https://azure.microsoft.com/global-infrastructure/locations/).  Bir bölgenin kodu, alanı olmayan bölge adıdır, **Orta ABD**  =  **merkezileştirme**.

12. Ayrıca, isterseniz şablondaki diğer parametreleri değiştirebilir ve gereksinimlerinize bağlı olarak isteğe bağlıdır:

    * **Güvenlik kuralları** -dosyadaki **template.js** **SecurityRules** bölümüne kural ekleyerek veya kaldırarak hedef NSG 'ye dağıtılan kuralları düzenleyebilirsiniz:

        ```json
           "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "TCP",
                            "sourcePortRange": "*",
                            "destinationPortRange": "3389",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 300,
                            "direction": "Inbound",
                            "sourcePortRanges": [],
                            "destinationPortRanges": [],
                            "sourceAddressPrefixes": [],
                            "destinationAddressPrefixes": []
                             }
                    },
                ]
            }
        ```

      Hedef NSG 'deki kuralların eklenmesini veya kaldırılmasını bitirmek için, aşağıdaki örnekte yer alan **template.js** dosya sonundaki özel kural türlerini de düzenlemeniz gerekir:

      ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
      ```

13. Çevrimiçi düzenleyicide **Kaydet** ' e tıklayın.

14.   >  Hedef NSG 'nin dağıtılacağı aboneliği seçmek için temel bilgiler **aboneliği** ' ne tıklayın.

15.   >  Hedef NSG 'nin dağıtılacağı kaynak grubunu seçmek için temel bilgiler **kaynak grubu** ' na tıklayın.  Hedef NSG için yeni bir kaynak grubu oluşturmak için **Yeni oluştur** ' a tıklayabilirsiniz.  Adın mevcut NSG kaynak kaynak grubuyla aynı olmadığından emin olun.

16. **Temel bilgilerin**  >  **,** NSG 'nin dağıtılmasını istediğiniz hedef konuma ayarlandığını doğrulayın.

17. **Ayarların** , yukarıdaki parametreler düzenleyicisinde girdiğiniz adla eşleştiğini doğrulayın.

18. **Hüküm ve koşullar** altındaki kutuyu işaretleyin.

19. Hedef ağ güvenlik grubunu dağıtmak için **satın al** düğmesine tıklayın.

## <a name="discard"></a>Vazgeç

Hedef NSG 'yi atmak istiyorsanız hedef NSG 'yi içeren kaynak grubunu silin.  Bunu yapmak için, portalda panodaki kaynak grubunu seçin ve genel bakış sayfasının en üstünde **Sil** ' i seçin.

## <a name="clean-up"></a>Temizleme

Değişiklikleri uygulamak ve NSG 'nin taşınmasını tamamlamak için kaynak NSG 'yi veya kaynak grubunu silin. Bunu yapmak için, portalda panodaki ağ güvenlik grubunu veya kaynak grubunu seçin ve her sayfanın en üstünde **Sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir Azure ağ güvenlik grubunu bir bölgeden diğerine taşıdı ve kaynak kaynakları temizledi.  Azure 'da bölgeler ve olağanüstü durum kurtarma arasında kaynakları taşıma hakkında daha fazla bilgi edinmek için bkz:


- [Kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure VM’lerini başka bir bölgeye taşıma](../site-recovery/azure-to-azure-tutorial-migrate.md)