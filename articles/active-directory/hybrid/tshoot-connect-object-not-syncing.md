---
title: Azure Active Directory ile eşitlenen bir nesnenin sorunlarını giderme | Microsoft Docs '
description: Azure Active Directory ile eşitlenen bir nesne için sorun giderin.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a0c8a42edad08308095469039c048f8dd8552af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94413471"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-with-azure-active-directory"></a>Azure Active Directory ile eşitlenmeyen bir nesneyle ilgili sorunları giderme

Bir nesne Microsoft Azure Active Directory (Azure AD) ile beklendiği gibi eşitlenmiyorsa, bunun nedeni birkaç nedenden kaynaklanabilir. Azure AD 'den bir hata e-postası aldıysanız veya Azure AD Connect Health hatayı görürseniz, bunun yerine [eşitleme sırasında sorun giderme hatalarını](tshoot-connect-sync-errors.md) okuyun. Ancak nesnenin Azure AD 'de olmadığı bir sorunu giderirken, bu makale size yöneliktir. Şirket içi bileşen Azure AD Connect eşitlemede hataların nasıl bulunacağını açıklar.

>[!IMPORTANT]
>Sürüm 1.1.749.0 veya üzeri bir dağıtım Azure AD Connect için, nesne eşitleme sorunlarını gidermek için sihirbazda [sorun giderme görevini](tshoot-connect-objectsync.md) kullanın. 

## <a name="synchronization-process"></a>Eşitleme işlemi

Eşitleme sorunlarını araştırmadan önce Azure AD Connect eşitleme işlemini anlayabiliriz:

  ![Azure AD Connect eşitleme işleminin diyagramı](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terimler**

* **CS:** Bir veritabanındaki bir tablo olan bağlayıcı alanı
* **MV:** Veritabanındaki bir tablo olan metaverse

### <a name="synchronization-steps"></a>**Eşitleme adımları**
Eşitleme işlemi aşağıdaki adımları içerir:

1. **Ad 'Den Içeri Aktar:** Active Directory nesneler Active Directory CS 'ye getirilir.

2. **Azure AD 'Den Içeri Aktar:** Azure AD nesneleri Azure AD CS 'ye getirilir.

3. **Eşitleme:** Gelen eşitleme kuralları ve giden eşitleme kuralları, daha düşük olan öncelik sayısı sırasına göre çalıştırılır. Eşitleme kurallarını görüntülemek için Masaüstü uygulamalarından eşitleme kuralları Düzenleyicisi 'ne gidin. Gelen eşitleme kuralları CS 'den MV 'ye veri getirir. Giden eşitleme kuralları, verileri MV 'dan CS 'ye taşır.

4. **Ad 'ye Aktar:** Eşitlemeden sonra, nesneler CS Active Directory Active Directory ' e aktarılabilir.

5. **Azure AD 'ye aktarma:** Eşitlemeden sonra, nesneler Azure AD CS 'den Azure AD 'ye aktarılabilir.

## <a name="troubleshooting"></a>Sorun giderme

Hataları bulmak için aşağıdaki sırada birkaç farklı yere bakın:

1. İşlem, içeri aktarma ve eşitleme sırasında eşitleme altyapısı tarafından tanımlanan hataları bulmak için [günlüğe kaydedilir](#operations) .
2. Eksik nesneleri ve eşitleme hatalarını bulmak için [bağlayıcı alanı](#connector-space-object-properties) .
3. Verilerle ilgili sorunları bulmak için [meta veri deposu](#metaverse-object-properties) .

Bu adımlara başlamadan önce [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) başlatın.

## <a name="operations"></a>Operations
Synchronization Service Manager **işlemler** sekmesi, sorun gidermeyi başlatmanız gereken yerdir. Bu sekme, en son işlemlerin sonuçlarını gösterir. 

![Synchronization Service Manager ekran görüntüsü, Işlemler sekmesinin seçili olduğu gösteriliyor](./media/tshoot-connect-object-not-syncing/operations.png)  

**İşlemler** sekmesinin üst yarısında tüm çalıştırmalar kronolojik sırayla gösterilir. Varsayılan olarak, işlem günlüğü son yedi güne ilişkin bilgileri tutar, ancak bu ayar [Zamanlayıcı](how-to-connect-sync-feature-scheduler.md)ile değiştirilebilir. **Başarı** durumu gösterolmayan herhangi bir çalıştırmaya bakın. Üst bilgiye tıklayarak sıralamayı değiştirebilirsiniz.

**Durum** sütunu en önemli bilgileri içerir ve bir çalıştırma için en ciddi sorunu gösterir. Aşağıda, araştırma önceliği sırasına göre en yaygın durumların hızlı bir özeti verilmiştir (burada * olası hata dizelerini gösterir).

| Durum | Yorum |
| --- | --- |
| durduruldu-* |Çalıştırma tamamlanamadı. Bu durum, örneğin uzak sistem kapalıysa ve iletişim kurulamıyorsa olabilir. |
| durduruldu-hata-limit |5.000 'den fazla hata var. Çok sayıda hata nedeniyle çalıştırma otomatik olarak durduruldu. |
| tamamlandı- \* -hatalar |Çalıştırma tamamlandı, ancak Araştırılması gereken hatalar (5.000 'den az). |
| tamamlandı- \* -Uyarılar |Çalıştırma tamamlandı, ancak bazı veriler beklenen durumda değil. Hatalar varsa, bu ileti genellikle yalnızca bir belirtidir. Hatalar giderene kadar uyarıları araştırmayın. |
| başarılı |Sorun yok. |

Bir satır seçtiğinizde, **işlemler** sekmesinin en alt kısmı bu çalıştırmanın ayrıntılarını gösterecek şekilde güncelleştirilir. Bu alanın en sol tarafında, **adım #** başlıklı bir listeniz olabilir. Bu liste yalnızca Ormanınızda birden fazla etki alanınız varsa ve her etki alanı bir adımla temsil edildiğinde görüntülenir. Etki alanı adı, başlık **bölümünün** altında bulunabilir. **Eşitleme istatistikleri** başlığı altında, işlenen değişiklik sayısı hakkında daha fazla bilgi edinebilirsiniz. Değiştirilen nesnelerin listesini almak için bağlantıları seçin. Hatalar içeren nesneler varsa, bu hatalar **Eşitleme hataları** başlığının altında görünür.

### <a name="errors-on-the-operations-tab"></a>Işlemler sekmesinde hatalar
Hatalar olduğunda Synchronization Service Manager hem hata içindeki nesneyi hem de hatayı daha fazla bilgi sağlayan bağlantılar olarak gösterir.

![Synchronization Service Manager hataların ekran görüntüsü](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Hata dizesini seçerek başlayın. (Önceki şekilde, hata dizesi **eşitleme-kural-hatası-işlev-tetiklendi**.) Önce nesneye bir genel bakış sunulur. Gerçek hatayı görmek için **yığın izlemesi**' ni seçin. Bu izleme hata için hata ayıklama düzeyi bilgileri sağlar.

**Çağrı yığını bilgilerini** sağ tıklatın, **Tümünü Seç**' e tıklayın ve ardından **Kopyala**' yı seçin. Ardından, yığını kopyalayın ve Not Defteri gibi en sevdiğiniz düzenleyicide hataya bakın.

Hata **SyncRulesEngine**' den ise, çağrı yığını bilgileri önce nesnedeki tüm öznitelikleri listeler. **InnerException =>** başlığını görene kadar aşağı kaydırın.  

  ![InnerException = başlığı altındaki hata bilgilerini gösteren Synchronization Service Manager ekran görüntüsü>](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)
  
Başlıktan sonraki satır hatayı gösterir. Yukarıdaki şekilde, hata Fabrikam tarafından oluşturulan özel bir eşitleme kuralından.

Hata yeterli bilgi vermeirse, verilerin kendisine bakmayı zaman atalım. Nesne tanımlayıcısıyla bağlantıyı seçin ve [bağlayıcı alanı içeri aktarılan nesne](#cs-import)sorunlarını gidermeye devam edin.

## <a name="connector-space-object-properties"></a>Bağlayıcı alanı nesne özellikleri
[**İşlemler**](#operations) sekmesinde hata yoksa, Azure AD 'ye Active Directory bağlayıcı alanı nesnesini meta veri deposuna izleyin. Bu yolda, sorunun nerede olduğunu bulmanız gerekir.

### <a name="searching-for-an-object-in-the-cs"></a>CS 'de nesne arama

Synchronization Service Manager, **Bağlayıcılar**' ı seçin, Active Directory bağlayıcısını seçin ve **bağlayıcı alanını ara**' yı seçin.

**Kapsam** kutusunda, CN özniteliğinde aramak istediğinizde **RDN** ' yi seçin veya **distinguishedName 'dir** özniteliğinde aramak istediğinizde **DN veya tutturucu** ' ı seçin. Bir değer girin ve **Ara**' yı seçin. 
 
![Bağlayıcı alanı aramasının ekran görüntüsü](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Aradığınız nesneyi bulamazsanız, [etki alanı tabanlı filtreleme](how-to-connect-sync-configure-filtering.md#domain-based-filtering) veya [OU tabanlı filtrelemeyle](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)filtrelenmiş olabilir. Filtrelemenin beklenen şekilde yapılandırıldığını doğrulamak için [Azure AD Connect eşitleme: filtrelemeyi yapılandırma](how-to-connect-sync-configure-filtering.md)' yı okuyun.

Azure AD bağlayıcısını seçerek başka bir faydalı arama gerçekleştirebilirsiniz. **Kapsam** kutusunda, **bekleyen içeri aktar**' ı seçin ve ardından **Ekle** onay kutusunu seçin. Bu arama, Azure AD 'de bulunan ve şirket içi bir nesneyle ilişkilendirilebilen tüm eşitlenmiş nesneleri sağlar.  

![Bir bağlayıcı alanı aramasında artık çalışma görüntüsü](./media/tshoot-connect-object-not-syncing/cssearchorphan.png) 
 
Bu nesneler başka bir eşitleme altyapısı veya farklı filtreleme yapılandırmasına sahip bir eşitleme altyapısı tarafından oluşturulmuştur. Bu artık nesneler artık yönetilmez. Bu listeyi gözden geçirin ve [Azure AD PowerShell](/previous-versions/azure/jj151815(v=azure.100)) cmdlet 'lerini kullanarak bu nesneleri kaldırmayı göz önünde bulundurun.

### <a name="cs-import"></a>CS içeri aktarma
Bir CS nesnesi açtığınızda, en üstte birkaç sekme vardır. **İçeri** aktarma sekmesi, bir içeri aktarma işleminden sonra hazırlanan verileri gösterir.  

![Bağlayıcı alanı nesnesinin, Içeri aktarma sekmesi seçiliyken Özellikler penceresi ekran görüntüsü](./media/tshoot-connect-object-not-syncing/csobject.png)    

**Eski değer** sütunu, şu anda Connect 'te nelerin depolandığını gösterir ve **Yeni değer** sütunu kaynak sistemden ne alındığını gösterir ve henüz uygulanmadı. Nesnede bir hata varsa değişiklikler işlenmez.

**Eşitleme hatası** sekmesi, yalnızca nesneyle ilgili bir sorun olduğunda **bağlayıcı alanı nesne özellikleri** penceresinde görünür. Daha fazla bilgi için, [ **işlemler** sekmesinde eşitleme hatalarıyla ilgili sorunları giderme](#errors-on-the-operations-tab)konusunu gözden geçirin.

![Bağlayıcı alanı nesnesindeki eşitleme hatası sekmesinin ekran görüntüsü Özellikler penceresi](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  

### <a name="cs-lineage"></a>CS kökenini
**Bağlayıcı alanı nesne özellikleri** penceresindeki **kökenini** sekmesi, bağlayıcı alanı nesnesinin meta veri deposu nesnesiyle nasıl ilişkili olduğunu gösterir. Bağlayıcının bağlı sistemden bir değişikliği ne zaman içeri aktardığına ve meta veri deposundaki verileri doldurmak için hangi kuralların uygulanacağını görebilirsiniz.  

![Bağlayıcı alanı nesnesindeki kökenini sekmesini gösteren ekran görüntüsü Özellikler penceresi](./media/tshoot-connect-object-not-syncing/cslineage.png)  

Yukarıdaki şekilde, **eylem** sütununda eylem **sağlaması** olan bir gelen eşitleme kuralı gösterilmektedir. Bu, bu bağlayıcı alanı nesnesi mevcut olduğu sürece meta veri deposu nesnesi kalır. Bunun yerine eşitleme kuralları listesi, bir **sağlama** eylemiyle giden eşitleme kuralı gösteriyorsa, meta veri deposu nesnesi silindiğinde bu nesne silinir.  

![Bağlayıcı alanı nesnesindeki kökenini sekmesinde bir kökenini penceresinin ekran görüntüsü Özellikler penceresi](./media/tshoot-connect-object-not-syncing/cslineageout.png)  

Yukarıdaki şekilde, bir eşitleme kuralı **true** değerine sahip olduğundan, **PasswordSync** sütununda gelen bağlayıcı alanının parola üzerinde değişikliklere katkıda bulunabileceğini de görebilirsiniz. Bu parola, giden kuralı aracılığıyla Azure AD 'ye gönderilir.

**Kökenini** sekmesinden [**Metadize nesne özelliklerini**](#mv-attributes)seçerek meta veri deposuna ulaşabilirsiniz.

### <a name="preview"></a>Önizleme
**Bağlayıcı alanı nesne özellikleri** penceresinin sol alt köşesinde **Önizleme** düğmesi bulunur. Tek bir nesneyi eşitleyebileceğiniz **Önizleme** sayfasını açmak için bu düğmeyi seçin. Bu sayfa, bazı özel eşitleme kurallarında sorun giderirken ve bir değişikliğin tek bir nesne üzerinde etkisini görmek istediğinizde yararlıdır. **Tam eşitleme** veya **Delta eşitlemesi** seçebilirsiniz. Ayrıca, yalnızca bellekteki değişikliği tutan **Önizleme Oluştur** seçeneğini de belirleyebilirsiniz. Ya da meta veri deposu güncelleyen **yürütme önizlemesi** seçin ve hedef bağlayıcı alanlarında tüm değişiklikleri aşamalar.  

![Önizleme sayfasının başlangıç önizlemesi seçiliyken ekran görüntüsü](./media/tshoot-connect-object-not-syncing/preview.png)  

Önizlemede, nesneyi inceleyebilir ve belirli bir öznitelik akışı için hangi kuralın uygulanacağını görebilirsiniz.  

![Içeri aktarma öznitelik akışını gösteren önizleme sayfasının ekran görüntüsü](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Günlük
**Önizleme** **düğmesinin yanındaki günlük düğmesini seçerek** **günlük** sayfasını açın. Burada parola eşitleme durumunu ve geçmişini görebilirsiniz. Daha fazla bilgi için bkz. [Azure AD Connect Sync ile parola karması eşitleme sorunlarını giderme](tshoot-connect-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>Meta veri deposu nesne özellikleri
Kaynak Active Directory bağlayıcı alanından aramaya başlamak genellikle daha iyidir. Ancak, meta veri deposundaki aramayı da başlatabilirsiniz.

### <a name="searching-for-an-object-in-the-mv"></a>MV 'da nesne arama
Synchronization Service Manager, aşağıdaki şekilde **Meta dize araması**' nı seçin. Kullanıcıyı bulduğunu bildiğiniz bir sorgu oluşturun. **AccountName** (**sAMAccountName**) ve **userPrincipalName** gibi ortak öznitelikleri arayın. Daha fazla bilgi için bkz. [Sync Service Manager Metadize Search](how-to-connect-sync-service-manager-ui-mvsearch.md).

![Metadize arama sekmesi seçiliyken Synchronization Service Manager ekran görüntüsü](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

**Arama sonuçları** penceresinde nesnesine tıklayın.

Nesneyi bulamazsanız, meta veri deposuna henüz ulaşılamadı. Active Directory [bağlayıcı alanında](#connector-space-object-properties)nesneyi aramaya devam edin. Nesneyi Active Directory bağlayıcı alanında bulursanız, nesnenin meta veri deposuna geldiğini engelleyen bir eşitleme hatası olabilir veya bir eşitleme kuralı kapsam filtresi uygulanabilir.

### <a name="object-not-found-in-the-mv"></a>Nesne MV 'da bulunamadı
Nesne Active Directory CS ise ve MV 'da yoksa, bir kapsam filtresi uygulanır. Kapsam filtresine bakmak için masaüstü uygulaması menüsüne gidin ve **eşitleme kuralları Düzenleyicisi**' ni seçin. Aşağıdaki filtreyi ayarlayarak nesnesine uygulanabilir kuralları filtreleyin.

  ![Bir gelen eşitleme kuralları aramasını gösteren eşitleme kuralları Düzenleyicisi 'nin ekran görüntüsü](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

Yukarıdaki listedeki her bir kuralı görüntüleyin ve **kapsam filtresini** denetleyin. Aşağıdaki kapsam filtresinde, **Iscriticalhandle** değeri null veya boş ya da boş ise kapsam içinde olur.

  ![Gelen eşitleme kuralı aramasında kapsam filtresinin ekran görüntüsü](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

[CS Içeri aktarma](#cs-import) öznitelik listesine gidin ve nesnenin MV 'a taşınmasını engelleyen filtreyi kontrol edin. **Bağlayıcı alanı** öznitelik listesi yalnızca null olmayan ve boş olmayan öznitelikleri gösterir. Örneğin, **Iskritiksystemobject** listede görünmüyorsa, bu özniteliğin değeri null veya boş olur.

### <a name="object-not-found-in-the-azure-ad-cs"></a>Azure AD CS 'de nesne bulunamadı
Nesne, Azure AD 'nin bağlayıcı alanında yoksa, ancak MV 'de varsa, ilgili bağlayıcı alanının giden kurallarının kapsam filtresini inceleyin ve [MV öznitelikleri](#mv-attributes) ölçütlere uygun olmadığından nesnenin filtreleyip filtrelenmediğini öğrenin.

Giden kapsam filtresine bakmak için aşağıdaki filtreyi ayarlayarak nesnenin uygulanabilir kurallarını seçin. Her kuralı görüntüleyin ve karşılık gelen [MV özniteliği](#mv-attributes) değerine bakın.

  ![Eşitleme kuralları düzenleyicisinde arama giden eşitleme kurallarının ekran görüntüsü](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>MV öznitelikleri
**Öznitelikler** sekmesinde, bu değerleri ve bunlara katkıda bulunan bağlayıcıları görebilirsiniz.  

![Meta veri deposu nesnesinin, öznitelikler sekmesi seçili Özellikler penceresi ekran görüntüsü](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Bir nesne eşitleniyorsa, meta veri deposundaki öznitelik durumları hakkında aşağıdaki soruları sorun.
- **Cloudfıltered** özniteliği var ve **true** olarak ayarlandı mı? Varsa, [öznitelik tabanlı filtrelemede](how-to-connect-sync-configure-filtering.md#attribute-based-filtering)adımlara göre filtrelenmiştir.
- Öznitelik **Sourcetutturucu** var mı? Aksi takdirde, bir hesap-kaynak orman topolojisi mi var? Bir nesne, bağlantılı bir posta kutusu olarak tanımlanmışsa ( **msExchRecipientTypeDetails** özelliği **2**' dir), **sourcetutturucu** , etkin bir Active Directory hesabıyla ormana katkıda bulunur. Ana hesabın içeri aktarıldığından ve doğru eşitlendiğinden emin olun. Ana hesap, nesne [bağlayıcıları](#mv-connectors) arasında listelenmiş olmalıdır.

### <a name="mv-connectors"></a>MV bağlayıcıları
**Bağlayıcılar** sekmesi, nesnesinin temsili olan tüm bağlayıcı alanlarını gösterir. 
 
![Özellikler penceresi, meta veri deposu nesnesinin, bağlayıcılar sekmesi seçili olan ekran görüntüsü](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  

Şunları yapmak için bir Bağlayıcınız olmalıdır:

- Kullanıcının temsil ettiği her bir orman Active Directory. Bu gösterim, **foreignSecurityPrincipals** ve **ilgili kişi** nesneleri içerebilir.
- Azure AD 'de bağlayıcı.

Bağlayıcıyı Azure AD 'ye kaçırdıysanız, Azure AD 'ye hazırlama ölçütlerini doğrulamak için [MV özniteliklerinin](#mv-attributes) bölümüne bakın.

**Bağlayıcılar** sekmesinden [bağlayıcı alanı nesnesine](#connector-space-object-properties)de gidebilirsiniz. Bir satır seçin ve **Özellikler**' e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar
- [Azure AD Connect eşitleme](how-to-connect-sync-whatis.md)hakkında daha fazla bilgi edinin.
- [Karma kimlik](whatis-hybrid-identity.md)hakkında daha fazla bilgi edinin.