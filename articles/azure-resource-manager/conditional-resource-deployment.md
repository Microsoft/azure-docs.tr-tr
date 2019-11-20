---
title: Şablonlarla koşullu dağıtım
description: Azure Resource Manager şablonunda bir kaynağın koşullu olarak nasıl dağıtılacağını açıklar.
ms.topic: conceptual
ms.date: 09/03/2019
ms.openlocfilehash: e3d78f875e39628194ac30310314e59e2ef19fdb
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147176"
---
# <a name="conditional-deployment-in-resource-manager-templates"></a>Kaynak Yöneticisi şablonlarda koşullu dağıtım

Bazen bir şablonda kaynak dağıtmanız gerekebilir. Kaynağın dağıtılıp dağıtılmayacağını belirtmek için `condition` öğesini kullanın. Bu öğenin değeri true veya false olarak çözümlenmektedir. Değer true olduğunda kaynak oluşturulur. Değer false olduğunda kaynak oluşturulmaz. Değer yalnızca kaynağın tamamına uygulanabilir.

## <a name="new-or-existing-resource"></a>Yeni veya mevcut kaynak

Koşullu dağıtımı, yeni bir kaynak oluşturmak veya var olan bir kaynağı kullanmak için kullanabilirsiniz. Aşağıdaki örnek, yeni bir depolama hesabı dağıtmak veya var olan bir depolama hesabını kullanmak için koşulun nasıl kullanılacağını gösterir.

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

**Neworexıting** parametresi **New**olarak ayarlandığında, koşul true olarak değerlendirilir. Depolama hesabı dağıtılır. Ancak, **Neworexıting** özelliği **var**olarak ayarlandığında, koşul yanlış olarak değerlendirilir ve depolama hesabı dağıtılmaz.

`condition` öğesini kullanan tüm örnek bir şablon için, [Yeni veya var olan sanal ağ, depolama ve genel IP Ile VM 'ye](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions)bakın.

## <a name="allow-condition"></a>Koşula izin ver

Bir koşula izin verilip verilmeyeceğini gösteren bir parametre değeri geçirebilirsiniz. Aşağıdaki örnek bir SQL Server dağıtır ve isteğe bağlı olarak Azure IP 'lerine izin verir.

```json
{
    "type": "Microsoft.Sql/servers",
    "name": "[parameters('serverName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('location')]",
    "properties": {
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
        "version": "12.0"
    },
    "resources": [
        {
            "condition": "[parameters('allowAzureIPs')]",
            "type": "firewallRules",
            "name": "AllowAllWindowsAzureIps",
            "apiVersion": "2015-05-01-preview",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/', parameters('serverName'))]"
            ],
            "properties": {
                "endIpAddress": "0.0.0.0",
                "startIpAddress": "0.0.0.0"
            }
        }
    ]
}
```

Tüm şablon için bkz. [Azure SQL mantıksal sunucusu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server).

## <a name="runtime-functions"></a>Çalışma zamanı işlevleri

Koşullu olarak dağıtılan bir kaynakla [başvuru](resource-group-template-functions-resource.md#reference) veya [liste](resource-group-template-functions-resource.md#list) işlevi kullanıyorsanız, işlev, kaynak dağıtılmasa bile değerlendirilir. İşlev mevcut olmayan bir kaynağa başvuruyorsa bir hata alırsınız.

İşlevin yalnızca kaynak dağıtıldığında koşullara göre değerlendirildiğinden emin olmak için [IF](resource-group-template-functions-logical.md#if) işlevini kullanın. Koşullu olarak dağıtılan bir kaynakla IF ve Reference kullanan bir örnek şablon için [IF işlevine](resource-group-template-functions-logical.md#if) bakın.

## <a name="condition-with-complete-mode"></a>Tamamlanmış mod ile koşul

Bir şablonu, [tamamlanma modu](deployment-modes.md) ile dağıtırsanız ve koşul false olarak değerlendirdiği için bir kaynak dağıtılmamışsa, sonuç, şablonu dağıtmak için kullandığınız REST API sürümüne bağlıdır. 2019-05-10 'den önceki bir sürümü kullanıyorsanız, kaynak **silinmez**. 2019-05-10 veya sonraki bir sürümü kullanarak kaynak **silinir**. Azure PowerShell ve Azure CLı 'nın en son sürümleri, koşul false olduğunda kaynağı siler.

## <a name="next-steps"></a>Sonraki adımlar

* Şablon oluşturma hakkında öneriler için bkz. [Azure Resource Manager şablonu en iyi yöntemleri](template-best-practices.md).
* Bir kaynağın birden çok örneğini oluşturmak için [Azure Resource Manager şablonlarda kaynak, özellik veya değişken yineleme](resource-group-create-multiple.md)bölümüne bakın.