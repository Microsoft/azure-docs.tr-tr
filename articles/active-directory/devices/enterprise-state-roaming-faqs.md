---
title: Enterprise State Roaming SSS-Azure Active Directory
description: ESR hakkında sık sorulan sorular
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb889298a09c30a629c69442ebf31bc735af31d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96173133"
---
# <a name="settings-and-data-roaming-faq"></a>Ayarlar ve veri dolaşımı hakkında SSS

Bu makalede, yöneticilerin ayarlar ve uygulama verilerinin eşitlenmesi hakkında sahip olabileceği bazı sorular yanıtlanmaktadır.

## <a name="what-data-roams"></a>Hangi veriler gezinsin?

**Windows ayarları**: Windows işletim sisteminde yerleşik olarak bulunan bilgisayar ayarları. Genellikle, bunlar BILGISAYARıNıZı kişiselleştirebileceğiniz ve aşağıdaki geniş kategorileri içeren ayarlardır:

* *Tema*, masaüstü teması ve görev çubuğu ayarları gibi özellikler içerir.
* *Internet Explorer ayarları*, en son açılan sekmeleri ve sık kullanılanları içerir.
* Sık Kullanılanlar ve okuma listesi gibi *Microsoft Edge tarayıcı ayarları*.
* Internet parolaları, Wi-Fi profilleri ve diğerleri dahil olmak üzere *parolalar*.
* *Dil tercihleri*, düzenleri, sistem dili, tarih ve saat ayarları ile daha fazlasını içerir.
* Yüksek karşıtlık teması, Ekran Okuyucusu ve Büyüteç gibi *kolay erişim özellikleri*.
* Fare ayarları gibi *diğer Windows ayarları*.

> [!NOTE]
> Bu makale, 2015 Temmuz sürümünde Windows 10 ile başlatılan Microsoft Edge eski HTML tabanlı tarayıcı için geçerlidir. Makale, 15 Ocak 2020 ' de yayınlanan yeni Microsoft Edge Bermıum tabanlı tarayıcı için de geçerlidir. Yeni Microsoft Edge için eşitleme davranışı hakkında daha fazla bilgi için [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync)makalesine bakın.

**Uygulama verileri**: Evrensel Windows uygulamaları, ayarlar verilerini bir dolaşım klasörüne yazabilir ve bu klasöre yazılan tüm veriler otomatik olarak eşitlenir. Bu özellikten yararlanmak için tek bir uygulama geliştiricisinin bir uygulama tasarlaması vardır. Dolaşım kullanan bir Evrensel Windows uygulaması geliştirme hakkında daha fazla bilgi için bkz. [AppData Storage API](/windows/uwp/design/app-settings/store-and-retrieve-app-data) ve [Windows 8 AppData gezici geliştirici blogu](https://blogs.windows.com/windowsdeveloper/2016/05/04/roaming-app-data-and-the-user-experience/).

## <a name="what-account-is-used-for-settings-sync"></a>Ayarların eşitlenmesi için kullanılacak hesap nedir?

Windows 8.1, ayarlar eşitleme her zaman kullanılan tüketici Microsoft hesapları. Kurumsal kullanıcılar, ayar eşitlemesine erişim kazanmak için bir Microsoft hesabı Active Directory etki alanı hesabına bağlama yeteneğine sahipti. Windows 10 ' da bu bağlı Microsoft hesabı işlevselliği birincil/ikincil hesap çerçevesiyle değiştiriliyor.

Birincil hesap, Windows 'da oturum açmak için kullanılan hesap olarak tanımlanır. Bu bir Microsoft hesabı, bir Azure Active Directory (Azure AD) hesabı, şirket içi Active Directory hesabı veya yerel bir hesap olabilir. Birincil hesaba ek olarak, Windows 10 kullanıcıları cihazlarına bir veya daha fazla ikincil bulut hesabı ekleyebilir. İkincil hesap genellikle Microsoft hesabı, bir Azure AD hesabıdır veya Gmail ya da Facebook gibi başka bir hesaptır. Bu ikincil hesaplar, çoklu oturum açma ve Windows Mağazası gibi ek hizmetlere erişim sağlar, ancak ayarlar eşitlemesini güçlayamazlar.

Windows 10 ' da, ayarların eşitlenmesi için yalnızca cihazın birincil hesabı kullanılabilir (bkz. Windows [8 ' de Microsoft hesabı ayarları eşitlemesini nasıl yaparım? Windows 10 ' da Azure AD ayarları eşitleme](enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)).

Veriler, cihazdaki farklı Kullanıcı hesapları arasında hiçbir şekilde karıştırılmaz. Ayarların eşitlenmesi için iki kural vardır:

* Windows ayarları her zaman birincil hesap ile dolaşımını görür.
* Uygulama verileri, uygulamayı almak için kullanılan hesapla etiketlenecek. Yalnızca birincil hesap ile etiketlenmiş uygulamalar eşitlenir. Uygulama sahipliği etiketleme, bir uygulama Windows Mağazası veya mobil cihaz yönetimi (MDM) aracılığıyla dışarıdan yüklendiğinde belirlenir.

Bir uygulamanın sahibi tanımlanmamışsa, birincil hesap ile dolaşımda olur. Bir cihaz Windows 8 ' den Windows 8.1 veya Windows 10 ' a yükseltilirse, tüm uygulamalar Microsoft hesabı alındığı şekilde etiketlenecek. Bunun nedeni, çoğu kullanıcının Windows Mağazası aracılığıyla uygulama edinmesi ve Windows 10 ' dan önceki Azure AD hesapları için Windows Mağazası desteği yoktu. Bir uygulama çevrimdışı bir lisans aracılığıyla yüklenirse, uygulama cihazdaki birincil hesap kullanılarak etiketlenecek.

> [!NOTE]
> Kuruluşa ait olan ve Azure AD 'ye bağlı olan Windows 10 cihazları artık Microsoft hesaplarını bir etki alanı hesabına bağlanamaz. Bir Microsoft hesabı etki alanı hesabına bağlama ve tüm kullanıcının verilerinin Microsoft hesabı eşitlemesine izin verme özelliği (yani, bağlı Microsoft hesabı ve Active Directory işlevselliği aracılığıyla gezici Microsoft hesabı), bağlı bir Active Directory veya Azure AD ortamına katılmış Windows 10 cihazlarından kaldırılır.

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Windows 8 ' deki Microsoft hesabı ayarları eşitlemesini Nasıl yaparım? Windows 10 ' da Azure AD ayarları eşitlemesine yükseltmek istiyor musunuz?

Bağlı bir Microsoft hesabı ile Windows 8.1 çalıştıran Active Directory etki alanına katılıyorsa, ayarları Microsoft hesabı aracılığıyla eşitleirsiniz. Windows 10 ' a yükselttikten sonra, etki alanına katılmış bir kullanıcı olduğu ve Active Directory etki alanının Azure AD 'ye bağlanmadığı sürece Microsoft hesabı aracılığıyla Kullanıcı ayarlarını eşitlemeye devam edersiniz.

Şirket içi Active Directory etki alanı Azure AD ile bağlantı alıyorsa, cihazınız bağlı Azure AD hesabını kullanarak ayarları eşitlemeye çalışır. Azure AD yöneticisi Enterprise State Roaming etkinleştirmezse, bağlı Azure AD hesabınız ayarları eşitlemeyi durdurur. Bir Windows 10 kullanıcısı ve bir Azure AD kimliğiyle oturum açarsanız, yöneticiniz Azure AD aracılığıyla ayarları eşitlemeyi sağladığından Windows ayarlarını eşitlemeye başlayacaksınız.

Şirket cihazınızda herhangi bir kişisel veri depoladıysanız, Windows işletim sisteminin ve uygulama verilerinin Azure AD 'ye eşitlemeye başlayacağına dikkat etmeniz gerekir. Bu, aşağıdaki etkilere sahiptir:

* Kişisel Microsoft hesabı ayarlarınız, iş veya okul Azure AD hesaplarınızın ayarlarından ayrı olacak. Bunun nedeni Microsoft hesabı ve Azure AD ayarları eşitlenebilmesi artık ayrı hesaplar kullanmaktır.
* Wi-Fi parolaları, Web kimlik bilgileri ve daha önce bağlı bir Microsoft hesabı üzerinden eşitlenmiş Internet Explorer sık kullanılanları gibi kişisel veriler Azure AD aracılığıyla eşitlenir.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Microsoft hesabı ve Azure AD Enterprise State Roaming birlikte çalışabilirliği nasıl çalışır?

Windows 10 ' un Kasım 2015 veya sonraki sürümlerinde Enterprise State Roaming yalnızca tek bir hesap için desteklenir. Windows 'da bir iş veya okul Azure AD hesabı kullanarak oturum açarsanız, tüm veriler Azure AD aracılığıyla eşitlenir. Kişisel bir Microsoft hesabı kullanarak Windows 'da oturum açarsanız, tüm veriler Microsoft hesabı aracılığıyla eşitlenir. Evrensel AppData, yalnızca cihazdaki birincil oturum açma hesabını kullanarak dolaşımını ve yalnızca uygulamanın lisansının birincil hesaba ait olması durumunda dolaşımını görür. Tüm ikincil hesaplara ait olan uygulamalar için evrensel AppData eşitlenmez.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>Azure AD hesapları için birden çok kiracıdan ayarlar eşitlemesi yapılsın mı?

Farklı Azure AD kiracılarından birden çok Azure AD hesabı aynı cihazdan olduğunda, her bir Azure AD kiracısı için Azure Rights Management hizmetiyle iletişim kurmak üzere cihazın kayıt defterini güncelleştirmeniz gerekir.  

1. Her Azure AD kiracısı için GUID 'ı bulun. Azure portal açın ve bir Azure AD kiracısı seçin. Kiracının GUID 'SI, seçilen kiracının ( https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) **dizin kimliği** etiketli) özellikler sayfasıdır. 
2. GUID 'yi aldıktan sonra **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<tenant ID GUID>** kayıt defteri anahtarını eklemeniz gerekir.
   **KIRACı KIMLIĞI GUID** anahtarından, **Allowedrmsserverurls** adlı yeni bir çok dizeli değer (reg-Multi-SZ) oluşturun. Verileri için, cihazın eriştiği diğer Azure kiracılarının lisanslama dağıtım noktası URL 'Lerini belirtin.
3. AADRM modülünden **Get-AadrmConfiguration** cmdlet 'ini çalıştırarak lisanslama dağıtım noktası URL 'lerini bulabilirsiniz. **Licensingıntranetdistributionpointurl** ve **LicensingExtranetDistributionPointUrl** değerleri farklıysa, her iki değeri de belirtin. Değerler aynıysa, değeri yalnızca bir kez belirtin.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Mevcut Windows Masaüstü uygulamaları için Dolaşım ayarları seçenekleri nelerdir?

Dolaşım yalnızca Evrensel Windows uygulamaları için geçerlidir. Mevcut bir Windows masaüstü uygulamasında dolaşımı etkinleştirmek için kullanılabilecek iki seçenek vardır:

* [Masaüstü Köprüsü](/windows/msix/desktop/source-code-overview) , mevcut Windows masaüstü uygulamalarınızı Evrensel Windows platformu taşımanıza yardımcı olur. Buradan, Azure AD uygulama veri dolaşımı avantajlarından yararlanmak için en az kod değişikliği yapmanız gerekecektir. Masaüstü Köprüsü, uygulamalarınızı mevcut masaüstü uygulamaları için uygulama verilerini dolaşımı etkinleştirmek için gereken bir uygulama kimliğiyle sağlar.
* [Kullanıcı deneyimi sanallaştırma (UE-V)](/previous-versions//dn458947(v=vs.85)) , var olan Windows Masaüstü uygulamaları için özel bir ayarlar şablonu oluşturmanıza ve Win32 uygulamaları için dolaşımı etkinleştirmesine yardımcı olur. Bu seçenek, uygulama geliştiricisinin uygulamanın kodunu değiştirmesini gerektirmez. UE-V, Microsoft Desktop optimizasyon paketini satın almış olan müşteriler için şirket içi Active Directory dolaşımla sınırlandırılmıştır.

Yöneticiler, Windows işletim sistemi ayarlarının ve evrensel uygulama verilerinin dolaşımını, [UE-v grup ilkeleriyle](/microsoft-desktop-optimization-pack/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2)ve aşağıdakiler de dahil olmak üzere değiştirerek Windows masaüstü uygulaması verilerini DOLAŞıMA arak UE-v ' yi yapılandırabilir.

* Gezici Windows ayarları Grup İlkesi
* Windows uygulamaları grup ilkesini eşitlememe
* Uygulamalar bölümünde Internet Explorer dolaşımı

Microsoft, Microsoft 'ta UE-V ' i daha derin hale getirme ve Azure AD bulutu aracılığıyla ayarları dolaşımda olan UE-v ' i genişletmenin yollarını araştırabilir.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Eşitlenmiş ayarları ve verileri şirket içinde depolayabilirim miyim?

Enterprise State Roaming tüm eşitlenmiş verileri Microsoft bulutta depolar. UE-V, şirket içi bir gezici çözüm sunar.

## <a name="who-owns-the-data-thats-being-roamed"></a>Gezinmekte olan verilerin sahibi kim?

Kuruluşlar, Enterprise State Roaming aracılığıyla dolaşıma getirilen verilere sahiptir. Veriler bir Azure veri merkezinde depolanır. Tüm Kullanıcı verileri, Azure Information Protection Azure Rights Management hizmeti kullanılarak hem aktarım sırasında hem de bulutta Rest 'de şifrelenir. Bu, cihazın ayrılmadan önce Kullanıcı kimlik bilgileri gibi yalnızca belirli hassas verileri şifreleyen Microsoft hesabı tabanlı ayar eşitlemesine kıyasla bir iyileştirmedir.

Microsoft, müşteri verilerini koruma konusunda kararlıdır. Bir kuruluş kullanıcısının ayarlar verileri, Windows 10 cihazından ayrılmadan önce Azure Rights Management hizmeti tarafından otomatik olarak şifrelenir, bu nedenle başka bir Kullanıcı bu verileri okuyabilirler. Kuruluşunuzda Azure Rights Management hizmeti için ücretli bir abonelik varsa, belgeleri izleme ve iptal etme, hassas bilgiler içeren e-postaları otomatik olarak koruma ve kendi anahtarlarınızı yönetme ("kendi anahtarını getir" çözümü, BYOK olarak da bilinir) gibi diğer koruma özelliklerini kullanabilirsiniz. Bu özellikler ve bu koruma hizmetinin nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Azure Rights Management nedir?](/azure/information-protection/what-is-information-protection).

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Belirli bir uygulama veya ayar için eşitlemeyi yönetebilir miyim?

Windows 10 ' da, tek bir uygulama için dolaşımı devre dışı bırakmak üzere MDM veya grup ilkesi ayarı yoktur. Kiracı yöneticileri yönetilen bir cihazdaki tüm uygulamalar için AppData eşitlemesini devre dışı bırakabilir, ancak uygulama başına veya uygulama içi düzeyde daha hassas bir denetim yoktur.

## <a name="how-can-i-enable-or-disable-roaming"></a>Dolaşımı nasıl etkinleştirebilir veya devre dışı bırakabilirim?

**Ayarlar** uygulamasında **hesaplar**  >  **Ayarlar ' a** gidin. Bu sayfadan, ayarları dolaşırken hangi hesabın kullanıldığını görebilir ve tek tek ayar gruplarını, dolaşımda olacak şekilde etkinleştirebilir veya devre dışı bırakabilirsiniz.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Microsoft 'un Windows 10 ' da dolaşımını etkinleştirme önerisi nedir?

Microsoft, dolaşım Kullanıcı profilleri, UE-V ve Enterprise State Roaming dahil olmak üzere birkaç farklı ayarı gezici çözümü içerir.  Microsoft, Windows 'un gelecek sürümlerinde Enterprise State Roaming yatırım yapmayı taahhüt etmektedir. Kuruluşunuz, verileri buluta taşımaya uygun veya uygun değilse, birincil dolaşım teknolojiniz olarak UE-V kullanmanızı öneririz. Kuruluşunuz var olan Windows Masaüstü uygulamaları için dolaşım desteği gerektiriyorsa, ancak buluta geçiş yapmak isterse, hem Enterprise State Roaming hem de UE-V kullanmanızı öneririz. UE-V ve Enterprise State Roaming benzer teknolojiler olsa da birbirini dışlamayan değildir. Kuruluşunuzun kullanıcılarınızın ihtiyacı olan dolaşım hizmetlerini sağladığından emin olmak için birbirini tamamlarlar.  

Hem Enterprise State Roaming hem de UE-V kullanırken aşağıdaki kurallar geçerlidir:

* Enterprise State Roaming, cihazdaki birincil dolaşım aracısıdır. UE-V, "Win32 Gap" i tamamlamak için kullanılıyor.
* UE-V Grup ilkeleri kullanılırken Windows ayarları için UE-V dolaşımı ve modern UWP uygulama verileri devre dışı bırakılmalıdır. Bunlar zaten Enterprise State Roaming kapsamına alınmıştır.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>Enterprise State Roaming sanal masaüstü altyapısını (VDı) nasıl destekler?

Enterprise State Roaming, Windows 10 istemci SKU 'Larında desteklenir, ancak sunucu SKU 'Larında desteklenmez. Bir istemci VM 'si hiper yönetici makinede barındırılıyorsa ve sanal makinede uzaktan oturum açarsanız, verileriniz dolaşımda bulunur. Birden çok kullanıcı aynı işletim sistemini ve kullanıcıları tam masaüstü deneyimi için bir sunucuda uzaktan oturum açtığında, dolaşım çalışmayabilir. Son oturum tabanlı senaryo resmi olarak desteklenmez.

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>Kuruluşum, dolaşım kullandıktan sonra Azure Rights Management içeren bir abonelik satın aldıysa ne olur?

Kuruluşunuz Windows 10 ' da Azure Rights Management sınırlı kullanım alanı ile daha önce dolaşımı kullanıyorsa, Azure Rights Management koruma hizmeti 'ni içeren [ücretli bir aboneliğin](https://azure.microsoft.com/pricing/details/information-protection/) satın alınması, dolaşım özelliğinin işlevselliğine hiçbir etkiye sahip olmaz ve BT yöneticiniz tarafından hiçbir yapılandırma değişikliği gerekmemektedir.

## <a name="known-issues"></a>Bilinen sorunlar

Bilinen sorunların listesi için [sorun giderme](enterprise-state-roaming-troubleshooting.md) bölümündeki belgelere bakın. 

## <a name="next-steps"></a>Sonraki adımlar 

Genel bakış için bkz. [kuruluş durumu dolaşımına genel bakış](enterprise-state-roaming-overview.md)