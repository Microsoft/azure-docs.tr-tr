---
title: 'Azure AD Connect: hesaplar ve izinler | Microsoft Docs'
description: Bu konuda, kullanılan ve oluşturulan hesaplar ve gerekli izinler açıklanmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 10/03/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72ae1301be4a3a3c086961aae72fb9eeb12aeda2
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960239"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: hesaplar ve izinler

## <a name="accounts-used-for-azure-ad-connect"></a>Azure AD Connect için kullanılan hesaplar

![hesaplara genel bakış](media/reference-connect-accounts-permissions/account5.png)

Azure AD Connect, şirket içi veya Windows Server Active Directory bilgileri Azure Active Directory 'e eşitleyebilmek için 3 hesap kullanır.  Bu hesaplar şunlardır:

- **AD DS bağlayıcı hesabı**: Windows Server Active Directory bilgi okumak/yazmak için kullanılır

- **ADSync hizmet hesabı**: eşitleme hizmetini ÇALıŞTıRMAK ve SQL veritabanına erişmek için kullanılır

- **Azure ad bağlayıcı hesabı**: Azure AD 'ye bilgi yazmak için kullanılır

Azure AD Connect çalıştırmak için kullanılan bu üç hesaba ek olarak, Azure AD Connect yüklemek için aşağıdaki ek hesaplara da ihtiyacınız olacaktır.  Bunlar:

- **Yerel yönetici hesabı**: Azure AD Connect yükleyen ve makinede yerel yönetici izinlerine sahip olan yönetici.

- **AD DS Kurumsal Yönetici hesabı**: isteğe bağlı olarak, yukarıdaki "AD DS bağlayıcı hesabı" oluşturmak için kullanılır.

- **Azure AD Genel yönetici hesabı**: Azure ad bağlayıcı hesabı oluşturmak ve Azure AD 'yi yapılandırmak için kullanılır.

- **SQL SA hesabı (isteğe bağlı)** : SQL Server tam sürümü kullanılırken ADSync veritabanını oluşturmak için kullanılır.  Bu SQL Server, Azure AD Connect yüklemesinde yerel veya uzak olabilir.  Bu hesap, kuruluş yöneticisiyle aynı hesap olabilir.  Veritabanının sağlanması artık SQL Yöneticisi tarafından bant dışında gerçekleştirilebilir ve ardından veritabanı sahibi haklarıyla Azure AD Connect Yöneticisi tarafından yüklenebilir.  Bu bilgi için bkz. [SQL yönetici temsilcisi izinlerini kullanarak Azure AD Connect yüklemesi](how-to-connect-install-sql-delegation.md)


>[!IMPORTANT]
> Yapı 1.4 itibariyle. # # #. # artık AD DS bağlayıcı hesabı olarak bir kurumsal yönetici veya etki alanı yöneticisi hesabı kullanmak için desteklenmiyor.  **Mevcut hesabı kullan**' ı belirtirken Kurumsal Yönetici veya etki alanı yöneticisi olan bir hesap girmeyi denerseniz, bir hata alırsınız.

> [!NOTE]
> Bir ESAE yönetim ormanına Azure AD Connect ' de kullanılan yönetim hesaplarını yönetmek için desteklenir ("kırmızı orman" olarak da bilinir).
> Adanmış yönetim ormanları, kuruluşların yönetim hesaplarını, iş istasyonlarını ve grupları üretim ortamından daha güçlü güvenlik denetimlerine sahip bir ortamda barındırmasına olanak tanır.
> Adanmış yönetim ormanları hakkında daha fazla bilgi edinmek için lütfen [Esae yönetim ormanı tasarım yaklaşımını](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#esae-administrative-forest-design-approach) inceleyin
>>>>>>> e683a61b0ed62ae739941410f658a127534e2481

> [!NOTE]
> Genel yönetici rolü, İlk kurulumdan sonra gerekli değildir ve tek gerekli hesap, **Dizin eşitleme hesapları** rol hesabı olur. Bu, necssarily değil, hesabı yalnızca genel yönetici rolüyle kaldırmak istediğinizi ifade eder. Rolü daha az güçlü bir rol olarak değiştirmek daha iyidir, ancak Sihirbazı yeniden çalıştırmanız gerektiğinde hesabın sorunları ortaya çıkarabilir. Rol ayrıcalığını azaltarak, Azure AD Connect Sihirbazı 'nı yeniden kullanmanız gerekiyorsa, her zaman ayrıcalıklarınızı yeniden kapatabilirsiniz. 

## <a name="installing-azure-ad-connect"></a>Azure AD Connect yükleniyor
Azure AD Connect Yükleme Sihirbazı iki farklı yol sunar:

* Hızlı Ayarlar ' da, sihirbaz daha fazla ayrıcalık gerektirir.  Bu, Kullanıcı oluşturmanıza veya izinleri yapılandırmanıza gerek kalmadan yapılandırmanızı kolayca ayarlayabilmesini sağlar.
* Özel ayarlar ' da, sihirbaz size daha fazla seçenek ve seçenek sunar. Ancak, doğru izinlere sahip olduğunuzdan emin olmak için gereken bazı durumlar vardır.



## <a name="express-settings-installation"></a>Hızlı ayarlar yüklemesi
Hızlı Ayarlar ' da, Yükleme Sihirbazı aşağıdakileri ister:

  - AD DS Kurumsal Yönetici kimlik bilgileri
  - Azure AD Genel yönetici kimlik bilgileri

### <a name="ad-ds-enterprise-admin-credentials"></a>AD DS Kurumsal Yönetici kimlik bilgileri
AD DS Kurumsal Yönetici hesabı, şirket içi Active Directory yapılandırmak için kullanılır. Bu kimlik bilgileri yalnızca yükleme sırasında kullanılır ve yükleme tamamlandıktan sonra kullanılmaz. Etki alanı yöneticisinin değil Kurumsal Yönetici, Active Directory içindeki izinlerin tüm etki alanlarında ayarlanmadığından emin olmalıdır.

DirSync 'ten yükseltiyorsanız, DirSync tarafından kullanılan hesabın parolasını sıfırlamak için AD DS Enterprise Admins kimlik bilgileri kullanılır. Ayrıca Azure AD Genel yönetici kimlik bilgilerine de ihtiyacınız vardır.

### <a name="azure-ad-global-admin-credentials"></a>Azure AD Genel yönetici kimlik bilgileri
Bu kimlik bilgileri yalnızca yükleme sırasında kullanılır ve yükleme tamamlandıktan sonra kullanılmaz. Azure AD 'de yapılan değişiklikleri eşitlemek için kullanılan Azure AD Bağlayıcısı hesabını oluşturmak için kullanılır. Hesap Ayrıca Azure AD 'de bir özellik olarak eşitlemeyi de mümkün.

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>AD DS bağlayıcı hesabı hızlı ayarlar için gerekli izinler
AD DS bağlayıcı hesabı, Windows Server AD 'ye okumak ve yazmak için oluşturulur ve Express ayarları tarafından oluşturulduğunda aşağıdaki izinlere sahiptir:

| İzin | Ne için kullanılır |
| --- | --- |
| <li>Dizin Değişikliklerini Çoğalt</li><li>Dizin değişikliklerini çoğaltma |Parola karması eşitleme |
| Tüm özellikleri oku/yaz kullanıcılar |İçeri ve Exchange karma |
| Tüm özellikleri oku/yaz InetOrgPerson |İçeri ve Exchange karma |
| Tüm özellikler grubunu oku/yaz |İçeri ve Exchange karma |
| Tüm özellikleri oku/yaz Ilgili kişisi |İçeri ve Exchange karma |
| Parolayı Sıfırla |Parola geri yazmayı etkinleştirme hazırlığı |

### <a name="express-installation-wizard-summary"></a>Hızlı Yükleme Sihirbazı Özeti

![Hızlı yükleme](./media/reference-connect-accounts-permissions/express.png)

Aşağıda, Hızlı Yükleme Sihirbazı sayfalarının bir özeti, toplanan kimlik bilgileri ve bunların için kullanıldıkları özellikler yer verilmiştir.

| Sihirbaz sayfası | Toplanan kimlik bilgileri | Gerekli İzinler | Kullanıldığı yer |
| --- | --- | --- | --- |
| Yok |Yükleme sihirbazını çalıştıran Kullanıcı |Yerel sunucunun yöneticisi |<li>Eşitleme hizmetini çalıştırmak için olarak kullanılan ADSync hizmet hesabını oluşturur. |
| Azure AD 'ye bağlanma |Azure AD dizin kimlik bilgileri |Azure AD 'de Genel yönetici rolü |<li>Azure AD dizininde eşitleme etkinleştiriliyor.</li>  <li>Azure AD 'de devam eden eşitleme işlemleri için kullanılan Azure AD bağlayıcı hesabının oluşturulması.</li> |
| AD DS Bağlan |Şirket içi Active Directory kimlik bilgileri |Active Directory 'de Enterprise Admins (EA) grubunun üyesi |<li>Active Directory AD DS bağlayıcı hesabı oluşturur ve buna izin verir. Bu oluşturulan hesap, eşitleme sırasında dizin bilgilerini okumak ve yazmak için kullanılır.</li> |


## <a name="custom-installation-settings"></a>Özel yükleme ayarları

Özel ayarlar yüklemesiyle, sihirbaz size daha fazla seçenek ve seçenek sunar. 

### <a name="custom-installation-wizard-summary"></a>Özel Yükleme Sihirbazı Özeti

Aşağıda, Özel Yükleme Sihirbazı sayfalarının Özeti, toplanan kimlik bilgileri ve bunların için kullanıldıkları Özellikler verilmiştir.

![Hızlı yükleme](./media/reference-connect-accounts-permissions/customize.png)

| Sihirbaz sayfası | Toplanan kimlik bilgileri | Gerekli İzinler | Kullanıldığı yer |
| --- | --- | --- | --- |
| Yok |Yükleme sihirbazını çalıştıran Kullanıcı |<li>Yerel sunucunun yöneticisi</li><li>Tam bir SQL Server kullanılıyorsa, kullanıcının SQL 'de Sistem Yöneticisi (SA) olması gerekir</li> |Varsayılan olarak, eşitleme altyapısı hizmet hesabı olarak kullanılan yerel hesabı oluşturur. Hesap yalnızca yönetici belirli bir hesap belirtmezse oluşturulur. |
| Eşitleme hizmetlerini, hizmet hesabı seçeneğini yükler |AD veya yerel kullanıcı hesabı kimlik bilgileri |Kullanıcı, izinler Yükleme Sihirbazı tarafından verilir |Yönetici bir hesap belirtiyorsa, bu hesap eşitleme hizmeti için hizmet hesabı olarak kullanılır. |
| Azure AD 'ye bağlanma |Azure AD dizin kimlik bilgileri |Azure AD 'de Genel yönetici rolü |<li>Azure AD dizininde eşitleme etkinleştiriliyor.</li>  <li>Azure AD 'de devam eden eşitleme işlemleri için kullanılan Azure AD bağlayıcı hesabının oluşturulması.</li> |
| Dizinlerinizi bağlama |Azure AD 'ye bağlı her bir ormanın şirket içi Active Directory kimlik bilgileri |İzinler, etkinleştirdiğiniz özelliklere ve AD DS Bağlayıcısı hesabını oluşturma bölümünde bulunabilir. |Bu hesap, eşitleme sırasında dizin bilgilerini okumak ve yazmak için kullanılır. |
| AD FS sunucuları |Listedeki her sunucu için, Sihirbazı çalıştıran kullanıcının oturum açma kimlik bilgileri bağlantı kurmak için yeterli olmadığında sihirbaz kimlik bilgilerini toplar |Etki alanı Yöneticisi |AD FS sunucusu rolünü yükleme ve yapılandırma. |
| Web uygulaması ara sunucusu |Listedeki her sunucu için, Sihirbazı çalıştıran kullanıcının oturum açma kimlik bilgileri bağlantı kurmak için yeterli olmadığında sihirbaz kimlik bilgilerini toplar |Hedef makinede yerel yönetici |WAP sunucu rolünü yükleme ve yapılandırma. |
| Proxy güveni kimlik bilgileri |Federasyon hizmeti güven kimlik bilgileri (proxy 'nin bir güven sertifikasını FS 'den kaydettirmek için kullandığı kimlik bilgileri |AD FS sunucusunun yerel Yöneticisi olan etki alanı hesabı |FS-WAP güven sertifikasının ilk kaydı. |
| AD FS hizmet hesabı sayfası, "etki alanı kullanıcı hesabı kullan seçeneği" |AD Kullanıcı hesabı kimlik bilgileri |Etki alanı kullanıcısı |Kimlik bilgileri sağlanmış olan Azure AD Kullanıcı hesabı, AD FS hizmetinin oturum açma hesabı olarak kullanılır. |

### <a name="create-the-ad-ds-connector-account"></a>AD DS Bağlayıcısı hesabını oluşturma

>[!IMPORTANT]
>Azure AD DS bağlayıcı hesabı için doğru Active Directory izinlerini yapılandırmanıza yardımcı olacak bir cmdlet koleksiyonu içeren, ADSyncConfig. psm1 adlı yeni bir PowerShell **modülü (ağustos** 2018 ' de yayımlanmıştır) eklenmiştir.
>
>Daha fazla bilgi için bkz [. Azure AD Connect: AD DS bağlayıcı hesabı Iznini yapılandırma](how-to-connect-configure-ad-ds-connector-account.md)

**Dizinlerinizi bağlama** sayfasında belirttiğiniz hesap, yüklemeden önce Active Directory bulunmalıdır.  Azure AD Connect sürüm 1.1.524.0 ve üzeri, Azure AD Connect sihirbazının Active Directory bağlanmak için kullanılan **AD DS Bağlayıcısı hesabını** oluşturmasına izin vermek için seçenek içerir.  

Ayrıca gerekli izinleri verilmiş olmalıdır. Yükleme Sihirbazı izinleri doğrulamaz ve tüm sorunlar yalnızca eşitleme sırasında bulunur.

Size gereken izinler, etkinleştirdiğiniz isteğe bağlı özelliklere bağlıdır. Birden çok etki alanı varsa, ormandaki tüm etki alanları için izinler verilmelidir. Bu özelliklerden herhangi birini etkinleştirmezseniz, varsayılan **etki alanı kullanıcı** izinleri yeterlidir.

| Özellik | İzinler |
| --- | --- |
| ms-DS-ımlarımguıd özelliği |Tasarım kavramları bölümünde belgelenen ms-DS-ımıbu GUID özniteliğine yazma izinleri [-MS-DS-ımıbu GUID kaynağını Sourcetutturucu olarak kullanma](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Parola karması eşitleme |<li>Dizin Değişikliklerini Çoğalt</li>  <li>Dizin değişikliklerini çoğaltma |
| Exchange karma dağıtımı |Kullanıcılar, gruplar ve kişiler için [Exchange hibrit geri yazma](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) bölümünde belgelenen özniteliklere yazma izinleri. |
| Exchange posta genel klasörü |Ortak klasörler için [Exchange posta ortak klasöründe](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) belgelenen özniteliklere yönelik okuma izinleri. | 
| Parola geri yazma |Kullanıcılar için [parola yönetimine](../authentication/howto-sspr-writeback.md) Başlarken bölümünde belgelenen özniteliklere yazma izinleri. |
| Cihaz geri yazma |[Cihaz geri yazma](how-to-connect-device-writeback.md)bölümünde açıklandığı şekilde bir PowerShell betiğine izin verildi. |
| Grup geri yazma |**Office 365 gruplarını** Exchange yüklü bir ormana geri almanıza olanak sağlar.  Daha fazla bilgi için bkz. [Grup geri yazma](how-to-connect-preview.md#group-writeback).|

## <a name="upgrade"></a>Upgrade
Bir Azure AD Connect sürümünden yeni bir sürüme yükselttiğinizde, aşağıdaki izinlere sahip olmanız gerekir:

>[!IMPORTANT]
>Build 1.1.484 ile başlayarak, Azure AD Connect SQL veritabanını yükseltmek için sysadmin izinleri gerektiren bir gerileme hatası sunmuştur.  Bu hata Build 1.1.647 içinde düzeltilir.  Bu yapıya yükseltiyorsanız sysadmin izinlerine ihtiyacınız olacaktır.  Dbo izinleri yeterli değildir.  Azure AD Connect, sysadmin izinlerine sahip olmadan yükseltmeyi denerseniz, yükseltme başarısız olur ve Azure AD Connect artık daha sonra düzgün çalışmayacaktır.  Microsoft bu durumun farkındadır ve bu sorunu düzeltmek için çalışmaktadır.


| Asıl | Gerekli izinler | Ne için kullanılır |
| --- | --- | --- |
| Yükleme sihirbazını çalıştıran Kullanıcı |Yerel sunucunun yöneticisi |İkili dosyaları güncelleştirin. |
| Yükleme sihirbazını çalıştıran Kullanıcı |ADSyncAdmins üyesi |Eşitleme kurallarında ve diğer yapılandırmada değişiklik yapın. |
| Yükleme sihirbazını çalıştıran Kullanıcı |Tam bir SQL Server kullanıyorsanız: eşitleme altyapısı veritabanının DBO (veya benzeri) |Yeni sütunlara sahip tabloları güncelleştirme gibi veritabanı düzeyinde değişiklikler yapın. |

## <a name="more-about-the-created-accounts"></a>Oluşturulan hesaplar hakkında daha fazla bilgi
### <a name="ad-ds-connector-account"></a>AD DS bağlayıcı hesabı
Hızlı ayarları kullanıyorsanız, Active Directory eşitleme için kullanılan bir hesap oluşturulur. Oluşturulan hesap, kullanıcılar kapsayıcısındaki orman kök etki alanında yer alır ve adı **MSOL_** önekli. Hesap, sona ermeyecek uzun bir karmaşık parolayla oluşturulur. Etki alanında bir parola ilkeniz varsa, bu hesap için uzun ve karmaşık parolalara izin verildiğinden emin olun.

![AD hesabı](./media/reference-connect-accounts-permissions/adsyncserviceaccount.png)

Özel ayarları kullanıyorsanız, yüklemeye başlamadan önce hesabı oluşturmadan sorumlu olursunuz.  Bkz. AD DS Bağlayıcısı hesabı oluşturma.

### <a name="adsync-service-account"></a>ADSync hizmet hesabı
Eşitleme hizmeti farklı hesaplar altında çalıştırılabilir. Bir **sanal hizmet hesabı** (VSA), **Grup tarafından yönetilen hizmet hesabı** (gMSA/sMSA) veya normal bir kullanıcı hesabı altında çalışabilir. Yeni bir yükleme yaptığınızda desteklenen seçenekler, Connect 'in 2017 Nisan sürümü ile değiştirilmiştir. Azure AD Connect önceki bir sürümünden yükseltiyorsanız, bu ek seçenekler kullanılamaz.

| Hesap türü | Yükleme seçeneği | Açıklama |
| --- | --- | --- |
| [Sanal hizmet hesabı](#virtual-service-account) | Express ve Custom, 2017 Nisan ve üzeri | Bu, etki alanı denetleyicisindeki yüklemeler hariç tüm Express yüklemeleri için kullanılan seçenektir. Özel için, başka bir seçenek kullanılmadığı takdirde varsayılan seçenektir. |
| [Grup tarafından yönetilen hizmet hesabı](#group-managed-service-account) | Özel, 2017 Nisan ve üzeri | Uzak bir SQL Server kullanıyorsanız, grup tarafından yönetilen bir hizmet hesabı kullanmanızı öneririz. |
| [Kullanıcı hesabı](#user-account) | Express ve Custom, 2017 Nisan ve üzeri | AAD_ önekli bir kullanıcı hesabı yalnızca Windows Server 2008 ' e yüklendiğinde ve bir etki alanı denetleyicisine yüklendiğinde yükleme sırasında oluşturulur. |
| [Kullanıcı hesabı](#user-account) | Express ve Custom, 2017 Mart ve önceki sürümleri | Yükleme sırasında AAD_ önekli bir yerel hesap oluşturulur. Özel yükleme kullanılırken, başka bir hesap belirtilebilir. |

2017 Mart veya daha önceki bir yapıyla Bağlan ' ı kullanırsanız, Windows şifreleme anahtarlarını güvenlik nedenleriyle yok etmek için hizmet hesabındaki parolayı sıfırlamamalısınız. Azure AD Connect yüklemeden hesabı başka bir hesaba değiştiremezsiniz. 2017 Nisan veya sonraki bir sürüme yükseltme yaparsanız, hizmet hesabındaki parolanın değiştirilmesi desteklenir, ancak kullanılan hesabı değiştiremezsiniz.

> [!Important]
> Yalnızca ilk yüklemede hizmet hesabını ayarlayabilirsiniz. Yükleme tamamlandıktan sonra hizmet hesabını değiştirmek desteklenmez.

Bu, eşitleme hizmeti hesabı için varsayılan, önerilen ve desteklenen seçeneklerin bir tablosudur.

Açıklama:

- **Kalın** , varsayılan seçeneği ve çoğu durumda önerilen seçeneği gösterir.
- *İtalik* varsayılan seçenek olmadığında önerilen seçeneği gösterir.
- 2008-Windows Server 2008 üzerine yüklendiğinde varsayılan seçenek
- Kalın olmayan desteklenen seçenek
- Yerel hesap-sunucuda yerel kullanıcı hesabı
- Etki alanı hesabı-etki alanı kullanıcı hesabı
- sMSA- [tek başına yönetilen hizmet hesabı](https://technet.microsoft.com/library/dd548356.aspx)
- gMSA- [Grup yönetilen hizmet hesabı](https://technet.microsoft.com/library/hh831782.aspx)

| | Yerel Veritabanı</br>Çaba | LocalDB/LocalSQL</br>Özel | Uzak SQL</br>Özel |
| --- | --- | --- | --- |
| **tek başına/çalışma grubu makinesi** | Desteklenmez | **'NıN**</br>Yerel hesap (2008)</br>Yerel hesap |  Desteklenmez |
| **etki alanına katılmış makine** | **'NıN**</br>Yerel hesap (2008) | **'NıN**</br>Yerel hesap (2008)</br>Yerel hesap</br>Etki alanı hesabı</br>sMSA, gMSA | **gMSA**</br>Etki alanı hesabı |
| **Etki alanı denetleyicisi** | **Etki alanı hesabı** | *gMSA*</br>**Etki alanı hesabı**</br>SMSA 'yı| *gMSA*</br>**Etki alanı hesabı**|

#### <a name="virtual-service-account"></a>Sanal hizmet hesabı
Bir sanal hizmet hesabı, parolası olmayan ve Windows tarafından yönetilen özel bir hesap türüdür.

!['NıN](./media/reference-connect-accounts-permissions/aadsyncvsa.png)

VSA, eşitleme altyapısının ve SQL 'in aynı sunucuda olduğu senaryolarla birlikte kullanılmak üzere tasarlanmıştır. Uzak SQL kullanıyorsanız, bunun yerine bir grup tarafından yönetilen hizmet hesabı kullanmanız önerilir.

Bu özellik Windows Server 2008 R2 veya üstünü gerektirir. Azure AD Connect Windows Server 2008 ' ye yüklerseniz, yükleme bunun yerine bir [Kullanıcı hesabı](#user-account) kullanmaya geri döner.

#### <a name="group-managed-service-account"></a>Grup tarafından yönetilen hizmet hesabı
Uzak bir SQL Server kullanıyorsanız, **Grup tarafından yönetilen bir hizmet hesabı**kullanmanızı öneririz. Grup tarafından yönetilen hizmet hesabı için Active Directory hazırlama hakkında daha fazla bilgi için bkz. [Grup yönetilen hizmet hesaplarına genel bakış](https://technet.microsoft.com/library/hh831782.aspx).

Bu seçeneği kullanmak için, [gerekli bileşenleri yüklensin](how-to-connect-install-custom.md#install-required-components) sayfasında, **var olan bir hizmet hesabını kullan**' ı seçin ve **yönetilen hizmet hesabı**' nı seçin.  
![VSA @ no__t-1  
[Tek başına yönetilen hizmet hesabı](https://technet.microsoft.com/library/dd548356.aspx)kullanmak için de desteklenir. Ancak bunlar yalnızca yerel makinede kullanılabilir ve bunları varsayılan sanal hizmet hesabı üzerinden kullanmanın bir avantajı yoktur.

Bu özellik Windows Server 2012 veya üstünü gerektirir. Daha eski bir işletim sistemi kullanmanız ve uzak SQL kullanmanız gerekiyorsa, bir [Kullanıcı hesabı](#user-account)kullanmanız gerekir.

#### <a name="user-account"></a>Kullanıcı hesabı
Bir yerel hizmet hesabı, Yükleme Sihirbazı tarafından oluşturulur (özel ayarlarda kullanılacak hesabı belirtmediğiniz durumlar dışında). Hesap önekli **AAD_** ve gerçek eşitleme hizmetinin farklı çalışması için kullanılır. Bir etki alanı denetleyicisine Azure AD Connect yüklüyorsanız, hesap etki alanında oluşturulur. Şu durumlarda **AAD_** hizmet hesabının etki alanında bulunması gerekir:
   - SQL Server çalıştıran bir uzak sunucu kullanıyorsunuz
   - kimlik doğrulaması gerektiren bir proxy kullanıyorsunuz

![Eşitleme hizmeti hesabı](./media/reference-connect-accounts-permissions/syncserviceaccount.png)

Hesap, sona ermeyecek uzun bir karmaşık parolayla oluşturulur.

Bu hesap, diğer hesapların parolalarını güvenli bir şekilde depolamak için kullanılır. Bu diğer hesaplar parolaları, veritabanında şifreli olarak depolanır. Şifreleme anahtarlarına yönelik özel anahtarlar, Windows Data Protection API (DPAPI) kullanılarak şifreleme hizmetleri gizli anahtar şifrelemesi ile korunur.

Tam bir SQL Server kullanıyorsanız, hizmet hesabı eşitleme altyapısının oluşturulan veritabanının DBO 'su olur. Hizmet diğer izinlerle ilgili olarak çalışmaz. Bir SQL oturum açma da oluşturulur.

Hesaba Ayrıca dosya, kayıt defteri anahtarları ve eşitleme altyapısıyla ilgili diğer nesneler için izin verilir.

### <a name="azure-ad-connector-account"></a>Azure AD bağlayıcı hesabı
Eşitleme hizmeti kullanımı için Azure AD 'de bir hesap oluşturulur. Bu hesap, görünen adıyla tanımlanabilir.

![AD hesabı](./media/reference-connect-accounts-permissions/aadsyncserviceaccount2.png)

Hesabın kullanıldığı sunucunun adı, Kullanıcı adının ikinci bölümünde belirlenebilir. Resimde sunucu adı DC1 ' dir. Hazırlama sunucularınız varsa, her sunucunun kendi hesabı vardır.

Hesap, sona ermeyecek uzun bir karmaşık parolayla oluşturulur. Yalnızca dizin eşitleme görevlerini gerçekleştirmek için izinlere sahip özel bir rol **dizini eşitleme hesabı** verilir. Bu özel yerleşik rol Azure AD Connect Sihirbazı dışında verilemez. Azure portal bu hesabı rol **kullanıcısına**gösterir.

Azure AD 'de 20 Sync Service hesabı sınırı vardır. Azure AD 'de mevcut Azure AD hizmet hesaplarının listesini almak için şu Azure AD PowerShell cmdlet 'ini çalıştırın: `Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Kullanılmayan Azure AD hizmet hesaplarını kaldırmak için şu Azure AD PowerShell cmdlet 'ini çalıştırın: `Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

>[!NOTE]
>Yukarıdaki PowerShell komutlarını kullanabilmeniz [için, Graph modülünün Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0#installing-the-azure-ad-module) 'i yüklemeniz ve [Connect-Azuread](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0) kullanarak Azure AD örneğinize bağlanmanız gerekir

Azure AD Bağlayıcısı hesabının parolasını yönetme veya sıfırlama hakkında daha fazla bilgi için bkz [. Azure AD Connect hesabını yönetme](how-to-connect-azureadaccount.md)

## <a name="related-documentation"></a>İlgili belgeler
Şirket [içi kimliklerinizi Azure Active Directory tümleştirme](whatis-hybrid-identity.md)hakkındaki belgeleri okumadıysanız, aşağıdaki tabloda ilgili konuların bağlantıları verilmiştir.

|Konu |Bağlantı|  
| --- | --- |
|Azure AD Connect indir | [Azure AD Connect indir](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Hızlı ayarları kullanarak yükler | [Azure AD Connect Express yüklemesi](how-to-connect-install-express.md)|
|Özelleştirilmiş ayarları kullanarak yükler | [Azure AD Connect özel yüklemesi](./how-to-connect-install-custom.md)|
|DirSync 'ten yükseltme | [Azure AD eşitleme aracından (DirSync) yükseltme](how-to-dirsync-upgrade-get-started.md)|
|Yükleme sonrasında | [Yüklemeyi doğrulama ve lisansları atama](how-to-connect-post-installation.md)|

## <a name="next-steps"></a>Sonraki adımlar
[Şirket içi kimliklerinizi Azure Active Directory tümleştirme](whatis-hybrid-identity.md)hakkında daha fazla bilgi edinin.
