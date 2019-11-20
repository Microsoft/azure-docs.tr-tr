---
title: Kaynakları yönetme-Azure portal
description: Kaynaklarınızı yönetmek için Azure portal ve Azure Resource Manager kullanın. Kaynakların nasıl dağıtılacağını ve silineceğini gösterir.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 5c0e09930ee53733b36e987356cef2963416006f
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149832"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Azure portal kullanarak Azure kaynaklarını yönetme

Azure kaynaklarınızı yönetmek için [Azure portal](https://portal.azure.com) [Azure Resource Manager](resource-group-overview.md) nasıl kullanacağınızı öğrenin. Kaynak gruplarını yönetmek için, bkz. [Azure Portal kullanarak Azure kaynak gruplarını yönetme](./manage-resource-groups-portal.md).

Kaynakları yönetme hakkında diğer makaleler:

- [Azure CLı kullanarak Azure kaynaklarını yönetme](./manage-resources-cli.md)
- [Azure PowerShell kullanarak Azure kaynaklarını yönetme](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Kaynakları bir kaynak grubuna dağıtma

Kaynak Yöneticisi şablonu oluşturduktan sonra, Azure kaynaklarınızı dağıtmak için Azure portal kullanabilirsiniz. Şablon oluşturmak için bkz. [hızlı başlangıç: Azure Portal kullanarak Azure Resource Manager şablonları oluşturma ve dağıtma](./resource-manager-quickstart-create-templates-use-the-portal.md). Portalı kullanarak bir şablon dağıtmak için bkz. [Kaynak Yöneticisi şablonlarıyla kaynak dağıtma ve Azure Portal](resource-group-template-deploy-portal.md).

## <a name="open-resources"></a>Açık kaynaklar

Azure kaynakları, Azure hizmetleri ve kaynak grupları tarafından düzenlenir. Aşağıdaki yordamlarda **mystorage0207**adlı bir depolama hesabının nasıl açılacağı gösterilmektedir. Sanal makine, **mystorage0207rg**adlı bir kaynak grubunda bulunur.

Bir kaynağı hizmet türüne göre açmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Sol bölmede Azure hizmeti ' ni seçin. Bu durumda, **depolama hesapları**.  Listelenen hizmeti görmüyorsanız, **tüm hizmetler**' i seçin ve ardından hizmet türünü seçin.

    ![Portalda Azure kaynağını açın](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Açmak istediğiniz kaynağı seçin.

    ![Portalda Azure kaynağını açın](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Bir depolama hesabı şöyle görünür:

    ![Portalda Azure kaynağını açın](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Kaynak grubuna göre bir kaynağı açmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Grubun içindeki kaynağı listelemek için sol bölmede **kaynak grupları** ' nı seçin.
3. Açmak istediğiniz kaynağı seçin. 

## <a name="manage-resources"></a>Kaynakları yönetme

Portalda bir kaynağı görüntülerken, belirli bir kaynağı yönetme seçeneklerini görürsünüz.

![Azure kaynaklarını yönetme](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

Ekran görüntüsü, bir Azure sanal makinesi için yönetim seçeneklerini gösterir. Bir sanal makineyi başlatma, yeniden başlatma ve durdurma gibi işlemleri gerçekleştirebilirsiniz.

## <a name="delete-resources"></a>Kaynakları silme

1. Kaynağı portalda açın. Adımlar için bkz. [kaynakları açma](#open-resources).
2. **Sil**’i seçin. Aşağıdaki ekran görüntüsünde, bir sanal makine için yönetim seçenekleri gösterilmektedir.

    ![Azure kaynağını Sil](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Silmeyi onaylamak için kaynağın adını yazın ve ardından **Sil**' i seçin.

Kaynakların silinmesini Azure Resource Manager nasıl sipariş Azure Resource Manager hakkında daha fazla bilgi için bkz. [kaynak grubu silme](./resource-group-delete.md).

## <a name="move-resources"></a>Kaynakları taşıma

1. Kaynağı portalda açın. Adımlar için bkz. [kaynakları açma](#open-resources).
2. **Taşı**' yı seçin. Aşağıdaki ekran görüntüsünde bir depolama hesabının yönetim seçenekleri gösterilmektedir.

    ![Azure kaynağını taşıma](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. **Başka bir kaynak grubuna geçme** veya gereksinimlerinize bağlı olarak **başka bir aboneliğe taşıma** ' yı seçin.

Daha fazla bilgi için bkz. [Kaynakları yeni kaynak grubuna veya aboneliğe taşıma](resource-group-move-resources.md).

## <a name="lock-resources"></a>Kaynakları kilitleme

Kilitleme, kuruluşunuzdaki diğer kullanıcıların Azure aboneliği, kaynak grubu veya kaynak gibi önemli kaynakları yanlışlıkla silmesini veya değiştirmelerini engeller. 

1. Kaynağı portalda açın. Adımlar için bkz. [kaynakları açma](#open-resources).
2. **Kilitleri**seçin. Aşağıdaki ekran görüntüsünde bir depolama hesabının yönetim seçenekleri gösterilmektedir.

    ![Azure kaynağını kilitle](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. **Ekle**' yi seçin ve ardından kilit özelliklerini belirtin.

Daha fazla bilgi için bkz. [Azure Resource Manager ile kaynakları kilitleme](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Kaynakları etiketleme

Etiketleme, kaynak grubunuzun ve kaynaklarınızın mantıksal olarak düzenlenmesine yardımcı olur. 

1. Kaynağı portalda açın. Adımlar için bkz. [kaynakları açma](#open-resources).
2. **Etiketler**'i seçin. Aşağıdaki ekran görüntüsünde bir depolama hesabının yönetim seçenekleri gösterilmektedir.

    ![Azure kaynağını etiketleme](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Etiket özelliklerini belirtin ve ardından **Kaydet**' i seçin.

Daha fazla bilgi için bkz. [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](./resource-group-using-tags.md#portal).

## <a name="monitor-resources"></a>Kaynakları izleme

Bir kaynağı açtığınızda Portal, kaynak türünü izlemeye yönelik varsayılan grafikleri ve tabloları sunar. Aşağıdaki ekran görüntüsünde bir sanal makinenin grafikleri gösterilmektedir:

![Azure kaynağını izleme](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

Grafiği panoya sabitlemek için grafiklerin sağ üst köşesindeki sabitleme simgesini seçebilirsiniz. Panolarla çalışma hakkında bilgi edinmek için, bkz. [Azure Portal panoları oluşturma ve paylaşma](../azure-portal/azure-portal-dashboards.md).

## <a name="manage-access-to-resources"></a>Kaynaklara erişimi yönetme

[Rol tabanlı erişim denetimi (RBAC)](../role-based-access-control/overview.md), Azure'daki kaynaklara erişimi yönetmek için kullanılan sistemdir. Daha fazla bilgi için bkz. [RBAC ve Azure portalını kullanarak erişimi yönetme](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Sonraki adımlar

- Azure Resource Manager öğrenmek için bkz. [Azure Resource Manager genel bakış](./resource-group-overview.md).
- Kaynak Yöneticisi Şablon sözdizimini öğrenmek için bkz. [Azure Resource Manager şablonlarının yapısını ve sözdizimini anlayın](./resource-group-authoring-templates.md).
- Şablon geliştirmeyi öğrenmek için [adım adım öğreticiler](/azure/azure-resource-manager/)bölümüne bakın.
- Azure Resource Manager şablonu şemalarını görüntülemek için bkz. [şablon başvurusu](/azure/templates/).
