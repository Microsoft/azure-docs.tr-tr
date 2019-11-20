---
title: Azure portal bir Service Fabric kümesi oluşturun | Microsoft Docs
description: Azure 'da Azure portal ve Azure Key Vault kullanarak güvenli Service Fabric kümesi ayarlamayı öğrenin.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: vturecek
ms.assetid: 426c3d13-127a-49eb-a54c-6bde7c87a83b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/06/2018
ms.author: atsenthi
ms.openlocfilehash: ca050e19ed37e2895507507338ebf4b4217851b6
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934053"
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Azure 'da Azure portal kullanarak Service Fabric kümesi oluşturma
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure portalda](service-fabric-cluster-creation-via-portal.md)
> 
> 

Bu, Azure portal kullanarak Azure 'da Service Fabric kümesi (Linux veya Windows) ayarlama adımlarında size yol gösterecek adım adım bir kılavuzdur. Bu kılavuzda aşağıdaki adımlarda izlenecek yol gösterilmektedir:

* Azure 'da Azure portal aracılığıyla bir küme oluşturun.
* Sertifikaları kullanarak yöneticilerin kimliğini doğrulayın.

> [!NOTE]
> Azure Active Directory ile Kullanıcı kimlik doğrulaması ve uygulama güvenliği için sertifika ayarlama gibi daha gelişmiş güvenlik seçenekleri için, [Azure Resource Manager kullanarak kümenizi oluşturun][create-cluster-arm].
> 
> 

## <a name="cluster-security"></a>Küme güvenliği 
Sertifikalar, Service Fabric’te bir küme ile uygulamalarının çeşitli yönlerini güvenli hale getirmek üzere kimlik doğrulaması ve şifreleme sağlamak için kullanılır. Sertifikaların Service Fabric’te kullanılmasıyla ilgili daha fazla bilgi için bkz. [Service Fabric kümesi güvenlik senaryoları][service-fabric-cluster-security].

İlk kez bir Service Fabric kümesi oluşturuyorsanız veya test iş yükleri için bir küme dağıtıyorsanız, sonraki bölüme atlayabilirsiniz (**Azure portalında küme oluştur**) ve sistemin çalıştıran kümeleriniz için gereken sertifikaları oluşturmasını sağlayabilirsiniz. test iş yükleri. Üretim iş yükleri için bir küme ayarlıyorsanız okumaya devam edin.

#### <a name="cluster-and-server-certificate-required"></a>Küme ve sunucu sertifikası (gerekli)
Bu sertifika, bir kümenin güvenliğini sağlamak ve bu sertifikaya yetkisiz erişimi engellemek için gereklidir. Küme güvenliğini birkaç yolla sağlar:

* **Küme kimlik doğrulaması:** Küme Federasyonu için düğümden düğüme iletişimin kimliğini doğrular. Yalnızca bu sertifikayla kimliklerini kanıtlayabilirler ve kümeye katılabilirler.
* **Sunucu kimlik doğrulaması:** Yönetim istemcisinin gerçek kümeyle konuştureceğini bilmesini sağlamak için, küme yönetimi uç noktalarının bir yönetim istemcisinde kimliğini doğrular. Bu sertifika, HTTPS yönetim API 'SI ve HTTPS üzerinden Service Fabric Explorer için de SSL sağlar.

Bu amaçlarla kullanılmak üzere, sertifika aşağıdaki gereksinimlere uymalıdır:

* Sertifika bir özel anahtar içermelidir.
* Sertifika, anahtar değişimi için, kişisel bilgi değişimi (. pfx) dosyasına verilebilir şekilde oluşturulmalıdır.
* Sertifikanın konu adı, Service Fabric kümesine erişmek için kullanılan **etki alanı ile aynı olmalıdır** . Bu, kümenin HTTPS yönetim uç noktaları ve Service Fabric Explorer için SSL sağlamak üzere gereklidir. `.cloudapp.azure.com` etki alanı için bir sertifika yetkilisinden (CA) bir SSL sertifikası edinemezsiniz. Kümeniz için özel bir etki alanı adı alın. CA 'dan bir sertifika istediğinizde, sertifikanın konu adı kümeniz için kullanılan özel etki alanı adıyla aynı olmalıdır.

#### <a name="client-authentication-certificates"></a>İstemci kimlik doğrulama sertifikaları
Ek istemci sertifikaları, küme yönetim görevleri için yöneticilerin kimliğini doğrular. Service Fabric iki erişim düzeyine sahiptir: **yönetici** ve **salt okuma Kullanıcı**. En azından, yönetim erişimi için tek bir sertifika kullanılmalıdır. Ek Kullanıcı düzeyinde erişim için ayrı bir sertifika sağlanmalıdır. Erişim rolleri hakkında daha fazla bilgi için bkz. [Service Fabric istemcileri için rol tabanlı erişim denetimi][service-fabric-cluster-security-roles].

Service Fabric çalışmak için Key Vault Istemci kimlik doğrulama sertifikalarını karşıya yüklemeniz gerekmez. Bu sertifikaların yalnızca küme yönetimi için yetkilendirilmiş kullanıcılara sağlanması gerekir. 

> [!NOTE]
> Azure Active Directory, küme yönetimi işlemlerine yönelik istemcilerin kimliğini doğrulamak için önerilen yoldur. Azure Active Directory kullanmak için [Azure Resource Manager kullanarak bir küme oluşturmanız][create-cluster-arm]gerekir.
> 
> 

#### <a name="application-certificates-optional"></a>Uygulama sertifikaları (isteğe bağlı)
Uygulama güvenliği amaçları için bir kümeye herhangi bir sayıda ek sertifika yüklenebilir. Kümenizi oluşturmadan önce, düğümlerde bir sertifikanın yüklü olması gereken uygulama güvenlik senaryolarını göz önünde bulundurun; örneğin:

* Uygulama yapılandırma değerlerinin şifrelenmesi ve şifresinin çözülmesi
* Çoğaltma sırasında düğümler arasında verilerin şifrelenmesi 

[Azure Portal aracılığıyla bir küme oluşturulurken](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-creation-via-portal.md)uygulama sertifikaları yapılandırılamaz. Küme kurulum zamanında uygulama sertifikalarını yapılandırmak için [Azure Resource Manager kullanarak bir küme oluşturmanız][create-cluster-arm]gerekir. Ayrıca, oluşturulduktan sonra kümeye uygulama sertifikaları ekleyebilirsiniz.

## <a name="create-cluster-in-the-azure-portal"></a>Azure portal küme oluştur

Uygulama gereksinimlerinizi karşılamak için bir üretim kümesi oluşturmak bazı planlamalar içerir, bu da size yardımcı olmak için [Service Fabric küme planlama konuları][service-fabric-cluster-capacity] belgesini okumanız ve anlamanız önemle tavsiye edilir. 

### <a name="search-for-the-service-fabric-cluster-resource"></a>Service Fabric kümesi kaynağını arayın

[Azure Portal][azure-portal]’ında oturum açın.
Yeni kaynak şablonu eklemek için **kaynak oluştur** ' a tıklayın. **Market** 'Teki Service Fabric kümesi şablonunu **her şey**için arayın.
Listeden **Service Fabric kümesi** seçin.

![Azure portal Service Fabric küme şablonu araması yapın.][SearchforServiceFabricClusterTemplate]

**Service Fabric kümesi** dikey penceresine gidin ve **Oluştur**' a tıklayın.

**Oluşturma Service Fabric kümesi** dikey penceresi aşağıdaki dört adıma sahiptir:

### <a name="1-basics"></a>1. temel bilgiler
![Yeni bir kaynak grubu oluşturma ekranının ekran görüntüsü.][CreateRG]

Temel bilgiler dikey penceresinde, kümenizin temel ayrıntılarını sağlamanız gerekir.

1. Kümenizin adını girin.
2. VM 'Ler için Uzak Masaüstü için bir **Kullanıcı adı** ve **parola** girin.
3. Özellikle birden fazla aboneliğiniz varsa, kümenizin dağıtılmasını istediğiniz **aboneliği** seçtiğinizden emin olun.
4. Yeni bir **kaynak grubu**oluşturun. Daha sonra, özellikle dağıtımınızda değişiklik yapmaya veya kümenizi silmeye çalışırken, bunları daha sonra bulmaya yardımcı olduğundan, bu kümeye kümeyle aynı adı vermek en iyisidir.
   
   > [!NOTE]
   > Var olan bir kaynak grubunu kullanmaya karar verebilirsiniz, ancak yeni bir kaynak grubu oluşturmak iyi bir uygulamadır. Bu, kümelerin ve kullandığı tüm kaynakların silinmesini kolaylaştırır.
   > 
   > 
5. Kümeyi oluşturmak istediğiniz **konumu** seçin. Zaten bir anahtar kasasına yüklediğiniz mevcut bir sertifikayı kullanmayı planlıyorsanız, anahtar kasanızın bulunduğu bölgeyi kullanmanız gerekir. 

### <a name="2-cluster-configuration"></a>2. küme yapılandırması
![Düğüm türü oluşturma][CreateNodeType]

Küme düğümlerinizi yapılandırın. Düğüm türleri, VM boyutlarını, VM sayısını ve bunların özelliklerini tanımlar. Kümeniz birden fazla düğüm türüne sahip olabilir, ancak Service Fabric sistem hizmetlerinin yerleştirildiği düğüm türü olduğundan, birincil düğüm türü (portalda tanımladığınız ilk bir tane) en az beş sanal makineye sahip olmalıdır. "NodeTypeName" öğesinin varsayılan yerleştirme özelliği otomatik olarak eklendiğinden **yerleştirme özelliklerini** yapılandırmayın.

> [!NOTE]
> Birden çok düğüm türü için yaygın bir senaryo, ön uç hizmeti ve arka uç hizmeti içeren bir uygulamadır. Ön uç hizmetini Internet 'e açık olan bağlantı noktalarıyla daha küçük VM 'Lere (D2_V2 gibi VM boyutları) yerleştirmek ve arka uç hizmetini Internet 'e yönelik bağlantı noktaları açık olan büyük VM 'Lere (D3_V2, D6_V2, D15_V2, vb. gibi VM boyutları ile) koymak istiyorsunuz.
> 

1. Düğüm türü için bir ad seçin (yalnızca harf ve sayı içeren 1 ile 12 karakter).
2. Birincil düğüm türü için en düşük sanal makine **boyutu** , küme Için seçtiğiniz **dayanıklılık katmanı** ile çalıştırılır. Dayanıklılık katmanı için varsayılan değer bronz ' dir. Dayanıklılık hakkında daha fazla bilgi için bkz. [Service Fabric kümesi dayanıklılığı seçme][service-fabric-cluster-durability].
3. **Sanal makine boyutunu**seçin. D serisi VM 'Ler SSD sürücülerine sahiptir ve durum bilgisi olan uygulamalar için önemle önerilir. Kısmi çekirdekleri olan veya 10 GB 'den az kullanılabilir disk kapasitesi olan herhangi bir VM SKU 'SU kullanmayın. VM boyutunu seçerken yardım almak için bkz. [Service Fabric küme planlama değerlendirmesi belgesi][service-fabric-cluster-capacity] .
4.  **Tek düğümlü küme ve üç düğümlü kümeler** yalnızca test kullanımı için tasarlanmıştır. Bunlar çalışan tüm üretim iş yükleri için desteklenmez.
5. Düğüm türü için **Ilk VM Ölçek kümesi kapasitesini** seçin. Üzerinde daha sonra bir düğüm türündeki sanal makine sayısını ölçeklendirebilir veya azaltabilirsiniz, ancak birincil düğüm türünde üretim iş yükleri için en az beş olur. Diğer düğüm türlerinde en az bir VM olabilir. Birincil düğüm türü için en az sanal makine **sayısı** , kümenizin **güvenilirliğini artırır** .  
6. **Özel uç noktaları**yapılandırın. Bu alan, uygulamalarınız için genel Internet 'e Azure Load Balancer göstermek istediğiniz, virgülle ayrılmış bağlantı noktalarının bir listesini girmenize olanak sağlar. Örneğin, kümenize bir Web uygulaması dağıtmayı planlıyorsanız, "80" girerek kümenize bağlantı noktası 80 üzerinde trafiğe izin verin. Uç noktalar hakkında daha fazla bilgi için bkz. [uygulamalarla iletişim kurma][service-fabric-connect-and-communicate-with-services]
7. **Ters proxy 'Yi etkinleştirin**.  [Ters proxy Service Fabric](service-fabric-reverseproxy.md) , bir Service Fabric kümesinde çalışan mikro hizmetlerin, HTTP uç noktalarına sahip diğer hizmetlerle bulmasına ve iletişim kurmasına yardımcı olur.
8. **Küme yapılandırması** dikey penceresine geri dönün ve **+ Isteğe bağlı ayarları göster**altında küme **tanılamayı**yapılandırın. Varsayılan olarak, tanılama, sorun giderme sorunlarını gidermeye yardımcı olmak için kümenizde etkinleştirilir. Tanılamayı devre dışı bırakmak istiyorsanız, **durumu** **kapalı**olarak değiştirin. Tanılamayı **kapatmak önerilmez.** Zaten Application Insights projesi oluşturduysanız, uygulama izlemelerinin kendisine yönlendirilmesi için anahtarını sağlayın.
9. **DNS hizmetini dahil edin**.  [DNS hizmeti](service-fabric-dnsservice.md) , DNS protokolünü kullanarak diğer hizmetleri bulmanızı sağlayan isteğe bağlı bir hizmettir.
10. Kümenizi ayarlamak istediğiniz **doku yükseltme modunu** seçin. Sistemin kullanılabilir en son sürümü otomatik olarak seçmesini ve kümenizi yükseltmeye denemesini istiyorsanız **Otomatik**' i seçin. Desteklenen bir sürüm seçmek istiyorsanız modu **el ile**olarak ayarlayın. Doku yükseltme modu hakkında daha fazla bilgi için [Service Fabric kümesi yükseltme belgesine bakın.][service-fabric-cluster-upgrade]

> [!NOTE]
> Yalnızca desteklenen Service Fabric sürümlerini çalıştıran kümeleri destekliyoruz. **El ile** modunu seçtiğinizde, kümenizi desteklenen bir sürüme yükseltme sorumluluğunu alırsınız.
> 

### <a name="3-security"></a>3. güvenlik
![Azure portal üzerindeki güvenlik yapılandırmalarının ekran görüntüsü.][BasicSecurityConfigs]

Sizin için güvenli bir test kümesi ayarlamayı kolay hale getirmek için **temel** seçeneği sunuyoruz. Zaten bir sertifikanız varsa ve bunu [anahtar kasanıza](/azure/key-vault/) yüklediyseniz (ve dağıtım için anahtar kasasını etkinleştirdiyseniz), **özel** seçeneğini kullanın

#### <a name="basic-option"></a>Temel seçenek
Var olan bir anahtar kasasını eklemek veya yeniden kullanmak ve bir sertifika eklemek için ekranları izleyin. Sertifikanın eklenmesi zaman uyumlu bir işlemdir ve bu nedenle sertifikanın oluşturulmasını beklemeniz gerekir.

Önceki işlem tamamlanana kadar ekrandan uzaklaşmak için ekranın sonuna kadar yeniden gezinme.

![Createkeykasası]

Anahtar Kasası oluşturuldığına göre, anahtar kasanızın erişim ilkelerini düzenleyin. 

![CreateKeyVault2]

**Erişim Ilkelerini Düzenle**' ye tıklayın, ardından **Gelişmiş erişim Ilkeleri** ' ni gösterin ve dağıtım için Azure sanal makinelerine erişimi etkinleştirin. Şablon dağıtımını da etkinleştirmeniz önerilir. Seçimlerinizi yaptıktan sonra **Kaydet** düğmesine tıklamayın ve **erişim ilkeleri** bölmesini kapatın.

![CreateKeyVault3]

Sertifikanın adını girip **Tamam**' a tıklayın.

![CreateKeyVault4]

#### <a name="custom-option"></a>Özel seçenek
**Temel** seçeneğinde adımları zaten gerçekleştirdiyseniz bu bölümü atlayın.

![SecurityCustomOption]

Güvenlik sayfasını tamamlayabilmeniz için kaynak Anahtar Kasası, sertifika URL 'SI ve sertifika parmak izi bilgilerine ihtiyacınız vardır. İhtiyacınız yoksa, başka bir tarayıcı penceresi açın ve Azure portal aşağıdaki adımları izleyin

1. Anahtar Kasası hizmetinize gidin.
2. "Özellikler" sekmesini seçin ve diğer tarayıcı penceresinde ' kaynak KIMLIĞI ' ni "kaynak Anahtar Kasası" olarak kopyalayın 

    ![CertInfo0]

3. Şimdi "Sertifikalar" sekmesini seçin.
4. Sertifika parmak izi ' ne tıklayarak sürümler sayfasına gidin.
5. Geçerli sürüm altında gördüğünüz GUID 'lere tıklayın.

    ![CertInfo1]

6. Artık aşağıdaki gibi ekranda olmanız gerekir. On altılı SHA-1 parmak Izini diğer tarayıcı penceresinde "sertifika parmak izi" olarak Kopyala
7. ' Gizli dizi tanımlayıcısı ' nı diğer tarayıcı penceresinde "sertifika URL 'SI" olarak kopyalayın.

    ![CertInfo2]

**Yönetici istemcisi** ve **salt okunurdur istemci**için istemci sertifikaları girmek üzere **Gelişmiş ayarları Yapılandır** kutusunu işaretleyin. Bu alanlarda, varsa, yönetici istemci sertifikanızın parmak izini ve salt okunurdur Kullanıcı istemci sertifikanızın parmak izini girin. Yöneticiler kümeye bağlanmayı denediklerinde, yalnızca buraya girilen parmak izi değerleriyle eşleşen parmak izine sahip bir sertifika varsa erişim izni verilir.  

### <a name="4-summary"></a>4. Özet

Şimdi kümeyi dağıtmaya hazırsınız. Bunu yapmadan önce, sertifikayı indirin, bağlantı için büyük mavi bilgi kutusu içine bakın. Sertifikayı güvenli bir yerde tutduğunuzdan emin olun. kümenize bağlanmanız gerekir. İndirdiğiniz sertifikanın parolası olmadığından, bir tane eklemeniz önerilir.

Küme oluşturmayı tamamladıktan sonra **Oluştur**' a tıklayın. İsterseniz şablonu indirebilirsiniz.

![Özet]

Oluşturma işleminin ilerleme durumunu bildirimler bölümünden görebilirsiniz. (Ekranınızın sağ üst köşesindeki durum çubuğunun yanında bulunan "zil" simgesine tıklayın.) Kümeyi oluştururken başlangıç **panosuna sabitle ' ye** tıkladıysanız, **dağıtım Service Fabric kümenin** **Başlangıç** panosuna sabitlenmiş olduğunu görürsünüz. Bu işlem biraz zaman alacak. 

PowerShell veya CLı kullanarak kümenizde yönetim işlemleri gerçekleştirmek için kümenize bağlanmanız gerekir, [kümenize bağlanma](service-fabric-connect-to-secure-cluster.md)hakkında daha fazla bilgi edinin.

## <a name="view-your-cluster-status"></a>Küme durumunuzu görüntüleme
![Panodaki küme ayrıntılarının ekran görüntüsü.][ClusterDashboard]

Kümeniz oluşturulduktan sonra, portalda kümenizi inceleyebilirsiniz:

1. Git ' **e gidin** ve **kümeler Service Fabric**' a tıklayın.
2. Kümenizi bulun ve tıklatın.
3. Kümenin genel uç noktası ve bir Service Fabric Explorer bağlantısıyla birlikte kümenizin ayrıntılarını panoda görebilirsiniz.

Kümenin Pano dikey penceresindeki **düğüm İzleyicisi** bölümü sağlıklı ve sağlıklı olmayan VM 'lerin sayısını belirtir. Kümenin sistem durumu hakkında daha fazla ayrıntıyı [Service Fabric sistem durumu modeli girişi][service-fabric-health-introduction]' nde bulabilirsiniz.

> [!NOTE]
> Service Fabric kümeler, kullanılabilirliği sürdürmek ve durumu korumak için her zaman belirli sayıda düğüm olmasını gerektirir. "çekirdek Bakımı" olarak adlandırılır. Bu nedenle, ilk olarak [durumlarınızın tam yedeklemesini][service-fabric-reliable-services-backup-restore]gerçekleştirmediğiniz müddetçe kümedeki tüm makineleri kapatmak güvenli değildir.
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Sanal makine ölçek kümesi örneğine veya bir küme düğümüne uzaktan Bağlan
Kümenizde belirttiğiniz NodeTypes 'ın her biri, bir sanal makine ölçek kümesi ayarlamaya neden olur. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Sonraki adımlar
Bu noktada, yönetim kimlik doğrulaması için sertifikaları kullanarak güvenli bir kümeniz vardır. Sonra, [kümenize bağlanın](service-fabric-connect-to-secure-cluster.md) ve [uygulama gizli dizilerini yönetmeyi](service-fabric-application-secret-management.md)öğrenin.  Ayrıca [Service Fabric destek seçenekleri](service-fabric-support.md)hakkında bilgi edinin.

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/key-vault-overview.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-cluster-durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[BasicSecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/BasicSecurityConfigs.PNG
[Createkeykasası]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault.PNG
[CreateKeyVault2]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault2.PNG
[CreateKeyVault3]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault3.PNG
[CreateKeyVault4]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault4.PNG
[CertInfo0]: ./media/service-fabric-cluster-creation-via-portal/CertInfo0.PNG
[CertInfo1]: ./media/service-fabric-cluster-creation-via-portal/CertInfo1.PNG
[CertInfo2]: ./media/service-fabric-cluster-creation-via-portal/CertInfo2.PNG
[SecurityCustomOption]: ./media/service-fabric-cluster-creation-via-portal/SecurityCustomOption.PNG
[DownloadCert]: ./media/service-fabric-cluster-creation-via-portal/DownloadCert.PNG
[Özet]: ./media/service-fabric-cluster-creation-via-portal/Summary.PNG
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
