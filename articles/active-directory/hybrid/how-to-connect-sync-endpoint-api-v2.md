---
title: Azure AD Connect Sync v2 uç noktası | Microsoft Docs
description: Bu belge, Azure AD Connect Sync v2 uç noktaları API 'SI için güncelleştirmeleri içerir.
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4434b59044aed8c9814431864e5c3c9b7d98254c
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575726"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api"></a>Azure AD Connect eşitleme v2 uç nokta API’si 
Microsoft, Azure Active Directory için eşitleme hizmeti işlemlerinin performansını geliştiren Azure AD Connect için yeni bir uç nokta (API) dağıttı. Yeni v2 uç noktasından yararlanarak, Azure AD 'ye dışarı ve içeri aktarma sırasında dikkat çekici performans kazanımları yaşarsınız. Bu yeni uç nokta şunları destekler:
    
 - en fazla 250 k üye içeren grupları eşitleme
 - Azure AD 'ye verme ve içeri aktarma ile ilgili performans artışı
 
> [!NOTE]
> Şu anda, yeni uç noktanın geri yazılan Microsoft 365 grupları için yapılandırılmış bir Grup boyutu sınırı yok. Bu, Active Directory ve eşitleme döngüleriyle gecikme süreleriyle ilgili bir etkiye sahip olabilir. Grup boyutlarınızı artımlı olarak artırmanız önerilir.  

>[!NOTE]
> Azure AD Connect Sync v2 uç nokta API 'SI Şu anda yalnızca şu Azure ortamlarında kullanılabilir:
> - Azure ticari
> - Azure Çin bulutu
> - Azure ABD kamu bulutu Azure Almanya bulutu 'nda kullanıma sunulacaktır

## <a name="prerequisites"></a>Önkoşullar  
Yeni v2 uç noktasını kullanabilmek için, [Azure AD Connect sürüm 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) veya üstünü kullanmanız ve aşağıdaki dağıtım adımlarını izleyerek Azure AD Connect sunucunuz için v2 uç noktasını etkinleştirmeniz gerekir.   

## <a name="deployment-guidance"></a>Dağıtım Kılavuzu 
V2 uç noktasını kullanmak için [Azure AD Connect sürüm 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) veya üstünü dağıtmanız gerekir. İndirmek için belirtilen bağlantıyı kullanın. 

Ortamınızdaki yeni uç noktayı kullanıma almak için [esnek geçiş](./how-to-upgrade-previous-version.md#swing-migration) yöntemini izlemeniz önerilir. Bu, olayda önemli bir geri almanın gerekli olduğu açık bir acil durum planı sağlar. Aşağıdaki örnekte, bu senaryoda bir esnek geçişin nasıl kullanılabileceği gösterilmektedir. Esnek geçiş dağıtım yöntemi hakkında daha fazla bilgi için, belirtilen bağlantıya başvurun. 

### <a name="swing-migration-for-deploying-v2-endpoint"></a>V2 uç noktası dağıtmak için esnek geçiş geçişi
Aşağıdaki adımlar, v2 uç noktasını dağıtım sırasında esnek yöntemi kullanarak size rehberlik edecektir.

1. V2 uç noktasını geçerli hazırlama sunucusunda dağıtın. Bu sunucu, aşağıdaki adımlarda **v2 sunucusu** olarak bilinir. Geçerli etkin sunucu, aşağıdaki v1 uç noktası kullanılarak üretim iş yükünü işlemeye devam edecektir. Bu işlem, aşağıda **v1 sunucusu** olarak adlandırılır.
1. **V2 sunucusunun** içeri aktarmaları hala beklendiği gibi işlediğini doğrulayın. Bu aşamada, Azure AD 'de veya şirket içi AD 'de büyük gruplar sağlanmayacak, ancak yükseltmenin mevcut eşitleme işlemine yönelik başka bir beklenmedik etkiye neden olmadığını doğrulayabileceksiniz. 
2. Doğrulama tamamlandıktan sonra, **v2 sunucusunu** , hazırlama sunucusu olacak şekilde etkin sunucu ve **v1 sunucusu** olacak şekilde değiştirin. Şu anda, kapsamdaki kapsamdaki büyük grupların Azure AD 'ye sağlanması ve ayrıca grup geri yazma özelliği etkinse büyük Microsoft 365 Birleşik grupların AD 'ye sağlanması gerekir.
3. **V2 sunucusunun** , büyük grupları başarıyla gerçekleştirdiğinden ve işlediğini doğrulayın. Bu adımda kalmak ve bir dönem için eşitleme işlemini izlemek isteyebilirsiniz.
  >[!NOTE]
  > Önceki yapılandırmanıza geri geçiş yapmanız gerekirse, **v2 sunucusundan** **v1 sunucusuna** geri dönüşümlü geçiş yapabilirsiniz. V1 uç noktası 50.000 üyesi olan grupları desteklemediğinden, Azure AD 'de veya şirket içi ad 'de Azure AD Connect tarafından sağlanan tüm büyük gruplar daha sonra silinir. 
4. V2 uç noktasını kullanarak emin olduktan sonra, v2 uç noktasını kullanmaya başlamak için **v1 sunucusunu** yükseltin. 
 

## <a name="expectations-of-performance-impact"></a>Performans etkisi beklentileri  
V2 uç noktası kullanılırken, performans kazançları, eşitlenen grupların sayısı, bu grupların boyutu ve grup karmaşıklığı (Kullanıcı ekleme ve kaldırma işleminden kaynaklanan etkinlik) için bir işlevdir. Yeni uç nokta kullanılarak, eşitlenen grupların sayı, boyut veya dalgalanmasını arttırmadan, Azure AD 'ye dışarı aktarma ve içeri aktarma işlemi daha kısa sürelerle sonuçlanır. 
 
Ancak, performans artışı, büyük grupları eşitlerken gereken ek işlem tarafından alınabilir. Eşitleme işlemine çok fazla sayıda büyük grup ekleyerek genel eşitleme süresinin arttırmaya son vermek isteyebilirsiniz.  

Yeni grupların eklenmesi, eşitleme performansınızı nasıl etkileyeceğini daha iyi anlamak için, yalnızca 100 ' den az üye içeren birkaç büyük grubu eşitleyerek başlatmanız önerilir. Daha sonra grubun sayısını ve boyutunu, OU, öznitelik veya en büyük Grup boyutu filtrelemesine göre kapsamda daha fazlasını yaparak artırabilirsiniz. Performans iyileştirmeleri, şirket içi AD Bağlayıcısı değil, Azure AD Bağlayıcısı için içeri ve dışarı aktarma görevlerinde gerçekleştirilir. 

## <a name="deployment-step-by-step"></a>Adım adım dağıtım adımı 
Aşağıdaki üç aşama, yeni v2 uç noktasını dağıtmaya yönelik ayrıntılı bir örnektir.  Aşamaları, dağıtımınız için bir kılavuz olarak kullanın.

### <a name="phase-1--install-and-validate-azure-ad-connect"></a>1. aşama – Azure AD Connect yükleyip doğrulama 
İlk olarak [Azure AD Connect sürüm 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) veya üstünü yüklemek veya yükseltmek için adımları gerçekleştirmeniz ve v2 uç noktasını etkinleştireceğinizi ikinci aşamaya geçmeden önce eşitleme işlemini doğrulamanız önerilir. Azure AD Connect sunucusunda: 


1. Seçim Veritabanı yedeklemesini al 
2. [Azure AD Connect sürüm 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) veya sonraki bir sürüme yükler veya yükseltin.
3. Yüklemeyi doğrulama 

### <a name="phase-2--enable-the-v2-endpoint"></a>2. aşama – v2 uç noktasını etkinleştirme 
Sonraki adım v2 uç noktasını etkinleştirmektir. 

> [!NOTE]
> Sunucunuz için v2 uç noktasını etkinleştirdikten sonra, mevcut iş yükünüz için bazı performans iyileştirmeleri görebilirsiniz. Grupları yine de 50K üyelerine daha fazla eşitleme yapamazsınız. 

V2 uç noktasına geçmek için aşağıdaki adımları kullanın: 

1. Yönetici olarak bir PowerShell istemi açın. 
2. Hiçbir eşitleme işleminin çalışmadığını doğruladıktan sonra eşitleme zamanlayıcısını devre dışı bırakın: 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`
 
3. Yeni modülü içeri aktarın: 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 
 
4.  V2 uç noktasına geçin:

 `Set-ADSyncAADConnectorExportApiVersion 2` 
 
 `Set-ADSyncAADConnectorImportApiVersion 2` 

 ![PowerShell](media/how-to-connect-sync-endpoint-api-v2/endpoint1.png)
 
Artık sunucunuz için v2 uç noktasını etkinleştirdiniz. Grup boyutu sınırını artıracağınız bir sonraki aşamaya geçmeden önce v2 uç noktasını etkinleştirdikten sonra beklenmeyen bir sonuç olmadığını doğrulamak için biraz zaman alın. 
>[!NOTE]
>Dosya/modül yolları, Azure AD Connect yüklenirken girilen yükleme yoluna bağlı olarak farklı bir sürücü harfi kullanabilir. 


### <a name="phase-3--increase-the-group-membership-limit"></a>3. aşama – grup üyeliği sınırını artırma 
Hizmetin beklenmeyen sonuçlar olmadan çalıştığını doğruladıktan sonra, Grup üyeliği sınırını yükseltmek için devam edebilirsiniz. Öncelikle üyelik sınırının biraz daha yüksek bir değere, e g 'ye yükseltmeniz önerilir. Azure AD ile eşitlenen daha büyük grupları görmek için 75K üyeleri. Sonuçlardan memnun olduktan sonra, üye sınırını daha fazla yükseltebilirsiniz.  

Maksimum sınır, Grup başına 250K üyeleridir. 

Üyelik limitini artırmak için aşağıdaki adımlar kullanılabilir:  

1. Azure AD eşitleme kuralları düzenleyicisini açın 
2. Düzenleyicide, yön için **giden** ' ı seçin 
3. **AAD 'ye tıklayın – gruba JOIN** eşitleme kuralı 
4.  ![ "AAD-Group JOIN" ile "eşitleme kurallarınızı görüntüleme" seçeneğini gösteren Düzenle düğmesi ekran görüntüsüne tıklayın.](media/how-to-connect-sync-endpoint-api-v2/endpoint2.png)

6. Varsayılan kuralı devre dışı bırakmak ve düzenlenebilir bir kopya oluşturmak için **Evet** düğmesine tıklayın.
 !["Evet" düğmesinin seçili olduğu "ayrılmış kural onayını Düzenle" penceresini gösteren ekran görüntüsü.](media/how-to-connect-sync-endpoint-api-v2/endpoint3.png)

7. **Açıklama** sayfasındaki açılır pencerede, " ![ öncelik" vurgulanmış "giden eşitleme kuralını Düzenle" penceresini gösteren 1 ile 99 arasında bir değeri kullanılabilir bir değer olarak ayarlayın.](media/how-to-connect-sync-endpoint-api-v2/endpoint4.png)

8. **Dönüşümler** sayfasında, **üye** dönüştürmesi için **kaynak** değerini güncelleştirin, ' 50000 ' değerini 50001 ve 250000 arasında bir değer ile değiştirin. Bu değişiklik, Azure AD ile eşitlenecek grupların en büyük üyelik boyutunu artırır. Büyük grupları eşitlemenin, eşitleme performanızda sahip olacağı etkiyi anlamak için bir dizi 100k ile başlayıp başladık. 
 
 **Örnek** 
 
 `IIF((ValueCount("member")> 75000),Error("Maximum Group member count exceeded"),IgnoreThisFlow)` 
 
 ![Eşitleme kuralını Düzenle](media/how-to-connect-sync-endpoint-api-v2/endpoint5.png)

9. Kaydet’e tıklayın. 
10. Yönetici PowerShell komut istemi 'ni aç 
11. Eşitleme zamanlayıcısını yeniden etkinleştirin 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true` 
 
>[!NOTE]
> Azure AD Connect Health etkin değilse, Windows uygulaması olay günlüğü ayarlarını, günlükleri üzerine yazmak yerine, günlükleri arşivlemek üzere değiştirin. Günlükler, gelecekteki sorun giderme çabalarına yardımcı olmak için kullanılabilir. 

>[!NOTE]
> Yeni uç noktayı etkinleştirdikten sonra, AAD bağlayıcısında ' DN-Attributes-Failure ' adlı ek dışarı aktarma hataları görebilirsiniz. Kimliği 6949 olan her hata için karşılık gelen bir olay günlüğü girişi olacaktır. Hatalar bilgilendirme amaçlıdır ve yüklemenizde bir sorun göstermez, ancak üye nesnenin kendisi Azure AD ile eşitlenmediği için eşitleme işlemi, Azure AD 'deki bir gruba belirli üyeleri ekleyemeyebilir. 

Yeni v2 uç noktası kodu, bazı dışarı aktarma hatalarının türlerini v1 kodunun nasıl yaptığınızdan biraz farklı işler.  V2 uç noktasını kullandığınızda bilgilendirici hata iletileri hakkında daha fazla bilgi alabilirsiniz. 

>[!NOTE]
> Azure AD Connect yükseltirken, değişiklikler yükseltme işlemi aracılığıyla korunmadığından, Aşama 2 ' deki adımların yeniden çalıştığından emin olun. 

**AAD** 'de grup üyesi sınırına daha sonraki artışlar sırasında, bir tam eşitleme gerekli değildir, bu nedenle PowerShell 'de aşağıdaki komutu çalıştırarak tam eşitleme işlemini bastırmayı tercih edebilirsiniz. 

 `Set-ADSyncSchedulerConnectorOverride -FullSyncRequired $false -ConnectorName "<AAD Connector Name>" `
 
>[!NOTE]
> 50 ' den fazla üye içeren Microsoft 365 Birleşik gruplarınız varsa, gruplar Azure AD Connect okunacak ve grup geri yazma etkinse, şirket içi AD 'nize yazılır. 

## <a name="rollback"></a>Geri alma 
V2 uç noktasını etkinleştirdiyseniz ve geri almanız gerekiyorsa, şu adımları izleyin: 

1. Azure AD Connect sunucusu: a. Seçim Veritabanı yedeklemesini al 
2. Bir yönetici PowerShell istemi açın:
3. Hiçbir eşitleme işleminin çalışmadığını doğruladıktan sonra eşitleme zamanlayıcısını devre dışı bırakın
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`

4. V1 uç noktasına geç * 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 

 `Set-ADSyncAADConnectorExportApiVersion 1`

 `Set-ADSyncAADConnectorImportApiVersion 1`
 
5. Azure AD eşitleme kuralları düzenleyicisini açın 
6. Dışarı AAD 'ye ait düzenlenebilir kopyasını silme **– gruba katılmayı** eşitleme kuralı 
7. AAD 'ye ait varsayılan kopyayı **-Group 'A Birleştir** eşitleme kuralını etkinleştir 
8. Yönetici PowerShell istemi aç 
9. Eşitleme zamanlayıcısını yeniden etkinleştirin 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true`
 
>[!NOTE]
> V2 'den v1 uç noktalarına geri geçiş yaparken, 50 ' den fazla üye ile eşitlenen gruplar, Azure AD 'ye sağlanan ve AD için sağlanan Birleşik grupları Microsoft 365 her iki AD grubu için tam eşitleme çalıştırıldıktan sonra silinir. 

## <a name="frequently-asked-questions"></a>Sık sorulan sorular  
 
**Yeni bitiş noktası, yükseltmeler ve yeni yüklemeler için varsayılan olarak ne zaman olur?**  
</br>Şubat 2021 ' de karşıdan yüklenmek üzere yayımlanacak AADConnect 'in yeni bir sürümünü planlıyoruz. Bu sürüm, varsayılan olarak v2 uç noktasını kullanır ve ek yapılandırma olmadan 50K 'den büyük olan eşitleme gruplarını etkinleştirir. Bu sürüm daha sonra uygun sunuculara otomatik olarak yükseltme için yayımlanacak.
 
## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD Connect eşitleme: eşitlemeyi anlama ve özelleştirme](how-to-connect-sync-whatis.md)
* [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md)
