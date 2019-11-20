---
title: Azure AD Domain Services için ortak dağıtım senaryoları | Microsoft Docs
description: Değer sağlamak ve iş ihtiyaçlarını karşılamak için Azure Active Directory Domain Services bazı yaygın senaryolar ve kullanım örnekleri hakkında bilgi edinin.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: 489f4a527a5afaf1bab5e2065137a5011d45baa6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474447"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services için yaygın kullanım örnekleri ve senaryolar

Azure Active Directory Domain Services (Azure AD DS), etki alanına katılması, Grup ilkesi, Hafif Dizin Erişim Protokolü (LDAP) ve Kerberos/NTLM kimlik doğrulaması gibi yönetilen etki alanı Hizmetleri sağlar. Azure AD DS, mevcut Azure AD kiracınızla tümleştirilir, bu da kullanıcıların mevcut kimlik bilgilerini kullanarak oturum açmasını olanaklı kılar. Bu etki alanı hizmetlerini, bulutta etki alanı denetleyicileri dağıtma, yönetme ve düzeltme eki uygulama ile birlikte kullanarak, şirket içi kaynakların Azure 'a daha iyi bir şekilde daha iyi bir şekilde daha iyi bir şekilde daha iyi bir şekilde kaymasını sağlar.

Bu makalede, Azure AD DS 'nin değer sağladığı ve bu ihtiyaçları karşılayan bazı yaygın iş senaryoları özetlenmektedir.

## <a name="secure-administration-of-azure-virtual-machines"></a>Azure sanal makinelerinin güvenli yönetimi

Tek bir AD kimlik bilgileri kümesi kullanmanıza olanak sağlamak için Azure sanal makineleri (VM 'Ler) Azure AD DS yönetilen bir etki alanına katılabilir. Bu yaklaşım, her VM 'de yerel yönetici hesaplarının veya ortamlar arasında ayrı hesap ve parolaların saklanması gibi kimlik bilgisi yönetimi sorunlarını azaltır.

Azure AD DS tarafından yönetilen bir etki alanına katılmış VM 'Ler de Grup İlkesi kullanılarak yönetilebilir ve güvenliği sağlanabilir. Gerekli güvenlik temelleri, kurumsal güvenlik yönergelerine uygun olarak onları kilitlemek için VM 'lere uygulanabilir. Örneğin, VM 'de başlatılabilen uygulama türlerini kısıtlamak için Grup İlkesi yönetimi özelliklerini kullanabilirsiniz.

![Azure sanal makinelerinin kolaylaştırılmış yönetimi](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Ortak bir örnek senaryoya göz atalım. Sunucular ve diğer altyapılar yaşam sonuna ulaştığında, contoso Şu anda şirket içinde barındırılan uygulamaları buluta taşımak istiyor. Şirket uygulamalarını barındıran sunucuların, etki alanına katılmış ve Grup İlkesi kullanılarak yönetilmesi gereken geçerli BT standardı. Contoso BT Yöneticisi, kullanıcıların kurumsal kimlik bilgilerini kullanarak oturum açmasını kolaylaştırmak için Azure 'da dağıtılan VM 'Lere etki alanına katılmayı tercih edebilir. Etki alanına katılmış olduğunda, VM 'Ler Grup İlkesi nesneleri (GPO 'Lar) kullanılarak gerekli güvenlik temellerine uymak üzere de yapılandırılabilir. Contoso, Azure 'da kendi etki alanı denetleyicilerini dağıtmayı, izlemeyi ve yönetmeyi tercih edebilir.

Azure AD DS bu kullanım örneği için harika bir fırsattır. Azure AD DS yönetilen bir etki alanı, VM 'Leri etki alanına katmanıza, tek bir kimlik bilgileri kümesi kullanmanıza ve Grup İlkesi uygulamanıza olanak tanır. Yönetilen bir etki alanı olarak, etki alanı denetleyicilerini kendiniz yapılandırmak ve bakımını yapmak zorunda kalmazsınız.

### <a name="deployment-notes"></a>Dağıtım notları

Aşağıdaki dağıtım konuları bu örnek kullanım örneği için geçerlidir:

* Azure AD DS yönetilen etki alanları, varsayılan olarak tek bir düz kuruluş birimi (OU) yapısı kullanır. Etki alanına katılmış tüm VM 'Ler tek bir OU 'da bulunur. İsterseniz, özel OU 'Lar oluşturabilirsiniz.
* Azure AD DS, her biri Kullanıcı ve bilgisayar kapsayıcıları için yerleşik GPO kullanır. Ek denetim için özel GPO 'Lar oluşturabilir ve bunları özel OU 'Lara hedefleyebilirsiniz.
* Azure AD DS, temel AD bilgisayar nesne şemasını destekler. Bilgisayar nesnesinin şemasını genişletemezsiniz.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>LDAP bağlama kimlik doğrulaması kullanan şirket içi uygulamaları kaldırın ve taşıyın

Contoso, örnek senaryo olarak, bir ISV çok yıldan önce satın alınan şirket içi bir uygulamaya sahiptir. Uygulama Şu anda ISV tarafından bakım modunda ve uygulamada değişiklikler yapmak canlı olarak pahalıdır. Bu uygulamada, Kullanıcı kimlik bilgilerini bir Web formu kullanarak toplayan ve ardından Şirket içi AD DS ortamına LDAP bağlaması gerçekleştirerek kullanıcıların kimliğini doğrulayan Web tabanlı bir ön uç vardır.

![LDAP bağlama](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso bu uygulamayı Azure 'a geçirmek istiyor. Uygulama, hiçbir değişiklik gerekmeden olduğu gibi çalışmaya devam etmelidir. Ayrıca, kullanıcılar mevcut kurumsal kimlik bilgilerini ve ek eğitim olmadan kimlik doğrulaması yapabilmelidir. Uygulamanın çalıştığı son kullanıcılar için saydam olması gerekir.

Bu senaryoda, Azure AD DS, uygulamaların kimlik doğrulama işleminin bir parçası olarak LDAP bağlamaları gerçekleştirmesini sağlar. Eski şirket içi uygulamalar, Azure 'a geçiş yapabilir ve yapılandırma veya kullanıcı deneyiminde değişiklik yapmadan kullanıcıların sorunsuzca kimlik doğrulamasından devam edebilir.

### <a name="deployment-notes"></a>Dağıtım notları

Aşağıdaki dağıtım konuları bu örnek kullanım örneği için geçerlidir:

* Uygulamanın dizin üzerinde değişiklik yapması ve dizine yazma ihtiyacı olmadığından emin olun. Azure AD DS yönetilen bir etki alanına LDAP yazma erişimi desteklenmez.
* Parolaları doğrudan Azure AD DS yönetilen bir etki alanına karşı değiştiremezsiniz. Son kullanıcılar, Azure AD 'nin self servis parola değiştirme mekanizmasını veya şirket içi dizine karşı parolalarını değiştirebilir. Bu değişiklikler daha sonra otomatik olarak eşitlenir ve Azure AD DS yönetilen etki alanında kullanılabilir.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>Dizine erişmek için LDAP Read kullanan şirket içi uygulamaları kaldırın ve taşıyın

Önceki örnek senaryoya benzer şekilde, contoso 'nun neredeyse yılda bir önce geliştirilen şirket içi iş kolu (LOB) uygulaması olduğunu varsayalım. Bu uygulama, Dizin farkınındır ve AD DS kullanıcılar hakkındaki bilgileri/öznitelikleri okumak için LDAP kullanmak üzere tasarlanmıştır. Uygulama, öznitelikleri değiştirmez veya başka bir şekilde dizine yazamaz.

Contoso bu uygulamayı Azure 'a geçirmek ve şu anda bu uygulamayı barındırmakta olan şirket içi donanımı devre dışı bırakmak istiyor. Uygulama, REST tabanlı Azure AD Graph API gibi modern Dizin API 'Lerini kullanmak için yeniden yazılabilir. Uygulama, kod değiştirmeden veya uygulamayı yeniden yazmadan bulutta çalışmak üzere geçirilebileceği bir kaldırma ve kaydırma seçeneği tercih edilir.

Azure AD DS, bu senaryoya yardımcı olmak için uygulamaların, ihtiyaç duyacağı öznitelik bilgilerini almak için yönetilen etki alanında LDAP okumaları gerçekleştirmesini sağlar. Uygulamanın yeniden yazılması gerekmez, bu nedenle Azure 'a bir yükseltme ve kaydırma, kullanıcıların, çalıştığı yerde bir değişiklik yapmadan uygulamayı kullanmaya devam etmesine olanak tanır.

### <a name="deployment-notes"></a>Dağıtım notları

Aşağıdaki dağıtım konuları bu örnek kullanım örneği için geçerlidir:

* Uygulamanın dizin üzerinde değişiklik yapması ve dizine yazma ihtiyacı olmadığından emin olun. Azure AD DS yönetilen bir etki alanına LDAP yazma erişimi desteklenmez.
* Uygulamanın özel/genişletilmiş Active Directory şemasına ihtiyacı olmadığından emin olun. Şema uzantıları Azure AD DS desteklenmez.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>Şirket içi hizmet veya Daemon uygulamasını Azure 'a geçirme

Bazı uygulamalar, katmanların bir veritabanı gibi bir arka uç katmanına kimliği doğrulanmış çağrılar gerçekleştirmesi gereken birden çok katmanı içerir. AD hizmet hesapları, bu senaryolarda yaygın olarak kullanılır. Uygulamaları Azure 'a kaldırıp sürüklerken Azure AD DS, hizmet hesaplarını aynı şekilde kullanmaya devam etmenizi sağlar. Şirket içi dizininizden Azure AD 'ye eşitlenen aynı hizmet hesabını kullanmayı tercih edebilir veya özel bir OU oluşturup bu OU 'da ayrı bir hizmet hesabı oluşturabilirsiniz. Her iki yaklaşım ile de uygulamalar diğer katmanlara ve hizmetlere kimliği doğrulanmış çağrılar yapmak için aynı şekilde çalışmaya devam eder.

![WIA kullanan hizmet hesabı](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Bu örnek senaryoda contoso, Web ön ucu, SQL Server ve arka uç FTP sunucusu içeren özel olarak oluşturulmuş bir yazılım Kasası uygulamasına sahiptir. Windows-tümleşik kimlik doğrulaması hizmet hesaplarını kullanan Web ön ucunun FTP sunucusuna kimliğini doğrular. Web ön ucu bir hizmet hesabı olarak çalışacak şekilde ayarlanır. Arka uç sunucusu, Web ön ucunun hizmet hesabından erişim yetkisi verecek şekilde yapılandırılmıştır. Contoso, bu uygulamayı Azure 'a taşımak için bulutta kendi etki alanı denetleyicisi sanal makinelerini dağıtıp yönetmek istemiyor.

Bu senaryo için, Web ön ucu, SQL Server ve FTP sunucusunu barındıran sunucular Azure sanal makinelerine geçirilebilir ve Azure AD DS yönetilen bir etki alanına eklenebilir. VM 'Ler daha sonra Azure AD aracılığıyla Azure AD Connect kullanılarak eşitlenen, uygulamanın kimlik doğrulama amaçları için şirket içi dizininde aynı hizmet hesabını kullanabilir.

### <a name="deployment-notes"></a>Dağıtım notları

Aşağıdaki dağıtım konuları bu örnek kullanım örneği için geçerlidir:

* Uygulamalarda kimlik doğrulaması için bir Kullanıcı adı + parola kullandığınızdan emin olun. Sertifika veya akıllı kart tabanlı kimlik doğrulaması Azure AD DS tarafından desteklenmez.
* Parolaları doğrudan Azure AD DS yönetilen bir etki alanına karşı değiştiremezsiniz. Son kullanıcılar, Azure AD 'nin self servis parola değiştirme mekanizmasını veya şirket içi dizine karşı parolalarını değiştirebilir. Bu değişiklikler daha sonra otomatik olarak eşitlenir ve Azure AD DS yönetilen etki alanında kullanılabilir.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Azure 'da Windows Server Uzak Masaüstü Hizmetleri dağıtımları

Azure AD DS, Azure 'da dağıtılan uzak masaüstü sunucularına yönetilen etki alanı hizmetleri sağlamak için kullanabilirsiniz. Bu dağıtım senaryosu hakkında daha fazla bilgi için bkz. [Azure AD DOMAIN SERVICES RDS dağıtımıyla tümleştirme][windows-rds].

## <a name="domain-joined-hdinsight-clusters-preview"></a>Etki alanına katılmış HDInsight kümeleri (Önizleme)

Apache Ranger etkin olan Azure AD DS tarafından yönetilen bir etki alanına katılmış bir Azure HDInsight kümesi oluşturabilirsiniz. Apache Ranger aracılığıyla Hive ilkeleri oluşturup uygulayabilir ve veri bilimcileri gibi kullanıcıların Excel veya Tableau gibi ODBC tabanlı araçları kullanarak Hive 'a bağlanmasına izin verebilirsiniz. Etki alanına katılmış HDInsight 'a HBase, Spark ve fırtınası gibi diğer iş yüklerini eklemek için çalışmaya devam ediyoruz.

Bu dağıtım senaryosu hakkında daha fazla bilgi için bkz. [etki alanına katılmış HDInsight kümelerini yapılandırma][hdinsight]

## <a name="next-steps"></a>Sonraki adımlar

Başlamak için [Azure Active Directory Domain Services bir örnek oluşturun ve yapılandırın][tutorial-create-instance]

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure.md
[tutorial-create-instance]: tutorial-create-instance.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds
