---
title: 'Öğretici: uyumluluğu zorlamak için ilke oluşturma'
description: Bu öğreticide, standartları zorlamak, maliyetleri denetlemek, güvenliği korumak ve kurumsal çapta tasarım ilkelerini uygulamak için ilkeleri kullanırsınız.
ms.date: 01/29/2021
ms.topic: tutorial
ms.openlocfilehash: a643e7ccede4966719972694ea29eeb77789595e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99221202"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Öğretici: uyumluluğu zorlamak için ilke oluşturma ve yönetme

Kurumsal standartlarınıza ve hizmet düzeyi sözleşmelerinize uyumluluğu korumak için Azure'da ilkelerin nasıl oluşturulduğunu ve yönetildiğini anlamak önemlidir. Bu öğreticide, Azure İlkesi'ni kullanarak kuruluşunuz genelinde ilkeler oluşturma, atama ve yönetmeyle ilgili en yaygın görevlerden bazılarını yerine getirmeyi öğreneceksiniz; örneğin:

> [!div class="checklist"]
> - Gelecekte oluşturacağınız kaynaklarda bir koşulu zorunlu tutmak için ilke atama
> - Birden çok kaynağın uyumluluğunu izlemek için girişim tanımı oluşturma ve atama
> - Uyumlu olmayan veya reddedilen kaynakları çözümleme
> - Kuruluş genelinde yeni bir ilke uygulama

Mevcut kaynaklarınızın geçerli uyumluluk durumunu tanımlamak için bir ilke atamak isterseniz, hızlı başlangıç makalelerinde bunun nasıl yapıldığı açıklanır.

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="assign-a-policy"></a>İlke atama

Azure İlkesi ile uyumluluğu zorlamanın ilk adımı bir ilke tanımı atamaktır. İlke tanımında, bir ilkenin hangi koşullar altında zorlanacağını ve hangi etkinin uygulanacağı tanımlanır. Bu örnekte, belirtilen etiketi üst kaynak grubundan yeni veya güncelleştirilmiş kaynaklara eklemek için _eksik olursa, kaynak grubundan etiketi devralma_ adlı yerleşik ilke tanımını atayın.

1. İlke atamak için Azure portal gidin. **İlke** arayın ve seçin.

   :::image type="content" source="../media/create-and-manage/search-policy.png" alt-text="Arama çubuğunda Ilke aramanın ekran görüntüsü." border="false":::

1. Azure İlkesi sayfasının sol tarafından **Atamalar**'ı seçin. Atama, belirli bir kapsamda gerçekleşmesi için atanmış olan bir ilkedir.

   :::image type="content" source="../media/create-and-manage/select-assignments.png" alt-text="Ilkeye genel bakış sayfasından atamalar düğümünü seçme ekran görüntüsü." border="false":::

1. **İlke - Atamalar** sayfasının üst kısmından **İlke Ata**'yı seçin.

   :::image type="content" source="../media/create-and-manage/select-assign-policy.png" alt-text="Atamalar sayfasında ' ilke ata ' düğmesini seçme ekran görüntüsü." border="false":::

1. **Ilke ata** sayfası ve **temel bilgiler** sekmesinde, üç noktayı seçip bir yönetim grubu veya abonelik seçerek **kapsamı** seçin. İsterseniz bir kaynak grubu seçin. Kapsam, ilke atamasının hangi kaynaklarda veya kaynak gruplarında uygulanacağını belirler.
   Ardından **kapsam** sayfasının alt kısmında **Seç** ' i seçin.

   Bu örnekte **Contoso** aboneliği kullanılır. Sizin aboneliğiniz farklı olacaktır.

1. Kaynaklar **Kapsam**’a göre dışlanabilir. **Dışlamalar**, **Kapsam**’dan bir düzey aşağıda başlatılır. **Dışlamalar** isteğe bağlıdır, bu yüzden şimdilik boş bırakın.

1. **İlke tanımı** üç nokta öğesini seçerek kullanılabilen tanımların listesini açın. **Tür** değeri _Yerleşik_ olan ilke tanımlarını filtreleyerek bunların tümünü görüntüleyebilir ve açıklamalarını okuyabilirsiniz.

1. **Eksikse kaynak grubundan etiket devralma**' yı seçin. Hemen bulamıyorsanız, arama kutusuna **bir etiket devralma** YAZıN ve ENTER tuşuna basın veya arama kutusunu seçin.
   Varsa ve ilke tanımını seçtikten sonra **kullanılabilir tanımlar** sayfasının alt kısmında **Seç** ' i seçin.

   :::image type="content" source="../media/create-and-manage/select-available-definition.png" alt-text="İlke tanımı seçerken arama filtresinin ekran görüntüsü.":::

1. **Atama adı** otomatik olarak seçtiğiniz ilke adıyla doldurulur, ancak bunu değiştirebilirsiniz. Bu örnek için, _eksikse kaynak grubundan bir etiketi devralmayı_ bırakın. İsteğe bağlı bir **Açıklama** da ekleyebilirsiniz. Açıklama, bu ilke atamasıyla ilgili ayrıntıları sağlar.

1. **İlke zorlamayı** _etkin_ olarak bırakın. _Devre dışı_ bırakıldığında, bu ayar, etkiyi tetiklemeden ilkenin sonucunun test edilmesine olanak tanır. Daha fazla bilgi için bkz. [zorlama modu](../concepts/assignment-structure.md#enforcement-mode).

1. **Tarafından atanan** , oturum açan kim temel alınarak otomatik olarak doldurulur. Bu alan isteğe bağlı olduğu için özel değerler girilebilir.

1. Sihirbazın üst kısmındaki **Parametreler** sekmesini seçin.

1. **Etiket adı** için _ortam_ girin.

1. Sihirbazın üst kısmındaki **Düzeltme** sekmesini seçin.

1. **Düzeltme oluşturma görevi** işaretlenmemiş olarak bırakın. Bu kutu, yeni veya güncelleştirilmiş kaynaklara ek olarak var olan kaynakları değiştirmek için bir görev oluşturmanıza olanak sağlar. Daha fazla bilgi için bkz. [kaynakları](../how-to/remediate-resources.md)düzeltme.

1. Bu ilke tanımı [değiştirme](../concepts/effects.md#modify) efektini kullandığından, **yönetilen kimlik oluşturma** otomatik olarak denetlenir. **İzinler** , ilke tanımına göre otomatik olarak _katkı_ olarak ayarlanır. Daha fazla bilgi için [yönetilen kimlikler](../../../active-directory/managed-identities-azure-resources/overview.md) ve [düzeltme güvenliğinin işleyişi](../how-to/remediate-resources.md#how-remediation-security-works) bölümlerine bakın.

1. Sihirbazın üst kısmındaki **uyumsuz iletiler** sekmesini seçin.

1. **Uyumsuzluk Iletisini** _Bu kaynak için gerekli etikete sahip değil_ olarak ayarlayın. Bu özel ileti, düzenli değerlendirme sırasında bir kaynak reddedildiğinde veya uyumlu olmayan kaynaklar için görüntülenir.

1. Sihirbazın üst kısmındaki **gözden geçir + oluştur** sekmesini seçin.

1. Seçimlerinizi gözden geçirin ve ardından sayfanın alt kısmındaki **Oluştur** ' u seçin.

## <a name="implement-a-new-custom-policy"></a>Yeni bir özel ilke uygulama

Artık bir yerleşik ilke tanımı atadığınıza göre, Azure İlkesi'yle daha fazlasını da yapabilirsiniz. Daha sonra, ortamınızda oluşturulan sanal makinelerin G serisinde yer aldığı doğrulayarak maliyetleri kaydetmek için yeni bir özel ilke oluşturun. Bu şekilde, kuruluşunuzdaki bir Kullanıcı G serisinde bir sanal makine oluşturmak için her seferinde istek reddedilir.

1. Azure Ilkesi sayfasının sol tarafında bulunan **yazma** altındaki **tanımlar** ' ı seçin.

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Yazma grubu altında tanımlar sayfasının ekran görüntüsü." border="false":::

1. Sayfanın üst kısmındaki **+ İlke tanımı** seçeneğini belirleyin. Bu düğme, **ilke tanımı** sayfası için açılır.

1. Aşağıdaki bilgileri girin:

   - İlke tanımının kayıtlı olduğu yönetim grubu veya abonelik. **Tanım konumu**’ndaki üç noktayı kullanarak seçin.

     > [!NOTE]
     > Bu ilke tanımını birden çok aboneliğe uygulamayı planlıyorsanız, konumun ilkeyi atayacağınız abonelikleri içeren yönetim grubu olması gerekir. Bir girişim tanımı için de aynısı geçerlidir.

   - İlke tanımının adı- _VM SKU 'Larını G serisinde değil iste_
   - İlke tanımının yapması amaçlanan Açıklama – _Bu ilke tanımı, bu kapsamda oluşturulan tüm sanal makinelerin maliyeti azaltmak Için G serisi dışındaki SKU 'lara sahip olmasını zorunlu kılar._
   - Mevcut seçenekler arasından seçim yapın (_İşlem_ gibi) veya bu ilke tanımı için yeni bir kategori oluşturun.
   - Aşağıdaki JSON kodunu kopyalayın ve ardından gereksinimlerinize uygun olarak aşağıdaki öğelerle güncelleştirin:
      - İlke parametreleri.
      - İlke kuralları/koşulları; bu örnekte, G serisine eşit VM SKU boyutu
      - İlkenin etkisi, bu örnekte **Reddet**.

   JSON aşağıdakine benzer olmalıdır. Düzeltilmiş kodunuzu Azure Portal'a yapıştırın.

   ```json
   {
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Compute/virtualMachines"
                   },
                   {
                       "field": "Microsoft.Compute/virtualMachines/sku.name",
                       "like": "Standard_G*"
                   }
               ]
           },
           "then": {
               "effect": "deny"
           }
       }
   }
   ```

   İlke kuralındaki _field_ özelliği desteklenen bir değer olmalıdır. [İlke tanımı yapısı alanlarında](../concepts/definition-structure.md#fields), değerlerin tam bir listesi bulunur. Diğer adlara örnek olarak `"Microsoft.Compute/VirtualMachines/Size"` verilebilir.

   Daha fazla Azure Ilke örneği görüntülemek için bkz. [Azure ilke örnekleri](../samples/index.md).

1. **Kaydet**’i seçin.

## <a name="create-a-policy-definition-with-rest-api"></a>REST API ile ilke tanımı oluşturma

Azure Ilke tanımları için REST API bir ilke oluşturabilirsiniz. REST API, ilke tanımlarını oluşturmanıza ve silmenize, ayrıca mevcut tanımlar hakkında bilgi almanıza olanak tanır. İlke tanımı oluşturmak için aşağıdaki örneği kullanın:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Aşağıdaki örnekte gösterilene benzer bir istek gövdesi ekleyin:

```json
{
    "properties": {
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

## <a name="create-a-policy-definition-with-powershell"></a>PowerShell ile ilke tanımı oluşturma

PowerShell örneğine devam etmeden önce, Azure PowerShell az Module 'ün en son sürümünü yüklediğinizden emin olun.

`New-AzPolicyDefinition` cmdlet'ini kullanarak ilke tanımı oluşturabilirsiniz.

Bir dosyadan ilke tanımı oluşturmak için, dosyanın yolunu geçirin. Dış dosya için aşağıdaki örneği kullanın:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -DisplayName 'Deny cool access tiering for storage' `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Yerel dosya kullanımı için aşağıdaki örneği kullanın:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -Description 'Deny cool access tiering for storage' `
    -Policy 'c:\policies\coolAccessTier.json'
```

Satır içi kuralla ilke tanımı oluşturmak için aşağıdaki örneği kullanın:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name 'denyCoolTiering' -Description 'Deny cool access tiering for storage' -Policy '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

Çıkış bir `$definition` nesnesinde depolanır ve bu nesne ilke ataması sırasında kullanılır. Aşağıdaki örnekte, parametreler içeren bir ilke tanımı oluşturulur:

```azurepowershell-interactive
$policy = '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of locations that can be specified when deploying storage accounts.",
            "strongType": "location",
            "displayName": "Allowed locations"
        }
    }
}'

$definition = New-AzPolicyDefinition -Name 'storageLocations' -Description 'Policy to specify locations for storage accounts.' -Policy $policy -Parameter $parameters
```

### <a name="view-policy-definitions-with-powershell"></a>İlke tanımlarını PowerShell ile görüntüleme

Aboneliğinizdeki tüm ilke tanımlarını görmek için aşağıdaki komutu kullanın:

```azurepowershell-interactive
Get-AzPolicyDefinition
```

Yerleşik ilkeler de dahil olmak üzere tüm kullanılabilir ilke tanımlarını döndürür. Her ilke şu biçimde döndürülür:

```output
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

## <a name="create-a-policy-definition-with-azure-cli"></a>Azure CLI ile ilke tanımı oluşturma

Komutuyla Azure CLı kullanarak bir ilke tanımı oluşturabilirsiniz `az policy definition` . Satır içi kuralla ilke tanımı oluşturmak için aşağıdaki örneği kullanın:

```azurecli-interactive
az policy definition create --name 'denyCoolTiering' --description 'Deny cool access tiering for storage' --rules '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

### <a name="view-policy-definitions-with-azure-cli"></a>İlke tanımlarını Azure CLI ile görüntüleme

Aboneliğinizdeki tüm ilke tanımlarını görmek için aşağıdaki komutu kullanın:

```azurecli-interactive
az policy definition list
```

Yerleşik ilkeler de dahil olmak üzere tüm kullanılabilir ilke tanımlarını döndürür. Her ilke şu biçimde döndürülür:

```json
{
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
    "displayName": "Allowed locations",
    "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "policyRule": {
        "if": {
            "not": {
                "field": "location",
                "in": "[parameters('listOfAllowedLocations')]"
            }
        },
        "then": {
            "effect": "Deny"
        }
    },
    "policyType": "BuiltIn"
}
```

## <a name="create-and-assign-an-initiative-definition"></a>Girişim tanımı oluşturma ve atama

Girişim tanımıyla, çeşitli ilke tanımlarını gruplandırıp kapsamlı bir hedefe ulaşabilirsiniz. Bir girişim, dahil edilen ilkelerle uyumluluk için atamanın kapsamındaki kaynakları değerlendirir. Girişim tanımları hakkında daha fazla bilgi için bkz. [Azure İlkesine genel bakış](../overview.md).

### <a name="create-an-initiative-definition"></a>Girişim tanımı oluşturma

1. Azure Ilkesi sayfasının sol tarafında bulunan **yazma** altındaki **tanımlar** ' ı seçin.

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Yazma grubu altındaki tanımlar sayfasının ekran görüntüsü.":::

1. **Girişim tanımı** Sihirbazı 'nı açmak için sayfanın üst kısmındaki **+ girişim tanımını** seçin.

   :::image type="content" source="../media/create-and-manage/initiative-definition.png" alt-text="Girişim tanımı sayfasının ve ayarlanacak özelliklerin ekran görüntüsü.":::

1. Tanımlamayı depolamak için bir yönetim grubu veya abonelik seçmek üzere **girişim konumu** üç nokta simgesini kullanın. Önceki sayfanın kapsamı tek bir yönetim grubu veya aboneliğine yayıldıysa, **girişim konumu** otomatik olarak doldurulur.

1. Girişim için **ad** ve **Açıklama** girin.

   Bu örnek, kaynakların güvenli hale getirme ile ilgili ilke tanımlarıyla uyumlu olduğunu doğrular. Girişimi **Güvenliği Sağlama** olarak adlandırın ve şöyle bir açıklama yazın: **Bu girişim kaynakların güvenliğini sağlamakla ilişkili tüm ilke tanımlarını işlemek için oluşturuldu**.

1. **Kategori** için mevcut seçenekler arasından seçim yapın veya yeni bir kategori oluşturun.

1. Girişim için _1,0_ gibi bir **Sürüm** ayarlayın.

   > [!NOTE]
   > Sürüm değeri kesinlikle meta verilerdir ve Azure Ilke hizmeti tarafından güncelleştirmeler veya herhangi bir işlem için kullanılmaz.

1. Sayfanın en altında bulunan **İleri ' yi** veya sihirbazın en üstündeki **ilkeler** sekmesini seçin.

1. **İlke tanımı Ekle** düğmesini seçin ve listeye göz atabilirsiniz. Bu girişimde eklenmesini istediğiniz ilke tanımlarını seçin. **Güvenli al** girişimi için, ilke tanımının yanındaki onay kutusunu seçerek aşağıdaki yerleşik ilke tanımlarını ekleyin:

   - İzin verilen konumlar
   - Azure Güvenlik Merkezi 'nde eksik Endpoint Protection izleme
   - İnternet 'e yönelik olmayan sanal makineler ağ güvenlik gruplarıyla korunmalıdır
   - Sanal makineler için Azure Backup etkinleştirilmelidir
   - Disk şifrelemesi sanal makinelere uygulanmalıdır
   - Kaynaklardaki bir etiketi ekleyin veya değiştirin (Bu ilke tanımını iki kez ekleyin)

   Listeden her bir ilke tanımını seçtikten sonra listenin en altında **Ekle** ' yi seçin.
   İki kez eklendiğinden, _kaynaklar için bir etiket ekle veya Değiştir_ ilke tanımlarına her biri farklı BIR _başvuru kimliği_ alır.

   :::image type="content" source="../media/create-and-manage/initiative-definition-2.png" alt-text="Girişim tanımı sayfasında başvuru kimliği ve grubu olan seçili ilke tanımlarının ekran görüntüsü.":::

   > [!NOTE]
   > Seçilen ilke tanımları, eklenen bir veya daha fazla tanım seçilerek ve **Seçili ilkeleri bir gruba ekle** seçilerek gruplara eklenebilir. Grup önce mevcut olmalı ve sihirbazın **gruplar** sekmesinde oluşturulabilir.

1. Sayfanın en altında bulunan **İleri ' yi** veya sihirbazın üst kısmındaki **gruplar** sekmesini seçin. Yeni gruplar, bu sekmeden eklenebilir. Bu öğreticide hiçbir grup ekliyoruz.

1. Sayfanın en altında bulunan **İleri ' yi** veya sihirbazın en üstündeki **girişim parametreleri** sekmesini seçin. Bir veya daha fazla dahil edilen ilke tanımına geçiş için girişimde bir parametrenin var olduğunu istiyorsam, parametre burada tanımlanır ve sonra **ilke parametreleri** sekmesinde kullanılır. Bu öğreticide, hiçbir girişim parametresi ekliyoruz.

   > [!NOTE]
   > Girişim tanımına kaydedildikten sonra, girişim parametreleri girişimden silinemez. Bir girişim parametresi artık gerekmiyorsa, herhangi bir ilke tanımı parametresi tarafından kullanımını kaldırın.

1. Sayfanın en altında bulunan **İleri ' yi** veya sihirbazın en üstündeki **ilke parametreleri** sekmesini seçin.

1. Parametrelere parametreler içeren bir kılavuzda görüntülenen ilke tanımı. _Değer türü_ ' varsayılan değer ', ' değer ayarla ' veya ' Use girişim parametresi ' olabilir. ' Değeri ayarla ' seçilirse ilgili değer _değer (ler)_ in altına girilir. İlke tanımındaki parametresinde izin verilen değerler listesi varsa, giriş kutusu bir açılan seçicidir. ' Girişim parametresi kullan ' seçilirse, **girişim parametreleri** sekmesinde oluşturulan girişim parametrelerinin adlarıyla birlikte bir açılan seçim sağlanır.

   :::image type="content" source="../media/create-and-manage/initiative-definition-3.png" alt-text="Girişim tanımı sayfasının ilke parametreleri sekmesinde izin verilen konumlar tanımı parametresi için izin verilen değerler için seçeneklerin ekran görüntüsü.":::

   > [!NOTE]
   > Bazı `strongType` parametrelerinde değer listesi otomatik olarak belirlenebilir. Böyle durumlarda parametre satırının sağ tarafında üç nokta simgesi görünür. Seçilirse, ' parametre kapsamı ( &lt; parametre adı &gt; ) ' sayfası açılır. Bu sayfada, değer seçeneklerini sağlamak için kullanılacak aboneliği seçin. Bu parametre kapsamı yalnızca girişim tanımı oluşturma işlemi sırasında kullanılır ve atandığında, ilke değerlendirmesi veya girişim kapsamı üzerinde herhangi bir etkisi olmaz.

   ' Izin verilen konumlar ' _değer türünü_ ' Set Value ' olarak ayarlayın ve açılan listeden ' Doğu ABD 2 ' seçeneğini belirleyin. _Kaynaklardaki bir etiketi ekle veya Değiştir_ ilke tanımlarında, **etiket adı** parametrelerini ' env ' ve ' Costcenter ' a ve **etiket değeri** parametrelerini aşağıda gösterildiği gibi ' test ' ve ' Lab ' olarak ayarlayın. Diğerlerini ' varsayılan değer ' olarak bırakın. Girişimde aynı tanımı iki kez kullanarak, ancak farklı parametrelerle bu yapılandırma, atamanın kapsamındaki kaynaklarda ' test ' değeri ve ' CostCenter ' etiketiyle ' Lab ' değeri ile bir ' env ' etiketi ekler veya değiştirir.

   :::image type="content" source="../media/create-and-manage/initiative-definition-4.png" alt-text="İzin verilen konumlar tanımı parametresi için izin verilen değerler için girilen seçeneklerin ve girişim tanımı sayfasının ilke parametreleri sekmesindeki etiket parametresi kümelerinin değerlerinin ekran görüntüsü.":::

1. Sayfanın alt kısmında veya sihirbazın en üstünde bulunan **gözden geçir + oluştur** ' u seçin.

1. Ayarları gözden geçirin ve **Oluştur**' u seçin.

#### <a name="create-a-policy-initiative-definition-with-azure-cli"></a>Azure CLı ile ilke girişim tanımı oluşturma

Komutuyla Azure CLı kullanarak bir ilke girişim tanımı oluşturabilirsiniz `az policy set-definition` . Var olan bir ilke tanımıyla bir ilke girişim tanımı oluşturmak için aşağıdaki örneği kullanın:

```azurecli-interactive
az policy set-definition create -n readOnlyStorage --definitions '[
        {
            "policyDefinitionId": "/subscriptions/mySubId/providers/Microsoft.Authorization/policyDefinitions/storagePolicy",
            "parameters": { "storageSku": { "value": "[parameters(\"requiredSku\")]" } }
        }
    ]' \
    --params '{ "requiredSku": { "type": "String" } }'
```

#### <a name="create-a-policy-initiative-definition-with-azure-powershell"></a>Azure PowerShell ile ilke girişim tanımı oluşturma

Cmdlet ile Azure PowerShell kullanarak bir ilke girişim tanımı oluşturabilirsiniz `New-AzPolicySetDefinition` . Var olan bir ilke tanımıyla bir ilke girişim tanımı oluşturmak için aşağıdaki ilke girişim tanım dosyasını şu şekilde kullanın `VMPolicySet.json` :

```json
[
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "parameters": {
            "tagName": {
                "value": "Business Unit"
            },
            "tagValue": {
                "value": "Finance"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/464dbb85-3d5f-4a1d-bb09-95a9b5dd19cf"
    }
]
```

```azurepowershell-interactive
New-AzPolicySetDefinition -Name 'VMPolicySetDefinition' -Metadata '{"category":"Virtual Machine"}' -PolicyDefinition C:\VMPolicySet.json
```

### <a name="assign-an-initiative-definition"></a>Girişim tanımını atama

1. Azure Ilkesi sayfasının sol tarafında bulunan **yazma** altındaki **tanımlar** ' ı seçin.

1. Daha önce oluşturduğunuz **Güvenliği Sağlama** adlı girişim tanımını bulup seçin. Sayfanın en üstünde bulunan **ata** ' yı seçerek **güvenli al: atama girişimi** sayfasına açın.

   :::image type="content" source="../media/create-and-manage/assign-definition.png" alt-text="Girişim tanımı sayfasındaki ' ata ' düğmesinin ekran görüntüsü." border="false":::

   Ayrıca, seçilen satıra sağ tıklayıp bir bağlamsal menü için satırın sonundaki üç nokta simgesini de seçebilirsiniz. Ardından **ata**' yı seçin.

   :::image type="content" source="../media/create-and-manage/select-right-click.png" alt-text="Atama işlevini seçmek için bir girişimin bağlam menüsünün ekran görüntüsü." border="false":::

1. Aşağıdaki örnek bilgileri girerek **Güvenliği Sağlama: Girişimi Ata** sayfasını doldurun. Kendi bilgilerinizi de kullanabilirsiniz.

   - Kapsam: Girişimi kaydettiğiniz yönetim grubu veya abonelik, varsayılan kapsam olur.
     Kapsamı değiştirerek girişimi kayıt konumundaki bir aboneliğe veya kaynak grubuna atayabilirsiniz.
   - Dışlamalar: Kapsam dahilinde yer alan ancak girişim atamasının uygulanmasını önlemek istediğiniz kaynaklar varsa bunları yapılandırın.
   - Girişim tanımı ve Atama adı: Güvenliği Sağlama (atanan girişimin adı olarak önceden oluşturulur).
   - Açıklama: Bu girişim ataması, bu ilke tanımları grubunu zorunlu tutmak için uyarlanmıştır.
   - İlke zorlama: varsayılan _etkin_ olarak bırakın.
   - Atayan: Oturum açmış kişiye göre otomatik olarak doldurulur. Bu alan isteğe bağlı olduğu için özel değerler girilebilir.

1. Sihirbazın üst kısmındaki **Parametreler** sekmesini seçin. Önceki adımlarda bir girişim parametresi yapılandırdıysanız, burada bir değer ayarlayın.

1. Sihirbazın üst kısmındaki **Düzeltme** sekmesini seçin. **Yönetilen Kimlik Oluşturun** seçeneğini işaretsiz bırakın. Atanan ilke veya girişim, [Deployifnotexists](../concepts/effects.md#deployifnotexists) veya [değişiklik](../concepts/effects.md#modify) efektlerini içeren bir ilke içerdiğinde bu kutu _denetlenmelidir_ . Bu öğretici için kullanılan ilke olmadığından boş bırakın. Daha fazla bilgi için [yönetilen kimlikler](../../../active-directory/managed-identities-azure-resources/overview.md) ve [düzeltme güvenliğinin işleyişi](../how-to/remediate-resources.md#how-remediation-security-works) bölümlerine bakın.

1. Sihirbazın üst kısmındaki **gözden geçir + oluştur** sekmesini seçin.

1. Seçimlerinizi gözden geçirin ve ardından sayfanın alt kısmındaki **Oluştur** ' u seçin.

## <a name="check-initial-compliance"></a>İlk uyumluluğu denetleme

1. Azure İlkesi sayfasının sol tarafından **Uyumluluklar**'ı seçin.

1. **Güvenli al** girişimi ' ni bulun. Hala **başlatılmamış** olan _uyumluluk durumunda_ olabilir.
   Atamanın tüm ayrıntılarını almak için girişimi seçin.

   :::image type="content" source="../media/create-and-manage/compliance-status-not-started.png" alt-text="Başlama durumunda atama değerlendirmelerinin gösterildiği girişim uyumluluk sayfasının ekran görüntüsü." border="false":::

1. Girişim ataması tamamlandıktan sonra, uyumluluk sayfası güncelleştirilerek _Uyumluluk durumu_ değeri **Uyumlu** olur.

   :::image type="content" source="../media/create-and-manage/compliance-status-compliant.png" alt-text="Atama değerlendirmelerinin tamamlandığını ve uyumlu durumda olduğunu gösteren girişim uyumluluk sayfasının ekran görüntüsü." border="false":::

1. Girişim uyumluluğu sayfasında herhangi bir ilkeyi seçmek, bu ilkenin uyumluluk ayrıntıları sayfasını açar. Bu sayfada uyumluluk için kaynak düzeyinde ayrıntılar sağlanır.

## <a name="remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion"></a>Uyumlu olmayan veya reddedilen bir kaynağı kapsamdan dışlama ile kaldırma

Belirli bir konum gerektirecek bir ilke girişimi atadıktan sonra, farklı bir konumda oluşturulan tüm kaynaklar reddedilir. Bu bölümde, tek bir kaynak grubunda dışlama oluşturarak, bir kaynak oluşturmak için reddedilen isteği çözmeye adım adım ilerleyerek. Dışlama, bu kaynak grubundaki ilkenin (veya girişim) zorlanmasını önler. Aşağıdaki örnekte, hariç tutulan kaynak grubunda herhangi bir konuma izin verilir. Dışlama bir aboneliğe, bir kaynak grubuna veya tek tek kaynaklara uygulanabilir.

> [!NOTE]
> Bir [ilke muafiyeti](../concepts/exemption-structure.md) , bir kaynağın değerlendirmesini atla da kullanılabilir. Daha fazla bilgi için bkz. [Azure Ilkesinde kapsam](../concepts/scope.md).

Atanan bir ilke veya girişim tarafından engellenen dağıtımlar, dağıtım tarafından hedeflenen kaynak grubunda görüntülenebilir: sayfanın sol tarafındaki **dağıtımlar** ' ı seçin, sonra başarısız dağıtımın **dağıtım adını** seçin. Reddedilen kaynak, _Yasaklandı_ durum bilgisiyle listelenir. Kaynağı reddeden ilkeyi veya girişimi ve atamayı belirlemek için başarısız ' ı seçin **. Dağıtım Genel Bakış sayfasında Ayrıntılar->için buraya tıklayın** . Sayfanın sağ tarafında hata bilgilerini içeren bir pencere açılır. **Hata ayrıntıları** ' nın altında, ilgili Ilke nesnelerinin GUID 'leri vardır.

:::image type="content" source="../media/create-and-manage/rg-deployment-denied.png" alt-text="Bir ilke ataması tarafından reddedilen başarısız dağıtımın ekran görüntüsü." border="false":::

Azure Ilkesi sayfasında: sayfanın sol tarafındaki **Uyumluluk** ' i seçin ve **güvenli ilke Al** girişim ' yı seçin. Bu sayfada, engellenen kaynaklar için **reddetme** sayısında bir artış vardır. **Olaylar** sekmesinin altında, ilke tanımı tarafından reddedilen kaynağı kimin oluşturmaya veya dağıtmaya çalıştığımız hakkında ayrıntılardır.

:::image type="content" source="../media/create-and-manage/compliance-overview.png" alt-text="Girişim uyumluluğu sayfasında olaylar sekmesinin ekran görüntüsü ve ilke olay ayrıntıları." border="false":::

Bu örnekte, contoso SR. Virtualization uzmanlarının biri olan Trent Baker, gerekli işleri yapıyor. Özel durum için bir alan vermemiz gerekiyor. Yeni bir kaynak grubu oluşturuldu, **Locationsexcluded** ve Next Bu ilke ataması için bir özel durum verin.

### <a name="update-assignment-with-exclusion"></a>Atamayı özel durumla güncelleştirme

1. Azure Ilkesi sayfasının sol tarafında bulunan **yazma** altındaki **atamalar** ' ı seçin.

1. Tüm ilke atamalarına göz atarak _güvenli Ilke al_ atamasını açın.

1. Üç noktayı seçerek ve dışlanacak kaynak grubunu seçerek **dışlamayı** ayarlayın, bu örnekte _Locationsexcluded_ . **Seçili kapsama Ekle** ' yi seçin ve ardından **Kaydet**' i seçin.

   :::image type="content" source="../media/create-and-manage/request-exclusion.png" alt-text="İlke atamasına dışlanan bir kaynak grubu eklemek için girişim atama sayfasındaki dışlamaları seçeneğinin ekran görüntüsü." border="false":::

   > [!NOTE]
   > İlke tanımına ve etkine bağlı olarak, dışlama, atama kapsamındaki bir kaynak grubu içindeki belirli kaynaklara da verilebilir. Bu öğreticide bir **reddetme** etkisi kullanıldığından, zaten mevcut olan belirli bir kaynakta dışlamanın ayarlanması mantıklı değildir.

1. **Gözden geçir + kaydet** ' i ve ardından **Kaydet**' i seçin.

Bu bölümde, tek bir kaynak grubunda dışlama oluşturarak reddedilen isteği çözümlettiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticideki kaynaklarla çalışmayı bitirdiğinizde, yukarıda oluşturulan ilke atamalarından veya tanımlardan birini silmek için aşağıdaki adımları kullanın:

1. Azure Ilkesi sayfasının sol tarafında **yazma** ' nın altında **tanımlar** (veya atamayı silmeye çalışıyorsanız **atamalar** ) ' ı seçin.

1. Kaldırmak istediğiniz yeni girişim veya tanımını (ya da atamayı) arayın.

1. Satıra sağ tıklayın ya da tanımın (veya atamanın) sonundaki üç noktayı seçip **Tanımı sil** (veya **Atamayı sil**) öğesini seçin.

## <a name="review"></a>Gözden geçirme

Bu öğreticide, aşağıdaki görevleri başarıyla gerçekleştirdiniz:

> [!div class="checklist"]
> - Gelecekte oluşturacağınız kaynaklarda bir koşulu zorunlu tutmak için ilke atandı
> - Birden çok kaynağın uyumluluğunu izlemek için girişim tanımı oluşturuldu ve atandı
> - Uyumlu olmayan veya reddedilen kaynak sorunu çözüldü
> - Kuruluş genelinde yeni bir ilke uygulandı

## <a name="next-steps"></a>Sonraki adımlar

İlke tanımlarının yapıları hakkında daha fazla bilgi edinmek için şu makaleyi gözden geçirin:

> [!div class="nextstepaction"]
> [Azure İlkesi tanım yapısı](../concepts/definition-structure.md)