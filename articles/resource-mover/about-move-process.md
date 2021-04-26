---
title: Azure Kaynak taşıyıcısı 'nde taşıma işlemi hakkında
description: Azure Kaynak taşıyıcısı ile bölgeler arasında kaynakları taşıma süreci hakkında bilgi edinin
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: overview
ms.date: 02/01/2021
ms.author: raynew
ms.openlocfilehash: 6eba77e1af2cd1a038f2ebd61a85ccd03b9a0741
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106074463"
---
# <a name="about-the-move-process"></a>Taşıma işlemi hakkında

Azure [kaynak taşıyıcısı](overview.md) Azure kaynaklarını Azure bölgelerinde taşımanızı sağlar. Bu makale, kaynak taşıyıcısı tarafından kullanılan bileşenleri özetler ve taşıma işlemini açıklar. 


## <a name="components"></a>Bileşenler

Bu bileşenler bölge taşıma sırasında kullanılır.

**Bileşen** | **Ayrıntılar**
--- | ---
**Kaynak taşıyıcısı** |  Kaynak taşıyıcısı, kaynakların bölgeler arasında taşınmasını yönetmek için [Azure kaynak sağlayıcıları](../azure-resource-manager/management/resource-providers-and-types.md) ile eşgüdümünü sağlar. Kaynak taşıyıcısı Kaynak bağımlılıklarını analiz eder ve taşıma işlemi sırasında kaynakların durumunu korur ve yönetir. 
**Koleksiyonu taşı** |  Taşıma koleksiyonu bir [Azure Resource Manager](../azure-resource-manager/management/overview.md) nesnesidir.<br/><br/> Taşıma koleksiyonu, bir abonelikteki kaynak ve hedef bölgelerin her eşleştirilmiş birleşimi için bölge taşıma işlemi sırasında oluşturulur. Koleksiyon, taşımak istediğiniz kaynaklarla ilgili meta verileri ve yapılandırma bilgilerini içerir.<br/><br/>Bir taşıma koleksiyonuna eklenen kaynakların aynı abonelikte olması gerekir, ancak farklı kaynak gruplarında yer alabilir. 
**Kaynağı taşı** | Bir taşıma koleksiyonuna kaynak eklediğinizde, kaynak taşıyıcısı tarafından taşıma kaynağı olarak izlenir.<br/><br/> Kaynak taşıyıcısı, taşıma koleksiyonundaki tüm taşıma kaynakları için bilgileri tutar ve kaynak ile hedef kaynak arasında bire bir ilişki sağlar. 
**Bağımlılıklar** | Kaynak taşıyıcısı bir koleksiyona eklediğiniz kaynakları doğrular ve kaynakların taşıma koleksiyonunda olmayan herhangi bir bağımlılığı olup olmadığını denetler.<br/><br/> Bir kaynağın bağımlılıklarını tanımladıktan sonra, bu bağımlılıkları taşıma koleksiyonuna ekleyebilir ve bunları da taşıyabilir ya da hedef bölgede alternatif var olan kaynakları seçebilirsiniz. Taşıma işlemine başlamadan önce tüm bağımlılıkların çözülmesi gerekir. 



## <a name="move-region-process"></a>Bölge işlemini taşıma 

![Taşıma adımlarını gösteren diyagram](./media/about-move-process/move-steps.png)

Her taşıma kaynağı özetlenen adımlardan geçer.

| **Adım** | **Ayrıntılar** | **Durum/sorunlar** |
| --- | --- | --- |
| **1. Adım: kaynakları seçin** | Bir kaynak seçin. Kaynak, taşıma koleksiyonuna eklenir. | Kaynak durumu hazırlama için *bekliyor* olarak taşınıyor.<br/><br/> Kaynağın bağımlılıkları varsa, *bağımlılığı doğrula* , bağımlı kaynakları taşıma koleksiyonuna eklemeniz gerektiğini gösterir. |
| **2. Adım: bağımlılıkları doğrulama** | Doğrulama sürecini başlatın.<br/><br/> Doğrulama bağımlı kaynakların beklendiğini gösteriyorsa, bunları taşıma koleksiyonuna ekleyin. <br/><br/> Tüm bağımlı kaynakları taşımak istemeseniz bile ekleyin. Daha sonra, taşıdığınız kaynakların bunun yerine hedef bölgede farklı kaynaklar kullanması gerektiğini belirtebilirsiniz.<br/><br/> Bekleyen bağımlılıklar kalmayana kadar tekrar doğrulayabilirsiniz. | Tüm bağımlılıklar eklendikten ve doğrulama başarılı olduktan sonra, kaynak durumu herhangi bir sorun olmadan *bekleyen hazırlık* durumuna getirilir. |
| **3. Adım: hazırlama** | Hazırlama işlemini başlatın. Hazırlama adımları, taşıdığınız kaynaklara bağlıdır:<br/><br/> - **Durum bilgisiz kaynakları**: durum bilgisiz kaynaklar yalnızca yapılandırma bilgilerine sahiptir. Bu kaynaklar, verileri taşımak için sürekli çoğaltmaya gerek kalmaz. Azure sanal ağları (sanal ağlar), ağ bağdaştırıcıları, yük dengeleyiciler ve ağ güvenlik grupları örnekleri bulunur. Bu tür bir kaynak için, hazırlama işlemi Azure Resource Manager bir şablon oluşturur.<br/><br/> - **Durum bilgisi olan kaynaklar**: durum bilgisi olan kaynaklar hem yapılandırma bilgilerine hem de taşınması gereken verilere sahiptir. Azure VM 'Leri ve Azure SQL veritabanlarını içeren örneklere örnek olarak verilebilir. Hazırlama işlemi her kaynak için farklılık gösterir. Kaynak kaynağın hedef bölgeye çoğaltılmasını içerebilir. | Devre dışı bırakma, kaynak durumunu *hazırlama işlemini devam* ettirir.<br/><br/> Hazırlama işlemi tamamlandıktan sonra, kaynak durumu, hiçbir sorun olmadan *taşıma Işlemini başlatmak* için taşınır.<br/><br/> Başarısız bir işlem durumu *hazırlama* durumuna taşınıyor. |
| **4. Adım: taşımayı başlatma** | Taşıma sürecini başlatın. Move yöntemi kaynak türüne bağlıdır:<br/><br/> - **Durum bilgisiz**: genellikle durum bilgisiz kaynaklar için taşıma işlemi, içeri aktarılan bir şablonu hedef bölgede dağıtır. Şablon, kaynak kaynak ayarlarını ve hedef ayarlarda yaptığınız tüm el ile düzenlemeleri temel alır.<br/><br/> - **Durum bilgisi**: durum bilgisi olan kaynaklar için taşıma işlemi, hedef bölgede kaynak oluşturmayı veya bir kopyayı etkinleştirmeyi içerebilir.<br/><br/>  Yalnızca durum bilgisi olan kaynaklar için, bir taşımanın başlatılması kaynak kaynakların kapalı kalma süresine yol açabilir. Örneğin, VM 'Ler ve SQL. | Taşıma devre dışı bırakma *işlemi, devam eden taşıma Işlemini başlatmak* için durumu kaydırır.<br/><br/> Başarılı bir başlatma taşıması kaynak durumunu, hiçbir sorun olmadan *taşıma bekleniyor* olarak taşır. <br/><br/> Taşıma işlemini başlatmak için başarısız bir taşıma işlemi durumu *başarısız oldu*. |
| **Adım 5 seçenek 1: taşımayı at** | İlk taşıdıktan sonra, bir tam taşıma ile devam etmek isteyip istemediğinize karar verebilirsiniz. Bunu yapmazsanız, taşımayı atabilir ve kaynak taşıyıcısı hedefte oluşturulan kaynakları siler. Durum bilgisi olan kaynaklar için çoğaltma işlemi, atma işleminden sonra devam eder. Bu seçenek, test etmek için kullanışlıdır. | Kaynakları atma *işlemi devam ediyor* durumuna getirilir.<br/><br/> Başarılı bir atma işlemi, hiçbir sorun olmadan *taşıma beklemeyi başlatacak* şekilde durumu taşır.<br/><br/> Başarısız bir atma, taşıma durumunun *atma işlemi başarısız oldu*. |
| **Adım 5 seçenek 2: taşımayı Kaydet** | İlk taşıma işleminden sonra, bir tam taşıma ile devam etmek istiyorsanız, hedef bölgedeki kaynakları doğrularsınız ve ne zaman hazırlandıysanız, taşımayı taşırsınız.<br/><br/> Yalnızca durum bilgisi olan kaynaklar için, COMMIT, VM 'Ler veya SQL gibi kaynak kaynaklarında erişilemez duruma gelmesine neden olabilir. | Taşıma işlemini kullanırsanız, kaynak durumu * devam eden taşıma devam ediyor * * olarak taşınır.<br/><br/> Başarılı bir işlemeden sonra, kaynak durumu bir sorun olmadan *Tamamlanan taşıma hareketini* gösterir.<br/><br/> Başarısız bir kayıt *taşıma durumunun yürütülmesi başarısız oldu*. |
| **6. Adım: kaynağı silme** | Taşıma işlemi tamamlandıktan ve hedef bölgede kaynakları doğruladıktan sonra kaynak kaynağı silebilirsiniz. | İşlem tamamlandıktan sonra, kaynak durumu *bekleyen kaynağı Sil*'e gider. Daha sonra kaynak kaynağı seçip silebilirsiniz.<br/><br/> -Yalnızca *kaynak silme bekleme* durumundaki kaynaklar silinebilir. <br/><br/>Kaynak taşıyıcısı portalındaki bir kaynak grubunun veya SQL Server silinmesi desteklenmez. Bu kaynaklar yalnızca kaynak özellikleri sayfasından silinebilir. |


## <a name="move-region-states"></a>Bölge durumlarını taşı

Taşıma işleminde bir dizi durum ve her durum sırasında ortaya çıkabilecek sorunlar vardır. Bunlar akış çizelgesinde özetlenir.

![Olası durumlar ve sorunlar için akış çizelgesi](./media/about-move-process/process.png)


### <a name="dependency-analysis"></a>Bağımlılık Analizi

Taşıma sürecinde ilerlemeniz durumunda şu durumlarda bağımlılıkları doğrulamanız istenebilir:
- Kaynak, taşıma koleksiyonunda olmayan bağımlı kaynakları kullanır.
- Taşıma koleksiyonundaki bağımlı bir kaynağın, taşıma koleksiyonunda olmayan kendi bağımlılıkları vardır.
- Kaynak için hedef ayarlarını değiştirdiniz ve bağımlılıkları yeniden doğrulamanız gerekiyor.


### <a name="remove-resources"></a>Kaynakları kaldır

Bir kaynağı taşımak istemiyorsanız, taşıma koleksiyonundan kaldırabilirsiniz. Genel olarak, kaynak, çoğaltma veya saklı şablonlar gibi ilişkili eylemler veya nesnelerle birlikte koleksiyondan silinir. Bir kaynağı kaldırdığınızda ne olacağı, kaynağın türüne ve kaynağı sildiğinizde kaynağın durumuna bağlıdır. [Daha fazla bilgi edinin](remove-move-resources.md).

## <a name="move-impact"></a>Taşıma etkisi

Tablo, bölgeler arasında hareket ettirdiğinde nelerin etkilendiğini özetler.

**Davranış** | **Bölgeler arasında**
--- | --- | --- 
**Veriler** | Kaynak verileri ve meta veriler taşınır.<br/><br/> Meta veriler, kaynak bağımlılıklarının ve işlemlerinin durumunu izlemek için geçici olarak depolanır.
**Kaynak** | Kaynak kaynak, uygulamaların çalışmaya devam etmesini sağlamak için değişmeden kalır ve taşıma işleminden sonra isteğe bağlı olarak kaldırılabilirler.<br/><br/> Hedef bölgede bir kaynak oluşturulur.
**İşlemi taşı** | El ile müdahale ve izleme gerektiren çok adımlı işlem.
**Test etme** | Taşıma işlemi, uygulamanın hedef bölgede beklenildiği gibi çalışmaya devam etmesi gerektiğinden, taşıma işleminin test edilmesi önemlidir.
**Downtime** |  Veri kaybı beklenmez, ancak kaynakları taşıyacağınız kapalı kalma süresi.



## <a name="next-steps"></a>Sonraki adımlar

[Taşı](tutorial-move-region-virtual-machines.md) Azure VM 'lerini başka bir bölgeye.
[Taşı](tutorial-move-region-sql.md) Azure SQL kaynaklarını başka bir bölgeye.
