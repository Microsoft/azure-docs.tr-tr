---
title: Kanada Federal pbmm şema örnek denetimi eşleme
description: Kanada Federal pbmm şema örneklerinin denetim eşlemesi. Her denetim, değerlendirmenize yardımcı olan bir veya daha fazla Azure Ilkesiyle eşleştirilir.
ms.date: 09/04/2019
ms.topic: sample
ms.openlocfilehash: 0be73497de36ac3c11637b80a492ab0a8558206d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039702"
---
# <a name="control-mapping-of-the-canada-federal-pbmm-blueprint-sample"></a>Kanada Federal pbmm şema örneğinin denetim eşlemesi

Aşağıdaki makalede, Azure şemaları Kanada Federal korunan B, Orta bütünlük, orta kullanılabilirlik (pbmm) şema örneği, Kanada Federal pbmm denetimlerine nasıl eşlenir. Denetimler hakkında daha fazla bilgi için bkz. [Kanada Federal PBMM](https://www.canada.ca/en/government/system/digital-government/modern-emerging-technologies/cloud-services/government-canada-security-control-profile-cloud-based-it-services.html#toc4).

Aşağıdaki eşlemeler **Kanada Federal PBMM** denetimlerine göre yapılır. Sağ taraftaki gezinmeyi kullanarak doğrudan belirli bir denetim eşlemesine atlayın. Eşlenmiş denetimlerin birçoğu bir [Azure Policy](../../../policy/overview.md) girişimi ile uygulanır. Tüm girişimi gözden geçirmek için Azure portal **ilkeyi** açın ve **tanımlar** sayfasını seçin. Ardından **\[önizleme\]bulun ve seçin: Kanada Federal PBMM denetimleri** yerleşik ilke girişim.

> [!IMPORTANT]
> Aşağıdaki her denetim bir veya daha fazla [Azure ilke](../../../policy/overview.md) tanımı ile ilişkilidir. Bu ilkeler, denetimiyle [uyumluluğu değerlendirmenize](../../../policy/how-to/get-compliance-data.md) yardımcı olabilir; Ancak, bir denetim ve bir veya daha fazla ilke arasında genellikle bir 1:1 veya bir eşleşme yoktur. Bu nedenle, Azure Ilkesi ile **uyumlu** , yalnızca ilkelerin kendilerine başvurur; Bu, bir denetimin tüm gereksinimleriyle tamamen uyumlu olduğunuzdan emin değildir. Buna ek olarak, uyumluluk standardı şu anda herhangi bir Azure Ilke tanımı tarafından açıklanmayan denetimler içerir. Bu nedenle, Azure Ilkesinde uyumluluk, genel uyumluluk durumunuzu yalnızca kısmi görünümüdür. Bu uyumluluk şeması örneği için denetimler ve Azure Ilke tanımları arasındaki ilişkilendirmeler zaman içinde değişebilir. Değişiklik geçmişini görüntülemek için [GitHub kayıt geçmişine](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/canada-federal-pbmm/control-mapping.md)bakın.

## <a name="location-constraints"></a>Konum kısıtlamaları

Bu şema, aşağıdaki Azure ilke tanımlarını atayarak tüm kaynakların ve kaynak grupların "Kanada Orta" ve "Kanada Doğu" ile dağıtım konumunu kısıtlamanıza yardımcı olur:

- İzin verilen konumlar ("Kanada Orta" ve "Kanada Doğu" için sabit olarak kodlanmıştır)
- Kaynak grupları için izin verilen konumlar ("Kanada Orta" ve "Kanada Doğu" için sabit olarak kodlanmıştır)

## <a name="ac-2-account-management"></a>AC-2 hesap yönetimi

Bu şema, kuruluşunuzun hesap yönetimi gereksinimleriyle uyumlu olmayan hesapları incelemenizi sağlar. Bu şema, abonelik ve kullanım dışı hesaplar üzerinde okuma, yazma ve sahibi izinleri olan dış hesapları denetleyen [Azure ilke](../../../policy/overview.md) tanımlarını atar. Bu ilkeler tarafından denetlenen hesapları inceleyerek, hesap yönetimi gereksinimlerinin karşılanmasını sağlamak için uygun işlemleri gerçekleştirebilirsiniz.

- Kullanım dışı bırakılan hesaplar aboneliğinizden kaldırılmalıdır
- Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır
- Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır
- Okuma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir
- Yazma izinleri olan dış hesapların aboneliğinizden kaldırılması gerekir


## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) hesap yönetimi | Rol tabanlı şemalar

Azure, Azure 'daki kaynaklara kimlerin erişebileceğini yönetmenize yardımcı olmak için [rol tabanlı erişim denetimi](../../../../role-based-access-control/overview.md) (RBAC) uygular. Azure portal kullanarak, Azure kaynaklarına kimlerin erişebileceğini ve bunların izinlerini gözden geçirebilirsiniz. Bu şema Ayrıca, SQL Server ve Service Fabric için Azure Active Directory kimlik doğrulamasının kullanımını denetlemek üzere [Azure ilke](../../../policy/overview.md) tanımları atar. Azure Active Directory kimlik doğrulaması kullanmak, veritabanı kullanıcıları ve diğer Microsoft Hizmetleri için Basitleştirilmiş izin yönetimi ve merkezi kimlik yönetimine izin verebilir. Ayrıca, bu şema özel RBAC kurallarının kullanımını denetlemek için bir Azure ilke tanımı atar. Özel RBAC kurallarının uygun olduğunu anlamak, özel RBAC kuralları hata durumunda olduğundan emin olmanıza ve doğru uygulamayı doğrulamaya yardımcı olabilir.

- SQL sunucuları için bir Azure Active Directory Yöneticisi sağlanmalıdır
- Service Fabric kümeler yalnızca istemci kimlik doğrulaması için Azure Active Directory kullanmalıdır

## <a name="ac-4-information-flow-enforcement"></a>AC-4 bilgi akışı zorlaması

Çapraz kaynak kaynak paylaşımı (CORS), App Services kaynaklarının bir dış etki alanından istenme izin verebilir. Microsoft, yalnızca gerekli etki alanlarının API, işleviniz ve Web uygulamalarınızla etkileşime geçmesini sağlar. Bu şema, Azure Güvenlik Merkezi 'ndeki CORS kaynakları erişim kısıtlamalarını izlemenize yardımcı olmak için bir [Azure ilke](../../../policy/overview.md) tanımı atar.
CORS uygulamalarını anlamak, bilgi akışı denetimlerinin uygulandığını doğrulamanıza yardımcı olabilir.

- CORS, her kaynağın Web uygulamanıza erişmesine izin vermemelidir

## <a name="ac-5-separation-of-duties"></a>AC-5 Görev ayrımı

Yalnızca bir Azure aboneliğinin sahibi, yönetici artıklığına izin vermez. Bunun tersine, çok fazla sayıda Azure aboneliği sahibi, güvenliği aşılmış bir sahip hesabı aracılığıyla ihlal olasılığını artırabilir. Bu şema, Azure abonelikleri için sahip sayısını denetleyen [Azure ilke](../../../policy/overview.md) tanımlarını atayarak uygun sayıda Azure abonelik sahibini korumanıza yardımcı olur. Bu şema Ayrıca, Windows sanal makinelerinde Yöneticiler grubunun üyeliğini denetlemenize yardımcı olan Azure ilke tanımlarını atar. Abonelik sahibini ve sanal makine yöneticisi izinlerini yönetmek, görevlerin uygun bir şekilde ayrılmasını sağlamanıza yardımcı olabilir.

- Aboneliğiniz için en fazla 3 sahip belirtilmelidir
- Aboneliğinize birden fazla sahip atanmalıdır
- Yöneticiler grubunun belirtilen üyelerden birini içerdiği Windows VM 'lerini denetleyin
- Yöneticiler grubunun belirtilen tüm üyeleri içermediği Windows VM 'lerini denetleyin
- Yöneticiler grubunun belirtilen üyelerden birini içerdiği Windows sanal makinelerini denetlemek için gereksinimleri dağıtın
- Yöneticiler grubunun belirtilen tüm üyeleri içermediği Windows sanal makinelerini denetlemek için gereksinimleri dağıtın

## <a name="ac-6-least-privilege"></a>AC-6 en az ayrıcalık

Azure, Azure 'daki kaynaklara kimlerin erişebileceğini yönetmenize yardımcı olmak için [rol tabanlı erişim denetimi](../../../../role-based-access-control/overview.md) (RBAC) uygular. Azure portal kullanarak, Azure kaynaklarına kimlerin erişebileceğini ve bunların izinlerini gözden geçirebilirsiniz. Bu şema, gözden geçirme için öncelik verilmelidir denetim hesaplarına [Azure ilke](../../../policy/overview.md) tanımları atar. Bu hesap göstergelerini gözden geçirmek, en az ayrıcalık denetimlerinin uygulandığından emin olmanıza yardımcı olabilir.

- Aboneliğiniz için en fazla 3 sahip belirtilmelidir
- Aboneliğinize birden fazla sahip atanmalıdır
- Yöneticiler grubunun belirtilen üyelerden birini içerdiği Windows VM 'lerini denetleyin
- Yöneticiler grubunun belirtilen tüm üyeleri içermediği Windows VM 'lerini denetleyin
- Yöneticiler grubunun belirtilen üyelerden birini içerdiği Windows sanal makinelerini denetlemek için gereksinimleri dağıtın
- Yöneticiler grubunun belirtilen tüm üyeleri içermediği Windows sanal makinelerini denetlemek için gereksinimleri dağıtın

## <a name="ac-7-security-attributes"></a>AC-7 Güvenlik öznitelikleri

Azure SQL veritabanı için gelişmiş veri güvenliği 'nin veri bulma ve sınıflandırma özelliği, veritabanlarınızdaki hassas verileri keşfetme, sınıflandırma, etiketleme ve korumaya yönelik özellikler sağlar. Veri sınıflandırma durumunuz için görünürlük sağlamanın yanı sıra veritabanı içindeki ve dışındaki hassas verilere erişimin izlenmesi için kullanılabilir. Gelişmiş veri güvenliği, kuruluşunuzun uygun güvenlik öznitelikleriyle ilişkili olarak bilgi sağlamanıza yardımcı olabilir. Bu şema, SQL Server 'da gelişmiş veri güvenliği kullanımını izlemek ve zorlamak için [Azure ilke](../../../policy/overview.md) tanımları atar.

- Gelişmiş veri güvenliği, SQL yönetilen örneklerinizin üzerinde etkinleştirilmelidir
- SQL sunucularınızda gelişmiş veri güvenliği etkin olmalıdır
- SQL Server 'lar üzerinde gelişmiş veri güvenliği dağıtma

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) uzaktan erişim | Otomatik Izleme/denetim

Bu şema, Azure App Service uygulaması için uzaktan hata ayıklamanın kapalı olduğunu izlemek üzere [Azure ilke](../../../policy/overview.md) tanımları atayarak uzaktan erişimi izleyip denetlemenize yardımcı olur. Şema Ayrıca, parola olmadan hesaplardan gelen uzak bağlantılara izin veren Linux sanal makinelerini denetleyen ilke tanımları atar. Ayrıca, şema, depolama hesaplarına Kısıtlanmamış erişimi izlemenize yardımcı olan bir Azure ilke tanımı atar. Bu göstergeleri izlemek, uzaktan erişim yöntemlerinin güvenlik ilkenize uyduğundan emin olmanıza yardımcı olabilir.

- \[Preview\]: parola olmadan hesaplardan uzak bağlantılara izin veren Linux VM 'lerini denetleme
- \[Preview\]: parola olmadan hesaplardan uzak bağlantılara izin veren Linux VM 'lerini denetlemek için gereksinimleri dağıtın
- Depolama hesaplarına Kısıtlanmamış ağ erişimini denetleme
- API uygulaması için uzaktan hata ayıklama kapatılmalıdır
- İşlev Uygulaması için uzaktan hata ayıklama kapatılmalıdır
- Uzaktan hata ayıklama için Web uygulaması kapalı olmaları

## <a name="au-3-2-content-of-audit-records"></a>AU-3 (2) denetim kayıtlarının Içeriği

Azure Izleyici tarafından toplanan günlük verileri, merkezi yapılandırma ve yönetimi sağlayan bir Log Analytics çalışma alanında depolanır. Bu şema, Azure sanal makinelerinde Log Analytics aracısının dağıtımını denetleyen ve zorlayacağı [Azure ilke](../../../policy/overview.md) tanımları atayarak olayların günlüğe kaydedilmesini sağlamanıza yardımcı olur.

- \[Preview\]: denetim Log Analytics aracı dağıtımı-VM görüntüsü (OS) listelenmemiş
- \[Preview\]: VMSS-VM görüntüsündeki (OS) denetim Log Analytics aracı dağıtımı listelenmemiş
- \[Önizleme\]: Audit Log Analytics çalışma alanı için VM - uyumsuzluğu bildir
- \[Önizleme\]: Linux sanal makineleri için Log Analytics aracısını dağıtmayı
- \[Önizleme\]: Windows Vm'leri için Log Analytics aracısını dağıtmayı

## <a name="au-5-response-to-audit-processing-failures"></a>AU-denetim Işleme hatalarının 5 yanıtı

Bu şema, denetim ve olay günlüğü yapılandırmasını izleyen [Azure ilke](../../../policy/overview.md) tanımlarını atar. Bu yapılandırmaların izlenmesi, bir denetim sistem hatası veya yanlış yapılandırması göstergesi sağlayabilir ve düzeltici eylem yapmanıza yardımcı olabilir.

- Tanılama ayarını denetle
- SQL Server Level denetim ayarlarını denetleme
- Yönetilen örneklerinizdeki gelişmiş veri güvenliği etkinleştirilmelidir
- SQL sunucularınızda gelişmiş veri güvenliği etkin olmalıdır

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) denetim Incelemesi, analiz ve raporlama | Merkezi Inceleme ve analiz

Azure Izleyici tarafından toplanan günlük verileri, merkezi raporlama ve analizi sağlayan bir Log Analytics çalışma alanında depolanır. Bu şema, Azure sanal makinelerinde Log Analytics aracısının dağıtımını denetleyen ve zorlayacağı [Azure ilke](../../../policy/overview.md) tanımları atayarak olayların günlüğe kaydedilmesini sağlamanıza yardımcı olur.

- \[Preview\]: denetim Log Analytics aracı dağıtımı-VM görüntüsü (OS) listelenmemiş
- \[Preview\]: VMSS-VM görüntüsündeki (OS) denetim Log Analytics aracı dağıtımı listelenmemiş
- \[Önizleme\]: Audit Log Analytics çalışma alanı için VM - uyumsuzluğu bildir
- \[Önizleme\]: Linux sanal makineleri için Log Analytics aracısını dağıtmayı
- \[Önizleme\]: Windows Vm'leri için Log Analytics aracısını dağıtmayı

## <a name="au-12-audit-generation"></a>AU-12 denetim oluşturma

Bu şema, Azure kaynaklarında günlük ayarlarını denetleyen [Azure ilke](../../../policy/overview.md) tanımları atanarak sistem olaylarının günlüğe kaydedildiğinden emin olmanıza yardımcı olur. Bu ilke tanımları, Azure sanal makinelerinde Log Analytics aracısının dağıtımını ve diğer Azure Kaynak türleri için denetim ayarları yapılandırmasını denetler ve uygular. Bu ilke tanımları Ayrıca Azure kaynakları içinde gerçekleştirilen işlemlere ilişkin Öngörüler sağlamak için tanılama günlüklerinin yapılandırmasını denetler. Ayrıca, denetim ve gelişmiş veri güvenliği SQL Server 'lar üzerinde yapılandırılır.

- \[Preview\]: denetim Log Analytics aracı dağıtımı-VM görüntüsü (OS) listelenmemiş
- \[Preview\]: VMSS-VM görüntüsündeki (OS) denetim Log Analytics aracı dağıtımı listelenmemiş
- \[Önizleme\]: Audit Log Analytics çalışma alanı için VM - uyumsuzluğu bildir
- \[Önizleme\]: Linux sanal makineleri için Log Analytics aracısını dağıtmayı
- \[Önizleme\]: Windows Vm'leri için Log Analytics aracısını dağıtmayı
- Tanılama ayarını denetle
- SQL Server Level denetim ayarlarını denetleme
- Yönetilen örneklerinizdeki gelişmiş veri güvenliği etkinleştirilmelidir
- SQL sunucularınızda gelişmiş veri güvenliği etkin olmalıdır
- SQL Server 'lar üzerinde gelişmiş veri güvenliği dağıtma
- SQL Server 'lar üzerinde denetim dağıtma
- Ağ güvenlik grupları için tanılama ayarlarını dağıtma

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) en az Işlevsellik | Yetkili yazılım/beyaz listeleme

Azure Güvenlik Merkezi 'nde Uyarlamalı uygulama denetimi, belirli yazılımların sanal makinelerinizde çalıştırılmasını engelleyebilen veya engelleyebilecek akıllı ve otomatik bir uçtan uca uygulama beyaz listeleme çözümüdür. Uygulama denetimi, sanal makineleriniz için onaylanan uygulama listeleri oluşturmanıza yardımcı olur. Bu şema, bir uygulama beyaz listesinin önerildiği ancak henüz yapılandırılmadığı sanal makineleri izlemenize yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Uyarlamalı uygulama denetimleri sanal makinelerde etkinleştirilmelidir

## <a name="cm-11-user-installed-software"></a>CM-11 Kullanıcı tarafından yüklenen yazılım

Azure Güvenlik Merkezi 'nde Uyarlamalı uygulama denetimi, belirli yazılımların sanal makinelerinizde çalıştırılmasını engelleyebilen veya engelleyebilecek akıllı ve otomatik bir uçtan uca uygulama beyaz listeleme çözümüdür. Uygulama denetimi, yazılım kısıtlama ilkeleriyle uyumluluğu zorlamanıza ve izlemenize yardımcı olabilir. Bu şema, bir uygulama beyaz listesinin önerildiği ancak henüz yapılandırılmadığı sanal makineleri izlemenize yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Uyarlamalı uygulama denetimleri sanal makinelerde etkinleştirilmelidir

## <a name="cp-7-alternate-processing-site"></a>CP-7 alternatif Işleme sitesi

Azure Site Recovery, sanal makinelerde çalışan iş yüklerini birincil bir konumdan ikincil konuma çoğaltır. Birincil sitede bir kesinti oluşursa, iş yükü ikincil konum üzerinde başarısız olur. Bu şema, olağanüstü durum kurtarma olmadan sanal makineleri denetleyen bir [Azure ilke](../../../policy/overview.md) tanımı atar. Bu göstergeyi izlemek, gerekli acil durum denetimlerinin yerinde olduğundan emin olmanıza yardımcı olabilir.

- Olağanüstü durum kurtarma yapılandırması olmadan sanal makineleri denetleme

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) tanımlama ve kimlik doğrulaması (Kurumsal kullanıcılar) | Ayrıcalıklı hesaplara ağ erişimi

Bu şema, çok faktörlü kimlik doğrulaması etkinleştirilmemiş olan sahip ve/veya yazma izinleri olan denetim hesaplarına [Azure ilke](../../../policy/overview.md) tanımları atayarak ayrıcalıklı erişimi kısıtlayıp denetlemenize yardımcı olur. Multi-Factor Authentication, bir dizi kimlik doğrulama bilgisinin tehlikeye düşmesi durumunda bile hesapların güvenli kalmasına yardımcı olur. Multi-Factor Authentication 'ı etkin olmayan hesapları izleyerek, tehlikeye geçmek daha olası olabilecek hesapları belirleyebilirsiniz.

- MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir
- MFA, aboneliğinizde yazma izinleri olan hesaplarda etkinleştirilmelidir

## <a name="ia-5-authenticator-management"></a>IA-5 Authenticator yönetimi

Bu şema, parola olmadan hesaplardan uzak bağlantılara izin veren ve/veya passwd dosyasında yanlış izinlere sahip olan Linux sanal makinelerini denetleyen [Azure ilke](../../../policy/overview.md) tanımlarını atar. Bu şema Ayrıca, Windows sanal makineleri için parola şifreleme türünün yapılandırılmasını denetleyen ilke tanımları atar. Bu göstergeleri izlemek, sistem kimlik doğrulamasının kuruluşunuzun kimlik ve kimlik doğrulama ilkesiyle uyumlu olmasını sağlamanıza yardımcı olur.

- \[Preview\]: passwd dosyası izinleri 0644 olarak ayarlanan Linux VM 'lerini denetleyin
- \[önizleme\]: parolaları olmayan hesaplara sahip Linux VM 'lerini denetleme
- \[Preview\]: passwd dosyası izinleri 0644 olarak ayarlanan Linux sanal makinelerini denetlemek için gereksinimleri dağıtın
- \[Preview\]: parola olmayan hesaplara sahip Linux sanal makinelerini denetlemek için gereksinimleri dağıtın

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) Authenticator yönetimi | Parola tabanlı kimlik doğrulaması

Bu şema, en düşük güç ve diğer parola gereksinimlerini zorlayamama Windows sanal makinelerini denetleyen [Azure ilke](../../../policy/overview.md) tanımlarını atayarak güçlü parolalar zorlamanıza yardımcı olur. Parola gücü ilkesini ihlal eden sanal makinelerin farkında olmak, tüm sanal makine Kullanıcı hesaplarının parolalarının kuruluşunuzun parola ilkesiyle uyumlu olmasını sağlamak için düzeltici eylemler almanıza yardımcı olur.

- \[Preview\]: önceki 24 parolanın yeniden kullanılmasına izin veren Windows VM 'lerini denetleyin
- \[Preview\]: en fazla 70 gün parola yaşı olmayan Windows VM 'Leri denetleyin
- \[Preview\]: en az 1 günlük parola yaşı olmayan Windows VM 'Leri denetleyin
- \[Preview\]: parola karmaşıklığı ayarı etkin olmayan Windows VM 'Leri denetleme
- \[Preview\]: en az parola uzunluğu 14 karakter olan Windows VM 'lerini denetleyin
- \[Preview\]: önceki 24 parolanın yeniden kullanılmasına izin veren Windows sanal makinelerini denetlemek için gereksinimleri dağıtın
- \[Preview\]: en fazla 70 gün parola yaşı olmayan Windows sanal makinelerini denetlemek için gereksinimleri dağıtın
- \[Preview\]: en az 1 günlük parola yaşı olmayan Windows VM 'Leri denetlemek için gereksinimleri dağıtın
- \[Preview\]: parola karmaşıklığı ayarı etkinleştirilmemiş Windows sanal makinelerini denetlemek için gereksinimleri dağıtın
- \[Preview\]: en az parola uzunluğu 14 karakter olan Windows sanal makinelerini denetlemek için gereksinimleri dağıtın

## <a name="ia-8-100-identification-and-authentication-non-organizational-users--identity-and-credential-assurance-levels"></a>IA-8 (100) tanımlama ve kimlik doğrulaması (kurumsal olmayan kullanıcılar) | Kimlik ve kimlik bilgisi güvence düzeyleri

Bu şema, çok faktörlü kimlik doğrulaması etkinleştirilmemiş olan sahip ve/veya yazma izinleri olan denetim hesaplarına [Azure ilke](../../../policy/overview.md) tanımları atayarak ayrıcalıklı erişimi kısıtlayıp denetlemenize yardımcı olur. Multi-Factor Authentication, bir dizi kimlik doğrulama bilgisinin tehlikeye düşmesi durumunda bile hesapların güvenli kalmasına yardımcı olur. Multi-Factor Authentication 'ı etkin olmayan hesapları izleyerek, tehlikeye geçmek daha olası olabilecek hesapları belirleyebilirsiniz.

- MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir
- MFA, aboneliğinizde yazma izinleri olan hesaplarda etkinleştirilmelidir

## <a name="ra-5-vulnerability-scanning"></a>RA-5 güvenlik açığı taraması

Bu şema, Azure Güvenlik Merkezi 'nde işletim sistemi güvenlik açıklarını, SQL güvenlik açıklarını ve sanal makine güvenlik açıklarını izleyen [Azure ilke](../../../policy/overview.md) tanımlarını atayarak bilgi sistemi güvenlik açıklarını yönetmenize yardımcı olur. Azure Güvenlik Merkezi, dağıtılan Azure kaynaklarının güvenlik durumu hakkında gerçek zamanlı Öngörüler elde etme olanağı sunan raporlama özellikleri sağlar. Bu şema Ayrıca, SQL sunucularında gelişmiş veri güvenliğini denetleyen ve uygulayan ilke tanımları da atar. Dağıtılmış kaynaklardaki güvenlik açıklarını anlamanıza yardımcı olmak için gelişmiş veri güvenliğine dahil edilen güvenlik açığı değerlendirmesi ve Gelişmiş tehdit koruması özellikleri.

- Yönetilen örneklerinizdeki gelişmiş veri güvenliği etkinleştirilmelidir
- SQL sunucularınızda gelişmiş veri güvenliği etkin olmalıdır
- SQL Server 'lar üzerinde gelişmiş veri güvenliği dağıtma
- Sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- Sanal makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- SQL veritabanlarınızdaki güvenlik açıkları düzeltilmelidir
- Güvenlik açıkları bir güvenlik açığı değerlendirme çözümü tarafından düzeltilmelidir

## <a name="sc-5-denial-of-service-protection"></a>SC-5 hizmet reddi koruması

Azure 'un dağıtılmış hizmet reddi (DDoS) standart katmanı, temel hizmet katmanı üzerinden ek özellikler ve hafifletme özellikleri sağlar. Bu ek özellikler, Azure Izleyici tümleştirmesini ve saldırı sonrası risk azaltma raporlarını inceleme olanağını içerir. Bu şema, DDoS standart katmanının etkinleştirilip etkinleştirilmediğini denetleyen bir [Azure ilke](../../../policy/overview.md) tanımı atar. Hizmet katmanları arasındaki yetenek farkını anlamak, Azure ortamınız için hizmet korumalarının reddedilmesine yönelik en iyi çözümü seçmenize yardımcı olabilir.

- DDoS koruma standardı etkinleştirilmelidir

## <a name="sc-7-boundary-protection"></a>SC-7 sınır koruması

Bu şema, Azure Güvenlik Merkezi 'nde ağ güvenlik grubu sağlamlaştırma önerilerini izleyen bir [Azure ilke](../../../policy/overview.md) tanımı atayarak sistem sınırını yönetmenize ve denetlemenize yardımcı olur. Azure Güvenlik Merkezi, Internet 'e yönelik sanal makinelerin trafik düzenlerini analiz eder ve olası saldırı yüzeyini azaltmak için ağ güvenlik grubu kuralı önerileri sağlar.
Ayrıca, bu şema korunmayan uç noktaları, uygulamalar ve depolama hesaplarını izleyen ilke tanımları da atar. Bir güvenlik duvarı tarafından korunmayan uç noktalar ve uygulamalar ve Kısıtlanmamış erişimi olan depolama hesapları, bilgi sisteminde bulunan bilgilere istenmeden erişime izin verebilir.

- Internet 'e yönelik sanal makineler için ağ güvenlik grubu kuralları sağlamlaştırılmış olmalıdır
- Internet 'e yönelik uç nokta ile erişim kısıtlı olmalıdır
- Depolama hesaplarına Kısıtlanmamış ağ erişimini denetleme
- IaaS üzerinde Web uygulamalarına yönelik NSG kuralları sağlamlaştırılmış olmalıdır

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) sınır koruması | Erişim noktaları

Tam zamanında (JıT) sanal makine erişimi, Azure sanal makinelerine giden trafiği kilitler ve gerektiğinde VM 'lere bağlanmak için kolay erişim sağlarken saldırılara maruz kalmayı azaltır. JıT sanal makine erişimi, Azure 'daki kaynaklarınıza yönelik dış bağlantı sayısını sınırlamanıza yardımcı olur. Bu şema, tam zamanında erişimi destekleyebilen ancak henüz yapılandırılmadığı sanal makineleri izlemenize yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Tam zamanında ağ erişim denetimi, sanal makinelere uygulanmalıdır

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) sınır koruması | Dış telekomünikasyon hizmetleri

Tam zamanında (JıT) sanal makine erişimi, Azure sanal makinelerine giden trafiği kilitler ve gerektiğinde VM 'lere bağlanmak için kolay erişim sağlarken saldırılara maruz kalmayı azaltır. JıT sanal makine erişimi, erişim isteği ve onay süreçlerini kolaylaştırarak trafik akışı ilkenizin özel durumlarını yönetmenize yardımcı olur. Bu şema, tam zamanında erişimi destekleyebilen ancak henüz yapılandırılmadığı sanal makineleri izlemenize yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Tam zamanında ağ erişim denetimi, sanal makinelere uygulanmalıdır

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>SC-8 (1) Iletim gizliliği ve bütünlüğü | Şifreleme veya alternatif fiziksel koruma

Bu şema, iletişim protokolleri için uygulanan şifreleme mekanizmasını izlemenize yardımcı olan [Azure ilke](../../../policy/overview.md) tanımlarını atayarak, iletilen bilgilerin gizli ve bütünlüğünü korumanıza yardımcı olur. İletişimin düzgün şekilde şifrelendiğinden emin olmak, kuruluşunuzun gereksinimlerini karşılamanıza veya bilgilerin yetkisiz olarak açıklanmasını ve değiştirilmesini sağlamanıza yardımcı olabilir.

- API uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır
- Güvenli iletişim protokolleri kullanmayan Windows Web sunucularını denetleme
- Güvenli iletişim protokolleri kullanmayan Windows Web sunucularını denetlemek için gereksinimleri dağıtma
- İşlev uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır
- Yalnızca Redis Cache güvenli bağlantılar etkinleştirilmelidir
- Web uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır
- Depolama hesaplarına Güvenli aktarım etkinleştirilmelidir

## <a name="sc-28-1-protection-of-information-at-rest"></a>SC-28 (1) bekleyen bilgilerin koruması

Bu şema, belirli bir cryptograph denetimi uygulayan [Azure ilke](../../../policy/overview.md) tanımlarını atayarak ve zayıf şifreleme ayarlarının kullanımını denetleyerek, bu şemayı, geri kalan bilgileri korumak için cryptograph denetimleri kullanma konusunda zorlamanıza yardımcı olur. Azure kaynaklarınızın en iyi durumda olmayan şifreleme yapılandırmalarının nerede olabileceğini anlamak, kaynakların bilgi güvenliği ilkenize uygun şekilde yapılandırıldığından emin olmak için düzeltici eylemler almanıza yardımcı olabilir. Özellikle, bu şema tarafından atanan ilke tanımları Data Lake Storage hesapları için şifrelemeyi gerektirir; SQL veritabanlarında saydam veri şifrelemesi gerektir; ve SQL veritabanlarında, sanal makine disklerinde ve Otomasyon hesabı değişkenlerinde eksik şifrelemeyi denetleyin.

- Yönetilen örneklerinizdeki gelişmiş veri güvenliği etkinleştirilmelidir
- SQL sunucularınızda gelişmiş veri güvenliği etkin olmalıdır
- SQL Server 'lar üzerinde gelişmiş veri güvenliği dağıtma
- SQL DB saydam veri şifrelemesini dağıtma
- Disk şifrelemesi sanal makinelere uygulanmalıdır
- Data Lake Store hesaplarında şifreleme gerektir
- SQL veritabanlarındaki Saydam Veri Şifrelemesi etkinleştirilmelidir

## <a name="si-2-flaw-remediation"></a>SI-2 hata düzeltmesi

Bu şema, Azure Güvenlik 'teki eksik sistem güncelleştirmelerini, işletim sistemi güvenlik açıklarını, SQL güvenlik açıklarını ve sanal makine güvenlik açıklarını izleyen [Azure ilke](../../../policy/overview.md) tanımlarını atayarak bilgi sistemi kusurlarını yönetmenize yardımcı olur Merkezinden. Azure Güvenlik Merkezi, dağıtılan Azure kaynaklarının güvenlik durumu hakkında gerçek zamanlı Öngörüler elde etme olanağı sunan raporlama özellikleri sağlar. Bu şema Ayrıca, sanal makine ölçek kümeleri için işletim sisteminin yama yapılmasını sağlayan bir ilke tanımı atar.

- Sanal makine ölçek kümelerinde otomatik işletim sistemi görüntüsü düzeltme eki uygulamayı gerektir
- Sanal makine ölçek kümelerindeki sistem güncelleştirmeleri yüklenmelidir
- Sistem güncelleştirmelerinin sanal makinelerinizde yüklü olması gerekir
- Sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- Sanal makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- SQL veritabanlarınızdaki güvenlik açıkları düzeltilmelidir
- Güvenlik açıkları bir güvenlik açığı değerlendirme çözümü tarafından düzeltilmelidir

## <a name="si-3-malicious-code-protection"></a>SI-3 kötü amaçlı kod koruması

Bu şema, Azure Güvenlik Merkezi 'nde sanal makinelerde eksik uç nokta koruması için izleme yapan [Azure ilke](../../../policy/overview.md) tanımlarını atayarak ve Microsoft 'a zorlamak için kötü amaçlı kod koruma dahil, Endpoint Protection 'ı yönetmenize yardımcı olur. Windows sanal makinelerinde kötü amaçlı yazılımdan koruma çözümü.

- Windows Server için varsayılan Microsoft ıaasantimalware uzantısını dağıt
- Uç nokta koruma çözümü, sanal makine ölçek kümelerine yüklenmelidir
- Azure Güvenlik Merkezi 'nde eksik Endpoint Protection izleme

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) kötü amaçlı kod koruması | Merkezi Yönetim

Bu şema, Azure Güvenlik Merkezi 'ndeki sanal makinelerde eksik uç nokta koruması için izleme yapan [Azure ilke](../../../policy/overview.md) tanımlarını atayarak kötü amaçlı kod koruma dahil, Endpoint Protection 'ı yönetmenize yardımcı olur. Azure Güvenlik Merkezi, dağıtılan Azure kaynaklarının güvenlik durumuyla ilgili gerçek zamanlı Öngörüler elde etme olanağı sunan merkezi yönetim ve raporlama özellikleri sağlar.

- Uç nokta koruma çözümü, sanal makine ölçek kümelerine yüklenmelidir
- Azure Güvenlik Merkezi 'nde eksik Endpoint Protection izleme

## <a name="si-4-information-system-monitoring"></a>SI-4 bilgi sistemi Izleme

Bu şema, Azure kaynakları arasında günlük ve veri güvenliğini denetleyerek ve zorunlu tutarak sisteminizi izlemenize yardımcı olur. Özellikle, Log Analytics aracısının dağıtımını denetleme ve uygulamaya zorlama ve SQL veritabanları, depolama hesapları ve ağ kaynakları için gelişmiş güvenlik ayarları atanmış ilkeleridir. Bu yetenekler, uygun işlemleri yapabilmeniz için anormal davranışları ve saldırı göstergelerini tespit etmenize yardımcı olabilir.

- \[Preview\]: denetim Log Analytics aracı dağıtımı-VM görüntüsü (OS) listelenmemiş
- \[Preview\]: VMSS-VM görüntüsündeki (OS) denetim Log Analytics aracı dağıtımı listelenmemiş
- \[Önizleme\]: Audit Log Analytics çalışma alanı için VM - uyumsuzluğu bildir
- \[Önizleme\]: Linux sanal makineleri için Log Analytics aracısını dağıtmayı
- \[Önizleme\]: Windows Vm'leri için Log Analytics aracısını dağıtmayı
- Yönetilen örneklerinizdeki gelişmiş veri güvenliği etkinleştirilmelidir
- SQL sunucularınızda gelişmiş veri güvenliği etkin olmalıdır
- SQL Server 'lar üzerinde gelişmiş veri güvenliği dağıtma
- Depolama hesaplarında Gelişmiş tehdit koruması dağıtma
- SQL Server 'lar üzerinde denetim dağıtma
- Sanal ağlar oluşturulduğunda Ağ İzleyicisi dağıt
- SQL Server 'lar üzerinde tehdit algılamayı dağıtma

> [!NOTE]
> Belirli Azure Ilke tanımlarının kullanılabilirliği, Azure Kamu ve diğer ulusal bulutlarda farklılık gösterebilir. 

## <a name="next-steps"></a>Sonraki adımlar

Kanada Federal pbmm şema örneğinin denetim eşlemesini gözden geçirdiniz. Daha sonra, genel bakış ve bu örneği dağıtma hakkında bilgi edinmek için aşağıdaki makaleleri ziyaret edin:

> [!div class="nextstepaction"]
> [Kanada Federal pbmm şema-genel bakış](./control-mapping.md)
> [Kanada Federal pbmm şema-Deploy adımları](./deploy.md)

Şemalar ve bunların kullanımı hakkındaki diğer makaleler:

- [Şema yaşam döngüsü](../../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../../how-to/update-existing-assignments.md) öğrenin.