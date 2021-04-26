---
title: Adanmış bir SQL havuzunda SYNAPSE çalışma alanı özelliklerini etkinleştirme (eski adıyla SQL DW)
description: Bu belgede, bir müşterinin çalışma alanında var olan SQL DW tek başına örneğini nasıl erişebileceği ve kullanabileceği açıklanır.
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 6d71d9889701ec834747e4bec1dd111157c3206e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101694623"
---
# <a name="enabling-synapse-workspace-features-on-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Var olan ayrılmış bir SQL havuzunda SYNAPSE çalışma alanı özelliklerini etkinleştirme (eski adıyla SQL DW)

Artık tüm SQL veri ambarı müşterileri, otomasyon, bağlantılar veya araçları etkilemeden, SYNAPSE Studio ve çalışma alanı aracılığıyla mevcut bir adanmış SQL Havuzu (eski adıyla SQL DW) örneğine erişebilir ve bunları kullanabilir. Bu makalede, var olan bir Azure SYNAPSE Analytics müşterisinin, SYNAPSE Workspace ve Studio aracılığıyla kullanıma sunulan yeni özellik açısından zengin özelliklerden yararlanarak mevcut analiz çözümlerini nasıl oluşturup genişletgetirebileceği açıklanmaktadır.   

## <a name="experience"></a>Deneyim
 
Artık SYNAPSE çalışma alanı GA olduğuna göre, DW portalı genel bakış bölümünde var olan adanmış SQL Havuzu (eski adıyla SQL DW) örnekleri için bir Synapse çalışma alanı oluşturmanıza olanak sağlayan yeni bir özellik mevcuttur. Bu yeni özellik, var olan veri ambarı örneklerinizi barındıran mantıksal sunucuyu yeni bir Synapse çalışma alanına bağlamanıza olanak tanır. Bağlantı, bu sunucuda barındırılan tüm veri ambarlarının çalışma alanından ve Studio 'dan erişilebilir hale gelmesini sağlar ve SYNAPSE iş ortağı hizmetleri (sunucusuz SQL havuzu, Apache Spark havuzu ve ADF) ile birlikte kullanılabilir. Sağlama adımları tamamlandıktan hemen sonra ve yeni oluşturulan çalışma alanına bağlantı kurulmuşdan kaynaklarınızı kullanmaya başlayabilirsiniz.  
:::image type="content" source="media/workspace-connected-overview/workspace-connected-dw-portal-overview-pre-create.png" alt-text="Bağlı SYNAPSE çalışma alanı":::

## <a name="using-synapse-workspace-and-studio-features-to-access-and-use-a-dedicated-sql-pool-formerly-sql-dw"></a>Özel bir SQL havuzuna erişmek ve kullanmak için SYNAPSE Workspace ve Studio özelliklerini kullanma (eski adıyla SQL DW)
 
Özel bir SQL DW (eski adıyla SQL DW) kullanılırken, SYNAPSE çalışma alanı özellikleri etkinleştirilmiş olarak aşağıdaki bilgiler geçerlidir: 
- **SQL özellikleri** SYNAPSE çalışma alanı özelliği etkinleştirildikten sonra tüm SQL özellikleri mantıksal SQL Server ile kalacaktır. Çalışma alanı etkinleştirildikten sonra, SQL kaynak sağlayıcısı üzerinden sunucuya erişim mümkün olmaya devam edecektir. Tüm yönetim işlevleri çalışma alanı aracılığıyla başlatılabilir ve işlem SQL havuzlarınızı barındıran mantıksal SQL Server yapılır. Bir çalışma alanı etkinleştirildiğinde varolan Otomasyon, araç veya bağlantı kopuk veya kesintiye uğratılmaz.  
- **Kaynak taşıma**  SYNAPSE çalışma alanı özelliği etkinleştirilmiş bir sunucuda bir kaynak taşıma işlemi başlatmak, sunucu ile çalışma alanı arasındaki bağlantının kesintiye uğramasına neden olur ve artık mevcut adanmış SQL Havuzu (eski adıyla SQL DW) örneklerine erişemeyeceksiniz. Bağlantının korunduğundan emin olmak için her iki kaynağın de aynı abonelik ve kaynak grubu içinde kalması önerilir. 
- **İzleme** Bir çalışma alanı etkin adanmış SQL Havuzu (eski adıyla SQL DW) ile SYNAPSE Studio aracılığıyla gönderilen SQL istekleri, Izleyici hub 'ında görüntülenebilir. Diğer tüm izleme etkinlikleri için Azure portal adanmış SQL Havuzu (eski adıyla SQL DW) izlemeye gidebilirsiniz. 
- **Güvenlik** ve **erişim denetimleri** yukarıda belirtilen şekilde, SQL Server ve adanmış SQL HAVUZLARı (eski adıyla SQL DW) örnekleri için tüm yönetim işlevleri mantıksal SQL Server 'da olmaya devam edecektir. Bu işlevler, Güvenlik Duvarı kural yönetimi, sunucunun Azure AD yöneticisini ve adanmış SQL havuzunuzdaki (eski adıyla SQL DW) verilere yönelik tüm erişim denetimini içerir. Adanmış SQL havuzunuzun (eski adıyla SQL DW) erişilebilir olduğundan ve SYNAPSE çalışma alanı aracılığıyla kullanılabilmesi için aşağıdaki adımlar gerçekleştirilmelidir. Çalışma alanı rol üyelikleri, kullanıcılara adanmış SQL Havuzu (eski adıyla SQL DW) örnekleri için izinler vermez. Kullanıcıların mantıksal sunucudaki adanmış SQL Havuzu (eski adıyla SQL DW) örneklerine erişebildiğinden emin olmak için normal [SQL kimlik doğrulama](sql-data-warehouse-authentication.md) ilkelerinizi izleyin. Adanmış SQL Havuzu (eski adıyla SQL DW) konak sunucusuna zaten atanmış bir yönetilen kimlik varsa, bu yönetilen kimliğin adı, çalışma alanı iş ortağı hizmetlerini desteklemek için otomatik olarak oluşturulan çalışma alanı yönetilen kimliği ile aynı olur (ör. ADF işlem hatları).  Bağlı bir senaryoda aynı ada sahip iki yönetilen kimlik bulunabilir. Yönetilen kimlikler, Azure AD nesne kimliklerine göre ayırt edilebilir, ancak nesne kimliklerini kullanarak SQL kullanıcıları oluşturma işlevselliği çok yakında kullanıma sunulacak.

    ```sql
    CREATE USER [<workspace managed identity] FROM EXTERNAL PROVIDER 
    GRANT CONTROL ON DATABASE:: <dedicated SQL pool name> TO [<workspace managed identity>
    ```

    > [!NOTE] 
    > Bağlı çalışma alanı SYNAPSE Studio, kullanıcının Azure 'daki izinleri temel alarak adanmış havuzların adlarını görüntüler. Kullanıcının SQL havuzları üzerinde izinleri yoksa havuzlardaki nesnelere erişilemeyecektir. 

- **Ağ güvenliği** Mevcut adanmış SQL havuzunuz (eski adıyla SQL DW) üzerinde etkinleştirdiğiniz SYNAPSE çalışma alanı veri girişi koruması için etkinleştirilirse. Çalışma alanından mantıksal SQL Server 'a yönetilen bir özel uç nokta bağlantısı oluşturun. Sunucu ile çalışma alanı arasında iletişime izin vermek için özel uç nokta bağlantı isteğini onaylayın.
- **Studio** **Veri MERKEZINDEKI** SQL havuzları bir çalışma alanı ETKINLEŞTIRILMIŞ adanmış SQL Havuzu (eskı ADıYLA SQL DW), veri merkezindeki araç ipucu aracılığıyla tanımlanabilir. 
- **Yeni bir ADANMıŞ SQL havuzu oluşturma (eski ADıYLA SQL DW)** Yeni adanmış SQL havuzları, çalışma alanı özelliği etkinleştirildikten sonra SYNAPSE çalışma alanı ve Studio aracılığıyla oluşturulabilir ve mantıksal SQL Server üzerinde yeni bir havuz sağlanacaktır. Yeni kaynaklar, sağlama tamamlandığında portalda ve Studio 'da görünür.      

## <a name="next-steps"></a>Sonraki adımlar
Mevcut adanmış SQL havuzunuzdaki [SYNAPSE çalışma alanı özelliklerini](workspace-connected-create.md) etkinleştirme (eskı ADıYLA SQL DW)
