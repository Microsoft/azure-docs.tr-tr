---
title: Azure HDInsight 'ta kurumsal güvenlik genel yönergeleri
description: Kurumsal Güvenlik Paketi dağıtımı ve yönetimi kolaylaştırmak için bazı en iyi uygulamalar.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 92ad8362f75cdf0613d4ee95f39c23aa6d4819bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933566"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Azure HDInsight 'ta kurumsal güvenlik genel bilgileri ve yönergeleri

Güvenli bir HDInsight kümesi dağıtımında, dağıtım ve küme yönetimini kolaylaştırmak için en iyi uygulamalar vardır. Bazı genel bilgi ve yönergeler burada ele alınmıştır.

## <a name="use-of-secure-cluster"></a>Güvenli küme kullanımı

### <a name="recommended"></a>Önerilen

* Küme aynı anda birden çok kullanıcı tarafından kullanılacak.
* Kullanıcıların aynı verilere farklı erişim düzeyleri vardır.

### <a name="not-necessary"></a>Gerekli değil

* Yalnızca otomatikleştirilmiş işleri (tek kullanıcı hesabı gibi) çalıştıracaksanız, standart bir küme yeterince iyidir.
* Veri içeri aktarmayı standart bir küme kullanarak yapabilir ve kullanıcıların analiz işlerini çalıştırabilecekleri farklı bir güvenli kümede aynı depolama hesabını kullanabilirsiniz.

## <a name="use-of-local-account"></a>Yerel hesabın kullanımı

* Paylaşılan bir kullanıcı hesabı veya yerel bir hesap kullanıyorsanız, yapılandırmayı veya hizmeti değiştirmek için hesabı kimin kullanmış olduğunu belirlemek zor olur.
* Kullanıcılar artık kuruluşun bir parçası olmadığında yerel hesapların kullanılması sorunlu olur.

## <a name="ranger"></a>Ranger

### <a name="policies"></a>İlkeler

* Varsayılan olarak, Ranger ilke olarak **Reddet** ' i kullanır.

* Veri erişimi, yetkilendirmenin etkinleştirildiği bir hizmet aracılığıyla yapıldığında:
  * Ranger yetkilendirme eklentisi çağrıldı ve isteğin bağlamı verildi.
  * Ranger, hizmet için yapılandırılmış ilkeleri uygular. Ranger ilkeleri başarısız olursa, erişim denetimi dosya sistemine ertelenir. MapReduce gibi bazı hizmetler yalnızca, isteği gönderen kullanıcının dosya/klasörün sahip olup olmadığını denetler. Hive gibi hizmetler, sahiplik eşleşmesi veya uygun dosya sistemi izinleri () olup olmadığını denetleyin `rwx` .

* Hive için, oluşturma/güncelleştirme/silme izinlerine sahip olmanın yanı sıra, kullanıcının `rwx` depolama ve tüm alt dizinlerde dizin üzerinde izinleri olmalıdır.

* İlkeler, bireyler yerine gruplara (tercih edilen) uygulanabilir.

* Ranger Yetkilendiricisi, her istek için bu hizmetin tüm Ranger ilkelerini değerlendirir. Bu değerlendirme, işi veya sorguyu kabul etmek için gereken süre üzerinde bir etkiye sahip olabilir.

### <a name="storage-access"></a>Depolama erişimi

* Depolama türü olarak kullanılıyorsa, OAuth belirteci dahil değildir.
* Ranger yetkilendirmeyi gerçekleştirdiyse, depolama erişimi yönetilen kimlik kullanılarak gerçekleşir.
* Ranger herhangi bir yetkilendirme gerçekleştirmediyse, depolama erişimi kullanıcının OAuth belirtecini kullanarak gerçekleşir.

### <a name="hierarchical-name-space"></a>Hiyerarşik ad alanı

Hiyerarşik ad alanı etkin olmadığında:

* Devralınan izin yok.
* Yalnızca Azure portal ' de kullanıcıya atanmak üzere yalnızca **depolama VERILERI xxxx** Azure rolü olan dosya sistemi iznine sahiptir.

### <a name="default-hdfs-permissions"></a>Varsayılan ayar izinleri

* Varsayılan olarak, kullanıcıların, bu **/** klasöre (başarılı olması için Depolama Blobu sahip rolünde olmaları gerekir) erişim izni yoktur.
* MapReduce ve diğerleri için hazırlama dizini için, kullanıcıya özgü bir dizin oluşturulur ve `sticky _wx` izinler sağlanır. Kullanıcılar, altında dosya ve klasör oluşturabilir, ancak diğer öğelere bakabilirler.

### <a name="url-auth"></a>URL kimlik doğrulaması

URL kimlik doğrulaması etkinse:

* Yapılandırma, URL kimlik doğrulaması (gibi) içinde hangi öneklerin ele alınanlara sahip olacaktır `adl://` .
* Bu URL için erişim varsa, Ranger kullanıcının izin verilenler listesinde olup olmadığını denetler.
* Ranger, hassas ilkelerin hiçbirini denetlemez.

## <a name="resource-groups"></a>Kaynak grupları

Küme kaynaklarını ayırt edebilmeniz için her küme için yeni bir kaynak grubu kullanın.

## <a name="nsgs-firewalls-and-internal-gateway"></a>NSG 'ler, güvenlik duvarları ve iç ağ geçidi

* Sanal ağları kilitlemek için ağ güvenlik grupları (NSG 'ler) kullanın.
* Giden erişim ilkelerini işlemek için güvenlik duvarı 'nı kullanın.
* Genel internet 'e açık olmayan iç ağ geçidini kullanın.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD), Microsoft 'un bulut tabanlı kimlik ve erişim yönetimi hizmetidir.

### <a name="policies"></a>İlkeler

* IP adresi tabanlı ilkeyi kullanarak koşullu erişim ilkesini devre dışı bırakın. Bu, kümelerin dağıtıldığı VNET 'lerde hizmet uç noktalarının etkinleştirilmesini gerektirir. MFA (AAD dışında bir şey) için bir dış hizmet kullanıyorsanız, IP adresi tabanlı ilke çalışmaz

* `AllowCloudPasswordValidation` Federasyon kullanıcıları için ilke gereklidir. HDInsight, Azure AD 'den belirteçleri almak için Kullanıcı adını/parolayı doğrudan kullandığından, bu ilkenin tüm Federasyon kullanıcıları için etkinleştirilmesi gerekir.

* Güvenilen IP 'Ler kullanılarak koşullu erişim atlamaya gerek duyuyorsanız hizmet uç noktalarını etkinleştirin.

### <a name="groups"></a>Gruplar

* Kümeleri her zaman bir grupla dağıtın.
* Grup üyeliklerini yönetmek için Azure AD 'yi kullanın (kümedeki hizmetleri yönetme girişimi daha kolay).

### <a name="user-accounts"></a>Kullanıcı hesapları

* Her senaryo için benzersiz bir kullanıcı hesabı kullanın. Örneğin, içeri aktarma için bir hesap kullanın, sorgu veya diğer işleme işleri için başka bir tane kullanın.
* Ayrı ilkeler yerine grup tabanlı Ranger ilkeleri kullanın.
* Artık kümelere erişimi olmayan kullanıcıları nasıl yönetebileceğinizi gösteren bir plana sahip olmanız gerekir.

## <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md) (Azure AD DS), etki alanına katılması, Grup ilkesi, Hafif Dizin Erişim Protokolü (LDAP) ve Windows Server Active Directory ile tamamen uyumlu Kerberos/NTLM kimlik doğrulaması gibi yönetilen etki alanı Hizmetleri sağlar.

Güvenli kümelerin bir etki alanına katılması için Azure AD DS gereklidir.
HDInsight çok fazla hata noktası, kimlik bilgisi paylaşımı, DNS izinleri vb. sunarak şirket içi etki alanı denetleyicilerine veya özel etki alanı denetleyicilerine bağlı olamaz. Daha fazla bilgi için bkz. [Azure AD DS SSS](../../active-directory-domain-services/faqs.md).

### <a name="azure-ad-ds-instance"></a>Azure AD DS örneği

* Örneğini ile oluşturun `.onmicrosoft.com domain` . Bu şekilde, etki alanına hizmet eden birden çok DNS sunucusu olmayacaktır.
* LDAPS için otomatik olarak imzalanan bir sertifika oluşturun ve Azure AD DS yükleyin.
* Kümeler dağıtmak için eşlenmiş bir sanal ağ kullanın (HDInsight ESP kümelerini dağıtan bir takım sayısına sahipseniz bu işlem yararlı olacaktır). Bu, etki alanı denetleyicisi ile sanal ağ üzerindeki bağlantı noktalarını (NSG 'ler) açmanıza gerek kalmaz.
* Sanal ağın DNS 'sini düzgün şekilde yapılandırın (Azure AD DS etki alanı adı hiçbir ana bilgisayar dosya girişi olmadan çözümlenmelidir).
* Giden trafiği kısıtaktarıyorsanız [HDInsight 'ta güvenlik duvarı desteğini](../hdinsight-restrict-outbound-traffic.md) okuduğunuzdan emin olun

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>Azure AD 'den Azure AD DS eşitlenen Özellikler

* Azure AD Connect Şirket içinden Azure AD 'ye eşitlenir.
* Azure AD DS, Azure AD 'den eşitlenir.

Azure AD DS, nesneleri Azure AD 'den düzenli aralıklarla eşitler. Azure portal Azure AD DS dikey penceresi eşitleme durumunu görüntüler. Her eşitleme aşamasında, benzersiz özellikler çakışmalı ve yeniden adlandırılabilir. Azure AD 'den Azure AD DS özellik eşlemesine dikkat edin.

Daha fazla bilgi için bkz. [Azure AD userPrincipalName popülasyonu](../../active-directory/hybrid/plan-connect-userprincipalname.md)ve [Azure AD DS eşitlemesinin nasıl çalıştığı](../../active-directory-domain-services/synchronization.md).

### <a name="password-hash-sync"></a>Parola karması eşitleme

* Parolalar diğer nesne türlerinden farklı şekilde eşitlenir. Azure AD ve Azure AD DS ile yalnızca geri alınamaz parola karmaları eşitlenir
* Şirket içi Azure AD 'ye AD Connect aracılığıyla etkinleştirilmesi
* Azure AD 'den Azure AD DS eşitleme otomatiktir (gecikme süresi 20 dakikadan fazla olur).
* Parola karmaları yalnızca değiştirilen bir parola olduğunda eşitlenir. Parola karma eşitlemesini etkinleştirdiğinizde, tüm mevcut parolalar, ırters olarak depolandıklarında otomatik olarak eşitlenmez. Parolayı değiştirdiğinizde parola karmaları eşitlenir.

### <a name="computer-objects-location"></a>Bilgisayar nesneleri konumu

Her küme tek bir OU ile ilişkilendirilir. Bir iç kullanıcı OU 'da sağlanıyor. Tüm düğümlerin etki alanı aynı OU 'ya katılmış.

### <a name="active-directory-administrative-tools"></a>Yönetim araçlarını Active Directory

Windows Server sanal makinesine Active Directory yönetim araçlarının nasıl yükleneceğine ilişkin adımlar için bkz. [Install Management Tools](../../active-directory-domain-services/tutorial-create-management-vm.md).

## <a name="troubleshooting"></a>Sorun giderme

### <a name="cluster-creation-fails-repeatedly"></a>Küme oluşturma işlemi sürekli başarısız oluyor

En yaygın nedenler:

* DNS yapılandırması doğru değil, küme düğümlerinin etki alanına katılması başarısız oldu.
* NSG 'ler çok kısıtlayıcıdır, etki alanına katılmayı önler.
* Yönetilen kimlik yeterli izinlere sahip değil.
* Küme adı, ilk altı karakterden (başka bir canlı küme ile veya silinen bir kümeyle) benzersiz değildir.

## <a name="authentication-setup-and-configuration"></a>Kimlik doğrulama kurulumu ve yapılandırması

### <a name="user-principal-name-upn"></a>Kullanıcı asıl adı (UPN)

* Lütfen tüm hizmetler için küçük harfli kullanın-UPN 'ler, ESP kümelerinde büyük küçük harfe duyarlı değildir, ancak
* UPN ön eki Azure AD-DS içindeki SAMAccountName ile aynı olmalıdır. Posta alanıyla eşleştirme gerekli değildir.

### <a name="ldap-properties-in-ambari-configuration"></a>Ambarı yapılandırmasındaki LDAP özellikleri

HDInsight kümesi yapılandırmanızı etkileyen ambarı özelliklerinin tam listesi için bkz. [AMBARı LDAP Kimlik doğrulama kurulumu](https://ambari.apache.org/1.2.1/installing-hadoop-using-ambari/content/ambari-chap2-4.html).

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight 'ta Azure Active Directory Domain Services olan Kurumsal Güvenlik Paketi konfigürasyonları](./apache-domain-joined-configure-using-azure-adds.md)

* [Azure Active Directory kullanıcılarını bir HDInsight kümesiyle eşitler](../hdinsight-sync-aad-users-to-cluster.md).
