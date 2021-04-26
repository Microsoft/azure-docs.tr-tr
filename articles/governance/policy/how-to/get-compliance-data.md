---
title: İlke uyumluluk verilerini al
description: Azure Ilke değerlendirmeleri ve etkileri uyumluluğu tespit edin. Azure kaynaklarınızın uyumluluk ayrıntılarını nasıl alabileceğinizi öğrenin.
ms.date: 04/19/2021
ms.topic: how-to
ms.openlocfilehash: e1a9a7fcbbcbd7f490b2f665b40c7ed922ec61ee
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864604"
---
# <a name="get-compliance-data-of-azure-resources"></a>Azure kaynaklarının uyumluluk verilerini alın

Azure Ilkesinin en büyük avantajlarından biri, bir abonelik ya da bir abonelik [Yönetim grubundaki](../../management-groups/overview.md) kaynaklar üzerinde sağladığı Öngörüler ve denetimlerdir. Bu denetim, kaynakları yanlış konumda oluşturulmasını önlemek, ortak ve tutarlı etiket kullanımı uygulanmasını sağlamak ya da uygun yapılandırma ve ayarlar için mevcut kaynakları denetlemek gibi birçok farklı yolla uygulanabilir. Her durumda, ortamınız uyumluluk durumunu anlamanıza olanak tanımak için Azure Ilkesi tarafından veriler oluşturulur.

İlke ve girişim atamalarınız tarafından oluşturulan uyumluluk bilgilerine erişmenin birkaç yolu vardır:

- [Azure Portal](#portal) kullanma
- [Komut satırı](#command-line) komut dosyası aracılığıyla

Uyumluluk hakkında raporlama yöntemlerine bakmadan önce uyumluluk bilgilerinin ne zaman güncelleştirildiğini ve bir değerlendirme döngüsünü tetikleyen sıklığı ve olayları inceleyelim.

> [!WARNING]
> Uyumluluk durumu **kayıtlı değil** olarak bildiriliyorsa, **Microsoft. policınghts** kaynak sağlayıcısının kayıtlı olduğunu ve kullanıcının Azure [ilkesinde Azure RBAC izinlerinde](../overview.md#azure-rbac-permissions-in-azure-policy)açıklandığı gibi uygun Azure rol tabanlı erişim denetimi (Azure RBAC) izinlerine sahip olduğunu doğrulayın.

## <a name="evaluation-triggers"></a>Değerlendirme Tetikleyicileri

Tamamlanmış bir değerlendirme döngüsünün sonuçları, `Microsoft.PolicyInsights` kaynak sağlayıcıda `PolicyStates` ve işlemler aracılığıyla kullanılabilir `PolicyEvents` . Azure Policy Insights REST API işlemleri hakkında daha fazla bilgi için bkz. [Azure Ilke öngörüleri](/rest/api/policy/).

Atanan ilkelerin ve girişimlerin değerlendirmeleri çeşitli olayların sonucu olarak gerçekleşir:

- Bir ilke veya girişim yeni bir kapsama atanır. Atamanın tanımlanan kapsama uygulanması 30 dakika içinde sürer. Uygulandıktan sonra, değerlendirme çevrimi, bu kapsamdaki kaynaklar için yeni atanan ilke veya girişim ile başlar ve ilke ya da girişim tarafından kullanılan etkilere bağlı olarak, kaynaklar uyumlu, uyumlu değil veya muaf olarak işaretlenir. Büyük bir kaynak kapsamı için değerlendirilen büyük bir ilke veya girişim zaman alabilir. Bu nedenle, değerlendirme döngüsünün tamamlandığı zaman önceden tanımlı bir beklentisi yoktur. İşlem tamamlandıktan sonra portalda ve SDK 'larda güncelleştirilmiş uyumluluk sonuçları bulunur.

- Bir kapsama zaten atanmış olan bir ilke veya girişim güncellenir. Bu senaryonun değerlendirme çevrimi ve zamanlaması, bir kapsama yönelik yeni atama ile aynıdır.

- Bir kaynak, Azure Resource Manager, REST API veya desteklenen bir SDK aracılığıyla atama ile bir kapsam içine dağıtılır veya güncelleştirilir. Bu senaryoda, tek tek kaynak için etkinlik olayı (ekleme, denetim, reddetme, dağıtma) ve uyumlu durum bilgileri portalda ve bu süre içinde 15 dakika daha sonra SDK 'larda kullanılabilir hale gelir. Bu olay diğer kaynakların değerlendirilmesine neden olmaz.

- Bir abonelik (kaynak türü `Microsoft.Resource/subscriptions` ), abonelik kaynak türünü hedefleyen atanmış bir ilke tanımıyla bir [Yönetim grubu hiyerarşisi](../../management-groups/overview.md) içinde oluşturulur veya taşınır. Abonelik tarafından desteklenen efektlerin değerlendirmesi (denetim, Auditınotexist, deployIfNotExists, MODIFY), günlüğe kaydetme ve düzeltme eylemleri 30 dakika sürer.

- [İlke muafiyeti](../concepts/exemption-structure.md) oluşturulur, güncellenir veya silinir. Bu senaryoda, ilgili atama tanımlanan muafiyet kapsamı için değerlendirilir.

- Standart uyumluluk değerlendirme çevrimi. Her 24 saatte bir, atamalar otomatik olarak yeniden değerlendirilecektir. Çok sayıda kaynak için büyük bir ilke veya girişim zaman alabilir, bu nedenle değerlendirme döngüsünün tamamlandığı zaman önceden tanımlanmış bir beklentisi yoktur. İşlem tamamlandıktan sonra portalda ve SDK 'larda güncelleştirilmiş uyumluluk sonuçları bulunur.

- [Konuk yapılandırma](../concepts/guest-configuration.md) kaynak sağlayıcısı, yönetilen bir kaynak tarafından uyumluluk ayrıntılarıyla güncelleştirildi.

- İsteğe bağlı tarama

### <a name="on-demand-evaluation-scan"></a>İsteğe bağlı değerlendirme taraması

Abonelik veya kaynak grubu için bir değerlendirme taraması Azure CLı, Azure PowerShell, REST API çağrısıyla veya [Azure Ilke uyumluluk taraması GitHub eylemi](https://github.com/marketplace/actions/azure-policy-compliance-scan)kullanılarak başlatılabilir.
Bu tarama zaman uyumsuz bir işlemdir.

#### <a name="on-demand-evaluation-scan---github-action"></a>İsteğe bağlı değerlendirme taraması-GitHub eylemi

Bir veya birden çok kaynakta, kaynak grubunda veya aboneliklerde [GitHub iş akışınız](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) için isteğe bağlı bir değerlendirme taraması tetikleyip, kaynakların uyumluluk durumuna göre iş akışını geçit halinde almak Için [Azure ilke uyumluluk tarama eylemini](https://github.com/marketplace/actions/azure-policy-compliance-scan) kullanın. İş akışını zamanlanan bir saatte çalışacak şekilde de yapılandırabilirsiniz, böylelikle en son uyumluluk durumu uygun bir zamanda alınır. İsteğe bağlı olarak, bu GitHub eylemi, daha fazla analiz veya arşivleme için taranan kaynakların uyumluluk durumu hakkında bir rapor oluşturabilir.

Aşağıdaki örnek bir abonelik için uyumluluk taraması çalıştırır. 

```yaml
on:
  schedule:    
    - cron:  '0 8 * * *'  # runs every morning 8am
jobs:
  assess-policy-compliance:    
    runs-on: ubuntu-latest
    steps:         
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}} 

    
    - name: Check for resource compliance
      uses: azure/policy-compliance-scan@v0
      with:
        scopes: |
          /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

Daha fazla bilgi ve iş akışı örnekleri için bkz. [Azure Ilke uyumluluk taraması deposu Için GitHub eylemi](https://github.com/Azure/policy-compliance-scan).

#### <a name="on-demand-evaluation-scan---azure-cli"></a>İsteğe bağlı değerlendirme taraması-Azure CLı

Uyumluluk taraması [az Policy State TRIGGER-Scan](/cli/azure/policy/state#az_policy_state_trigger_scan) komutuyla başlatılır.

Varsayılan olarak, `az policy state trigger-scan` geçerli abonelikteki tüm kaynaklar için bir değerlendirme başlatır. Belirli bir kaynak grubunda bir değerlendirmeyi başlatmak için, **Resource-Group** parametresini kullanın. Aşağıdaki örnek, _Myrg_ kaynak grubu için geçerli abonelikte bir uyumluluk taraması başlatır:

```azurecli-interactive
az policy state trigger-scan --resource-group "MyRG"
```

Bekleme parametresi olmadan devam etmeden önce, zaman uyumsuz işlemin tamamlanmasını **beklememe** seçeneğini belirleyebilirsiniz.

#### <a name="on-demand-evaluation-scan---azure-powershell"></a>İsteğe bağlı değerlendirme taraması-Azure PowerShell

Uyumluluk taraması [Start-Azpolicykarmaşıkancescan](/powershell/module/az.policyinsights/start-azpolicycompliancescan) cmdlet 'i ile başlatılır.

Varsayılan olarak, `Start-AzPolicyComplianceScan` geçerli abonelikteki tüm kaynaklar için bir değerlendirme başlatır. Belirli bir kaynak grubunda bir değerlendirmeyi başlatmak için **Resourcegroupname** parametresini kullanın. Aşağıdaki örnek, _Myrg_ kaynak grubu için geçerli abonelikte bir uyumluluk taraması başlatır:

```azurepowershell-interactive
Start-AzPolicyComplianceScan -ResourceGroupName 'MyRG'
```

Zaman uyumsuz çağrının sonuçları elde etmeden önce veya bir [iş](/powershell/module/microsoft.powershell.core/about/about_jobs)arka planda çalışmasını sağlamak için PowerShell 'in tamamlanmasını beklemesini sağlayabilirsiniz. Uyumluluk taramasını arka planda çalıştırmak üzere bir PowerShell işi kullanmak için, **AsJob** parametresini kullanın ve değeri bu örnekte olduğu gibi bir nesne olarak ayarlayın `$job` :

```azurepowershell-interactive
$job = Start-AzPolicyComplianceScan -AsJob
```

Nesneyi denetleyerek, işin durumunu kontrol edebilirsiniz `$job` . İş türündedir `Microsoft.Azure.Commands.Common.AzureLongRunningJob` . `Get-Member` `$job` Kullanılabilir özellikleri ve yöntemleri görmek için nesnesi üzerinde kullanın.

Uyumluluk taraması çalışırken, `$job` nesne aşağıdaki gibi sonuçlar verir:

```azurepowershell-interactive
$job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
2      Long Running O… AzureLongRunni… Running       True            localhost            Start-AzPolicyCompliance…
```

Uyumluluk taraması tamamlandığında **durum** özelliği _tamamlandı_ olarak değişir.

#### <a name="on-demand-evaluation-scan---rest"></a>İsteğe bağlı değerlendirme taraması-REST

Zaman uyumsuz bir işlem olarak, taramayı başlatmak için REST uç noktası, taramanın yanıt vermesi tamamlanana kadar beklemez. Bunun yerine, istenen değerlendirmenin durumunu sorgulamak için bir URI sağlar.

Her bir REST API URI'sinde kendi değerlerinizle değiştirmeniz gereken değişkenler bulunur:

- `{YourRG}` -Kaynak grubunuzun adıyla değiştirin
- `{subscriptionId}` - Abonelik kimliğinizle değiştirin

Tarama, bir abonelikteki veya bir kaynak grubundaki kaynakların değerlendirilmesini destekler. Aşağıdaki URI yapılarını kullanarak bir REST API **Post** komutuyla kapsama göre tarama başlatın:

- Abonelik

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

- Kaynak grubu

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

Çağrı, **202 kabul edilen** bir durum döndürür. Yanıt üst bilgisine eklenen bir **konum** özelliği aşağıdaki biçimdedir:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2019-10-01
```

`{ResourceContainerGUID}` , istenen kapsam için statik olarak oluşturulur. Bir kapsam zaten bir isteğe bağlı tarama çalıştırıyorsa yeni bir tarama başlatılmaz. Bunun yerine, yeni istek `{ResourceContainerGUID}` durum için aynı **konum** URI 'si olarak sağlanır. **Konum** URI 'sine yönelik bir REST API **Get** komutu, değerlendirme devam ederken **kabul edilen bir 202** döndürür. Değerlendirme taraması tamamlandığında, **200 bir Tamam** durumu döndürür. Tamamlanmış taramanın gövdesi, şu duruma sahip bir JSON yanıtdır:

```json
{
    "status": "Succeeded"
}
```

#### <a name="on-demand-evaluation-scan---visual-studio-code"></a>İsteğe bağlı değerlendirme taraması-Visual Studio Code

Visual Studio Code için Azure Ilke uzantısı, belirli bir kaynak için bir değerlendirme taraması çalıştırabilme özelliğine sahiptir. Bu tarama, Azure PowerShell ve REST yöntemlerinin aksine zaman uyumlu bir işlemdir.
Ayrıntılar ve adımlar için bkz. [vs Code Uzantısı ile isteğe bağlı değerlendirme](./extension-for-vscode.md#on-demand-evaluation-scan).

## <a name="how-compliance-works"></a>Uyumluluk nasıl işe yarar?

Bir atamada, ilke veya girişim kurallarını takip etmez ve _muaf tutulmazsa_ bir kaynak **uyumlu** değildir. Aşağıdaki tabloda, farklı ilke efektlerinin, sonuçta elde edilen uyumluluk durumu için koşul değerlendirmesiyle nasıl çalıştığı gösterilmektedir:

| Kaynak Durumu | Etki | İlke Değerlendirmesi | Uyumluluk Durumu |
| --- | --- | --- | --- |
| Yeni veya Güncelleştirilmiş | Denetim, değiştirme, Auditınotexist | Doğru | Uyumlu Değil |
| Yeni veya Güncelleştirilmiş | Denetim, değiştirme, Auditınotexist | Yanlış | Uyumlu |
| Var | Reddet, denetle, Ekle, Değiştir, DeployIfNotExist, AuditIfNotExist | Doğru | Uyumlu Değil |
| Var | Reddet, denetle, Ekle, Değiştir, DeployIfNotExist, AuditIfNotExist | Yanlış | Uyumlu |

> [!NOTE]
> DeployIfNotExist ve Auditınotexist etkileri, IF ifadesinin TRUE olmasını ve varlık koşulunun uyumsuz olması için FALSE olmasını gerektirir. TRUE olduğunda, IF koşulu ilgili kaynaklar için varlık koşulunun değerlendirilmesini tetikler.

Örneğin, ortak ağlara sunulan bazı depolama hesaplarıyla (kırmızı renkle vurgulanmış) bir kaynak grubunuz olduğunu varsayalım.

:::image type="complex" source="../media/getting-compliance-data/resource-group01.png" alt-text="Contoso R G kaynak grubundaki ortak ağlara açık olan depolama hesaplarının diyagramı." border="false":::
   Contoso R G kaynak grubundaki beş depolama hesabının görüntülerini gösteren diyagram.  Depolama hesapları bir ve üçü mavi, ancak iki, dört ve beş depolama hesabı kırmızı.
:::image-end:::

Bu örnekte, güvenlik riskleri konusunda dikkatli olmanız gerekir. Artık bir ilke ataması oluşturduğunuza göre, ContosoRG kaynak grubundaki tüm dahil edilmiş ve muaf olmayan depolama hesapları için değerlendirilir. Bu, uyumlu olmayan üç depolama hesabını denetler, sonuç olarak durumlarını **uyumlu değil** olarak değiştirir.

:::image type="complex" source="../media/getting-compliance-data/resource-group03.png" alt-text="Contoso R G kaynak grubundaki depolama hesabı uyumluluğu diyagramı." border="false":::
   Contoso R G kaynak grubundaki beş depolama hesabının görüntülerini gösteren diyagram. Şu anda bir ve üç depolama hesabının altında yeşil onay işaretleri vardır. iki, dört ve beş depolama hesabı artık bunların altında kırmızı uyarı işaretlerine sahiptir.
:::image-end:::

**Uyumlu** ve **uyumsuz**, ilkelerin ve kaynakların yanı sıra diğer dört durum da vardır:

- **Muaf**: kaynak atama kapsamıdır, ancak [tanımlı bir istisna](../concepts/exemption-structure.md)vardır.
- **Çakışıyor**: çakışan kurallarla iki veya daha fazla ilke tanımı var. Örneğin, iki tanım aynı etiketi farklı değerlerle birlikte ekler.
- **Başlamadı**: ilke veya kaynak için değerlendirme çevrimi başlatılmadı.
- **Kayıtlı değil**: Azure Ilke kaynak sağlayıcısı kayıtlı değil veya oturum açmış hesabın uyumluluk verilerini okuma izni yok.

Azure Ilkesi, bir kaynağın eşleşme olup olmadığını anlamak için tanımda **tür**, **ad** veya **tür** alanlarını kullanır. Kaynak eşleştiğinde, uygulanabilir kabul edilir ve **uyumlu**, **uyumsuz** ya da **muaf** bir duruma sahip olur. Her iki **tür**, **ad** veya **tür** , tanımdaki tek özelliktir, dahil edilen ve muaf olmayan tüm kaynaklar uygulanabilir kabul edilir ve değerlendirilir.

Uyumluluk yüzdesi, **uyumlu** ve **muaf** kaynakları _Toplam kaynağa_ bölerek belirlenir. _Toplam kaynak_ , **uyumlu**, **uyumlu olmayan**, **muaf** ve **Çakışan** kaynakların toplamı olarak tanımlanır. Genel uyumluluk numaraları, tüm ayrı kaynakların toplamına göre **uyumlu** veya **muaf tutulan** ayrı kaynakların toplamıdır. Aşağıdaki görüntüde, uygulanabilir ve yalnızca bir tane **uyumlu olmayan** 20 farklı kaynak vardır.
Genel kaynak uyumluluğu %95 ' dir (19/20).

:::image type="content" source="../media/getting-compliance-data/simple-compliance.png" alt-text="Uyumluluk sayfasından ilke uyumluluğu ayrıntılarının ekran görüntüsü." border="false":::

> [!NOTE]
> Azure Ilkesinde mevzuata uyumluluğu bir önizleme özelliğidir. SDK 'dan ve portaldaki sayfalardan uyumluluk özellikleri, etkin girişimler için farklıdır. Daha fazla bilgi için bkz. [mevzuat uyumluluğu](../concepts/regulatory-compliance.md)

## <a name="portal"></a>Portal

Azure portal, ortamınızdaki uyumluluk durumunu görselleştirmenin ve anlayabilecek grafiksel bir deneyim gösterir. **İlke** sayfasında **genel bakış** seçeneği, hem ilkelerin hem de girişimlerin uyumluluğuyla kullanılabilir kapsamlar için ayrıntılar sağlar. Atama başına uyumluluk durumu ve sayı ile birlikte, son yedi güne ait uyumluluğu gösteren bir grafik içerir. **Uyumluluk** sayfası, bu bilgilerin büyük bölümünü (grafik dışında) içerir, ancak ek filtreleme ve sıralama seçenekleri sağlar.

:::image type="content" source="../media/getting-compliance-data/compliance-page.png" alt-text="Uyumluluk sayfasının ekran görüntüsü, filtreleme seçenekleri ve ayrıntıları." border="false":::

Bir ilke veya girişim farklı kapsamlara atanabileceği için tablo, her atamanın kapsamını ve atanan tanım türünü içerir. Her atamaya yönelik uyumlu olmayan kaynakların ve uyumlu olmayan ilkelerin sayısı da sağlanır. Tablodaki bir ilke veya girişim üzerinde seçim yapmak, söz konusu atamaya yönelik uyumluluğa daha ayrıntılı bir bakış sağlar.

:::image type="content" source="../media/getting-compliance-data/compliance-details.png" alt-text="Sayar ve kaynakla uyumlu ayrıntılar dahil olmak üzere uyumluluk Ayrıntıları sayfasının ekran görüntüsü." border="false":::

**Kaynak uyumluluğu** sekmesindeki kaynakların listesi, geçerli atama için mevcut kaynakların değerlendirme durumunu gösterir. Sekme varsayılan olarak **uyumlu değildir**, ancak filtrelenebilir.
Kaynak oluşturma isteği tarafından tetiklenen olaylar (ekleme, denetim, reddetme, dağıtma, değiştirme), **Olaylar** sekmesinde gösterilir.

> [!NOTE]
> AKS altyapı ilkesi için, gösterilen kaynak kaynak grubudur.

:::image type="content" source="../media/getting-compliance-data/compliance-events.png" alt-text="Uyumluluk ayrıntıları sayfasındaki olaylar sekmesinin ekran görüntüsü." border="false":::

<a name="component-compliance"></a> Kaynak [sağlayıcısı modu](../concepts/definition-structure.md#resource-provider-modes) kaynakları için kaynak **uyumluluğu** sekmesinde, kaynağı seçip satırı sağ tıklatıp **görünüm uyumluluk ayrıntıları** ' nı seçerek bileşen uyumluluk ayrıntılarını açılır. Bu sayfa, bu kaynağa, olaylara, bileşen olaylarına ve değişiklik geçmişine atanan ilkeleri görmek için de sekmeler sağlar.

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="Bir kaynak sağlayıcısı modu ataması için bileşen uyumluluğu sekmesinin ekran görüntüsü ve uyumluluk ayrıntıları." border="false":::

Kaynak uyumluluğu sayfasına dönün, daha fazla ayrıntı toplamak istediğiniz olayın satırına sağ tıklayın ve **etkinlik günlüklerini göster**' i seçin. Etkinlik günlüğü sayfası açılır ve atamanın ayrıntılarını ve olayları gösteren aramaya önceden filtrelenmiştir. Etkinlik günlüğü ek bağlam ve bu olaylar hakkında bilgi sağlar.

:::image type="content" source="../media/getting-compliance-data/compliance-activitylog.png" alt-text="Azure Ilkesi etkinlikleri ve değerlendirmeleri için etkinlik günlüğü ekranının ekran görüntüsü." border="false":::

### <a name="understand-non-compliance"></a>Uyumsuzluğun anlaşılma

Bir kaynağın **uyumsuz** olduğu belirlendiğinde birçok olası neden vardır. Kaynağın **uyumsuz** olma nedenini öğrenmek veya sorumluyu bulmak için bkz. [uyumsuzluğu belirleme](./determine-non-compliance.md).

## <a name="command-line"></a>Komut satırı

Portalda bulunan bilgiler, REST API ( [Armclient](https://github.com/projectkudu/ARMClient)dahil), Azure PowerShell ve Azure CLI ile alınabilir. REST API hakkında tam Ayrıntılar için bkz. [Azure ilke](/rest/api/policy/) başvurusu. REST API başvuru sayfalarında, her bir işlemin üzerinde yeşil bir ' deneyin ' düğmesi vardır ve bu, tarayıcıyı tarayıcıda doğrudan denemenize olanak tanır.

REST API örnekleri için Azure kimlik doğrulamasını işlemek üzere ARMClient veya benzer bir araç kullanın.

### <a name="summarize-results"></a>Sonuçları özetleme

REST API, özetleme kapsayıcı, tanım veya atamaya göre gerçekleştirilebilir. Azure Policy Insight 'ın abonelik Özeti kullanılarak abonelik düzeyinde [özetlemeye](/rest/api/policy/policystates/summarizeforsubscription)bir örnek aşağıda verilmiştir:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2019-10-01
```

Çıktı, aboneliği özetler. Aşağıdaki örnek çıktıda, özetlenen uyumluluk **değer. Results., uyumsuz Antresources** ve **Value. Results. Nonkarmaşıkolmayan bir ilke**. Bu istek, her atamaya yönelik uyumlu olmayan numaraları ve tanım bilgilerini oluşturan her atama dahil olmak üzere daha fazla ayrıntı sağlar. Hiyerarşideki her ilke nesnesi, bu düzeyde ek ayrıntı almak için kullanılabilecek bir **Queryresultsurı** sağlar.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant'",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Kaynaklar için sorgu

Yukarıdaki örnekte **Value. Policyaslamalar. policyDefinitions. Results. queryResultsUri** , belirli bir ilke tanımı için uyumlu olmayan tüm kaynaklar için örnek bir URI sağlar. **$Filter** değerine bakarak, ' uyumsuz ' olarak eşit (EQ), ilke tanımı Için Policyassignmentıd belirtilir ve sonra Policydefinitionıd 'nin kendisi olur. Filtrede Policyassignmentıd ekleme nedeni, Policydefinitionıd 'nin farklı kapsamlarla çeşitli ilkede veya girişim atamalarında mevcut olmasından kaynaklanır. Hem Policyassignmentıd hem de Policydefinitionıd belirterek aradığımız sonuçlara açık bir şekilde olabiliriz. Daha önce, PolicyStates için son **24 saatin bir** **Kimden** ve zaman penceresini otomatik olarak ayarlayan **en son** kullandık.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Aşağıdaki örnek yanıt, kısaltma için uyumlu olmayan tek bir kaynağa kırpılmıştır. Ayrıntılı yanıtta, kaynak, ilke veya girişim ve atama hakkında çeşitli veri parçaları bulunur. İlke tanımına hangi atama parametrelerinin geçtiğini de görebileceğinize dikkat edin.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "ComplianceState": "NonCompliant",
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>Etkinlikleri görüntüleme

Bir kaynak oluşturulduğunda veya güncelleştirilirken bir ilke değerlendirme sonucu oluşturulur. Sonuçlara _ilke olayları_ denir. Abonelikle ilişkili en son ilke olaylarını görüntülemek için aşağıdaki URI 'yi kullanın.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2019-10-01
```

Sonuçlarınız aşağıdaki örneğe benzer:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

İlke olaylarını sorgulama hakkında daha fazla bilgi için bkz. [Azure Ilke olayları](/rest/api/policy/policyevents) başvuru makalesi.

### <a name="azure-cli"></a>Azure CLI

Azure Ilkesi için [Azure CLI](/cli/azure/what-is-azure-cli) komut grubu, REST veya Azure PowerShell bulunan birçok işlemi içerir. Kullanılabilir komutların tam listesi için bkz. [Azure CLI-Azure Ilkesine genel bakış](/cli/azure/policy).

Örnek: en yüksek sayıda uyumsuz kaynağa sahip en üstteki atanan ilkeye ait durum özeti alınıyor.

```azurecli-interactive
az policy state summarize --top 1
```

Yanıtın en üst kısmı Şu örneğe benzer şekilde görünür:

```json
{
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
    "odataid": null,
    "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
            "policyDefinitions": [{
                "effect": "audit",
                "policyDefinitionGroupNames": [
                    ""
                ],
                "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
                "policyDefinitionReferenceId": "",
                "results": {
                    "nonCompliantPolicies": null,
                    "nonCompliantResources": 398,
                    "policyDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "policyGroupDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2020-07-14 14:01:22Z&$to=2020-07-15 14:01:22Z and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8' and PolicyDefinitionId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a'",
                    "resourceDetails": [{
                            "complianceState": "noncompliant",
                            "count": 398
                        },
                        {
                            "complianceState": "compliant",
                            "count": 4
                        }
                    ]
                }
            }],
    ...
```

Örnek: en son değerlendirilen kaynağın durum kaydını alma (varsayılan değer zaman damgasına göre azalan sırada).

```azurecli-interactive
az policy state list --top 1
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "Compliant",
    "effectiveParameters": "",
    "isCompliant": true,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/securitycenterbuiltin",
    "policyAssignmentName": "SecurityCenterBuiltIn",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "auditifnotexists",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionName": "aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionReferenceId": "identityenablemfaforownerpermissionsmonitoring",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "security center",
    "policySetDefinitionId": "/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionName": "1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "",
    "resourceId": "/subscriptions/{subscriptionId}",
    "resourceLocation": "",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Resources/subscriptions",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.903433+00:00"
  }
]
```

Örnek: uyumlu olmayan tüm sanal ağ kaynaklarının ayrıntılarını alma.

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'"
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

Örnek: belirli bir tarihten sonra gerçekleşen, uyumlu olmayan sanal ağ kaynaklarıyla ilgili olayları alma.

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'" --from '2020-07-14T00:00:00Z'
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

### <a name="azure-powershell"></a>Azure PowerShell

Azure Ilkesi için Azure PowerShell modülü, PowerShell Galerisi [az. Poliyghts](https://www.powershellgallery.com/packages/Az.PolicyInsights)olarak kullanılabilir. PowerShellGet kullanarak modülünü kullanarak yükleyebilirsiniz `Install-Module -Name Az.PolicyInsights` (en son [Azure PowerShell](/powershell/azure/install-az-ps) yüklü olduğundan emin olun):

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

Modül aşağıdaki cmdlet 'lere sahiptir:

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Örnek: en yüksek sayıda uyumsuz kaynağa sahip en üstteki atanan ilkeye ait durum özeti alınıyor.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Örnek: en son değerlendirilen kaynağın durum kaydını alma (varsayılan değer zaman damgasına göre azalan sırada).

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Örnek: uyumlu olmayan tüm sanal ağ kaynaklarının ayrıntılarını alma.

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Örnek: belirli bir tarihten sonra gerçekleşen, uyumlu olmayan sanal ağ kaynaklarıyla ilgili olayları alma, CSV nesnesine dönüştürme ve bir dosyaya aktarma.

```azurepowershell-interactive
$policyEvents = Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2020-09-19'
$policyEvents | ConvertTo-Csv | Out-File 'C:\temp\policyEvents.csv'
```

`$policyEvents`Nesnenin çıktısı aşağıdaki çıktı gibi görünür:

```output
Timestamp                  : 9/19/2020 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

Azure PowerShell cmdlet 'ini kullanarak belirli bir kullanıcıyı almak için, **prenıd** alanı kullanılabilir `Get-AzADUser` . **{Prenaloıd}** değerini, önceki örnekte aldığınız Yanıtla değiştirin.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Azure İzleyici günlükleri

Aboneliğinize bağlı Etkinlik Günlüğü Analizi çözümden sahip bir [Log Analytics çalışma alanınız](../../../azure-monitor/logs/log-query-overview.md) varsa `AzureActivity` , basit kusto sorguları ve tablosunu kullanarak yeni ve güncelleştirilmiş kaynakların değerlendirmesinden de uyumsuzluk sonuçlarını görüntüleyebilirsiniz [](../../../azure-monitor/essentials/activity-log.md) `AzureActivity` . Azure Izleyici günlüklerindeki Ayrıntılar sayesinde, uyarılar uyumsuzluk izlemek üzere yapılandırılabilir.

:::image type="content" source="../media/getting-compliance-data/compliance-loganalytics.png" alt-text="AzureActivity tablosundaki Azure Ilke eylemlerini gösteren Azure Izleyici günlüklerinin ekran görüntüsü." border="false":::

## <a name="next-steps"></a>Sonraki adımlar

- [Azure ilke örneklerindeki](../samples/index.md)örnekleri gözden geçirin.
- [Azure İlkesi tanımı yapısını](../concepts/definition-structure.md) gözden geçirin.
- [İlkenin etkilerini anlama](../concepts/effects.md) konusunu gözden geçirin.
- [Program aracılığıyla ilkelerin nasıl oluşturulduğunu](programmatically-create.md)anlayın.
- [Uyumlu olmayan kaynakları nasıl düzelteceğinizi](remediate-resources.md)öğrenin.
- [Kaynakları Azure Yönetim gruplarıyla düzenleme](../../management-groups/overview.md)ile yönetim grubunun ne olduğunu inceleyin.
