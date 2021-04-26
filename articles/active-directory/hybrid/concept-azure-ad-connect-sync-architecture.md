---
title: 'Azure AD Connect eşitleme: mimariyi anlama-Azure'
description: Bu konuda Azure AD Connect eşitleme mimarisi açıklanmakta ve kullanılan terimler açıklanmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 465bcbe9-3bdd-4769-a8ca-f8905abf426d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b27055ce84bbb073045b69b942fd13f4fde4e3b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90563871"
---
# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Azure AD Connect eşitleme: mimariyi anlama
Bu konuda Azure AD Connect eşitleme için temel mimari ele alınmaktadır. Birçok bakımdan, öncülleri MıSıS 2003, Ile 2007 ve FIM 2010 ' ye benzer. Azure AD Connect eşitleme, bu teknolojilerin gelişmidir. Bu önceki teknolojilerden herhangi birini biliyorsanız, bu konunun içeriği de size tanıdık gelecektir. Eşitleme için yeni bir konudur, bu konu sizin için önemlidir. Bu konu başlığı, özelleştirmeleri Azure AD Connect eşitleme yapma (Bu konudaki eşitleme altyapısı adı verilir) sırasında başarılı olmak için bu konunun ayrıntılarını sağlamak için gerekli değildir.

## <a name="architecture"></a>Mimari
Eşitleme altyapısı, birden fazla bağlı veri kaynağında depolanan nesnelerin tümleşik bir görünümünü oluşturur ve bu veri kaynaklarındaki kimlik bilgilerini yönetir. Bu tümleşik görünüm, bağlı veri kaynaklarından alınan kimlik bilgileri ve bu bilgilerin nasıl işleyeceğini belirleme kuralları kümesi tarafından belirlenir.

### <a name="connected-data-sources-and-connectors"></a>Bağlı veri kaynakları ve bağlayıcılar
Eşitleme altyapısı, Active Directory veya bir SQL Server veritabanı gibi farklı veri depolarından kimlik bilgilerini işler. Verilerini veritabanı benzeri biçimde düzenleyen ve standart veri erişim yöntemleri sağlayan her veri deposu, eşitleme altyapısı için olası bir veri kaynağı adaysıdır. Eşitleme altyapısı tarafından eşitlenen veri depolarında **bağlı veri kaynakları** veya **bağlı dizinler** (CD) adı verilir.

Eşitleme altyapısı, **bağlayıcı** adı verilen bir modül içindeki bağlı bir veri kaynağıyla etkileşimi kapsüller. Her bağlantılı veri kaynağı türü için belirli bir bağlayıcı bulunur. Bağlayıcı gerekli bir işlemi bağlantılı veri kaynağının anladığı biçime çevirir.

Bağlayıcılar, bağlı bir veri kaynağıyla Exchange kimlik bilgileri (hem okuma hem de yazma) için API çağrıları yapar. Genişletilebilir bağlantı çerçevesini kullanarak özel bir bağlayıcı eklemek de mümkündür. Aşağıdaki çizim, bir bağlayıcının bir bağlantı veri kaynağını eşitleme altyapısına nasıl bağladığını gösterir.

![Diyagramda bağlı bir veri kaynağı ve bağlayıcı adlı bir satırla ilişkili bir eşitleme altyapısı gösterilmektedir.](./media/concept-azure-ad-connect-sync-architecture/arch1.png)

Veriler her iki yönde de akabilir, ancak aynı anda iki yönde de akamaz. Diğer bir deyişle, bir bağlayıcı, verilerin bağlı veri kaynağından Sync Engine 'e veya Sync Engine 'e bağlı veri kaynağına akmasını sağlamak üzere yapılandırılabilir, ancak bu işlemlerden yalnızca biri bir nesne ve öznitelik için herhangi bir zamanda meydana gelebilir. Yön farklı nesneler ve farklı öznitelikler için farklı olabilir.

Bir bağlayıcıyı yapılandırmak için, eşitlenmesini istediğiniz nesne türlerini belirtirsiniz. Nesne türlerini belirtmek, eşitleme işlemine dahil edilen nesnelerin kapsamını tanımlar. Bir sonraki adım, bir öznitelik ekleme listesi olarak bilinen eşitleyeceğiniz öznitelikleri seçmesidir. Bu ayarlar, iş kurallarınızdaki değişikliklere yanıt olarak herhangi bir zamanda değiştirilebilir. Azure AD Connect Yükleme Sihirbazı 'nı kullandığınızda, bu ayarlar sizin için yapılandırılır.

Bağlı bir veri kaynağına nesneleri dışarı aktarmak için, öznitelik ekleme listesi, bağlı bir veri kaynağında belirli bir nesne türü oluşturmak için gereken en az sayıda özniteliği içermelidir. Örneğin, Active Directory içindeki tüm Kullanıcı nesnelerinin tanımlanmış bir **sAMAccountName** özniteliği olması gerektiğinden Active Directory için bir Kullanıcı nesnesini dışarı aktarmak üzere öznitelik ekleme listesine **sAMAccountName** özniteliği eklenmelidir. Yine, Yükleme Sihirbazı bu yapılandırmayı sizin için yapar.

Bağlı veri kaynağı nesneleri düzenlemek için bölümler veya kapsayıcılar gibi yapısal bileşenleri kullanıyorsa, bağlantılı veri kaynağındaki alanı belirli bir çözüm için sınırlayabilirsiniz.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Eşitleme altyapısı ad alanının iç yapısı
Tüm eşitleme altyapısı ad alanı, kimlik bilgilerini depolayan iki ad alanından oluşur. İki ad alanı şunlardır:

* Bağlayıcı alanı (CS)
* Metadize (MV)

**Bağlayıcı alanı** , bağlı bir veri kaynağından belirlenen nesnelerin temsillerini ve öznitelik ekleme listesinde belirtilen öznitelikleri içeren bir hazırlama alanıdır. Eşitleme altyapısı, bağlı veri kaynağında nelerin değiştiğini belirlemek ve gelen değişiklikleri hazırlamak için bağlayıcı alanını kullanır. Eşitleme altyapısı Ayrıca, bağlantılı veri kaynağına dışarı aktarma için giden değişiklikleri hazırlamak üzere bağlayıcı alanını kullanır. Eşitleme altyapısı, her bağlayıcı için bir hazırlama alanı olarak ayrı bir bağlayıcı alanı tutar.

Hazırlama alanı kullanarak, eşitleme altyapısı bağlı veri kaynaklarından bağımsız kalır ve bunların kullanılabilirliği ve erişilebilirliği bundan etkilenmez. Sonuç olarak, hazırlama alanındaki verileri kullanarak istediğiniz zaman kimlik bilgilerini işleyebilirsiniz. Eşitleme altyapısı, yalnızca son iletişim oturumu sonlandırıldığı veya yalnızca bağlı veri kaynağının henüz almamış olduğu kimlik bilgilerine yapılan değişiklikleri göndererek, eşitleme altyapısı ile bağlı veri kaynağı arasındaki ağ trafiğini azaltan, yalnızca bağlı veri kaynağı içinde yapılan değişiklikleri isteyebilir.

Ayrıca, eşitleme altyapısı, bağlayıcı alanında aşamaları olan tüm nesneler hakkında durum bilgilerini depolar. Yeni veriler alındığında, eşitleme altyapısı, verilerin zaten eşitlenip eşitlenmediğini her zaman değerlendirir.

**Meta** veri deposu, birden fazla bağlı veri kaynağından toplanan kimlik bilgilerini içeren bir depolama alanıdır ve tüm birleştirilmiş nesnelerin tek küresel, tümleşik bir görünümünü sağlar. Metadize nesneleri, bağlı veri kaynaklarından alınan kimlik bilgileri ve eşitleme işlemini özelleştirmenizi sağlayan bir dizi kural temel alınarak oluşturulur.

Aşağıdaki çizimde, bağlayıcı alanı ad alanı ve Sync Engine içindeki meta veri deposu ad alanı gösterilmektedir.

![Diyagramda bağlı bir veri kaynağı ve bağlayıcı alanı ve meta dize ad alanları ile ayrılmış, bağlayıcı adlı bir satırla ilişkilendirilen bir eşitleme altyapısı gösterilmektedir.](./media/concept-azure-ad-connect-sync-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Eşitleme altyapısı kimlik nesneleri
Eşitleme altyapısındaki nesneler, bağlı veri kaynağındaki nesnelerin veya eşitleme motorunun bu nesnelere sahip olduğu tümleşik görünümün temsilleridir. Her eşitleme motoru nesnesinin bir genel benzersiz tanıtıcısı (GUID) olmalıdır. GUID 'Ler, nesneler arasında veri bütünlüğü ve hızlı ilişkiler sağlar.

### <a name="connector-space-objects"></a>Bağlayıcı alanı nesneleri
Eşitleme altyapısı bağlı bir veri kaynağıyla iletişim kurduğunda, bağlantılı veri kaynağındaki kimlik bilgilerini okur ve bağlayıcı alanında kimlik nesnesinin bir gösterimini oluşturmak için bu bilgileri kullanır. Bu nesneleri tek tek oluşturamaz veya silemezsiniz. Ancak, bir bağlayıcı alanındaki tüm nesneleri el ile silebilirsiniz.

Bağlayıcı alanındaki tüm nesneler iki özniteliğe sahiptir:

* Genel benzersiz tanımlayıcı (GUID)
* Ayırt edici ad (DN olarak da bilinir)

Bağlı veri kaynağı nesneye benzersiz bir öznitelik atarsa, bağlayıcı alanındaki nesneler de bir tutturucu özniteliğine sahip olabilir. Tutturucu özniteliği bağlı veri kaynağındaki bir nesneyi benzersiz şekilde tanımlar. Eşitleme altyapısı, bağlantılı veri kaynağında bu nesnenin karşılık gelen gösterimini bulmak için bağlayıcıyı kullanır. Sync Engine, nesne bağlantısının nesnenin kullanım ömrü boyunca hiçbir zaman değişdüğünü varsayar.

Bağlayıcılardan birçoğu içeri aktarıldığında her bir nesne için otomatik olarak bir bağlantı oluşturmak üzere bilinen benzersiz bir tanımlayıcı kullanır. Örneğin, Active Directory Bağlayıcısı bir tutturucu için **Objectguıd** özniteliğini kullanır. Açıkça tanımlanmış benzersiz bir tanımlayıcı sağlamayan bağlı veri kaynakları için bağlayıcı yapılandırmasının bir parçası olarak bağlayıcı oluşturmayı belirtebilirsiniz.

Bu durumda, tutturucu bir nesne türünün bir veya daha fazla benzersiz özniteliklerinden oluşturulmuştur, hiçbir değişiklik değildir ve bağlayıcı alanında nesneyi benzersiz bir şekilde tanımlar (örneğin, bir çalışan numarası veya Kullanıcı KIMLIĞI).

Bir bağlayıcı alanı nesnesi aşağıdakilerden biri olabilir:

* Hazırlama nesnesi
* Yer tutucu

### <a name="staging-objects"></a>Hazırlama nesneleri
Hazırlama nesnesi, bağlı veri kaynağından belirlenen nesne türlerinin bir örneğini temsil eder. GUID ve ayırt edici ada ek olarak, bir hazırlama nesnesi her zaman nesne türünü gösteren bir değere sahiptir.

İçeri aktarılan hazırlama nesnelerinin her zaman tutturucu özniteliği için bir değeri vardır. Eşitleme altyapısı tarafından yeni sağlanmış olan ve bağlı veri kaynağında oluşturulma sürecinde olan hazırlama nesneleri, tutturucu özniteliği için bir değere sahip değil.

Hazırlama nesneleri aynı zamanda iş özniteliklerinin geçerli değerlerini ve eşitleme altyapısı ile eşitleme işlemini gerçekleştirmek için gereken işletimsel bilgileri de taşır. İşletimsel bilgiler, hazırlama nesnesinde hazırlanan güncelleştirmelerin türünü belirten bayraklar içerir. Hazırlama nesnesi henüz işlenmemiş bağlı veri kaynağından yeni kimlik bilgileri aldıysa, nesne **bekleyen içeri aktarma** olarak işaretlenir. Hazırlama nesnesi henüz bağlı veri kaynağına dışarı aktarılmamış yeni kimlik bilgilerine sahipse, bu, **bekleyen dışarı aktarma** olarak işaretlenir.

Hazırlama nesnesi bir içeri aktarma nesnesi veya dışa aktarma nesnesi olabilir. Eşitleme altyapısı, bağlı veri kaynağından alınan nesne bilgilerini kullanarak bir içeri aktarma nesnesi oluşturur. Eşitleme altyapısı, bağlayıcının seçtiği nesne türlerinden biriyle eşleşen yeni bir nesnenin varlığı hakkında bilgi aldığında, bağlayıcı alanında bağlı veri kaynağındaki nesnenin temsili olarak bir içeri aktarma nesnesi oluşturur.

Aşağıdaki çizimde, bağlantılı veri kaynağındaki bir nesneyi temsil eden bir içeri aktarma nesnesi gösterilmektedir.

![Diyagram, bağlı veri kaynağından eşitleme altyapısındaki bağlayıcı alanı ad alanına getirilen bir içeri aktarma nesnesi gösterir.](./media/concept-azure-ad-connect-sync-architecture/arch3.png)

Eşitleme altyapısı, meta veri deposundaki nesne bilgilerini kullanarak bir dışarı aktarma nesnesi oluşturur. Dışarı aktarma nesneleri, sonraki iletişim oturumu sırasında bağlı veri kaynağına dışarı aktarılabilir. Eşitleme altyapısının perspektifinden, dışarı aktarma nesneleri henüz bağlı veri kaynağında yok. Bu nedenle, bir dışa aktarma nesnesi için tutturucu özniteliği kullanılamaz. Eşitleme altyapısından nesne aldıktan sonra, bağlantılı veri kaynağı nesnenin tutturucu özniteliği için benzersiz bir değer oluşturur.

Aşağıdaki çizim, meta veri deposundaki kimlik bilgileri kullanılarak dışarı aktarma nesnesinin nasıl oluşturulduğunu gösterir.

![Diyagramda, meta veri deposundaki bağlayıcı alanı ad alanına, ardından bağlı veri kaynağına getirilen bir dışarı aktarma nesnesi gösterilir.](./media/concept-azure-ad-connect-sync-architecture/arch4.png)

Eşitleme altyapısı, nesneyi bağlı veri kaynağından yeniden içe aktararak nesnenin dışarı aktarılmasını onaylar. Dışarı aktarma nesneleri, eşitleme altyapısı bu bağlı veri kaynağından bir sonraki içeri aktarma işlemi sırasında aldığında içeri aktarma nesneleri haline gelir.

### <a name="placeholders"></a>Yer tutucular
Eşitleme altyapısı, nesneleri depolamak için düz bir ad alanı kullanır. Ancak, Active Directory gibi bazı bağlı veri kaynakları hiyerarşik bir ad alanı kullanır. Hiyerarşik bir ad alanından düz bir ad alanına bilgi dönüştürmek için, eşitleme altyapısı hiyerarşiyi korumak için yer tutucuları kullanır.

Her yer tutucu, bir nesnenin, eşitleme altyapısına aktarılmamış ancak hiyerarşik ad oluşturmak için gerekli olan hiyerarşik adının bir bileşenini (örneğin, bir kuruluş birimi) temsil eder. Bağlantılı veri kaynağındaki başvurular tarafından oluşturulan boşlukları, bağlayıcı alanında hazırlama nesneleri olmayan nesnelere doldurur.

Eşitleme altyapısı, henüz içeri aktarılmamış olan Başvurulmuş nesneleri depolamak için yer tutucuları de kullanır. Örneğin, eşitleme, *Abbie Spencer* nesnesi için Manager özniteliğini içerecek şekilde yapılandırıldıysa ve alınan değer henüz içeri aktarılmamış bir nesnedir (örneğin, *CN = Lee SPERRY, CN = kullanıcılar, DC = Fabrikam, DC = com*), yönetici bilgileri bağlayıcı alanında yer tutucu olarak depolanır. Yönetici nesnesi daha sonra içeri aktarıldıysa, yer tutucu nesnesinin, yöneticiyi temsil eden hazırlama nesnesi tarafından üzerine yazılır.

### <a name="metaverse-objects"></a>Meta veri deposu nesneleri
Meta veri deposu nesnesi, eşitleme altyapısının bağlayıcı alanındaki hazırlama nesnelerine sahip olduğu toplanmış görünümü içerir. Eşitleme altyapısı, içeri aktarma nesneleri içindeki bilgileri kullanarak meta dize nesneleri oluşturur. Birkaç bağlayıcı alanı nesnesi tek bir meta veri deposu nesnesine bağlanabilir, ancak bir bağlayıcı alanı nesnesi birden fazla meta veri deposu nesnesine bağlanamaz.

Meta veri deposu nesneleri el ile oluşturulamaz veya silinemez. Eşitleme altyapısı, bağlayıcı alanındaki herhangi bir bağlayıcı alanı nesnesine bağlantısı olmayan metadize nesnelerini otomatik olarak siler.

Bir bağlı veri kaynağı içindeki nesneleri meta veri deposu içindeki karşılık gelen bir nesne türüne eşlemek için, Sync Engine, önceden tanımlanmış bir nesne türleri kümesi ve ilişkili öznitelikler içeren genişletilebilir bir şema sağlar. Meta veri deposu nesneleri için yeni nesne türleri ve öznitelikler oluşturabilirsiniz. Öznitelikler tek değerli veya çok değerli olabilir ve öznitelik türleri dizeler, başvurular, sayılar ve Boole değerleri olabilir.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Hazırlama nesneleri ile meta veri deposu nesneleri arasındaki ilişkiler
Eşitleme altyapısı ad alanı içinde, veri akışı hazırlama nesneleri ile meta veri deposu nesneleri arasındaki bağlantı ilişkisi tarafından etkinleştirilir. Meta veri deposu nesnesine bağlı bir hazırlama nesnesi, **birleştirilmiş nesne** (veya **bağlayıcı nesnesi**) olarak adlandırılır. Meta veri deposu nesnesine bağlı olmayan hazırlama nesnesi, bağlantısı kesilen **nesne** (veya **ayırıcı nesnesi**) olarak adlandırılır. Katılmış ve bağlı olmayan koşullar, bağlı bir dizinden veri içeri ve dışarı aktarılırken sorumlu bağlayıcılar ile karıştırılmaması için tercih edilir.

Yer tutucular hiçbir şekilde meta veri deposu nesnesine bağlanmaz

Birleştirilmiş bir nesne, hazırlama nesnesini ve bu nesnenin bağlantılı ilişkisini tek bir metadize nesnesiyle kapsar. Birleştirilmiş nesneler, bir bağlayıcı alanı nesnesi ile meta veri deposu nesnesi arasındaki öznitelik değerlerini eşleştirmek için kullanılır.

Bir hazırlama nesnesi eşitleme sırasında birleştirilmiş bir nesne haline geldiğinde, öznitelikler hazırlama nesnesi ile metadize nesnesi arasında akabilir. Öznitelik akışı çift yönlü ve öznitelik kurallarını içeri aktar ve öznitelik kurallarını dışarı aktar kullanılarak yapılandırılır.

Tek bağlayıcı alanı nesnesi yalnızca bir meta veri deposu nesnesine bağlanabilir. Ancak, her metadizeler nesnesi, aşağıdaki çizimde gösterildiği gibi, aynı veya farklı bağlayıcı alanlarında bulunan birden çok bağlayıcı alanı nesnesine bağlanabilir.

![Diyagram, bağlayıcılarla ilişkili iki bağlı veri nesnesini, birleştirilmiş nesneler ve birleştirilmemiş bir nesne içeren bir Sync altyapısına gösterir.](./media/concept-azure-ad-connect-sync-architecture/arch5.png)

Hazırlama nesnesi ile meta veri deposu nesnesi arasındaki bağlantılı ilişki kalıcıdır ve yalnızca belirttiğiniz kurallarla kaldırılabilir.

Bağlantılı olmayan nesne, herhangi bir meta veri deposu nesnesine bağlı olmayan bir hazırlama nesnesidir. Bağlantılı olmayan bir nesnenin öznitelik değerleri, meta veri deposu içinde başka hiçbir şekilde işlenmez. Bağlı veri kaynağındaki karşılık gelen nesnenin öznitelik değerleri, Sync Engine tarafından güncellenmez.

Bağlantılı nesneleri kullanarak, kimlik bilgilerini eşitleme altyapısında depolayıp daha sonra işleyebilirsiniz. Hazırlama nesnesinin bağlayıcı alanındaki bağlantılı bir nesne olarak tutulması birçok avantaj sunar. Sistem bu nesne hakkında gerekli bilgileri zaten aşamalandırdığı için, bağlantılı veri kaynağından bir sonraki içeri aktarma sırasında bu nesnenin bir gösterimini oluşturmak gerekli değildir. Bu şekilde, bağlantılı veri kaynağına geçerli bir bağlantı olmasa bile, eşitleme altyapısı her zaman bağlı veri kaynağının tamamen anlık görüntüsüne sahiptir. Birleştirilmemiş nesneler, belirttiğiniz kurallara bağlı olarak birleştirilmiş nesnelere dönüştürülebilir ve tam tersi de geçerlidir.

İçeri aktarma nesnesi, bağlantılı olmayan bir nesne olarak oluşturulur. Dışarı aktarma nesnesi birleştirilmiş bir nesne olmalıdır. Sistem mantığı bu kuralı uygular ve birleştirilmiş bir nesne olmayan tüm dışarı aktarma nesnelerini siler.

## <a name="sync-engine-identity-management-process"></a>Eşitleme altyapısı kimlik yönetimi işlemi
Kimlik Yönetimi işlemi, farklı bağlı veri kaynakları arasında kimlik bilgilerinin nasıl güncelleştirileceğini denetler. Kimlik yönetimi üç işlem halinde gerçekleşir:

* İçeri Aktar
* Eşitleme
* Dışarı Aktarma

İçeri aktarma işlemi sırasında, eşitleme altyapısı gelen kimlik bilgilerini bağlı bir veri kaynağından değerlendirir. Değişiklikler algılandığında, yeni hazırlama nesneleri oluşturur ya da eşitleme için bağlayıcı alanında var olan hazırlama nesnelerini güncelleştirir.

Eşitleme işlemi sırasında, eşitleme altyapısı meta veri deposu ' nu bağlayıcı alanında oluşan değişiklikleri yansıtacak şekilde güncelleştirir ve bağlayıcı alanını meta veri deposunda gerçekleşen değişiklikleri yansıtacak şekilde güncelleştirir.

Dışarı aktarma işlemi sırasında, eşitleme altyapısı hazırlama nesnelerinde hazırlanan ve bekleyen dışarı aktarma olarak işaretlenen değişiklikleri gönderir.

Aşağıdaki çizimde, her bir işlemin her birinin, kimlik bilgileri bağlı bir veri kaynağından diğerine akacağı nerede gerçekleştiği gösterilmektedir.

![Diyagramda, bağlı verilerden (içeri aktarma) bağlantılı verilere ait kimlik bilgilerinin, bağlı verilere (dışarı aktarma), bağlayıcı alanına (eşitleme) meta veri deposuna](./media/concept-azure-ad-connect-sync-architecture/arch6.png)

### <a name="import-process"></a>İçeri aktarma işlemi
İçeri aktarma işlemi sırasında, eşitleme altyapısı, güncelleştirmeleri kimlik bilgilerine göre değerlendirir. Sync Engine, bağlı veri kaynağından alınan kimlik bilgilerini hazırlama nesnesi hakkındaki kimlik bilgileriyle karşılaştırır ve hazırlama nesnesinin güncelleştirme gerektirip gerektirmediğini belirler. Hazırlama nesnesini yeni verilerle güncelleştirmek gerekirse, hazırlama nesnesi bekleyen içeri aktarma olarak işaretlenir.

Eşitleme altyapısı, eşitlemeden önce bağlayıcı alanındaki hazırlama nesneleri ile yalnızca değiştirilen kimlik bilgilerini işleyebilir. Bu işlem aşağıdaki avantajları sağlar:

* **Verimli eşitleme**. Eşitleme sırasında işlenen veri miktarı en aza indirilir.
* **Etkili yeniden eşitleme**. Eşitleme altyapısının veri kaynağına yeniden bağlanmadan, eşitleme altyapısının kimlik bilgilerini işleme şeklini değiştirebilirsiniz.
* **Eşitlemeyi önizleme fırsatı**. Kimlik yönetimi işlemiyle ilgili Varsayımlarınızın doğru olduğunu doğrulamak için eşitlemeyi önizleyebilirsiniz.

Bağlayıcıda belirtilen her nesne için, eşitleme altyapısı ilk olarak bağlayıcının bağlayıcı alanındaki nesnenin bir gösterimini bulmaya çalışır. Sync Engine, bağlayıcı alanındaki tüm hazırlama nesnelerini inceler ve eşleşen bir bağlayıcı özniteliğine sahip karşılık gelen bir hazırlama nesnesi bulmaya çalışır. Varolan hazırlama nesnesinin eşleşen bir tutturucu özniteliği yoksa, eşitleme altyapısı aynı ayırt edici ada sahip karşılık gelen bir hazırlama nesnesi bulmaya çalışır.

Eşitleme altyapısı ayırt edici ad ile eşleşen bir hazırlama nesnesi bulduğunda, ancak tutturucu tarafından eşleşmez, aşağıdaki özel davranış oluşur:

* Bağlayıcı alanında bulunan nesnenin bağlantısı yoksa, Sync Engine bu nesneyi bağlayıcı alanından kaldırır ve bir **sonraki eşitleme çalıştırmasında yeniden deneme sağlama** olarak bağlı olduğu metadize nesnesini işaretler. Ardından, yeni içeri aktarma nesnesini oluşturur.
* Bağlayıcı alanında bulunan nesnenin bir Bağlayıcısı varsa, eşitleme altyapısı bu nesnenin bağlı dizinde yeniden adlandırılmış veya silinmiş olduğunu varsayar. Bu, bağlayıcı alanı nesnesi için geçici ve yeni bir ayırt edici ad atar ve böylece gelen nesneyi hazırbir hale getirebilirsiniz. Eski nesne daha sonra **geçici** hale gelir ve durumu çözmek için bağlayıcının yeniden adlandırma veya silme işlemini içeri aktarması bekleniyor.

Eşitleme altyapısı, bağlayıcıda belirtilen nesneye karşılık gelen bir hazırlama nesnesi bulduktan sonra, ne tür değişiklikler uygulanacağını belirler. Örneğin, eşitleme altyapısı bağlı veri kaynağındaki nesneyi yeniden adlandırabilir veya silebilir ya da yalnızca nesnenin öznitelik değerlerini güncelleştirebilir.

Güncelleştirilmiş verilerle hazırlama nesneleri, bekleyen içeri aktarma olarak işaretlenir. Farklı türlerde bekleyen içeri aktarmalar mevcuttur. İçeri aktarma işleminin sonucuna bağlı olarak, bağlayıcı alanındaki bir hazırlama nesnesi aşağıdaki bekleyen içeri aktarma türlerinden birine sahiptir:

* **Yok**. Hazırlama nesnesinin özniteliklerinin hiçbirinde hiçbir değişiklik yok. Sync Engine, bu türe bekleyen içeri aktarma olarak bayrak eklemez.
* **Ekleyin**. Hazırlama nesnesi, bağlayıcı alanındaki yeni bir içeri aktarma nesnesidir. Sync Engine bu türü, meta veri deposundaki ek işleme için bekleyen içeri aktarma olarak işaretler.
* **Güncelleştirin**. Eşitleme altyapısı bağlayıcı alanında karşılık gelen bir hazırlama nesnesi bulur ve özniteliklerde yapılan güncelleştirmelerin meta veri deposunda işlenebilmesi için bu türü bekleyen içeri aktarma olarak işaretler. Güncelleştirmeler, nesne yeniden adlandırmayı içerir.
* **Silme**. Sync Engine, bağlayıcı alanında karşılık gelen bir hazırlama nesnesi bulur ve birleştirilmiş nesne silinebilmesi için bu türü bekleyen içeri aktarma olarak işaretler.
* **Sil/Ekle**. Eşitleme altyapısı bağlayıcı alanında karşılık gelen bir hazırlama nesnesi bulur, ancak nesne türleri eşleşmiyor. Bu durumda, silme-ekleme değişikliği hazırlanacaktır. Silme-ekleme değişikliği, eşitleme altyapısına, nesne türü değiştiğinde bu nesneye farklı kural kümeleri uygulandığı için, bu nesnenin tam bir yeniden eşitleme işleminin gerçekleşmesi gerektiğini gösterir.

Hazırlama nesnesinin bekleyen içeri aktarma durumunu ayarlayarak, eşitleme sırasında işlenen veri miktarını azaltmak mümkündür çünkü bu, sistemin yalnızca güncelleştirilmiş verileri olan nesneleri işlemesini sağlar.

### <a name="synchronization-process"></a>Eşitleme işlemi
Eşitleme iki ilişkili işlemlerden oluşur:

* Gelen eşitleme, meta veri deposu içeriği bağlayıcı alanındaki veriler kullanılarak güncelleniyorsa.
* Giden eşitleme, bağlayıcı alanının içeriği meta veri deposundaki veriler kullanılarak güncelleniyorsa.

Bağlayıcı alanında hazırlanan bilgileri kullanarak, gelen eşitleme işlemi, bağlantılı veri kaynaklarında depolanan verilerin tümleşik görünümünde meta veri deposunda oluşturulur. Kuralların nasıl yapılandırıldığına bağlı olarak, tüm hazırlama nesneleri ya da yalnızca bekleyen içeri aktarma bilgilerine sahip olanlar toplanır.

Giden eşitleme işlemi, meta veri deposu nesneleri değiştiğinde dışarı aktarma nesnelerini güncelleştirir.

Gelen eşitleme, bağlantılı veri kaynaklarından alınan kimlik bilgilerinin meta dizesinde tümleşik görünümü oluşturur. Eşitleme altyapısı, bağlı veri kaynağından aldığı en son kimlik bilgilerini kullanarak, kimlik bilgilerini istediğiniz zaman işleyebilir.

**Gelen eşitleme**

Gelen eşitlemeye aşağıdaki süreçler dahildir:

* **Sağlama** (Bu işlemi giden eşitleme sağlamaktan ayırt etmek önemliyse, **İzdüşüm** da denir). Eşitleme altyapısı, hazırlama nesnesini temel alan yeni bir meta veri deposu nesnesi oluşturur ve bunları bağlar. Sağlama, nesne düzeyinde bir işlemdir.
* **Birleştirin**. Eşitleme altyapısı, hazırlama nesnesini var olan meta veri deposu nesnesine bağlar. JOIN, nesne düzeyinde bir işlemdir.
* **Öznitelik akışını Içeri aktar**. Sync Engine, meta veri deposundaki nesnenin öznitelik akışı olarak adlandırılan öznitelik değerlerini güncelleştirir. Import Öznitelik Flow, hazırlama nesnesi ile meta veri deposu nesnesi arasında bağlantı gerektiren öznitelik düzeyi bir işlemdir.

Sağlama, meta veri deposunda nesne oluşturan tek işlemdir. Sağlama yalnızca, bağlantılı olmayan nesneler içeri aktarma nesnelerini etkiler. Sağlama sırasında, eşitleme altyapısı içeri aktarma nesnesinin nesne türüne karşılık gelen bir meta veri deposu nesnesi oluşturur ve her iki nesne arasında bir bağlantı kurar ve bu nedenle birleştirilmiş bir nesne oluşturulur.

JOIN işlemi ayrıca içeri aktarma nesneleri ile meta veri deposu nesnesi arasında bir bağlantı oluşturur. JOIN ve provision arasındaki fark, JOIN işleminin içeri aktarma nesnesinin, sağlama işleminin yeni bir meta veri deposu nesnesi oluşturduğu mevcut bir meta veri deposu nesnesine bağlanmasını gerektirmesidir.

Eşitleme altyapısı, eşitleme kuralı yapılandırmasında belirtilen ölçütleri kullanarak bir içeri aktarma nesnesine bir meta veri deposu nesnesine katılmayı dener.

Sağlama ve katılma işlemi sırasında, Sync Engine, bağlantılı bir nesneyi metabir nesne ile bir meta veri deposu nesnesine bağlar ve bu nesnelerin katılmasını sağlar. Bu nesne düzeyindeki işlemler tamamlandıktan sonra, eşitleme altyapısı ilişkili metadize nesnesinin öznitelik değerlerini güncelleştirebilir. Bu işleme, Import öznitelik akışı olarak adlandırılır.

İçeri aktarma öznitelik akışı, yeni veriler taşıyan ve bir meta veri deposu nesnesine bağlanmış olan tüm içeri aktarma nesnelerinde gerçekleşir.

**Giden eşitleme**

Giden eşitleme güncelleştirmeleri, meta veri deposu nesnesi değiştiğinde ve silinmediği zaman nesneleri dışarı aktarır. Giden eşitlemenin amacı, meta veri deposu nesnelerinde yapılan değişikliklerin bağlayıcı alanlarında hazırlama nesneleri için güncelleştirme gerekip gerekmediğini değerlendirmelidir. Bazı durumlarda, değişiklikler tüm bağlayıcı alanlarında hazırlama nesnelerinin güncelleştirilmesini gerektirebilir. Değiştirilen hazırlama nesneleri, bekleyen dışarı aktarma olarak işaretlenir, bu da nesneleri dışarı aktarır. Bu dışarı aktarma nesneleri daha sonra dışa aktarma işlemi sırasında bağlı veri kaynağına gönderilir.

Giden eşitlemede üç işlem vardır:

* **Sağlanıyor**
* **Sağlamayı kaldırma**
* **Öznitelik akışını dışarı aktar**

Sağlama ve sağlamayı kaldırma, nesne düzeyinde işlemlerdir. Sağlamayı kaldırma, yalnızca sağlama işlemi başlatabileceğinden, sağlama bağımlıdır. Sağlama kaldırma, meta veri deposu nesnesi ve dışarı aktarma nesnesi arasındaki bağlantıyı kaldırdığında tetiklenir.

Sağlama, meta veri deposundaki nesnelere değişiklikler uygulandığında her zaman tetiklenir. Meta veri deposu nesnelerinde değişiklik yapıldığında, eşitleme altyapısı sağlama sürecinin bir parçası olarak aşağıdaki görevlerden herhangi birini gerçekleştirebilir:

* Meta veri deposu nesnesinin yeni oluşturulan bir dışarı aktarma nesnesiyle bağlantılı olduğu birleştirilmiş nesneleri oluşturun.
* Birleştirilmiş bir nesneyi yeniden adlandırın.
* Bir meta veri deposu nesnesi ve hazırlama nesneleri arasındaki bağlantıları, bağlantısı kesilen bir nesne oluşturarak birleştirin.

Hazırlama, yeni bir bağlayıcı nesnesi oluşturmak için eşitleme altyapısı gerektiriyorsa, nesne bağlı veri kaynağında henüz mevcut olmadığından, meta veri deposu nesnesinin bağlı olduğu hazırlama nesnesi her zaman dışa aktarma nesnesidir.

Sağlama, eşitleme altyapısının birleştirilmiş bir nesneye katılmasını gerektiriyorsa, bağlantısı kesilen bir nesne oluşturmak için, kaldırma işlemi tetiklenir. Sağlamayı kaldırma işlemi nesneyi siler.

Sağlama kaldırma sırasında, bir dışa aktarma nesnesinin silinmesi nesneyi fiziksel olarak silmez. Nesne **Silinmiş** olarak işaretlenir, bu, silme işleminin nesne üzerinde hazırlanması anlamına gelir.

Dışarı aktarma öznitelik akışı, giden eşitleme işlemi sırasında da meydana gelen eşitleme sırasında öznitelik akışını içeri aktarma yöntemine benzer şekilde gerçekleşir. Dışarı aktarma öznitelik akışı yalnızca meta veri deposu ve birleştirilmiş nesneler arasında gerçekleşir.

### <a name="export-process"></a>Dışarı aktarma işlemi
Dışarı aktarma işlemi sırasında, eşitleme altyapısı, bağlayıcı alanında bekleyen dışarı aktarma olarak işaretlenen tüm dışarı aktarma nesnelerini inceler ve sonra bağlı veri kaynağına güncelleştirmeleri gönderir.

Eşitleme altyapısı bir dışarı aktarmanın başarısını tespit edebilir, ancak kimlik yönetimi işleminin tamamlandığını yeterince belirleyemez. Bağlı veri kaynağındaki nesneler, her zaman diğer süreçler tarafından değiştirilebilir. Eşitleme altyapısının bağlantılı veri kaynağıyla kalıcı bağlantısı olmadığından, bağlı veri kaynağındaki bir nesnenin özellikleri hakkında varsayımlar yapmak, yalnızca başarılı bir dışarı aktarma bildirimine göre yeterli değildir.

Örneğin, bağlantılı veri kaynağındaki bir işlem nesnenin özniteliklerini orijinal değerlerine değiştirebilir (yani, bağlı veri kaynağı, veriler eşitleme altyapısı tarafından gönderildikten ve bağlı veri kaynağında başarıyla uygulandıktan sonra değerlerin üzerine yazabilir).

Eşitleme altyapısı her hazırlama nesnesi hakkında dışarı ve içeri aktarma durum bilgilerini depolar. Öznitelik ekleme listesinde belirtilen özniteliklerin değerleri son dışarı aktarma işleminden sonra değiştiyse, içeri ve dışarı aktarma durumunun depolanması, eşitleme altyapısının uygun şekilde tepki vermesini sağlar. Sync Engine, bağlı veri kaynağına aktarılmış öznitelik değerlerini onaylamak için içeri aktarma işlemini kullanır. İçeri aktarılan ve dışarı aktarılan bilgiler arasında, aşağıdaki çizimde gösterildiği gibi bir karşılaştırma, eşitleme altyapısının dışarı aktarmanın başarılı olup olmadığını veya tekrarlanması gerekip gerekmediğini belirlemesine olanak sağlar.

![Diyagramda bağlayıcı alanı ve bağlayıcı üzerinden bağlı veriler arasındaki bir nesnenin eşitlenmesi gösterilmektedir.](./media/concept-azure-ad-connect-sync-architecture/arch7.png)

Örneğin, eşitleme altyapısı, bir değeri 5 olan C özniteliğini bağlı bir veri kaynağına dışa aktardığında, C = 5 değerini dışa aktarma durumu belleğinde depolar. Bu nesnedeki her bir ek dışa aktarma işlemi, bu değerin kalıcı olarak nesneye uygulanmadığını (yani, bağlı veri kaynağından farklı bir değer içeri aktarılmadığı müddetçe) yeniden bağlantılı veri kaynağına dışa aktarma girişimine neden olur. Nesne üzerindeki bir içeri aktarma işlemi sırasında C = 5 alındığında dışarı aktarma belleği temizlenir.

## <a name="next-steps"></a>Sonraki adımlar
[Azure AD Connect eşitleme](how-to-connect-sync-whatis.md) yapılandırması hakkında daha fazla bilgi edinin.

[Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md) hakkında daha fazla bilgi edinin.

