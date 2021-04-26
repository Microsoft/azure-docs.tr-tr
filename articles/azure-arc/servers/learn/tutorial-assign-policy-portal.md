---
title: Öğretici-Azure portal yeni ilke ataması
description: Bu öğreticide, uyumlu olmayan kaynakları belirlemek üzere bir Azure Ilkesi ataması oluşturmak için Azure portal kullanırsınız.
ms.topic: tutorial
ms.date: 04/21/2021
ms.openlocfilehash: 20cb8125a157a3ce97c88278c99f90936b449b65
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831663"
---
# <a name="tutorial-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Öğretici: uyumlu olmayan kaynakları belirlemek için bir ilke ataması oluşturma

Azure’da uyumluluğu anlamanın ilk adımı, kaynaklarınızın durumunu belirlemektir. Azure Ilkesi, Konuk yapılandırma ilkeleriyle yay etkin sunucunuzun durumunu denetlemeyi destekler. Konuk yapılandırma ilkeleri yapılandırmaları uygulamaz, yalnızca makinenin içindeki ayarları denetlarlar. Bu öğretici, bir ilke oluşturma ve atama sürecinde size kılavuzluk eden, Arc etkin sunucularınızın Log Analytics aracısının yüklü olmadığı tanımlamayı tanımlar.

Bu işlemin sonunda, Windows veya Linux için Log Analytics aracısına sahip olmayan makineleri başarıyla tanımlayacaksınız. Bu sanal makineler, ilke ataması ile _uyumsuzdur_.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="create-a-policy-assignment"></a>İlke ataması oluşturma

Bu öğreticide, bir ilke ataması oluşturur ve _\[ önizlemeyi atayabilirsiniz]: Log Analytics Aracısı, Linux Azure yay makineleri ilke tanımınızda yüklü olmalıdır_ .

1. Azure portalında **Tüm hizmetler**’e tıkladıktan sonra **İlke**'yi arayıp seçerek Azure İlkesi hizmetini başlatın.

   :::image type="content" source="./media/tutorial-assign-policy-portal/search-policy.png" alt-text="Tüm hizmetlerde Ilke ara" border="false":::

1. Azure İlkesi sayfasının sol tarafından **Atamalar**'ı seçin. Atama, belirli bir kapsamda gerçekleşmesi için atanmış olan bir ilkedir.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assignment.png" alt-text="Ilkeye genel bakış sayfasından atamaları seçin sayfası" border="false":::

1. **İlke - Atamalar** sayfasının üst kısmından **İlke Ata**'yı seçin.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assign-policy.png" alt-text="Atamalar sayfasından bir ilke tanımı atama" border="false":::

1. **İlke Ata** sayfasında üç noktaya tıklayıp bir yönetim grubu veya abonelik belirleyerek **Kapsam**’ı seçin. İsterseniz bir kaynak grubu seçin. Kapsam, ilke atamasının hangi kaynaklarda veya kaynak gruplarında uygulanacağını belirler. Ardından **Kapsam** sayfasının alt kısmından **Seç**’e tıklayın.

   Bu örnek, **Parnell Aerospace** aboneliğini kullanır. Sizin aboneliğiniz farklı olacaktır.

1. Kaynaklar **Kapsam**’a göre dışlanabilir. **Dışlamalar**, **Kapsam**’dan bir düzey aşağıda başlatılır. **Dışlamalar** isteğe bağlıdır, bu yüzden şimdilik boş bırakın.

1. **İlke tanımı** üç nokta öğesini seçerek kullanılabilen tanımların listesini açın. Azure İlkesi, kullanabileceğiniz yerleşik ilke tanımlarıyla birlikte gelir. Aşağıdakiler gibi çok sayıda ilke mevcuttur:

   - Etiketi ve değerini zorla
   - Etiketi ve değerini uygula
   - Eksikse, kaynak grubundan bir etiketi devralma

   Kullanılabilir yerleşik ilkelerin kısmi listesi için bkz. [Azure ilke örnekleri](../../../governance/policy/samples/index.md).

1. Önizlemeyi bulmak için ilke tanımları listesinde arama yapın]: Windows tabanlı bir makinede yay etkin sunucular aracısını etkinleştirdiyseniz, _\[ Log Analytics Aracısı Windows Azure Arc makineleri tanımınızda yüklü olmalıdır_ . Linux tabanlı bir makine için, karşılık gelen önizlemeyi bulun _\[ ]: Linux Azure yay makineleri ilke tanımınızda Log Analytics Aracısı yüklü_ olmalıdır. Bu ilkeye tıklayın ve **Seç**'e tıklayın.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-available-definition.png" alt-text="Doğru ilke tanımını bulma" border="false":::

1. **Atama adı** otomatik olarak seçtiğiniz ilke adıyla doldurulur, ancak bunu değiştirebilirsiniz. Bu örnekte, önizlemeyi bırakın _\[ ]: Log Analytics aracı Windows Azure yay makinelerinize_ veya önizlemeye yüklenmelidir]: Log Analytics aracısının, seçtiğiniz seçeneğe bağlı olarak _\[ Linux Azure yay makinelerinizde yüklü olması gerekir_ . İsteğe bağlı bir **Açıklama** da ekleyebilirsiniz. Açıklama, bu ilke atamasıyla ilgili ayrıntıları sağlar.
   **Atayan**, oturum açmış kişiye göre otomatik olarak doldurulur. Bu alan isteğe bağlı olduğu için özel değerler girilebilir.

1. **Yönetilen Kimlik Oluşturun** seçeneğini işaretsiz bırakın. İlke veya girişim, [Deployifnotexists](../../../governance/policy/concepts/effects.md#deployifnotexists) efektli bir ilke içerdiğinde bu kutu _denetlenmelidir_ . Bu hızlı başlangıç için kullanılan ilke olmadığından boş bırakın. Daha fazla bilgi için [yönetilen kimlikler](../../../active-directory/managed-identities-azure-resources/overview.md) ve [düzeltme güvenliğinin işleyişi](../../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works) bölümlerine bakın.

1. **Ata**'ya tıklayın.

Artık ortamınızın uyumluluk durumunu anlamak için uyumlu olmayan kaynakları belirlemek için hazırsınız demektir.

## <a name="identify-non-compliant-resources"></a>Uyumlu olmayan kaynakları belirleme

Sayfanın sol tarafındaki **Uyumluluk** ' i seçin. Daha sonra **\[ önizlemeyi]: Log Analytics aracısının Windows Azure Arc makinelerinize veya önizlemeye yüklenmesi gerekir** **\[ ]: Log Analytics aracısının oluşturduğunuz Linux Azure yay makineleri** ilke atamasında yüklü olması gerekir.

:::image type="content" source="./media/tutorial-assign-policy-portal/policy-compliance.png" alt-text="Ilke uyumluluk sayfasındaki uyumluluk ayrıntıları" border="false":::

Bu yeni atamayla uyumlu olmayan mevcut kaynaklar varsa **uyumlu olmayan kaynaklar** altında görünürler.

Bir koşul mevcut kaynaklarınıza göre değerlendirilip true sonucunu verdiğinde, bu kaynaklar ilkeyle uyumlu değil olarak işaretlenir. Aşağıdaki tabloda, elde edilen uyumluluk durumu için farklı ilke etkilerinin koşul değerlendirmesi ile nasıl çalıştığı gösterilmektedir. Azure portal değerlendirme mantığını görmeseniz de, uyumluluk durumu sonuçları gösterilir. Uyumluluk durumu sonucu uyumlu veya uyumsuz şeklindedir.

| **Kaynak Durumu** | **Etki** | **İlke Değerlendirmesi** | **Uyumluluk durumu** |
| --- | --- | --- | --- |
| Var | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | Doğru | Uyumlu Değil |
| Var | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | Yanlış | Uyumlu |
| Yeni | Audit, AuditIfNotExist\* | Doğru | Uyumlu Değil |
| Yeni | Audit, AuditIfNotExist\* | Yanlış | Uyumlu |

\* Append, DeployIfNotExist ve AuditIfNotExist etkileri IF deyiminin TRUE olmasını gerektirir.
Etkiler ayrıca varlık koşulunun uyumlu olmaması için FALSE olmasını gerektirir. TRUE olduğunda, IF koşulu ilgili kaynaklar için varlık koşulunun değerlendirilmesini tetikler.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Oluşturulan atamayı kaldırmak için şu adımları izleyin:

1. Azure Ilkesi sayfasının sol tarafındaki **Uyumluluk** (veya **atamalar**) ' ı seçin ve **\[ önizlemeyi bulun]: Log Analytics aracısının Windows Azure Arc makinelerinize veya önizlemeye yüklenmesi gerekir** **\[ ]: Log Analytics aracısının, oluşturduğunuz Linux Azure yay makineleri** ilke atamasında yüklü olması gerekir.

1. İlke atamasını sağ tıklatın ve **atamayı Sil**' i seçin.

   :::image type="content" source="./media/tutorial-assign-policy-portal/delete-assignment.png" alt-text="Uyumluluk sayfasından bir atamayı silme" border="false":::

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir kapsama ilke tanımı atadınız ve uyumluluk raporunu değerlendirdiniz. İlke tanımı, kapsamdaki tüm kaynakların uyumlu olduğunu doğrular ve hangilerinin olduğunu tanımlar. Artık, Azure Arc etkin sunucu makinenizi, [VM öngörülerini](../../../azure-monitor/vm/vminsights-overview.md)etkinleştirerek izlemeye hazırsınız.

Performans, çalışan işlem ve bunların bağımlılıklarındaki bağımlılıklarını izleme ve görüntüleme hakkında bilgi edinmek için öğreticiye geçin:

> [!div class="nextstepaction"]
> [VM öngörülerini etkinleştir](tutorial-enable-vm-insights.md)