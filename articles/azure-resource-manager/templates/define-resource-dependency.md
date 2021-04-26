---
title: Kaynaklar için dağıtım sırasını ayarla
description: Dağıtım sırasında başka bir kaynağa bağlı olarak bir Azure kaynağının nasıl ayarlanacağını açıklar. Bağımlılıklar kaynakların doğru sırada dağıtılmasını güvence altına alınır.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: f6b63b066da06a17c3a2e51ab0f3ab9bf521a144
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934756"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>ARM şablonlarında kaynak dağıtım sırasını belirleme

Kaynakları dağıttığınızda, bazı kaynakların diğer kaynaklardan önce mevcut olduğundan emin olmanız gerekebilir. Örneğin, bir veritabanını dağıtmaya başlamadan önce mantıksal bir SQL Server gerekir. Bu ilişkiyi, bir kaynağı diğer kaynağa bağımlı olarak işaretleyerek kurarsınız. `dependsOn`Açık bir bağımlılık tanımlamak için öğesini kullanın. Örtük bir bağımlılık tanımlamak için **başvuru** veya **liste** işlevlerini kullanın.

Azure Resource Manager, kaynaklar arasındaki bağımlılıkları değerlendirir ve bunları kendi bağımlı sıralarına dağıtır. Resource Manager, birbirine bağımlı olmayan kaynakları paralel olarak dağıtır. Yalnızca aynı şablonda dağıtılan kaynaklar için bağımlılıklar tanımlamanız gerekir.

## <a name="dependson"></a>dependsOn

Azure Resource Manager şablonunuz (ARM şablonu) içinde, öğesi bir `dependsOn` veya daha fazla kaynağa bağımlı olan bir kaynağı tanımlamanızı sağlar. Değeri, her biri bir kaynak adı veya KIMLIĞI olan dizelerin bir JavaScript Nesne Gösterimi (JSON) dizisidir. Dizi, [koşullu olarak dağıtılan](conditional-resource-deployment.md)kaynakları içerebilir. Koşullu bir kaynak dağıtıldığında Azure Resource Manager, gerekli bağımlılıklardan otomatik olarak kaldırır.

Aşağıdaki örnek, bir sanal ağa, ağ güvenlik grubuna ve genel IP adresine bağlı olan bir ağ arabirimini gösterir. Tam şablon için bkz. [bir Linux sanal makinesi için hızlı başlangıç şablonu](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-simple-linux/azuredeploy.json).

```json
{
    "type": "Microsoft.Network/networkInterfaces",
    "apiVersion": "2020-06-01",
    "name": "[variables('networkInterfaceName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
      "[resourceId('Microsoft.Network/networkSecurityGroups/', parameters('networkSecurityGroupName'))]",
      "[resourceId('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
      "[resourceId('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
    ],
    ...
}
```

`dependsOn`Kaynaklarınız arasındaki ilişkileri eşlemek için kullanılması, bu işlemi neden yaptığınızı anlamak önemlidir. Örneğin, kaynakların nasıl birbirine bağlı olduğunu belgelemek için `dependsOn` doğru yaklaşım değildir. Dağıtımdan sonra öğede tanımlanan kaynakları sorgulayamaz `dependsOn` . Gereksiz bağımlılıkların ayarlanması dağıtım süresini yavaşlatır çünkü Kaynak Yöneticisi bu kaynakları paralel olarak dağıtabuyor.

## <a name="child-resources"></a>Alt kaynaklar

Bir [alt kaynak](child-resource-name-type.md) ve üst kaynak arasında örtük bir dağıtım bağımlılığı otomatik olarak oluşturulmaz. Alt kaynağı üst kaynaktan sonra dağıtmanız gerekiyorsa, `dependsOn` özelliği ayarlayın.

Aşağıdaki örnekte, bir mantıksal SQL Server ve veritabanı gösterilmektedir. Veritabanı sunucusunun bir alt öğesi olsa da, veritabanı ve sunucu arasında açık bir bağımlılık tanımlandığına dikkat edin.

```json
"resources": [
  {
    "type": "Microsoft.Sql/servers",
    "apiVersion": "2020-02-02-preview",
    "name": "[parameters('serverName')]",
    "location": "[parameters('location')]",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "type": "databases",
        "apiVersion": "2020-08-01-preview",
        "name": "[parameters('sqlDBName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
          },
        "dependsOn": [
          "[resourceId('Microsoft.Sql/servers', concat(parameters('serverName')))]"
        ]
      }
    ]
  }
]
```

Tam şablon için bkz. [Azure SQL veritabanı için hızlı başlangıç şablonu](https://github.com/Azure/azure-quickstart-templates/blob/master/101-sql-database/azuredeploy.json).

## <a name="reference-and-list-functions"></a>Başvuru ve liste işlevleri

[Başvuru işlevi](template-functions-resource.md#reference) , bir ifadenin DEĞERINI diğer JSON adından ve değer çiftlerinden veya çalışma zamanı kaynaklarından türemesini sağlar. [Liste * işlevleri](template-functions-resource.md#list) bir kaynak için bir liste işleminden değerler döndürür.

Başvuru ve liste ifadeleri, bir kaynağın diğerine bağlı olduğunu dolaylı olarak bildirir. Mümkün olduğunda, gereksiz bir bağımlılık eklemekten kaçınmak için örtük bir başvuru kullanın.

Örtülü bağımlılığı zorlamak için kaynağa kaynak KIMLIĞI değil, ada göre başvurun. Kaynak KIMLIĞINI başvuru veya liste işlevlerine geçirirseniz, örtük bir başvuru oluşturulmaz.

İşlevin genel biçimi `reference` :

```json
reference('resourceName').propertyPath
```

İşlevin genel biçimi `listKeys` :

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

Aşağıdaki örnekte, bir CDN uç noktası açıkça CDN profiline bağımlıdır ve örtülü olarak bir Web uygulamasına bağlıdır.

```json
{
    "name": "[variables('endpointName')]",
    "apiVersion": "2016-04-02",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "dependsOn": [
      "[variables('profileName')]"
    ],
    "properties": {
      "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
      ...
    }
```

Daha fazla bilgi için bkz. [başvuru işlevi](template-functions-resource.md#reference).

## <a name="depend-on-resources-in-a-loop"></a>Bir döngüdeki kaynaklara bağlıdır

Bir [kopyalama döngüsünde](copy-resources.md)kaynaklara bağlı kaynakları dağıtmak için iki seçeneğiniz vardır. Döngüde veya bütün döngüde tek tek kaynaklar için bir bağımlılık ayarlayabilirsiniz.

> [!NOTE]
> Çoğu senaryoda, kopyalama döngüsünde tek tek kaynaklardaki bağımlılığı ayarlamanız gerekir. Bir sonraki kaynağı oluşturmadan önce döngüdeki tüm kaynakların var olması gerektiğinde tüm döngüye bağımlıdır. Tüm döngüde bağımlılığı ayarlandığında, özellikle bu döngüsel kaynaklar diğer kaynaklara bağımlıysa bağımlılıklar grafiğinin önemli ölçüde genişlemesine neden olur. Genişletilmiş bağımlılıklar, dağıtımın etkili bir şekilde tamamlanmasını zorlaştırır.

Aşağıdaki örnek, birden çok sanal makinenin nasıl dağıtılacağını göstermektedir. Şablon aynı sayıda ağ arabirimi oluşturur. Her sanal makine, tüm döngü yerine bir ağ arabirimine bağımlıdır.

```json
{
  "type": "Microsoft.Network/networkInterfaces",
  "apiVersion": "2020-05-01",
  "name": "[concat(variables('nicPrefix'),'-',copyIndex())]",
  "location": "[parameters('location')]",
  "copy": {
    "name": "nicCopy",
    "count": "[parameters('vmCount')]"
  },
  ...
},
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "name": "[concat(variables('vmPrefix'),copyIndex())]",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]"
  ],
  "copy": {
    "name": "vmCopy",
    "count": "[parameters('vmCount')]"
  },
  "properties": {
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]",
          "properties": {
            "primary": "true"
          }
        }
      ]
    },
    ...
  }
}
```

Aşağıdaki örnek, sanal makineyi dağıtmadan önce üç depolama hesabının nasıl dağıtılacağını göstermektedir. `copy`Öğesinin `name` olarak ayarlandığını `storagecopy` ve `dependsOn` sanal makine için olan öğesinin de olarak ayarlandığını unutmayın `storagecopy` .

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

## <a name="circular-dependencies"></a>Döngüsel bağımlılıklar

Kaynak Yöneticisi, şablon doğrulaması sırasında dairesel bağımlılıkları belirler. Döngüsel bağımlılık için bir hata alırsanız, herhangi bir bağımlılıkların kaldırılabileceği olup olmadığını görmek için şablonunuzu değerlendirin. Bağımlılıkları kaldırmak işe yaramazsa, bazı dağıtım işlemlerini alt kaynaklara taşıyarak dairesel bağımlılıklardan kaçınabilirsiniz. Döngüsel bağımlılığı olan kaynaklardan sonra alt kaynakları dağıtın. Örneğin, iki sanal makine dağıttığını, ancak birbirlerine başvuran her bir üzerinde Özellikler ayarlamanız gerektiğini varsayalım. Bunları aşağıdaki sırayla dağıtabilirsiniz:

1. vm1
2. VM2
3. VM1 üzerindeki uzantı VM1 ve VM2 'ye bağımlıdır. Uzantı, VM1 'teki VM2 'dan aldığı değerleri ayarlar.
4. VM2 üzerindeki uzantı VM1 ve VM2 'ye bağımlıdır. Uzantı, VM2 'teki VM1 'dan aldığı değerleri ayarlar.

Dağıtım sırasını değerlendirme ve bağımlılık hatalarını çözme hakkında bilgi için bkz. [Azure Resource Manager ile yaygın Azure dağıtım hatalarıyla Ilgili sorunları giderme](common-deployment-errors.md).

## <a name="next-steps"></a>Sonraki adımlar

* Öğreticiye gitmek için bkz. [öğretici: bağımlı kaynaklarla ARM şablonları oluşturma](template-tutorial-create-templates-with-dependent-resources.md).
* Kaynak bağımlılıklarını içeren Microsoft Learn bir modül için bkz. [GELIŞMIŞ ARM şablon özelliklerini kullanarak karmaşık bulut dağıtımlarını yönetme](/learn/modules/manage-deployments-advanced-arm-template-features/).
* Bağımlılıkları ayarlarken öneriler için bkz. [ARM şablonu en iyi uygulamaları](template-best-practices.md).
* Dağıtım sırasında bağımlılıklar sorunlarını giderme hakkında bilgi edinmek için bkz. [Azure Resource Manager ile yaygın Azure dağıtım hatalarıyla Ilgili sorunları giderme](common-deployment-errors.md).
* Azure Resource Manager şablonları oluşturma hakkında bilgi edinmek için bkz. [ARM şablonlarının yapısını ve sözdizimini anlayın](template-syntax.md).
* Bir şablondaki kullanılabilir işlevlerin listesi için bkz. [ARM şablon işlevleri](template-functions.md).
