---
title: SQL Information Protection 'ı özelleştirme-Azure Güvenlik Merkezi
description: Azure Güvenlik Merkezi 'nde bilgi koruma ilkelerini özelleştirmeyi öğrenin.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/29/2019
ms.author: memildin
ms.openlocfilehash: d37333c0ca3f8acab7a35c23bbab67beef056b72
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73664385"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>Azure Güvenlik Merkezi 'nde SQL Information Protection ilkesini özelleştirme (Önizleme)
 
Azure Güvenlik Merkezi 'nde tüm Azure kiracınız için bir SQL Information Protection ilkesi tanımlayabilir ve özelleştirebilirsiniz.

Information Protection, Azure veri kaynaklarınızda hassas verileri keşfetme, sınıflandırma, etiketleme ve korumanın gelişmiş bir güvenlik özelliğidir. En hassas verilerinizi bulma ve sınıflandırma (iş, finans, Sağlık Hizmetleri, kişisel veriler vb.), kurumsal bilgi koruma ortamınızda bir özetleme rolü yürütebilir. Bu, için altyapı işlevi görebilir:
- Veri gizliliği standartları ve yasal uyumluluk gereksinimlerini karşılamanıza yardımcı olma
- Hassas verilere anormal erişimle izleme (denetim) ve uyarı verme gibi güvenlik senaryoları
- Son derece hassas veriler içeren veri depolarının güvenliğine erişimi denetleme ve güvenliği artırma
 
[Sql Information Protection](../sql-database/sql-database-data-discovery-and-classification.md) Şu anda Azure SQL veritabanı IÇIN desteklenen SQL veri depolarınız için Bu paradigma uygular. SQL Information Protection, potansiyel olarak hassas verileri otomatik olarak bulur ve sınıflandırır, hassas verileri sınıflandırma öznitelikleriyle kalıcı olarak etiketlemek için etiketleme mekanizması sağlar ve şu şekilde gösterildiği ayrıntılı bir pano sağlar veritabanının sınıflandırma durumu. Ayrıca, hassas verileri çıkaran sorguların açıkça denetlenmesi ve verilerin korunabilmesi için SQL sorgularının sonuç kümesi duyarlılığını hesaplar. SQL Information Protection hakkında daha fazla bilgi için bkz. [Azure SQL veritabanı veri bulma ve sınıflandırma](../sql-database/sql-database-data-discovery-and-classification.md).
 
Sınıflandırma mekanizması, sınıflandırma- **Etiketler** ve **bilgi türlerini**oluşturan iki birincil yapıları temel alır.
- **Etiketler** : sütunda depolanan verilerin duyarlılık düzeyini tanımlamak için kullanılan ana sınıflandırma öznitelikleri. 
- **Bilgi türleri** : sütunda depolanan verilerin türüne ek ayrıntı düzeyi sağlar.
 
Information Protection, varsayılan olarak kullanılan yerleşik Etiketler ve bilgi türleri kümesiyle birlikte gelir. Bu etiketleri ve türleri özelleştirmek için, güvenlik merkezi 'nde bilgi koruma ilkesini özelleştirebilirsiniz.
 
## <a name="customize-the-information-protection-policy"></a>Bilgi koruma ilkesini özelleştirme
Azure kiracınız için Information Protection ilkesini özelleştirmek üzere [kiracının kök yönetim grubunda yönetici ayrıcalıklarına](security-center-management-groups.md)sahip olmanız gerekir. 
 
1. Güvenlik Merkezi ana menüsünde, **kaynak güvenliği HYGIENE** ' ın altında **veri & depolama alanına** gidin ve **SQL Information Protection** düğmesine tıklayın.

   ![Bilgi koruma ilkesini yapılandırma](./media/security-center-info-protection-policy/security-policy.png) 
 
2. **SQL Information Protection** sayfasında, geçerli etiket kümesini görüntüleyebilirsiniz. Bunlar, verilerinizin duyarlılık düzeyini kategorilere ayırmak için kullanılan ana sınıflandırma öznitelikleridir. Buradan, kiracı için **bilgi koruma etiketlerini** ve **bilgi türlerini** yapılandırabilirsiniz. 
 
### <a name="customizing-labels"></a>Etiketleri özelleştirme
 
1. Varolan herhangi bir etiketi düzenleyebilir veya silebilir veya yeni bir etiket ekleyebilirsiniz. Varolan bir etiketi düzenlemek için, bu etiketi seçin ve sonra üstteki ya da sağdaki bağlam menüsünde **Yapılandır**' a tıklayın. Yeni bir etiket eklemek için üstteki menü çubuğunda veya Etiketler tablosunun en altında **etiket oluştur** ' a tıklayın.
2. **Duyarlılık etiketini Yapılandır** ekranında, etiket adı ve açıklamasını oluşturabilir veya değiştirebilirsiniz. Ayrıca, **etkin** anahtarı değiştirerek veya devre dışı bırakmak için etiketin etkin veya devre dışı olduğunu da ayarlayabilirsiniz. Son olarak, etiketle ilişkili bilgi türlerini ekleyebilir veya kaldırabilirsiniz. Bu bilgi türüyle eşleşen tüm veriler, sınıflandırma önerilerine ilişkili duyarlılık etiketini otomatik olarak dahil eder.
3. **Tamam** düğmesine tıklayın.
 
   ![Duyarlılık etiketini Yapılandır](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. Etiketler, artan duyarlılık sırasına göre listelenmiştir. Etiketler arasındaki derecelendirmeyi değiştirmek için etiketleri tablo içinde yeniden sıralamak üzere sürükleyin veya sıralamayı değiştirmek için **yukarı** taşı ve **aşağı taşı** düğmelerini kullanın. 
 
    ![Bilgi koruma ilkesini yapılandırma](./media/security-center-info-protection-policy/move-up.png)
 
5. İşiniz bittiğinde ekranın en üstündeki **Kaydet** ' e tıkladığınızdan emin olun.
 
 
## <a name="adding-and-customizing-information-types"></a>Bilgi türlerini ekleme ve özelleştirme
 
1. Bilgi türlerini **Yönet**' i tıklatarak bilgi türlerini yönetebilir ve özelleştirebilirsiniz.
2. Yeni bir **bilgi türü**eklemek için üst menüde **bilgi türü oluştur** ' u seçin. **Bilgi türü**için bir ad, açıklama ve arama deseninin dizelerini yapılandırabilirsiniz. Arama desenli dizeler, isteğe bağlı olarak joker karakterlerle ('% ' karakterini kullanarak) anahtar sözcükleri kullanabilir ve bu da otomatik bulma altyapısının, veritabanınızdaki gizli verileri, sütunların meta verilerine göre belirlemek için kullanır.
 
    ![Bilgi koruma ilkesini yapılandırma](./media/security-center-info-protection-policy/info-types.png)
 
3. Ayrıca, ek arama model dizeleri ekleyerek, var olan dizelerin bazılarını devre dışı bırakarak veya açıklamayı değiştirerek yerleşik **bilgi türlerini** yapılandırabilirsiniz. Yerleşik **bilgi türlerini** silemez veya adlarını düzenleyemezsiniz. 
4. **Bilgi türleri** , artan bulma derecelendirmesi sırasına göre listelenmiştir, yani listede daha yüksek olan türlerin birinciden eşleştirmeye çalışır. Bilgi türleri arasındaki derecelendirmeyi değiştirmek için, türleri tablodaki doğru noktaya sürükleyin veya sıralamayı değiştirmek için **Yukarı taşı** ve **aşağı taşı** düğmelerini kullanın. 
5. İşiniz bittiğinde **Tamam** ' a tıklayın.
6. Bilgi türlerinizi yönetmeyi tamamladıktan sonra, belirli bir etiket için **Yapılandır** ' a tıklayarak ve bilgi türlerini uygun şekilde ekleyerek veya silerek ilgili türleri ilgili etiketlerle ilişkilendirdiğinizden emin olun.
7. Tüm değişikliklerinizi uygulamak için ana **Etiketler** dikey penceresinde **Kaydet** ' e tıkladığınızdan emin olun.
 
Bilgi koruma ilkeniz tam olarak tanımlandıktan ve kaydedildikten sonra, kiracınızdaki tüm Azure SQL veritabanlarında verilerin sınıflandırmasına uygulanır.
 
 
## <a name="next-steps"></a>Sonraki adımlar
 
Bu makalede, Azure Güvenlik Merkezi 'nde bir SQL Information Protection ilkesi tanımlamayı öğrendiniz. SQL veritabanlarındaki hassas verileri sınıflandırmak ve korumak için SQL Information Protection kullanma hakkında daha fazla bilgi edinmek için bkz. [Azure SQL veritabanı veri bulma ve sınıflandırma](../sql-database/sql-database-data-discovery-and-classification.md). 

Azure Güvenlik Merkezi 'nde güvenlik ilkeleri ve veri güvenliği hakkında daha fazla bilgi için aşağıdaki makalelere bakın:
 
- [Azure Güvenlik Merkezi 'nde güvenlik Ilkelerini ayarlama](tutorial-security-policy.md): Azure abonelikleriniz ve kaynak gruplarınız için güvenlik ilkelerini yapılandırma hakkında bilgi edinin
- [Azure Güvenlik Merkezi veri güvenliği](security-center-data-security.md): Güvenlik Merkezi 'nin verileri nasıl yönettiğini ve korumalarını öğrenin