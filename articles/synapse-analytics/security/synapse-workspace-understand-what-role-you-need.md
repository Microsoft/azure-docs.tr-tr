---
title: SYNAPSE 'de ortak görevleri gerçekleştirmek için gereken rolleri anlayın
description: Bu makalede, belirli görevleri gerçekleştirmek için hangi yerleşik SYNAPSE RBAC rollerinin gerekli olduğu açıklanır
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 950a786b29a8144c4bb192fa6078e8c88d67481d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384461"
---
# <a name="understand-the-roles-required-to-perform-common-tasks-in-synapse"></a>SYNAPSE 'de ortak görevleri gerçekleştirmek için gereken rolleri anlayın

Bu makale, SYNAPSE Studio 'da yapılması gereken SYNAPSE RBAC (rol tabanlı erişim denetimi) veya Azure RBAC rollerinin ne olduğunu anlamanıza yardımcı olur.  

## <a name="synapse-studio-access-control-and-workflow-summary"></a>SYNAPSE Studio erişim denetimi ve iş akışı Özeti 

### <a name="accessing-synapse-studio-and-viewing-its-content"></a>SYNAPSE Studio 'Ya erişme ve içeriğini görüntüleme

- Herhangi bir Synapse RBAC rolü atandıysa veya çalışma alanında Azure sahibi, katkıda bulunan veya okuyucu rolüne sahipseniz, SYNAPSE Studio 'Yu açabilir ve çalışma alanının ayrıntılarını görüntüleyebilir ve Azure kaynaklarından (SQL havuzları, Spark havuzları veya tümleştirme çalışma zamanları) herhangi birini listeleyebilirsiniz.

### <a name="resource-management"></a>Kaynak yönetimi

- Çalışma alanında bir Azure sahibiyseniz veya katkıda bulunsanız, SQL havuzları, Apache Spark havuzları ve tümleştirme çalışma zamanları oluşturabilirsiniz.
- Özel bir SQL havuzunu duraklatabilir veya ölçeklendirebilirsiniz, bir Azure sahibiyseniz veya bir tümleştirme çalışma zamanı yapılandırabilir veya çalışma alanında ya da kaynak üzerinde katkıda bulunmanız gerekir.

### <a name="viewing-and-editing-code-artifacts"></a>Kod yapılarını görüntüleme ve düzenlemeyle

- SYNAPSE Studio erişimi sayesinde SQL betikleri, Not defterleri, Spark işleri, bağlı hizmetler, işlem hatları, veri akışları, Tetikleyiciler ve kimlik bilgileri gibi yeni kod yapıtları oluşturabilirsiniz.  (Bu yapıtlar, ek izinlerle yayımlanabilir veya kaydedilebilir.)  
- SYNAPSE yapıtı bir Kullanıcı, SYNAPSE yapıt yayımcısı, SYNAPSE katılımcısı veya SYNAPSE Yöneticisi kullanıyorsanız, önceden yayınlanmış kod yapılarını listeleyebilir, açabilir ve düzenleyebilirsiniz.

### <a name="executing-your-code"></a>Kodunuzu yürütme

- SQL havuzlarında gerekli SQL izinleriniz varsa SQL havuzları üzerinde SQL betikleri çalıştırabilirsiniz.  
- Çalışma alanında veya belirli Apache Spark havuzlarda SYNAPSE Işlem Işletmeni izinleriniz varsa, not defterlerini ve Spark işlerini çalıştırabilirsiniz.  
- Çalışma alanı veya belirli tümleştirme çalışma zamanları üzerinde Işlem Işletmeni izinleri ve uygun kimlik bilgileri izinleri ile Işlem hatlarını çalıştırabilirsiniz.

### <a name="monitoring-and-managing-execution"></a>Yürütmeyi izleme ve yönetme
- SYNAPSE bir Kullanıcı kullanıyorsanız, Apache Spark havuzlarında çalışan Not defterlerinin ve işlerin durumunu inceleyebilirsiniz.
- Çalışma alanında veya belirli bir Spark havuzu ya da Işlem hattı için SYNAPSE Işlem operatörü kullanıyorsanız, günlükleri gözden geçirebilir ve çalışan işleri ve işlem hatlarını iptal edebilirsiniz.  

### <a name="publishing-and-saving-your-code"></a>Kodunuzu yayımlama ve kaydetme

- SYNAPSE yapıt yayımcısı, SYNAPSE katılımcısı veya SYNAPSE yöneticisiyseniz, hizmette yeni veya güncelleştirilmiş kod yapıtları yayımlayabilirsiniz. 
- Çalışma alanı git etkinse ve git izinleriniz varsa, git deposunun çalışma dalına kod yapıtlarını kaydedebilirsiniz. Git etkin olduğunda, yalnızca işbirliği dalından yayımlamaya izin verilir.
- Kod yapıtlarına yapılan değişiklikleri yayımlamadan veya işlemeden SYNAPSE Studio 'Yu kapatırsanız, bu değişiklikler kaybedilir.


## <a name="tasks-and-required-roles"></a>Görevler ve gerekli roller

Aşağıdaki tabloda, ortak görevler ve her görev, SYNAPSE RBAC veya Azure RBAC rolleri listelenmektedir.  

>[!Note]
>- Gerekli izni sağlayan tek rol olmadığı takdirde, SYNAPSE Yöneticisi her bir görev için listelenmez.  SYNAPSE Yöneticisi, diğer SYNAPSE RBAC rolleri tarafından etkinleştirilen tüm görevleri gerçekleştirebilir.</br>
>- Gerekli en düşük SYNAPSE RBAC rolü gösteriliyor.
>- Herhangi bir kapsamdaki tüm SYNAPSE RBAC rolleri, çalışma alanında SYNAPSE Kullanıcı izinleri sağlar
>- Tabloda gösterilen tüm SYNAPSE RBAC izinleri/eylemleri önekli Microsoft/SYNAPSE/Workspaces/... </br>


Görev (istiyorum...) |Rol (yapmam gerekiyor...)|SYNAPSE RBAC izni/eylemi
--|--|--
|Çalışma alanında SYNAPSE Studio 'Yu açma|SYNAPSE kullanıcısı veya|okuma
| |Çalışma alanında Azure sahibi, katkıda bulunan veya okuyucu|yok
|SQL havuzlarını, Apache Spark havuzlarını, tümleştirme çalışma zamanlarını listeleyin ve yapılandırma ayrıntılarına erişin|SYNAPSE kullanıcısı veya|okuma|
||Çalışma alanında Azure sahibi, katkıda bulunan veya okuyucu|yok
|Bağlı hizmetleri, kimlik bilgilerini, yönetilen özel uç noktaları listeleyin|SYNAPSE kullanıcısı|okuma
SQL HAVUZLARı|
Adanmış bir SQL havuzu veya sunucusuz SQL havuzu oluşturma|Çalışma alanında Azure sahibi veya katkıda bulunan|yok
Adanmış bir SQL havuzunu yönetme (duraklatma, ölçekleme veya silme)|SQL havuzunda veya çalışma alanında Azure sahibi veya katkıda bulunan|yok
SQL betiği oluşturma</br>|SYNAPSE kullanıcısı veya </br>Çalışma alanında Azure sahibi veya katkıda bulunan </br>*BIR SQL betiği çalıştırmak, yayımlamak veya değişiklikleri yürütmek Için ek SQL izinleri gerekir*.|
Yayınlanan tüm SQL betiğini listeleyin ve açın| SYNAPSE yapıt kullanıcısı, yapıt yayımcısı, SYNAPSE katkıda bulunan|yapıtlar/okuma
Sunucusuz SQL havuzunda SQL betiği çalıştırma|Havuzda SQL izinleri (bir Synapse yöneticisine otomatik olarak verilir)|yok
Adanmış bir SQL havuzunda SQL betiği çalıştırma|Havuzda SQL izinleri|yok
Yeni, güncelleştirilmiş veya silinmiş bir SQL betiği yayımlama|SYNAPSE yapıt yayımcısı, SYNAPSE katkıda bulunan|sqlScripts/Write, Sil
Değişiklikleri bir SQL betiğine git deposuna Kaydet|Depoda git izinleri gerekir|
Çalışma alanına yönetici Active Directory atama (Azure portalındaki çalışma alanı özellikleri aracılığıyla)|Çalışma alanında Azure sahibi veya katkıda bulunan |
APACHE SPARK HAVUZLARı|
Apache Spark havuzu oluşturma|Çalışma alanında Azure sahibi veya katkıda bulunan|
Apache Spark uygulamalarını izleme| SYNAPSE kullanıcısı|okuma
Not defteri ve iş yürütmeye yönelik günlükleri görüntüleme |SYNAPSE Işlem Işleci|
Apache Spark havuzunda çalışan herhangi bir not defteri veya Spark işini iptal etme|Apache Spark havuzunda SYNAPSE Işlem Işletmeni.|bigDataPools/useCompute
Bir not defteri veya iş tanımı oluşturun|SYNAPSE kullanıcısı veya </br>Çalışma alanında Azure sahibi, katkıda bulunan veya okuyucu</br> *Değişiklikleri çalıştırmak, yayımlamak veya uygulamak için ek izinler gerekir*|okuma</br></br></br></br></br> 
Kaydedilmiş çıktıları gözden geçirme dahil yayımlanmış bir not defteri veya iş tanımı listeleyin ve açın|Çalışma alanında SYNAPSE yapıt kullanıcısı, SYNAPSE yapıt yayımcısı, SYNAPSE katkıda bulunan|yapıtlar/okuma
Bir not defteri çalıştırın ve çıktısını gözden geçirin ya da bir Spark işi iletin|SYNAPSE Apache Spark Administrator, SYNAPSE COMPUTE Işleci seçili Apache Spark havuzunda|bigDataPools/useCompute 
Hizmete bir not defteri veya iş tanımı (çıktı dahil) yayımlama veya silme|Çalışma alanında yapıt yayımcısı, SYNAPSE Apache Spark Yöneticisi|Not defterleri/yazma, silme
Değişiklikleri bir not defterine veya iş tanımına git deposuna Kaydet|Git izinleri|yok
IŞLEM HATLARı, TÜMLEŞTIRME ÇALıŞMA ZAMANLARı, VERI AKıŞLARı, VERI KÜMELERI & TETIKLEYICILERI|
Tümleştirme çalışma zamanı oluşturma, güncelleştirme veya silme|Çalışma alanında Azure sahibi veya katkıda bulunan|
Tümleştirme çalışma zamanı durumunu izleme|SYNAPSE kullanıcısı|okuma, işlem hatları/Viewçıktılar
İşlem hattı çalıştırmalarını gözden geçirme|SYNAPSE yapıt yayımcısı/SYNAPSE katkıda bulunan|okuma, işlem hatları/Viewçıktılar 
İşlem hattı oluşturma |SYNAPSE kullanıcısı</br>*Hata ayıklamak, Tetikleyiciler eklemek, yayımlamak veya değişiklikleri kaydetmek için ek SYNAPSE izinleri gerekir*|okuma
Veri akışı veya veri kümesi oluşturma |SYNAPSE kullanıcısı</br>*Değişiklikleri yayınlamak veya kaydetmek için ek SYNAPSE izinleri gerekir*|okuma
Yayımlanmış bir işlem hattını listeleme ve açma |SYNAPSE yapıt kullanıcısı | yapıtlar/okuma
Veri kümesi verilerini Önizle|SYNAPSE Kullanıcı + SYNAPSE kimlik bilgisi kullanıcısı WorkspaceSystemIdentity| 
Varsayılan tümleştirme çalışma zamanını kullanarak bir işlem hattının hatalarını ayıklama|WorkspaceSystemIdentity kimlik bilgilerinde Kullanıcı + SYNAPSE kimlik bilgisi kullanıcısı SYNAPSE|okuyamaz </br>kimlik bilgileri/useSecret
Şimdi tetikleyici dahil olmak üzere bir tetikleyici oluşturun (işlem hattını yürütmek için izin gerekir)|SYNAPSE Kullanıcı + SYNAPSE kimlik bilgisi kullanıcısı WorkspaceSystemIdentity|okuma, kimlik bilgileri/useSecret/eylem
İşlem hattını yürütme/çalıştırma|SYNAPSE Kullanıcı + SYNAPSE kimlik bilgisi kullanıcısı WorkspaceSystemIdentity|okuma, kimlik bilgileri/useSecret/eylem
Veri Kopyalama aracını kullanarak veri kopyalama|SYNAPSE Kullanıcı + SYNAPSE kimlik bilgileri Kullanıcı çalışma alanı sistem kimliği|okuma, kimlik bilgileri/useSecret/eylem
Veri alma (bir zamanlama kullanarak)|SYNAPSE Author + SYNAPSE, çalışma alanı sistem kimliği üzerinde Kullanıcı kimlik bilgileri|okuma, kimlik bilgileri/useSecret/eylem
Yeni, güncelleştirilmiş veya silinmiş bir işlem hattını, veri akışını veya tetikleyiciyi hizmete yayımlayın|Çalışma alanında SYNAPSE yapıt yayımcısı|işlem hatları/yazma, silme</br>veri akışları/yazma, silme</br>Tetikleyiciler/yazma, silme
İşlem hatlarına, veri akışlarına, veri kümelerine veya tetikleyicilere değişiklikleri git deposuna Kaydet |Git izinleri|yok 
BAĞLı HIZMETLER|
Bağlı hizmet oluşturma (kimlik bilgisi atamayı içerir)|SYNAPSE kullanıcısı</br>*Bağlı bir hizmeti kimlik bilgileriyle kullanmak veya değişiklikleri yayınlamak veya kaydetmek için ek izinler gerekir*|okuma
Yayınlanan bağlı hizmeti listeleyin ve açın|SYNAPSE yapıt kullanıcısı|linkedServices/Write, Sil  
Bir kimlik bilgisi ile korunan bağlı bir hizmette test bağlantısı|SYNAPSE User + SYNAPSE kimlik bilgisi kullanıcısı|kimlik bilgileri/useSecret/eylem|
Bağlı hizmet yayımlama|SYNAPSE yapıt yayımcısı, SYNAPSE bağlı Veri Yöneticisi|linkedServices/Write, Sil
Bağlı hizmet tanımlarını git deposuna işleme|Git izinleri|yok
ERIŞIM YÖNETIMI|
Herhangi bir kapsamdaki SYNAPSE RBAC rol atamalarını gözden geçirin|SYNAPSE kullanıcısı|okuma
Kullanıcılar, gruplar ve hizmet sorumluları için SYNAPSE RBAC rol atamaları atama ve kaldırma| Çalışma alanında veya belirli bir çalışma alanı öğesi kapsamında SYNAPSE Yöneticisi|Roleatamalar/yazma, silme 

>[!Note]
>Başka bir kiracıya ait Konuk kullanıcılar atanan rolden bağımsız olarak rol atamalarını gözden geçiremez, ekleyemez veya değiştiremezler. 

## <a name="next-steps"></a>Sonraki adımlar

[SYNAPSE RBAC rol atamalarını incelemeyi](./how-to-review-synapse-rbac-role-assignments.md) öğrenin

[SYNAPSE RBAC rol atamalarını yönetmeyi](./how-to-manage-synapse-rbac-role-assignments.md)öğrenin. 
