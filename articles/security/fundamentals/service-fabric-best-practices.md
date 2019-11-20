---
title: Azure Service Fabric en iyi güvenlik uygulamaları | Microsoft Docs
description: Bu makalede, Azure Service Fabric güvenliği için bir dizi en iyi yöntem sunulmaktadır.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2019
ms.author: tomsh
ms.openlocfilehash: dc063621e6b3e1d0d3e1a51d744ca9d9a6ef8c8d
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934622"
---
# <a name="azure-service-fabric-security-best-practices"></a>Azure Service Fabric güvenliği en iyi yöntemleri
Azure 'da uygulama dağıtmak hızlı, kolay ve ekonomik bir işlemdir. Bulut uygulamanızı üretime dağıtmadan önce, uygulamanızda güvenli kümeler uygulamaya yönelik temel ve önerilen en iyi yöntemler listesini gözden geçirin.

Azure Service Fabric; ölçeklenebilir ve güvenilir mikro hizmetleri paketlemeyi, dağıtmayı ve yönetmeyi kolaylaştırmayı sağlayan bir dağıtılmış sistemler platformudur. Service Fabric ayrıca bulut uygulamalarını geliştirme ve yönetme sürecinde karşılaşılan başlıca sorunların giderilmesini de sağlar. Geliştiriciler ve yöneticiler, karmaşık altyapı sorunlarını çözmeye çalışmak yerine görev açısından kritik, zorlu iş yüklerini uygulamaya odaklanabilir. Service Fabric, bu iş yüklerinin ölçeklenebilir, güvenilir ve yönetilebilir olmasını sağlar.

Her bir en iyi uygulama için şunları açıkladık:

-   En iyi yöntem.
-   Neden en iyi yöntemi uygulamanız gerekir.
-   En iyi yöntemi uygulamadıysanız ne olabilir?
-   En iyi uygulamayı nasıl sağlayabileceğinizi öğrenin.

Aşağıdaki Azure Service Fabric güvenliği en iyi yöntemlerini öneririz:

-   Güvenli kümeler oluşturmak için Azure Resource Manager şablonları ve Service Fabric PowerShell modülünü kullanın.
-   X. 509.440 sertifikalarını kullanın.
-   Güvenlik ilkelerini yapılandırın.
-   Reliable Actors güvenlik yapılandırmasını uygulayın.
-   Azure Service Fabric için SSL 'yi yapılandırın.
-   Azure Service Fabric ile ağ yalıtımı ve güvenliği kullanın.
-   Azure Key Vault güvenlik için yapılandırın.
-   Kullanıcıları rollere atayın.


## <a name="best-practices-for-securing-your-clusters"></a>Kümelerinizi güvenli hale getirmenin en iyi yöntemleri

Her zaman güvenli bir küme kullanın:
-   Sertifikaları kullanarak küme güvenliğini uygulayın.
-   Azure Active Directory (Azure AD) kullanarak istemci erişimi (yönetici ve salt okuma) sağlayın.

Otomatik dağıtımları kullan:
-   Gizli dizileri oluşturmak, dağıtmak ve ele almak için betikleri kullanın.
-   Gizli dizileri Azure Key Vault depolayın ve diğer tüm istemci erişimleri için Azure AD kullanın.
-   Gizli dizi için insan erişimi için kimlik doğrulaması gerektir.

Ayrıca, aşağıdaki yapılandırma seçeneklerini göz önünde bulundurun:
-   Azure ağ güvenlik grupları (NSG 'ler) kullanarak çevre ağları (sivilleştirilmiş bölgeler, DMZs ve ekran tarafından kullanılan alt ağlar olarak da bilinir) oluşturun.
-   Küme sanal makinelerine (VM 'Ler) erişin veya Uzak Masaüstü Bağlantısı ile geçiş sunucularını kullanarak kümenizi yönetin.

Özellikle bir küme üretimde çalışırken yetkisiz kullanıcıların bağlanmasını engellemek için kümeleriniz güvenli olmalıdır. Güvenli olmayan bir küme oluşturmak mümkün olsa da, küme yönetim uç noktalarını genel İnternet 'e kullanıma sunarsa anonim kullanıcılar kümenize bağlanabilir.

Çeşitli teknolojiler kullanarak küme güvenliğini uygulamak için üç [senaryo](../../service-fabric/service-fabric-cluster-security.md) vardır:

-   Düğümden düğüme güvenlik: Bu senaryo, sanal makineler ve kümedeki bilgisayarlar arasındaki iletişimin güvenliğini sağlar. Bu güvenlik biçimi, yalnızca kümeye katılması gereken bilgisayarların kümedeki uygulamaları ve Hizmetleri barındırmasını sağlar.
Bu senaryoda, Azure 'da çalışan kümeler ya da Windows üzerinde çalışan tek başına kümeler Windows Server makineleri için [sertifika güvenliği](../../service-fabric/service-fabric-windows-cluster-x509-security.md) veya [Windows güvenliği](../../service-fabric/service-fabric-windows-cluster-windows-security.md) kullanabilir.
-   İstemciden düğüme güvenlik: Bu senaryo Service Fabric istemci ile kümedeki tek tek düğümler arasındaki iletişimin güvenliğini sağlar.
-   Rol tabanlı Access Control (RBAC): Bu senaryo, kümeye erişen her yönetici ve Kullanıcı istemci rolü için ayrı kimlikler (sertifikalar, Azure AD vb.) kullanır. Kümeyi oluştururken rol kimliklerini belirtirsiniz.

>[!NOTE]
>**Azure kümeleri için güvenlik önerisi:** Düğümden düğüme güvenlik için istemcilerin ve sertifikaların kimliğini doğrulamak üzere Azure AD güvenliği ' ni kullanın.

Tek başına bir Windows kümesini yapılandırmak için bkz. [tek başına Windows kümesi için ayarları yapılandırma](../../service-fabric/service-fabric-cluster-manifest.md).

Güvenli bir küme oluşturmak için Azure Resource Manager şablonları ve Service Fabric PowerShell modülünü kullanın.
Azure Resource Manager şablonları kullanarak güvenli bir Service Fabric kümesi oluşturmaya yönelik adım adım yönergeler için, bkz. [Service Fabric kümesi oluşturma](../../service-fabric/service-fabric-cluster-creation-via-arm.md).

Azure Resource Manager şablonunu kullanın:
-   VM sanal sabit diskleri (VHD 'ler) için yönetilen depolamayı yapılandırmak üzere şablonu kullanarak kümenizi özelleştirin.
-   Kolay yapılandırma yönetimi ve denetimi için şablonu kullanarak kaynak grubunuzdaki değişiklikleri sürücü olarak gruplandırın.

Küme yapılandırmanızı kod olarak değerlendirin:
-   Dağıtım yapılandırlarınız denetlenirken tam olun.
-   Kaynaklarınızı doğrudan değiştirmek için örtük komutları kullanmaktan kaçının.

[Service Fabric uygulama yaşam döngüsünün](../../service-fabric/service-fabric-application-lifecycle.md) birçok yönü otomatikleştirilebilir. [Service Fabric PowerShell modülü](../../service-fabric/service-fabric-deploy-remove-applications.md#upload-the-application-package) , Azure Service Fabric uygulamalarını dağıtmak, yükseltmek, kaldırmak ve test etmek için ortak görevleri otomatikleştirir. Yönetilen API 'Ler ve uygulama yönetimi için HTTP API 'Leri de kullanılabilir.

## <a name="use-x509-certificates"></a>X. 509.440 sertifikalarını kullanma
, Kümelerinizi her zaman X. 509.440 sertifikaları veya Windows güvenliği kullanarak güvenli hale getirin. Güvenlik yalnızca küme oluşturma zamanında yapılandırılır. Küme oluşturulduktan sonra güvenliği açmak mümkün değildir.

Bir [küme sertifikası](../../service-fabric/service-fabric-windows-cluster-x509-security.md)belirtmek Için **clustercredentialtype** özelliğinin değerini x509 olarak ayarlayın. Dış bağlantılar için bir sunucu sertifikası belirtmek üzere **Servercredentialtype** özelliğini x509 olarak ayarlayın.

Ayrıca, aşağıdaki yöntemleri izleyin:
-   Doğru yapılandırılmış bir Windows Server sertifika hizmetini kullanarak üretim kümeleri için sertifikaları oluşturun. Ayrıca, onaylanmış bir sertifika yetkilisinden (CA) sertifikaları elde edebilirsiniz.
-   Sertifika, MakeCert. exe veya benzer bir araç kullanılarak oluşturulduysa, hiçbir şekilde üretim kümeleri için geçici veya test sertifikası kullanmayın.
-   Test kümeleri için otomatik olarak imzalanan bir sertifika kullanın, ancak üretim kümeleri için kullanmayın.

Küme güvenli değilse, herkes kümeye anonim olarak bağlanabilir ve yönetim işlemleri gerçekleştirebilir. Bu nedenle, her zaman X. 509.440 sertifikalarını veya Windows güvenliğini kullanarak üretim kümelerini güvenli hale getirin.

X. 509.440 sertifikalarını kullanma hakkında daha fazla bilgi için bkz. [Service Fabric kümesi için sertifika ekleme veya kaldırma](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md).

## <a name="configure-security-policies"></a>Güvenlik ilkelerini yapılandır
Service Fabric Ayrıca, uygulamalar tarafından kullanılan kaynakların güvenliğini sağlar. Dosyalar, dizinler ve sertifikalar gibi kaynaklar, uygulama dağıtıldığında Kullanıcı hesapları altında depolanır. Bu özellik, paylaşılan barındırılan bir ortamda bile, çalışan uygulamaların diğerinden daha güvenli olmasını sağlar.

-   Active Directory bir etki alanı grubu veya Kullanıcı kullanın: Active Directory Kullanıcı veya grup hesabı için kimlik bilgileri altında hizmeti çalıştırın. Azure Active Directory değil, etki alanı içinde şirket içi Active Directory kullandığınızdan emin olun. Etki alanındaki bir etki alanı kullanıcısı veya grubu kullanılarak izin verilen diğer kaynaklara erişin. Örneğin, dosya paylaşımları gibi kaynaklar.

-   HTTP ve HTTPS uç noktaları için bir güvenlik erişim ilkesi atama: Hizmet bildirimi uç nokta kaynaklarını HTTP ile bildirdiği zaman bir hizmete **runas** ilkesi uygulamak Için **securityaccesspolicy** özelliğini belirtin. HTTP uç noktalarına ayrılan bağlantı noktaları, hizmetin altında çalıştığı RunAs Kullanıcı hesabı için doğru şekilde erişim denetimli listelerdir. İlke ayarlanmamışsa, http. sys hizmete erişemez ve istemciden gelen çağrılarla ilgili hatalara sahip olabilirsiniz.

Service Fabric kümesinde güvenlik ilkeleri kullanmayı öğrenmek için bkz. [uygulamanız için güvenlik Ilkelerini yapılandırma](../../service-fabric/service-fabric-application-runas-security.md).

## <a name="implement-the-reliable-actors-security-configuration"></a>Reliable Actors güvenlik yapılandırmasını Uygula
Service Fabric Reliable Actors aktör tasarım deseninin bir uygulamasıdır. Tüm yazılım tasarımı modelinde olduğu gibi, belirli bir model kullanma kararı, bir yazılım sorununun modele uygun olup olmadığına bağlıdır.

Genel olarak, aşağıdaki yazılım sorunları veya güvenlik senaryolarıyla ilgili çözüm modeline yardımcı olması için aktör tasarım modelini kullanın:
-   Sorun alanınız, büyük bir sayı (binlerce veya daha fazla), küçük, bağımsız ve yalıtılmış birim ve mantık birimleri içerir.
-   Dış bileşenlerden önemli bir etkileşim gerektirmeyen tek iş parçacıklı nesneler ile çalışıyorsunuz, bu da durumu bir aktör kümesi genelinde sorgulama.
-   Aktör örneklerinizin g/ç işlemlerini vererek öngörülemeyen gecikmelerle arayan çağıranlar engellenmektedir.

Service Fabric, aktörler Reliable Actors uygulama çerçevesinde uygulanır. Bu çerçeve aktör düzenine dayalıdır ve [Service Fabric Reliable Services](../../service-fabric/service-fabric-reliable-services-introduction.md)üzerine kurulmuştur. Yazdığınız her güvenilir aktör hizmeti bölümlenmiş bir durum bilgisi olan güvenilir hizmettir.

Her aktör bir aktör türünün örneği olarak tanımlanır, .NET nesnesinin bir .NET türünün örneği olduğu şekilde aynıdır. Örneğin, bir hesaplayıcısının işlevselliğini uygulayan bir **aktör türü** , bir küme genelinde çeşitli düğümlere dağıtılan bu türden birçok aktöre sahip olabilir. Dağıtılmış aktörlerin her biri bir aktör tanımlayıcısıyla benzersiz bir şekilde belirlenir.

[Çoğaltıcı güvenlik yapılandırması](../../service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration.md) , çoğaltma sırasında kullanılan iletişim kanalının güvenliğini sağlamak için kullanılır. Bu yapılandırma, hizmetlerin birbirlerinin çoğaltma trafiğini görmesini ve yüksek oranda kullanılabilir verilerin güvende olmasını önler. Varsayılan olarak, boş bir güvenlik yapılandırması bölümü, çoğaltma güvenliğini engeller.
Çoğaltıcı yapılandırması, aktör durum sağlayıcısı durumunu yüksek düzeyde güvenilir hale getirmekten sorumlu olan çoğaltıcısını yapılandırır.

## <a name="configure-ssl-for-azure-service-fabric"></a>Azure Service Fabric için SSL 'yi yapılandırma
Sunucu kimlik doğrulama işlemi, küme yönetimi uç noktalarının bir yönetim istemcisinde [kimliğini doğrular](../../service-fabric/service-fabric-cluster-creation-via-arm.md) . Yönetim istemcisi daha sonra gerçek kümeyle konuşuyor olduğunu algılar. Bu sertifika, HTTPS yönetim API 'SI için SSL ve HTTPS üzerinden Service Fabric Explorer için de bir [SSL](../../service-fabric/service-fabric-cluster-creation-via-arm.md) sağlar.
Kümeniz için özel bir etki alanı adı edinmeniz gerekir. Bir sertifika yetkilisinden bir sertifika istediğinizde, sertifikanın konu adı kümeniz için kullandığınız özel etki alanı adıyla aynı olmalıdır.

Bir uygulamanın SSL 'yi yapılandırmak için, önce bir CA tarafından imzalanmış bir SSL sertifikası edinmeniz gerekir. CA, SSL güvenlik amaçları için sertifika veren, güvenilir bir üçüncü tarafdır. Henüz bir SSL sertifikanız yoksa, SSL sertifikaları satan bir şirketten bir tane edinmeniz gerekir.

Sertifikanın, Azure 'daki SSL sertifikaları için aşağıdaki gereksinimleri karşılaması gerekir:
-   Sertifika bir özel anahtar içermelidir.

-   Sertifika, anahtar değişimi için oluşturulmalı ve kişisel bilgi değişimi (. pfx) dosyasına verilebilir olmalıdır.

-   Sertifikanın konu adı, bulut hizmetinize erişmek için kullanılan etki alanı adıyla aynı olmalıdır.

    - Bulut hizmetinize erişmek için kullanmak üzere özel bir etki alanı adı alın.
    - Bir CA 'dan, hizmetinizin özel etki alanı adıyla eşleşen bir konu adına sahip bir sertifika isteyin. Örneğin, özel etki alanı adınız __contoso__ **. com**ise, CA 'nızdan alınan sertifikanın konu adı **. contoso.com** veya __www__ **. contoso.com**olmalıdır.

    >[!NOTE]
    >__Cloudapp__ **.net** etkı alanı IÇIN bir CA 'dan SSL sertifikası edinemezsiniz.

-   Sertifika en az 2.048 bit şifreleme kullanmalıdır.

HTTP Protokolü güvenli değildir ve gizlice dinleme saldırılarına tabidir. HTTP üzerinden aktarılan veriler Web tarayıcısından Web sunucusuna veya diğer uç noktalar arasında düz metin olarak gönderilir. Saldırganlar, kredi kartı ayrıntıları ve hesap oturum açmaları gibi HTTP aracılığıyla gönderilen hassas verileri ele geçirebilir ve görüntüleyebilir. Veriler HTTPS üzerinden gönderilirken veya bir tarayıcı aracılığıyla gönderildiğinde, SSL hassas bilgilerin şifrelenmesini ve ele geçirilmesini sağlar.

SSL sertifikalarını kullanma hakkında daha fazla bilgi için bkz. [Azure uygulamaları IÇIN SSL yapılandırma](../../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Azure Service Fabric ağ yalıtımı ve güvenliği kullanma
Örnek olarak [Azure Resource Manager şablonunu](../../azure-resource-manager/resource-group-authoring-templates.md) kullanarak 3 NodeType güvenli bir küme ayarlayın. Şablon ve ağ güvenlik gruplarını kullanarak gelen ve giden ağ trafiğini denetleyin.

Şablonda, sanal makine ölçek kümelerinin her biri için bir NSG vardır ve kümenin içinde ve dışında trafiği denetlemek için kullanılır. Kurallar, sistem hizmetleri ve şablonda belirtilen uygulama bağlantı noktaları için gereken tüm trafiğe izin vermek üzere varsayılan olarak yapılandırılır. Uygulamalarınız için yeni kurallar ekleme dahil olmak üzere bu kuralları gözden geçirin ve gereksinimlerinize uyacak değişiklikler yapın.

Daha fazla bilgi için bkz. [Azure Service Fabric Için genel ağ senaryoları](../../service-fabric/service-fabric-patterns-networking.md).

## <a name="set-up-azure-key-vault-for-security"></a>Güvenlik için Azure Key Vault ayarlama
Service Fabric, bir kümeyi ve uygulamalarını güvenli hale getirmek için kimlik doğrulama ve şifreleme sağlamak üzere sertifikaları kullanır.

Service Fabric, bir kümeyi güvenli hale getirmek ve uygulama güvenliği özellikleri sağlamak için X. 509.440 sertifikaları kullanır. Azure 'da Service Fabric kümeler için [sertifikaları yönetmek](../../service-fabric/service-fabric-cluster-security-update-certs-azure.md) üzere Azure Key Vault kullanırsınız. Kümeleri oluşturan Azure Kaynak sağlayıcısı, sertifikaları bir anahtar kasasından çeker. Daha sonra sağlayıcı, Azure üzerinde dağıtıldığında sertifikaları VM 'Lere yüklenir.

[Azure Key Vault](../../key-vault/key-vault-secure-your-key-vault.md), Service Fabric kümesi ve sertifikaları kullanan kaynak sağlayıcısı arasında bir sertifika ilişkisi bulunur. Küme oluşturulduğunda, sertifika ilişkisiyle ilgili bilgiler bir anahtar kasasında depolanır.

Bir Anahtar Kasası kurmak için iki temel adım vardır:
1. Anahtar kasanız için özel olarak bir kaynak grubu oluşturun.

    Anahtar kasasını kendi kaynak grubuna yerleştirmenizi öneririz. Bu eylem, depolama, işlem veya kümenizi içeren grup gibi diğer kaynak grupları kaldırılırsa, anahtarlarınızın ve sırlarınızın kaybolmasını önlemeye yardımcı olur. Anahtar kasanızı içeren kaynak grubunun, kendisini kullanan kümeyle aynı bölgede olması gerekir.

2. Yeni kaynak grubunda bir Anahtar Kasası oluşturun.

    Dağıtım için Anahtar Kasası etkinleştirilmelidir. İşlem kaynak sağlayıcısı daha sonra kasadan sertifikaları alabilir ve bunları sanal makine örneklerine yükleyebilir.

Bir anahtar kasasının nasıl ayarlanacağı hakkında daha fazla bilgi edinmek için bkz. [Azure Key Vault nedir?](../../key-vault/key-vault-overview.md).

## <a name="assign-users-to-roles"></a>Rollere kullanıcı atama
Kümenizi temsil etmek üzere uygulamalar oluşturduktan sonra, kullanıcılarınızı Service Fabric tarafından desteklenen rollere atayın: salt okunurdur ve yönetici. Azure portal kullanarak bu rolleri atayabilirsiniz.

>[!NOTE]
> Service Fabric rollerini kullanma hakkında daha fazla bilgi için bkz. [Service Fabric istemcileri Için rol tabanlı Access Control](../../service-fabric/service-fabric-cluster-security-roles.md).

Azure Service Fabric, bir [Service Fabric kümesine](../../service-fabric/service-fabric-cluster-creation-via-arm.md)bağlı istemciler için iki erişim denetimi türünü destekler: yönetici ve Kullanıcı. Küme Yöneticisi, farklı Kullanıcı grupları için belirli küme işlemlerine erişimi sınırlandırmak üzere erişim denetimini kullanabilir. Erişim denetimi kümenin daha güvenli olmasını sağlar.

## <a name="next-steps"></a>Sonraki adımlar

- [Service Fabric güvenlik denetim listesi](service-fabric-checklist.md)
- Service Fabric [geliştirme ortamınızı](../../service-fabric/service-fabric-get-started.md)ayarlayın.
- [Service Fabric destek seçenekleri](../../service-fabric/service-fabric-support.md)hakkında bilgi edinin.
