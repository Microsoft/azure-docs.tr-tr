---
title: Azure Service Fabric kümesinin güvenliğini sağlama | Microsoft Docs
description: Azure Service Fabric kümesi için güvenlik senaryoları ve bunları uygulamak için kullanabileceğiniz çeşitli teknolojiler hakkında bilgi edinin.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 26b58724-6a43-4f20-b965-2da3f086cf8a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/14/2018
ms.author: atsenthi
ms.openlocfilehash: cf808bef75a73cef6e8c17045506f29fabf3b52e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819439"
---
# <a name="service-fabric-cluster-security-scenarios"></a>Service Fabric kümesi güvenlik senaryoları
Azure Service Fabric kümesi, sahip olduğunuz bir kaynaktır. Yetkisiz kullanıcıların bunlara bağlanmasını önlemeye yardımcı olmak için kümelerinizin güvenliğini sağlamak sizin sorumluluğunuzdadır. Küme üzerinde üretim iş yüklerini çalıştırırken güvenli bir küme özellikle önemlidir. Güvenli olmayan bir küme oluşturmak mümkündür, ancak küme yönetim uç noktalarını genel İnternet 'e alıyorsa anonim kullanıcılar bu sunucuya bağlanabilir. Güvenli olmayan kümeler üretim iş yükleri için desteklenmez. 

Bu makalede, Azure kümeleri ve tek başına kümeler için güvenlik senaryolarına ve bunları uygulamak için kullanabileceğiniz çeşitli teknolojilerle ilgili bir genel bakış sunulmaktadır:

* Düğümden düğüme güvenlik
* İstemciden düğüme güvenlik
* Rol Tabanlı Erişim Denetimi (RBAC)

## <a name="node-to-node-security"></a>Düğümden düğüme güvenlik
Düğümden düğüme güvenlik, bir kümedeki VM 'Ler veya bilgisayarlar arasındaki iletişimin güvenliğinin sağlanmasına yardımcı olur. Bu güvenlik senaryosu, yalnızca kümeye katılma yetkisi olan bilgisayarların kümedeki uygulamaları ve Hizmetleri barındırmak için katılmasına olanak sağlar.

![Düğümden düğüme iletişimin diyagramı][Node-to-Node]

Azure 'da çalıştırılan kümeler ve Windows üzerinde çalışan tek başına kümeler, Windows Server bilgisayarları için [sertifika güvenliği](https://msdn.microsoft.com/library/ff649801.aspx) veya [Windows güvenliği](https://msdn.microsoft.com/library/ff649396.aspx) kullanabilir.

### <a name="node-to-node-certificate-security"></a>Düğümden düğüme Sertifika güvenliği
Service Fabric, küme oluştururken düğüm türü yapılandırmanın bir parçası olarak belirttiğiniz X. 509.952 sunucu sertifikalarını kullanır. Bu makalenin sonunda, bu sertifikaların ne olduğuna ve bunları nasıl elde edebilir veya oluşturabileceğiniz hakkında kısa bir genel bakış görebilirsiniz.

Azure portal, bir Azure Resource Manager şablonu kullanarak ya da tek başına bir JSON şablonu kullanarak küme oluştururken sertifika güvenliği ayarlayın. Service Fabric SDK 'nın varsayılan davranışı, sertifikayı, gelecekteki süresi dolan sertifikaya en uzdan dağıtmaktır. Klasik davranış, el ile başlatılan rollover 'lar için birincil ve ikincil sertifikaların tanımlanmasına izin verilir ve yeni işlevsellik üzerinde kullanılması önerilmez. Kullanılacak olan birincil sertifikaların gelecekteki kullanım süresi dolmak üzere olması, yönetim istemcisinden ve [istemciden düğüme güvenlik](#client-to-node-security)için ayarladığınız Salt okunabilir istemci sertifikalarından farklı olmalıdır.

Azure için bir kümede sertifika güvenliği ayarlamayı öğrenmek için bkz. [Azure Resource Manager şablonu kullanarak küme ayarlama](service-fabric-cluster-creation-via-arm.md).

Tek başına Windows Server kümesi için bir kümede sertifika güvenliği ayarlama hakkında bilgi edinmek için bkz. [X. 509.440 sertifikalarını kullanarak Windows 'da tek başına kümeyi güvenli hale getirme](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Düğümden düğüme Windows güvenliği
Tek başına bir Windows Server kümesi için Windows güvenliği ayarlama hakkında bilgi edinmek için bkz. Windows [güvenliği kullanarak Windows 'da tek başına kümeyi güvenli hale getirme](service-fabric-windows-cluster-windows-security.md).

## <a name="client-to-node-security"></a>İstemciden düğüme güvenlik
İstemciden düğüme güvenlik, istemcilerin kimliğini doğrular ve kümedeki istemci ve tek düğümler arasındaki iletişimin güvenliğini sağlamaya yardımcı olur. Bu tür bir güvenlik, kümeye ve kümeye dağıtılan uygulamalara yalnızca yetkili kullanıcıların erişebildiğinden emin olmanıza yardımcı olur. İstemciler, Windows güvenlik kimlik bilgileri veya sertifika güvenlik kimlik bilgileri aracılığıyla benzersiz şekilde tanımlanır.

![İstemciden düğüme iletişimin diyagramı][Client-to-Node]

Azure 'da çalıştırılan kümeler ve Windows üzerinde çalışan tek başına kümeler, [sertifika güvenliği](https://msdn.microsoft.com/library/ff649801.aspx) veya [Windows güvenliği](https://msdn.microsoft.com/library/ff649396.aspx)kullanabilir.

### <a name="client-to-node-certificate-security"></a>İstemciden düğüme Sertifika güvenliği
Azure portal, bir Kaynak Yöneticisi şablonu kullanarak veya tek başına bir JSON şablonu kullanarak küme oluştururken istemciden düğüme Sertifika güvenliği ayarlayın. Sertifikayı oluşturmak için, bir yönetici istemci sertifikası veya bir kullanıcı istemci sertifikası belirtin. En iyi uygulama olarak, belirttiğiniz yönetici istemcisi ve Kullanıcı istemci sertifikaları, [düğümden düğüme güvenlik](#node-to-node-security)için belirttiğiniz birincil ve ikincil sertifikalardan farklı olmalıdır. Küme sertifikaları, istemci yönetici sertifikalarıyla aynı haklara sahiptir. Bununla birlikte, yalnızca bir güvenlik en iyi uygulaması olarak yönetici kullanıcılar tarafından değil, yalnızca küme tarafından kullanılmalıdır.

Yönetici sertifikasını kullanarak kümeye bağlanan istemciler, yönetim özelliklerine tam erişime sahiptir. Salt okuma Kullanıcı istemci sertifikasını kullanarak kümeye bağlanan istemciler yalnızca yönetim özelliklerine okuma erişimine sahiptir. Bu sertifikalar, bu makalenin ilerleyen kısımlarında açıklanan RBAC için kullanılır.

Azure için bir kümede sertifika güvenliği ayarlamayı öğrenmek için bkz. [Azure Resource Manager şablonu kullanarak küme ayarlama](service-fabric-cluster-creation-via-arm.md).

Tek başına Windows Server kümesi için bir kümede sertifika güvenliği ayarlama hakkında bilgi edinmek için bkz. [X. 509.440 sertifikalarını kullanarak Windows 'da tek başına kümeyi güvenli hale getirme](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Azure 'da istemciden düğüme Azure Active Directory güvenliği
Azure AD, kuruluşların (kiracılar olarak bilinir) uygulamalara Kullanıcı erişimini yönetmesine olanak sağlar. Uygulamalar, Web tabanlı bir oturum açma kullanıcı arabirimine ve yerel bir istemci deneyimine sahip olanlara bölünmüştür. Henüz bir kiracı oluşturmadıysanız, [Azure Active Directory kiracının nasıl alınacağını][active-directory-howto-tenant]okuyarak başlayın.

Service Fabric kümesi, Web tabanlı [Service Fabric Explorer][service-fabric-visualizing-your-cluster] ve [Visual Studio][service-fabric-manage-application-in-visual-studio]da dahil olmak üzere yönetim işlevlerine birkaç giriş noktası sunar. Sonuç olarak, küme, tek bir Web uygulaması ve bir yerel uygulama için erişimi denetlemek üzere iki Azure AD uygulaması oluşturursunuz.

Azure üzerinde çalışan kümeler için Azure Active Directory (Azure AD) kullanarak yönetim uç noktalarına erişimi de güvenli hale getirebilirsiniz. Gerekli Azure AD yapıtlarını oluşturma ve kümeyi oluştururken bunları doldurma hakkında bilgi edinmek için bkz. [Azure AD 'yi istemci kimlik doğrulaması Için ayarlama](service-fabric-cluster-creation-setup-aad.md).

## <a name="security-recommendations"></a>Güvenlik önerileri
Azure 'da barındırılan ortak bir ağda dağıtılan Service Fabric kümeleri için istemciden düğüme karşılıklı kimlik doğrulaması önerisi:
*   İstemci kimliği için Azure Active Directory kullan
*   HTTP iletişiminin sunucu kimliği ve SSL şifrelemesi için bir sertifika

Azure 'da barındırılan ortak bir ağda dağıtılan Service Fabric kümeleri için düğümden düğüme güvenlik önerisi, düğümlerin kimliğini doğrulamak için bir küme sertifikası kullanmaktır. 


Tek başına Windows Server kümeleri için Windows Server 2012 R2 ve Windows Active Directory varsa, grup yönetilen hizmet hesaplarıyla Windows güvenliği kullanmanızı öneririz. Aksi takdirde, Windows Güvenlik ile Windows hesaplarını kullanın.

## <a name="role-based-access-control-rbac"></a>Rol Tabanlı Erişim Denetimi (RBAC)
Farklı Kullanıcı grupları için belirli küme işlemlerine erişimi sınırlandırmak için erişim denetimi kullanabilirsiniz. Bu, kümenin daha güvenli olmasına yardımcı olur. Bir kümeye bağlanan istemciler için iki erişim denetimi türü desteklenir: yönetici rolü ve Kullanıcı rolü.

Yönetici rolüne atanan kullanıcıların, okuma ve yazma özellikleri dahil olmak üzere yönetim özelliklerine tam erişimi vardır. Kullanıcı rolüne atanan kullanıcılar varsayılan olarak, yönetim özelliklerine yalnızca okuma erişimine sahiptir (örneğin, sorgu özellikleri). Ayrıca, uygulama ve hizmetleri de çözümleyebilir.

Kümeyi oluştururken yönetici ve Kullanıcı istemci rollerini ayarlayın. Her rol türü için ayrı kimlikler (örneğin, sertifikalar veya Azure AD kullanarak) sağlayarak roller atayın. Varsayılan erişim denetimi ayarları ve varsayılan ayarların nasıl değiştirileceği hakkında daha fazla bilgi için bkz. [Service Fabric istemcileri Için rol tabanlı Access Control](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>X. 509.440 sertifikaları ve Service Fabric
X. 509.440 dijital sertifikaları genellikle istemcilerin ve sunucuların kimliğini doğrulamak için kullanılır. İletileri şifrelemek ve dijital olarak imzalamak için de kullanılır. Service Fabric, bir kümeyi güvenli hale getirmek ve uygulama güvenliği özellikleri sağlamak için X. 509.440 sertifikaları kullanır. X. 509.952 dijital sertifikaları hakkında daha fazla bilgi için bkz. [sertifikalarla çalışma](https://msdn.microsoft.com/library/ms731899.aspx). Azure 'da Service Fabric kümeler için sertifikaları yönetmek üzere [Key Vault](../key-vault/key-vault-overview.md) kullanırsınız.

Göz önünde bulundurmanız gereken bazı önemli noktalar:

* Üretim iş yüklerini çalıştıran kümeler için sertifikalar oluşturmak üzere, doğru yapılandırılmış bir Windows Server sertifika hizmetini veya bir onaylanmış [sertifika yetkilisinden (CA)](https://en.wikipedia.org/wiki/Certificate_authority)bir tane kullanın.
* Bir üretim ortamında MakeCert. exe gibi araçları kullanarak oluşturduğunuz geçici veya test sertifikalarını hiçbir şekilde kullanmayın.
* Otomatik olarak imzalanan bir sertifika kullanabilirsiniz, ancak yalnızca bir test kümesinde olabilir. Üretimde kendinden imzalı bir sertifika kullanmayın.
* Sertifika parmak izini oluştururken, bir SHA1 parmak izi üretdiğinizden emin olun. SHA1, Istemci ve küme sertifikası parmak izlerini yapılandırırken kullanılan şeydir.

### <a name="cluster-and-server-certificate-required"></a>Küme ve sunucu sertifikası (gerekli)
Bir kümenin güvenliğini sağlamak ve yetkisiz erişimi engellemek için bu sertifikalar (bir birincil ve isteğe bağlı olarak bir ikincil) gereklidir. Bu sertifikalar, küme ve sunucu kimlik doğrulaması sağlar.

Küme kimlik doğrulaması, küme Federasyonu için düğümden düğüme iletişimin kimliğini doğrular. Yalnızca bu sertifikayla kimliklerini kanıtlayabilirler ve kümeye katılabilirler. Sunucu kimlik doğrulaması, yönetim istemcisinin gerçek kümeyle konuştureceğini ve bir ' ortadaki bir ' Man ' değil ' i öğrendiğinden emin olmak için küme yönetim uç noktalarının kimliğini doğrular. Bu sertifika, HTTPS yönetim API 'SI için SSL ve HTTPS üzerinden Service Fabric Explorer için de bir SSL sağlar. Bir istemci veya düğüm bir düğümün kimliğini doğruladığında, ilk denetimlerinden biri **Konu** alanındaki ortak adın değeridir. Bu ortak ad veya sertifikaların konu diğer adları (San 'Lar), izin verilen ortak adlar listesinde bulunmalıdır.

Sertifikanın aşağıdaki gereksinimleri karşılaması gerekir:

* Sertifika bir özel anahtar içermelidir. Bu sertifikaların genellikle uzantıları. pfx veya. ped vardır  
* Sertifika, kişisel bilgi değişimi (. pfx) dosyasına verilebilir olan anahtar değişim için oluşturulmalıdır.
* **Sertifikanın konu adı, Service Fabric kümesine erişmek için kullandığınız etki alanıyla aynı olmalıdır**. Bu eşleştirme, kümenin HTTPS yönetim uç noktası ve Service Fabric Explorer için bir SSL sağlamak üzere gereklidir. *. Cloudapp.azure.com etki alanı için bir sertifika yetkilisinden (CA) bir SSL sertifikası edinemezsiniz. Kümeniz için özel bir etki alanı adı edinmeniz gerekir. CA’dan sertifika istediğinizde sertifikanın konu adı, kümeniz için kullandığınız özel etki alanı adıyla eşleşmelidir.

Göz önünde bulundurulması gereken bazı şeyler:

* **Konu** alanı birden fazla değere sahip olabilir. Değer türünü belirtmek için her değere bir başlatma ön eki eklenir. Genellikle, başlatma **CN** 'dir ( *ortak ad*için); Örneğin, **CN = www\.contoso.com**. 
* **Konu** alanı boş olabilir. 
* İsteğe bağlı **konu alternatif adı** alanı doldurulmuşsa, hem sertifikanın ortak adına hem de San başına bir girişe sahip olmalıdır. Bunlar **DNS ad** değerleri olarak girilir. San 'Lara sahip sertifikalar oluşturmayı öğrenmek için bkz. güvenli bir [LDAP sertifikasına konu alternatif adı ekleme](https://support.microsoft.com/kb/931351).
* Sertifikanın **amaçlanan amaçlar** alanının değeri, **sunucu kimlik doğrulaması** veya **istemci kimlik doğrulaması**gibi uygun bir değer içermelidir.

### <a name="application-certificates-optional"></a>Uygulama sertifikaları (isteğe bağlı)
Uygulama güvenliği amaçları için bir kümeye herhangi bir sayıda ek sertifika yüklenebilir. Kümenizi oluşturmadan önce, düğümlerde bir sertifikanın yüklü olması gereken uygulama güvenlik senaryolarını göz önünde bulundurun; örneğin:

* Uygulama yapılandırma değerlerini şifreleme ve şifre çözme.
* Çoğaltma sırasında düğümler arasında verilerin şifrelenmesi.

Güvenli kümeler oluşturma kavramı, Linux veya Windows kümeleri olup olmalarından bağımsız olarak aynıdır.

### <a name="client-authentication-certificates-optional"></a>İstemci kimlik doğrulama sertifikaları (isteğe bağlı)
Yönetici veya Kullanıcı istemci işlemleri için herhangi bir sayıda ek sertifika belirtilebilir. İstemci, karşılıklı kimlik doğrulaması gerektiğinde bu sertifikayı kullanabilir. İstemci sertifikaları genellikle bir üçüncü taraf CA tarafından verilmez. Bunun yerine, geçerli kullanıcı konumunun kişisel deposu genellikle bir kök yetkilisi tarafından yerleştirilmiş istemci sertifikalarını içerir. Sertifikanın, **Istemci kimlik doğrulaması**için **amaçlanan bir amaç** değeri olmalıdır.  

Varsayılan olarak, küme sertifikasında yönetici istemci ayrıcalıkları vardır. Bu ek istemci sertifikaları kümeye yüklenmemelidir, ancak küme yapılandırmasında izin verilme olarak belirtilir.  Ancak, kümeye bağlanmak ve tüm işlemleri gerçekleştirmek için istemci sertifikalarının istemci makinelere yüklenmesi gerekir.

> [!NOTE]
> Bir Service Fabric kümesindeki tüm yönetim işlemleri sunucu sertifikaları gerektirir. İstemci sertifikaları yönetim için kullanılamaz.

## <a name="next-steps"></a>Sonraki adımlar
* [Kaynak Yöneticisi şablonu kullanarak Azure 'da küme oluşturma](service-fabric-cluster-creation-via-arm.md) 
* [Azure portal kullanarak bir küme oluşturma](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
