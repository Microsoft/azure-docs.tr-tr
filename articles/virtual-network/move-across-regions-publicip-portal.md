---
title: Azure genel IP yapılandırmasını başka bir Azure bölgesine taşıyın Azure portal
description: Azure genel IP yapılandırmasını, Azure portal kullanarak bir Azure bölgesinden diğerine taşımak için bir şablon kullanın.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: b00fca8cf39bc44e0e53a112a332e6f6c5f0194e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98218612"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-the-azure-portal"></a>Azure genel IP yapılandırmasını Azure portal kullanarak başka bir bölgeye taşıma

Mevcut Azure genel IP yapılandırmalarınızı bir bölgeden diğerine taşımak istediğiniz çeşitli senaryolar vardır. Örneğin, test için aynı yapılandırma ve SKU ile genel bir IP oluşturmak isteyebilirsiniz. Ayrıca, olağanüstü durum kurtarma planlamasının bir parçası olarak genel bir IP yapılandırmasını başka bir bölgeye taşımak isteyebilirsiniz.

**Azure genel IP 'Leri bölgeye özeldir ve bir bölgeden diğerine taşınamaz.** Bununla birlikte, genel bir IP 'nin var olan yapılandırmasını dışarı aktarmak için bir Azure Resource Manager şablonu kullanabilirsiniz.  Daha sonra, genel IP 'yi bir şablona dışarı aktararak, hedef bölgeyle eşleşecek parametreleri değiştirerek ve sonra şablonu yeni bölgeye dağıtabilmeniz için kaynağı başka bir bölgede da oluşturabilirsiniz.  Kaynak Yöneticisi ve şablonlar hakkında daha fazla bilgi için bkz. [hızlı başlangıç: Azure Portal kullanarak Azure Resource Manager şablonları oluşturma ve dağıtma](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Önkoşullar

- Azure genel IP 'nin taşımak istediğiniz Azure bölgesinde olduğundan emin olun.

- Azure genel IP 'Leri bölgeler arasında taşınamaz.  Yeni genel IP 'yi hedef bölgedeki kaynaklarla ilişkilendirmeniz gerekir.

- Genel bir IP yapılandırmasını dışarı aktarmak ve başka bir bölgede genel IP oluşturmak üzere şablon dağıtmak için, ağ katılımcısı rolü veya daha yüksek bir sürümü gerekir.

- Kaynak ağ düzeni ve şu anda kullanmakta olduğunuz tüm kaynakları belirler. Bu düzen, yük dengeleyiciler, ağ güvenlik grupları (NSG 'ler) ve sanal ağlar dahil değildir ancak bunlarla sınırlı değildir.

- Azure aboneliğinizin, kullanılan hedef bölgede ortak IP 'Ler oluşturmanıza izin verdiğini doğrulayın. Gerekli kotayı sağlamak için desteğe başvurun.

- Aboneliğinizin bu işlem için genel IP 'lerin eklenmesini desteklemek için yeterli kaynağa sahip olduğundan emin olun.  Bkz. [Azure aboneliği ve hizmet sınırları, kotalar ve kısıtlamalar](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Hazırlama ve taşıma
Aşağıdaki adımlarda, bir Kaynak Yöneticisi şablonu kullanarak yapılandırma taşıma için genel IP 'nin nasıl hazırlanacağı ve Azure portal kullanarak genel IP yapılandırmasını hedef bölgeye nasıl taşıyacağınız gösterilmektedir.

### <a name="export-the-template-and-deploy-from-a-script"></a>Şablonu dışarı aktarma ve bir betikten dağıtma

1. [Azure Portal](https://portal.azure.com)  >  **kaynak gruplarında** oturum açın.
2. Kaynak ortak IP 'yi içeren kaynak grubunu bulun ve üzerine tıklayın.
3. > **ayarları**  >  **dışarı aktarma şablonu**' nu seçin.
4. **Şablonu dışarı aktar** dikey penceresinde **Dağıt** ' ı seçin.
5.   >  Çevrimiçi düzenleyicide dosya **parameters.js** açmak için şablon **düzenleme parametreleri** ' ne tıklayın.
8. Genel IP adının parametresini düzenlemek için, **parametre**  >  **değeri** altındaki özelliği kaynak genel IP adından hedef ortak IP 'nizin adına değiştirin, adın tırnak içinde olduğundan emin olun:

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```
8.  Düzenleyicide **Kaydet** ' e tıklayın.

9.    >  Çevrimiçi düzenleyicide dosya **template.js** açmak için şablon **düzenleme** şablonu ' na tıklayın.

10. Genel IP 'nin taşınacağı hedef bölgeyi düzenlemek için **kaynaklar** altındaki **Location** özelliğini değiştirin:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]
    ```

11. Bölge konum kodlarını almak için bkz. [Azure konumları](https://azure.microsoft.com/global-infrastructure/locations/).  Bir bölgenin kodu, alanı olmayan bölge adıdır, **Orta ABD**  =  **merkezileştirme**.

12. Ayrıca, isterseniz şablondaki diğer parametreleri değiştirebilir ve gereksinimlerinize bağlı olarak isteğe bağlıdır:

    * **SKU** -   >  dosyadaki **template.js** SKU **adı** özelliğini değiştirerek, yapılandırmadaki genel IP 'yi standart iken Basic veya Basic 'e dönüştürebilirsiniz:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        Temel ve standart SKU genel IP 'leri arasındaki farklılıklar hakkında daha fazla bilgi için bkz. [genel IP adresi oluşturma, değiştirme veya silme](./virtual-network-public-ip-address.md).

    * **Ortak IP ayırma yöntemi** ve **boşta kalma zaman aşımı** - **publicıpallocationmethod** özelliğini **dinamik** veya **statik** olarak **dinamik** olarak değiştirerek şablonda bu seçeneklerin her ikisini de değiştirebilirsiniz  . Boşta **kalma zaman aşımı özelliği,** istediğiniz tutara göre değiştirilebilir.  Varsayılan değer **4**' dir:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []

        ```

        Ayırma yöntemleri ve boşta kalma zaman aşımı değerleri hakkında daha fazla bilgi için bkz. [genel IP adresi oluşturma, değiştirme veya silme](./virtual-network-public-ip-address.md).


13. Çevrimiçi düzenleyicide **Kaydet** ' e tıklayın.

14.   >  Hedef genel IP 'nin dağıtılacağı aboneliği seçmek için temel bilgiler **aboneliği** ' ne tıklayın.

15.   >  Hedef genel IP 'nin dağıtılacağı kaynak grubunu seçmek için temel bilgiler **kaynak grubu** ' na tıklayın.  Hedef genel IP 'si için yeni bir kaynak grubu oluşturmak için **Yeni oluştur** ' a tıklayabilirsiniz.  Adın mevcut kaynak genel IP 'nin kaynak kaynak grubuyla aynı olmadığından emin olun.

16. **Temel**  >  IP 'nin dağıtılmasını istediğiniz hedef konuma göre, temel bilgilerin bir **konum** olarak ayarlandığını doğrulayın.

17. **Ayarların** , yukarıdaki parametreler düzenleyicisinde girdiğiniz adla eşleştiğini doğrulayın.

18. **Hüküm ve koşullar** altındaki kutuyu işaretleyin.

19. Hedef ortak IP 'yi dağıtmak için **satın al** düğmesine tıklayın.

## <a name="discard"></a>Vazgeç

Hedef ortak IP 'yi atmak isterseniz, hedef ortak IP 'yi içeren kaynak grubunu silin.  Bunu yapmak için, portalda panodaki kaynak grubunu seçin ve genel bakış sayfasının en üstünde **Sil** ' i seçin.

## <a name="clean-up"></a>Temizleme

Değişiklikleri uygulamak ve genel IP 'nin taşınmasını tamamlamak için kaynak genel IP veya kaynak grubunu silin. Bunu yapmak için, portalda panodaki genel IP veya kaynak grubunu seçin ve her sayfanın en üstünde **Sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir Azure genel IP 'sini bir bölgeden diğerine taşımış ve kaynak kaynakları temizledi.  Azure 'da bölgeler ve olağanüstü durum kurtarma arasında kaynakları taşıma hakkında daha fazla bilgi edinmek için bkz:


- [Kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure VM’lerini başka bir bölgeye taşıma](../site-recovery/azure-to-azure-tutorial-migrate.md)