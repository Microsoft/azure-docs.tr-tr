---
title: 'Öğretici: kural altyapısını Yapılandırma-Azure ön kapısı'
description: Bu makalede, hem Azure portal hem de CLı 'de Rules Engine 'in nasıl yapılandırılacağı hakkında bir öğretici sunulmaktadır.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/09/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: e64f27480126fdf444f8dd72abc2d66c78b30407
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868816"
---
# <a name="tutorial-configure-your-rules-engine"></a>Öğretici: kurallar altyapısını yapılandırma

Bu öğreticide, hem Azure portal hem de CLı 'de bir kural Altyapısı yapılandırması ve ilk kuralınız oluşturma gösterilmektedir. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> - Portalı kullanarak kural altyapısını yapılandırın.
> - Azure CLı kullanarak kural altyapısını yapılandırma

## <a name="prerequisites"></a>Önkoşullar

* Bu öğreticideki adımları tamamlayabilmeniz için öncelikle bir Front Door oluşturmanız gerekir. Daha fazla bilgi için bkz. [Hızlı başlangıç: Front Door oluşturma](quickstart-create-front-door.md).

## <a name="configure-rules-engine-in-azure-portal"></a>Azure portal kural altyapısını yapılandırma
1. Ön kapı kaynağınız içinde **Ayarlar** ' a gidin ve **kural Altyapısı yapılandırması**' nı seçin. **Ekle**' ye tıklayın, yapılandırmanıza bir ad verin ve Ilk kural altyapısı yapılandırmanızı oluşturmaya başlayın.

    ![Ön kapı ayarları menüsü](./media/front-door-rules-engine/rules-engine-tutorial-1.png)

1. İlk kuralınızı oluşturmak için **Kural Ekle** ' ye tıklayın. Ardından, **Koşul Ekle** veya **Eylem Ekle** ' ye tıklayarak kuralınızı tanımlayabilirsiniz.
    
    > [!NOTE]
    >- Kuraldan bir koşul veya eylem silmek için, belirli koşulun veya eylemin sağ tarafında çöp kutusu ' nu kullanın.
    > - Tüm gelen trafiğe uygulanan bir kural oluşturmak için herhangi bir koşul belirtmeyin.
    > - İlk eşleşme koşulu karşılandığında kuralları değerlendirmeyi durdurmak için, **kalan kuralı değerlendirmeyi durdur**' u işaretleyin. Bu işaretlenirse ve belirli bir kuralın eşleşme koşulları karşılanıyorsa, yapılandırmadaki kalan kurallar yürütülmez.  

    ![Kural Altyapısı yapılandırması](./media/front-door-rules-engine/rules-engine-tutorial-4.png) 

1. Yukarı taşı, aşağı taşı ve üste Taşı düğmelerini kullanarak yapılandırmanızın içindeki kuralların önceliğini belirleme. Öncelik artan sıradadır, yani ilk listelenen kural en önemli kuraldır.

1. Bir veya daha fazla kural oluşturduktan sonra **Kaydet**' e basın. Bu eylem, kural altyapısı yapılandırmanızı oluşturur.

1. Bir veya daha fazla yapılandırma oluşturduktan sonra, bir kural altyapısı yapılandırmasını bir yol kuralıyla ilişkilendirin. Tek bir yapılandırma birçok yol kuralına uygulanamadığından, bir yol kuralında yalnızca bir kural Altyapısı yapılandırması bulunabilir. İlişkilendirmeyi yapmak için **ön kapılı tasarımcı**  >  **yol kurallarınızın** bölümüne gidin. Kural altyapısı yapılandırmasını eklemek istediğiniz yol kuralını seçin, **yol ayrıntıları**  >  **kuralları altyapı yapılandırması**' na gidin ve ilişkilendirmek istediğiniz yapılandırmayı seçin.

    ![Bir yönlendirme kuralına yapılandırma](./media/front-door-rules-engine/rules-engine-tutorial-5.png)


## <a name="configure-rules-engine-in-azure-cli"></a>Azure CLı 'de kural altyapısını yapılandırma

1. Henüz yapmadıysanız [Azure CLI](/cli/azure/install-azure-cli)'yı yükleyemezsiniz. "Ön kapı" uzantısı ekleyin:-az Extension Add--Name ön kapısı. Ardından, oturum açın ve aboneliğinize geçiş yapın az Account set--Subscription <name_or_Id>.

1. Bir kural altyapısı oluşturarak başlayın-Bu örnekte, bir üst bilgi tabanlı eyleme ve bir eşleşme koşuluna sahip bir kural gösterilmektedir. 

    ```azurecli-interactive
    az network front-door rules-engine rule create -f {front_door} -g {resource_group} --rules-engine-name {rules_engine} --name {rule1} --priority 1 --action-type RequestHeader --header-action Overwrite --header-name Rewrite --header-value True --match-variable RequestFilenameExtension --operator Contains --match-values jpg png --transforms Lowercase
    ```

1. Tüm kuralları listeleyin. 

    ```azurecli-interactive
    az network front-door rules-engine rule list -f {front_door} -g {rg} --name {rules_engine}
    ```

1. Bir iletme yolu geçersiz kılma eylemi ekleyin. 

    ```azurecli-interactive
    az network front-door rules-engine rule action add -f {front_door} -g {rg} --rules-engine-name {rules_engine} --name {rule1} --action-type ForwardRouteOverride --backend-pool {backend_pool_name} --caching Disabled
    ```

1. Bir kuraldaki tüm eylemleri listeleyin. 

    ```azurecli-interactive
    az network front-door rules-engine rule action list -f {front_door} -g {rg} -r {rules_engine} --name {rule1}
    ```

1. Bir kural altyapısı yapılandırmasını bir yönlendirme kuralına bağlayın.  

    ```azurecli-interactive
    az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --rules-engine {rules_engine}
    ```

1. Kural altyapısının bağlantısını kaldır. 

    ```azurecli-interactive
    az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --remove rulesEngine # case sensitive word ‘rulesEngine’
    ```

Daha fazla bilgi için, [burada](/cli/azure/network/front-door/rules-engine)AFD kuralları altyapı komutlarının tam listesini bulabilirsiniz.   

## <a name="clean-up-resources"></a>Kaynakları temizleme

Yukarıdaki adımlarda, kural altyapısı yapılandırmasını Yönlendirme kurallarınız için yapılandırdığınıza ve bu kuralları ilişkilendirdiyseniz. Artık kural altyapısı yapılandırmasının ön kapısından ilişkilendirilmesini istemiyorsanız, aşağıdaki adımları gerçekleştirerek yapılandırmayı kaldırabilirsiniz:

1. Kural altyapısı adı ' nın yanındaki üç noktaya tıklayarak, kural altyapısı yapılandırmasından herhangi bir yönlendirme kuralının ilişkisini kaldırın.

    :::image type="content" source="./media/front-door-rules-engine/front-door-rule-engine-routing-association.png" alt-text="Yönlendirme kurallarını ilişkilendir":::

1. Bu kural altyapısı yapılandırmasının ilişkilendirildiği tüm yönlendirme kurallarının işaretini kaldırın ve Kaydet ' e tıklayın.

    :::image type="content" source="./media/front-door-rules-engine/front-door-routing-rule-association.png" alt-text="Yönlendirme kuralı ilişkilendirmesi":::

1. Artık, kural altyapısı yapılandırmasını ön kapısından silebilirsiniz.

    :::image type="content" source="./media/front-door-rules-engine/front-door-delete-rule-engine-configuration.png" alt-text="Kural altyapısı yapılandırmasını sil":::

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

* Kural Altyapısı yapılandırması oluşturma
* Yapılandırmayı ön kapı yönlendirme kurallarınız ile ilişkilendirin.

Kural altyapısına güvenlik üstbilgileri ekleme hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Kural altyapısı ile güvenlik üstbilgileri](front-door-security-headers.md)