---
title: ISO 27001 paylaşılan hizmetleri şema örnek denetimleri
description: ISO 27001 paylaşılan hizmetleri şema örneğinin denetim eşlemesi. Her denetim, değerlendirmede yardımcı olan bir veya daha fazla Azure Ilke tanımına eşlenir.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: 64266e72bf2aa24ac7fbf70f87f27739709fa0be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626350"
---
# <a name="control-mapping-of-the-iso-27001-shared-services-blueprint-sample"></a>ISO 27001 paylaşılan hizmetleri şema örneğinin denetim eşlemesi

Aşağıdaki makalede, Azure şemaları ISO 27001 paylaşılan hizmetleri şema örneği 'nin ISO 27001 denetimleriyle nasıl eşleştiği açıklanır. Denetimler hakkında daha fazla bilgi için bkz. [ıso 27001](https://www.iso.org/isoiec-27001-information-security.html).

Aşağıdaki eşlemeler **ıso 27001:2013** denetimlerine göre yapılır. Sağ taraftaki gezinmeyi kullanarak doğrudan belirli bir denetim eşlemesine atlayın. Eşlenmiş denetimlerin birçoğu bir [Azure Policy](../../../policy/overview.md) girişimi ile uygulanır. Tüm girişimi gözden geçirmek için Azure portal **ilkeyi** açın ve **tanımlar** sayfasını seçin. Ardından, **\[ ÖNIZLEME denetimi ISO 27001:2013 denetimlerini bulun ve seçin ve denetim gereksinimleri yerleşik ilke girişimi ' ni \] desteklemek Için belirli VM uzantılarını dağıtın** .

> [!IMPORTANT]
> Aşağıdaki her denetim bir veya daha fazla [Azure ilke](../../../policy/overview.md) tanımı ile ilişkilidir. Bu ilkeler, denetimiyle [uyumluluğu değerlendirmenize](../../../policy/how-to/get-compliance-data.md) yardımcı olabilir; Ancak, bir denetim ile bir veya daha fazla ilke arasında genellikle bire bir veya tam eşleşme yoktur. Bu nedenle, Azure Ilkesi ile **uyumlu** , yalnızca ilkelerin kendilerine başvurur; Bu, bir denetimin tüm gereksinimleriyle tamamen uyumlu olduğunuzdan emin değildir. Buna ek olarak, uyumluluk standardı şu anda herhangi bir Azure Ilke tanımı tarafından açıklanmayan denetimler içerir. Bu nedenle, Azure Ilkesinde uyumluluk, genel uyumluluk durumunuzu yalnızca kısmi görünümüdür. Bu uyumluluk şeması örneği için denetimler ve Azure Ilke tanımları arasındaki ilişkilendirmeler zaman içinde değişebilir. Değişiklik geçmişini görüntülemek için [GitHub kayıt geçmişine](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001-shared/control-mapping.md)bakın.

## <a name="a612-segregation-of-duties"></a>A. 6.1.2 ayrımı

Yalnızca bir Azure aboneliğinin sahibi, yönetici artıklığına izin vermez. Bunun tersine, çok fazla sayıda Azure aboneliği sahibi, güvenliği aşılmış bir sahip hesabı aracılığıyla ihlal olasılığını artırabilir. Bu şema, Azure abonelikleri için sahip sayısını denetleyen iki [Azure ilke](../../../policy/overview.md) tanımı atayarak uygun sayıda Azure abonelik sahibini korumanıza yardımcı olur. Abonelik sahibi izinlerinin yönetilmesi, görevlerin uygun bir şekilde ayrılmasını sağlamanıza yardımcı olabilir.

- Aboneliğiniz için en fazla 3 sahip belirtilmelidir
- Aboneliğinize birden fazla sahip atanmalıdır

## <a name="a821-classification-of-information"></a>A. 8.2.1 bilgi sınıflandırması

Azure 'un [SQL güvenlik açığı değerlendirme hizmeti](../../../../azure-sql/database/sql-vulnerability-assessment.md) , veritabanlarınızda depolanan hassas verileri bulmanıza yardımcı olabilir ve bu verileri sınıflandırmaya yönelik öneriler içerir. Bu şema, SQL güvenlik açığı değerlendirme taraması sırasında tanımlanan güvenlik açıklarını denetlemek için bir [Azure ilke](../../../policy/overview.md) tanımı atar.

- SQL veritabanlarınızdaki güvenlik açıkları düzeltilmelidir

## <a name="a912-access-to-networks-and-network-services"></a>A. 9.1.2, ağlara ve ağ hizmetlerine erişim

Azure [rol tabanlı erişim denetimi (Azure RBAC)](../../../../role-based-access-control/overview.md) , Azure kaynaklarına kimlerin erişebileceğini yönetmeye yardımcı olur. Bu şema yedi [Azure ilke](../../../policy/overview.md) tanımı atayarak Azure kaynaklarına erişimi denetlemenize yardımcı olur. Bu ilkeler, kaynaklara daha fazla izin vermeyi sağlayan kaynak türlerinin ve yapılandırmaların kullanımını denetler.
Bu ilkeleri ihlal eden kaynakları anlamak, Azure kaynaklarının yetkili kullanıcılarla sınırlı olduğundan emin olmak için düzeltici eylemler almanıza yardımcı olabilir.

- Parolası olmayan hesaplara sahip Linux VM 'lerinden denetim sonuçlarını göster
- Parolasız uzak bağlantılara izin veren Linux VM 'lerinden denetim sonuçlarını göster
- Depolama hesaplarının yeni Azure Resource Manager kaynaklarına geçirilmesi gerekir
- Sanal makinelerin yeni Azure Resource Manager kaynaklara geçirilmesi gerekir
- Yönetilen diskleri kullanmayan VM 'Leri denetleme

## <a name="a923-management-of-privileged-access-rights"></a>A. 9.2.3 ayrıcalıklı erişim haklarının yönetimi

Bu şema, sahip ve/veya yazma izinleri ve çok faktörlü kimlik doğrulaması etkinleştirilmemiş olan sahip ve/veya yazma izinlerine sahip olan dış hesapları denetlemek için dört [Azure ilke](../../../policy/overview.md) tanımı atayarak ayrıcalıklı erişim haklarını kısıtlayıp denetlemenize yardımcı olur. Azure rol tabanlı erişim denetimi (Azure RBAC), Azure kaynaklarına kimlerin erişebileceğini yönetmeye yardımcı olur. Bu şema Ayrıca, SQL Server ve Service Fabric için Azure Active Directory kimlik doğrulaması kullanımını denetlemek üzere üç Azure ilke tanımı da atar. Azure Active Directory kimlik doğrulaması kullanmak, veritabanı kullanıcıları ve diğer Microsoft Hizmetleri için Basitleştirilmiş izin yönetimi ve merkezi kimlik yönetimine izin verebilir. Bu şema Ayrıca, özel Azure RBAC kurallarının kullanımını denetlemek için bir Azure ilke tanımı atar. Özel Azure RBAC kurallarının nerede uygulandığını anlamak, özel Azure RBAC kuralları hata durumunda olduğundan, gereksinimi ve uygun uygulamayı doğrulamanıza yardımcı olabilir.

- MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir
- Aboneliğinizde yazma izinleri olan hesaplarda MFA etkinleştirilmelidir
- Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır
- Yazma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir
- SQL sunucuları için bir Azure Active Directory Yöneticisi sağlanmalıdır
- Service Fabric kümeler yalnızca istemci kimlik doğrulaması için Azure Active Directory kullanmalıdır
- Özel RBAC kurallarının kullanımını denetleme

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A. 9.2.4, kullanıcıların gizli kimlik doğrulama bilgilerinin yönetimi

Bu şema, Multi-Factor Authentication etkinleştirilmemiş hesapları denetlemek için üç [Azure ilke](../../../policy/overview.md) tanımı atar. Multi-Factor Authentication, bir dizi kimlik doğrulama bilgisinin tehlikeye düşmesi durumunda bile hesapların güvenli kalmasına yardımcı olur. Multi-Factor Authentication 'ı etkin olmayan hesapları izleyerek, tehlikeye geçmek daha olası olabilecek hesapları belirleyebilirsiniz. Bu şema Ayrıca, Linux VM parola dosyası izinlerinin yanlış ayarlandıklarında uyarı vermek üzere denetdukları iki Azure ilke tanımı da atar. Bu kurulum, kimlik doğrulayıcılar güvenliğinin aşılmadığından emin olmak için düzeltici eylem gerçekleştirmenizi sağlar.

- MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir
- MFA, aboneliğinizde okuma izinleri olan hesaplarda etkinleştirilmelidir
- Aboneliğinizde yazma izinleri olan hesaplarda MFA etkinleştirilmelidir
- Passwd dosyası izinleri 0644 olarak ayarlanan Linux VM 'lerinden denetim sonuçlarını göster

## <a name="a925-review-of-user-access-rights"></a>A. 9.2.5 Kullanıcı erişimi haklarının Incelemesi

Azure [rol tabanlı erişim denetimi (Azure RBAC)](../../../../role-based-access-control/overview.md) , Azure 'daki kaynaklara kimlerin erişebileceğini yönetmenize yardımcı olur. Azure portal kullanarak, Azure kaynaklarına kimlerin erişebileceğini ve bunların izinlerini gözden geçirebilirsiniz. Bu şema, hesap ve yükseltilmiş izinlere sahip harici hesaplar dahil olmak üzere, gözden geçirme için öncelik verilmelidir, denetlenecek hesaplara dört adet [Azure ilke](../../../policy/overview.md) tanımı atar.

- Kullanım dışı bırakılan hesaplar aboneliğinizden kaldırılmalıdır
- Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır
- Sahip izinleri olan dış hesaplar aboneliğinizden kaldırılmalıdır
- Yazma izinlerine sahip dış hesapların aboneliğinizden kaldırılması gerekir

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A. 9.2.6 kaldırma veya erişim hakları ayarlama

Azure [rol tabanlı erişim denetimi (Azure RBAC)](../../../../role-based-access-control/overview.md) , Azure 'daki kaynaklara kimlerin erişebileceğini yönetmenize yardımcı olur. [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) ve Azure RBAC kullanarak, Kullanıcı rollerini kurumsal değişiklikleri yansıtacak şekilde güncelleştirebilirsiniz. Gerektiğinde, hesapların oturum açması (veya kaldırılması) engellenebilir ve bu da Azure kaynaklarına erişim haklarını hemen kaldırır. Bu şema, kaldırma için göz önünde bulundurmanız gereken amorti edilmiş hesabı denetlemek için iki [Azure ilke](../../../policy/overview.md) tanımı atar.

- Kullanım dışı bırakılan hesaplar aboneliğinizden kaldırılmalıdır
- Sahip izinleri olan kullanım dışı hesaplar aboneliğinizden kaldırılmalıdır

## <a name="a942-secure-log-on-procedures"></a>A. 9.4.2 Secure oturum açma yordamları

Bu şema, Multi-Factor Authentication etkinleştirilmemiş hesapları denetlemek için üç Azure Ilke tanımı atar. Azure AD Multi-Factor Authentication ikinci bir kimlik doğrulama biçimi isteyerek ve güçlü kimlik doğrulaması sunarak ek güvenlik sağlar. Multi-Factor Authentication 'ı etkin olmayan hesapları izleyerek, tehlikeye geçmek daha olası olabilecek hesapları belirleyebilirsiniz.

- MFA, aboneliğinizde sahip izinleri olan hesaplarda etkinleştirilmelidir
- MFA, aboneliğinizde okuma izinleri olan hesaplarda etkinleştirilmelidir
- Aboneliğinizde yazma izinleri olan hesaplarda MFA etkinleştirilmelidir

## <a name="a943-password-management-system"></a>A. 9.4.3 Password yönetim sistemi

Bu şema, en düşük güç ve diğer parola gereksinimlerini zorlayamama Windows VM 'Leri denetleyen 10 [Azure ilke](../../../policy/overview.md) tanımı atayarak güçlü parolalar zorlamanıza yardımcı olur. Parola gücü ilkesini ihlal eden VM 'lerin farkında, tüm VM Kullanıcı hesaplarının parolalarının ilkeyle uyumlu olduğundan emin olmak için düzeltici eylemler almanıza yardımcı olur.

- Parola karmaşıklığı ayarı etkin olmayan Windows VM 'lerinden denetim sonuçlarını göster
- Maksimum parola yaşı 70 gün olmayan Windows VM 'lerinden denetim sonuçlarını göster
- En az 1 günlük parola yaşı olmayan Windows VM 'lerinden denetim sonuçlarını göster
- Minimum parola uzunluğunu 14 karakter olarak kısıtlayan Windows VM 'lerinden denetim sonuçlarını göster
- Önceki 24 parolanın yeniden kullanılmasına izin veren Windows VM 'lerinden denetim sonuçlarını göster

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>Şifreleme denetimlerinin kullanımıyla ilgili bir. 10.1.1 Ilkesi

Bu şema, belirli bir cryptograph denetimi uygulayan ve zayıf şifreleme ayarlarının kullanımını kontrol eden 13 [Azure ilke](../../../policy/overview.md) tanımı atayarak, bu şemayı cryptograph denetimlerinin kullanımı üzerinde zorlamanıza yardımcı olur. Azure kaynaklarınızın en iyi durumda olmayan şifreleme yapılandırmalarının nerede olabileceğini anlamak, kaynakların bilgi güvenliği ilkenize uygun şekilde yapılandırıldığından emin olmak için düzeltici eylemler almanıza yardımcı olabilir. Özellikle, bu şema tarafından atanan ilkeler BLOB depolama hesapları ve Data Lake Storage hesapları için şifreleme gerektirir; SQL veritabanlarında saydam veri şifrelemesi gerektir; depolama hesaplarında, SQL veritabanlarında, sanal makine disklerinde ve Otomasyon hesabı değişkenlerinde eksik şifrelemeyi denetleyin; depolama hesaplarına, Işlev uygulamalarına, Web uygulamasına, API Apps ve Redis Cache yönelik güvenli olmayan bağlantıları denetleyin zayıf sanal makine parola şifrelemesini denetleme; ve şifrelenmemiş Service Fabric iletişimini denetleyin.

- İşlev Uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır
- Web uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır
- API uygulaması yalnızca HTTPS üzerinden erişilebilir olmalıdır
- Ters çevrilebilir şifreleme kullanarak parolaları depomayan Windows VM 'lerinden denetim sonuçlarını göster
- Disk şifrelemesi sanal makinelere uygulanmalıdır
- Otomasyon hesabı değişkenleri şifrelenmelidir
- Redsıs için yalnızca Azure önbelleğinize güvenli bağlantılar etkinleştirilmelidir
- Depolama hesaplarına güvenli aktarım etkinleştirilmelidir
- Service Fabric kümelerinde ClusterProtectionLevel özelliği EncryptAndSign olarak ayarlanmalıdır
- SQL veritabanlarındaki Saydam Veri Şifrelemesi etkinleştirilmelidir

## <a name="a1241-event-logging"></a>A. 12.4.1 olay günlüğü

Bu şema, Azure kaynaklarında günlük ayarlarını denetleyen yedi [Azure ilke](../../../policy/overview.md) tanımı atayarak sistem olaylarının günlüğe kaydedilmesini sağlamanıza yardımcı olur.
Tanılama günlükleri, Azure kaynakları içinde gerçekleştirilen işlemlere ilişkin öngörüler sağlar.

- Denetim bağımlılığı aracı dağıtımı-VM görüntüsü (OS) listelenmemiş
- Sanal makine ölçek kümelerinde denetim bağımlılığı Aracısı dağıtımı-VM görüntüsü (OS) listelenmemiş
- [Önizleme]: denetim Log Analytics aracı dağıtımı-VM görüntüsü (OS) listelenmemiş
- Sanal makine ölçek kümelerinde denetim Log Analytics Aracısı dağıtımı-VM görüntüsü (OS) listelenmemiş
- Tanılama ayarını denetle
- SQL Server üzerinde denetim etkinleştirilmelidir

## <a name="a1243-administrator-and-operator-logs"></a>A. 12.4.3 Yöneticisi ve işleç günlükleri

Bu şema, Azure kaynaklarında günlük ayarlarını denetleyen yedi Azure ilke tanımı atayarak sistem olaylarının günlüğe kaydedilmesini sağlamanıza yardımcı olur. Tanılama günlükleri, Azure kaynakları içinde gerçekleştirilen işlemlere ilişkin öngörüler sağlar.

- Denetim bağımlılığı aracı dağıtımı-VM görüntüsü (OS) listelenmemiş
- Sanal makine ölçek kümelerinde denetim bağımlılığı Aracısı dağıtımı-VM görüntüsü (OS) listelenmemiş
- [Önizleme]: denetim Log Analytics aracı dağıtımı-VM görüntüsü (OS) listelenmemiş
- Sanal makine ölçek kümelerinde denetim Log Analytics Aracısı dağıtımı-VM görüntüsü (OS) listelenmemiş
- Tanılama ayarını denetle
- SQL Server üzerinde denetim etkinleştirilmelidir

## <a name="a1244-clock-synchronization"></a>A. 12.4.4 saat eşitleme

Bu şema, Azure kaynaklarında günlük ayarlarını denetleyen yedi Azure ilke tanımı atayarak sistem olaylarının günlüğe kaydedilmesini sağlamanıza yardımcı olur. Azure günlükleri, kaynak genelinde olayların zaman bağıntılı bir kaydını oluşturmak için eşitlenmiş iç saatleri kullanır.

- Denetim bağımlılığı aracı dağıtımı-VM görüntüsü (OS) listelenmemiş
- Sanal makine ölçek kümelerinde denetim bağımlılığı Aracısı dağıtımı-VM görüntüsü (OS) listelenmemiş
- [Önizleme]: denetim Log Analytics aracı dağıtımı-VM görüntüsü (OS) listelenmemiş
- Sanal makine ölçek kümelerinde denetim Log Analytics Aracısı dağıtımı-VM görüntüsü (OS) listelenmemiş
- Tanılama ayarını denetle
- SQL Server üzerinde denetim etkinleştirilmelidir

## <a name="a1251-installation-of-software-on-operational-systems"></a>A. 12.5.1 işletimsel sistemlere yazılım yüklemesi

Uyarlamalı uygulama denetimi, Azure Güvenlik Merkezi 'nin Azure 'da bulunan sanal makinelerinizde hangi uygulamaların çalıştırılacağını denetlemenize yardımcı olan bir çözümdür. Bu şema, izin verilen uygulamalar kümesinde yapılan değişiklikleri izleyen bir Azure ilke tanımı atar. Bu özellik, Azure VM 'lerinde yazılım ve uygulama yüklemelerini denetlemenize yardımcı olur.

- Güvenli uygulamaları tanımlamaya yönelik Uyarlamalı uygulama denetimleri, makinelerinizde etkinleştirilmelidir

## <a name="a1261-management-of-technical-vulnerabilities"></a>A. 12.6.1 teknik güvenlik açıklarının yönetimi

Bu şema, Azure Güvenlik Merkezi 'nde eksik sistem güncelleştirmelerini, işletim sistemi güvenlik açıklarını, SQL güvenlik açıklarını ve sanal makine güvenlik açıklarını izleyen beş [Azure ilke](../../../policy/overview.md) tanımı atayarak bilgi sistemi güvenlik açıklarını yönetmenize yardımcı olur. Azure Güvenlik Merkezi, dağıtılan Azure kaynaklarının güvenlik durumu hakkında gerçek zamanlı Öngörüler elde etme olanağı sunan raporlama özellikleri sağlar.

- Azure Güvenlik Merkezi 'nde eksik Endpoint Protection izleme
- Makinelerinize sistem güncelleştirmeleri yüklenmelidir
- Makinelerinizdeki güvenlik yapılandırmasındaki güvenlik açıkları düzeltilmelidir
- SQL veritabanlarınızdaki güvenlik açıkları düzeltilmelidir
- Güvenlik açıkları bir güvenlik açığı değerlendirme çözümü tarafından düzeltilmelidir

## <a name="a1262-restrictions-on-software-installation"></a>Yazılım yüklemesinde bir. 12.6.2 kısıtlamaları

Uyarlamalı uygulama denetimi, Azure Güvenlik Merkezi 'nin Azure 'da bulunan sanal makinelerinizde hangi uygulamaların çalıştırılacağını denetlemenize yardımcı olan bir çözümdür. Bu şema, izin verilen uygulamalar kümesinde yapılan değişiklikleri izleyen bir Azure ilke tanımı atar. Yazılım yükleme kısıtlamaları, yazılım güvenlik açıklarına giriş olasılığını azaltmanıza yardımcı olabilir.

- Güvenli uygulamaları tanımlamaya yönelik Uyarlamalı uygulama denetimleri, makinelerinizde etkinleştirilmelidir

## <a name="a1311-network-controls"></a>A. 13.1.1 ağ denetimleri

Bu şema, ağ güvenlik gruplarını, izin veren kurallarla izleyen bir [Azure ilke](../../../policy/overview.md) tanımı atayarak ağları yönetmenize ve denetlemenize yardımcı olur. Çok izin verilen kurallar istenmeyen ağ erişimine izin verebilir ve incelenmelidir. Bu şema Ayrıca korumasız uç noktaları, uygulamalar ve depolama hesaplarını izleyen üç Azure ilke tanımı da atar. Bir güvenlik duvarı tarafından korunmayan uç noktalar ve uygulamalar ve Kısıtlanmamış erişimi olan depolama hesapları, bilgi sisteminde bulunan bilgilere istenmeden erişime izin verebilir.

- Internet 'e yönelik uç nokta ile erişim kısıtlı olmalıdır
- Depolama hesapları, ağ erişimini kısıtlıyor olmalıdır

## <a name="a1321-information-transfer-policies-and-procedures"></a>A. 13.2.1 Information transfer ilkeleri ve yordamları

Şema, depolama hesaplarına Güvenli olmayan bağlantıları denetlemek için iki [Azure ilke](../../../policy/overview.md) tanımı atayarak ve Redis Cache Azure hizmetleri ile bilgi aktarımını güvence altına almanıza yardımcı olur.

- Redsıs için yalnızca Azure önbelleğinize güvenli bağlantılar etkinleştirilmelidir
- Depolama hesaplarına güvenli aktarım etkinleştirilmelidir

## <a name="next-steps"></a>Sonraki adımlar

Artık ISO 27001 paylaşılan hizmetler şeması 'nın denetim eşlemesini gözden geçirdiğinize göre, mimari ve bu örneğin nasıl dağıtılacağı hakkında bilgi edinmek için aşağıdaki makaleleri ziyaret edin:

> [!div class="nextstepaction"]
> [ISO 27001 paylaşılan hizmetleri şema-genel bakış](./index.md) 
>  [ISO 27001 paylaşılan hizmetleri şema-Deploy adımları](./deploy.md)

Şemalar ve bunların kullanımı hakkındaki diğer makaleler:

- [Şema yaşam döngüsü](../../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Mevcut atamaları güncelleştirmeyi](../../how-to/update-existing-assignments.md) öğrenin.
