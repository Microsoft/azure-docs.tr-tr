---
title: Uyumsuzluk nedenlerini belirleme
description: Bir kaynak uyumsuz olduğunda birçok olası neden vardır. Uyumsuzluğun ne olduğunu öğrenin.
ms.date: 03/31/2021
ms.topic: how-to
ms.openlocfilehash: 66b0c6d7d0575e7361d04d16e6ba6fb06e36801b
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106094124"
---
# <a name="determine-causes-of-non-compliance"></a>Uyumsuzluk nedenlerini belirleme

Bir Azure kaynağının bir ilke kuralıyla uyumlu olmadığı belirlendiğinde, kuralın kaynağın hangi bölümünün uyumlu olmadığını anlamak yararlı olur. Ayrıca, daha önce uyumlu olmayan bir kaynağın uyumsuz hale getirmek için hangi değişikliğin değiştirilmediğini anlamak da yararlıdır. Bu bilgileri bulmanın iki yolu vardır:

- [Uyumluluk ayrıntıları](#compliance-details)
- [Değişiklik geçmişi (Önizleme)](#change-history)

## <a name="compliance-details"></a>Uyumluluk ayrıntıları

Bir kaynak uyumsuz olduğunda, bu kaynağa ilişkin uyumluluk ayrıntıları **ilke uyumluluğu** sayfasından kullanılabilir. Uyumluluk ayrıntıları bölmesi aşağıdaki bilgileri içerir:

- Ad, tür, konum ve kaynak KIMLIĞI gibi kaynak ayrıntıları
- Geçerli ilke atamasının en son değerlendirmesinin uyumluluk durumu ve zaman damgası
- Kaynak uyumsuzluğu için _nedenler_ listesi

> [!IMPORTANT]
> _Uyumlu olmayan_ bir kaynağın uyumluluk ayrıntıları söz konusu kaynaktaki özelliklerin geçerli değerini gösteriyorsa, kullanıcının kaynak **türüne** **okuma** işlemi olması gerekir. Örneğin, _uyumlu olmayan_ kaynak **Microsoft. COMPUTE/virtualmachines** ise, kullanıcının **Microsoft. COMPUTE/virtualmachines/Read** işlemi olması gerekir. Kullanıcının gerekli işlemi yoksa, bir erişim hatası görüntülenir.

Uyumluluk ayrıntılarını görüntülemek için aşağıdaki adımları izleyin:

1. **Tüm hizmetler**' i seçip **ilke** arayıp ' yi seçerek Azure Portal Azure ilke hizmetini başlatın.

1. **Genel bakış** veya **Uyumluluk** sayfasında, **uyumluluk durumunda** _uyumlu olmayan_ bir ilke seçin.

1. **İlke uyumluluğu** sayfasının **kaynak uyumluluğu** sekmesinde, _uyumlu olmayan_ bir **Uyumluluk durumundaki** kaynağın üç nokta simgesini sağ tıklayın veya seçin. Ardından **uyumluluk ayrıntılarını görüntüle**' yi seçin.

   :::image type="content" source="../media/determine-non-compliance/view-compliance-details.png" alt-text="Kaynak uyumluluğu sekmesindeki ' uyumluluk ayrıntılarını görüntüle ' bağlantısının ekran görüntüsü." border="false":::

1. **Uyumluluk ayrıntıları** bölmesi, kaynağın en son değerlendirmesinin bilgilerini geçerli ilke atamasına görüntüler. Bu örnekte, ilke tanımı _14,0_ beklenirken **Microsoft. SQL/Servers/Version** alanı _12,0_ olarak bulunur. Kaynak birden çok nedenden dolayı uyumlu değilse, her biri bu bölmede listelenir.

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane.png" alt-text="Uyumluluk ayrıntıları bölmesinin ekran görüntüsü ve geçerli değere ilişkin uyumsuzluk ve hedef değer on dört." border="false":::

   **Auditınotexists** veya **deployifnotexists** ilke tanımı için Ayrıntılar **. Type** özelliğini ve isteğe bağlı tüm özellikleri içerir. Bir liste için, bkz. [Auditınotexists Properties](../concepts/effects.md#auditifnotexists-properties) ve [deployifnotexists özellikleri](../concepts/effects.md#deployifnotexists-properties). **Son değerlendirilen kaynak** , tanımın **Ayrıntılar** bölümünden ilgili bir kaynaktır.

   Örnek kısmi **Deployifnotexists** tanımı:

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane-existence.png" alt-text="Değerlendirilen kaynak sayısı dahil olmak üzere ifNotExists için uyumluluk ayrıntıları bölmesinin ekran görüntüsü." border="false":::

> [!NOTE]
> Verileri korumak için, bir özellik değeri _gizli_ olduğunda, geçerli değer yıldız işaretlerini görüntüler.

Bu ayrıntılar, bir kaynağın Şu anda uyumsuz olduğunu, ancak kaynağın uyumsuz olmasına neden olan değişikliğin ne zaman yapıldığını göstermez. Bu bilgiler için aşağıdaki [değişiklik geçmişi (Önizleme)](#change-history) bölümüne bakın.

### <a name="compliance-reasons"></a>Uyumluluk nedenleri

Aşağıdaki matris, her olası _nedeni_ ilke tanımındaki sorumlu [koşula](../concepts/definition-structure.md#conditions) eşler:

|Nedeni | Koşul |
|-|-|
|Geçerli değer, anahtar olarak hedef değeri içermelidir. |containsKey veya notcontainskey **değil** |
|Geçerli değer hedef değer içermelidir. |notcontains içerir veya **içermez** |
|Geçerli değer, hedef değere eşit olmalıdır. |eşittir veya **Not** al |
|Geçerli değer, hedef değerden küçük olmalıdır. |greaterOrEquals daha az veya **Not** |
|Geçerli değer, hedef değere eşit veya ondan büyük olmalıdır. |greaterOrEquals veya daha az **değil** |
|Geçerli değer, hedef değerden büyük olmalıdır. |daha büyük veya **azalmayan** öğeler |
|Geçerli değer, hedef değere eşit veya ondan küçük olmalıdır. |Azallar veya daha büyük **değil** |
|Geçerli değer mevcut olmalıdır. |bulunur |
|Geçerli değer, hedef değerde olmalıdır. |notın içinde veya **değil** |
|Geçerli değer hedef değer gibi olmalıdır. |Beğen  gibi |
|Geçerli değer, büyük/küçük harfe duyarlı olmalıdır ve hedef değerle eşleşmelidir. |Match veya notmatch **değil** |
|Geçerli değer büyük/küçük harfe duyarsız olmalıdır ve hedef değerle eşleşmelidir. |matchInsensitively veya **Not** notMatchInsensitively |
|Geçerli değer, anahtar olarak hedef değeri içermemelidir. |notContainsKey veya bir ContainsKey **değil**|
|Geçerli değer, hedef değeri içermemelidir. |notContains veya **içermez** |
|Geçerli değer hedef değere eşit olmamalı. |Not harfler veya eşit **değildir** |
|Geçerli değer mevcut olmamalıdır. |**yok**  |
|Geçerli değerin hedef değerde olmaması gerekir. |Notın veya **Not** |
|Geçerli değer hedef değer gibi olmamalıdır. |notLike veya **Not** beğen |
|Geçerli değer, büyük/küçük harfe duyarlı olmalıdır ve hedef değerle eşleşmelidir. |notMatch veya **Match** |
|Geçerli değer büyük/küçük harfe duyarsız olmalıdır ve hedef değerle eşleşmez. |notMatchInsensitively veya **Not** matchInsensitively |
|İlke tanımındaki efekt ayrıntılarıyla eşleşen ilgili kaynak yok. |**Daha sonra. details. Type** içinde tanımlanan ve ilke kuralının **IF** bölümünde tanımlanan kaynakla ilişkili bir kaynak türü yok. |

## <a name="component-details-for-resource-provider-modes"></a>Kaynak sağlayıcısı modları için bileşen ayrıntıları

[Kaynak sağlayıcısı modundaki](../concepts/definition-structure.md#resource-manager-modes)atamalar için, daha derin bir görünüm açmak üzere _uyumlu olmayan_ kaynağını seçin. **Bileşen uyumluluğu** sekmesi altında, _uyumlu olmayan_ **bileşeni** ve **bileşen kimliğini** gösteren atanan ilkedeki kaynak sağlayıcısı moduna özgü ek bilgiler yer alır.

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="Bir kaynak sağlayıcısı modu ataması için bileşen uyumluluğu sekmesinin ekran görüntüsü ve uyumluluk ayrıntıları." border="false":::

## <a name="compliance-details-for-guest-configuration"></a>Konuk Yapılandırması için uyumluluk ayrıntıları

_Konuk yapılandırma_ kategorisindeki _Auditınotexists_ ilkeleri için, sanal makine içinde birden çok ayar değerlendirilmiş olabilir ve ayar başına ayrıntıları görüntülemeniz gerekir. Örneğin, parola ilkelerinin bir listesini denetliyoruz ve yalnızca birinin durumu _uyumlu değil_ ise, hangi parola ilkelerinin uyumsuz olduğunu ve neden olduğunu bilmeniz gerekir.

Ayrıca, sanal makinede doğrudan oturum açma erişiminiz olmayabilir, ancak sanal makinenin neden _uyumlu_ olmadığı hakkında rapor etmeniz gerekir.

### <a name="azure-portal"></a>Azure portalı

İlke uyumluluk ayrıntılarını görüntülemek için yukarıdaki bölümde bulunan adımları izleyerek başlayın.

Uyumluluk ayrıntıları bölmesi görünümünde, **son değerlendirilen kaynağı** bağla ' yı seçin.

:::image type="content" source="../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png" alt-text="Auditınotexists tanım uyumluluk ayrıntılarını görüntüleme ekran görüntüsü." border="false":::

**Konuk atama** sayfası tüm kullanılabilir uyumluluk ayrıntılarını görüntüler. Görünümdeki her satır, makine içinde gerçekleştirilen bir değerlendirmeyi temsil eder. **Neden** sütununda, Konuk atamasının neden _uyumlu olmadığı açıklanamayan_ bir ifade gösterilir. Örneğin, parola ilkelerini denetleriz, **Reason** sütununda her ayar için geçerli değer de dahil olmak üzere metin görüntülenir.

:::image type="content" source="../media/determine-non-compliance/guestconfig-compliance-details.png" alt-text="Konuk atama uyumluluğu ayrıntılarının ekran görüntüsü." border="false":::

## <a name="change-history-preview"></a><a name="change-history"></a>Değişiklik geçmişi (Önizleme)

Yeni bir **genel önizlemenin** parçası olarak, değişiklik geçmişinin son 14 günü, [tamamlanmış mod silme Işlemini](../../../azure-resource-manager/templates/complete-mode-deletion.md)destekleyen tüm Azure kaynakları için kullanılabilir. Değişiklik geçmişi bir değişikliğin ne zaman algılandığına ilişkin ayrıntıları ve her değişiklik için _görsel bir farkı_ sağlar. Azure Resource Manager Özellikler eklendiğinde, kaldırıldığında veya değiştirilmediğinde değişiklik algılama tetiklenir.

1. **Tüm hizmetler**' i seçip **ilke** arayıp ' yi seçerek Azure Portal Azure ilke hizmetini başlatın.

1. **Genel bakış** veya **Uyumluluk** sayfasında, herhangi bir **uyumluluk durumunda** bir ilke seçin.

1. **İlke uyumluluğu** sayfasının **kaynak uyumluluğu** sekmesinde bir kaynak seçin.

1. **Kaynak uyumluluğu** sayfasında **değişiklik geçmişi (Önizleme)** sekmesini seçin. Varsa, algılanan değişikliklerin bir listesi görüntülenir.

   :::image type="content" source="../media/determine-non-compliance/change-history-tab.png" alt-text="Değişiklik geçmişi sekmesinin ekran görüntüsü ve kaynak uyumluluğu sayfasında değişiklik süreleri algılandı." border="false":::

1. Algılanan değişikliklerden birini seçin. Kaynak için _görsel fark_ , **değişiklik geçmişi** sayfasında sunulur.

   :::image type="content" source="../media/determine-non-compliance/change-history-visual-diff.png" alt-text="Değişiklik geçmişi sayfasında özelliklerin önceki ve sonraki durumunun değişiklik geçmişi görsel farkı görüntüsünün ekran görüntüsü." border="false":::

Bir kaynakta yapılan değişiklikleri tanımlayan _görsel fark_ . Algılanan değişiklikler kaynağın geçerli uyumluluk durumuyla ilişkili olmayabilir.

Değişiklik geçmişi verileri [Azure Kaynak Grafiği](../../resource-graph/overview.md)tarafından sağlanır. Bu bilgileri Azure portal dışında sorgulamak için bkz. [kaynak değişikliklerini al](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure ilke örneklerindeki](../samples/index.md)örnekleri gözden geçirin.
- [Azure İlkesi tanımı yapısını](../concepts/definition-structure.md) gözden geçirin.
- [İlkenin etkilerini anlama](../concepts/effects.md) konusunu gözden geçirin.
- [Program aracılığıyla ilkelerin nasıl oluşturulduğunu](programmatically-create.md)anlayın.
- [Uyumluluk verilerini nasıl alabileceğinizi](get-compliance-data.md)öğrenin.
- [Uyumlu olmayan kaynakları nasıl düzelteceğinizi](remediate-resources.md)öğrenin.
- [Kaynakları Azure Yönetim gruplarıyla düzenleme](../../management-groups/overview.md)ile yönetim grubunun ne olduğunu inceleyin.
