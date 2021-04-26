---
title: "Azure AD Connect: DirSync'ten yükseltme | Microsoft Belgeleri"
description: DirSync'ten Azure AD Connect'e nasıl yükseltme yapılacağı konusunda bilgi edinin. Bu makalede DirSync'ten Azure AD Connect'e yükseltmeye yönelik adımlar açıklanmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: baf52da7-76a8-44c9-8e72-33245790001c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8aa45294de4ef644c20ef66b7163706dca9759d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996534"
---
# <a name="azure-ad-connect-upgrade-from-dirsync"></a>Azure AD Connect: DirSync'ten yükseltme
Azure AD Connect, DirSync'in yerini almıştır. Bu konu başlığı altında DirSync'ten yükseltme yöntemlerini bulabilirsiniz. Bu adımlar, Azure AD Connect'in başka bir sürümünden veya Azure AD Eşitleme'den yapılacak yükseltmeler için geçerli değildir.

Azure AD Connect'i yüklemeye başlamadan önce [Azure AD Connect'i indirdiğinizden](https://go.microsoft.com/fwlink/?LinkId=615771) ve [Azure AD Connect: Donanım ve önkoşullar](how-to-connect-install-prerequisites.md) bölümündeki önkoşul adımlarını tamamladığınızdan emin olun. Bu alanlar DirSync’ten farklı olduğu için özellikle aşağıdakiler hakkında bilgi edinmek isteyebilirsiniz:

* Gerekli .NET ve PowerShell sürümü. Sunucuda, DirSync’in gerektirdiğinden daha yeni sürümlerin bulunması gerekir.
* Ara sunucu yapılandırması. İnternet’e erişmek için bir ara sunucu kullanıyorsanız yükseltmeden önce bu ayarın yapılandırılması gerekir. DirSync her zaman programı yükleyen kullanıcı için yapılandırılan ara sunucuyu kullanıyordu, ancak Azure AD Connect bunun yerine makine ayarlarını kullanır.
* URL’lerin ara sunucuda açılması gerekir. DirSync tarafından da desteklenen temel senaryolar için gereksinimler aynıdır. Azure AD Connect’in içerdiği yeni özelliklerden herhangi birini kullanmak istiyorsanız bazı yeni URL’lerin açılması gerekir.

> [!NOTE]
> Azure AD 'de yapılan değişiklikleri eşitlemeye başlamak için yeni Azure AD Connect sunucunuzu etkinleştirdikten sonra DirSync veya Azure AD Eşitleme kullanmaya geri alma yapmanız gerekir. DirSync ve Azure AD Eşitleme de dahil olmak üzere Azure AD Connect eski istemcilere düşürümcmesi desteklenmez ve Azure AD 'de veri kaybı gibi sorunlara yol açabilir.

DirSync'ten yükseltmiyorsanız diğer senaryolar için ilgili belgelere göz atın.

## <a name="upgrade-from-dirsync"></a>DirSync'ten yükseltme
Geçerli DirSync dağıtımınıza bağlı olarak farklı yükseltme seçenekleri mevcuttur. Tahmini yükseltme süresi üç saatten azsa yerinde yükseltme yapılması önerilir. Tahmini yükseltme süresi üç saatten fazlaysa başka bir sunucu üzerinde paralel dağıtım yapılması önerilir. 50.000'den fazla nesneniz varsa yükseltmenin üç saatten fazla süreceği tahmin edilir.

| Senaryo |
| --- |
| [Yerinde yükseltme](#in-place-upgrade) |
| [Paralel dağıtım](#parallel-deployment) |

> [!NOTE]
> DirSync'ten Azure AD Connect'e yükseltmeyi planlıyorsanız yükseltmeden önce DirSync'i kendiniz kaldırmayın. Azure AD Connect, DirSync'ten yapılandırmayı okuyup geçişini yapar ve sunucuyu denetledikten sonra kaldırma işlemini gerçekleştirir.

**Yerinde yükseltme**  
Yükseltmenin tamamlanmasına ilişkin tahmini süre sihirbaz tarafından görüntülenir. Bu tahmin, yükseltme işleminin 50.000 nesne (kullanıcılar, kişiler ve gruplar) içeren bir veritabanı için üç saatte tamamlanacağı varsayımına dayanır. Veritabanınızdaki nesnelerin sayısı 50.000’den azsa Azure AD Connect yerinde yükseltme yapılmasını önerir. Devam etmek isterseniz yükseltme sırasında geçerli ayarlarınız otomatik olarak uygulanır ve sunucunuz etkin eşitlemeyi otomatik olarak sürdürür.

Yapılandırma geçişi ve paralel dağıtım yapmak istiyorsanız yerinde yükseltme önerisini geçersiz kılabilirsiniz. Örneğin, donanım ve işletim sistemini yenileme olanağından faydalanabilirsiniz. Daha fazla bilgi için [paralel dağıtım](#parallel-deployment) bölümüne bakın.

**Paralel dağıtım**  
50.000'den fazla nesneniz varsa paralel dağıtım seçeneğini kullanmanız önerilir. Bu dağıtım, kullanıcılarınızın işletimsel gecikme yaşamasını önler. Azure AD Connect yüklemesi, yükseltme için kesinti süresini tahmin etmeye çalışır ancak DirSync'i daha önce yükselttiyseniz kendi deneyiminizin sizin için en iyi kılavuz olacağını söyleyebiliriz.

### <a name="supported-dirsync-configurations-to-be-upgraded"></a>Yükseltilecek olan desteklenen DirSync yapılandırmaları
Yükseltilmiş DirSync ile şu yapılandırma değişiklikleri desteklenir:

* Etki alanı ve OU filtreleme
* Alternatif kimlik (UPN)
* Parola eşitleme ve Exchange karma ayarları
* Ormanınız/etki alanınız ve Azure AD ayarlarınız
* Kullanıcı özniteliği tabanlı filtreleme

Aşağıdaki değişiklik yükseltilemez. Bu yapılandırmaya sahipseniz yükseltme engellenir:

* Kaldırılan öznitelikler ve özel uzantı DLL'si kullanmak gibi desteklenmeyen DirSync değişiklikleri

![Yükseltme engellendi](./media/how-to-dirsync-upgrade-get-started/analysisblocked.png)

Bu durumlarda, [hazırlama modunda](how-to-connect-sync-staging-server.md) yeni bir Azure AD Connect sunucusu yüklemek ve eski DirSync ve yeni Azure AD Connect yapılandırmasını doğrulamak için öneri önerilir. [Azure AD Connect Eşitleme özel yapılandırmasında](how-to-connect-sync-whatis.md) tanımlanan şekilde, özel yapılandırmayı kullanarak yapılan tüm değişiklikleri yeniden uygulayın.

DirSync tarafından hizmet hesapları için kullanılan parolalar alınamaz ve geçirilmez. Bu parolalar yükseltme sırasında sıfırlanır.

### <a name="high-level-steps-for-upgrading-from-dirsync-to-azure-ad-connect"></a>DirSync'ten Azure AD Connect'e yükseltme için üst düzey adımlar
1. Azure AD Connect'e Hoş Geldiniz
2. Geçerli DirSync yapılandırmasının analizi
3. Azure AD genel yönetici parolası toplama
4. Kuruluş yöneticisi hesabı için kimlik bilgileri toplama (yalnızca Azure AD Connect yüklemesi sırasında kullanılır)
5. Azure AD Connect yüklemesi
   * DirSync’i kaldırma (veya geçici olarak devre dışı bırakma)
   * Azure AD Connect'i yükleme
   * Eşitlemeyi isteğe bağlı olarak başlatma

Ek adımların gerekli olduğu durumlar:

* Tam SQL Server (yerel veya uzak) kullanıyor olmanız
* Eşitleme kapsamında 50.000'den fazla nesnenizin olması

## <a name="in-place-upgrade"></a>Yerinde yükseltme
1. Azure AD Connect yükleyicisini (MSI) başlatın.
2. Lisans koşulları ve gizlilik bildirimini gözden geçirin ve kabul edin.  
   ![Azure AD'ye Hoş Geldiniz](./media/how-to-dirsync-upgrade-get-started/Welcome.png)
3. Mevcut DirSync yüklemenizin analizine başlamak için İleri'ye tıklayın.  
   ![Mevcut Directory Sync yüklemesini analiz etme](./media/how-to-dirsync-upgrade-get-started/Analyze.png)
4. Analiz tamamlandığında nasıl devam edeceğinize ilişkin öneriler görürsünüz.  
   * SQL Server Express kullanıyorsanız ve 50.000'den az nesneniz varsa aşağıdaki ekran gösterilir:  
     ![Analiz tamamlandı, DirSync'ten yükseltme yapmaya hazırsınız](./media/how-to-dirsync-upgrade-get-started/AnalysisReady.png)
   * DirSync için tam SQL Sunucusu kullanıyorsanız bu paketi görürsünüz:  
     ![kullanılmakta olan SQL veritabanı sunucusunu gösteren ekran görüntüsü.](./media/how-to-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)  
     DirSync tarafından kullanılan mevcut SQL Server veritabanı sunucusuyla ilgili bilgiler görüntülenir. Gerekirse uygun ayarlamaları yapın. Yüklemeye devam etmek için **İleri**'ye tıklayın.
   * 50.000’den fazla nesneniz varsa şu ekranı görürsünüz:  
     ![Yükseltmek için 50.000 ' den fazla nesneniz olduğunda gördüğünüz ekranı gösteren ekran görüntüsü.](./media/how-to-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)  
     Yerinde yükseltme işlemiyle devam etmek için şu iletinin yanındaki onay kutusuna tıklayın: **Bu bilgisayarda DirSync'i yükseltmeye devam edin.**
     Bunun yerine [paralel dağıtım](#parallel-deployment) yapmak için DirSync yapılandırma ayarlarını dışarı aktarın ve yapılandırmayı yeni sunucuya taşıyın.
5. Şu anda Azure AD'ye bağlanmak için kullandığınız hesabın parolasını belirtin. Bu hesabın, şu anda DirSync tarafından kullanılan hesap olması gerekir.  
   ![Azure AD kimlik bilgilerinizi girin](./media/how-to-dirsync-upgrade-get-started/ConnectToAzureAD.png)  
   Bir hatayla karşılaştıysanız ve bağlantı sorunlarınız varsa bkz. [Bağlantı sorunlarını giderme](tshoot-connect-connectivity.md).
6. Active Directory için bir kuruluş yöneticisi hesabı sağlayın.  
   ![ADDS kimlik bilgilerinizi girin](./media/how-to-dirsync-upgrade-get-started/ConnectToADDS.png)
7. Artık yapılandırma için hazırsınız. **Yükselt**'e tıkladığınızda DirSync kaldırılır, Azure AD Connect yapılandırılır ve eşitleme başlar.  
   ![Yapılandırma için hazır](./media/how-to-dirsync-upgrade-get-started/ReadyToConfigure.png)
8. Yükleme tamamlandıktan sonra Synchronization Service Manager'ı ve Synchronization Rule Editor'ı kullanmadan veya başka bir yapılandırma değişikliği yapmadan önce Windows oturumunuzu kapatıp tekrar açın.

## <a name="parallel-deployment"></a>Paralel dağıtım
### <a name="export-the-dirsync-configuration"></a>DirSync yapılandırmasını dışarı aktarma
**50.000'den fazla nesneyle paralel dağıtım**

50.000'den fazla nesneniz varsa Azure AD Connect yüklemesi tarafından paralel dağıtım seçeneğini kullanmanız önerilir.

Şuna benzer bir ekran görüntülenir:  
![Analiz tamamlandı](./media/how-to-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

Paralel dağıtım ile devam etmek istiyorsanız şu adımları tamamlamanız gerekir:

* **Ayarları dışarı aktar** düğmesine tıklayın. Azure AD Connect'i ayrı bir sunucuya yüklediğinizde bu ayarlar, geçerli DirSync hesabınızdan yeni Azure AD Connect yüklemenize geçirilir.

Ayarlarınız başarıyla dışarı aktarıldıktan sonra DirSync sunucusundaki Azure AD Connect sihirbazından çıkabilirsiniz. Azure AD Connect'i ayrı bir sunucuya yüklemek için bir sonraki adımla devam edin

**50.000'den az nesneyle paralel dağıtım**

50.000'den az nesneniz varsa ancak yine de paralel dağıtım yapmak istiyorsanız şunları yapın:

1. Azure AD Connect yükleyicisini (MSI) çalıştırın.
2. **Azure AD Connect'e Hoş Geldiniz** ekranını gördüğünüzde, pencerenin sağ üst köşesindeki "X" işaretine tıklayarak yükleme sihirbazından çıkın.
3. Bir komut istemi açın.
4. Azure AD Connect yükleme konumundan (Varsayılan: C:\Program Files\Microsoft Azure Active Directory Connect) şu komutu yürütün:  `AzureADConnect.exe /ForceExport`.
5. **Ayarları dışarı aktar** düğmesine tıklayın. Azure AD Connect'i ayrı bir sunucuya yüklediğinizde bu ayarlar, geçerli DirSync hesabınızdan yeni Azure AD Connect yüklemenize geçirilir.

![Ayarlarınızı yeni Azure AD Connect yüklemesine geçirmeye yönelik ayarları dışarı aktarma seçeneğini gösteren ekran görüntüsü.](./media/how-to-dirsync-upgrade-get-started/forceexport.png)

Ayarlarınız başarıyla dışarı aktarıldıktan sonra DirSync sunucusundaki Azure AD Connect sihirbazından çıkabilirsiniz. Azure AD Connect ayrı bir sunucuya yüklemek için bir sonraki adımla devam edin.

### <a name="install-azure-ad-connect-on-separate-server"></a>Azure AD Connect'i ayrı bir sunucuya yükleme
Azure AD Connect'i yeni bir sunucuya yüklediğinizde, Azure AD Connect’i temiz bir şekilde yüklemek istediğiniz varsayılır. DirSync yapılandırmasını kullanmak istiyorsanız tamamlamanız gereken bazı ek adımlar vardır:

1. Azure AD Connect yükleyicisini (MSI) çalıştırın.
2. **Azure AD Connect'e Hoş Geldiniz** ekranını gördüğünüzde, pencerenin sağ üst köşesindeki "X" işaretine tıklayarak yükleme sihirbazından çıkın.
3. Bir komut istemi açın.
4. Azure AD Connect yükleme konumundan (Varsayılan: C:\Program Files\Microsoft Azure Active Directory Connect) şu komutu yürütün: `AzureADConnect.exe /migrate`.
   Azure AD Connect yükleme sihirbazı başlar ve şu ekranla karşılaşırsınız:  
   ![Yükselttiğinizde, ayarlar dosyasının nereye içeri aktarılacağını gösteren ekran görüntüsü.](./media/how-to-dirsync-upgrade-get-started/ImportSettings.png)
5. DirSync yüklemesinden dışarı aktarılan ayarlar dosyasını seçin.
6. Şunlar dahil olmak üzere tüm gelişmiş seçenekleri yapılandırın:
   * Azure AD Connect için özel bir yükleme konumu.
   * Mevcut bir SQL Server örneği (Varsayılan: Azure AD Connect, SQL Server 2012 Express'i yükler). DirSync sunucunuzla aynı veritabanını kullanmayın.
   * SQL Server'a bağlanmak için kullanılan hizmet hesabı. (SQL Server veritabanınız uzak ise bu hesabın etki alanı hizmet hesabı olması gerekir.)
     Bu seçenekler şu ekranda görülebilir:  
     ![DirSync 'ten yükseltmeye yönelik gelişmiş yapılandırma seçeneklerini gösteren ekran görüntüsü.](./media/how-to-dirsync-upgrade-get-started/advancedsettings.png)
7. **İleri**’ye tıklayın.
8. **Yapılandırma için hazır** sayfasında, **Start the synchronization process as soon as configuration completes (Yapılandırma tamamlanınca eşitlemeyi başlat)** seçeneğini işaretli olarak bırakın. Sunucu şu an [hazırlama modunda](how-to-connect-sync-staging-server.md) olduğundan değişiklikler Azure AD’ye dışarı aktarılmaz.
9. **Yükle**'ye tıklayın.
10. Yükleme tamamlandıktan sonra Synchronization Service Manager'ı ve Synchronization Rule Editor'ı kullanmadan veya başka bir yapılandırma değişikliği yapmadan önce Windows oturumunuzu kapatıp tekrar açın.

> [!NOTE]
> Windows Server Active Directory ile Azure Active Directory arasında eşitleme başlar, ancak hiçbir değişiklik Azure AD'ye dışarı aktarılmaz. Aynı anda yalnızca bir eşitleme aracı değişiklikleri etkin olarak dışarı aktarabilir. Bu durum, [hazırlama modu](how-to-connect-sync-staging-server.md) olarak adlandırılır.

### <a name="verify-that-azure-ad-connect-is-ready-to-begin-synchronization"></a>Azure AD Connect'in eşitlemeye başlamak için hazır olduğunu doğrulama
Azure AD Connect'in DirSync'ten devralma işleminin hazır olduğunu doğrulamak için başlat menüsünden **Azure AD Connect** grubundaki **Synchronization Service Manager**'ı açmanız gerekir.

Uygulamada, **işlemler** sekmesine gidin. Bu sekmede, aşağıdaki işlemlerin tamamlandığını onaylayın:

* AD Bağlayıcısı üzerinde içeri aktarma
* Azure AD Bağlayıcısı üzerinde içeri aktarma
* AD Bağlayıcısı üzerinde tam eşitleme
* Azure AD Bağlayıcısı üzerinde tam eşitleme

![İçeri aktarma ve Eşitleme tamamlandı](./media/how-to-dirsync-upgrade-get-started/importsynccompleted.png)

Bu işlemlerin sonucunu gözden geçirin ve herhangi bir hata olmadığından emin olun.

Azure AD'ye aktarılacak olan değişiklikleri görmek ve incelemek isterseniz [hazırlama modu](how-to-connect-sync-staging-server.md) bölümüne giderek yapılandırmanın nasıl doğrulanacağı hakkında bilgi edinin. Beklenmeyen bir durumla karşılaşmadığınız sürece gerekli yapılandırma değişikliklerini yapın.

Bu adımları tamamladıysanız ve sonuçtan memnunsanız DirSync’ten Azure AD’ye geçmeye hazırsınız demektir.

### <a name="uninstall-dirsync-old-server"></a>DirSync'i (eski sunucuyu) kaldırma
* **Programlar ve özellikler**’de **Microsoft Azure Active Directory eşitleme aracını** bulun
* **Microsoft Azure Active Directory eşitleme aracını** kaldırma
* Kaldırma işlemi 15 dakika kadar sürebilir.

DirSync’i daha sonra kaldırmak isterseniz sunucuyu geçici olarak kapatabilir veya hizmeti devre dışı bırakabilirsiniz. Yanlış giden bir şey olursa bu yöntem, hizmeti yeniden etkinleştirmenize izin verir. Ancak sonraki adımın başarısız olması beklenmediğinden buna ihtiyacınız olmayabilir.

DirSync kaldırıldığında veya devre dışı bırakıldığında Azure AD’ye dışarı aktarım gerçekleştiren hiç etkin sunucu kalmaz. Şirket içi Active Directory'nizdeki değişiklikler Azure AD ile eşitlenmeye devam etmeden önce Azure AD’yi etkinleştirmeye yönelik bir sonraki adımın tamamlanması gerekir.

### <a name="enable-azure-ad-connect-new-server"></a>Azure AD Connect'i (yeni sunucu) etkinleştirme
Yükleme sonrasında Azure AD Connect'i yeniden açarak ek yapılandırma değişiklikleri gerçekleştirebilirsiniz. **Azure AD Connect**'i başlat menüsünden veya masaüstündeki kısayoldan başlatın. MSI yüklemesini tekrar çalıştırmayı denemeyin.

Şunları görmeniz gerekir:  
![Ek görevler](./media/how-to-dirsync-upgrade-get-started/AdditionalTasks.png)

* **Hazırlama modunu yapılandır** seçeneğini belirleyin.
* **Hazırlama modunu etkinleştir** onay kutusunun işaretini kaldırarak hazırlamayı devre dışı bırakın.

![Hazırlama modunu etkinleştirme seçeneğini gösteren ekran görüntüsü.](./media/how-to-dirsync-upgrade-get-started/configurestaging.png)

* **İleri** düğmesine tıklayın
* Onay sayfasındaki **yükle** düğmesine tıklayın.

Azure AD Connect artık etkin sunucunuzdur ve mevcut DirSync sunucunuzu kullanmaya geri dönemezsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Azure AD Connect'i yüklediniz, artık [yüklemeyi doğrulayabilir ve lisans atayabilirsiniz](how-to-connect-post-installation.md).

Yüklemeyle etkinleştirilen şu yeni özellikler hakkında daha fazla bilgi edinin: [Otomatik yükseltme](how-to-connect-install-automatic-upgrade.md), [Yanlışlıkla silmeleri engelleme](how-to-connect-sync-feature-prevent-accidental-deletes.md) ve [Azure AD Connect Health](how-to-connect-health-sync.md).

Şu genel konu başlıkları hakkında daha fazla bilgi edinin: [Zamanlayıcı ve eşitleme tetikleme](how-to-connect-sync-feature-scheduler.md).

[Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md) hakkında daha fazla bilgi edinin.
