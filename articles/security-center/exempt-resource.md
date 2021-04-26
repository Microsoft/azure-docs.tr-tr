---
title: Bir kaynak, abonelik, yönetim grubu ve güvenli puanın Azure Güvenlik Merkezi önerisini muaf tutma
description: Aboneliklerden veya yönetim gruplarından güvenlik önerilerini muaf tutmak ve bunların güvenli puanınızı etkilemelerini engellemek için kurallar oluşturmayı öğrenin
author: memildin
ms.author: memildin
ms.date: 03/11/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 13abb35d0fa9ad3ee949b6edf5205de601a02956
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718566"
---
# <a name="exempting-resources-and-recommendations-from-your-secure-score"></a>Güvenli puanınızdan kaynakları ve önerileri muaf tutma 

Her güvenlik ekibinin temel önceliği, analistlerin kuruluşa bağlı görevlere ve olaylara odaklanabildiğinden emin olunması sağlamaktır. Güvenlik Merkezi, deneyimi özelleştirmek ve güvenli puanınızın kuruluşunuzun güvenlik önceliklerini yansıttığından emin olmak için birçok özelliğe sahiptir. **Muaf** seçeneği bu tür bir özelliktir.

Azure Güvenlik Merkezi 'nde güvenlik önerilerinizi araştırdığınızda, gözden geçirenlerin ilk bilgi parçalarından biri etkilenen kaynakların listesidir.

Bazen, dahil edilmemeniz gerektiğini belirten bir kaynak listelenecektir. Ya da bir öneri, ait olmadığı yerde bir kapsamda gösterilecektir. Kaynak, Güvenlik Merkezi tarafından izlenmeyen bir işlem tarafından düzeltildi olabilir. Öneri, belirli bir abonelik için uygun olmayabilir. Ya da kuruluşunuz yalnızca belirli kaynakla veya öneriyle ilgili riskleri kabul etmeye karar verdi.

Bu gibi durumlarda öneri için bir istisna oluşturabilirsiniz:

- İleride sağlıksız kaynaklarla listelenmediğinden emin olmak için **bir kaynağı muaf tut** ve güvenli puanınızı etkilemez. Kaynak geçerli değil olarak listelenecektir ve bu nedenle seçtiğiniz belirli bir gerekçe ile "muaf tutulan" olarak gösterilir.

- Önerinin güvenli puanınızı etkilememesini sağlamak için **bir aboneliği veya yönetim grubunu muaf tut** ve gelecekte abonelik veya yönetim grubu için gösterilmeyecektir. Bu, mevcut kaynaklarla ve gelecekte oluşturduğunuz herhangi bir kaynak ile ilgilidir. Öneri, seçtiğiniz kapsam için seçtiğiniz belirli bir gerekçe ile işaretlenir.

## <a name="availability"></a>Kullanılabilirlik

| Görünüş                          | Ayrıntılar                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Yayın durumu:                  | Önizleme<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                                                                                                                                                                                                             |
| Fiyat                        | Bu, Azure Defender müşterileri için ek ücret ödemeden sunulan bir Premium Azure Ilke özelliğidir. Diğer kullanıcılar için ücretler gelecekte uygulanabilir.                                                                                                                                                                 |
| Gerekli roller ve izinler: | Muafiyet oluşturmak için **sahip** veya **kaynak ilkesi katılımcısı**<br>Bir kural oluşturmak için Azure Ilkesinde ilkeleri düzenleme izinlerinizin olması gerekir.<br>Azure [Ilkesinde Azure RBAC izinleri](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy)hakkında daha fazla bilgi edinin.                                            |
| Sınırlamalar:                    | Muafiyetler yalnızca güvenlik merkezi 'nin varsayılan girişim, [Azure Güvenlik kıyaslaması](https://docs.microsoft.com/security/benchmark/azure/introduction)veya sağlanan mevzuata standart girişimlerinin herhangi biri için eklenen öneriler için oluşturulabilir. Özel girişimlerden oluşturulan öneriler muaf tutulamaz. [İlkeler, girişimler ve öneriler](security-policy-concept.md)arasındaki ilişkiler hakkında daha fazla bilgi edinin. |
| Larının                         | ![Yes](./media/icons/yes-icon.png) Ticari bulutlar<br>![No](./media/icons/no-icon.png) Ulusal/Sogeign (US Gov, Çin gov, diğer gov)                                                                                                                                                                                         |
|                                 |                                                                                                                                                                                                                                                                                                                                    |

## <a name="define-an-exemption"></a>İstisna tanımlama

Güvenlik Merkezi 'nin abonelikleriniz, yönetim grubunuz veya kaynaklarınız için yaptığı güvenlik önerilerini hassas bir şekilde ayarlamak için, aşağıdakileri yapmak için bir muafiyet kuralı oluşturabilirsiniz:

- Belirli bir **öneriyi** veya "azaltıldığında" veya "risk kabul edildi" olarak işaretleyin. Bir abonelik, birden çok abonelik veya bir yönetim grubu için öneri muafiyetleri oluşturabilirsiniz.
- Belirli bir öneri için **bir veya daha fazla kaynağı** "azaltıldığında" veya "risk kabul edildi" olarak işaretleyin.

> [!NOTE]
> Muafiyetler yalnızca güvenlik merkezi 'nin varsayılan girişim, Azure Güvenlik kıyaslaması veya sağlanan mevzuata standart girişimlerinin herhangi biri için eklenen öneriler için oluşturulabilir. Aboneliklerinize atanan özel girişimlerden oluşturulan öneriler muaf tutulamaz. [İlkeler, girişimler ve öneriler](security-policy-concept.md)arasındaki ilişkiler hakkında daha fazla bilgi edinin.

> [!TIP]
> Ayrıca API kullanarak dışarıda bırakma oluşturabilirsiniz. Bir JSON örneği ve ilgili yapıların açıklaması için bkz. [Azure ilke muafiyet yapısı](../governance/policy/concepts/exemption-structure.md).

Muafiyet kuralı oluşturmak için:

1. Belirli bir öneri için öneriler ayrıntıları sayfasını açın.

1. Sayfanın üst kısmındaki araç çubuğundan **muaf tut**' u seçin.

    :::image type="content" source="media/exempt-resource/exempting-recommendation.png" alt-text="Bir aboneliğin veya yönetim grubunun dışında tutulan bir öneri için muafiyet kuralı oluşturun.":::

1. **Dışlama** bölmesinde:
    1. Bu muafiyet kuralının kapsamını seçin:
        - Bir yönetim grubu seçerseniz, öneri bu grup içindeki tüm aboneliklerden muaf tutulur
        - Bu kuralı bir veya daha fazla kaynağın öneriden muaf tutulması için oluşturuyorsanız, "Seçili kaynaklar" ı seçin ve listeden ilgili olanları seçin

    1. Bu muafiyet kuralı için bir ad girin.
    1. İsteğe bağlı olarak, bir sona erme tarihi ayarlayın.
    1. Muafiyet kategorisini seçin:
        - **3. taraf üzerinden çözüldü (** azaltılan): Güvenlik Merkezi 'nin tanımlanmayan üçüncü taraf bir hizmet kullanıyorsanız. 

            > [!NOTE]
            > Öneriyi azaltılan olarak muaf bulundurarak, güvenli puanınızın altına işaret vermemesini sağlayabilirsiniz. Ancak, uygun olmayan kaynaklar için noktaların *kaldırılmadığı* için sonuç puanınızın artması olur.

        - **Risk kabul edildi (feragat aldığınız)** – bu öneriyi azaltıcı riski kabul etmeye karar verdiyseniz
    1. İsteğe bağlı olarak bir açıklama girin.
    1. **Oluştur**’u seçin.

    :::image type="content" source="media/exempt-resource/defining-recommendation-exemption.png" alt-text="Aboneliğinizi veya yönetim grubunuzu bir öneriyi muaf tutmak için muafiyet kuralı oluşturma adımları":::

    Muafiyet yürürlüğe girer (30 dakikaya kadar sürebilir):
    - Öneri veya kaynaklar, güvenli puanınızı etkilemez.
    - Belirli kaynakları muaf Tutuldıysanız, bunlar öneri Ayrıntıları sayfasının **geçerli değil** sekmesinde listelenecektir.
    - Bir öneriyi muaf tutarak, güvenlik merkezi 'nin öneriler sayfasında varsayılan olarak gizlenir. Bunun nedeni, söz konusu sayfadaki **öneri durumu** filtresinin varsayılan seçeneklerinin, **uygulanamaz** önerilerin dışlanmasını sağlar. Bir güvenlik denetimindeki tüm önerileri muaf tutmak için de aynı değer geçerlidir.

        :::image type="content" source="media/exempt-resource/recommendations-filters-hiding-not-applicable.png" alt-text="Azure Güvenlik Merkezi 'nin öneriler sayfasında varsayılan filtreler uygulanabilir değil önerilerini ve güvenlik denetimlerini gizleyin":::

    - Öneri Ayrıntıları sayfasının en üstündeki bilgi şeridi, muaf tutulan kaynakların sayısını güncelleştirir:
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="Muaf tutulan kaynak sayısı":::

1. Muaf tutulan kaynaklarınızı gözden geçirmek için, **geçerli değil** sekmesini açın:

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="Muafiyeti değiştirme":::

    Her bir istisna için neden tabloya (1) dahildir.

    Bir istisnayı değiştirmek veya silmek için (2) gösterildiği gibi üç nokta menüsünü ("...") seçin.

1. Aboneliğinizdeki tüm muafiyet kurallarını gözden geçirmek için, bilgi şeridinde **muafiyetleri görüntüle** ' yi seçin:

    > [!IMPORTANT]
    > Bir öneriyle ilgili belirli muafiyetleri görmek için listeyi ilgili kapsama ve öneri adına göre filtreleyin.

    :::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Azure Ilkesinin muafiyet sayfası":::

    > [!TIP]
    > Alternatif olarak, [Muafiyetli öneriler bulmak Için Azure Kaynak Grafiği](#find-recommendations-with-exemptions-using-azure-resource-graph)' ni kullanın.

## <a name="monitor-exemptions-created-in-your-subscriptions"></a>Aboneliklerinizde oluşturulan, izleme muafiyetleri

Bu sayfada daha önce anlatıldığı gibi istisna kuralları, aboneliklerinizde ve yönetim gruplarındaki kaynakları etkileyen öneriler üzerinde ayrıntılı denetim sağlayan güçlü bir araçtır. 

Kullanıcılarınızın bu yeteneği nasıl kullandığını izlemek için, bir istisna oluşturulduğunda size bildirimde bulunan bir Logic App PlayBook 'u ve gerekli tüm API bağlantılarını dağıtan bir Azure Resource Manager (ARM) şablonu oluşturduk.

- PlayBook hakkında daha fazla bilgi edinmek için bkz. Tech Community blog gönderisi [Azure Güvenlik Merkezi 'Nde kaynak muafiyetlerini izleme](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-keep-track-of-resource-exemptions-in-azure-security/ba-p/1770580)
- ARM şablonunu [Azure Güvenlik Merkezi GitHub deposunda](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation/Notify-ResourceExemption) bulacaksınız
- Tüm gerekli bileşenleri dağıtmak için [Bu otomatik işlemi kullanın](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Security-Center%2Fmaster%2FWorkflow%2520automation%2FNotify-ResourceExemption%2Fazuredeploy.json)


## <a name="find-recommendations-with-exemptions-using-azure-resource-graph"></a>Azure Kaynak Grafını kullanarak muafiyetle öneriler bulma

Azure Kaynak Grafiği (ARG), güçlü filtreleme, gruplama ve sıralama özelliklerine sahip bulut ortamlarınızdaki kaynak bilgilerine anında erişim sağlar. Azure aboneliklerindeki bilgileri programlı olarak veya Azure portal içinden sorgulamak için hızlı ve verimli bir yoldur.

Muafiyet kuralları olan tüm önerileri görüntülemek için:

1. **Azure Kaynak Grafiği Gezginini** açın.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Azure Kaynak Grafiği Gezgini * * öneri sayfası başlatılıyor" :::

1. Aşağıdaki sorguyu girin ve **Sorguyu Çalıştır**' ı seçin.

    ```kusto
    securityresources
    | where type == "microsoft.security/assessments"
    // Get recommendations in useful format
    | project
    ['TenantID'] = tenantId,
    ['SubscriptionID'] = subscriptionId,
    ['AssessmentID'] = name,
    ['DisplayName'] = properties.displayName,
    ['ResourceType'] = tolower(split(properties.resourceDetails.Id,"/").[7]),
    ['ResourceName'] = tolower(split(properties.resourceDetails.Id,"/").[8]),
    ['ResourceGroup'] = resourceGroup,
    ['ContainsNestedRecom'] = tostring(properties.additionalData.subAssessmentsLink),
    ['StatusCode'] = properties.status.code,
    ['StatusDescription'] = properties.status.description,
    ['PolicyDefID'] = properties.metadata.policyDefinitionId,
    ['Description'] = properties.metadata.description,
    ['RecomType'] = properties.metadata.assessmentType,
    ['Remediation'] = properties.metadata.remediationDescription,
    ['Severity'] = properties.metadata.severity,
    ['Link'] = properties.links.azurePortal
    | where StatusDescription contains "Exempt"    
    ```


Aşağıdaki sayfalarda daha fazla bilgi edinin:
- [Azure Kaynak Grafiği hakkında daha fazla bilgi edinin](../governance/resource-graph/index.yml).
- [Azure Kaynak Grafı Gezgini ile sorgu oluşturma](../governance/resource-graph/first-query-portal.md)
- [Kusto Sorgu Dili (KQL)](/azure/data-explorer/kusto/query/)





## <a name="faq---exemption-rules"></a>SSS-muafiyet kuralları

### <a name="what-happens-when-one-recommendation-is-in-multiple-policy-initiatives"></a>Bir öneri birden çok ilke girişimleriyle olduğunda ne olur?

Bazen, bir güvenlik önerisi birden fazla ilke girişimi içinde görünür. Aynı öneriyi aynı aboneliğe atanmış birden fazla örneğe sahipseniz ve öneri için bir istisna oluşturursanız, düzenleme izniniz olan tüm girişimleri etkiler. 

Örneğin, * * * * önerisi, Azure Güvenlik Merkezi tarafından tüm Azure aboneliklerine atanan varsayılan ilke girişiminin bir parçasıdır. Ayrıca, XXXXX ' de de olur.

Bu öneri için bir istisna oluşturmaya çalışırsanız aşağıdaki iki iletiden birini görürsünüz:

- Her iki girişimi de düzenlemek için gerekli izinlere sahipseniz şunları görürsünüz:

    *Bu öneri çeşitli ilke girişimleriyle birlikte bulunur: [virgülle ayrılmış girişim adları]. Muafiyetler Bunların tümünde oluşturulur.*  

- Her iki girişim üzerinde yeterli izniniz yoksa, bunun yerine şu iletiyi görürsünüz:

    *Tüm ilke girişimlerinde muafiyeti uygulamak için sınırlı izinleriniz vardır. muafiyet yalnızca yeterli izinlere sahip girişimlerle oluşturulacaktır.*


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, güvenli puanınızı etkilememesi için bir kaynağı öneriden muaf tutmak öğrendiniz. Güvenli Puanlama hakkında daha fazla bilgi için bkz.

- [Azure Güvenlik Merkezi'nde güvenlik puanı](secure-score-security-controls.md)
