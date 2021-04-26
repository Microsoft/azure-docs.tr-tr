---
title: SYNAPSE rol tabanlı erişim denetimi
description: Azure SYNAPSE Analytics 'te rol tabanlı erişim denetimini açıklayan bir makale
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9e96d6decba679c7a4764a77f1e9720000faf78c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100105156"
---
# <a name="what-is-synapse-role-based-access-control-rbac"></a>SYNAPSE rol tabanlı erişim denetimi (RBAC) nedir?

SYNAPSE RBAC, SYNAPSE çalışma alanları ve bunların içeriği için [Azure RBAC](../../role-based-access-control/overview.md) özelliklerini genişletir. 

Azure RBAC, SYNAPSE çalışma alanını ve SQL havuzlarını, Apache Spark havuzlarını ve tümleştirme çalışma zamanlarını kimin oluştur, güncelleştirebileceğini veya silebileceğini yönetmek için kullanılır.

SYNAPSE RBAC şunları şunları yapabilir:
- Kod yapılarını yayımlayın ve yayınlanmış kod yapıtlarını listeleyin veya erişin, 
- Kodu Apaches Spark havuzlarında ve tümleştirme çalışma zamanları üzerinde yürütün,
- Kimlik bilgileriyle korunan bağlı (veri) hizmetlere erişin 
- İş yürütmeyi izleyin veya iptal edin, iş çıkışını gözden geçirin ve yürütme günlüklerini inceleyin.  

>[!Note]
>Yayınlanan SQL betiklerine erişimi yönetmek için SYNAPSE RBAC kullanıldığında, sunucusuz SQL havuzlarına yalnızca sınırlı erişim denetimi sağlar ve adanmış SQL havuzlarına erişimi denetlemek _için kullanılmaz._  SQL havuzlarının erişimi öncelikle SQL güvenliği kullanılarak denetlenir.

## <a name="what-can-i-do-with-synapse-rbac"></a>SYNAPSE RBAC ile ne yapabilirim?

SYNAPSE RBAC ile yapabilecekleriniz için bazı örnekler şunlardır:
  - Bir kullanıcının canlı hizmette Apache Spark not defterlerine ve işlere yapılan değişiklikleri yayımlamasına izin verin.
  - Bir kullanıcının belirli bir Apache Spark havuzundaki not defterlerini ve Spark işlerini çalıştırmasına ve iptal edebilmesini sağlar.
  - Bir kullanıcının, çalışma alanı sistem kimliği tarafından güvenli hale getirilmiş işlem hatlarını çalıştırabilmeleri ve bağlantılı hizmetlerde kimlik bilgileriyle korunan verilere erişmesi için belirli kimlik bilgilerini kullanmasına izin verin. 
  - Yöneticinin belirli Spark havuzlarında iş yürütmeyi yönetmesine, izlemesine ve iptal edebilmesini sağlar.    

## <a name="how-synapse-rbac-works"></a>SYNAPSE RBAC nasıl çalışacaktır?
Azure RBAC gibi SYNAPSE RBAC, rol atamaları oluşturarak işe yarar. Rol ataması üç öğeden oluşur: güvenlik sorumlusu, rol tanımı ve kapsam.  

### <a name="security-principals"></a>Güvenlik sorumluları

Bir _güvenlik sorumlusu_ , bir Kullanıcı, Grup, hizmet sorumlusu veya yönetilen bir kimliktir.

### <a name="roles"></a>Roller
 
_Rol_ , belirli kaynak türlerinde veya yapıt türlerinde gerçekleştirilebilecek izinler veya eylemler koleksiyonudur.

SYNAPSE, farklı kişilerin gereksinimleriyle eşleşen eylem koleksiyonlarını tanımlayan yerleşik roller sağlar:
- Yöneticiler bir çalışma alanı oluşturmak ve yapılandırmak için tam erişim alabilir 
- Geliştiriciler SQL betikleri, Not defterleri, işlem hatları ve veri akışları oluşturabilir, güncelleştirebilir ve hata ayıklamanıza karşın bu kodu üretim işlem kaynakları/verilerinde yayımlayamaz veya yürütmeyebilir
- İşleçler, kod veya yürütmenin çıktılarına erişim olmadan sistem durumunu, uygulama yürütmeyi ve gözden geçirme günlüklerini izleyebilir ve yönetebilir.
- Güvenlik personeli, kod, işlem kaynağı veya verilere erişim olmadan uç noktaları yönetebilir ve yapılandırabilir.

Yerleşik SYNAPSE rolleri hakkında [daha fazla bilgi edinin](./synapse-workspace-synapse-rbac-roles.md) . 

### <a name="scopes"></a>Kapsamlar

Bir _kapsam_ , erişimin uygulandığı kaynakları veya yapıtları tanımlar.  SYNAPSE hiyerarşik kapsamları destekler.  Daha yüksek düzeyde bir kapsamda verilen izinler, daha düşük düzeydeki nesneler tarafından devralınır.  SYNAPSE RBAC 'de, en üst düzey kapsam bir çalışma alanıdır.  Çalışma alanı kapsamına sahip bir rolü atamak, çalışma alanındaki tüm ilgili nesnelere izin verir.  

Bir çalışma alanı içinde desteklenen geçerli kapsamlar şunlardır: Apache Spark havuz, tümleştirme çalışma zamanı, bağlı hizmet ve kimlik bilgisi. 

Kod yapılarına erişim çalışma alanı kapsamı ile verilir.  Bir çalışma alanındaki yapıların koleksiyonlarına erişim verilmesi, sonraki bir sürümde desteklenecektir.

## <a name="resolving-role-assignments-to-determine-permissions"></a>İzinleri belirlemekte rol atamaları çözümleniyor

Rol ataması, belirtilen kapsamda rol tarafından tanımlanan izinleri verir.

SYNAPSE RBAC, Azure RBAC gibi bir eklenebilir modeldir. Tek bir sorumluya ve farklı kapsamlara birden çok rol atanabilir. Bir güvenlik sorumlusunun izinleri hesaplanırken, sistem sorumlu için atanan tüm rolleri ve doğrudan veya dolaylı olarak sorumlu olan grupları kabul eder.  Ayrıca, uygulanan izinleri belirlemede her atamanın kapsamını da dikkate alır.  

## <a name="enforcing-assigned-permissions"></a>Atanan izinleri zorlama

SYNAPSE Studio 'da, belirli düğmeler veya seçenekler gri olabilir veya gerekli izinlere sahip değilseniz bir eylem denenmeden bir izin hatası döndürülebilir. 

Bir düğme veya seçenek devre dışıysa, düğme veya seçeneğin üzerine gelindiğinde, gerekli izne sahip bir araç ipucu görüntülenir.  Gerekli izni veren bir rol atamak için bir Synapse yöneticisiyle iletişim kurun. Belirli eylemleri sağlayan rolleri [buradan](./synapse-workspace-synapse-rbac-roles.md)görebilirsiniz.

## <a name="who-can-assign-synapse-rbac-roles"></a>SYNAPSE RBAC rollerini kim atayabilirler?

Yalnızca bir Synapse Yöneticisi SYNAPSE RBAC rolleri atayabilir.  Çalışma alanı düzeyindeki bir Synapse Yöneticisi herhangi bir kapsamda erişim izni verebilir.  Daha düşük düzeydeki bir kapsamdaki bir Synapse Yöneticisi yalnızca ilgili kapsamda erişim izni verebilir. 

Yeni bir çalışma alanı oluşturulduğunda, Oluşturucu, çalışma alanı kapsamında SYNAPSE yönetici rolü otomatik olarak verilir.   

## <a name="where-do-i-manage-synapse-rbac"></a>SYNAPSE RBAC 'yi nerede yönetebilirim?

SYNAPSE RBAC, yönetim hub 'ındaki erişim denetimi araçları kullanılarak SYNAPSE Studio içinden yönetilir. 

## <a name="next-steps"></a>Sonraki adımlar

Yerleşik [SYNAPSE RBAC rollerini](./synapse-workspace-synapse-rbac-roles.md)anlayın.

Bir çalışma alanı için [SYNAPSE RBAC rol atamalarını incelemeyi](./how-to-review-synapse-rbac-role-assignments.md) öğrenin.

[SYNAPSE RBAC rollerinin nasıl atanacağını](./how-to-manage-synapse-rbac-role-assignments.md) öğrenin