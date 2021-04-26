---
title: Azure DevTest Labs ve Azure Resource Manager şablonu kullanarak laboratuvar oluşturma
description: Bu hızlı başlangıçta, bir Azure Resource Manager şablonu (ARM şablonu) kullanarak Azure DevTest Labs bir laboratuvar oluşturacaksınız. Laboratuvar Yöneticisi laboratuvar yapar, laboratuvarda VM 'Ler oluşturur ve ilkeleri yapılandırır.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/26/2020
ms.openlocfilehash: 2b825b4d4485f401199556b6faaef0017f583cc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91461198"
---
# <a name="quickstart-set-up-a-lab-by-using-azure-devtest-labs-arm-template"></a>Hızlı başlangıç: Azure DevTest Labs ARM şablonunu kullanarak laboratuvar ayarlama
Bu hızlı başlangıçta, bir Azure Resource Manager şablonu (ARM şablonu) kullanarak Windows Server 2019 Datacenter VM ile bir laboratuvar oluşturacaksınız. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Bu hızlı başlangıçta aşağıdaki eylemleri gerçekleştirebilirsiniz:

> [!div class="checklist"]
> * Şablonu gözden geçirme 
> * Şablonu dağıtma
> * Şablonu doğrulama
> * Kaynakları temizleme

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-dtl-create-lab-windows-vm/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-dtl-create-lab-windows-vm/azuredeploy.json":::

Şablonda tanımlanan kaynaklar şunları içerir:

- [**Microsoft. DevTestLab/Labs**](/azure/templates/microsoft.devtestlab/labs)
- [**Microsoft. DevTestLab Labs/virtualnetworks**](/azure/templates/microsoft.devtestlab/labs/virtualnetworks)
- [**Microsoft. DevTestLab Labs/virtualmachines**](/azure/templates/microsoft.devtestlab/labs/virtualmachines)

Daha fazla şablon örneği bulmak için bkz. [Azure hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab).

## <a name="deploy-the-template"></a>Şablonu dağıtma
Dağıtımı otomatik olarak çalıştırmak için aşağıdaki düğmeye tıklayın. 

[![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

1. Daha sonra kolayca temizlemek için **Yeni bir kaynak grubu** oluşturun.
1. Kaynak grubu için bir **konum** seçin. 
1. **Laboratuvar için bir ad** girin. 
1. **VM için bir ad** girin. 
1. VM 'ye erişebilen bir **Kullanıcı adı** girin. 
1. Kullanıcının **parolasını** girin. 
1. **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum '** u seçin. 
1. Ardından **satın al**' ı seçin.

    :::image type="content" source="./media/create-lab-windows-vm-template/deploy-template-page.png" alt-text="Şablon dağıtma sayfası":::

## <a name="validate-the-deployment"></a>Dağıtımı doğrulama
1. Dağıtımın durumunu görmek için üstteki **Bildirimler** ' i seçin ve **devam eden dağıtım** bağlantısı ' na tıklayın.

    :::image type="content" source="./media/create-lab-windows-vm-template/deployment-notification.png" alt-text="Dağıtım bildirimi":::
2. **Dağıtım-genel bakış** sayfasında, dağıtım tamamlanana kadar bekleyin. Bu işlemin (özellikle, VM oluşturma) tamamlaması biraz zaman alır. Ardından, aşağıdaki görüntüde gösterildiği gibi kaynak grubuna **Git** veya **kaynak grubunun adını** seçin: 

    :::image type="content" source="./media/create-lab-windows-vm-template/navigate-resource-group.png" alt-text="Kaynak grubuna git":::
3. **Kaynak grubu** sayfasında kaynak grubundaki kaynakların listesini görürsünüz. Kaynak türünde laboratuvarınızı görtığınızdan emin olun `DevTest Lab` . Ayrıca, kaynak grubundaki sanal ağ ve sanal makine gibi bağımlı kaynakları da görürsünüz. 

    :::image type="content" source="./media/create-lab-windows-vm-template/resource-group-home-page.png" alt-text="Kaynak grubunun giriş sayfası":::
4. Laboratuvarınızın giriş sayfasını görmek için kaynak listesinden laboratuvarınızı seçin. **Sanal makinelerim** listesinde Windows Server 2019 Datacenter VM 'yi görtığınızdan emin olun. Aşağıdaki görüntüde **temel** bileşenler bölümü küçültülmüş. 

    :::image type="content" source="./media/create-lab-windows-vm-template/lab-home-page.png" alt-text="Laboratuvarın ana sayfası":::

    > [!IMPORTANT] 
    > Laboratuvar ve VM 'yi Azure üzerinde çalıştırmaya yönelik maliyetleri önlemek için bu sayfayı açık tutun ve sonraki bölümde yer alan yönergeleri izleyin. Laboratuvardaki VM 'ye erişimi test etmek için bir sonraki öğreticide gezinmek istiyorsanız, Bu öğreticiye geçtikten sonra kaynakları temizleyin. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

1. İlk olarak, kaynak grubunu silebilmeniz için Laboratuvarı silin. Kaynak grubunu içindeki bir laboratuvara silemezsiniz. Laboratuvarı silmek için araç çubuğunda **Sil** ' i seçin. 

    :::image type="content" source="./media/create-lab-windows-vm-template/delete-lab-button.png" alt-text="Laboratuvar düğmesini Sil":::
 2. Onay sayfasında, **Laboratuvar adını** yazın ve **Sil**' i seçin. 
 3. Laboratuvar silinene kadar bekleyin. Silme işleminden gelen bildirimleri görmek için **zil** simgesini seçin. Bu işlem biraz zaman alır. Laboratuvar silme işlemini onaylayın ve ardından içerik haritası menüsünde **kaynak grubunu** seçin. 
 
    :::image type="content" source="./media/create-lab-windows-vm-template/confirm-lab-deletion.png" alt-text="Bildirimlerde VM 'nin silinmesini onaylayın":::
 1. **Kaynak grubu** sayfasında, araç çubuğundan **kaynak grubunu sil** ' i seçin. Onay sayfasında, **kaynak grubu adını** yazın ve **Sil**' i seçin. Kaynak grubunun silindiğini onaylamak için bildirimleri kontrol edin.
 
    :::image type="content" source="./media/create-lab-windows-vm-template/delete-resource-group-button.png" alt-text="Kaynak grubunu Sil düğmesi":::

## <a name="next-steps"></a>Sonraki adımlar
Bu hızlı başlangıçta VM ile bir laboratuvar oluşturdunuz. Laboratuvara erişme hakkında daha fazla bilgi edinmek için sonraki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
> [Öğretici: Laboratuvara erişme](tutorial-use-custom-lab.md)
