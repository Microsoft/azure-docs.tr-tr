---
title: Synapse RBAC rolleri
description: Bu makalede yerleşik SYNAPSE RBAC rolleri açıklanmaktadır
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 35f66732fa9cb48b94f80bab203534c9d04b7a7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100102130"
---
# <a name="synapse-rbac-roles"></a>SYNAPSE RBAC rolleri

Makalede yerleşik SYNAPSE RBAC rolleri, izin verdiğiniz izinler ve kullanılabilecekleri kapsamlar açıklanmaktadır.  

## <a name="whats-changed-since-the-preview"></a>Önizlemeden bu yana neler değişti?
Önizleme sırasında belirtilen SYNAPSE RBAC rollerine alışkın olan kullanıcılar için aşağıdaki değişiklikler geçerlidir:
- Çalışma alanı Yöneticisi **SYNAPSE Administrator** olarak yeniden adlandırıldı
- Apache Spark Yöneticisi, **Synapse Apache Spark Yöneticisi** olarak yeniden ADLANDıRıLıR ve SQL betikleri dahil tüm yayınlanan kod yapılarını görme iznine sahiptir.  Bu rol artık SYNAPSE Credential Kullanıcı rolünü gerektiren MSI çalışma alanını kullanma izni vermez.  Bu izin, işlem hatlarını çalıştırmak için gereklidir. 
- SQL Yöneticisi **SYNAPSE SQL Yöneticisi** olarak yeniden adlandırılır ve Spark Not defterleri ve işler dahil tüm yayınlanan kod yapılarını görme iznine sahiptir.  Bu rol artık SYNAPSE Credential Kullanıcı rolünü gerektiren MSI çalışma alanını kullanma izni vermez. Bu izin, işlem hatlarını çalıştırmak için gereklidir.
- **Yeni daha ayrıntılı SYNAPSE RBAC rolleri** , belirli analiz çalışma zamanları yerine, geliştirme ve operasyonlarının desteklemeye odaklanmaktadır.  
- **Yeni alt düzey kapsamlar** birkaç rol için tanıtılmıştır.  Bu kapsamlar, rollerin belirli kaynaklarla veya nesnelerle sınırlı olmasını sağlar.

>[!Note]
>**Yeni SYNAPSE RBAC rolleri ve alt düzey kapsamları Şu anda önizleme** aşamasındadır.  Bu yeni rolleri ve kapsamları kullanmanız önerilir. Bu roller, tam olarak desteklenir ve kullanımları hakkında geri bildirim sağlar.

## <a name="built-in-synapse-rbac-roles-and-scopes"></a>Yerleşik SYNAPSE RBAC rolleri ve kapsamları

Aşağıdaki tabloda, yerleşik roller ve kullanılabilecekleri kapsamlar açıklanmaktadır.

>[!Note]
> Herhangi bir kapsamda SYNAPSE RBAC rolüne sahip kullanıcılar, çalışma alanı kapsamında SYNAPSE Kullanıcı rolüne otomatik olarak sahiptir. 

|Rol |İzinler|Kapsamlar|
|---|---|-----|
|SYNAPSE Yöneticisi  |Sunucusuz SQL havuzlarına, Apache Spark havuzlarına ve tümleştirme çalışma zamanları için tam SYNAPSE erişimi.  Tüm yayımlanmış kod yapıtlarına oluşturma, okuma, güncelleştirme ve silme erişimi içerir.  Çalışma alanı sistem kimliği kimlik bilgilerinde Işlem operatörü, bağlantılı Veri Yöneticisi ve kimlik bilgileri Kullanıcı izinleri içerir.  SYNAPSE RBAC rollerinin atanmasını içerir. SYNAPSE Yöneticisi 'ne ek olarak, Azure sahipleri SYNAPSE RBAC rolleri de atayabilir. İşlem kaynaklarını oluşturmak, silmek ve yönetmek için Azure izinleri gereklidir. </br></br>_Yapıtlar okuma ve yazma, </br> Spark etkinliklerinde tüm eylemleri gerçekleştirebilir. </br> Spark havuz günlüklerini görüntüleyebilir </br> kaydedilen Not defteri ve işlem hattı çıkışı </br> , bağlı hizmetler tarafından depolanan gizli dizileri kullanabilir veya kimlik bilgileri, </br> SQL sunucusuz uç noktalara SQL `db_datareader` ,,, `db_datawriter` `connect` ve izinleri atayabilir ve bu,,, ve `grant` İzinler </br> geçerli kapsamda SYNAPSE RBAC rolleri atayabilir ve iptal edebilir_|Çalışma alanı </br> Spark havuzu<br/>Tümleştirme çalışma zamanı </br>Bağlı hizmet</br>Kimlik Bilgisi |
|SYNAPSE Apache Spark Yöneticisi</br>|Apache Spark havuzlara tam SYNAPSE erişimi.  Yayımlanmış Spark iş tanımları, Not defterleri ve çıkışları, kitaplıklar, bağlı hizmetler ve kimlik bilgileri için oluşturma, okuma, güncelleştirme ve silme erişimi.  Diğer tüm yayımlanmış kod yapıtlarına okuma erişimi içerir. Kimlik bilgilerini kullanma ve işlem hatlarını çalıştırma izni içermez. Erişim verme içermez. </br></br>_Spark yapıtları üzerinde tüm eylemlerin, </br> Spark etkinlikleri üzerinde tüm eylemleri gerçekleştirebilir_|Çalışma alanı</br>Spark havuzu|
|SYNAPSE SQL Yöneticisi|Sunucusuz SQL havuzlarına tam SYNAPSE erişimi.  Yayınlanan SQL betikleri, kimlik bilgileri ve bağlı hizmetlere erişimi oluşturun, okuyun, güncelleştirin ve silin.  Diğer tüm yayımlanmış kod yapıtlarına okuma erişimi içerir.  Kimlik bilgilerini kullanma ve işlem hatlarını çalıştırma izni içermez. Erişim verme içermez. </br></br>*SQL betiklerinde tüm eylemlerin SQL <br/> `db_datareader` , `db_datawriter` , `connect` , ve `grant` izinleriyle SQL sunucusuz uç noktalarına bağlanabilmesi için kullanılabilir*|Çalışma alanı|
|SYNAPSE Katılımcısı|Apache Spark havuzlarına ve tümleştirme çalışma zamanları için tam SYNAPSE erişimi. , Kimlik bilgileri ve bağlı hizmetler de dahil olmak üzere tüm yayınlanan kod yapıtları ve çıkışları için oluşturma, okuma, güncelleştirme ve silme erişimi içerir.  İşlem işletmeni izinleri içerir. Kimlik bilgilerini kullanma ve işlem hatlarını çalıştırma izni içermez. Erişim verme içermez. </br></br>_Okuma ve yazma yapıtları, </br> kaydedilen Not defteri ve işlem hattı çıkışının, </br> Spark etkinliklerindeki tüm eylemlerin </br> Spark havuz günlüklerini görüntüleyebileceği_|Çalışma alanı </br> Spark havuzu<br/> Tümleştirme çalışma zamanı|
|SYNAPSE yapıt yayımcısı|Yayınlanan kod yapıtları ve çıkışları için oluşturma, okuma, güncelleştirme ve silme erişimi. Kodu veya işlem hatlarını çalıştırma veya erişim verme iznini içermez. </br></br>_Yayınlanan yapıtları okuyabilir ve yayımlama yapıtları </br> , kaydedilen Not defteri, Spark işi ve işlem hattı çıkışını görüntüleyebilir_|Çalışma alanı
|SYNAPSE yapıt kullanıcısı|Yayınlanan kod yapıtları ve çıkışları için okuma erişimi. Yeni yapıtlar oluşturabilir, ancak ek izinler olmadan değişiklikleri yayımlayamaz veya kodu çalıştıramaz.|Çalışma alanı
|SYNAPSE Işlem Işleci |Spark işleri ve Not defterleri gönderme ve günlükleri görüntüleme.  Herhangi bir kullanıcı tarafından gönderilen Spark işlerinin iptal edilmesini içerir. İşlem hatlarını çalıştırmak için çalışma alanı sistem kimliğinde ek kullanım kimlik bilgisi izinleri gerektirir, işlem hattı çalıştırmalarını ve çıkışları görüntüleyin. </br></br>_Başkaları tarafından gönderilen işler de dahil olmak üzere işleri gönderebilir ve iptal edebilir. </br> Spark havuz günlüklerini görüntüleyebilir_|Çalışma alanı</br>Spark havuzu</br>Tümleştirme çalışma zamanı|
|SYNAPSE kimlik bilgisi kullanıcısı|Çalışma zamanı ve yapılandırma zamanı, işlem hattı çalıştırmaları gibi etkinliklerde kimlik bilgileri ve bağlı hizmetler içinde gizli dizi kullanımı. İşlem hatlarını çalıştırmak için, bu rol, çalışma alanı sistem kimliği kapsamında gereklidir. </br></br>_Bir kimlik bilgisi kapsamında, kimlik bilgileri ile korunan bağlı bir hizmet aracılığıyla verilere erişime izin verir (Ayrıca işlem kullanım izni gerektirir) </br> , çalışma alanı sistem kimliği kimlik bilgisi (ek işlem kullanımı izniyle) tarafından korunan işlem hatlarının yürütülmesine izin verir_|Çalışma alanı </br>Bağlı hizmet</br>Kimlik Bilgisi
|SYNAPSE bağlı Veri Yöneticisi|Yönetilen özel uç noktaların, bağlı hizmetlerin ve kimlik bilgilerinin oluşturulması ve yönetimi. , Kimlik bilgileriyle korunan bağlı hizmetleri kullanan yönetilen özel uç noktalar oluşturabilir|Çalışma alanı|
|SYNAPSE kullanıcısı|SQL havuzlarının, Apache Spark havuzlarının, tümleştirme çalışma zamanlarının ve yayımlanan bağlı hizmetlerin ve kimlik bilgilerinin ayrıntılarını listeleyin ve görüntüleyin. Diğer yayımlanmış kod yapıtları içermez.  Yeni yapıtlar oluşturabilir, ancak ek izinler olmadan çalıştırılamaz veya yayımlanamaz.</br></br>_Spark havuzlarını listeleyebilir ve okuyabilir, tümleştirme çalışma zamanları._|Çalışma alanı, Spark havuzu</br>Bağlı hizmet </br>Kimlik Bilgisi|

## <a name="synapse-rbac-roles-and-the-actions-they-permit"></a>SYNAPSE RBAC rolleri ve izin verdikleri eylemler

>[!Note]
>- Aşağıdaki tablolarda listelenen tüm eylemler ön eki, "Microsoft. SYNAPSE/..."</br>
>- Tüm yapıt okuma, yazma ve silme eylemleri, canlı hizmette yayımlanan yapıtlara göre yapılır.  Bu izinler bağlı bir git deposunda yapıtlara erişimi etkilemez.  

Aşağıdaki tabloda, yerleşik roller ve her biri tarafından desteklenen eylemler/izinler listelenmektedir.

Rol|Eylemler
--|--
SYNAPSE Yöneticisi|çalışma alanları/okuma</br>çalışma alanları/rol atamaları/yazma, silme</br>çalışma alanları/managedPrivateEndpoint/yazma, silme</br>çalışma alanları/bigDataPools/useCompute/Action</br>çalışma alanları/bigDataPools/viewLogs/Action</br>çalışma alanları/tümleştirme çalışma zamanları/useCompute/Action</br>çalışma alanları/yapıtlar/okuma</br>çalışma alanları/Not defterleri/yazma, silme</br>çalışma alanları/mini iş tanımları/yazma, silme</br>çalışma alanları/sqlScripts/Write, Sil</br>çalışma alanları/veri akışları/yazma, silme</br>çalışma alanları/işlem hatları/yazma, silme</br>çalışma alanları/Tetikleyiciler/yazma, silme</br>çalışma alanları/veri kümeleri/yazma, silme</br>çalışma alanları/kitaplıklar/yazma, silme</br>çalışma alanları/linkedServices/Write, Sil</br>çalışma alanları/kimlik bilgileri/yazma, silme</br>çalışma alanları/Not defterleri/görünüm çıktıları/eylem</br>çalışma alanları/işlem hatları/Viewçıktılar/eylem</br>çalışma alanları/linkedServices/useSecret/eylem</br>çalışma alanları/kimlik bilgileri/useSecret/eylem|
|SYNAPSE Apache Spark Yöneticisi|çalışma alanları/okuma</br>çalışma alanları/bigDataPools/useCompute/Action</br>çalışma alanları/bigDataPools/viewLogs/Action</br>çalışma alanları/Not defterleri/görünüm çıktıları/eylem</br>çalışma alanları/yapıtlar/okuma</br>çalışma alanları/Not defterleri/yazma, silme</br>çalışma alanları/mini iş tanımları/yazma, silme</br>çalışma alanları/kitaplıklar/yazma, silme</br>çalışma alanları/linkedServices/Write, Sil</br>çalışma alanları/kimlik bilgileri/yazma, silme|
|SYNAPSE SQL Yöneticisi|çalışma alanları/okuma</br>çalışma alanları/yapıtlar/okuma</br>çalışma alanları/sqlScripts/Write, Sil</br>çalışma alanları/linkedServices/Write, Sil</br>çalışma alanları/kimlik bilgileri/yazma, silme|
|SYNAPSE Katılımcısı|çalışma alanları/okuma</br>çalışma alanları/bigDataPools/useCompute/Action</br>çalışma alanları/bigDataPools/viewLogs/Action</br>çalışma alanları/tümleştirme çalışma zamanları/useCompute/Action</br>çalışma alanları/tümleştirme çalışma zamanları/viewLogs/eylem</br>çalışma alanları/yapıtlar/okuma</br>çalışma alanları/Not defterleri/yazma, silme</br>çalışma alanları/mini iş tanımları/yazma, silme</br>çalışma alanları/sqlScripts/Write, Sil</br>çalışma alanları/veri akışları/yazma, silme</br>çalışma alanları/işlem hatları/yazma, silme</br>çalışma alanları/Tetikleyiciler/yazma, silme</br>çalışma alanları/veri kümeleri/yazma, silme</br>çalışma alanları/kitaplıklar/yazma, silme</br>çalışma alanları/linkedServices/Write, Sil</br>çalışma alanları/kimlik bilgileri/yazma, silme</br>çalışma alanları/Not defterleri/görünüm çıktıları/eylem</br>çalışma alanları/işlem hatları/Viewçıktılar/eylem|
|SYNAPSE yapıt yayımcısı|çalışma alanları/okuma</br>çalışma alanları/yapıtlar/okuma</br>çalışma alanları/Not defterleri/yazma, silme</br>çalışma alanları/mini iş tanımları/yazma, silme</br>çalışma alanları/sqlScripts/Write, Sil</br>çalışma alanları/veri akışları/yazma, silme</br>çalışma alanları/işlem hatları/yazma, silme</br>çalışma alanları/Tetikleyiciler/yazma, silme</br>çalışma alanları/veri kümeleri/yazma, silme</br>çalışma alanları/kitaplıklar/yazma, silme</br>çalışma alanları/linkedServices/Write, Sil</br>çalışma alanları/kimlik bilgileri/yazma, silme</br>çalışma alanları/Not defterleri/görünüm çıktıları/eylem</br>çalışma alanları/işlem hatları/Viewçıktılar/eylem|
|SYNAPSE yapıt kullanıcısı|çalışma alanları/okuma</br>çalışma alanları/yapıtlar/okuma</br>çalışma alanları/Not defterleri/görünüm çıktıları/eylem</br>çalışma alanları/işlem hatları/Viewçıktılar/eylem|
|SYNAPSE Işlem Işleci |çalışma alanları/okuma</br>çalışma alanları/bigDataPools/useCompute/Action</br>çalışma alanları/bigDataPools/viewLogs/Action</br>çalışma alanları/tümleştirme çalışma zamanları/useCompute/Action</br>çalışma alanları/tümleştirme çalışma zamanları/viewLogs/eylem|
|SYNAPSE kimlik bilgisi kullanıcısı|çalışma alanları/okuma</br>çalışma alanları/linkedServices/useSecret/eylem</br>çalışma alanları/kimlik bilgileri/useSecret/eylem|
|SYNAPSE bağlı Veri Yöneticisi|çalışma alanları/okuma</br>çalışma alanları/managedPrivateEndpoint/yazma, silme</br>çalışma alanları/linkedServices/Write, Sil</br>çalışma alanları/kimlik bilgileri/yazma, silme|
|SYNAPSE kullanıcısı|çalışma alanları/okuma|

## <a name="synapse-rbac-actions-and-the-roles-that-permit-them"></a>SYNAPSE RBAC eylemleri ve bunlara izin veren roller

Aşağıdaki tabloda, bu eylemlere izin veren SYNAPSE eylemleri ve yerleşik roller listelenmektedir:

Eylem|Rol
--|--
çalışma alanları/okuma|SYNAPSE Yöneticisi</br>SYNAPSE Apache Spark Yöneticisi</br>SYNAPSE SQL Yöneticisi</br>SYNAPSE Katılımcısı</br>SYNAPSE yapıt yayımcısı</br>SYNAPSE yapıt kullanıcısı</br>SYNAPSE Işlem Işleci </br>SYNAPSE kimlik bilgisi kullanıcısı</br>SYNAPSE bağlı Veri Yöneticisi</br>SYNAPSE kullanıcısı 
çalışma alanları/rol atamaları/yazma, silme|SYNAPSE Yöneticisi
çalışma alanları/managedPrivateEndpoint/yazma, silme|SYNAPSE Yöneticisi</br>SYNAPSE bağlı Veri Yöneticisi
çalışma alanları/bigDataPools/useCompute/Action|SYNAPSE Yöneticisi</br>SYNAPSE Apache Spark Yöneticisi</br>SYNAPSE Katılımcısı</br>SYNAPSE Işlem Işleci 
çalışma alanları/bigDataPools/viewLogs/Action|SYNAPSE Yöneticisi</br>SYNAPSE Apache Spark Yöneticisi</br>SYNAPSE Katılımcısı</br>SYNAPSE Işlem Işleci 
çalışma alanları/tümleştirme çalışma zamanları/useCompute/Action|SYNAPSE Yöneticisi</br>SYNAPSE Katılımcısı</br>SYNAPSE Işlem Işleci 
çalışma alanları/yapıtlar/okuma|SYNAPSE Yöneticisi</br>SYNAPSE Apache Spark Yöneticisi</br>SYNAPSE SQL Yöneticisi</br>SYNAPSE Katılımcısı</br>SYNAPSE yapıt yayımcısı</br>SYNAPSE yapıt kullanıcısı
çalışma alanları/Not defterleri/yazma, silme|SYNAPSE Yöneticisi</br>SYNAPSE Apache Spark Yöneticisi</br>SYNAPSE Katılımcısı</br>SYNAPSE yapıt yayımcısı
çalışma alanları/mini iş tanımları/yazma, silme|SYNAPSE Yöneticisi</br>SYNAPSE Apache Spark Yöneticisi</br>SYNAPSE Katılımcısı</br>SYNAPSE yapıt yayımcısı
çalışma alanları/sqlScripts/Write, Sil|SYNAPSE Yöneticisi</br>SYNAPSE SQL Yöneticisi</br>SYNAPSE Katılımcısı</br>SYNAPSE yapıt yayımcısı
çalışma alanları/veri akışları/yazma, silme|SYNAPSE Yöneticisi</br>SYNAPSE Katılımcısı</br>SYNAPSE yapıt yayımcısı
çalışma alanları/işlem hatları/yazma, silme|SYNAPSE Yöneticisi</br>SYNAPSE Katılımcısı</br>SYNAPSE yapıt yayımcısı
çalışma alanları/Tetikleyiciler/yazma, silme|SYNAPSE Yöneticisi</br>SYNAPSE Katılımcısı</br>SYNAPSE yapıt yayımcısı
çalışma alanları/veri kümeleri/yazma, silme|SYNAPSE Yöneticisi</br>SYNAPSE Katılımcısı</br>SYNAPSE yapıt yayımcısı
çalışma alanları/kitaplıklar/yazma, silme|SYNAPSE Yöneticisi</br>SYNAPSE Apache Spark Yöneticisi</br>SYNAPSE Katılımcısı</br>SYNAPSE yapıt yayımcısı
çalışma alanları/linkedServices/Write, Sil|SYNAPSE Yöneticisi</br>SYNAPSE Katılımcısı</br>SYNAPSE yapıt yayımcısı</br>SYNAPSE bağlı Veri Yöneticisi
çalışma alanları/kimlik bilgileri/yazma, silme|SYNAPSE Yöneticisi</br>SYNAPSE Katılımcısı</br>SYNAPSE yapıt yayımcısı</br>SYNAPSE bağlı Veri Yöneticisi
çalışma alanları/Not defterleri/görünüm çıktıları/eylem|SYNAPSE Yöneticisi</br>SYNAPSE Apache Spark Yöneticisi</br>SYNAPSE Katılımcısı</br>SYNAPSE yapıt yayımcısı</br>SYNAPSE yapıt kullanıcısı
çalışma alanları/işlem hatları/Viewçıktılar/eylem|SYNAPSE Yöneticisi</br>SYNAPSE Katılımcısı</br>SYNAPSE yapıt yayımcısı</br>SYNAPSE yapıt kullanıcısı
çalışma alanları/linkedServices/useSecret/eylem|SYNAPSE Yöneticisi</br>SYNAPSE kimlik bilgisi kullanıcısı
çalışma alanları/kimlik bilgileri/useSecret/eylem|SYNAPSE Yöneticisi</br>SYNAPSE kimlik bilgisi kullanıcısı

## <a name="synapse-rbac-scopes-and-their-supported-roles"></a>SYNAPSE RBAC kapsamları ve desteklenen rolleri

Aşağıdaki tabloda, SYNAPSE RBAC kapsamları ve her kapsamda atanabilecek roller listelenmektedir. 

>[!note]
>Bir nesne oluşturmak veya silmek için, daha üst düzey bir kapsamda izinleriniz olmalıdır.

Kapsam|Roller
--|--
Çalışma alanı |SYNAPSE Yöneticisi</br>SYNAPSE Apache Spark Yöneticisi</br>SYNAPSE SQL Yöneticisi</br>SYNAPSE Katılımcısı</br>SYNAPSE yapıt yayımcısı</br>SYNAPSE yapıt kullanıcısı</br>SYNAPSE Işlem Işleci </br>SYNAPSE kimlik bilgisi kullanıcısı</br>SYNAPSE bağlı Veri Yöneticisi</br>SYNAPSE kullanıcısı
Apache Spark havuzu | SYNAPSE Yöneticisi </br>SYNAPSE Katılımcısı </br> SYNAPSE Işlem Işleci
Tümleştirme çalışma zamanı | SYNAPSE Yöneticisi </br>SYNAPSE Katılımcısı </br> SYNAPSE Işlem Işleci
Bağlı hizmet |SYNAPSE Yöneticisi </br>SYNAPSE kimlik bilgisi kullanıcısı
Kimlik Bilgisi |SYNAPSE Yöneticisi </br>SYNAPSE kimlik bilgisi kullanıcısı
 
>[!note]
>Tüm yapıt rolleri ve eylemleri, çalışma alanı düzeyinde kapsamlandırılır. 

## <a name="next-steps"></a>Sonraki adımlar

Bir çalışma alanı için [SYNAPSE RBAC rol atamalarını incelemeyi](./how-to-review-synapse-rbac-role-assignments.md) öğrenin.

[SYNAPSE RBAC rollerinin nasıl atanacağını](./how-to-manage-synapse-rbac-role-assignments.md) öğrenin
