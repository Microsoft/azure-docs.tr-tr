---
title: Azure CLı ile Azure portal panosu oluşturma
description: 'Hızlı başlangıç: Azure CLı kullanarak Azure portal Pano oluşturmayı öğrenin. Pano, bulut kaynaklarınızın odaklanmış ve düzenlenmiş bir görünümüdür.'
ms.topic: quickstart
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.date: 12/4/2020
ms.openlocfilehash: 581c8cc4c2da275467bc39c5c2008b29a5bc0e0e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875764"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-azure-cli"></a>Hızlı başlangıç: Azure CLı ile Azure portal panosu oluşturma

Azure portal bir Pano, bulut kaynaklarınızın odaklanmış ve düzenlenmiş bir görünümüdür. Bu makalede, bir pano oluşturmak için Azure CLı kullanma işlemine odaklanılır.
Pano, bir sanal makinenin (VM) ve bazı statik bilgi ve bağlantıların performansını gösterir.


[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Birden çok Azure aboneliğiniz varsa, kaynakların faturalandırılbileceği uygun aboneliği seçin.
[Az Account set](/cli/azure/account#az_account_set) komutunu kullanarak bir abonelik seçin:

  ```azurecli
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```

- [Az Group Create](/cli/azure/group#az_group_create) komutunu kullanarak bir [Azure Kaynak grubu](../azure-resource-manager/management/overview.md) oluşturun veya var olan bir kaynak grubunu kullanın:

  ```azurecli
  az group create --name myResourceGroup --location centralus
  ```

   Kaynak grubu, Azure kaynaklarının grup olarak dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

[Az VM Create](/cli/azure/vm#az_vm_create) komutunu kullanarak bir sanal makine oluşturun:

```azurecli
az vm create --resource-group myResourceGroup --name SimpleWinVM --image win2016datacenter \
   --admin-username azureuser --admin-password 1StrongPassword$
```

> [!Note]
> Parolanın karmaşık olması gerekir.
> Bu yeni bir Kullanıcı adı ve paroladır.
> Örneğin, Azure 'da oturum açmak için kullandığınız hesap değildir.
> Daha fazla bilgi için bkz. [Kullanıcı adı gereksinimleri](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) ve [Parola gereksinimleri](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

Dağıtım başlar ve genellikle birkaç dakika sürer.
Dağıtım tamamlandıktan sonra bir sonraki bölüme geçin.

## <a name="download-the-dashboard-template"></a>Pano şablonunu indirme

Azure panoları kaynak olduğundan, bu değerler JSON olarak temsil edilebilir.
Daha fazla bilgi için bkz. [Azure panoları yapısı](./azure-portal-dashboards-structure.md).

Şu dosyayı indirin: [ üzerindeportal-dashboard-template-testvm.js](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json).

Aşağıdaki değerleri değerlerinizle değiştirerek indirilen şablonu özelleştirin:

* `<subscriptionID>`: Aboneliğiniz
* `<rgName>`: Kaynak grubu, örneğin `myResourceGroup`
* `<vmName>`: Sanal makine adı, örneğin `SimpleWinVM`
* `<dashboardTitle>`: Pano başlığı, örneğin `Simple VM Dashboard`
* `<location>`: Örneğin, Azure bölgeniz `centralus`

Daha fazla bilgi için bkz. [Microsoft Portal panoları şablon başvurusu](/azure/templates/microsoft.portal/dashboards).

## <a name="deploy-the-dashboard-template"></a>Pano şablonunu dağıtma

Şimdi de şablonu Azure CLı içinden dağıtabilirsiniz.

1. Şablonu dağıtmak için [az Portal Dashboard Create](/cli/azure/portal/dashboard#az_portal_dashboard_create) komutunu çalıştırın:

   ```azurecli
   az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
      --input-path portal-dashboard-template-testvm.json --location centralus
   ```

1. Panonun, [az Portal panosu göster](/cli/azure/portal/dashboard#az_portal_dashboard_show) komutunu çalıştırarak başarıyla oluşturulduğunu denetleyin:

   ```azurecli
   az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
   ```

Geçerli abonelik için tüm panoları görmek için [az Portal Dashboard List](/cli/azure/portal/dashboard#az_portal_dashboard_list)kullanın:

```azurecli
az portal dashboard list
```

Ayrıca, bir kaynak grubu için tüm panoları görebilirsiniz:

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

[Az Portal Dashboard Update](/cli/azure/portal/dashboard#az_portal_dashboard_update) komutunu kullanarak bir panoyu güncelleştirebilirsiniz:

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

[!INCLUDE [azure-portal-review-deployed-resources](../../includes/azure-portal-review-deployed-resources.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sanal makineyi ve ilişkili panoyu kaldırmak için, bunları içeren kaynak grubunu silin.

> [!CAUTION]
> Aşağıdaki örnek, belirtilen kaynak grubunu ve içinde yer alan tüm kaynakları siler.
> Bu makalenin kapsamı dışındaki kaynaklar belirtilen kaynak grubunda mevcutsa, bunlar da silinir.

```azurecli
az group delete --name myResourceGroup
```

Yalnızca panoyu kaldırmak için [az Portal Dashboard Delete](/cli/azure/portal/dashboard#az_portal_dashboard_delete) komutunu kullanın:

```azurecli
az portal dashboard delete --resource-group myResourceGroup --name "Simple VM Dashboard"
```

## <a name="next-steps"></a>Sonraki adımlar

Panolar için Azure CLı desteği hakkında daha fazla bilgi için, bkz. [az Portal Dashboard](/cli/azure/portal/dashboard).
