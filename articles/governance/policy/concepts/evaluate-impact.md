---
title: Yeni bir Azure ilkesinin etkisini değerlendirin
description: Azure ortamınıza yeni bir ilke oluştururken izlenecek işlemi anlayın.
ms.date: 09/23/2019
ms.topic: conceptual
ms.openlocfilehash: 6c1a3ea764aedec7de5eab1f564176eaafead70b
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73959840"
---
# <a name="evaluate-the-impact-of-a-new-azure-policy"></a>Yeni bir Azure ilkesinin etkisini değerlendirin

Azure Ilkesi, Azure kaynaklarınızı iş standartlarına göre yönetmek ve uyumluluk gereksinimlerini karşılamak için güçlü bir araçtır. Kişiler, süreçler veya işlem hatları kaynakları oluştururken veya güncelleştirdiklerinde, Azure Ilkesi isteği inceler. İlke tanımı efekti [append](./effects.md#deny) veya [Deployifnotexists](./effects.md#deployifnotexists)olduğunda, ilke isteği değiştirir veya buna ekler. İlke tanımı efekti [Denetim](./effects.md#audit) veya [Auditınotexists](./effects.md#auditifnotexists)olduğunda, ilke bir etkinlik günlüğü girişinin oluşturulmasına neden olur. İlke tanımı etkin [olduğunda, ilke, isteğin](./effects.md#deny)oluşturulmasını veya değişiklik işlemini engeller.

Bu sonuçlar, ilkenin doğru şekilde tanımlandığını bildiğiniz durumlarda tam olarak istenen şekilde yapılır. Bununla birlikte, yeni bir ilkenin, çalışmayı değiştirmesine veya engellemeye izin vermeden önce beklendiği gibi çalıştığını doğrulamak önemlidir. Doğrulama yalnızca amaçlanan kaynakların uyumsuz olarak belirlendiğinden ve sonuçlarda yanlış bir şekilde ( _yanlış pozitif_olarak bilinirdi) emin olunması gerekir.

Yeni bir ilke tanımını doğrulamak için önerilen yaklaşım aşağıdaki adımları takip eden bir yaklaşımdır:

- İlkenize sıkı bir şekilde tanımlama
- Mevcut kaynaklarınızı denetleyin
- Yeni veya güncelleştirilmiş kaynak isteklerini denetleme
- İlkenizi kaynaklara dağıtma
- Sürekli izleme

## <a name="tightly-define-your-policy"></a>İlkenize sıkı bir şekilde tanımlama

İş ilkesinin bir ilke tanımı olarak nasıl uygulandığını ve diğer Azure hizmetleriyle Azure kaynakları arasındaki ilişkiyi anlamak önemlidir. Bu adım, [Gereksinimleri tanımlayarak](../tutorials/create-custom-policy-definition.md#identify-requirements) ve [kaynak özellikleri belirlenirken](../tutorials/create-custom-policy-definition.md#determine-resource-properties)gerçekleştirilir.
Ancak, iş ilkenizin dar tanımıyla daha fazla bilgi almak da önemlidir. İlke durumlarınızın "tüm sanal makineler..." olması gerekir mi? HDInsight veya AKS gibi VM 'Leri kullanan diğer Azure hizmetleriyle ilgili ne olacak? Bir ilke tanımlarken, bu ilkenin diğer hizmetler tarafından kullanılan kaynakları nasıl etkilediğini dikkate almalısınız.

Bu nedenle, ilke tanımlarınız sıkı bir şekilde tanımlanmalıdır ve kaynaklara ve mümkün olduğunca uyumluluk için değerlendirmeniz gereken özelliklere odaklanmalıdır.

## <a name="audit-existing-resources"></a>Mevcut kaynakları denetleme

Yeni veya güncelleştirilmiş kaynakları yeni ilke tanımınızda yönetmeyi aramadan önce, bir test kaynak grubu gibi var olan kaynakların sınırlı bir alt kümesini nasıl değerlendirdiği hakkında daha iyi bir seçenektir. Tetiklemenin veya etkinlik günlüğü girişlerinin oluşturulmasını engellemek için ilke atamasındaki [zorlama modunu](./assignment-structure.md#enforcement-mode)
_devre dışı_ (donotenzorlamalı) kullanın. [](./effects.md)

Bu adım, iş akışını etkilemeden mevcut kaynaklardaki yeni ilkenin uyumluluk sonuçlarını değerlendirmek için bir şans sağlar. Uyumlu olmayan bir kaynağın uyumsuz (_yanlış pozitif_) olarak işaretlenip işaretlenmediğini ve uyumlu olmadığını düşündüğünüz tüm kaynakların doğru şekilde işaretlendiğinden emin olun.
Kaynakların ilk alt kümesi beklendiği gibi doğrulandıktan sonra, tüm mevcut kaynaklarla değerlendirmeyi yavaş genişletin.

Mevcut kaynakları bu şekilde değerlendirmek, yeni ilkenin tam uygulanmasıyla uyumlu olmayan kaynakları düzeltmeye yönelik bir fırsat de sağlar. Bu temizleme el ile veya ilke tanımı efekti _Deployifnotexists_ise bir [Düzeltme görevi](../how-to/remediate-resources.md) aracılığıyla yapılabilir.

## <a name="audit-new-or-updated-resources"></a>Yeni veya güncelleştirilmiş kaynakları denetleme

Yeni ilke tanımınızın, mevcut kaynaklar üzerinde doğru şekilde raporlanmasını doğrulandıktan sonra, kaynakların oluşturulma veya güncelleştirilme zamanı sırasında ilkenin etkisini göz atalım. İlke tanımı efekt parametrelemeyi destekliyorsa, [Denetim](./effects.md#audit)' i kullanın. Bu yapılandırma, yeni ilke tanımının mevcut iş veya istekleri etkilemeden uyumsuz bir kaynak için Azure etkinlik günlüğünde bir girişi tetikleyip tetiklenmediğini görmek için kaynakların oluşturulmasını ve güncelleştirilmesini izlemenizi sağlar.

_Denetim_ efektinin beklendiğinde doğru tetiklendiğini görmek için, her iki güncelleştirme de ilke tanımınızda eşleşen yeni kaynaklar oluşturmanız önerilir. _Denetim_ efektini tetikleyen yeni ilke tanımından etkilenmemesi gereken kaynak istekleri için bir gevdekte olun.
Bu etkilenen kaynaklar, bir _Hatalı pozitif_ sonuç örneğidir ve tam uygulamadan önce ilke tanımında düzeltilmelidir.

Bu testin bu aşamasında ilke tanımı değiştirilirse, var olan kaynakların denetimi ile birlikte doğrulama işleminin başlaması önerilir. Yeni veya güncelleştirilmiş kaynaklardaki _Hatalı pozitif_ bir değer için ilke tanımında yapılan değişikliğin, var olan kaynakları üzerinde de etkisi olabilir.

## <a name="deploy-your-policy-to-resources"></a>İlkenizi kaynaklara dağıtma

Yeni ilke tanımınızın doğrulanmasını hem var olan kaynaklarla hem de yeni veya güncelleştirilmiş kaynak istekleriyle tamamladıktan sonra, ilkeyi uygulama işlemine başlarsınız. Yeni ilke tanımı için, ilk olarak kaynak grubu gibi tüm kaynakların bir alt kümesine, ilke atamasını oluşturmanız önerilir. İlk dağıtımı doğruladıktan sonra, ilke kapsamını abonelikler ve yönetim grupları gibi daha geniş ve daha geniş düzeyler olarak genişletin. Bu genişleme, atama kaldırılarak ve yeni ilke tanımınızda kapsanacak kaynakların tam kapsamına atanana kadar hedef kapsamlardan yeni bir tane oluşturularak elde edilir.

Dağıtım sırasında, yeni ilke tanımınızdan muaf tutulması gereken kaynaklar bulunursa, bunları aşağıdaki yollarla ele alır:

- İstenmeyen etkileri azaltmak için ilke tanımını daha açık olacak şekilde güncelleştirin
- İlke atamasının kapsamını değiştirme (yeni bir atamayı kaldırarak ve oluşturarak)
- Kaynak grubunu ilke atamasının dışlama listesine ekleyin

Kapsamda herhangi bir değişiklik olmadığından emin olmak için kapsamdaki tüm değişiklikler (düzey veya Dışlamalar) tam olarak doğrulanıp, güvenlik ve uyumluluk kuruluşlarıyla birlikte verilmelidir.

## <a name="monitor-your-policy-and-compliance"></a>İlkenizi ve uyumluluğunu izleyin

İlke tanımınızı uygulamak ve atamak son adım değildir. Kaynakların [Uyumluluk](../how-to/get-compliance-data.md) düzeyini yeni ilke tanımınıza sürekli olarak izleyin ve uyumlu olmayan cihazlar tanımlandığında Ilgili [Azure izleyici uyarılarını ve bildirimlerini](../../../azure-monitor/platform/alerts-overview.md) ayarlayın. İlke tanımının iş ilkesi ve uyumluluk ihtiyaçlarını karşıladığını doğrulamak için, ilke tanımını ve ilgili atamaları zamanlanan bir şekilde değerlendirmek de önerilir. Artık gerekmiyorsa ilkelerin kaldırılması gerekir. Ayrıca, temel alınan Azure kaynakları geliştikçe ve yeni özellikler ve yetenekler eklerken ilkelerin zaman zaman güncel olarak güncelleştirilmesi gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [İlke tanımı yapısı](./definition-structure.md)hakkında bilgi edinin.
- [İlke atama yapısı](./assignment-structure.md)hakkında bilgi edinin.
- [Program aracılığıyla ilkelerin nasıl oluşturulduğunu](../how-to/programmatically-create.md)anlayın.
- [Uyumluluk verilerini nasıl alabileceğinizi](../how-to/getting-compliance-data.md)öğrenin.
- [Uyumlu olmayan kaynakları nasıl düzelteceğinizi](../how-to/remediate-resources.md)öğrenin.
- [Kaynakları Azure Yönetim gruplarıyla düzenleme](../../management-groups/overview.md)ile yönetim grubunun ne olduğunu inceleyin.