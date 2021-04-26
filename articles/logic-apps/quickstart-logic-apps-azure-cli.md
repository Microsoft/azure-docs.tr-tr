---
title: Azure CLı ile mantıksal uygulamalar oluşturma ve yönetme
description: Bir mantıksal uygulama oluşturmak için Azure CLı 'yı kullanın, ardından liste, göster (Al), Güncelleştir ve Sil gibi işlemleri kullanarak mantıksal uygulamanızı yönetin.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli, contperf-fy21q2
ms.date: 11/23/2020
ms.openlocfilehash: afc39673a30f5c99455696c7a075cb1a6a33ecd1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875512"
---
# <a name="quickstart-create-and-manage-logic-apps-using-the-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak Logic Apps oluşturma ve yönetme

Bu hızlı başlangıçta, [Azure clı Logic Apps uzantısı](/cli/azure/logic) () kullanılarak mantıksal uygulamalar oluşturma ve yönetme işlemlerinin nasıl yapılacağı gösterilir `az logic` . Komut satırından, bir mantıksal uygulama iş akışı tanımı için JSON dosyasını kullanarak bir mantıksal uygulama oluşturabilirsiniz. Daha sonra `list` , `show` (), ve gibi işlemleri `get` `update` `delete` komut satırından çalıştırarak mantıksal uygulamanızı yönetebilirsiniz.

> [!WARNING]
> Azure CLı Logic Apps uzantısı Şu anda *deneysel* ve *müşteri desteği kapsamında değil*. Özellikle uzantıyı üretim ortamlarında kullanmayı seçerseniz, bu CLı uzantısını dikkatle kullanın.

Logic Apps yeni başladıysanız, [Visual Studio 'da](quickstart-create-logic-apps-with-visual-studio.md)ve [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)' de ilk Logic [Azure Portal](quickstart-create-first-logic-app-workflow.md)Apps oluşturmayı da öğrenebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. Azure aboneliğiniz yoksa [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Yerel bilgisayarınızda yüklü olan [Azure CLI](/cli/azure/install-azure-cli) .
* Bilgisayarınızda yüklü [Logic Apps Azure CLI uzantısı](/cli/azure/azure-cli-extensions-list) . Bu uzantıyı yüklemek için şu komutu kullanın: `az extension add --name logic`
* Mantıksal uygulamanızın oluşturulacağı bir [Azure Kaynak grubu](#example---create-resource-group) .

### <a name="prerequisite-check"></a>Önkoşul denetimi

Başlamadan önce ortamınızı doğrulayın:

* Azure portal oturum açın ve ' i çalıştırarak aboneliğinizin etkin olduğunu denetleyin `az login` .
* Çalıştıran bir Terminal veya komut penceresinde Azure CLı sürümünüzü denetleyin `az --version` . En son sürüm için bkz. [en son sürüm notları](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * En son sürüme sahip değilseniz, [işletim sisteminiz veya platformunuzun yükleme kılavuzunu](/cli/azure/install-azure-cli)izleyerek yüklemenizi güncelleştirin.

### <a name="example---create-resource-group"></a>Örnek-kaynak grubu oluştur

Mantıksal uygulamanız için henüz bir kaynak grubunuz yoksa, komutuyla grubu oluşturun `az group create` . Örneğin, aşağıdaki komut, konumunda adlı bir kaynak grubu oluşturur `testResourceGroup` `westus` .

```azurecli-interactive

az group create --name testResourceGroup --location westus

```

Çıkış, `provisioningState` `Succeeded` kaynak grubunuzun başarıyla oluşturulduğu gibi gösterir:

```output

<...>
  "name": "testResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
<...>

```

## <a name="workflow-definition"></a>İş akışı tanımı

[Yeni bir mantıksal uygulama](#create-logic-apps-from-cli) oluşturmadan veya Azure CLI kullanarak [mevcut bir mantıksal uygulamayı güncelleştirmeden](#update-logic-apps-from-cli) önce mantıksal uygulamanız için bir iş akışı tanımına ihtiyacınız vardır. Azure portal, **Tasarımcı** görünümünden **kod görünümüne** geçerek mantıksal uygulamanızın temel ALıNAN iş akışı tanımını JSON biçiminde görüntüleyebilirsiniz.

Mantıksal uygulamanızı oluşturmak veya güncelleştirmek için komutları çalıştırdığınızda, iş akışı tanımınız gerekli bir parametre () olarak karşıya yüklenir `--definition` . İş akışı tanımınızı, [Iş akışı Tanım Dili şemasını](./logic-apps-workflow-definition-language.md)IZLEYEN bir JSON dosyası olarak oluşturmanız gerekir.

## <a name="create-logic-apps-from-cli"></a>CLı 'dan Logic Apps oluşturma

[`az logic workflow create`](/cli/azure/logic/workflow#az_logic_workflow_create)Tanımı için BIR JSON dosyası ile komutunu kullanarak Azure CLI 'dan bir mantıksal uygulama iş akışı oluşturabilirsiniz.

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

Komutunuz aşağıdaki [gerekli parametreleri](/cli/azure/logic/workflow#az_logic_workflow_create-required-parameters)içermelidir:

| Parametre | Değer | Açıklama |
| --------- | ----- | ----------- |
| İş akışı tanımı | `--definition` | Mantıksal uygulamanızın [iş akışı tanımına](#workflow-definition)sahıp bir JSON dosyası. |
| Konum | `--location -l` | Mantıksal uygulamanızın bulunduğu Azure bölgesi. |
| Name | `--name -n` | Mantıksal uygulamanızın adı. Ad yalnızca harf, sayı, kısa çizgi ( `-` ), alt çizgi () `_` , parantez ( `()` ) ve nokta () içerebilir `.` . Ad aynı zamanda bölgeler genelinde benzersiz olmalıdır. |
| Kaynak grubu adı | `--resource-group -g` | Mantıksal uygulamanızı oluşturmak istediğiniz [Azure Kaynak grubu](../azure-resource-manager/management/overview.md) . Mantıksal uygulamanız için henüz bir tane yoksa, başlamadan önce [bir kaynak grubu oluşturun](#example---create-resource-group) . |

Mantıksal uygulamanızın erişim denetimleri, uç noktaları, tümleştirme hesabı, tümleştirme hizmeti ortamı, durum ve kaynak etiketlerinizi yapılandırmak için ek [isteğe bağlı parametreleri](/cli/azure/logic/workflow#az_logic_workflow_create-optional-parameters) de ekleyebilirsiniz.

### <a name="example---create-logic-app"></a>Örnek-mantıksal uygulama oluşturma

Bu örnekte, konumundaki kaynak grubunda adlı bir iş akışı `testLogicApp` oluşturulur `testResourceGroup` `westus` . JSON dosyası `testDefinition.json` , iş akışı tanımını içerir.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

İş akışınız başarıyla oluşturulduğunda, CLı yeni iş akışı tanımınızın JSON kodunu gösterir. İş akışı oluşturma işlemi başarısız olursa [olası hatalar listesine](#errors)bakın.

## <a name="update-logic-apps-from-cli"></a>CLı 'dan mantıksal uygulamaları güncelleştirme

Ayrıca, komutunu kullanarak bir mantıksal uygulamanın iş akışını Azure CLı 'dan da güncelleştirebilirsiniz [`az logic workflow create`](/cli/azure/logic/workflow#az_logic_workflow_create) .

Komutunuz, [mantıksal uygulama oluştururken](#create-logic-apps-from-cli) [gereken parametreleri](/cli/azure/logic/workflow#az_logic_workflow_create-required-parameters) de içermelidir. Ayrıca, mantıksal uygulama oluştururken aynı [isteğe bağlı parametreleri](/cli/azure/logic/workflow#az_logic_workflow_create-optional-parameters) de ekleyebilirsiniz.

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

### <a name="example---update-logic-app"></a>Örnek-mantıksal uygulamayı Güncelleştir

Bu örnekte, [önceki bölümde oluşturulan örnek iş akışı](#example---create-logic-app) farklı bir JSON tanım dosyası kullanacak şekilde güncelleştirilir `newTestDefinition.json` ve iki kaynak etiketi `testTag1` ve `testTag2` Açıklama değerleriyle birlikte eklenir.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "newTestDefinition.json" --tags "testTag1=testTagValue1" "testTag2=testTagValue"

```

İş akışınız başarıyla güncelleştirildiği zaman, CLı mantıksal uygulamanızın güncelleştirilmiş iş akışı tanımını gösterir. Güncelleştirmeniz başarısız olursa [olası hatalar listesine](#errors)bakın.

## <a name="delete-logic-apps-from-cli"></a>CLı 'dan mantıksal uygulamaları silme

Komutunu kullanarak bir mantıksal uygulamanın iş akışını Azure CLı 'dan silebilirsiniz [`az logic workflow delete`](/cli/azure/logic/workflow#az_logic_workflow_delete) .

Komutunuz aşağıdaki [gerekli parametreleri](/cli/azure/logic/workflow#az_logic_workflow_delete-required-parameters)içermelidir:

| Parametre | Değer | Açıklama |
| --------- | ----- | ----------- |
| Ad | `--name -n` | Mantıksal uygulamanızın adı. |
| Kaynak grubu adı | `-resource-group -g` | Mantıksal uygulamanızın bulunduğu kaynak grubu. |

Ayrıca onay istemlerini atlamak için [isteğe bağlı bir parametre](/cli/azure/logic/workflow#az_logic_workflow_delete-optional-parameters) ekleyebilirsiniz `--yes -y` .

```azurecli

az logic workflow delete --name
                         --resource-group
                         [--yes]

```

CLı daha sonra mantıksal uygulamanızı silme işlemini onaylamanızı ister. Komutunuz ile isteğe bağlı parametreyi kullanarak onay istemi atlayabilirsiniz `--yes -y` .

```azurecli

Are you sure you want to perform this operation? (y/n):

```

Logic [Apps 'ı CLI 'da listeleyerek](#list-logic-apps-in-cli)veya Azure Portal mantıksal uygulamalarınızı görüntüleyerek bir mantıksal uygulamanın silinmesini doğrulayabilirsiniz.

### <a name="example---delete-logic-app"></a>Örnek-mantıksal uygulamayı silme

Bu örnekte, [önceki bölümde oluşturulan örnek iş akışı](#example---create-logic-app) silinir.

```azurecli-interactive

az logic workflow delete --resource-group "testResourceGroup" --name "testLogicApp"

```

Onaylama istemine ile yanıt verdikten sonra `y` mantıksal uygulama silinir.

## <a name="show-logic-apps-in-cli"></a>CLı 'de Logic Apps 'i göster

Komutunu kullanarak belirli bir mantıksal uygulama iş akışı alabilirsiniz [`az logic workflow show`](/cli/azure/logic/workflow#az_logic_workflow_show) .

```azurecli

az logic workflow show --name
                       --resource-group

```

Komutunuz aşağıdaki [gerekli parametreleri](/cli/azure/logic/workflow#az_logic_workflow_show-required-parameters) içermelidir

| Parametre | Değer | Açıklama |
| --------- | ----- | ----------- |
| Ad | `--name -n` | Mantıksal uygulamanızın adı. |
| Kaynak grubu adı | `--resource-group -g` | Mantıksal uygulamanızın bulunduğu kaynak grubunun adı. |

### <a name="example---get-logic-app"></a>Örnek-mantıksal uygulama al

Bu örnekte, kaynak grubundaki mantıksal uygulama `testLogicApp` `testResourceGroup` hata ayıklama için tam günlüklerle döndürülür.

```azurecli-interactive

az logic workflow show --resource-group "testResourceGroup" --name "testLogicApp" --debug

```

## <a name="list-logic-apps-in-cli"></a>CLı 'da mantıksal uygulamaları listeleyin

Komutunu kullanarak mantıksal uygulamalarınızı aboneliğe göre listeleyebilirsiniz [`az logic workflow list`](/cli/azure/logic/workflow#az_logic_workflow_list) . Bu komut, mantıksal uygulamalarınızın İş akışlarınızın JSON kodunu döndürür.

Sonuçlarınızı aşağıdaki [isteğe bağlı parametrelere](/cli/azure/logic/workflow#az_logic_workflow_list-optional-parameters)göre filtreleyebilirsiniz:

| Parametre | Değer | Açıklama |
| --------- | ----- | ----------- |
| Kaynak grubu adı | `--resource-group -g` | Sonuçlarınızı filtrelemek istediğiniz kaynak grubunun adı. |
| Öğe sayısı | `--top` | Sonuçlarınıza dahil edilen öğe sayısı. |
| Filtre | `--filter` | Listenizde kullandığınız filtrenin türü. Durum ( `State` ), tetikleyici ( `Trigger` ) ve başvurulan kaynak () tanımlayıcısına göre filtreleyebilirsiniz `ReferencedResourceId` . |

```azurecli

az logic workflow list [--filter]
                       [--resource-group]
                       [--top]

```

### <a name="example---list-logic-apps"></a>Örnek-mantıksal uygulamaları Listele

Bu örnekte, kaynak grubundaki tüm etkin iş akışları `testResourceGroup` BIR ASCII Tablo biçiminde döndürülür.

```azurecli-interactive

az logic workflow list --resource-group "testResourceGroup" --filter "(State eq 'Enabled')" --output "table"

```

## <a name="errors"></a>Hatalar

Aşağıdaki hata Azure Logic Apps CLı uzantısının yüklenmediğini gösterir. [Logic Apps uzantısını bilgisayarınıza yüklemek](#prerequisites) için önkoşulların içindeki adımları izleyin.

```output

az: 'logic' is not in the 'az' command group. See 'az --help'. If the command is from an extension, please make sure the corresponding extension is installed. To learn more about extensions, please visit https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview

```

Aşağıdaki hata, iş akışı tanımınızı karşıya yüklemek için dosya yolunun yanlış olduğunu gösteriyor.

```output

Expecting value: line 1 column 1 (char 0)

```

## <a name="global-parameters"></a>Genel parametreler

Komutlarınız ile aşağıdaki isteğe bağlı Global Azure CLı parametrelerini kullanabilirsiniz `az logic` :

| Parametre | Değer | Açıklama |
| --------- | ----- | ----------- |
| Çıkış biçimi | `--output -o` | [Çıkış biçimini](/cli/azure/format-output-azure-cli) varsayılan JSON ' dan değiştirin. |
| Yalnızca hataları göster | `--only-show-errors` | Uyarıları gösterme ve yalnızca hataları göster. |
| Ayrıntılı | `--verbose` | Ayrıntılı günlükleri göster. |
| Hata Ayıklama | `--debug` | Tüm hata ayıklama günlüklerini gösterir. |
| Yardım iletisi | `--help -h` | Yardım iletişimini göster. |
| Sorgu | `--query` | JSON çıktısı için bir JMESPath sorgu dizesi ayarlayın. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLı hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure/).

[Microsoft 'un kod örnekleri tarayıcısında](/samples/browse/?products=azure-logic-apps)daha fazla Logic Apps CLI betik örneği bulabilirsiniz.

Daha sonra örnek bir betik ve iş akışı tanımı kullanarak Azure CLı aracılığıyla örnek bir uygulama mantığı oluşturabilirsiniz.

> [!div class="nextstepaction"]
> [Örnek betiği kullanarak mantıksal uygulama oluşturun](sample-logic-apps-cli-script.md).
