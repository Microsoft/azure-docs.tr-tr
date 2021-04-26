---
title: SYNAPSE çalışma alanı erişim denetimine genel bakış
description: Bu makalede, bir Synapse çalışma alanına ve içerdiği kaynak ve kod yapılarına erişimi denetlemek için kullanılan mekanizmalar açıklanmaktadır.
services: synapse-analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 12/03/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 20614b1c397bdf24e807d48d3de33f0033da14bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100105122"
---
# <a name="synapse-access-control"></a>SYNAPSE Access Control 

Bu makalede SYNAPSE işlem kaynaklarına ve verilerine erişimi denetlemek için kullanılabilen mekanizmalarla genel bir bakış sunulmaktadır.  

## <a name="overview"></a>Genel Bakış

SYNAPSE, aşağıdakileri tümleştiren kapsamlı ve ayrıntılı bir erişim denetimi sistemi sağlar: 
- Kaynak yönetimi için **Azure rolleri** ve depolama alanındaki verilere erişim, 
- **SYNAPSE** , kod ve yürütmeye canlı erişimin yönetilmesi için roller, 
- SQL havuzlarındaki verilere veri düzlemi erişimi için **SQL rolleri** ve 
- Sürekli tümleştirme ve dağıtım desteği dahil olmak üzere kaynak kodu denetimi için **Git izinleri** .  

SYNAPSE rolleri, farklı kapsamlara uygulanabilen izinler kümesi sağlar. Bu ayrıntı düzeyi, kaynakları ve verileri hesaplamak için Yöneticiler, geliştiriciler, güvenlik personeli ve operatörlere uygun erişim vermeyi kolaylaştırır.

Erişim denetimi, kişilerin iş rolleriyle hizalanmış güvenlik grupları kullanılarak basitleştirilebilir. Erişimi yönetmek için yalnızca uygun güvenlik gruplarından Kullanıcı ekleyip kaldırmanız gerekir.

## <a name="access-control-elements"></a>Erişim denetimi öğeleri

### <a name="creating-and-managing-synapse-compute-resources"></a>SYNAPSE işlem kaynakları oluşturma ve yönetme

Azure rolleri şu yönetimi denetlemek için kullanılır: 
- Adanmış SQL havuzları, 
- Apache Spark havuzları ve 
- Tümleştirme çalışma zamanları. 

Bu kaynakları *oluşturmak* için kaynak grubunda bir Azure sahibi veya katkıda bulunan olması gerekir. Bunları bir kez oluşturduktan sonra *yönetmek* için kaynak grubunda veya tek tek kaynaklarda bir Azure sahibi veya katkıda bulunan olması gerekir. 

### <a name="developing-and-executing-code-in-synapse"></a>SYNAPSE 'de kod geliştirme ve yürütme 

SYNAPSE iki geliştirme modelini destekler.

- **SYNAPSE canlı geliştirme**. SYNAPSE Studio 'da kod geliştirip hata ayıklayın ve ardından kaydetmek ve yürütmek üzere **yayımlayın** .  SYNAPSE hizmeti, kod düzenlemesi ve yürütülmesi için Truth kaynağıdır.  SYNAPSE Studio 'Yu kapattığınızda yayımdan kaldırılan tüm işler kaybolur.  
- **Git etkin geliştirme**. SYNAPSE Studio 'da kod geliştirin ve hata ayıklayın ve değişiklikleri git deposunun çalışma dalına **işleyin** . Bir veya daha fazla daldan iş, hizmette **yayımladığınızda** bir işbirliği dalında tümleşiktir. Git deposu, kod düzenlemesi için Truth kaynağıdır, ancak hizmet, yürütme için Truth kaynağıdır. Değişiklikler SYNAPSE Studio 'Yu kapatmadan önce git deposuna kaydedilmiş veya hizmette yayımlanacak olmalıdır. Git ile SYNAPSE Analytics kullanma hakkında [daha fazla bilgi edinin](../cicd/continuous-integration-deployment.md) .

Her iki geliştirme modelinde da, SYNAPSE Studio 'ya erişimi olan tüm kullanıcılar kod yapıtları oluşturabilir. Ancak, hizmet için yapıtları yayımlamak, yayımlanan yapıtları okumak, git 'e yapılan değişiklikleri kaydetmek ve kimlik bilgileriyle korunan bağlantılı verilere erişmek için ek izinlere sahip olmanız gerekir.

### <a name="synapse-roles"></a>SYNAPSE rolleri

SYNAPSE rolleri, size izin veren SYNAPSE hizmetine erişimi denetlemek için kullanılır: 
- Yayınlanan kod yapılarını listeleme, 
- Kod yapılarını, bağlı hizmetleri ve kimlik bilgisi tanımlarını yayımlayın,
- SYNAPSE işlem kaynaklarını kullanan kod veya işlem hatlarını yürütün,
- Kimlik bilgileriyle korunan bağlantılı verilere erişen kod veya işlem hatlarını yürütün,
- Yayınlanan kod yapıtları ile ilişkili çıktıları görüntüleyin,
- İşlem kaynağı durumunu izleyin ve çalışma zamanı günlüklerini görüntüleyin.

SYNAPSE rolleri, belirli SYNAPSE kaynaklarına verilen izinleri sınırlandırmak için çalışma alanı kapsamında veya daha hassas kapsamlar atanabilir.

### <a name="git-permissions"></a>Git izinleri

Git modunda git etkin geliştirme kullanırken, git izinleriniz, bağlantılı hizmet ve kimlik bilgisi tanımları dahil olmak üzere kod yapıtlarına yapılan değişiklikleri okuyup işlemeyeceğinizi kontrol edebilir.   
   
### <a name="accessing-data-in-sql"></a>SQL 'de verilere erişme

Adanmış ve sunucusuz SQL havuzlarıyla çalışırken, veri düzlemi erişimi SQL izinleri kullanılarak denetlenir. 

Çalışma alanının Oluşturucusu, çalışma alanında Active Directory yöneticisi olarak atanır. Oluşturulduktan sonra, bu rol farklı bir kullanıcıya veya Azure portal bir güvenlik grubuna atanabilir.

**SUNUCUSUZ SQL havuzları**: SYNAPSE yöneticilerine `db_owner` `DBO` , ' YERLEŞIK ' sunucusuz SQL havuzunda izin verilir (). Diğer kullanıcılara sunucusuz SQL havuzlarının erişimini sağlamak için, SYNAPSE yöneticilerinin her bir sunucusuz havuzda SQL betikleri çalıştırması gerekir.  

**ADANMıŞ SQL havuzları**: Active Directory yönetici izni, çalışma alanının Oluşturucusu ve MSI çalışma alanı için verilir.  Adanmış SQL havuzlarına erişme izni Aksi takdirde otomatik olarak verilmez. Diğer kullanıcılara veya gruplara adanmış SQL havuzlarına erişim sağlamak için, Active Directory yöneticisinin her adanmış SQL havuzunda SQL betikleri çalıştırması gerekir.

SQL havuzlarında SQL izinleri vermek için SQL komut dosyaları örnekleri için [Synapse Access Control ayarlama](./how-to-set-up-access-control.md) konusuna bakın.  

 ### <a name="accessing-system-managed-data-in-storage"></a>Depolamadaki sistem tarafından yönetilen verilere erişme

Sunucusuz SQL havuzları ve Apache Spark tabloları, verilerini çalışma alanıyla ilişkili bir ADLS 2. kapsayıcısında depolar. Kullanıcı tarafından yüklenen Apache Spark kitaplıkları aynı depolama hesabında da yönetilir. Bu kullanım örneklerini etkinleştirmek için, kullanıcılara ve bu çalışma alanı MSI **Depolama Blobu veri katılımcısı** , depolama kapsayıcısı ADLS 2. bu çalışma alanına erişimine sahip olmalıdır.  

## <a name="using-security-groups-as-a-best-practice"></a>En iyi uygulama olarak güvenlik gruplarını kullanma

Erişim denetimini yönetmeyi basitleştirmek için, kişilere ve gruplara roller atamak üzere güvenlik gruplarını kullanabilirsiniz. Güvenlik grupları, kuruluşunuzda SYNAPSE kaynaklarına veya yapılarına erişmesi gereken kişiler veya iş işlevlerini yansıtacak şekilde oluşturulabilir.  Bu kişi tabanlı güvenlik gruplarına bir veya daha fazla Azure rolü, SYNAPSE rolü, SQL izinleri veya git izinleri atanabilir. İyi seçilmiş güvenlik grupları sayesinde, bir kullanıcıya uygun güvenlik grubuna ekleyerek gerekli izinleri atamak kolaydır. 

>[!Note]
>Erişimi yönetmek için güvenlik grupları kullanılıyorsa, değişiklikler yürürlüğe girmeden önce Azure Active Directory tarafından tanıtılan ek gecikme vardır. 

## <a name="access-control-enforcement-in-synapse-studio"></a>SYNAPSE Studio 'da erişim denetimi zorlaması

SYNAPSE Studio, izinleriniz ve geçerli moda göre farklı davranır:
- **SYNAPSE canlı modu:** SYNAPSE Studio, yayımlanan içeriği görmenizi, içerik yayımlamayı veya gerekli izniniz yoksa başka eylemler yapılmasını engeller.  Bazı durumlarda, kullanamadığı veya kaydedebir kod yapıtları oluşturmanız engellenir. 
- **Git modu:** Geçerli dalda değişiklikleri yürütmenizi sağlayan git izinleriniz varsa, canlı hizmette değişiklik yayımlama izniniz olmasa bile işleme eylemine izin verilir.  

Bazı durumlarda, yayımlama veya işlemeye izin vermeden bile kod yapıtları oluşturma izni verilir. Bu, kodu yürütmeniz (gerekli yürütme izinleriyle birlikte) sağlar. Ortak görevler için gereken roller hakkında [daha fazla bilgi edinin](./synapse-workspace-understand-what-role-you-need.md) . 

SYNAPSE Studio 'da bir özellik devre dışıysa, bir araç ipucu gerekli izni gösterir. Eksik izni sağlamak için hangi rolün gerekli olduğunu aramak için [SYNAPSE RBAC rolleri kılavuzunu](./synapse-workspace-synapse-rbac-roles.md#synapse-rbac-actions-and-the-roles-that-permit-them) kullanın.


## <a name="next-steps"></a>Sonraki adımlar

- [SYNAPSE RBAC](./synapse-workspace-synapse-rbac.md) hakkında daha fazla bilgi edinin
- [SYNAPSE RBAC rolleri](./synapse-workspace-synapse-rbac-roles.md) hakkında daha fazla bilgi edinin
- Güvenlik gruplarını kullanarak bir Synapse çalışma alanı için [erişim denetimi ayarlamayı](./how-to-set-up-access-control.md) öğrenin.
- [SYNAPSE RBAC rol atamalarını incelemeyi](./how-to-review-synapse-rbac-role-assignments.md) öğrenin
- [SYNAPSE RBAC rol atamalarını yönetmeyi](./how-to-manage-synapse-rbac-role-assignments.md) öğrenin
