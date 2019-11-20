---
title: 'Azure AD Connect: Desteklenen topolojiler | Microsoft Docs'
description: Bu konu, Azure AD Connect için desteklenen ve desteklenmeyen topolojilerle ilgili ayrıntılı bilgi
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: conceptual
ms.date: 11/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9618e02f54fbb2a3b92771761c5fcf700d126b5c
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70275225"
---
# <a name="topologies-for-azure-ad-connect"></a>Azure AD Connect için topolojiler
Bu makalede, anahtar tümleştirme çözümü olarak Azure AD Connect eşitleme kullanan çeşitli şirket içi ve Azure Active Directory (Azure AD) topolojileri açıklanmaktadır. Bu makalede hem desteklenen hem de desteklenmeyen yapılandırmalar bulunur.


Makalede resimler için gösterge aşağıda verilmiştir:

| Açıklama | Sembol |
| --- | --- |
| Şirket içi Active Directory ormanı |![Şirket içi Active Directory ormanı](./media/plan-connect-topologies/LegendAD1.png) |
| Filtrelenmiş içeri aktarma ile şirket içi Active Directory |![Filtrelenmiş içeri aktarma ile Active Directory](./media/plan-connect-topologies/LegendAD2.png) |
| Azure AD Connect eşitleme sunucusu |![Azure AD Connect eşitleme sunucusu](./media/plan-connect-topologies/LegendSync1.png) |
| "Hazırlama modu" Azure AD Connect eşitleme sunucusu |!["Hazırlama modu" Azure AD Connect eşitleme sunucusu](./media/plan-connect-topologies/LegendSync2.png) |
| Forefront Identity Manager (FIM) 2010 veya Microsoft Identity Manager (MıM) 2016 ile GALSync |![FIM 2010 veya MıM 2016 ile GALSync](./media/plan-connect-topologies/LegendSync3.png) |
| Azure AD Connect eşitleme sunucusu, ayrıntılı |![Azure AD Connect eşitleme sunucusu, ayrıntılı](./media/plan-connect-topologies/LegendSync4.png) |
| Azure AD |![Azure Active Directory](./media/plan-connect-topologies/LegendAAD.png) |
| Desteklenmeyen senaryo |![Desteklenmeyen senaryo](./media/plan-connect-topologies/LegendUnsupported.png) |


> [!IMPORTANT]
> Microsoft, resmi olarak belgelenen yapılandırmaların veya eylemlerin dışında değiştirme veya çalışma Azure AD Connect eşitlemeyi desteklemez. Bu yapılandırmaların veya eylemlerin herhangi biri, tutarsız veya desteklenmeyen Azure AD Connect eşitleme durumuyla sonuçlanabilir. Sonuç olarak Microsoft, bu tür dağıtımlar için teknik destek sağlayamaz.


## <a name="single-forest-single-azure-ad-tenant"></a>Tek orman, tek Azure AD kiracısı
![Tek bir orman ve tek bir kiracı için topoloji](./media/plan-connect-topologies/SingleForestSingleDirectory.png)

En yaygın topoloji, bir veya birden çok etki alanı ve tek bir Azure AD kiracısı içeren tek bir şirket içi ormandır. Azure AD kimlik doğrulaması için Parola karması eşitleme kullanılır. Azure AD Connect Express yüklemesi yalnızca bu topolojiyi destekler.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Tek orman, birden çok eşitleme sunucusu tek bir Azure AD kiracısına
![Tek bir orman için desteklenmeyen ve filtrelenmiş topoloji](./media/plan-connect-topologies/SingleForestFilteredUnsupported.png)

Aynı Azure AD kiracısına bağlı birden çok Azure AD Connect eşitleme sunucusunun olması, [hazırlama sunucusu](#staging-server)dışında desteklenmez. Bu sunucular, birbirini dışlayan bir nesne kümesiyle eşitlenmek üzere yapılandırılmış olsa bile desteklenmez. Ormandaki tüm etki alanlarına tek bir sunucudan ulaşamıyorsanız veya yükü çeşitli sunucularda dağıtmak istiyorsanız bu topolojiyi kabul edebilirsiniz.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Birden çok orman, tek Azure AD kiracısı
![Birden çok orman için topoloji ve tek bir kiracı](./media/plan-connect-topologies/MultiForestSingleDirectory.png)

Birçok kuruluşun birden çok şirket içi Active Directory ormanlı ortamları vardır. Birden fazla şirket içi Active Directory ormanına sahip olmanın çeşitli nedenleri vardır. Tipik örnekler, hesap-kaynak ormanları ve bir birleşme veya alım sonucunu içeren tasarımlardır.

Birden çok ormanınız varsa, tüm ormanların tek bir Azure AD Connect eşitleme sunucusu tarafından erişilebilir olması gerekir. Sunucu bir etki alanına katılmalıdır. Tüm ormanlara ulaşmak için gerekiyorsa, sunucuyu bir çevre ağına (DMZ, sivil bölge ve denetimli alt ağ olarak da bilinir) yerleştirebilirsiniz.

Azure AD Connect Yükleme Sihirbazı, birden çok ormanda temsil edilen kullanıcıları birleştirmek için çeşitli seçenekler sunar. Amaç, bir Kullanıcı Azure AD 'de yalnızca bir kez temsil edilir. Yükleme sihirbazındaki özel yükleme yolunda yapılandırabileceğiniz bazı yaygın topolojiler vardır. **Kullanıcılarınızın benzersiz olarak tanımlanması** sayfasında topolojinizi temsil eden ilgili seçeneği belirleyin. Birleştirme yalnızca kullanıcılar için yapılandırılır. Yinelenen gruplar varsayılan yapılandırmayla birleştirilmez.

Genel topolojiler, ayrı topolojiler, [tam ağ](#multiple-forests-full-mesh-with-optional-galsync)ve [Hesap-kaynak topolojisi](#multiple-forests-account-resource-forest)hakkındaki bölümlerde ele alınmıştır.

Azure AD Connect eşitlemede varsayılan yapılandırma şunları varsayar:

* Her Kullanıcı yalnızca bir etkin hesaba sahiptir ve bu hesabın bulunduğu orman, kullanıcının kimliğini doğrulamak için kullanılır. Bu varsayım, Parola karması eşitleme, geçişli kimlik doğrulama ve Federasyon içindir. UserPrincipalName ve Sourcetutturucu/ImmutableID bu ormandan geliyor.
* Her kullanıcının yalnızca bir posta kutusu vardır.
* Bir kullanıcı için posta kutusunu barındıran orman, Exchange genel adres listesinde (GAL) görünecek özniteliklerin en iyi veri kalitesine sahiptir. Kullanıcı için bir posta kutusu yoksa, bu öznitelik değerlerinin katkıda bulunmak için herhangi bir orman kullanılabilir.
* Bağlı bir posta kutunuz varsa, oturum açma için kullanılan farklı bir ormanda de bir hesap vardır.

Ortamınız bu varsayımlara eşleşmezse, aşağıdakiler gerçekleşir:

* Birden fazla etkin hesabınız veya birden fazla posta kutusu varsa, eşitleme altyapısı bir tane seçer ve diğerini yoksayar.
* Başka bir etkin hesabı olmayan bağlı bir posta kutusu Azure AD 'ye aktarılmaz. Kullanıcı hesabı herhangi bir grupta üye olarak temsil edilmez. DirSync 'teki bağlı bir posta kutusu her zaman normal bir posta kutusu olarak temsil edilir. Bu değişiklik, çok ormanlı senaryolara daha iyi destek sağlamak için özellikle farklı bir davranıştır.

[Varsayılan yapılandırmayı anlamak için](concept-azure-ad-connect-sync-default-configuration.md)daha fazla ayrıntı bulabilirsiniz.

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Birden çok orman, birden çok eşitleme sunucusu bir Azure AD kiracısına
![Birden çok orman ve birden çok eşitleme sunucusu için desteklenmeyen topoloji](./media/plan-connect-topologies/MultiForestMultiSyncUnsupported.png)

Tek bir Azure AD kiracısına bağlı birden fazla Azure AD Connect eşitleme sunucusu olması desteklenmez. Özel durum, [hazırlama sunucusu](#staging-server)kullanmaktır.

Bu topoloji, tek bir Azure AD kiracısına bağlı **birden çok eşitleme sunucusu** tarafından aşağıdaki olandan farklıdır.

### <a name="multiple-forests-single-sync-server-users-are-represented-in-only-one-directory"></a>Birden çok orman, tek bir eşitleme sunucusu, kullanıcılar yalnızca bir dizinde temsil edilir
![Kullanıcıları tüm dizinlerde yalnızca bir kez temsil etme seçeneği](./media/plan-connect-topologies/MultiForestUsersOnce.png)

![Birden çok ormanın ve ayrı topolojilerinin gösterimi](./media/plan-connect-topologies/MultiForestSeparateTopologies.png)

Bu ortamda, tüm şirket içi ormanlar ayrı varlıklar olarak kabul edilir. Başka bir ormanda hiçbir Kullanıcı yok. Her ormanın kendi Exchange kuruluşu vardır ve ormanlar arasında bir GALSync yoktur. Bu topoloji, bir merger/alma işleminden sonra veya her bir işletme biriminin bağımsız olarak çalıştığı bir kuruluşta durum olabilir. Bu ormanlar Azure AD 'deki aynı kuruluşta bulunur ve birleştirilmiş bir GAL ile birlikte görüntülenir. Yukarıdaki resimde, her ormandaki her bir nesne meta veri deposunda bir kez temsil edilir ve hedef Azure AD kiracısında toplanır.

### <a name="multiple-forests-match-users"></a>Birden çok orman: kullanıcıları Eşleştir
Tüm bu senaryolarda yaygın olarak, dağıtım ve güvenlik gruplarının bir Kullanıcı, kişi ve yabancı güvenlik sorumlusu (FSPs) karışımı içerebildiği bir yer vardır. FSPs 'ler, bir güvenlik grubundaki diğer ormanlardan üyeleri göstermek için Active Directory Domain Services (AD DS) ' de kullanılır. Tüm FSPs 'ler, Azure AD 'deki gerçek nesneye çözümlenir.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Birden çok orman: isteğe bağlı GALSync ile tam ağ
![Birden çok dizinde kullanıcı kimliklerinin mevcut olduğu durumlarda eşleşen posta özniteliğini kullanma seçeneği](./media/plan-connect-topologies/MultiForestUsersMail.png)

![Birden çok orman için tam ağ topolojisi](./media/plan-connect-topologies/MultiForestFullMesh.png)

Tam ağ topolojisi, kullanıcıların ve kaynakların herhangi bir ormanda konumlandırılabilir olmasını sağlar. Genellikle, ormanlar arasında iki yönlü güvenler vardır.

Exchange birden fazla ormanda mevcutsa, şirket içi bir GALSync çözümü (isteğe bağlı olarak) olabilir. Her Kullanıcı, diğer tüm ormanlarda bir kişi olarak temsil edilir. GALSync genellikle FIM 2010 veya MıM 2016 aracılığıyla uygulanır. Azure AD Connect, şirket içi GALSync için kullanılamaz.

Bu senaryoda, kimlik nesneleri posta özniteliği aracılığıyla birleştirilir. Bir ormanda posta kutusuna sahip olan bir Kullanıcı, diğer ormanlarda bulunan kişilerle birleştirilir.

### <a name="multiple-forests-account-resource-forest"></a>Birden çok orman: hesap-kaynak ormanı
![Birden çok dizinde kimlik bulunduğunda eşleştirme için objectSID ve msExchMasterAccountSID özniteliklerinin kullanılmasına yönelik seçenek](./media/plan-connect-topologies/MultiForestUsersObjectSID.png)

![Hesap-birden çok orman için kaynak orman topolojisi](./media/plan-connect-topologies/MultiForestAccountResource.png)

Hesap-kaynak orman topolojisinde, etkin kullanıcı hesaplarına sahip bir veya daha fazla *Hesap* ormanınızın olması gerekir. Ayrıca, devre dışı bırakılmış hesaplara sahip bir veya daha fazla *kaynak* ormanda vardır.

Bu senaryoda, bir (veya daha fazla) kaynak ormanı tüm hesap ormanlarına güvenir. Kaynak ormanı genellikle Exchange ve Lync ile genişletilmiş bir Active Directory şemasına sahiptir. Diğer paylaşılan hizmetlerle birlikte tüm Exchange ve Lync Hizmetleri bu ormanda bulunur. Kullanıcılar bu ormanda devre dışı bırakılmış bir kullanıcı hesabına sahiptir ve posta kutusu hesap ormanına bağlanır.

## <a name="office-365-and-topology-considerations"></a>Office 365 ve topoloji konuları
Bazı Office 365 iş yükleri desteklenen topolojilerde belirli kısıtlamalara sahiptir:

| İş yükü | Kısıtlamalar |
| --------- | --------- |
| Exchange Online | Exchange Online tarafından desteklenen karma topolojiler hakkında daha fazla bilgi için bkz. [birden çok Active Directory ormanlı karma dağıtımlar](https://technet.microsoft.com/library/jj873754.aspx). |
| Skype Kurumsal | Birden çok şirket içi orman kullandığınızda yalnızca hesap-kaynak orman topolojisi desteklenir. Daha fazla bilgi için bkz. [Skype Kurumsal Sunucu Için ortam gereksinimleri 2015](https://technet.microsoft.com/library/dn933910.aspx). |

Daha büyük bir kuruluşunuz varsa, [Office 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) özelliğini kullanmayı göz önünde bulundurmanız gerekir. Kullanıcının kaynaklarının bulunduğu veri merkezi bölgesinde tanımlamanızı sağlar.

## <a name="staging-server"></a>Hazırlama sunucusu
![Bir topolojide hazırlama sunucusu](./media/plan-connect-topologies/MultiForestStaging.png)

Azure AD Connect, *hazırlama modunda*ikinci bir sunucu yüklemeyi destekler. Bu moddaki bir sunucu, tüm bağlı dizinlerden verileri okur, ancak bağlı dizinlere herhangi bir şey yazmaz. Normal eşitleme döngüsünü kullanır ve bu nedenle kimlik verilerinin güncelleştirilmiş bir kopyasına sahiptir.

Birincil sunucunun başarısız olduğu bir olağanüstü durumda, hazırlama sunucusuna yük devretmek için yük devretme yapabilirsiniz. Bunu Azure AD Connect sihirbazında yapabilirsiniz. Birincil sunucuyla paylaşılan bir altyapı olmadığından, bu ikinci sunucu farklı bir veri merkezinde bulunabilir. Birincil sunucuda yapılan tüm yapılandırma değişikkisini ikinci sunucuya el ile kopyalamanız gerekir.

Yeni bir özel yapılandırmayı ve bu verilerin verilerinize ait etkisini test etmek için bir hazırlama sunucusu kullanabilirsiniz. Değişiklikleri önizleyebilir ve yapılandırmayı ayarlayabilirsiniz. Yeni yapılandırmadan memnun olduğunuzda, hazırlama sunucusunu etkin sunucu yapabilir ve eski etkin sunucuyu hazırlama moduna ayarlayabilirsiniz.

Bu yöntemi, etkin eşitleme sunucusunu değiştirmek için de kullanabilirsiniz. Yeni sunucuyu hazırlayın ve hazırlama moduna ayarlayın. Bunun iyi bir durumda olduğundan emin olun, hazırlama modunu devre dışı bırakın (etkin hale getirin) ve şu anda etkin olan sunucuyu kapatın.

Farklı veri merkezlerinde birden çok yedekleme kullanmak istediğinizde birden fazla hazırlama sunucusu olması mümkündür.

## <a name="multiple-azure-ad-tenants"></a>Birden çok Azure AD kiracısından
Bir kuruluş için Azure AD 'de tek bir kiracının olması önerilir.
Birden çok Azure AD kiracılarını kullanmayı planlayabilmeniz için önce [Azure AD 'de yönetim birimleri yönetimi](../users-groups-roles/directory-administrative-units.md)makalesine bakın. Tek bir kiracıyı kullanabileceğiniz yaygın senaryolar ele alınmaktadır.

![Birden çok orman ve birden çok kiracı için topoloji](./media/plan-connect-topologies/MultiForestMultiDirectory.png)

Bir Azure AD Connect eşitleme sunucusu ile bir Azure AD kiracısı arasında 1:1 ilişkisi vardır. Her Azure AD kiracısı için bir Azure AD Connect eşitleme sunucusu yüklemeniz gerekir. Azure AD kiracı örnekleri tasarıma göre yalıtılmıştır. Diğer bir deyişle, bir Kiracıdaki kullanıcılar diğer Kiracıdaki kullanıcıları göremez. Bu ayrımı istiyorsanız, bu desteklenen bir yapılandırmadır. Aksi takdirde, tek Azure AD kiracı modelini kullanmanız gerekir.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Her nesne bir Azure AD kiracısında yalnızca bir kez
![Tek bir orman için filtrelenmiş topoloji](./media/plan-connect-topologies/SingleForestFiltered.png)

Bu topolojide, her bir Azure AD kiracısına bir Azure AD Connect eşitleme sunucusu bağlanır. Azure AD Connect eşitleme sunucuları, her birinin üzerinde çalışması için birbirini dışlayan bir nesne kümesine sahip olacak şekilde filtrelemeye yönelik olarak yapılandırılmalıdır. Örneğin, her bir sunucuyu belirli bir etki alanı veya kuruluş birimi için kapsam yapabilirsiniz.

Bir DNS etki alanı, yalnızca tek bir Azure AD kiracısına kaydedilebilir. Şirket içi Active Directory örneğindeki kullanıcıların UPN 'leri de ayrı ad alanları kullanmalıdır. Örneğin, yukarıdaki resimde, şirket içi Active Directory örneğine üç ayrı UPN soneki kaydedilir: contoso.com, fabrikam.com ve wingtiptoys.com. Her şirket içi Active Directory etki alanındaki kullanıcılar farklı bir ad alanı kullanır.

>[!NOTE]
>Genel adres listesi eşitlemesi (GalSync) bu topolojide otomatik olarak yapılmaz ve her kiracının Exchange Online ve Skype Kurumsal Çevrimiçi ortamda tüm genel adres listesi (GAL) olduğundan emin olmak için ek bir özel MıM uygulamasını gerektirir.


Bu topoloji, aksi takdirde desteklenen senaryolarda aşağıdaki kısıtlamalara sahiptir:

* Azure AD kiracılarından yalnızca biri, şirket içi Active Directory örneğiyle bir Exchange hibrit karma ayarı etkinleştirebilir.
* Windows 10 cihazları yalnızca bir Azure AD kiracısı ile ilişkilendirilebilir.
* Parola karması eşitleme ve doğrudan kimlik doğrulama için çoklu oturum açma (SSO) seçeneği yalnızca bir Azure AD kiracısı ile kullanılabilir.

Birbirini dışlayan bir nesne kümesinin gereksinimi geri yazma için de geçerlidir. Tek bir şirket içi yapılandırma varsaydıklarından, bazı geri yazma özellikleri bu topolojide desteklenmez. Bu özellikler şunlardır:

* Varsayılan yapılandırma ile grup geri yazma.
* Cihaz geri yazma.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Bir Azure AD kiracısında her bir nesne birden çok kez
![Tek bir orman ve birden çok kiracı için desteklenmeyen topoloji](./media/plan-connect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Tek bir orman ve birden çok bağlayıcı için desteklenmeyen topoloji](./media/plan-connect-topologies/SingleForestMultiConnectorsUnsupported.png)

Bu görevler desteklenmez:

* Aynı kullanıcıyı birden çok Azure AD kiracısından eşitleyin.
* Bir Azure AD kiracısındaki kullanıcıların başka bir Azure AD kiracısında kişi olarak görünmesi için bir yapılandırma değişikliği yapın.
* Birden çok Azure AD kiracılarına bağlanmak için Azure AD Connect eşitlemesini değiştirin.

### <a name="galsync-by-using-writeback"></a>Geri yazma kullanarak GALSync
![Birden çok orman ve birden çok dizin için desteklenmeyen topoloji, Azure AD 'de GALSync ile](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![Birden çok orman ve birden çok dizin için desteklenmeyen topoloji, şirket içi Active Directory üzerinde GALSync ile](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Azure AD kiracılar tasarıma göre yalıtılmıştır. Bu görevler desteklenmez:

* Başka bir Azure AD kiracısından verileri okumak için Azure AD Connect eşitleme yapılandırmasını değiştirin.
* Azure AD Connect eşitleme kullanarak kullanıcıları başka bir şirket içi Active Directory örneğine kişiler olarak dışarı aktarın.

### <a name="galsync-with-on-premises-sync-server"></a>Şirket içi eşitleme sunucusu ile GALSync
![Birden çok orman ve birden çok dizin için topolojide GALSync](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync.png)

İki Exchange kuruluşu arasında kullanıcıları eşitlemek için FIM 2010 veya MıM 2016 ' i şirket içi olarak (GALSync aracılığıyla) kullanabilirsiniz. Bir kuruluştaki kullanıcılar diğer kuruluşta yabancı Kullanıcı/kişi olarak görünür. Bu farklı şirket içi Active Directory örnekleri, kendi Azure AD kiracılarıyla eşitlenebilir.

## <a name="next-steps"></a>Sonraki adımlar
Bu senaryolar için Azure AD Connect yükleme hakkında bilgi edinmek için bkz. [özel Azure AD Connect yüklemesi](how-to-connect-install-custom.md).

[Azure AD Connect eşitleme](how-to-connect-sync-whatis.md) yapılandırması hakkında daha fazla bilgi edinin.

[Şirket içi kimliklerinizi Azure Active Directory tümleştirme](whatis-hybrid-identity.md)hakkında daha fazla bilgi edinin.
