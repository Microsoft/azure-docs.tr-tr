---
title: Kod En Iyi uygulamalar olarak Azure Service Fabric altyapısı
description: Azure Service Fabric 'yi kod olarak altyapı olarak yönetmeye yönelik en iyi yöntemler ve tasarım konuları.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: b765d92778df40caec0864dc6f547324216fdb07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102611989"
---
# <a name="infrastructure-as-code"></a>Kod olarak altyapı

Bir üretim senaryosunda, Kaynak Yöneticisi şablonları kullanarak Azure Service Fabric kümeleri oluşturun. Kaynak Yöneticisi şablonlar, kaynak özellikleri üzerinde daha fazla denetim sağlar ve tutarlı bir kaynak modeline sahip olduğunuzdan emin olun.

Örnek Kaynak Yöneticisi şablonlar, [GitHub 'Daki Azure örneklerinde](https://github.com/Azure-Samples/service-fabric-cluster-templates)Windows ve Linux 'ta kullanılabilir. Bu şablonlar, küme şablonunuz için bir başlangıç noktası olarak kullanılabilir. `azuredeploy.json` `azuredeploy.parameters.json` Özel gereksinimlerinizi karşılayacak şekilde indirin ve düzenleyin.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

`azuredeploy.json` `azuredeploy.parameters.json` Yukarıda indirdiğiniz ve şablonlarını dağıtmak Için AŞAĞıDAKI Azure CLI komutlarını kullanın:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az deployment group create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

PowerShell kullanarak kaynak oluşturma

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Azure Service Fabric kaynakları

Uygulamaları ve Hizmetleri, Service Fabric kümenize Azure Resource Manager aracılığıyla dağıtabilirsiniz. Ayrıntılar için bkz. [Uygulamaları ve hizmetleri Azure Resource Manager kaynak olarak yönetme](./service-fabric-application-arm-resource.md) . Aşağıda, Kaynak Yöneticisi Şablon kaynaklarınıza dahil etmek üzere uygulamaya özgü kaynaklar Service Fabric en iyi uygulamadır.

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

Azure Resource Manager kullanarak uygulamanızı dağıtmak için önce [bir sfpkg](./service-fabric-package-apps.md#create-an-sfpkg) Service Fabric uygulama paketi oluşturmanız gerekir. Aşağıdaki Python betiği bir sfpkg oluşturma örneğidir:

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(
    self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(
            root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Azure sanal makine Işletim sistemi otomatik yükseltme yapılandırması 
Sanal makinelerinizin yükseltilmesi Kullanıcı tarafından başlatılan bir işlemdir ve Azure Service Fabric kümeleri ana bilgisayar düzeltme eki yönetimi için [sanal makine ölçek kümesi otomatik Işletim sistemi yükseltmesini](service-fabric-patch-orchestration-application.md) kullanmanız önerilir; Düzeltme Eki düzenleme uygulaması, POA 'nın Azure 'da barındırılmasına yönelik olarak Azure 'da barındırılması için tasarlanan alternatif bir çözümdür ve Azure 'da POA 'nın sanal makine Işletim sistemi otomatik yükseltmesini tercih etmek için sık karşılaşılan bir neden olabilir. Otomatik işletim sistemi yükseltmesini etkinleştirmek için, Işlem Sanal Makine Ölçek Kümesi Kaynak Yöneticisi şablonu özellikleri aşağıda verilmiştir:

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
Service Fabric ile otomatik IŞLETIM sistemi yükseltmeleri kullanırken, yeni işletim sistemi görüntüsü aynı anda bir güncelleştirme etki alanını kullanıma alarak Service Fabric çalıştıran hizmetlerin yüksek kullanılabilirliğe sahip olur. Service Fabric otomatik işletim sistemi yükseltmelerini kullanmak için, kümenizin gümüş dayanıklılık katmanını veya üstünü kullanacak şekilde yapılandırılması gerekir.

Windows konak makinelerinizin Eşgüdümlü olmayan güncelleştirmeleri başlatmasını engellemek için aşağıdaki kayıt defteri anahtarının false olarak ayarlandığından emin olun: HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

Aşağıda, WindowsUpdate kayıt defteri anahtarını false olarak ayarlamak için, Işlem Sanal Makine Ölçek Kümesi Kaynak Yöneticisi Şablon özellikleri verilmiştir:
```json
"osProfile": {
        "computerNamePrefix": "{vmss-name}",
        "adminUsername": "{your-username}",
        "secrets": [],
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "enableAutomaticUpdates": false
        }
      },
```

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Azure Service Fabric kümesi yükseltme yapılandırması
Otomatik yükseltmeyi etkinleştirmek için Service Fabric kümesi Kaynak Yöneticisi şablonu özelliği aşağıda verilmiştir:
```json
"upgradeMode": "Automatic",
```
Kümenizi el ile yükseltmek için, bir küme sanal makinesine cab/Deb dağıtımını indirin ve ardından aşağıdaki PowerShell 'i çağırın:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>Sonraki adımlar

* VM 'lerde veya Windows Server çalıştıran bilgisayarlarda küme oluşturma: [Windows Server için Service Fabric kümesi oluşturma](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* VM 'lerde veya Linux çalıştıran bilgisayarlarda küme oluşturma: [Linux kümesi oluşturma](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* [Service Fabric destek seçenekleri](service-fabric-support.md) hakkında bilgi edinin
