---
title: Şablonlar ile uygulama dağıtma kılavuzu-Azure App Service | Microsoft Docs
description: Web uygulamalarını dağıtmak için Azure Resource Manager şablonları oluşturmaya yönelik öneriler.
services: app-service
documentationcenter: app-service
author: tfitzmac
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 2af3ec61537dc28ab652b669ff46500db19ab307
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74130597"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Azure Resource Manager şablonları kullanarak Web uygulamalarını dağıtma Kılavuzu

Bu makalede Azure App Service çözümlerini dağıtmak için Azure Resource Manager şablonları oluşturmaya yönelik öneriler sağlanır. Bu öneriler, yaygın sorunları önlemenize yardımcı olabilir.

## <a name="define-dependencies"></a>Bağımlılıkları tanımlama

Web Apps bağımlılıklarını tanımlama, bir Web uygulamasındaki kaynakların nasıl etkileşim kuracağını anlamak için gereklidir. Bağımlılıkları yanlış sırada belirtirseniz, dağıtım hatalarına neden olabilir veya dağıtımı yükleyen bir yarış durumu oluşturabilirsiniz.

> [!WARNING]
> Şablonunuza bir MSDeploy site uzantısı eklerseniz, tüm yapılandırma kaynaklarını MSDeploy kaynağına bağımlı olarak ayarlamanız gerekir. Yapılandırma değişiklikleri, sitenin zaman uyumsuz olarak yeniden başlatılmasına neden olur. Yapılandırma kaynaklarını MSDeploy 'e bağımlı hale getirerek, sitenin yeniden başlatılmasından önce MSDeploy 'in tamamlandığından emin olursunuz. Bu bağımlılıklar olmadan site, MSDeploy dağıtım işlemi sırasında yeniden başlatılabilir. Örnek bir şablon için, [Web dağıtımı bağımlılığı olan WordPress şablonu](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json)' na bakın.

Aşağıdaki görüntüde çeşitli App Service kaynakları için bağımlılık sırası gösterilmektedir:

![Web uygulaması bağımlılıkları](media/web-sites-rm-template-guidance/web-dependencies.png)

Kaynakları aşağıdaki sırayla dağıtabilirsiniz:

**Katman 1**
* Planı App Service.
* Veritabanları veya depolama hesapları gibi diğer ilgili kaynaklar.

**Katman 2**
* Web uygulaması--App Service planına bağlıdır.
* Sunucu grubunu hedefleyen Azure Application Insights örneği--App Service planına bağlıdır.

**Katman 3**
* Kaynak denetimi--Web uygulamasına göre değişir.
* MSDeploy site uzantısı--Web uygulamasına göre değişir.
* Web uygulamasını hedefleyen Azure Application Insights örneği--Web uygulamasına göre değişir.

**Katman 4**
* App Service sertifikası--varsa, kaynak denetimine veya MSDeploy 'a bağımlıdır. Aksi halde, Web uygulamasına bağlıdır.
* Yapılandırma ayarları (bağlantı dizeleri, Web. config değerleri, uygulama ayarları)--varsa, kaynak denetimine veya MSDeploy 'a bağımlıdır. Aksi halde, Web uygulamasına bağlıdır.

**Katman 5**
* Ana bilgisayar adı bağlamaları--varsa sertifikaya bağlıdır. Aksi halde, daha üst düzey bir kaynağa bağlıdır.
* Site uzantıları--varsa, yapılandırma ayarlarına bağımlıdır. Aksi halde, daha üst düzey bir kaynağa bağlıdır.

Genellikle çözümünüz bu kaynak ve katmanlardan yalnızca bazılarını içerir. Eksik katmanlar için, daha düşük kaynakları daha yüksek bir katmana eşleyin.

Aşağıdaki örnek bir şablonun parçasını gösterir. Bağlantı dizesi yapılandırmasının değeri MSDeploy uzantısına bağlıdır. MSDeploy uzantısı, Web uygulamasına ve veritabanına bağlıdır. 

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

Yukarıdaki kodu kullanan, çalıştırmaya hazırlanma bir örnek için bkz. [Template: Simple DICO Web uygulaması oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple).

## <a name="find-information-about-msdeploy-errors"></a>MSDeploy hataları hakkında bilgi bulma

Kaynak Yöneticisi şablonunuz MSDeploy kullanıyorsa, dağıtım hata iletilerinin anlaşılması zor olabilir. Başarısız bir dağıtımdan sonra daha fazla bilgi edinmek için aşağıdaki adımları deneyin:

1. Sitenin [kudu konsoluna](https://github.com/projectkudu/kudu/wiki/Kudu-console)gidin.
2. Browse to the folder at D:\home\LogFiles\SiteExtensions\MSDeploy.
3. AppManagerStatus. xml ve appManagerLog. xml dosyalarını arayın. İlk dosya durumu günlüğe kaydeder. İkinci dosya hatayla ilgili bilgileri günlüğe kaydeder. Hata sizin için açık değilse, forumda yardım isteyip istemediğiniz zaman ekleyebilirsiniz.

## <a name="choose-a-unique-web-app-name"></a>Benzersiz bir Web uygulaması adı seçin

Web uygulamanızın adı genel olarak benzersiz olmalıdır. Benzersiz olabilecek bir adlandırma kuralı kullanabilirsiniz veya benzersiz bir ad oluşturmaya yardımcı olması için [Uniquestring işlevini](../azure-resource-manager/resource-group-template-functions-string.md#uniquestring) kullanabilirsiniz.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Key Vault Web uygulaması sertifikası dağıtma

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Şablonunuz SSL bağlaması için bir [Microsoft. Web/sertifika](/azure/templates/microsoft.web/certificates) kaynağı içeriyorsa ve sertifika bir Key Vault depolanıyorsa, App Service kimliğin sertifikaya erişebildiğinizden emin olmanız gerekir.

Küresel Azure 'da App Service hizmet sorumlusu **ABFA0A7C-A6B6-4736-8310-5855508787CD**kimliğine sahiptir. App Service hizmet sorumlusu için Key Vault erişim vermek için şunu kullanın:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

Azure Kamu 'da App Service hizmet sorumlusu, **6a02c803-dadfd-4136-b4c3-5a6f318b4714**kimliğine sahiptir. Yukarıdaki örnekte bu KIMLIĞI kullanın.

Key Vault **Sertifikalar** ' ı seçin ve sertifikayı karşıya yüklemek için **Oluştur/içeri aktar** ' a tıklayın.

![Sertifikayı içeri aktar](media/web-sites-rm-template-guidance/import-certificate.png)

Şablonunuzda `keyVaultSecretName`sertifikanın adını sağlayın.

Örnek bir şablon için bkz. [Key Vault gizli alanından Web uygulaması sertifikası dağıtma ve SSL bağlaması oluşturmak için bunu kullanma](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault).

## <a name="next-steps"></a>Sonraki adımlar

* Bir şablonla Web uygulamaları dağıtmaya yönelik bir öğretici için bkz. [Azure 'da mikro hizmetler sağlama ve dağıtma](deploy-complex-application-predictably.md).
* Şablonlarda kaynak türlerinin JSON sözdizimi ve özellikleri hakkında bilgi edinmek için, bkz. [Azure Resource Manager şablonu başvurusu](/azure/templates/).
