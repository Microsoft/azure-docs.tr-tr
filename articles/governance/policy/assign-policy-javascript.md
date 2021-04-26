---
title: 'Hızlı başlangıç: JavaScript ile yeni ilke ataması'
description: Bu hızlı başlangıçta, uyumlu olmayan kaynakları belirlemek üzere bir Azure Ilkesi ataması oluşturmak için JavaScript kullanırsınız.
ms.date: 03/31/2021
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: d42156e68747b66778ea1cf2001d2b4bfc901bb0
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106090197"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-javascript"></a>Hızlı başlangıç: JavaScript kullanarak uyumlu olmayan kaynakları belirlemek için bir ilke ataması oluşturma

Azure’da uyumluluğu anlamanın ilk adımı, kaynaklarınızın durumunu belirlemektir. Bu hızlı başlangıçta, yönetilen disk kullanmayan sanal makineleri belirlemek için bir ilke ataması oluşturacaksınız. Bu tamamlandığında, _uyumlu olmayan_ sanal makineleri tanımlayacaksınız.

JavaScript kitaplığı, Azure kaynaklarını komut satırından veya betiklerden yönetmek için kullanılır. Bu kılavuzda, bir ilke ataması oluşturmak için JavaScript kitaplığı 'nın nasıl kullanılacağı açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

- **Azure aboneliği**: bir Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

- **Node.js**: [Node.js](https://nodejs.org/) sürüm 12 veya üzeri gereklidir.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-libraries"></a>Ilke kitaplıklarını ekleme

JavaScript 'ı Azure Ilkesiyle çalışacak şekilde etkinleştirmek için, kitaplıkların eklenmesi gerekir. Bu kitaplıklar, [Windows 10 üzerinde Bash](/windows/wsl/install-win10)dahil olmak üzere JavaScript 'in kullanılabileceği her yerde çalışır.

1. Aşağıdaki komutu çalıştırarak yeni bir Node.js projesi ayarlayın.

   ```bash
   npm init -y
   ```

1. Yargs kitaplığına bir başvuru ekleyin.

   ```bash
   npm install yargs
   ```

1. Azure Ilke kitaplıklarına bir başvuru ekleyin.

   ```bash
   # arm-policy is for working with Azure Policy objects such as definitions and assignments
   npm install @azure/arm-policy

   # arm-policyinsights is for working with Azure Policy compliance data such as events and states
   npm install @azure/arm-policyinsights
   ```

1. Azure kimlik doğrulama kitaplığına bir başvuru ekleyin.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > _package.json_ `@azure/arm-policy` 'ın sürüm **3.1.0** veya üzeri olduğundan emin olun, `@azure/arm-policyinsights` Sürüm **3.2.0** veya üzeri, sürüm `@azure/ms-rest-nodeauth` **3.0.5** veya üzeri.

## <a name="create-a-policy-assignment"></a>İlke ataması oluşturma

Bu hızlı başlangıçta, bir ilke ataması oluşturup yönetilen diskler () tanımını **kullanmayan denetim sanal** makinelerini atarsınız `06a78e20-9358-41c9-923c-fb736d382a4d` . Bu ilke tanımı, ilke tanımında ayarlanan koşullarla uyumlu olmayan kaynakları belirler.

1. _policyAssignment.js_ adlı yeni bir dosya oluşturun ve aşağıdaki kodu girin.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyObjects = require("@azure/arm-policy");
   
   if (argv.subID && argv.name && argv.displayName && argv.policyDefID && argv.scope && argv.description) {
   
       const createAssignment = async () => {
           const credentials = await authenticator.interactiveLogin();
           const client = new policyObjects.PolicyClient(credentials, argv.subID);
           const assignments = new policyObjects.PolicyAssignments(client);
   
           const result = await assignments.create(
               argv.scope,
               argv.name,
               {
                   displayName: argv.displayName,
                   policyDefinitionId: argv.policyDefID,
                   description: argv.description
               }
           );
           console.log(result);
       };
   
       createAssignment();
   }
   ```

1. Terminalde aşağıdaki komutu girin:

   ```bash
   node policyAssignment.js `
      --subID "{subscriptionId}" `
      --name "audit-vm-manageddisks" `
      --displayName "Audit VMs without managed disks Assignment" `
      --policyDefID "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      --description "Shows all virtual machines not using managed disks" `
      --scope "{scope}"
   ```

Yukarıdaki komutlarda aşağıdaki bilgiler kullanılmaktadır:

- **Subıd** -kimlik doğrulama bağlamı IÇIN abonelik kimliği. Aboneliğiniz ile değiştirdiğinizden emin olun `{subscriptionId}` .
- **ad** -ilke atama nesnesinin benzersiz adı. Yukarıdaki örnekte _audit-VM-manageddisks_ kullanılır.
- **DisplayName** -ilke ataması için görünen ad. Bu durumda, _yönetilen disk ataması olmadan VM 'Leri denetleme_ kullanıyorsunuz demektir.
- **Policydeıd** : atamayı oluşturmak için kullandığınız ilke tanım yolu. Bu durumda, _yönetilen diskleri kullanmayan ilke tanımı denetim VM_'lerinin kimliğidir.
- **Açıklama** -ilkenin ne yaptığını veya neden bu kapsama atandığını daha derin bir açıklama.
- **kapsam** -bir kapsam, ilke atamasının hangi kaynaklarda veya gruplandırmaya uygulanacağını belirler. Bir yönetim grubundan tek bir kaynağa kadar değişebilir. `{scope}`Aşağıdaki desenlerden biriyle değiştirdiğinizden emin olun:
  - Yönetim grubu: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Abonelik: `/subscriptions/{subscriptionId}`
  - Kaynak grubu: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Kaynak `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

Artık ortamınızın uyumluluk durumunu anlamak için uyumlu olmayan kaynakları belirlemek için hazırsınız demektir.

## <a name="identify-non-compliant-resources"></a>Uyumlu olmayan kaynakları belirleme

İlke atamalarınız oluşturuldığına göre, uyumlu olmayan kaynakları belirleyebilirsiniz.

1. _policyState.js_ adlı yeni bir dosya oluşturun ve aşağıdaki kodu girin.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyInsights = require("@azure/arm-policyinsights");
   
   if (argv.subID && argv.name) {
   
       const getStates = async () => {
   
           const credentials = await authenticator.interactiveLogin();
           const client = new policyInsights.PolicyInsightsClient(credentials);
           const policyStates = new policyInsights.PolicyStates(client);
           const result = await policyStates.listQueryResultsForSubscription(
               "latest",
               argv.subID,
               {
                   queryOptions: {
                       filter: "IsCompliant eq false and PolicyAssignmentId eq '" + argv.name + "'",
                       apply: "groupby((ResourceId))"
                   }
               }
           );
           console.log(result);
       };
   
       getStates();
   }
   ```

1. Terminalde aşağıdaki komutu girin:

   ```bash
   node policyState.js --subID "{subscriptionId}" --name "audit-vm-manageddisks"
   ```

`{subscriptionId}`Önceki adımlarda oluşturduğumuz ' denetim-VM-manageddisks ' adlı ilke atamasının uyumluluk sonuçlarını görmek istediğiniz abonelikle değiştirin. Diğer kapsamların listesi ve verileri özetleme yolları için bkz. [Policystates *](/javascript/api/@azure/arm-policyinsights/) yöntemleri.

Sonuçlarınız aşağıdaki örneğe benzer:

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

Sonuçlar, Azure portal görünümündeki bir ilke atamasının **kaynak uyumluluğu** sekmesinde gördüklerinize göre eşleşir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

- Portal üzerinden _yönetilen disk ataması olmadan VM 'Leri denetleme_ ilke atamasını silin. İlke tanımı yerleşik bir derleme olduğundan, kaldırılacak tanım yok.

- Yüklü kitaplıkları uygulamanızdan kaldırmak istiyorsanız aşağıdaki komutu çalıştırın.

  ```bash
  npm uninstall @azure/arm-policy @azure/arm-policyinsights @azure/ms-rest-nodeauth yargs
  ```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure ortamınızda uyumlu olmayan kaynakları belirlemek üzere bir ilke tanımı atadınız.

Yeni kaynakların uyumlu olduğunu doğrulamak üzere ilke tanımları atama hakkında daha fazla bilgi edinmek için öğreticiye geçin:

> [!div class="nextstepaction"]
> [İlke oluşturma ve yönetme](./tutorials/create-and-manage.md)