---
title: Azure hizmet durumu bildirimleri nelerdir?
description: Hizmet durumu bildirimleri Microsoft Azure tarafından yayınlanan hizmet durumu iletilerini görüntülemenize izin verir.
author: stephbaron
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2018
ms.author: stbaron
ms.subservice: logs
ms.openlocfilehash: b41c2cdc54ab5eecdc4503cbd98e69932c901a3d
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007096"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Azure portalını kullanarak hizmet durumu bildirimlerini görüntüleme

Hizmet durumu bildirimleri Azure tarafından yayımlanır ve aboneliğinizdeki kaynaklarla ilgili bilgiler içerir. Bu bildirimler, etkinlik günlüğü olaylarının alt sınıfıdır ve etkinlik günlüğünde da bulunabilir. Hizmet durumu bildirimleri, sınıfa bağlı olarak bilgilendirici veya işlem yapılabilir olabilir.

Çeşitli hizmet durum bildirimleri sınıfları vardır:  

- **Eylem gerekiyor:** Azure, hesabınızda olağan dışı bir durum olduğunu fark edebilir ve bunu çözmek için sizinle birlikte çalışabilirsiniz. Azure size, gerçekleştirmeniz gereken eylemleri ayrıntılandıran veya Azure Mühendisliği ya da destek ile iletişim kurma hakkında bir bildirim gönderir.  
- **Olay:** Hizmeti etkileyen bir olay, şu anda aboneliğinizdeki bir veya daha fazla kaynağı etkiliyor.  
- **Bakım:** Aboneliğinizdeki bir veya daha fazla kaynağı etkileyebilecek planlı bir bakım etkinliği.  
- **Bilgi:** Kaynak kullanımını iyileştirmenize yardımcı olabilecek olası iyileştirmeler. 
- **Güvenlik:** Azure üzerinde çalışan çözümlerinizle ilgili acil güvenlikle ilgili bilgiler.

Her hizmet durumu bildirimi, kapsamlarınızla ilgili ayrıntıları ve kaynaklarınızın etkisini içerir. Ayrıntılar şunları içerir:

Özellik adı | Açıklama
-------- | -----------
lardan | Şu değerlerden biri: **yönetici** veya **işlem**.
correlationId | Genellikle dize biçimindeki bir GUID. Aynı eyleme ait olan olaylar genellikle aynı Bağıntıkimliği 'ı paylaşır.
Eventdataıd | Bir olayın benzersiz tanımlayıcısı.
eventName | Bir olayın başlığı.
level | Bir olayın düzeyi
resourceProviderName | Etkilenen kaynak için kaynak sağlayıcının adı.
resourceType| Etkilenen kaynağın kaynak türü.
Dosya | Genellikle karşılık gelen REST çağrısının HTTP durum kodu, ancak alt durumu tanımlayan diğer dizeleri de içerebilir. Örneğin: Tamam (HTTP durum kodu: 200), oluşturulan (http durum kodu: 201), kabul edildi (http durum kodu: 202), Içerik yok (http durum kodu: 204), bozuk Istek (http durum kodu: 400), bir durum bulunamadı (http durum kodu: 404), çakışma Hata (HTTP durum kodu: 500), hizmet kullanılamıyor (HTTP durum kodu: 503) ve ağ geçidi zaman aşımı (HTTP durum kodu: 504).
EventTimestamp | Olay, olaya karşılık gelen isteği işleyen Azure hizmeti tarafından oluşturulduğunda zaman damgası.
submissionTimestamp | Olay sorgulama için kullanılabilir hale geldiğinde zaman damgası.
subscriptionId | Bu olayın günlüğe kaydedildiği Azure aboneliği.
status | İşlemin durumunu açıklayan dize. Bazı ortak değerler şunlardır: **başlatıldı**, **devam ediyor**, **başarılı**, **başarısız**, **etkin**ve **çözümlendi**.
operationName | İşlemin adı.
category | Bu özellik her zaman **Servicehealth**' dır.
resourceId | Etkilenen kaynağın kaynak KIMLIĞI.
Properties. title | Bu iletişim için yerelleştirilmiş başlık. Varsayılan değer İngilizce 'dir.
Properties. Communication | HTML işaretlemesi ile iletişimin yerelleştirilmiş ayrıntıları. Varsayılan değer İngilizce 'dir.
Properties. incidentType | Şu değerlerden biri: **Actionrequired**, **bilgilendirici**, **olay**, **bakım**veya **güvenlik**.
Properties.trackingId | Bu olayın ilişkilendirildiği olay. Bir olayla ilgili olayları ilişkilendirmek için bunu kullanın.
Properties.impactedServices | Olaydan etkilenen hizmetleri ve bölgeleri açıklayan, kaçan JSON blobu. Özelliği, her birinin bir **ServiceName**'e sahip olduğu hizmetlerin bir listesini ve her birinin bir **BölgeAdı**olan etkilenen bölgelerin bir listesini içerir.
Properties. defaultLanguageTitle | Ingilizce olarak iletişim.
Properties. defaultLanguageContent | HTML biçimlendirme veya düz metin olarak Ingilizce iletişim.
Properties. aşama | **Olay**ve **güvenlik** için olası değerler **etkin,** **Çözümlenmiş** veya **RCA**. **Actionrequired** veya **bilgilendirme** için tek değer **etkindir.** **Bakım** Için: **etkin**, **planlı**, **sürüyor**, **iptal edildi**, yeniden **zamanlandı**, **çözümlendi**veya **tamamlanmıştır**.
Properties. Communicationıd | Bu olayın ilişkilendirildiği iletişim.

### <a name="details-on-service-health-level-information"></a>Hizmet durumu düzeyi bilgileri hakkındaki ayrıntılar

**Eylem gerekli** (Properties. incidentType = = actionrequired)
- Mevcut hizmetlere etkileri engellemek için bilgilendirici yönetici eylemi gereklidir.
    
**Bakım** (Properties. incidentType = = Maintenance)
- Uyarı-acil durum Bakımı
- Bilgilendirici standart planlı bakım

**Bilgi** (Properties. incidentType = = Information)
- Bilgilendirici yönetici, mevcut hizmetlere etkileri engellemek için gerekli olabilir.

**Güvenlik** (Properties. incidentType = = Security)
- Uyarı-mevcut hizmetleri etkileyen ve yönetici eylemi gerektirebilecek güvenlik danışmanlığı.
- Mevcut hizmetleri etkileyen bilgilendirici güvenlik danışmanlığı.

**Hizmet sorunları** (Özellikler. incidentType = = olay)
- Hata-birden çok bölge genelinde birden çok hizmete erişen yaygın sorunlar çok sayıda müşteriyi etkiliyor.
- Uyarı-belirli hizmetlere ve/veya belirli bölgelere erişme sorunları müşterilerin bir alt kümesini etkiliyor.
- Bilgilendirici-yönetim işlemlerini ve/veya gecikmeyi etkileyen, hizmet kullanılabilirliğini etkilemeden oluşan sorunlar.
