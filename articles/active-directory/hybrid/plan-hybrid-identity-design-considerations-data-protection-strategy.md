---
title: Karma kimlik tasarımı-veri koruma stratejisi Azure | Microsoft Docs
description: Tanımladığınız iş gereksinimlerini karşılamak üzere karma kimlik çözümünüz için veri koruma stratejisini tanımlarsınız.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e76fd1f4-340a-492a-84d9-e05f3b7cc396
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: bac3f53def6db1038a6dd7e45d7933daa22df9f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98703861"
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Karma kimlik çözümünüz için veri koruma stratejisini tanımlama
Bu görevde, karma kimlik çözümünüz için veri koruma stratejisini, içinde tanımladığınız iş gereksinimlerini karşılayacak şekilde tanımlayacaksınız:

* [Veri koruma gereksinimlerini belirleme](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [İçerik yönetimi gereksinimlerini belirleme](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [Erişim denetimi gereksinimlerini belirleme](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [Olay yanıtı gereksinimlerini belirleme](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Veri koruma seçeneklerini tanımlayın
[Dizin eşitleme gereksinimlerini belirleme](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)bölümünde açıklandığı gibi Microsoft Azure AD, şirket içi Active Directory Domain Services (AD DS) ile eşitlenebilir. Bu tümleştirme, kuruluşların şirket kaynaklarına erişmeye çalışırken kullanıcıların kimlik bilgilerini doğrulamak için Azure AD 'yi kullanmasına olanak sağlar. Bunu her iki senaryo için de yapabilirsiniz: bekleyen veriler şirket içinde ve bulutta. Azure AD 'deki verilere erişim, bir güvenlik belirteci hizmeti (STS) aracılığıyla Kullanıcı kimlik doğrulaması gerektirir.

Kimliği doğrulandıktan sonra, Kullanıcı asıl adı (UPN) kimlik doğrulama belirtecinden okundu. Ardından, yetkilendirme sistemi, çoğaltılan bölümü ve kullanıcının etki alanına karşılık gelen kapsayıcıyı belirler. Kullanıcının varlığı, etkin durumu ve rolü hakkındaki bilgiler, yetkilendirme sisteminin, bu oturumdaki Kullanıcı için hedef kiracıya erişimin yetkilendirilip yetkilendirilmediğini belirlemesine yardımcı olur. Belirli yetkili eylemler (özellikle, Kullanıcı ve parola sıfırlama oluşturma) bir kiracı yöneticisinin daha sonra uyumluluk çabalarını veya araştırmacıyı yönetmek için kullandığı bir denetim izi oluşturun.

Şirket içi veri merkezinizdeki verileri bir Internet bağlantısı üzerinden Azure depolama 'ya taşımak, veri hacmi, bant genişliği kullanılabilirliği veya diğer konular nedeniyle her zaman uygulanabilir olmayabilir. [Azure Storage içeri/dışarı aktarma hizmeti](../../import-export/storage-import-export-service.md) , blob depolamada büyük miktarlarda veri yerleştirme/alma için donanım tabanlı bir seçenek sağlar. Cloud [-şifrelenen](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn306081(v=ws.11)#BKMK_BL2012R2) sabit disk sürücülerinizi doğrudan bulut işletmenlerinin depolama hesabınıza yüklendiği bir Azure veri merkezine göndermenizi sağlar veya size dönmek için Azure verilerinizi sürücülerinizde indirebilir. Bu işlem için yalnızca şifrelenmiş diskler kabul edilir (iş kurulumu sırasında hizmet tarafından oluşturulan bir BitLocker anahtarı kullanılarak). BitLocker anahtarı ayrı olarak Azure 'a sağlanır ve bu sayede bant dışı anahtar paylaşımı sağlanır.

Yoldaki veriler farklı senaryolarda gerçekleşeceğinizden, Ayrıca, Microsoft Azure kiracıların bir diğerinden yalıtmak için [sanal ağ](https://azure.microsoft.com/documentation/services/virtual-network/) kullandığını, ana bilgisayar ve konuk düzeyinde güvenlik DUVARLARı, IP paket filtrelemesi, bağlantı noktası engelleme ve HTTPS uç noktaları gibi ölçüler oluşturmayı da unutmayın. Ancak, Azure 'un altyapıdan altyapıya ve altyapıdan müşteriye (Şirket içi) de dahil olmak üzere iç iletişimleri büyük bir olasılıkla şifrelenir. Diğer önemli senaryolar, Azure veri merkezlerindeki iletişimlerdir; Microsoft, ağları, başka bir VM 'nin IP adresi üzerinde taklit veya gizlice dinmeyeceğini güvence altına almak için yönetir. Azure depolama veya SQL veritabanlarına erişirken veya Cloud Services bağlanırken TLS/SSL kullanılır. Bu durumda, müşteri yöneticisi bir TLS/SSL sertifikası edinmekten ve kiracı altyapısına dağıtmaya sorumludur. Aynı dağıtımdaki sanal makineler arasında veya Microsoft Azure Sanal Ağ aracılığıyla tek bir dağıtımda bulunan kiracılar arasında taşınan veri trafiği, HTTPS, SSL/TLS veya diğerleri gibi şifreli iletişim protokolleri aracılığıyla korunabilir.

[Veri koruma gereksinimlerini belirleme](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)içindeki soruların nasıl yanıtlandığına bağlı olarak, verilerinizi nasıl korumak istediğinizi ve hibrit kimlik çözümünün bu işlemle size nasıl yardımcı olacağını belirleyebilmelisiniz. Aşağıdaki tabloda, Azure tarafından desteklenen ve her bir veri koruma senaryosunda kullanılabilen seçenekler gösterilmektedir.

| Veri koruma seçenekleri | Bulutta kalan | Şirket içi bekleyen | Yoldaki |
| --- | --- | --- | --- |
| BitLocker Sürücü Şifrelemesi |X |X | |
| Veritabanlarını şifrelemek için SQL Server |X |X | |
| VM 'den VM 'ye şifreleme | | |X |
| SSL/TLS | | |X |
| VPN | | |X |

> [!NOTE]
> Her bir Azure hizmetinin uyumlu olduğu sertifikalar hakkında daha fazla bilgi edinmek için [Microsoft Azure Güven Merkezi](https://azure.microsoft.com/support/trust-center/) ' nde [özelliğe göre uyumluluğu](https://azure.microsoft.com/support/trust-center/services/) okuyun.
> Veri koruma seçenekleri çok katmanlı bir yaklaşım kullandığından, bu seçenekler arasındaki karşılaştırma bu görev için geçerli değildir. Verilerin her durumu için kullanılabilen tüm seçenekleri kullandığınızdan emin olun.
>
>

## <a name="define-content-management-options"></a>İçerik yönetimi seçeneklerini tanımlayın

Karma kimlik altyapısını yönetmek için Azure AD kullanmanın avantajlarından biri, işlemin son kullanıcının perspektifinden tamamen saydam olmasını sağlar. Kullanıcı, paylaşılan bir kaynağa erişmeyi dener, kaynak kimlik doğrulaması gerektirir, kullanıcının belirteci edinmek ve kaynağa erişmesi için Azure AD 'ye bir kimlik doğrulama isteği gönderebilmesi gerekir. Bu işlemin tamamı, Kullanıcı etkileşimi olmadan arka planda gerçekleşir. 

Veri gizliliği hakkında sorun olan kuruluşlar, genellikle çözümü için veri sınıflandırması gerektirir. Mevcut şirket içi altyapınız zaten veri sınıflandırması kullanıyorsa, kullanıcının kimliğinin ana deposu olarak Azure AD 'yi kullanmak mümkündür. Veri sınıflandırması için şirket içinde kullanılan ortak bir araç, Windows Server 2012 R2 için [veri sınıflandırma araç seti](/previous-versions/tn-archive/hh204743(v=technet.10)) olarak adlandırılır. Bu araç, özel bulutunuzda dosya sunucularındaki verileri tanımlamak, sınıflandırmak ve korumak için yardımcı olabilir. Bu görevi gerçekleştirmek için Windows Server 2012 ' de [Otomatik dosya sınıflandırmasını](/windows-server/identity/solution-guides/deploy-automatic-file-classification--demonstration-steps-) kullanmak da mümkündür.

Kuruluşunuzda veri sınıflandırması yoksa ancak yeni sunucular eklemek zorunda kalmadan hassas dosyaları korumaları gerekiyorsa, Microsoft [Azure Rights Management hizmetini](/azure/information-protection/what-is-azure-rms)kullanabilirler.  Azure RMS, dosyalarınızı ve e-postanızı güvenli hale getirmeye yardımcı olmak için şifreleme, kimlik ve yetkilendirme ilkelerini kullanır ve telefon, tablet ve bilgisayar gibi birden çok cihazda çalışmaktadır. Azure RMS bir bulut hizmeti olduğundan, korumalı içeriği bunlarla paylaşmadan önce diğer kuruluşlarla güvenleri açıkça yapılandırmaya gerek yoktur. Zaten bir Microsoft 365 veya bir Azure AD dizini varsa, kuruluşlar arasında işbirliği otomatik olarak desteklenir. Ayrıca, Azure Active Directory Eşitleme Hizmetleri (Azure AD Eşitleme) veya Azure AD Connect kullanarak, şirket içi Active Directory hesaplarınız için ortak bir kimlik desteklemek üzere yalnızca Azure RMS ihtiyaç duyacağı Dizin özniteliklerini de eşzamanlı hale getirebilirsiniz.

İçerik yönetiminin önemli bir bölümü, kimin hangi kaynağa eriştiğini öğrenmektir. bu nedenle, kimlik yönetimi çözümü için zengin bir günlüğe kaydetme özelliği önemlidir. Azure AD, aşağıdakiler dahil olmak üzere 30 gün boyunca günlük sağlar:

* Rol üyeliğindeki değişiklikler (örn. genel yönetici rolüne eklenen Kullanıcı)
* Kimlik bilgisi güncelleştirmeleri (örn: parola değişiklikleri)
* Etki alanı yönetimi (örn. özel etki alanını doğrulama, etki alanını kaldırma)
* Uygulamaları ekleme veya kaldırma
* Kullanıcı Yönetimi (örn. bir kullanıcı ekleme, kaldırma, güncelleştirme)
* Lisansları ekleme veya kaldırma

> [!NOTE]
> Azure 'da günlüğe kaydetme özellikleri hakkında daha fazla bilgi için [Microsoft Azure Güvenlik ve denetim günlük yönetimini](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) okuyun.
> [İçerik yönetimi gereksinimlerini belirleme](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)içindeki soruların nasıl yanıtlandığına bağlı olarak, karma kimlik çözümünüzde içeriğin nasıl yönetilmesini istediğinizi belirleyebilmelisiniz. Tablo 6 ' da kullanıma sunulan tüm seçenekler Azure AD ile tümleştirilmesine karşın, iş gereksinimleriniz için ne kadar uygun olduğunu tanımlamak önemlidir.
>
>

| İçerik yönetimi seçenekleri | Avantajlar | Dezavantajlar |
| --- | --- | --- |
| Merkezi şirket içi (Active Directory Rights Management sunucusu) |Verileri sınıflandırmaktan sorumlu sunucu altyapısı üzerinde tam denetim <br> Windows Server 'da yerleşik yetenek, ek lisans veya abonelik gerekmez <br> Bir karma senaryoda Azure AD ile tümleştirilebilir <br> Exchange Online ve SharePoint Online gibi Microsoft Online hizmetlerinde bilgi hakları yönetimi (ıRM) özelliklerini ve Microsoft 365 destekler <br> Exchange Server, SharePoint Server gibi şirket içi Microsoft sunucu ürünlerini ve Windows Server ve dosya sınıflandırma altyapısını (FCı) çalıştıran dosya sunucularını destekler. |Sunucunun sahibi olduğu için daha yüksek bakım (güncelleştirmeler, yapılandırma ve olası yükseltmelerden haberdar olma) <br> Şirket içi sunucu altyapısı gerektir<br> Yerel olarak Azure özelliklerinden faydalanır |
| Bulutta Merkezi (Azure RMS) |Şirket içi çözümle karşılaştırıldığında daha kolay yönetilebilir <br> Karma bir senaryoda AD DS tümleştirilebilirler <br>  Azure AD ile tam olarak tümleşik <br> Hizmeti dağıtmak için şirket içi bir sunucu gerektirmez <br> Exchange Server, SharePoint, Server ve Windows Server ve dosya sınıflandırması, altyapı (FCı) çalıştıran dosya sunucuları gibi şirket içi Microsoft sunucu ürünlerini destekler <br> Bu, BYOK özelliği ile kiracının anahtarı üzerinde tamamen denetime sahip olabilir. |Kuruluşunuzun RMS 'yi destekleyen bir bulut aboneliği olmalıdır <br> RMS için Kullanıcı kimlik doğrulamasını desteklemek üzere kuruluşunuzun bir Azure AD dizini olmalıdır |
| Karma (Azure RMS, şirket Içi Active Directory Rights Management sunucusu ile tümleşik) |Bu senaryo, merkezi olarak şirket içinde ve bulutta bulunan avantajları birikir. |Kuruluşunuzun RMS 'yi destekleyen bir bulut aboneliği olmalıdır <br> Kuruluşunuzun RMS için Kullanıcı kimlik doğrulamasını desteklemesi için bir Azure AD dizinine sahip olması gerekir. <br> Azure bulut hizmeti ile şirket içi altyapı arasında bağlantı gerektirir |

## <a name="define-access-control-options"></a>Erişim denetimi seçeneklerini tanımlayın
Azure AD 'de bulunan kimlik doğrulama, yetkilendirme ve erişim denetimi yeteneklerini kullanarak, şirketinizin bir merkezi kimlik deposu kullanmasını etkinleştirerek, kullanıcıların ve iş ortaklarının aşağıdaki şekilde gösterildiği gibi çoklu oturum açma (SSO) kullanmasına izin verebilirsiniz:

![Merkezi Yönetim](./media/plan-hybrid-identity-design-considerations/centralized-management.png)

Merkezi Yönetim ve diğer dizinlerle tam tümleştirme

Azure Active Directory binlerce SaaS uygulamasına ve şirket içi Web uygulamalarına çoklu oturum açma olanağı sunar. Microsoft tarafından test edilmiş SSO üçüncü tarafı hakkında daha fazla bilgi için Azure Active Directory bkz. [Federasyon uyumluluk listesi: çoklu oturum açma makalesini uygulamak için kullanılabilecek üçüncü taraf kimlik sağlayıcıları](how-to-connect-fed-compatibility.md) . Bu özellik, kuruluşun kimlik ve erişim yönetiminin denetimini tutarken çeşitli B2B senaryolarını uygulamasına olanak sağlar. Ancak, B2B tasarlama işlemi sırasında, iş ortağı tarafından kullanılan kimlik doğrulama yöntemini anlamak ve bu yöntemin Azure tarafından desteklenip desteklenmediğini doğrulamak önemlidir. Şu anda aşağıdaki yöntemler Azure AD tarafından desteklenmektedir:

* Security Assertion Markup Language (SAML)
* OAuth
* Kerberos
* Belirteçler
* Sertifikalar

> [!NOTE]
> her protokol ve Azure 'un özellikleri hakkında daha fazla ayrıntıyı öğrenmek için [Azure Active Directory kimlik doğrulama protokollerini](/previous-versions/azure/dn151124(v=azure.100)) okuyun.
>
>

Mobil iş uygulamaları, Azure AD desteğini kullanarak, çalışanların mobil uygulamalarında kurumsal Active Directory kimlik bilgileriyle oturum açmalarına olanak tanımak için aynı kolay Mobile Services kimlik doğrulama deneyimini kullanabilir. Bu özellik ile Azure AD, zaten desteklenen diğer kimlik sağlayıcılarının (Microsoft hesapları, Facebook ID, Google ID ve Twitter ID) yanı sıra Mobile Services bir kimlik sağlayıcısı olarak desteklenir. Şirket içi uygulamalar kullanıcının şirket AD DS bulunan kimlik bilgilerini kullanıyorsa, iş ortaklarının ve buluttan gelen kullanıcıların erişimi saydam olmalıdır. Kullanıcının koşullu erişim denetimini (bulut tabanlı) Web uygulamaları, Web API 'SI, Microsoft bulut Hizmetleri, üçüncü taraf SaaS uygulamaları ve yerel (mobil) istemci uygulamaları için yönetebilir ve güvenlik, denetleme, tek bir yerde raporlama avantajlarına sahip olabilirsiniz. Ancak, uygulamayı üretim dışı bir ortamda veya sınırlı sayıda kullanıcıyla doğrulamanız önerilir.

> [!TIP]
> Azure AD 'nin AD DS sahip olduğu grup ilkesi sahip olmadığından bahsetmek önemlidir. Cihazlara ilke zorlamak için [Microsoft Intune](/mem/intune/)gibi bir mobil cihaz yönetimi çözümüne ihtiyacınız vardır.
>
>

Kullanıcının kimliği Azure AD kullanarak doğrulandıktan sonra, kullanıcının sahip olduğu erişim düzeyini değerlendirmek önemlidir. Kullanıcının bir kaynak üzerinde sahip olduğu erişim düzeyi farklılık gösterebilir. Azure AD bazı kaynaklara erişimi denetleyerek ek bir güvenlik katmanı ekleyebilirken, kaynağın kendisinin bir dosya sunucusunda bulunan dosyalar için erişim denetimi gibi kendi erişim denetimi listesine de ayrıca sahip olabileceğini aklınızda bulundurun. Aşağıdaki şekil, karma bir senaryoda sahip olduğunuz erişim denetimi düzeylerini özetler:

![erişim denetimi](./media/plan-hybrid-identity-design-considerations/accesscontrol.png)

Şekil X ' te gösterilen diyagramdaki her etkileşim, Azure AD tarafından kapsanacak bir erişim denetimi senaryosunu temsil eder. Aşağıda her senaryonun açıklamasına sahipsiniz:

1. Şirket içinde barındırılan uygulamalara koşullu erişim: Windows Server 2012 R2 ile AD FS kullanmak üzere yapılandırılmış uygulamalar için erişim ilkeleriyle kayıtlı cihazları kullanabilirsiniz.

2. Azure portal Access Control: Azure, Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak portala erişimi denetlemenize de olanak tanır. Bu yöntem, şirketin Azure portal bir bireyin izin veren işlem sayısını kısıtlayabilmesini sağlar. BT yöneticileri portala erişimi denetlemek için Azure RBAC kullanarak, aşağıdaki erişim yönetimi yaklaşımlarını kullanarak erişim temsilciliğini alabilir:

   - Grup tabanlı rol atama: Azure AD gruplarına, yerel Active Directory eşitlenemeyen erişim atayabilirsiniz. Bu, kuruluşunuzun araç yönetiminde yaptığı mevcut yatırımlardan ve grupları yönetmek için süreçlerden yararlanmanızı sağlar. Azure AD Premium için temsilci Grup Yönetimi özelliğini de kullanabilirsiniz.
   - Azure 'da yerleşik roller kullanın: kullanıcıların ve grupların yalnızca işlerini yapmak için gereken görevleri yapmak için izne sahip olduğundan emin olmak için, sahip, katkıda bulunan ve okuyucu olmak üzere üç rol kullanabilirsiniz.
   -  Kaynaklara ayrıntılı erişim: belirli bir abonelik, kaynak grubu veya bir Web sitesi ya da veritabanı gibi tek bir Azure kaynağı için kullanıcılara ve gruplara roller atayabilirsiniz. Bu şekilde, kullanıcıların ihtiyaç duydukları tüm kaynaklara erişimi olduğundan ve yönetilmesi gerekmeyen kaynaklara erişimleri olduğundan emin olabilirsiniz.

   > [!NOTE]
   > Uygulama oluşturuyorsanız ve bunlara yönelik erişim denetimini özelleştirmek istiyorsanız, Azure AD uygulama rollerini yetkilendirme için kullanmak da mümkündür. Bu özelliği kullanmak için uygulamanızı derlemek üzere bu [WebApp-Roleclaim-DotNet örneğini](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) gözden geçirin.


3. Microsoft Intune olan Microsoft 365 uygulamalar için koşullu erişim: BT yöneticileri kurumsal kaynakların güvenliğini sağlamak için koşullu erişim cihaz ilkeleri sağlayabilir, ancak aynı zamanda uyumlu cihazlardaki bilgi çalışanlarının hizmetlere erişmesine izin verir. 
  
4. SaaS uygulamaları için koşullu erişim: [Bu özellik](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work/) , uygulama başına Multi-Factor Authentication erişim kurallarını ve güvenilen bir ağda olmayan kullanıcılara erişimi engelleme olanağını yapılandırmanıza olanak tanır. Multi-Factor Authentication kurallarını uygulamaya atanan tüm kullanıcılara veya yalnızca belirtilen güvenlik grupları içindeki kullanıcılara uygulayabilirsiniz. Kullanıcılar, uygulamaya kuruluşun ağı içinde olan bir IP adresinden erişiyorsa Multi-Factor Authentication gereksiniminden dışlanırlar.

Access Control seçenekleri çok katmanlı bir yaklaşım kullandığından, bu seçenekler arasındaki karşılaştırma bu görev için geçerli değildir. Kaynaklarınızın erişimini denetlemenizi gerektiren her senaryo için kullanılabilen tüm seçenekleri kullandığınızdan emin olun.

## <a name="define-incident-response-options"></a>Olay yanıtı seçeneklerini tanımlayın
Azure AD, kullanıcının etkinliğini izleyerek bu BT ortamında potansiyel güvenlik risklerini kimlik konusunda yardımcı olabilir. Kuruluşunuzun dizininin bütünlüğünden ve güvenliğine ilişkin görünürlük elde etmek için Azure AD erişim ve kullanım raporlarını kullanabilir. Bu bilgilerle, bir BT Yöneticisi, olası güvenlik risklerini daha iyi bir şekilde belirleyebilir ve bu da riskleri hafifletmek için yeterli planlayabilirler.  [Azure AD Premium aboneliğin](../fundamentals/active-directory-get-started-premium.md) , bu bilgileri elde etmek için etkinleştirebileceği bir güvenlik raporları kümesi vardır. [Azure AD raporları](../reports-monitoring/overview-reports.md) aşağıdaki şekilde kategorilere ayrılır:

* **Anomali raporları**: anormal olarak bulunan oturum açma olaylarını içerir. Amaç, Bu etkinlikten haberdar olmanız ve bir olayın şüpheli olup olmadığı konusunda bir belirleme yapmanız sağlamaktır.
* **Tümleşik uygulama raporu**: bulut uygulamalarının kuruluşunuzda kullanılma şekli hakkında öngörüler sağlar. Azure Active Directory binlerce bulut uygulaması ile tümleştirme sunar.
* **Hata raporları**: hesapların dış uygulamalara hazırlanması sırasında oluşabilecek hataları belirtin.
* **Kullanıcıya özel raporlar**: belirli bir kullanıcı için cihaz/oturum açma etkinlik verilerini görüntüler.
* **Etkinlik günlükleri**: son 24 saat, son 7 gün veya son 30 gün içindeki tüm denetlenen olayların kaydını, Ayrıca grup etkinliği değişikliklerini ve parola sıfırlama ve kayıt etkinliğini içerir.

> [!TIP]
> Aynı zamanda, [kimlik bilgilerinin sızmış](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials/) olduğu olay yanıt ekibinin Kullanıcı tarafından da çalışmasına yardımcı olabilecek başka bir rapor. Bu rapor, sızdırılan kimlik bilgileri listesi ve kiracınız arasındaki tüm eşleşmeleri yüzeyler.
>


Azure AD 'de bir olay yanıtı araştırması sırasında kullanılabilen diğer önemli yerleşik raporlar ve şunları yapabilirsiniz:

* **Parola sıfırlama etkinliği**: kuruluşta etkin olmayan parola sıfırlamasının nasıl kullanıldığı hakkında öngörüler sağlar.
* **Parola sıfırlama kayıt etkinliği**: kullanıcıların parola sıfırlama yöntemlerini ve hangi yöntemleri seçtikleri hakkında öngörüler sağlar.
* **Grup etkinliği**: erişim panelinde başlatılan grupta yapılan değişikliklerin bir geçmişini (örn. eklenen veya kaldırılan kullanıcılar) sağlar.

Olay yanıtı araştırma işlemi sırasında kullanabileceğiniz Azure AD Premium çekirdek raporlama özelliğine ek olarak, şu gibi bilgileri almak için denetim raporundan de yararlanabilir:

* Rol üyeliğindeki değişiklikler (örneğin, Kullanıcı, genel yönetici rolüne eklendi)
* Kimlik bilgisi güncelleştirmeleri (örneğin, parola değişiklikleri)
* Etki alanı yönetimi (örneğin, özel etki alanını doğrulama, etki alanını kaldırma)
* Uygulamaları ekleme veya kaldırma
* Kullanıcı Yönetimi (örneğin, bir kullanıcı ekleme, kaldırma, güncelleştirme)
* Lisansları ekleme veya kaldırma

Olay yanıtı seçenekleri çok katmanlı bir yaklaşım kullandığından, bu seçenekler arasındaki karşılaştırma bu görev için geçerli değildir. Azure AD raporlama özelliğini şirketinizin olay yanıtı işleminin bir parçası olarak kullanmanızı gerektiren her senaryo için kullanılabilen tüm seçenekleri kullandığınızdan emin olun.

## <a name="next-steps"></a>Sonraki adımlar
[Karma kimlik yönetimi görevlerini belirleme](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>Ayrıca Bkz.
[Tasarım konularına genel bakış](plan-hybrid-identity-design-considerations-overview.md)