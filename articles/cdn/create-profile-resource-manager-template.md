---
title: Hızlı başlangıç-Kaynak Yöneticisi şablonları kullanarak bir profil ve uç nokta oluşturma
titleSuffix: Azure Content Delivery Network
description: Kaynak Yöneticisi şablonları kullanarak ağ profili ve uç nokta sunma Azure Içeriği oluşturma hakkında bilgi edinin
services: cdn
documentationcenter: ''
author: senthuransivananthan
manager: danielgi
editor: ''
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: azure-cdn
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/05/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: b711a12161bc134bdcbb8c1f3e74f2e5ae06e701
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083133"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint-using-resource-manager-template"></a>Hızlı başlangıç: Kaynak Yöneticisi şablonu kullanarak Azure CDN profili ve uç noktası oluşturma

Bu hızlı başlangıçta, CLı kullanarak bir Azure Resource Manager şablonu dağıtırsınız. Oluşturduğunuz şablon, Web uygulamanıza ön bir CDN profili ve CDN uç noktası dağıtır.
Bu adımların tamamlanması yaklaşık on dakika sürer.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prequisites"></a>Belgelerinden

Bu hızlı başlangıç amacıyla, kaynağınız olarak kullanılacak bir Web uygulamasına sahip olmanız gerekir. Bu hızlı başlangıçta kullanılan örnek Web uygulaması https://cdndemo.azurewebsites.net için dağıtıldı

Daha fazla bilgi için bkz. [Azure 'da STATIK HTML Web uygulaması oluşturma](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html).

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Tüm kaynakların aynı kaynak grubunda dağıtılması gerekir.

Seçtiğiniz konumda kaynak grubunu oluşturun. Bu örnek, Doğu ABD konumunda CDN adlı bir kaynak grubu oluşturmayı gösterir.

```bash
az group create --name cdn --location eastus
```

![Yeni kaynak grubu](./media/create-profile-resource-manager-template/cdn-create-resource-group.png)

## <a name="create-the-resource-manager-template"></a>Resource Manager şablonu oluşturma

Bu adımda, kaynakları dağıtan bir şablon dosyası oluşturacaksınız.

Bu örnek, genel bir Web sitesi hızlandırma senaryosunda, yapılandırılabilecek birçok başka ayar de vardır. Bu ayarlar Azure Resource Manager şablonu başvurusunda bulunur. Lütfen [CDN profili](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles) ve [CDN profil uç noktası](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints)başvurularını inceleyin.

Microsoft CDN 'nin içerik türü listesini değiştirmeyi desteklemediğini unutmayın.

Şablonu **Resource-Manager-CDN. JSON**olarak kaydedin.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "cdnProfileSku": {
            "type": "string",
            "allowedValues": [
                "Standard_Microsoft",
                "Standard_Akamai",
                "Standard_Verizon",
                "Premium_Verizon"
            ]
        },
        "endpointOriginHostName": {
            "type": "string"
        }
    },
    "variables": {
        "profile": {
            "name": "[replace(toLower(parameters('cdnProfileSku')), '_', '-')]"
        },
        "endpoint": {
            "name": "[replace(toLower(parameters('endpointOriginHostName')), '.', '-')]",
            "originHostName": "[parameters('endpointOriginHostName')]"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Cdn/profiles",
            "apiVersion": "2017-10-12",
            "location": "[resourceGroup().location]",
            "name": "[variables('profile').name]",
            "sku": {
                "name": "[parameters('cdnProfileSku')]"
            }
        },
        {
            "dependsOn": [
                "[resourceId('Microsoft.Cdn/profiles', variables('profile').name)]"
            ],
            "type": "Microsoft.Cdn/profiles/endpoints",
            "apiVersion": "2017-10-12",
            "location": "[resourceGroup().location]",
            "name": "[concat(variables('profile').name, '/', variables('endpoint').name)]",
            "properties": {
                "hostName": "[concat(variables('endpoint').name, '.azureedge.net')]",
                "originHostHeader": "[variables('endpoint').originHostName]",
                "isHttpAllowed": true,
                "isHttpsAllowed": true,
                "queryStringCachingBehavior": "IgnoreQueryString",
                "origins": [
                    {
                        "name": "[replace(variables('endpoint').originHostName, '.', '-')]",
                        "properties": {
                            "hostName": "[variables('endpoint').originHostName]",
                            "httpPort": 80,
                            "httpsPort": 443
                        }
                    }
                ],
                "contentTypesToCompress": [
                    "application/eot",
                    "application/font",
                    "application/font-sfnt",
                    "application/javascript",
                    "application/json",
                    "application/opentype",
                    "application/otf",
                    "application/pkcs7-mime",
                    "application/truetype",
                    "application/ttf",
                    "application/vnd.ms-fontobject",
                    "application/xhtml+xml",
                    "application/xml",
                    "application/xml+rss",
                    "application/x-font-opentype",
                    "application/x-font-truetype",
                    "application/x-font-ttf",
                    "application/x-httpd-cgi",
                    "application/x-javascript",
                    "application/x-mpegurl",
                    "application/x-opentype",
                    "application/x-otf",
                    "application/x-perl",
                    "application/x-ttf",
                    "font/eot",
                    "font/ttf",
                    "font/otf",
                    "font/opentype",
                    "image/svg+xml",
                    "text/css",
                    "text/csv",
                    "text/html",
                    "text/javascript",
                    "text/js",
                    "text/plain",
                    "text/richtext",
                    "text/tab-separated-values",
                    "text/xml",
                    "text/x-script",
                    "text/x-component",
                    "text/x-java-source"
                ],
                "isCompressionEnabled": true,
                "optimizationType": "GeneralWebDelivery"
            }
        }
    ],
    "outputs": {
        "cdnUrl": {
            "type": "string",
            "value": "[concat('https://', variables('endpoint').name, '.azureedge.net')]"
        }
    }
}
```

## <a name="create-the-resources"></a>Kaynakları oluşturma

Azure CLı kullanarak şablonu dağıtın. Sizden 2 giriş istenir:

**Cdnprofilesku** -kullanmak istediğiniz CDN sağlayıcısı. Seçenekler şunlardır:

* Standard_Microsoft
* Standard_Akamai
* Standard_Verizon
* Premium_Verizon.

**endpointOriginHostName** -CDN aracılığıyla sunulacak uç nokta, örneğin, cdndemo.azurewebsites.net.

```bash
az group deployment create --resource-group cdn --template-file arm-cdn.json
```

![Kaynak Yöneticisi şablonu dağıtma](./media/create-profile-resource-manager-template/cdn-deploy-resource-manager.png)

## <a name="view-the-cdn-profile"></a>CDN profilini görüntüleme

```bash
az cdn profile list --resource-group cdn -o table
```

![CDN profilini görüntüle](./media/create-profile-resource-manager-template/cdn-view-profile.png)

## <a name="view-the-cdn-endpoint-for-the-profile-standard-microsoft"></a>Profil standardı için CDN uç noktasını görüntüleme-Microsoft

```bash
az cdn endpoint list --profile-name standard-microsoft --resource-group cdn -o table
```

![CDN uç noktasını görüntüle](./media/create-profile-resource-manager-template/cdn-view-endpoint.png)

İçeriği görüntülemek için ana bilgisayar adını kullanın. Örneğin, tarayıcınızı kullanarak https://cdndemo-azurewebsites-net.azureedge.net erişin.

## <a name="clean-up"></a>Temizleme

Kaynak grubunun silinmesi, içinde dağıtılan tüm kaynakları otomatik olarak kaldırır.

```bash
az group delete --name cdn
```

![Kaynak grubunu sil](./media/create-profile-resource-manager-template/cdn-delete-resource-group.png)

## <a name="references"></a>Başvurular

* CDN profili- [Azure Resource Manager şablonu başvurusu](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles)
* CDN uç noktası- [Azure Resource Manager şablonu başvuru belgeleri](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints)

## <a name="next-steps"></a>Sonraki adımlar

CDN uç noktanıza özel etki alanı ekleme hakkında bilgi edinmek için aşağıdaki öğreticiye bakın:

> [!div class="nextstepaction"]
> [Öğretici: Azure CDN uç noktanıza özel etki alanı ekleme](cdn-map-content-to-custom-domain.md)
