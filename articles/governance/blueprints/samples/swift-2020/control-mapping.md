---
title: Örnek-SWIFT CSP-cscf V2020 şema-Control eşleme
description: SWIFT CSP-cscf V2020 şema örneğinin denetim eşlemesi. Her denetim, değerlendirmenize yardımcı olan bir veya daha fazla Azure Ilkesiyle eşleştirilir.
ms.date: 09/24/2019
ms.topic: sample
ms.openlocfilehash: 0e5b45ec86acbe7c55aed0aa1611f499316e9118
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037835"
---
# <a name="control-mapping-of-the-swift-csp-cscf-v2020-blueprint-sample"></a>SWIFT CSP-cscf V2020 şema örneğinin denetim eşlemesi

Aşağıdaki makalede, Azure şemaları Swift CSP-cscf V2020 şema örneği 'nin Swift CSP-cscf V2020 denetimlerine nasıl eşleştiği açıklanır. Denetimler hakkında daha fazla bilgi için bkz. [SWIFT CSP-CSCF V2020](https://www.swift.com/myswift/customer-security-programme-csp).

Aşağıdaki eşlemeler **SWIFT CSP-CSCF V2020** denetimlerine göre yapılır. Sağ taraftaki gezinmeyi kullanarak doğrudan belirli bir denetim eşlemesine atlayın. Eşlenmiş denetimlerin birçoğu bir [Azure Policy](../../../policy/overview.md) girişimi ile uygulanır. Tüm girişimi gözden geçirmek için Azure portal **ilkeyi** açın ve **tanımlar** sayfasını seçin. Ardından\[önizleme\]bulun ve seçin: denetim gereksinimleri yerleşik ilke girişimi ' ni **desteklemek IÇIN SWIFT CSP-CSCF V2020 denetimlerini denetleme ve belırlı VM uzantılarını dağıtma** .

> [!IMPORTANT]
> Aşağıdaki her denetim bir veya daha fazla [Azure ilke](../../../policy/overview.md) tanımı ile ilişkilidir. Bu ilkeler, denetimiyle [uyumluluğu değerlendirmenize](../../../policy/how-to/get-compliance-data.md) yardımcı olabilir; Ancak, bir denetim ve bir veya daha fazla ilke arasında genellikle bir 1:1 veya bir eşleşme yoktur. Bu nedenle, Azure Ilkesi ile **uyumlu** , yalnızca ilkelerin kendilerine başvurur; Bu, bir denetimin tüm gereksinimleriyle tamamen uyumlu olduğunuzdan emin değildir. Buna ek olarak, uyumluluk standardı şu anda herhangi bir Azure Ilke tanımı tarafından açıklanmayan denetimler içerir. Bu nedenle, Azure Ilkesinde uyumluluk, genel uyumluluk durumunuzu yalnızca kısmi görünümüdür. Bu uyumluluk şeması örneği için denetimler ve Azure Ilke tanımları arasındaki ilişkilendirmeler zaman içinde değişebilir. Değişiklik geçmişini görüntülemek için [GitHub kayıt geçmişine](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/swift-2020/control-mapping.md)bakın.

## <a name="12-and-51-account-management"></a>1,2 ve 5,1 hesap yönetimi

Bu şema, kuruluşunuzun hesap yönetimi gereksinimleriyle uyumlu olmayan hesapları incelemenizi sağlar. Bu şema, abonelik ve kullanım dışı hesaplar üzerinde okuma, yazma ve sahibi izinleri olan dış hesapları denetleyen [Azure ilke](../../../policy/overview.md) tanımlarını atar. Bu ilkeler tarafından denetlenen hesapları inceleyerek, hesap yönetimi gereksinimlerinin karşılanmasını sağlamak için uygun işlemleri gerçekleştirebilirsiniz.

- Kullanım dışı bırakılan hesaplar aboneliğinizden kaldırılmalıdır
- Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır
- Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır
- Okuma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir
- Yazma izinleri olan dış hesapların aboneliğinizden kaldırılması gerekir

## <a name="26-51-64-and-65a-account-management--role-based-schemes"></a>2,6, 5,1, 6,4 ve 6,5 hesap yönetimi | Rol tabanlı şemalar

Azure, Azure 'daki kaynaklara kimlerin erişebileceğini yönetmenize yardımcı olmak için [rol tabanlı erişim denetimi](../../../../role-based-access-control/overview.md) (RBAC) uygular. Azure portal kullanarak, Azure kaynaklarına kimlerin erişebileceğini ve bunların izinlerini gözden geçirebilirsiniz. Bu şema Ayrıca, SQL Server ve Service Fabric için Azure Active Directory kimlik doğrulamasının kullanımını denetlemek üzere [Azure ilke](../../../policy/overview.md) tanımları atar. Azure Active Directory kimlik doğrulaması kullanmak, veritabanı kullanıcıları ve diğer Microsoft Hizmetleri için Basitleştirilmiş izin yönetimi ve merkezi kimlik yönetimine izin verebilir. Ayrıca, bu şema özel RBAC kurallarının kullanımını denetlemek için bir Azure ilke tanımı atar. Özel RBAC kurallarının hangi noktada uygulanacağını anlamak, özel RBAC kuralları hata durumunda olduğundan, gereksinimi ve uygun uygulamayı doğrulamanıza yardımcı olabilir.

- SQL sunucuları için bir Azure Active Directory Yöneticisi sağlanmalıdır
- Özel RBAC kurallarının kullanımını denetleme
- Service Fabric kümeler yalnızca istemci kimlik doğrulaması için Azure Active Directory kullanmalıdır

## <a name="29a--account-management--account-monitoring--atypical-usage"></a>2.9 a Account Management | Hesap Izleme/genel kullanım

Tam zamanında (JıT) sanal makine erişimi, Azure sanal makinelerine giden trafiği kilitler ve gerektiğinde VM 'lere bağlanmak için kolay erişim sağlarken saldırılara maruz kalmayı azaltır. Sanal makinelere erişim için tüm JıT istekleri etkinlik günlüğüne kaydedilir ve bu da genel kullanım için izleme sağlar. Bu şema, tam zamanında erişimi destekleyebilen ancak henüz yapılandırılmamış sanal makineleri izlemenize yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Tam zamanında ağ erişim denetimi, sanal makinelere uygulanmalıdır

## <a name="13-51-and-64-separation-of-duties"></a>1,3, 5,1 ve 6,4, görevlerin ayrımı

Yalnızca bir Azure aboneliğinin sahibi, yönetici artıklığına izin vermez. Bunun tersine, çok fazla sayıda Azure aboneliği sahibi, güvenliği aşılmış bir sahip hesabı aracılığıyla ihlal olasılığını artırabilir. Bu şema, Azure abonelikleri için sahip sayısını denetleyen [Azure ilke](../../../policy/overview.md) tanımlarını atayarak uygun sayıda Azure abonelik sahibini korumanıza yardımcı olur. Bu şema Ayrıca, Windows sanal makinelerinde Yöneticiler grubunun üyeliğini denetlemenize yardımcı olan Azure ilke tanımlarını atar. Abonelik sahibini ve sanal makine yöneticisi izinlerini yönetmek, görevlerin uygun bir şekilde ayrılmasını sağlamanıza yardımcı olabilir.

- Aboneliğiniz için en fazla 3 sahip belirtilmelidir
- Yöneticiler grubunun belirtilen tüm üyeleri içermediği Windows VM 'lerinden denetim sonuçlarını göster
- Yöneticiler grubunun belirtilen tüm üyeleri içermediği Windows sanal makinelerini denetlemek için önkoşulları dağıtın
- Aboneliğinize birden fazla sahip atanmalıdır

## <a name="13-51-and-64-least-privilege--review-of-user-privileges"></a>1,3, 5,1 ve 6,4 en az ayrıcalık | Kullanıcı Ayrıcalıklarını Gözden geçirme

Azure, Azure 'daki kaynaklara kimlerin erişebileceğini yönetmenize yardımcı olmak için [rol tabanlı erişim denetimi](../../../../role-based-access-control/overview.md) (RBAC) uygular. Azure portal kullanarak, Azure kaynaklarına kimlerin erişebileceğini ve bunların izinlerini gözden geçirebilirsiniz. Bu şema, gözden geçirme için öncelik verilmelidir denetim hesaplarına [Azure ilke](../../../policy/overview.md) tanımları atar. Bu hesap göstergelerini gözden geçirmek, en az ayrıcalık denetimlerinin uygulandığından emin olmanıza yardımcı olabilir.

- Aboneliğiniz için en fazla 3 sahip belirtilmelidir
- Yöneticiler grubunun belirtilen tüm üyeleri içermediği Windows VM 'lerinden denetim sonuçlarını göster
- Yöneticiler grubunun belirtilen tüm üyeleri içermediği Windows sanal makinelerini denetlemek için önkoşulları dağıtın
- Aboneliğinize birden fazla sahip atanmalıdır

## <a name="22-and-27-security-attributes"></a>2,2 ve 2,7 Güvenlik öznitelikleri

Azure SQL veritabanı için gelişmiş veri güvenliği 'nin veri bulma ve sınıflandırma özelliği, veritabanlarınızdaki hassas verileri keşfetme, sınıflandırma, etiketleme ve korumaya yönelik özellikler sağlar. Veri sınıflandırma durumunuz için görünürlük sağlamanın yanı sıra veritabanı içindeki ve dışındaki hassas verilere erişimin izlenmesi için kullanılabilir. Gelişmiş veri güvenliği, kuruluşunuzun uygun güvenlik öznitelikleriyle ilişkili olarak bilgi sağlamanıza yardımcı olabilir. Bu şema, SQL Server 'da gelişmiş veri güvenliği kullanımını izlemek ve zorlamak için [Azure ilke](../../../policy/overview.md) tanımları atar. 

- SQL sunucularınızda gelişmiş veri güvenliği etkin olmalıdır
- SQL Server 'lar üzerinde gelişmiş veri güvenliği dağıtma

## <a name="22-27-41-and-61-remote-access--automated-monitoring--control"></a>2,2, 2,7, 4,1 ve 6,1 uzaktan erişim | Otomatik Izleme/denetim

Bu şema, Azure App Service uygulaması için uzaktan hata ayıklamanın kapalı olduğunu ve uzak makineye izin veren Linux sanal makinelerini denetleyen [ilke tanımlarını,](../../../policy/overview.md) uzaktan erişimi izleyip denetlemenize yardımcı olur. parolasız olan hesapların bağlantıları. Bu şema ayrıca depolama hesaplarına Kısıtlanmamış erişimi izlemenize yardımcı olan bir Azure ilke tanımı atar. Bu göstergeleri izlemek, uzaktan erişim yöntemlerinin güvenlik ilkenize uyduğundan emin olmanıza yardımcı olabilir.

- \[Preview\]: parola olmadan hesaplardan uzak bağlantılara izin veren Linux VM 'lerinden denetim sonuçlarını gösterme
- \[Preview\]: parola olmadan hesaplardan uzak bağlantılara izin veren Linux sanal makinelerini denetlemek için önkoşulları dağıtın
- Depolama hesaplarına Kısıtlanmamış ağ erişimini denetleme
- API uygulaması için uzaktan hata ayıklama kapatılmalıdır
- İşlev Uygulaması için uzaktan hata ayıklama kapatılmalıdır
- Uzaktan hata ayıklama için Web uygulaması kapalı olmaları

## <a name="13-and-64-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>Denetim kayıtlarının 1,3 ve 6,4 Içeriği | Planlı denetim kaydı Içeriğinin Merkezi Yönetimi

Azure Izleyici tarafından toplanan günlük verileri, merkezi yapılandırma ve yönetimi sağlayan bir Log Analytics çalışma alanında depolanır. Bu şema, Azure sanal makinelerinde Log Analytics aracısının dağıtımını denetleyen ve zorlayacağı [Azure ilke](../../../policy/overview.md) tanımları atayarak olayların günlüğe kaydedilmesini sağlamanıza yardımcı olur.

- \[Preview\]: denetim Log Analytics aracı dağıtımı-VM görüntüsü (OS) listelenmemiş
- \[Preview\]: Linux VM Ölçek Kümeleri için Log Analytics aracısı dağıtma (VMSS)
- \[Önizleme\]: Linux sanal makineleri için Log Analytics aracısını dağıtmayı
- \[Preview\]: Windows VM Ölçek Kümeleri için Log Analytics aracısı dağıtma (VMSS)
- \[Önizleme\]: Windows Vm'leri için Log Analytics aracısını dağıtmayı

## <a name="22-27-and-64-response-to-audit-processing-failures"></a>Işleme başarısızlıklarını denetlemek için 2,2, 2,7 ve 6,4 yanıtı

Bu şema, denetim ve olay günlüğü yapılandırmasını izleyen [Azure ilke](../../../policy/overview.md) tanımlarını atar. Bu yapılandırmaların izlenmesi, bir denetim sistem hatası veya yanlış yapılandırması göstergesi sağlayabilir ve düzeltici eylem yapmanıza yardımcı olabilir.

- SQL sunucularınızda gelişmiş veri güvenliği etkin olmalıdır
- Tanılama ayarını denetle
- SQL Server 'da denetim dağıtma

## <a name="13-and-64-audit-review-analysis-and-reporting--central-review-and-analysis"></a>1,3 ve 6,4 denetim Inceleme, analiz ve raporlama | Merkezi Inceleme ve analiz

Azure Izleyici tarafından toplanan günlük verileri, merkezi raporlama ve analizi sağlayan bir Log Analytics çalışma alanında depolanır. Bu şema, Azure sanal makinelerinde Log Analytics aracısının dağıtımını denetleyen ve zorlayacağı [Azure ilke](../../../policy/overview.md) tanımları atayarak olayların günlüğe kaydedilmesini sağlamanıza yardımcı olur.

- \[Preview\]: denetim Log Analytics aracı dağıtımı-VM görüntüsü (OS) listelenmemiş
- \[Preview\]: Linux VM Ölçek Kümeleri için Log Analytics aracısı dağıtma (VMSS)
- \[Önizleme\]: Linux sanal makineleri için Log Analytics aracısını dağıtmayı
- \[Preview\]: Windows VM Ölçek Kümeleri için Log Analytics aracısı dağıtma (VMSS)
- \[Önizleme\]: Windows Vm'leri için Log Analytics aracısını dağıtmayı

## <a name="13-22-27-64-and-65a-audit-generation"></a>1,3, 2,2, 2,7, 6,4 ve 6,5 bir denetim oluşturma

Bu şema, Azure kaynaklarında günlük ayarlarını denetleyen [Azure ilke](../../../policy/overview.md) tanımları atanarak sistem olaylarının günlüğe kaydedildiğinden emin olmanıza yardımcı olur. Bu ilke tanımları, Azure sanal makinelerinde Log Analytics aracısının dağıtımını ve diğer Azure Kaynak türleri için denetim ayarları yapılandırmasını denetler ve uygular. Bu ilke tanımları Ayrıca Azure kaynakları içinde gerçekleştirilen işlemlere ilişkin Öngörüler sağlamak için tanılama günlüklerinin yapılandırmasını denetler. Ayrıca, denetim ve gelişmiş veri güvenliği SQL Server 'lar üzerinde yapılandırılır.

- \[Preview\]: denetim Log Analytics aracı dağıtımı-VM görüntüsü (OS) listelenmemiş
- \[Preview\]: Linux VM Ölçek Kümeleri için Log Analytics aracısı dağıtma (VMSS)
- \[Önizleme\]: Linux sanal makineleri için Log Analytics aracısını dağıtmayı
- \[Preview\]: Windows VM Ölçek Kümeleri için Log Analytics aracısı dağıtma (VMSS)
- \[Önizleme\]: Windows Vm'leri için Log Analytics aracısını dağıtmayı
- Tanılama ayarını denetle
- SQL Server Level denetim ayarlarını denetleme
- SQL sunucularınızda gelişmiş veri güvenliği etkin olmalıdır
- SQL Server 'lar üzerinde gelişmiş veri güvenliği dağıtma
- SQL Server 'lar üzerinde denetim dağıtma
- Ağ güvenlik grupları için tanılama ayarlarını dağıtma

## <a name="11-least-functionality--prevent-program-execution"></a>1,1 en az Işlevsellik | Programın yürütülmesini engelle

Azure Güvenlik Merkezi 'nde Uyarlamalı uygulama denetimi, belirli yazılımların sanal makinelerinizde çalıştırılmasını engelleyebilen veya engelleyebilecek akıllı ve otomatik bir uçtan uca uygulama beyaz listeleme çözümüdür. Uygulama denetimi, onaylanmamış uygulamanın çalışmasını engelleyen bir zorlama modunda çalışabilir. Bu şema, bir uygulama beyaz listesinin önerildiği ancak henüz yapılandırılmadığı sanal makineleri izlemenize yardımcı olan bir Azure ilke tanımı atar.

- Uyarlamalı uygulama denetimleri sanal makinelerde etkinleştirilmelidir

## <a name="11-least-functionality--authorized-software--whitelisting"></a>1,1 en az Işlevsellik | Yetkili yazılım/beyaz listeleme

Azure Güvenlik Merkezi 'nde Uyarlamalı uygulama denetimi, belirli yazılımların sanal makinelerinizde çalıştırılmasını engelleyebilen veya engelleyebilecek akıllı ve otomatik bir uçtan uca uygulama beyaz listeleme çözümüdür. Uygulama denetimi, sanal makineleriniz için onaylanan uygulama listeleri oluşturmanıza yardımcı olur. Bu şema, bir uygulama beyaz listesinin önerildiği ancak henüz yapılandırılmadığı sanal makineleri izlemenize yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Uyarlamalı uygulama denetimleri sanal makinelerde etkinleştirilmelidir

## <a name="11-user-installed-software"></a>1,1 Kullanıcı tarafından yüklenen yazılım

Azure Güvenlik Merkezi 'nde Uyarlamalı uygulama denetimi, belirli yazılımların sanal makinelerinizde çalıştırılmasını engelleyebilen veya engelleyebilecek akıllı ve otomatik bir uçtan uca uygulama beyaz listeleme çözümüdür. Uygulama denetimi, yazılım kısıtlama ilkeleriyle uyumluluğu zorlamanıza ve izlemenize yardımcı olabilir. Bu şema, bir uygulama beyaz listesinin önerildiği ancak henüz yapılandırılmadığı sanal makineleri izlemenize yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Uyarlamalı uygulama denetimleri sanal makinelerde etkinleştirilmelidir

## <a name="42-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>4,2 tanımlama ve kimlik doğrulaması (Kurumsal kullanıcılar) | Ayrıcalıklı hesaplara ağ erişimi

Bu şema, çok faktörlü kimlik doğrulaması etkinleştirilmemiş olan sahip ve/veya yazma izinleri olan denetim hesaplarına [Azure ilke](../../../policy/overview.md) tanımları atayarak ayrıcalıklı erişimi kısıtlayıp denetlemenize yardımcı olur. Multi-Factor Authentication, bir dizi kimlik doğrulama bilgisinin tehlikeye düşmesi durumunda bile hesapların güvenli kalmasına yardımcı olur. Multi-Factor Authentication 'ı etkin olmayan hesapları izleyerek, tehlikeye geçmek daha olası olabilecek hesapları belirleyebilirsiniz.

- MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir
- MFA, aboneliğinizde yazma izinleri olan hesaplarda etkinleştirilmelidir

## <a name="42-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>4,2 tanımlama ve kimlik doğrulaması (Kurumsal kullanıcılar) | Ayrıcalıklı olmayan hesaplara ağ erişimi

Bu şema, çok faktörlü kimlik doğrulaması etkinleştirilmemiş okuma izinleriyle hesapları denetlemek için bir [Azure ilke](../../../policy/overview.md) tanımı atayarak erişimi kısıtlayıp denetlemenize yardımcı olur. Multi-Factor Authentication, bir dizi kimlik doğrulama bilgisinin tehlikeye düşmesi durumunda bile hesapların güvenli kalmasına yardımcı olur. Multi-Factor Authentication 'ı etkin olmayan hesapları izleyerek, tehlikeye geçmek daha olası olabilecek hesapları belirleyebilirsiniz.

- MFA, aboneliğinizde okuma izinleri olan hesaplarda etkinleştirilmelidir

## <a name="23-and-41-authenticator-management"></a>2,3 ve 4,1 Authenticator yönetimi

Bu şema, parola olmadan hesaplardan uzak bağlantılara izin veren ve/veya passwd dosyasında yanlış izinlere sahip olan Linux sanal makinelerini denetleyen [Azure ilke](../../../policy/overview.md) tanımlarını atar. Bu şema Ayrıca, Windows sanal makineleri için parola şifreleme türünün yapılandırılmasını denetleyen ilke tanımları atar. Bu göstergeleri izlemek, sistem kimlik doğrulamasının kuruluşunuzun kimlik ve kimlik doğrulama ilkesiyle uyumlu olmasını sağlamanıza yardımcı olur.

- \[Preview\]: passwd dosyası izinleri 0644 olarak ayarlanan Linux VM 'lerinden denetim sonuçlarını göster
- \[Preview\]: passwd dosyası izinleri 0644 olarak ayarlanan Linux sanal makinelerini denetlemek için gereksinimleri dağıtın
- \[önizleme\]: parolaları olmayan hesaplara sahip Linux VM 'lerinden denetim sonuçlarını gösterme
- \[Preview\]: parola olmayan hesaplara sahip Linux sanal makinelerini denetlemek için gereksinimleri dağıtın
- \[önizleme\]: ters çevrilebilir şifreleme kullanarak parolaları depomayan Windows VM 'lerinden denetim sonuçlarını gösterme
- \[Preview\]: ters çevrilebilir şifreleme kullanarak parolaları depolamamayan Windows sanal makinelerini denetlemek için gereksinimleri dağıtın

## <a name="23-and-41-authenticator-management--password-based-authentication"></a>2,3 ve 4,1 Authenticator yönetimi | Parola tabanlı kimlik doğrulaması

Bu şema, en düşük güç ve diğer parola gereksinimlerini zorlayamama Windows sanal makinelerini denetleyen [Azure ilke](../../../policy/overview.md) tanımlarını atayarak güçlü parolalar zorlamanıza yardımcı olur. Parola gücü ilkesini ihlal eden sanal makinelerin farkında olmak, tüm sanal makine Kullanıcı hesaplarının parolalarının kuruluşunuzun parola ilkesiyle uyumlu olmasını sağlamak için düzeltici eylemler almanıza yardımcı olur.

- \[Preview\]: önceki 24 parolanın yeniden kullanılmasına izin veren Windows VM 'lerinden denetim sonuçlarını gösterme
- \[Preview\]: en fazla 70 gün parola yaşı olmayan Windows VM 'lerinden denetim sonuçlarını göster
- \[Preview\]: en az 1 günlük parola yaşı olmayan Windows VM 'lerinden denetim sonuçlarını göster
- \[Preview\]: parola karmaşıklığı ayarı etkinleştirilmemiş Windows VM 'lerinden denetim sonuçlarını göster
- \[Preview\]: en düşük parola uzunluğunu 14 karakter olarak kısıtlayan Windows VM 'lerinden denetim sonuçlarını göster
- \[önizleme\]: ters çevrilebilir şifreleme kullanarak parolaları depomayan Windows VM 'lerinden denetim sonuçlarını gösterme
- \[Preview\]: önceki 24 parolanın yeniden kullanılmasına izin veren Windows sanal makinelerini denetlemek için önkoşulları dağıtın
- \[Preview\]: en fazla 70 gün parola yaşı olmayan Windows sanal makinelerini denetlemek için önkoşulları dağıtın
- \[Preview\]: en az 1 günlük parola yaşı olmayan Windows VM 'Leri denetlemek için önkoşulları dağıtın
- \[Preview\]: parola karmaşıklığı ayarı etkin olmayan Windows VM 'Leri denetlemek için önkoşulları dağıtın
- \[Preview\]: en az parola uzunluğu 14 karakter olan Windows VM 'Leri denetlemek için önkoşulları dağıtın
- \[Preview\]: ters çevrilebilir şifreleme kullanarak parolaları depolamamayan Windows sanal makinelerini denetlemek için önkoşulları dağıtın

## <a name="22-and-27-vulnerability-scanning"></a>2,2 ve 2,7 güvenlik açığı taraması

Bu şema, Azure Güvenlik Merkezi 'nde işletim sistemi güvenlik açıklarını, SQL güvenlik açıklarını ve sanal makine güvenlik açıklarını izleyen [Azure ilke](../../../policy/overview.md) tanımlarını atayarak bilgi sistemi güvenlik açıklarını yönetmenize yardımcı olur. Azure Güvenlik Merkezi, dağıtılan Azure kaynaklarının güvenlik durumu hakkında gerçek zamanlı Öngörüler elde etme olanağı sunan raporlama özellikleri sağlar. Bu şema Ayrıca, SQL sunucularında gelişmiş veri güvenliğini denetleyen ve uygulayan ilke tanımları da atar. Dağıtılmış kaynaklardaki güvenlik açıklarını anlamanıza yardımcı olmak için gelişmiş veri güvenliğine dahil edilen güvenlik açığı değerlendirmesi ve Gelişmiş tehdit koruması özellikleri.

- SQL sunucularınızda gelişmiş veri güvenliği etkin olmalıdır
- SQL Server 'lar üzerinde gelişmiş veri güvenliği dağıtma
- Sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- SQL veritabanlarınızdaki güvenlik açıkları düzeltilmelidir 
- Makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir

## <a name="13-denial-of-service-protection"></a>1,3 hizmet reddi koruması

Azure 'un dağıtılmış hizmet reddi (DDoS) standart katmanı, temel hizmet katmanı üzerinden ek özellikler ve hafifletme özellikleri sağlar. Bu ek özellikler, Azure Izleyici tümleştirmesini ve saldırı sonrası risk azaltma raporlarını inceleme olanağını içerir. Bu şema, DDoS standart katmanının etkinleştirilip etkinleştirilmediğini denetleyen bir [Azure ilke](../../../policy/overview.md) tanımı atar. Hizmet katmanları arasındaki yetenek farkını anlamak, Azure ortamınız için hizmet korumalarının reddedilmesine yönelik en iyi çözümü seçmenize yardımcı olabilir.

- DDoS koruma standardı etkinleştirilmelidir

## <a name="11-and-61-boundary-protection"></a>1,1 ve 6,1 sınır koruması

Bu şema, Azure Güvenlik Merkezi 'nde ağ güvenlik grubu sağlamlaştırma önerilerini izleyen bir [Azure ilke](../../../policy/overview.md) tanımı atayarak sistem sınırını yönetmenize ve denetlemenize yardımcı olur. Azure Güvenlik Merkezi, Internet 'e yönelik sanal makinelerin trafik düzenlerini analiz eder ve olası saldırı yüzeyini azaltmak için ağ güvenlik grubu kuralı önerileri sağlar.
Ayrıca, bu şema korunmayan uç noktaları, uygulamalar ve depolama hesaplarını izleyen ilke tanımları da atar. Bir güvenlik duvarı tarafından korunmayan uç noktalar ve uygulamalar ve Kısıtlanmamış erişimi olan depolama hesapları, bilgi sisteminde bulunan bilgilere istenmeden erişime izin verebilir.

- Internet 'e yönelik sanal makineler için ağ güvenlik grubu kuralları sağlamlaştırılmış olmalıdır
- Internet 'e yönelik uç nokta ile erişim kısıtlı olmalıdır
- Depolama hesaplarına Kısıtlanmamış ağ erişimini denetleme

## <a name="29a-boundary-protection--access-points"></a>Sınır korumasını 2.9 | Erişim noktaları

Tam zamanında (JıT) sanal makine erişimi, Azure sanal makinelerine giden trafiği kilitler ve gerektiğinde VM 'lere bağlanmak için kolay erişim sağlarken saldırılara maruz kalmayı azaltır. JıT sanal makine erişimi, Azure 'daki kaynaklarınıza yönelik dış bağlantı sayısını sınırlamanıza yardımcı olur. Bu şema, tam zamanında erişimi destekleyebilen ancak henüz yapılandırılmamış sanal makineleri izlemenize yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Tam zamanında ağ erişim denetimi, sanal makinelere uygulanmalıdır

## <a name="29a-boundary-protection--external-telecommunications-services"></a>Sınır korumasını 2.9 | Dış telekomünikasyon hizmetleri

Tam zamanında (JıT) sanal makine erişimi, Azure sanal makinelerine giden trafiği kilitler ve gerektiğinde VM 'lere bağlanmak için kolay erişim sağlarken saldırılara maruz kalmayı azaltır. JıT sanal makine erişimi, erişim isteği ve onay süreçlerini kolaylaştırarak trafik akışı ilkenizin özel durumlarını yönetmenize yardımcı olur. Bu şema, tam zamanında erişimi destekleyebilen ancak henüz yapılandırılmamış sanal makineleri izlemenize yardımcı olan bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- Tam zamanında ağ erişim denetimi, sanal makinelere uygulanmalıdır

## <a name="21-24-24a-25a-and-26-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>2,1, 2,4, 2,4 a, 2.5 A ve 2,6 Iletim gizliliği ve bütünlüğü | Şifreleme veya alternatif fiziksel koruma

Bu şema, iletişim protokolleri için uygulanan şifreleme mekanizmasını izlemenize yardımcı olan [Azure ilke](../../../policy/overview.md) tanımlarını atayarak, iletilen bilgilerin gizli ve bütünlüğünü korumanıza yardımcı olur. İletişimin düzgün şekilde şifrelendiğinden emin olmak, kuruluşunuzun gereksinimlerini karşılamanıza veya bilgilerin yetkisiz olarak açıklanmasını ve değiştirilmesini sağlamanıza yardımcı olabilir.

- API uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır
- Güvenli iletişim protokolleri kullanmayan Windows Web sunucularından denetim sonuçlarını göster
- Güvenli iletişim protokolleri kullanmayan Windows Web sunucularını denetlemek için önkoşulları dağıtma
- İşlev uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır
- Yalnızca Redis Cache güvenli bağlantılar etkinleştirilmelidir
- Depolama hesaplarına Güvenli aktarım etkinleştirilmelidir
- Web uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır

## <a name="22-23-25-41-and-27-protection-of-information-at-rest--cryptographic-protection"></a>2,2, 2,3, 2,5, 4,1 ve 2,7 bekleyen bilgilerin koruması | Şifreleme koruması

Bu şema, belirli bir cryptograph denetimi uygulayan [Azure ilke](../../../policy/overview.md) tanımlarını atayarak ve zayıf şifreleme ayarlarının kullanımını denetleyerek, bu şemayı, geri kalan bilgileri korumak için cryptograph denetimleri kullanma konusunda zorlamanıza yardımcı olur. Azure kaynaklarınızın en iyi durumda olmayan şifreleme yapılandırmalarının nerede olabileceğini anlamak, kaynakların bilgi güvenliği ilkenize uygun şekilde yapılandırıldığından emin olmak için düzeltici eylemler almanıza yardımcı olabilir. Özellikle, bu şema tarafından atanan ilke tanımları Data Lake Storage hesapları için şifrelemeyi gerektirir; SQL veritabanlarında saydam veri şifrelemesi gerektir; ve SQL veritabanlarında, sanal makine disklerinde ve Otomasyon hesabı değişkenlerinde eksik şifrelemeyi denetleyin.

- SQL sunucularınızda gelişmiş veri güvenliği etkin olmalıdır
- SQL Server 'lar üzerinde gelişmiş veri güvenliği dağıtma
- SQL DB saydam veri şifrelemesini dağıtma
- SQL veritabanlarındaki Saydam Veri Şifrelemesi etkinleştirilmelidir

## <a name="13-22-and-27-flaw-remediation"></a>1,3, 2,2 ve 2,7 hata düzeltmesi

Bu şema, Azure Güvenlik 'teki eksik sistem güncelleştirmelerini, işletim sistemi güvenlik açıklarını, SQL güvenlik açıklarını ve sanal makine güvenlik açıklarını izleyen [Azure ilke](../../../policy/overview.md) tanımlarını atayarak bilgi sistemi kusurlarını yönetmenize yardımcı olur Merkezinden. Azure Güvenlik Merkezi, dağıtılan Azure kaynaklarının güvenlik durumu hakkında gerçek zamanlı Öngörüler elde etme olanağı sunan raporlama özellikleri sağlar. Bu şema Ayrıca, sanal makine ölçek kümeleri için işletim sisteminin yama yapılmasını sağlayan bir ilke tanımı atar.

- Sanal makine ölçek kümelerinde otomatik işletim sistemi görüntüsü düzeltme eki uygulamayı gerektir
- Sanal makine ölçek kümelerindeki sistem güncelleştirmeleri yüklenmelidir
- Sistem güncelleştirmelerinin sanal makinelerinizde yüklü olması gerekir
- Sanal makine ölçek kümelerinizin güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- Sanal makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- SQL veritabanlarınızdaki güvenlik açıkları düzeltilmelidir

## <a name="61-malicious-code-protection"></a>6,1 kötü amaçlı kod koruması

Bu şema, Azure Güvenlik Merkezi 'nde sanal makinelerde eksik uç nokta koruması için izleme yapan [Azure ilke](../../../policy/overview.md) tanımlarını atayarak ve Microsoft 'a zorlamak için kötü amaçlı kod koruma dahil, Endpoint Protection 'ı yönetmenize yardımcı olur. Windows sanal makinelerinde kötü amaçlı yazılımdan koruma çözümü.

- Windows Server için varsayılan Microsoft ıaasantimalware uzantısını dağıt
- Uç nokta koruma çözümü, sanal makine ölçek kümelerine yüklenmelidir
- Azure Güvenlik Merkezi 'nde eksik Endpoint Protection izleme

## <a name="61-malicious-code-protection--central-management"></a>6,1 kötü amaçlı kod koruma | Merkezi Yönetim

Bu şema, Azure Güvenlik Merkezi 'ndeki sanal makinelerde eksik uç nokta koruması için izleme yapan [Azure ilke](../../../policy/overview.md) tanımlarını atayarak kötü amaçlı kod koruma dahil, Endpoint Protection 'ı yönetmenize yardımcı olur. Azure Güvenlik Merkezi, dağıtılan Azure kaynaklarının güvenlik durumuyla ilgili gerçek zamanlı Öngörüler elde etme olanağı sunan merkezi yönetim ve raporlama özellikleri sağlar.

- Uç nokta koruma çözümü, sanal makine ölçek kümelerine yüklenmelidir
- Azure Güvenlik Merkezi 'nde eksik Endpoint Protection izleme

## <a name="11-13-22-27-28-and-64-information-system-monitoring"></a>1,1, 1,3, 2,2, 2,7, 2,8 ve 6,4 Information sistem Izleme

Bu şema, Azure kaynakları arasında günlük ve veri güvenliğini denetleyerek ve zorunlu tutarak sisteminizi izlemenize yardımcı olur. Özellikle, Log Analytics aracısının dağıtımını denetleme ve uygulamaya zorlama ve SQL veritabanları, depolama hesapları ve ağ kaynakları için gelişmiş güvenlik ayarları atanmış ilkeleridir. Bu yetenekler, uygun işlemleri yapabilmeniz için anormal davranışları ve saldırı göstergelerini tespit etmenize yardımcı olabilir.

- \[Preview\]: denetim Log Analytics aracı dağıtımı-VM görüntüsü (OS) listelenmemiş
- \[Preview\]: Linux VM Ölçek Kümeleri için Log Analytics aracısı dağıtma (VMSS)
- \[Önizleme\]: Linux sanal makineleri için Log Analytics aracısını dağıtmayı
- \[Preview\]: Windows VM Ölçek Kümeleri için Log Analytics aracısı dağıtma (VMSS)
- \[Önizleme\]: Windows Vm'leri için Log Analytics aracısını dağıtmayı
- SQL sunucularınızda gelişmiş veri güvenliği etkin olmalıdır
- SQL Server için gelişmiş veri güvenliği ayarları, güvenlik uyarılarını almak için bir e-posta adresi içermelidir
- Azure Stream Analytics tanılama günlükleri etkinleştirilmelidir
- SQL Server 'lar üzerinde gelişmiş veri güvenliği dağıtma
- SQL Server 'lar üzerinde denetim dağıtma
- Sanal ağlar oluşturulduğunda Ağ İzleyicisi dağıt
- SQL Server 'lar üzerinde tehdit algılamayı dağıtma

## <a name="22-and-28-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>2,2 ve 2,8 bilgi sistemi Izleme | Trafik/Covert exfiltration çözümleme

Azure depolama için Gelişmiş tehdit koruması, depolama hesaplarına erişmeye veya açıktan yararlanmaya yönelik olağan dışı ve zararlı olabilecek girişimleri algılar. Koruma uyarıları, anormal erişim desenleri, anormal ayıklar/karşıya yüklemeler ve şüpheli depolama etkinlikleri içerir. Bu göstergeler, bilgilerin birlikte kullanımını tespit etmenize yardımcı olabilir.

- SQL Server 'lar üzerinde tehdit algılamayı dağıtma

> [!NOTE]
> Belirli Azure Ilke tanımlarının kullanılabilirliği, Azure Kamu ve diğer ulusal bulutlarda farklılık gösterebilir.

## <a name="next-steps"></a>Sonraki adımlar

SWIFT CSP-cscf V2020 şema öğesinin denetim eşlemesini gözden geçirdiğinize göre, şema ve bu örneği dağıtma hakkında bilgi edinmek için aşağıdaki makaleleri ziyaret edin:

> [!div class="nextstepaction"]
> [Swift CSP-cscf V2020 şema-genel bakış](./index.md)
> [Swift CSP-cscf V2020 şema-Deploy adımları](./deploy.md)

Şemalar ve bunların kullanımı hakkındaki diğer makaleler:

- [Şema yaşam döngüsü](../../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../../how-to/update-existing-assignments.md) öğrenin.
