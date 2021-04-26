---
Başlık: Media Services Account ARM şablonu: Azure Media Services Description: Bu makalede, bir medya Hizmetleri hesabı oluşturmak için ARM şablonunun nasıl kullanılacağı gösterilmektedir.
Hizmetler: Media-Services belgetationcenter: ' ' Yazar: ınridatmicrosoft Manager: femila Düzenleyicisi: ' '

MS. Service: Media-Services MS. Workload: MS. Topic: hızlı başlangıç MS. Date: 03/23/2021 MS. Author: inhenkel MS. Custom: Subject-armqs

---

# <a name="quickstart-media-services-account-arm-template"></a>Hızlı başlangıç: Media Services hesabı ARM şablonu

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bu makalede, bir Media Services hesabı oluşturmak için bir Azure Resource Manager şablonunun (ARM şablonu) nasıl kullanılacağı gösterilmektedir.

## <a name="introduction"></a>Giriş

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

ARM şablonlarıyla karşılaşan okuyucular [dağıtım bölümüne](#deploy-the-template)devam edebilir.

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-media-services-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

Daha önce bir ARM şablonu dağıtmadıysanız, [Azure ARM şablonları](../../azure-resource-manager/templates/index.yml) hakkında bilgi edinmek ve [öğreticiden](../../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-powershell)gezinmek yararlı olur.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-media-services-create/) alınmıştır.

JSON kod dilimi için sözdizimi şöyledir:

:::code language="json" source="~/quickstart-templates/101-media-services-create/azuredeploy.json":::

Şablonda üç Azure Kaynak türü tanımlanmıştır:

- [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): depolama hesabı oluşturma
- [Microsoft. Media/mediaservices](/azure/templates/microsoft.media/mediaservices): Media Services hesap oluşturma

## <a name="set-the-account"></a>Hesabı ayarla

```azurecli-interactive

az account set --subscription {your subscription name or id}

```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

```azurecli-interactive

az group create --name {the name you want to give your resource group} --location "{pick a location}"

```

## <a name="assign-a-variable-to-your-deployment-file"></a>Dağıtım dosyanıza bir değişken atama

Kolaylık sağlaması için, şablon dosyasının yolunu depolayan bir değişken oluşturun. Her dağıtım yaptığınızda yolu yeniden yazmanız gerekmediğinden bu değişken, dağıtım komutlarını çalıştırmanızı kolaylaştırır.

```azurecli-interactive

templateFile="{provide the path to the template file}"

```

## <a name="deploy-the-template"></a>Şablonu dağıtma

Media Services hesap adını girmeniz istenir.

```azurecli-interactive

az deployment group create --name {the name you want to give to your deployment} --resource-group {the name of resource group you created} --template-file $templateFile

```

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

Aşağıdakine benzer bir JSON yanıtı görmeniz gerekir:

```json
{
  "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Resources/deployments/amsarmquickstartdeploy",
  "location": null,
  "name": "amsarmquickstartdeploy",
  "properties": {
    "correlationId": "{correlationid}",
    "debugSetting": null,
    "dependencies": [
      {
        "dependsOn": [
          {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
            "resourceGroup": "amsarmquickstartrg",
            "resourceName": "storagey44cfdmliwatk",
            "resourceType": "Microsoft.Storage/storageAccounts"
          }
        ],
        "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg",
        "resourceName": "{accountname}",
        "resourceType": "Microsoft.Media/mediaServices"
      }
    ],
    "duration": "PT1M10.8615001S",
    "error": null,
    "mode": "Incremental",
    "onErrorDeployment": null,
    "outputResources": [
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg"
      },
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
        "resourceGroup": "amsarmquickstartrg"
      }
    ],
    "outputs": null,
    "parameters": {
      "mediaServiceName": {
        "type": "String",
        "value": "{accountname}"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.Media",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "mediaServices"
          }
        ]
      },
      {
        "id": null,
        "namespace": "Microsoft.Storage",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "storageAccounts"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "templateHash": "{templatehash}",
    "templateLink": null,
    "timestamp": "2020-11-24T23:25:52.598184+00:00",
    "validatedResources": null
  },
  "resourceGroup": "amsarmquickstartrg",
  "tags": null,
  "type": "Microsoft.Resources/deployments"
}

```

Azure portal kaynaklarınızın oluşturulduğunu doğrulayın.

![oluşturulan hızlı başlangıç kaynakları](./media/media-services-arm-template-quickstart/quickstart-arm-template-resources.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Az önce oluşturduğunuz kaynakları kullanmayı planlamadıysanız, kaynak grubunu silebilirsiniz.

```azurecli-interactive

az group delete --name {name of the resource group}

```

## <a name="next-steps"></a>Sonraki adımlar

Parametreleri, değişkenleri ve daha fazlasını içeren bir şablon oluşturma işlemini izleyerek ARM şablonunu kullanma hakkında daha fazla bilgi için, deneyin

> [!div class="nextstepaction"]
> [Öğretici: ilk ARM şablonunuzu oluşturma ve dağıtma](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)