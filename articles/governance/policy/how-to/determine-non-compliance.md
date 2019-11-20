---
title: Uyumsuzluk nedenlerini belirleme
description: Bir kaynak uyumsuz olduğunda birçok olası neden vardır. Uyumsuzluğun ne olduğunu öğrenin.
ms.date: 04/26/2019
ms.topic: conceptual
ms.openlocfilehash: 6f113080ab84fe7d159766e6543ff61b28f4ead2
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73959609"
---
# <a name="determine-causes-of-non-compliance"></a>Uyumsuzluk nedenlerini belirleme

Bir Azure kaynağının bir ilke kuralıyla uyumlu olmadığı belirlendiğinde, kuralın kaynağın hangi bölümünün uyumlu olmadığını anlamak yararlı olur. Ayrıca, daha önce uyumlu olmayan bir kaynağın uyumsuz hale getirmek için hangi değişikliğin değiştirilmediğini anlamak da yararlıdır. Bu bilgileri bulmanın iki yolu vardır:

> [!div class="checklist"]
> - [Uyumluluk ayrıntıları](#compliance-details)
> - [Değişiklik geçmişi (Önizleme)](#change-history)

## <a name="compliance-details"></a>Uyumluluk ayrıntıları

Bir kaynak uyumsuz olduğunda, bu kaynağa ilişkin uyumluluk ayrıntıları **ilke uyumluluğu** sayfasından kullanılabilir. Uyumluluk ayrıntıları bölmesi aşağıdaki bilgileri içerir:

- Ad, tür, konum ve kaynak KIMLIĞI gibi kaynak ayrıntıları
- Geçerli ilke atamasının en son değerlendirmesinin uyumluluk durumu ve zaman damgası
- Kaynak uyumsuzluğu için _nedenler_ listesi

> [!IMPORTANT]
> _Uyumlu olmayan_ bir kaynağın uyumluluk ayrıntıları söz konusu kaynaktaki özelliklerin geçerli değerini gösteriyorsa, kullanıcının kaynak **türüne** **okuma** işlemi olması gerekir. Örneğin, _uyumlu olmayan_ kaynak **Microsoft. COMPUTE/virtualmachines** ise, kullanıcının **Microsoft. COMPUTE/virtualmachines/Read** işlemi olması gerekir. Kullanıcının gerekli işlemi yoksa, bir erişim hatası görüntülenir.

Uyumluluk ayrıntılarını görüntülemek için aşağıdaki adımları izleyin:

1. Azure portalında **Tüm hizmetler**’e tıkladıktan sonra **İlke**'yi arayıp seçerek Azure İlkesi hizmetini başlatın.

1. **Genel bakış** veya **Uyumluluk** sayfasında, **uyumluluk durumunda** _uyumlu olmayan_bir ilke seçin.

1. **İlke uyumluluğu** sayfasının **kaynak uyumluluğu** sekmesinde, _uyumlu olmayan_bir **Uyumluluk durumundaki** kaynağın üç nokta simgesini sağ tıklayın veya seçin. Ardından **uyumluluk ayrıntılarını görüntüle**' yi seçin.

   ![Uyumluluk ayrıntılarını görüntüleme seçeneği](../media/determine-non-compliance/view-compliance-details.png)

1. **Uyumluluk ayrıntıları** bölmesi, kaynağın en son değerlendirmesinin bilgilerini geçerli ilke atamasına görüntüler. Bu örnekte, ilke tanımı _14,0_beklenirken **Microsoft. SQL/Servers/Version** alanı _12,0_ olarak bulunur. Kaynak birden çok nedenden dolayı uyumlu değilse, her biri bu bölmede listelenir.

   ![Uyumluluk ayrıntıları bölmesi ve uyumsuzluğun nedenleri](../media/determine-non-compliance/compliance-details-pane.png)

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

   ![Uyumluluk ayrıntıları bölmesi-* ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> Verileri korumak için, bir özellik değeri _gizli_ olduğunda, geçerli değer yıldız işaretlerini görüntüler.

Bu ayrıntılar, bir kaynağın Şu anda uyumsuz olduğunu, ancak kaynağın uyumsuz olmasına neden olan değişikliğin ne zaman yapıldığını göstermez. Bu bilgiler için aşağıdaki [değişiklik geçmişi (Önizleme)](#change-history) bölümüne bakın.

### <a name="compliance-reasons"></a>Uyumluluk nedenleri

Aşağıdaki matris, her olası _nedeni_ ilke tanımındaki sorumlu [koşula](../concepts/definition-structure.md#conditions) eşler:

|Neden | Koşul |
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
|Geçerli değer hedef değer gibi olmalıdır. |Beğen gibi |
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

## <a name="compliance-details-for-guest-configuration"></a>Konuk yapılandırması için uyumluluk ayrıntıları

_Konuk yapılandırma_ kategorisindeki _Auditınotexists_ ilkeleri için, VM 'de birden fazla ayar değerlendirilebilecek ve ayar başına ayrıntıları görüntülemeniz gerekir. Örneğin, parola ilkelerinin bir listesini denetliyoruz ve yalnızca birinin durumu _uyumlu değil_ise, hangi parola ilkelerinin uyumsuz olduğunu ve neden olduğunu bilmeniz gerekir.

Ayrıca, VM 'de doğrudan oturum açma erişimi de olmayabilir, ancak VM 'nin neden _uyumlu_olmadığı hakkında rapor etmeniz gerekir.

### <a name="azure-portal"></a>Azure portal

İlke uyumluluk ayrıntılarını görüntülemek için yukarıdaki bölümde bulunan adımları izleyerek başlayın.

**Uyumluluk ayrıntıları** bölmesi görünümünde, **son değerlendirilen kaynağı**bağla ' ya tıklayın.

   ![Auditınotexists tanım ayrıntılarını görüntüle](../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png)

**Konuk atama** sayfası tüm kullanılabilir uyumluluk ayrıntılarını görüntüler. Görünümdeki her satır, makine içinde gerçekleştirilen bir değerlendirmeyi temsil eder. **Neden** sütununda, Konuk atamasının neden _uyumlu_ olmadığı açıklayan bir ifade gösterilmektedir. Örneğin, parola ilkelerini denetleriz, **Reason** sütununda her ayar için geçerli değer de dahil olmak üzere metin görüntülenir.

![Uyumluluk ayrıntılarını görüntüle](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell uyumluluk ayrıntılarını da görüntüleyebilirsiniz. İlk olarak, Konuk yapılandırma modülünün yüklü olduğundan emin olun.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

Aşağıdaki komutu kullanarak, bir VM için tüm konuk atamalarının geçerli durumunu görüntüleyebilirsiniz:

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Yalnızca VM 'nin neden _uyumsuz_olduğunu açıklayan _neden_ ifadesini görüntülemek için, yalnızca Child özelliğini döndürün.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

Ayrıca, makinenin kapsamındaki Konuk atamaları için bir uyumluluk geçmişi de çıktısını alabilirsiniz. Bu komutun çıktısı, sanal makine için her bir raporun ayrıntılarını içerir.

> [!NOTE]
> Çıktı, büyük miktarda veri döndürebilir. Çıktıyı bir değişkende depolamanız önerilir.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname>
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
[Preview]: Audit that an application is installed inside Windows VMs      NonCompliant                       02/10/2019 12:00:38 PM 02/10/2019 12:00:41 PM VM01  ../17fg0...
<truncated>
```

Bu görünümü basitleştirmek için, **Showchanged** parametresini kullanın. Bu komutun çıktısı yalnızca uyumluluk durumunda değişiklik yapan raporları içerir.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname> -ShowChanged
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/10/2019 10:00:38 PM 02/10/2019 10:00:41 PM VM01  ../12ab0...
Audit that an application is installed inside Windows VMs.                Compliant                          02/09/2019 11:00:38 AM 02/09/2019 11:00:39 AM VM01  ../e3665...
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/09/2019 09:00:20 AM 02/09/2019 09:00:23 AM VM01  ../15ze1...
```

## <a name="a-namechange-historychange-history-preview"></a>Değişiklik geçmişi <a name="change-history"/>(Önizleme)

Yeni bir **genel önizlemenin**parçası olarak, değişiklik geçmişinin son 14 günü, [tamamlanmış mod silme Işlemini](../../../azure-resource-manager/complete-mode-deletion.md)destekleyen tüm Azure kaynakları için kullanılabilir. Değişiklik geçmişi bir değişikliğin ne zaman algılandığına ilişkin ayrıntıları ve her değişiklik için _görsel bir farkı_ sağlar. Kaynak Yöneticisi Özellikler eklendiğinde, kaldırıldığında veya değiştirilmediğinde değişiklik algılama tetiklenir.

1. Azure portalında **Tüm hizmetler**’e tıkladıktan sonra **İlke**'yi arayıp seçerek Azure İlkesi hizmetini başlatın.

1. **Genel bakış** veya **Uyumluluk** sayfasında, herhangi bir **uyumluluk durumunda**bir ilke seçin.

1. **İlke uyumluluğu** sayfasının **kaynak uyumluluğu** sekmesinde bir kaynak seçin.

1. **Kaynak uyumluluğu** sayfasında **değişiklik geçmişi (Önizleme)** sekmesini seçin. Varsa, algılanan değişikliklerin bir listesi görüntülenir.

   ![Kaynak uyumluluğu sayfasında Azure Ilke değiştirme geçmişi sekmesi](../media/determine-non-compliance/change-history-tab.png)

1. Algılanan değişikliklerden birini seçin. Kaynak için _görsel fark_ , **değişiklik geçmişi** sayfasında sunulur.

   ![Değişiklik geçmişi sayfasında Azure Ilkesi değişiklik geçmişi görsel farkı](../media/determine-non-compliance/change-history-visual-diff.png)

Bir kaynakta yapılan değişiklikleri tanımlayan _görsel fark_ . Algılanan değişiklikler kaynağın geçerli uyumluluk durumuyla ilişkili olmayabilir.

Değişiklik geçmişi verileri [Azure Kaynak Grafiği](../../resource-graph/overview.md)tarafından sağlanır. Bu bilgileri Azure portal dışında sorgulamak için bkz. [kaynak değişikliklerini al](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure ilke örneklerindeki](../samples/index.md)örnekleri gözden geçirin.
- [Azure İlkesi tanımı yapısını](../concepts/definition-structure.md) gözden geçirin.
- [İlkenin etkilerini anlama](../concepts/effects.md) konusunu gözden geçirin.
- [Program aracılığıyla ilkelerin nasıl oluşturulduğunu](programmatically-create.md)anlayın.
- [Uyumluluk verilerini nasıl alabileceğinizi](getting-compliance-data.md)öğrenin.
- [Uyumlu olmayan kaynakları nasıl düzelteceğinizi](remediate-resources.md)öğrenin.
- [Kaynakları Azure Yönetim gruplarıyla düzenleme](../../management-groups/overview.md)ile yönetim grubunun ne olduğunu inceleyin.
