---
title: Azure depolama Güvenlik Kılavuzu | Microsoft Docs
description: Yönetim düzlemi güvenlik, yetkilendirme, ağ güvenliği, şifreleme vb. dahil olmak üzere Azure depolama hesaplarının güvenliğini sağlamaya yönelik ayrıntılar yöntemleri.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 15c59a29bff50f13eea104cb436d1a3764f6d713
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72926716"
---
# <a name="azure-storage-security-guide"></a>Azure depolama Güvenlik Kılavuzu

Azure depolama, kuruluşların güvenli uygulamalar oluşturup dağıtmalarına olanak tanıyan kapsamlı bir dizi güvenlik özelliği sağlar:

- Azure depolama 'ya yazılan tüm veriler (meta veriler dahil) [depolama hizmeti şifrelemesi (SSE)](storage-service-encryption.md)kullanılarak otomatik olarak şifrelenir. Daha fazla bilgi için bkz. [Azure Blobları, dosyalar, tablolar ve kuyruklar depolama Için varsayılan şifrelemeyi bildirme](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).
- Azure Active Directory (Azure AD) ve rol tabanlı Access Control (RBAC) hem kaynak yönetimi işlemleri hem de veri düzlemi işlemleri için desteklenir:   
    - Depolama hesabına kapsamlı RBAC rollerini güvenlik sorumlularına atayabilir ve anahtar yönetimi gibi kaynak yönetimi işlemlerini yetkilendirmek için Azure AD 'yi kullanabilirsiniz.
    - Azure AD tümleştirmesi, blob ve kuyruk verileri işlemleri için desteklenir. RBAC rolleri bir abonelik, kaynak grubu, depolama hesabı, tek bir kapsayıcı veya kuyruk kapsamına eklenebilir. Roller, bir güvenlik sorumlusuna veya Azure kaynakları için yönetilen kimliğe atanabilir. Daha fazla bilgi için bkz. [Azure Active Directory kullanarak Azure depolama 'ya erişim kimlik doğrulaması](storage-auth-aad.md).
- Veriler, [Istemci tarafı şifreleme](../storage-client-side-encryption.md), https veya SMB 3,0 kullanarak bir uygulama ile Azure arasında geçiş için güvenli hale getirilir.  
- Azure sanal makineleri tarafından kullanılan işletim sistemi ve veri diskleri, [Azure disk şifrelemesi](../../security/fundamentals/encryption-overview.md)kullanılarak şifrelenebilir.
- Azure Storage 'da veri nesnelerine temsilci erişimi, paylaşılan erişim imzası kullanılarak verilebilir. Daha fazla bilgi için bkz. [paylaşılan erişim imzaları (SAS) kullanarak Azure depolama kaynaklarına sınırlı erişim verme](storage-sas-overview.md).
- Uygulama bileşenleriniz ve depolama alanı arasındaki ağ katmanı güvenliği, depolama güvenlik duvarı, hizmet uç noktaları veya özel uç noktalar kullanılarak etkinleştirilebilir.

Bu makalede, Azure depolama ile kullanılabilecek olan bu güvenlik özelliklerinin her biri için bir genel bakış sunulmaktadır. Makalelere bağlantılar sağlandığında her bir özellik hakkında ek ayrıntılar sağlar.

Bu makalede ele alınan bölgeler şunlardır:

* [Yönetim düzlemi güvenliği](#management-plane-security) – depolama hesabınıza kaynak düzeyinde erişimin güvenliğini sağlama

  Yönetim düzlemi, depolama hesabınızı yönetmek için kullanılan işlemlerden oluşur. Bu bölümde Azure Resource Manager dağıtım modeli ve depolama hesaplarınıza erişimi denetlemek için rol tabanlı Access Control (RBAC) nasıl kullanılacağı ele alınmaktadır. Ayrıca, depolama hesabı anahtarlarınızı yönetme ve bunları yeniden oluşturma da ele alınmaktadır.

* [Ağ güvenliği](#network-security) -depolama hesabınıza ağ düzeyinde erişimin güvenliğini sağlama

  Bu bölüm, depolama hizmetleri uç noktalarına ağ düzeyinde erişimin nasıl güvence altına alınacağını anlatmaktadır. Belirli sanal ağlardan veya IP adresi aralıklarından verilerinize erişim sağlamak için depolama güvenlik duvarını nasıl kullanabileceğinizi anlatır. Ayrıca, hizmet uç noktalarının ve depolama hesaplarıyla özel uç noktaların kullanımını da ele alır.

* [Yetkilendirme](#authorization) – verilerinize erişimi yetkilendirme

  Bu bölümde, paylaşılan erişim Imzalarını ve depolanan erişim Ilkelerini kullanarak Depolama hesabınızdaki Bloblar, dosyalar, kuyruklar ve tablolar gibi veri nesnelerine yönelik erişim açıklanmaktadır. Hem hizmet düzeyi SAS hem de hesap düzeyi SAS 'ları ele alınacaktır. Ayrıca, erişimi belirli bir IP adresine (veya IP adresi aralığına) sınırlamayı, HTTPS için kullanılan protokolü sınırlamayı ve paylaşılan erişim Imzasının süre sonu beklemeden nasıl iptal edileceği de görüyoruz.

* [Aktarım Sırasında Şifreleme](#encryption-in-transit)

  Bu bölümde, Azure depolama alanına veya dışına aktarırken verilerin nasıl güvenliği anlatılmaktadır. Azure dosya paylaşımları için SMB 3,0 tarafından önerilen HTTPS ve şifrelemenin kullanılması hakkında konuşacağız. Ayrıca, depolama alanına aktarmadan önce verileri şifrelemenizi ve depolama dışına aktarıldıktan sonra verilerin şifresini çözmeyi sağlayan Istemci tarafı şifrelemesini de tartışacağız.

* [Bekleme Sırasında Şifreleme](#encryption-at-rest)

  Artık yeni ve var olan depolama hesapları için otomatik olarak etkinleştirilen Depolama Hizmeti Şifrelemesi (SSE) hakkında konuşacağız. Ayrıca, Azure disk şifrelemesi 'ni nasıl kullanabileceğinizi ve disk şifrelemesi ile Istemci tarafı şifrelemeye karşı temel farklılıkları ve durumları nasıl keşfededeceğiz. ABD devlet bilgisayarları için FIPS uyumluluğuna kısaca bakacağız.

* Azure Storage erişimini denetlemek için [depolama Analizi](#storage-analytics) kullanma

  Bu bölümde, bir istek için depolama Analizi günlüklerinde bilgi bulma açıklanmaktadır. Gerçek depolama analiz günlüğü verilerine göz atacağız ve depolama hesabı anahtarıyla bir isteğin, paylaşılan erişim imzası ile mi yoksa anonim olarak mı yoksa başarısız mı olduğunu ve başarılı olup olmadığını nasıl ayırt ettireceğiz.

* [CORS kullanarak tarayıcı tabanlı Istemcileri etkinleştirme](#cross-origin-resource-sharing-cors)

  Bu bölüm, çıkış noktaları arası kaynak paylaşımına (CORS) izin verme hakkında bilgi verir. Etki alanları arası erişim hakkında konuşacak ve Azure depolama 'da yerleşik CORS özellikleri ile nasıl işleneceğini inceleyeceğiz.

## <a name="management-plane-security"></a>Yönetim düzlemi güvenliği
Yönetim düzlemi, depolama hesabının kendisini etkileyen işlemlerden oluşur. Örneğin, bir depolama hesabı oluşturabilir veya silebilir, bir abonelikte depolama hesaplarının bir listesini alabilir, depolama hesabı anahtarlarını alabilir veya depolama hesabı anahtarlarını yeniden oluşturabilirsiniz.

Yeni bir depolama hesabı oluşturduğunuzda, klasik veya Kaynak Yöneticisi dağıtım modelini seçersiniz. Azure 'da kaynak oluşturmak için klasik model, yalnızca aboneliğe hiçbir şey erişimi ve depolama hesabını sırayla sağlar.

Bu kılavuz, depolama hesapları oluşturmak için önerilen yollarla Kaynak Yöneticisi modeline odaklanır. Kaynak Yöneticisi depolama hesapları ile aboneliğin tamamına erişim vermek yerine, rol tabanlı Access Control (RBAC) kullanarak yönetim düzlemine erişimi daha sınırlı bir düzeyde denetleyebilirsiniz.

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Rol tabanlı Access Control (RBAC) ile depolama hesabınızı güvenli hale getirme
RBAC 'in ne olduğu ve nasıl kullanılacağı hakkında konuşalım. Her Azure aboneliği bir Azure Active Directory’ye sahiptir. Bu dizindeki kullanıcılara, gruplara ve uygulamalara, Kaynak Yöneticisi dağıtım modelini kullanan Azure aboneliğindeki kaynakları yönetmek için erişim izni verilebilir. Bu tür bir güvenlik, rol tabanlı Access Control (RBAC) olarak adlandırılır. Bu erişimi yönetmek için [Azure Portal](https://portal.azure.com/), [Azure CLI araçları](../../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs)veya [Azure depolama kaynak sağlayıcısı REST API 'lerini](https://msdn.microsoft.com/library/azure/mt163683.aspx)kullanabilirsiniz.

Kaynak Yöneticisi modeliyle, depolama hesabını bir kaynak grubuna yerleştirip, bu belirli depolama hesabının yönetim düzlemine erişimi Azure Active Directory kullanarak kontrol edersiniz. Örneğin, belirli kullanıcılara depolama hesabı anahtarlarına erişme özelliği verebilirsiniz, ancak diğer kullanıcılar depolama hesabı hakkındaki bilgileri görüntüleyebilir, ancak depolama hesabı anahtarlarına erişemez.

#### <a name="granting-access"></a>Erişim verme
Kullanıcılara, gruplara ve uygulamalara uygun RBAC rolü doğru kapsamda atanarak erişim verilir. Tüm aboneliğe erişim vermek için abonelik düzeyinde bir rol atarsınız. Kaynak grubunun kendisine izinler vererek bir kaynak grubundaki tüm kaynaklara erişim izni verebilirsiniz. Depolama hesapları gibi belirli kaynaklara belirli roller de atayabilirsiniz.

Azure depolama hesabının yönetim işlemlerine erişmek için RBAC kullanma hakkında bilmeniz gereken ana noktaları burada bulabilirsiniz:

* Erişim atadığınızda, temelde erişimi olmasını istediğiniz hesaba bir rol atarsınız. Bu depolama hesabını yönetmek için kullanılan işlemlere erişimi denetleyebilir, ancak hesaptaki veri nesnelerine erişemezsiniz. Örneğin, depolama hesabının (artıklık gibi) özelliklerini alma izni verebilir, ancak BLOB depolama alanı içindeki bir kapsayıcı içindeki bir kapsayıcıya veya verilere uygulanmaz.
* Birisinin depolama hesabındaki veri nesnelerine erişim izni olması için, depolama hesabı anahtarlarını okumaya izin verebilir ve bu kullanıcı bu anahtarları blob, kuyruk, tablo ve dosyalara erişmek için kullanabilir.
* Roller belirli bir kullanıcı hesabına, bir kullanıcı grubuna veya belirli bir uygulamaya atanabilir.
* Her rol, eylemlerin bir listesini içerir. Örneğin, sanal makine katılımcısı rolü, depolama hesabı anahtarlarının okunmasını sağlayan bir "listKeys" eylemine sahiptir. Katkıda bulunan, Active Directory kullanıcıların erişimini güncelleştirme gibi "eylem değil".
* Depolama için roller şunları içerir (ancak bunlarla sınırlı değildir):

  * Sahip – erişim dahil her şeyi yönetebilir.
  * Katkıda bulunan: atama erişimi hariç, sahibinin yapabilecekleri her şeyi gerçekleştirebilir. Bu role sahip birisi depolama hesabı anahtarlarını görüntüleyebilir ve yeniden oluşturabilir. Depolama hesabı anahtarlarıyla, veri nesnelerine erişebilirler.
  * Okuyucu: parolalar dışında depolama hesabı hakkındaki bilgileri görüntüleyebilirler. Örneğin, depolama hesabı üzerinde başka birine okuyucu izinleri olan bir rol atarsanız, depolama hesabının özelliklerini görüntüleyebilirler, ancak Özellikler üzerinde herhangi bir değişiklik yapamaz veya depolama hesabı anahtarlarını görüntüleyemez.
  * Depolama hesabı katılımcısı – depolama hesabını yönetebilir, aboneliğin kaynak gruplarını ve kaynaklarını okuyabilir ve abonelik kaynak grubu dağıtımlarını oluşturabilir ve yönetebilir. Ayrıca, depolama hesabı anahtarlarına erişebilir, bu da veri düzlemine erişebileceği anlamına gelir.
  * Kullanıcı erişimi Yöneticisi – depolama hesabına Kullanıcı erişimini yönetebilirler. Örneğin, belirli bir kullanıcıya okuyucu erişimi verebilir.
  * Sanal makine katılımcısı: sanal makineleri yönetebilir, ancak bağlı oldukları depolama hesabını yönetemez. Bu rol, depolama hesabı anahtarlarını listeleyebilir, bu da bu rolü atadığınız kullanıcının veri düzlemini güncelleştirebileceği anlamına gelir.

    Bir kullanıcının bir sanal makine oluşturması için bir depolama hesabında karşılık gelen VHD dosyasını oluşturabilmeleri gerekir. Bunu yapmak için, depolama hesabı anahtarını alıp VM 'yi oluşturma API 'sine geçirebilmeleri gerekir. Bu nedenle, depolama hesabı anahtarlarını listelemek için bu izne sahip olmaları gerekir.
* Özel rolleri tanımlama yeteneği, Azure kaynaklarında gerçekleştirilebilecek kullanılabilir Eylemler listesinden bir dizi eylem oluşturmanıza olanak sağlayan bir özelliktir.
* Kullanıcılara bir rol atayabilmeniz için önce Azure Active Directory ayarlanmalıdır.
* PowerShell veya Azure CLı kullanarak ne tür erişimi hangilerinin ve ne kadar erişim izni verildiğini/hangilerinin iptal edildiğini bir rapor oluşturabilirsiniz.

#### <a name="resources"></a>Kaynaklar
* [Azure Active Directory Rol Tabanlı Erişim Denetimi](../../role-based-access-control/role-assignments-portal.md)

  Bu makalede Azure Active Directory Rol Tabanlı Access Control ve nasıl çalıştığı açıklanmaktadır.
* [RBAC: Yerleşik Roller](../../role-based-access-control/built-in-roles.md)

  Bu makalede RBAC 'de kullanılabilen tüm yerleşik roller ayrıntılı olarak anlatılmaktadır.
* [Resource Manager dağıtımını ve klasik dağıtımı anlama](../../azure-resource-manager/resource-manager-deployment-model.md)

  Bu makalede, Kaynak Yöneticisi dağıtımı ve klasik dağıtım modelleri açıklanmakta ve Kaynak Yöneticisi ve kaynak gruplarını kullanmanın avantajları açıklanmaktadır. Azure Işlem, ağ ve depolama sağlayıcılarının Kaynak Yöneticisi modeli altında nasıl çalıştığını açıklar.
* [Rol Tabanlı Erişim Denetimini REST API’si ile Yönetme](../../role-based-access-control/role-assignments-rest.md)

  Bu makalede RBAC yönetimi için REST API’sinin nasıl kullanılacağı gösterilmektedir.
* [Azure Depolama Kaynak Sağlayıcısı REST API Başvurusu](https://msdn.microsoft.com/library/azure/mt163683.aspx)

  Bu API başvurusu, depolama hesabınızı programlama yoluyla yönetmek için kullanabileceğiniz API 'Leri açıklar.

* [Ignite’tan Microsoft Azure için Rol Tabanlı Erişim Denetimi](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

  Bu bağlantı 2015 MS Ignite konferansının 9. Kanalındaki videoya aittir. Bu oturumda, Azure’daki erişim yönetimi ve raporlama özellikleri konuşulmakta ve Azure Active Directory'yi kullanarak Azure aboneliklerine erişimin güvenliğini sağlama konusundaki en iyi uygulamalar keşfedilmektedir.

### <a name="managing-your-storage-account-keys"></a>Depolama hesabı anahtarlarınızı yönetme
Depolama hesabı anahtarları, depolama hesabında depolanan 512 bitlik dizelerdir; Örneğin, blob 'lar, bir tablo içindeki varlıklar, kuyruk iletileri ve bir Azure dosya paylaşımında bulunan dosyalar gibi depolama hesabında depolanan veri nesnelerine erişmek için kullanılabilir. Depolama hesabı anahtarlarına erişimin denetlenmesi, bu depolama hesabı için veri düzlemine erişimi denetler.

Her depolama hesabının, [Azure Portal](https://portal.azure.com/) ve PowerShell cmdlet 'Lerinde "anahtar 1" ve "anahtar 2" olarak adlandırılan iki anahtarı vardır. Bunlar, [Azure Portal](https://portal.azure.com/), PowerShell, Azure CLI veya .net depolama istemci kitaplığı 'nı ya da Azure depolama hizmetleri REST API kullanarak programlı bir şekilde, ancak bunlarla sınırlı olmamak üzere çeşitli yöntemlerden birini kullanarak el ile oluşturulabilir.

Depolama hesabı anahtarlarınızı yeniden oluşturmak için çeşitli nedenler vardır.

* Güvenliği için düzenli aralıklarla yeniden oluşturabilirsiniz.
* Uygulamanızın veya ağ güvenliğinin tehlikeye düşmesi durumunda depolama hesabı anahtarlarınızı yeniden oluşturabilirsiniz.
* Anahtar yeniden oluşturma için başka bir örnek ise, anahtarlara erişimi olan takım üyelerinin ne zaman çıkış olduğunu. Paylaşılan erişim Imzaları öncelikle bu senaryoya yönelik olarak tasarlanmıştır. erişim anahtarlarını paylaşmak yerine, en çok bireyler veya uygulamalarla bir hesap düzeyi SAS bağlantı dizesi veya belirteci paylaşmanız gerekir.

#### <a name="key-regeneration-plan"></a>Anahtar yeniden oluşturma planı
Bir erişim anahtarı 'nı planlamadan gerek olmadan yeniden oluşturmanız gerekmez. Ani anahtar yeniden oluşturma, mevcut uygulamalar için bir depolama hesabına erişimi engelleyebilir ve bu da büyük kesintiye yol açabilir. Azure depolama hesapları iki anahtar sağlar, böylece bir seferde bir anahtarı yeniden oluşturmanız için.

Anahtarlarınızı yeniden oluşturmadan önce, depolama hesabına bağımlı tüm uygulamaların bir listesini ve Azure 'da kullandığınız diğer hizmetleri de kullandığınızdan emin olun. Örneğin, depolama hesabınızı kullanmak Azure Media Services kullanıyorsanız, anahtarı yeniden oluşturduktan sonra erişim anahtarlarını medya hizmetinize yeniden eşitlemeniz gerekir. Depolama Gezgini gibi bir uygulama kullanıyorsanız, bu uygulamalara da yeni anahtarlar sağlamanız gerekir. VHD dosyaları depolama hesabında depolanan sanal makinelere sahipseniz, depolama hesabı anahtarlarını yeniden oluşturma işleminden etkilenmeyecektir.

Azure portal anahtarlarınızı yeniden oluşturabilirsiniz. Anahtarlar yeniden üretildikten sonra, depolama hizmetleri arasında eşitlenmesi 10 dakika kadar sürebilir.

Hazır olduğunuzda, anahtarınızı nasıl değiştirmeniz gerektiğine ilişkin genel süreç aşağıda verilmiştir. Bu durumda, şu anda Key 1 kullanıyor olmanız ve bunun yerine her şeyi anahtar 2 kullanacak şekilde değiştirmeniz gerekir.

1. Güvenli olduğundan emin olmak için anahtar 2 ' ye yeniden oluşturun. Bunu Azure portal yapabilirsiniz.
2. Depolama anahtarının depolandığı tüm uygulamalarda, anahtar 2 ' nin yeni değerini kullanmak için depolama anahtarını değiştirin. Uygulamayı test edin ve yayımlayın.
3. Tüm uygulama ve hizmetler başarıyla çalışmaya başladıktan sonra anahtar 1 ' i yeniden oluşturun. Bu, yeni anahtarı açıkça vermiş olduğunuz herhangi bir gövdenin artık depolama hesabına erişememesini sağlar.

Şu anda Key 2 kullanıyorsanız, aynı işlemi kullanabilirsiniz, ancak anahtar adlarını ters çevirebilirsiniz.

Her bir uygulamayı yeni anahtarı kullanmak ve yayımlamak üzere değiştirerek birkaç gün boyunca geçiş yapabilirsiniz. Bunların tümü yapıldıktan sonra geri dönüp eski anahtarı yeniden oluşturmanız gerekir, böylece artık çalışmaz.

Diğer bir seçenek de, depolama hesabı anahtarını bir [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) gizli olarak koymak ve uygulamalarınızın anahtarı buradan alması gerekir. Anahtarı yeniden oluşturup Azure Key Vault güncelleştirdiğinizde uygulamaların yeniden dağıtılması gerekmez, çünkü yeni anahtarı otomatik olarak Azure Key Vault alırlar. Uygulamanın her ihtiyaç duyduğunda anahtarı okumasını sağlayabilir veya uygulama onu bellekte önbelleğe alabilir ve kullanırken başarısız olursa, anahtarı Azure Key Vault yeniden alın.

Azure Key Vault kullanmak, depolama anahtarlarınız için başka bir güvenlik düzeyi de ekler. Key Vault kullanarak, uygulama yapılandırma dosyalarında depolama anahtarları yazılmasını önlemenize olanak sağlar. Ayrıca, anahtarların bu yapılandırma dosyalarına erişimi olan herkese maruz kalmalarına engel olur.

Azure Key Vault, Anahtarlarınıza erişimi denetlemek için Azure AD kullanmanın avantajlarından de yararlanır. Anahtarları Key Vault ' den alması gereken belirli uygulamalara erişim verebilirsiniz. bu uygulamalar, anahtarları erişmesi gerekmeyen diğer uygulamalar için bunları ortaya çıkarmaz.

> [!NOTE]
> Microsoft, tüm uygulamalarınızda aynı anda yalnızca bir tane anahtar kullanılmasını önerir. Anahtar 1 ' i bazı yerlerde ve anahtar 2 ' de kullanırsanız, bazı uygulama erişimi kaybetmeksizin anahtarlarınızı döndüremezsiniz.

#### <a name="resources"></a>Kaynaklar

* [Azure Portalında depolama hesabı ayarlarını yönetme](storage-account-manage.md)
* [Azure Depolama Kaynak Sağlayıcısı REST API Başvurusu](https://msdn.microsoft.com/library/mt163683.aspx)

## <a name="network-security"></a>Ağ Güvenliği
Ağ güvenliği, bir Azure depolama hesabındaki verilere erişimi, select Networks 'tan kısıtlamanıza olanak sağlar. Belirli genel IP adresi aralıklarından istemcilere erişimi kısıtlamak için Azure Storage güvenlik duvarı 'nı kullanabilir, Azure 'da sanal ağları (VNet) veya belirli Azure kaynaklarını seçebilirsiniz. Ayrıca, erişmesi gereken VNet 'te depolama hesabınız için özel bir uç nokta oluşturma ve genel uç nokta aracılığıyla tüm erişimi engelleme seçeneğiniz de vardır.

Depolama hesabınız için ağ erişim kurallarını, Azure portal [güvenlik duvarları ve sanal ağlar](storage-network-security.md) sekmesinden yapılandırabilirsiniz. Depolama güvenlik duvarını kullanarak, genel İnternet trafiği için erişimi reddedebilir ve yapılandırılmış ağ kurallarına göre istemcileri Seç erişimine izin verebilirsiniz.

Özel [bağlantıları](../../private-link/private-link-overview.md)kullanarak bir VNET 'ten bir depolama hesabına özel olarak ve güvenli bir şekilde bağlanmak Için [Özel uç noktaları](../../private-link/private-endpoint-overview.md) da kullanabilirsiniz.

Depolama güvenlik duvarı kuralları yalnızca depolama hesabı için genel uç nokta için geçerlidir. Bir depolama hesabı için özel bir uç nokta barındıran alt ağ, bu özel uç noktanın oluşturulmasını onayladığınızda hesaba örtülü erişimi alır.

> [!NOTE]
> Depolama güvenlik duvarı kuralları, Azure portal ve Azure depolama yönetim API 'SI aracılığıyla gerçekleştirilen depolama yönetimi işlemlerine uygulanamaz.

### <a name="access-rules-for-public-ip-address-ranges"></a>Genel IP adresi aralıkları için erişim kuralları
Azure depolama güvenlik duvarı, belirli genel IP adresi aralıklarından bir depolama hesabına erişimi kısıtlamak için kullanılabilir. IP adresi kurallarını, sabit bir genel IP uç noktası ile iletişim kuran belirli internet tabanlı hizmetlere erişimi kısıtlamak veya şirket içi ağları seçmek için kullanabilirsiniz.

### <a name="access-rules-for-azure-virtual-networks"></a>Azure sanal ağları için erişim kuralları
Depolama hesapları varsayılan olarak herhangi bir ağdaki istemcilerden gelen bağlantıları kabul eder. Depolama hesabındaki verilere yönelik istemci erişimini, depolama güvenlik duvarını kullanarak seçili ağlarla sınırlayabilirsiniz. [Hizmet uç noktaları](../../virtual-network/virtual-network-service-endpoints-overview.md) bir Azure sanal ağından depolama hesabına trafik yönlendirmeyi etkinleştirir. 

### <a name="granting-access-to-specific-trusted-resource-instances"></a>Belirli güvenilir kaynak örneklerine erişim verme
[Azure güvenilir Hizmetleri 'nin bir alt kümesinin](storage-network-security.md#trusted-microsoft-services) , hizmet kaynak türüne veya bir kaynak örneğine göre güçlü kimlik doğrulaması ile güvenlik duvarı aracılığıyla depolama hesabına erişmesine izin verebilirsiniz.

Depolama güvenlik duvarı aracılığıyla kaynak örneği tabanlı erişimi destekleyen hizmetler için, depolama hesabındaki verilere yalnızca seçili örnek erişebilir. Bu durumda, hizmet, sistem tarafından atanan [yönetilen kimlikleri](../../active-directory/managed-identities-azure-resources/overview.md)kullanarak kaynak örneği kimlik doğrulamasını desteklemelidir.

### <a name="using-private-endpoints-for-securing-connections"></a>Bağlantıları güvenli hale getirmek için özel uç noktaları kullanma
Azure depolama, Azure sanal ağından depolama hesabına güvenli erişim sağlayan özel uç noktaları destekler. Özel uç noktalar, sanal ağınızın adres alanından depolama hizmetine özel bir IP adresi atar. Özel uç noktalar kullanılırken, depolama bağlantı dizesi depolama hesabı için hedeflenen trafiği özel IP adresine yönlendirir. Özel uç nokta ve depolama hesabı arasındaki bağlantı özel bir bağlantı kullanır. Özel uç noktaları kullanarak sanal ağınızdan veri alımını engelleyebilirsiniz.

VPN veya [ExpressRoute](../../expressroute/expressroute-locations.md) özel eşlemesi ve diğer eşlenmiş sanal ağlar üzerinde bağlanmış şirket içi ağlar, depolama hesabına özel uç nokta üzerinden da erişebilir. Depolama hesaplarınız için özel uç nokta, her bölgedeki bir sanal ağda oluşturulabilir ve güvenli küresel bir erişim olanağı tanır. Diğer [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) kiracılarda, depolama hesapları için özel uç noktalar da oluşturabilirsiniz.

## <a name="authorization"></a>Yetkilendirme
Veri düzlemi güvenliği, Azure depolama 'da depolanan veri nesnelerinin güvenliğini sağlamak için kullanılan yöntemleri belirtir: Bloblar, kuyruklar, tablolar ve dosyalar. Verileri aktarma sırasında verileri ve güvenliği şifrelemek için yöntemler görüldü, ancak nesnelere erişimi denetleme hakkında nasıl devam edersiniz?

Azure Storage 'da veri nesnelerine erişimi yetkilendirmek için kullanabileceğiniz üç seçenek vardır:

- Kapsayıcılara ve kuyruklara erişim yetkisi vermek için Azure AD kullanma. Azure AD, kodunuza gizli dizileri depolama gereksinimini kaldırma dahil olmak üzere, yetkilendirme için diğer yaklaşımlardan daha fazla avantaj sağlar. Daha fazla bilgi için bkz. [Azure Active Directory kullanarak Azure depolama 'ya erişim kimlik doğrulaması](storage-auth-aad.md). 
- Paylaşılan anahtar aracılığıyla erişim yetkisi vermek için depolama hesabı anahtarlarınızı kullanma. Paylaşılan anahtar aracılığıyla yetkilendirmek, depolama hesabı anahtarlarınızın uygulamanızda depolanmasını gerektirir, bu nedenle Microsoft Azure AD 'yi mümkün olduğunca kullanmanızı önerir.
- Belirli bir süre boyunca belirli veri nesnelerine denetimli izinler vermek için paylaşılan erişim Imzalarını kullanma.

Bunlara ek olarak, BLOB depolama için Blobları uygun şekilde tutan kapsayıcının erişim düzeyini ayarlayarak bloblarınıza genel erişime izin verebilirsiniz. Blob veya kapsayıcıya bir kapsayıcı için erişim ayarlarsanız, bu kapsayıcıdaki Bloblar için genel okuma erişimine izin verir. Bu, söz konusu kapsayıcıdaki bir blobu işaret eden bir URL 'ye sahip olan herkes, paylaşılan erişim Imzası kullanmadan veya depolama hesabı anahtarlarına sahip olmayan bir tarayıcıda açabilme anlamına gelir.

### <a name="storage-account-keys"></a>Depolama Hesabı Anahtarları
Depolama hesabı anahtarları, depolama hesabı adı ile birlikte Azure tarafından oluşturulan 512 bitlik dizelerdir ve depolama hesabında depolanan veri nesnelerine erişim için kullanılabilir.

Örneğin, Blobları okuyabilir, kuyruklara yazabilir, tablolar oluşturabilir ve dosyaları değiştirebilirsiniz. Bu eylemlerin birçoğu Azure portal aracılığıyla veya birçok Depolama Gezgini uygulamadan biri kullanılarak gerçekleştirilebilir. Ayrıca, bu işlemleri gerçekleştirmek için REST API veya depolama Istemci kitaplıklarından birini kullanmak üzere kod yazabilirsiniz.

[Yönetim düzlemi güvenlik](#management-plane-security)bölümünde açıklandığı gibi, Azure aboneliğine tam erişim Izni vererek klasik bir depolama hesabı için depolama anahtarlarına erişim sağlayabilirsiniz. Azure Resource Manager modelini kullanarak bir depolama hesabı için depolama anahtarlarına erişim, rol tabanlı Access Control (RBAC) aracılığıyla denetlenebilir.

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Paylaşılan erişim Imzalarını ve depolanan erişim Ilkelerini kullanarak hesabınızdaki nesnelere erişim yetkisi verme
Paylaşılan erişim Imzası, depolama nesnelerine erişim atamanıza ve izinler ve erişim tarih/saat aralığı gibi kısıtlamaları belirtmenize olanak tanıyan bir URI 'ye iliştirilebilecek bir güvenlik belirteci içeren bir dizedir.

Bloblar, kapsayıcılar, kuyruk iletileri, dosyalar ve tablolar için erişim izni verebilirsiniz. Tablolar sayesinde, aslında kullanıcının erişmesini istediğiniz bölüm ve satır anahtar aralıklarını belirterek tablodaki bir dizi varlığa erişme izni verebilirsiniz. Örneğin, coğrafi durum bölüm anahtarıyla depolanan verileriniz varsa, birine yalnızca California için verilere erişim izni verebilirsiniz.

Başka bir örnekte, bir Web uygulamasına bir kuyruğa girdi yazmasına ve bir çalışan rolü uygulamasına bir SAS belirteci vererek kuyruktan ileti almasını ve bunları işlemesini sağlayan bir SAS belirteci verebilirsiniz. Ya da bir müşteriye, BLOB depolama alanındaki bir kapsayıcıya resim yüklemek ve bu resimleri okumak için bir Web uygulamasına izin vermek üzere kullanabilecekleri bir SAS belirteci verebilirsiniz. Her iki durumda da bir sorun ayrımı vardır: her bir uygulamaya, yalnızca görevlerini gerçekleştirmek için ihtiyaç duydukları erişim verilebilir. Bu, paylaşılan erişim Imzaları kullanılarak yapılabilir.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Paylaşılan erişim Imzalarını neden kullanmak istiyorsunuz?
Neden çok daha kolay olan depolama hesabı anahtarınızı vermek yerine SAS kullanmak istersiniz? Depolama hesabı anahtarınızı verme, depolama alanı anahtarlarınızın anahtarlarını paylaşma gibidir. Tüm erişim verir. Birisi anahtarlarınızı kullanabilir ve tüm müzik kütüphanesini depolama hesabınıza yükleyebilir. Ayrıca, dosyalarınızı virüs bulaşmış sürümlerle değiştirebilir veya verilerinizi çalabilir. Depolama hesabınıza sınırsız erişim verilmesi, güvenle alınmamalıdır.

Paylaşılan erişim Imzaları ile bir istemciye yalnızca sınırlı bir süre için gerekli izinleri verebilirsiniz. Örneğin, hesabınız hesabınıza bir blob yüklürsa, blob 'u karşıya yüklemek için (örneğin, Blobun boyutuna bağlı olarak) yazma erişimi verebilirsiniz. Fikrinizi değiştirirseniz, bu erişimi iptal edebilirsiniz.

Ayrıca, SAS kullanılarak yapılan isteklerin belirli bir IP adresi veya Azure dış IP adresi aralığıyla sınırlandırılacağını belirtebilirsiniz. Ayrıca, isteklerin belirli bir protokol (HTTPS veya HTTP/HTTPS) kullanılarak yapılmasını isteyebilirsiniz. Bu, yalnızca HTTPS trafiğine izin vermek istiyorsanız, gerekli protokolü yalnızca HTTPS olarak ayarlayabilirsiniz ve HTTP trafiği engellenir.

#### <a name="definition-of-a-shared-access-signature"></a>Paylaşılan erişim Imzasının tanımı
Paylaşılan erişim Imzası, kaynağı işaret eden URL 'ye eklenen bir sorgu parametreleri kümesidir

Bu, izin verilen erişim ve erişim izni verilen sürenin uzunluğu hakkında bilgi sağlar. İşte bir örnek; Bu URI, beş dakikalık bir bloba okuma erişimi sağlar. SAS sorgu parametrelerinin, iki nokta için% 3A gibi URL kodlamalı olması gerektiğini unutmayın (:) ya da bir boşluk için %20.

```
http://mystorage.blob.core.windows.net/mycontainer/myblob.txt (URL to the blob)
?sv=2015-04-05 (storage service version)
&st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
&se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
&sr=b (resource is a blob)
&sp=r (read access)
&sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
&spr=https (only allow HTTPS requests)
&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)
```

#### <a name="how-the-shared-access-signature-is-authorized-by-the-azure-storage-service"></a>Paylaşılan erişim Imzasının Azure depolama hizmeti tarafından nasıl yetkilendirildiği
Depolama hizmeti isteği aldığında, giriş sorgusu parametrelerini alır ve çağıran programla aynı yöntemi kullanarak bir imza oluşturur. Daha sonra iki imzayı karşılaştırır. Bu durumda, depolama hizmeti, geçerli olduğundan emin olmak için depolama hizmeti sürümünü denetleyebilir, geçerli tarih ve saatin belirtilen pencerede olduğundan emin olun, istenen erişimin, yapılan isteğe karşılık geldiğini doğrulayın.

Örneğin, yukarıdaki URL 'imizde, URL bir blob yerine bir dosyayı işaret ettiğinden, bu istek başarısız olur çünkü paylaşılan erişim Imzasının bir blob için olduğunu belirtir. Çağrılan REST komutu bir blobu güncelleştiriyorsa, paylaşılan erişim Imzası yalnızca okuma erişimine izin verildiğini belirttiğinden başarısız olur.

#### <a name="types-of-shared-access-signatures"></a>Paylaşılan erişim Imzası türleri
* Hizmet düzeyi SAS, bir depolama hesabındaki belirli kaynaklara erişmek için kullanılabilir. Buna örnek olarak, bir kapsayıcıdaki Blobların listesini alma, blob indirme, bir tablodaki varlığı güncelleştirme, bir kuyruğa ileti ekleme veya dosya paylaşımının dosya yükleme.
* Hesap düzeyi SAS, hizmet düzeyi SAS 'nin kullanılabileceği her şeye erişmek için kullanılabilir. Ayrıca, kapsayıcı, tablo, kuyruk ve dosya paylaşımları oluşturma gibi hizmet düzeyi SAS ile izin verilmeyen kaynaklara seçenek verebilir. Aynı zamanda birden çok hizmete erişimi de belirtebilirsiniz. Örneğin, bir kişiye Depolama hesabınızdaki her iki blob ve dosya erişimi verebilirsiniz.

#### <a name="creating-a-sas-uri"></a>SAS URI 'SI oluşturma
1. Her seferinde sorgu parametrelerinin tümünü tanımlayarak isteğe bağlı bir URI oluşturabilirsiniz.

   Bu yaklaşım esnektir, ancak her seferinde benzer bir mantıksal parametre kümesine sahipseniz, depolanan erişim Ilkesini kullanmak daha iyi bir fikirdir.
2. Tüm kapsayıcı, dosya paylaşma, tablo veya kuyruk için depolanan bir erişim Ilkesi oluşturabilirsiniz. Daha sonra bunu, oluşturduğunuz SAS URI 'Leri için temel olarak kullanabilirsiniz. Depolanan erişim Ilkelerine dayalı izinler kolayca iptal edilebilir. Her kapsayıcı, kuyruk, tablo veya dosya paylaşımında tanımlanmış en fazla beş ilke olabilir.

   Örneğin, belirli bir kapsayıcıdaki Blobları okuduğunuzdan çok sayıda kişi varsa, "okuma erişimi ver" i ve her seferinde aynı olacak diğer ayarları belirten bir saklı erişim Ilkesi oluşturabilirsiniz. Ardından, depolanan erişim Ilkesinin ayarlarını kullanarak bir SAS URI 'SI oluşturabilir ve sona erme tarihi/saati belirtebilirsiniz. Bunun avantajı, her seferinde sorgu parametrelerinin tümünü belirtmeniz gerekmez.

#### <a name="revocation"></a>Sayılabilir
SAS güvenliğinin aşıldığını veya kurumsal güvenlik ya da yasal uyumluluk gereksinimleri nedeniyle değiştirmek istediğinizi varsayalım. Bu SAS kullanarak bir kaynağa erişimi nasıl iptal edersiniz? SAS URI 'sini nasıl oluşturduğunuza bağlıdır.

Geçici URI 'Ler kullanıyorsanız, üç seçeneğiniz vardır. Kısa süre sonu ilkeleriyle SAS belirteçleri verebilir ve SAS süresinin dolmasını bekleyebilirsiniz. Kaynağı yeniden adlandırabilir veya silebilirsiniz (belirtecin tek bir nesne kapsamında olduğu varsayılarak). Depolama hesabı anahtarlarını değiştirebilirsiniz. Bu son seçenek, bu depolama hesabını kaç hizmetin kullandığını bağlı olarak önemli bir etkiye sahip olabilir ve muhtemelen bir planlama yapmadan yapmak istediğiniz bir şey değildir.

Depolanan erişim Ilkesinden türetilmiş bir SAS kullanıyorsanız, depolanan erişim Ilkesini iptal ederek erişimi kaldırabilirsiniz. bu işlemi, zaten süresi sona ermiş olacak şekilde değiştirebilirsiniz veya tamamen kaldırabilirsiniz. Bu, hemen etkili olur ve bu saklı erişim Ilkesi kullanılarak oluşturulan her sa 'yı geçersiz kılar. Saklı erişim Ilkesini güncelleştirme veya kaldırma, bu belirli kapsayıcıya, dosya paylaşımıyla, tabloya veya kuyruğa erişen kişileri SAS aracılığıyla etkileyebilir, ancak istemciler yeni bir SAS isteyecek şekilde yazılmışsa, bu durum sorunsuz bir şekilde çalışır.

Depolanan bir erişim Ilkesinden türetilmiş bir SAS kullanılması, bu SAS 'ı hemen iptal etmenizi sağlar. Bu, mümkün olduğunda her zaman depolanan erişim Ilkelerini kullanmak için önerilen en iyi uygulamadır.

#### <a name="resources"></a>Kaynaklar
Paylaşılan erişim Imzalarını ve depolanan erişim Ilkelerini kullanma hakkında daha ayrıntılı bilgi için örneklerle birlikte aşağıdaki makalelere bakın:

* Bunlar başvuru makaleleridir.

  * [Hizmet SAS](https://msdn.microsoft.com/library/dn140256.aspx)

    Bu makalede, Bloblar, kuyruk iletileri, tablo aralıkları ve dosyalarla hizmet düzeyi SAS kullanma örnekleri verilmektedir.
  * [Hizmet SAS oluşturma](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Hesap SAS oluşturma](https://msdn.microsoft.com/library/mt584140.aspx)

* Bu, paylaşılan erişim Imzaları ve depolanan erişim Ilkeleri oluşturmak için .NET istemci kitaplığını kullanmaya yönelik bir öğreticidir.
  * [Paylaşılan erişim Imzalarını kullanma (SAS)](../storage-dotnet-shared-access-signature-part-1.md)

    Bu makale SAS modeli hakkında bir açıklama, paylaşılan erişim Imzaları örnekleri ve SAS 'ın en iyi uygulama kullanımı için öneriler içerir. Ayrıca, verilen iznin iptali de ele alınmıştır.

* Kimlik Doğrulaması

  * [Azure depolama hizmetleri için kimlik doğrulaması](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* Paylaşılan erişim Imzaları Başlangıç Öğreticisi

  * [SAS Başlangıç Öğreticisi](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>Aktarım sırasında şifreleme
### <a name="transport-level-encryption--using-https"></a>Aktarım düzeyi şifreleme – HTTPS kullanarak
Azure depolama verilerinizin güvenliğinin, istemci ile Azure depolama arasındaki verileri şifrelediğinden emin olmak için gerçekleştirmeniz gereken başka bir adım. İlk öneri, genel Internet üzerinden güvenli iletişim sağlayan [https](https://en.wikipedia.org/wiki/HTTPS) protokolünü her zaman kullanmaktır.

Güvenli bir iletişim kanalına sahip olmak için REST API 'Lerini çağırırken veya depolama alanındaki nesnelere erişirken her zaman HTTPS kullanmanız gerekir. Ayrıca, Azure Storage nesnelerine erişim yetkisi vermek için kullanılabilen **paylaşılan erişim imzaları**, paylaşılan erişim imzaları KULLANıLıRKEN yalnızca HTTPS protokolünün kullanılabileceğini belirtmek için bir seçenek içerir ve bu da, diğer GÖVDENIN SAS belirteçleriyle bağlantı göndermesini sağlar , uygun protokolü kullanır.

Depolama hesabı için [gerekli olan güvenli aktarımı](../storage-require-secure-transfer.md) etkinleştirerek depolama hesaplarındaki nesnelere erışmek Için REST API 'LERINI çağırırken https kullanımını zorunlu kılabilirsiniz. HTTP kullanan bağlantılar, bu etkinleştirildikten sonra reddedilir.

### <a name="using-encryption-during-transit-with-azure-file-shares"></a>Azure dosya paylaşımları ile aktarım sırasında şifrelemeyi kullanma
[Azure dosyaları](../files/storage-files-introduction.md) , Dosya REST API KULLANıLıRKEN, SMB 3,0 ve https ile şifrelemeyi destekler. Azure dosya paylaşımının şirket içi veya başka bir Azure bölgesinde bulunduğu Azure bölgesinin dışına bağlanırken, şifreleme ile SMB 3,0 her zaman gereklidir. SMB 2,1 şifrelemeyi desteklemez, bu nedenle varsayılan bağlantılara yalnızca Azure 'daki aynı bölgede izin verilir, ancak depolama hesabı için [Güvenli aktarım gerektirerek](../storage-require-secure-transfer.md) ŞIFRELEME ile SMB 3,0 de zorlanabilir.

Şifreleme ile SMB 3,0, Windows 7 ve Windows Server 2008 R2 dışındaki [tüm desteklenen Windows ve Windows Server işletim sistemlerinde](../files/storage-how-to-use-files-windows.md) kullanılabilir ve yalnızca SMB 2,1 desteklenir. SMB 3,0, [MacOS](../files/storage-how-to-use-files-mac.md) 'ta ve linux çekirdek 4,11 ve üzeri kullanılarak [Linux](../files/storage-how-to-use-files-linux.md) dağıtımları üzerinde de desteklenir. SMB 3,0 için şifreleme desteği, Linux çekirdeğinin birkaç Linux dağıtımı tarafından daha eski sürümlerine de eklenmiştir, [SMB istemci gereksinimlerini anlama](../files/storage-how-to-use-files-linux.md#smb-client-reqs)bölümüne bakın.

### <a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>Depolamaya göndereceğiniz verileri güvenli hale getirmek için Istemci tarafı şifrelemeyi kullanma
Bir istemci uygulaması ve depolama alanı arasında aktarılırken verilerinizin güvende olduğundan emin olmanıza yardımcı olan başka bir seçenek de Istemci tarafı şifrelemedir. Veriler, Azure depolama alanına aktarılmadan önce şifrelenir. Azure depolama 'dan verileri alırken, verilerin şifresi istemci tarafında alındıktan sonra çözülür. Veriler hat genelinde şifrelense de, verilerin bütünlüğünü etkileyen ağ hatalarını azaltmaya yardımcı olan veri bütünlüğü denetimleri içerdiğinden HTTPS kullanmanızı öneririz.

Veriler şifreli biçimde depolandığından, istemci tarafı şifreleme, bekleyen verileri şifrelemek için de bir yöntemdir. Bu konuda, [bekleyen şifreleme](#encryption-at-rest)bölümündeki bölümünde daha ayrıntılı konuşacağız.

## <a name="encryption-at-rest"></a>Bekleyen şifreleme
Bekleyen şifreleme sağlayan üç Azure özelliği vardır. IaaS sanal makinelerinde işletim sistemi ve veri disklerini şifrelemek için Azure disk şifrelemesi kullanılır. İstemci tarafı şifreleme ve SSE her ikisi de Azure Storage 'daki verileri şifrelemek için kullanılır. 

Istemci tarafı şifrelemeyi, yoldaki verileri şifrelemek için (aynı zamanda şifreli biçimde depolamada da depolanır) kullandığınızda, aktarım sırasında HTTPS kullanmayı tercih edebilir ve verilerin depolandığı zaman otomatik olarak şifrelenmesi için bir yol kullanabilirsiniz. Bunu yapmak için iki yol vardır--Azure disk şifrelemesi ve SSE. Bunlardan biri, VM 'Ler tarafından kullanılan işletim sistemi ve veri disklerindeki verileri doğrudan şifrelemek için, diğeri ise Azure Blob depolama alanına yazılan verileri şifrelemek için kullanılır.

### <a name="storage-service-encryption-sse"></a>Depolama Hizmeti Şifrelemesi (SSE)

SSE tüm depolama hesapları için etkinleştirildi ve devre dışı bırakılamaz. SSE, verileri Azure depolama 'ya yazarken otomatik olarak şifreler. Azure depolama 'dan verileri okurken, döndürülmeden önce Azure Storage tarafından şifresi çözülür. SSE, kodu değiştirmek veya herhangi bir uygulamaya kod eklemek zorunda kalmadan verilerinizin güvenliğini sağlamanıza olanak sağlar.

Microsoft tarafından yönetilen anahtarları ya da kendi özel anahtarlarınızı kullanabilirsiniz. Microsoft, iç Microsoft ilkesi tarafından tanımlandığı şekilde, yönetilen anahtarlar oluşturur ve bunların güvenli depolama alanını ve normal yönlerini işler. Özel anahtarları kullanma hakkında daha fazla bilgi için, [Depolama Hizmeti Şifrelemesi Azure Key Vault müşteri tarafından yönetilen anahtarları kullanma](storage-service-encryption-customer-managed-keys.md)konusuna bakın.

SSE tüm performans katmanları (Standart ve Premium), tüm dağıtım modelleri (Azure Resource Manager ve Klasik) ve tüm Azure Depolama hizmetlerinde (Blob, Kuyruk, Tablo ve Dosya) verileri otomatik olarak şifreler. 

### <a name="client-side-encryption"></a>İstemci tarafı şifreleme
İletim sırasında verilerin şifrelenmesini ele alırken istemci tarafı şifrelemesini belirttik. Bu özellik, verileri Azure depolama alanına yazılacak şekilde göndermeden önce bir istemci uygulamasındaki verilerinizi, Azure depolama alanından aldıktan sonra programlı bir şekilde şifrelemenizi sağlar.

Bu, aktarım sırasında şifreleme sağlar, ancak bekleyen şifreleme özelliğini de sağlar. Veriler aktarım sırasında şifrelense de, verilerin bütünlüğünü etkileyen ağ hatalarının azaltılmasına yardımcı olan yerleşik veri bütünlüğü denetimlerinden yararlanmak için HTTPS kullanılmasını öneririz.

Bunu nerede kullanacağınızı gösteren bir örnek, Blobları depolayan ve BLOB 'ları alan bir Web uygulamanız varsa ve uygulama ve verilerin mümkün olduğunca güvenli olmasını istiyorsanız. Bu durumda, istemci tarafı şifrelemeyi kullanırsınız. İstemci ile Azure Blob hizmeti arasındaki trafik, şifrelenmiş kaynağı içerir ve hiçbir kimse iletimde verileri yorumlayıp özel bloblarınıza edilmeyen.

İstemci tarafı şifreleme, Java ve .NET depolama istemci kitaplıklarında yerleşik olarak bulunur ve bu da Azure Key Vault API 'Lerini kullanarak, uygulamanızı kolay hale getirir. Verilerin şifrelenmesi ve şifresinin çözülmesi, zarf tekniğini kullanır ve her bir depolama nesnesindeki şifreleme tarafından kullanılan meta verileri depolar. Örneğin, Bloblar için blob meta verilerinde depolar, ancak kuyruklar için her bir kuyruk iletisine eklenir.

Şifrelemenin kendisi için kendi şifreleme anahtarlarınızı oluşturabilir ve yönetebilirsiniz. Ayrıca, Azure Storage Istemci kitaplığı tarafından oluşturulan anahtarları kullanabilir veya Azure Key Vault anahtarları oluşturabilir. Şifreleme anahtarlarınızı şirket içi anahtar depoağınızda saklayabilir veya bir Azure Key Vault kaydedebilirsiniz. Azure Key Vault, Azure Active Directory kullanarak belirli kullanıcılara Azure Key Vault gizli dizi erişimine izin vermenizi sağlar. Bu, yalnızca herhangi bir gövdenin Azure Key Vault okuyamayacağı ve istemci tarafı şifreleme için kullandığınız anahtarları alabileceği anlamına gelir.

#### <a name="resources"></a>Kaynaklar
* [Azure Key Vault kullanarak Microsoft Azure Depolama Blobları şifreleme ve şifre çözme](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)

  Bu makalede, KEK oluşturma ve PowerShell kullanarak kasada depolama gibi Azure Key Vault ile istemci tarafı şifrelemenin nasıl kullanılacağı gösterilmektedir.
* [Microsoft Azure Depolama için istemci tarafı şifreleme ve Azure Key Vault](../storage-client-side-encryption.md)

  Bu makale, istemci tarafı şifreleme hakkında bir açıklama sağlar ve depolama istemci kitaplığını kullanarak dört depolama hizmetinden kaynakları şifrelemek ve şifrelerini çözmek için örnekler sağlar. Ayrıca Azure Key Vault de konuşuyor.

### <a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Sanal makineleriniz tarafından kullanılan diskleri şifrelemek için Azure disk şifrelemesini kullanma
Azure disk şifrelemesi, bir IaaS sanal makinesi tarafından kullanılan işletim sistemi disklerini ve veri disklerini şifrelemenizi sağlar. Windows için Sürücüler, sektör standardı BitLocker şifreleme teknolojisi kullanılarak şifrelenir. Linux için diskler DM-Crypt teknolojisi kullanılarak şifrelenir. Bu, disk şifreleme anahtarlarını denetlemenize ve yönetmenize olanak tanımak için Azure Key Vault ile tümleşiktir.

Çözüm, Microsoft Azure ' de etkinleştirildiklerinde IaaS VM 'Leri için aşağıdaki senaryoları destekler:

* Azure Key Vault ile tümleştirme
* Standart katman VM 'Leri: [A, D, DS, G, GS, vb. seri IaaS VM 'leri](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Windows ve Linux IaaS VM 'lerinde şifrelemeyi etkinleştirme
* Windows IaaS VM 'Leri için işletim sistemi ve veri sürücülerinde şifrelemeyi devre dışı bırakma
* Linux IaaS VM 'Leri için veri sürücülerinde şifrelemeyi devre dışı bırakma
* Windows istemci işletim sistemi çalıştıran IaaS sanal makinelerinde şifrelemeyi etkinleştirme
* Bağlama yollarındaki birimlerde şifrelemeyi etkinleştirme
* Mdaddm kullanılarak disk şeridi (RAID) ile yapılandırılmış Linux VM 'lerinde şifrelemeyi etkinleştirme
* Veri diskleri için LVM 'yi kullanarak Linux sanal makinelerinde şifrelemeyi etkinleştirme
* Depolama alanları kullanılarak yapılandırılan Windows VM 'lerinde şifrelemeyi etkinleştirme
* Tüm Azure ortak bölgeleri desteklenir

Çözüm, sürümde aşağıdaki senaryoları, özellikleri ve teknolojiyi desteklemez:

* Temel katman IaaS VM 'Leri
* Linux IaaS VM 'Leri için bir işletim sistemi sürücüsünde şifrelemeyi devre dışı bırakma
* Klasik VM oluşturma yöntemi kullanılarak oluşturulan IaaS VM 'Leri
* Şirket içi anahtar yönetim hizmetiniz ile tümleştirme
* Azure dosyaları (paylaşılan dosya sistemi), ağ dosya sistemi (NFS), dinamik birimler ve yazılım tabanlı RAID sistemleriyle yapılandırılmış Windows VM 'Leri


> [!NOTE]
> Linux işletim sistemi disk şifrelemesi Şu anda şu Linux dağıtımları üzerinde destekleniyor: RHEL 7,2, CentOS 7.2 n ve Ubuntu 16,04.
>
>

Bu özellik, sanal makine disklerinizdeki tüm verilerin Azure Storage 'da bekleyen olarak şifrelenmesini sağlar.

#### <a name="resources"></a>Kaynaklar
* [Windows ve Linux IaaS VM 'Leri için Azure disk şifrelemesi](../../security/fundamentals/encryption-overview.md)

### <a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Azure disk şifrelemesi, SSE ve Istemci tarafı şifreleme karşılaştırması

#### <a name="iaas-vms-and-their-vhd-files"></a>IaaS sanal makineleri ve bunların VHD dosyaları

IaaS VM 'Leri tarafından kullanılan veri diskleri için Azure disk şifrelemesi önerilir. Azure Marketi 'nden bir görüntü kullanarak yönetilmeyen diskler içeren bir sanal makine oluşturursanız, Azure, Azure depolama 'daki depolama hesabınıza görüntünün [basit bir kopyasını](https://en.wikipedia.org/wiki/Object_copying) uygular ve bu, SSE etkin olsa bile şifrelenmez. VM 'yi oluşturup görüntüyü güncelleştirmeye başladıktan sonra, SSE verileri şifrelemeye başlayacaktır. Bu nedenle, tam olarak şifrelenmesini istiyorsanız Azure Marketi 'ndeki görüntülerden oluşturulan yönetilmeyen disklere sahip VM 'lerde Azure disk şifrelemesi 'ni kullanmak en iyisidir. Yönetilen diskler içeren bir sanal makine oluşturursanız, SSE, platform tarafından yönetilen anahtarları kullanarak tüm verileri varsayılan olarak şifreler. 

Şirket içinden Azure 'a önceden şifrelenen bir VM 'yi yüklerseniz, şifreleme anahtarlarını Azure Key Vault karşıya yükleyebilir ve şirket içinde kullandığınız VM 'nin şifrelemesini kullanmaya devam edebilirsiniz. Azure disk şifrelemesi bu senaryoyu işleyecek şekilde etkinleştirildi.

Şirket içinde şifrelenmemiş VHD 'SI varsa, bunu özel bir görüntü olarak galeriye yükleyebilir ve bundan bir VM sağlayabilirsiniz. Bunu Kaynak Yöneticisi şablonları kullanarak yaparsanız, VM 'yi önyüklediğinde Azure disk şifrelemesi 'ni açmayı isteyebilirsiniz.

Bir veri diski ekleyip VM 'ye bağladığınızda, bu veri diskinde Azure disk şifrelemesi 'ni açabilirsiniz. Önce bu veri diskini yerel olarak şifreler ve ardından klasik dağıtım modeli katmanı, depolama içeriğinin şifrelenmesi için depolamaya karşı bir geç yazma işlemi kullanacaktır.

#### <a name="client-side-encryption"></a>İstemci Tarafında Şifreleme
İstemci tarafı şifreleme, verileri aktarma işleminden önce şifreleyen için en güvenli yöntemdir.  Ancak, depolama kullanarak uygulamalarınıza kod eklemenizi gerektirir. Bu işlem yapmak istemeyebilirsiniz. Bu durumlarda, iletim sırasında verilerinizin güvenliğini sağlamak için HTTPS kullanabilirsiniz. Veriler Azure Storage 'a ulaştığında, SSE tarafından şifrelenir.

İstemci tarafı şifreleme ile tablo varlıklarını, kuyruk iletilerini ve Blobları şifreleyebilirsiniz. 

İstemci tarafı şifreleme, tamamen uygulama tarafından yönetilir. Bu en güvenli yaklaşımdır, ancak uygulamanızda programlı değişiklikler yapmanız ve anahtar yönetim süreçlerini yerinde yerleştirmeniz gerekir. Bu, geçiş sırasında ek güvenlik istediğinizde ve depolanan verilerin şifrelenmesini istediğinizde kullanırsınız.

İstemci tarafı şifreleme, istemci üzerinde daha fazla yüke ve özellikle büyük miktarda veri şifrelerken ve aktarıyorsanız, ölçeklenebilirlik planlarınızda bunu hesaba geçirmeniz gerekir.

#### <a name="storage-service-encryption-sse"></a>Depolama Hizmeti Şifrelemesi (SSE)

SSE, Azure depolama tarafından yönetiliyor. SSE, yoldaki verilerin güvenliğini sağlamaz, ancak verileri Azure depolama 'ya yazıldığı şekilde şifreler. SSE, Azure Depolama performansını etkilemez.

SSE (blok Blobları, ekleme Blobları, sayfa Blobları, tablo verileri, kuyruk verileri ve dosyalar) kullanarak depolama hesabı türlerini şifreleyebilirsiniz.

Yeni sanal makineler oluşturmak için temel olarak kullandığınız bir arşiv veya VHD dosyaları kitaplığınız varsa, yeni bir depolama hesabı oluşturabilir ve sonra bu hesaba VHD dosyalarını karşıya yükleyebilirsiniz. Bu VHD dosyaları, Azure depolama tarafından şifrelenir.

Bir VM 'deki diskler için Azure disk şifrelemesi etkinse, yeni yazılmış tüm veriler, SSE ve Azure Disk Şifrelemesi tarafından şifrelenir.

## <a name="storage-analytics"></a>Depolama Analizi
### <a name="using-storage-analytics-to-monitor-authorization-type"></a>Yetkilendirme türünü izlemek için Depolama Analizi kullanma
Her depolama hesabı için Azure Depolama Analizi, günlüğe kaydetme ve ölçüm verilerini depolama işlemleri gerçekleştirmesini sağlayabilirsiniz. Bu, bir depolama hesabının performans ölçümlerini denetlemek istediğinizde kullanabileceğiniz harika bir araçtır veya performans sorunlarınız olduğundan bir depolama hesabında sorun gidermeniz gerekir.

Depolama Analizi günlüklerinde görebileceğiniz başka bir veri parçası, depolama erişimi olduğunda birisi tarafından kullanılan kimlik doğrulama yöntemidir. Örneğin, BLOB depolama ile, paylaşılan erişim Imzası veya depolama hesabı anahtarları kullandıklarından ya da erişim varsa blob 'un genel olup olmadığını görebilirsiniz.

Bu, depolama erişimini sıkı bir şekilde koruyarak yararlı olabilir. Örneğin, BLOB depolama alanında tüm kapsayıcıları özel olarak ayarlayabilir ve uygulamalarınızın tamamında bir SAS hizmetinin kullanımını uygulayabilirsiniz. Daha sonra, bloblarınızın depolama hesabı anahtarları kullanılarak erişilip erişilmeyeceğini, güvenliğin ihlal olabileceğini veya Blobların genel olduğunu, ancak bunların olmaması gerektiğini görmek için günlükleri düzenli olarak kontrol edebilirsiniz.

#### <a name="what-do-the-logs-look-like"></a>Günlükler nasıl görünür?
Depolama hesabı ölçümlerini etkinleştirdikten ve Azure portal aracılığıyla oturum açtıktan sonra, analiz verileri hızlı bir şekilde birikme başlar. Her hizmet için günlüğe kaydetme ve ölçümler ayrıdır; günlüğe kaydetme, bu depolama hesabında yalnızca bir etkinlik olduğunda yazılır, ancak ölçümler her saatte, her saat veya her gün, nasıl yapılandırdığınıza bağlı olarak günlüğe kaydedilir.

Günlükler, depolama hesabındaki $logs adlı kapsayıcıda Blok Bloblarında depolanır. Bu kapsayıcı Depolama Analizi etkinleştirildiğinde otomatik olarak oluşturulur. Bu kapsayıcı oluşturulduktan sonra, içeriğini silebilseniz de onu silemezsiniz.

$Logs kapsayıcısı altında, her hizmet için bir klasör vardır ve sonra yıl/ay/gün/saat için alt klasörler bulunur. Saat altında Günlükler numaralandırılır. Dizin yapısı şöyle görünür:

![Günlük dosyalarının görünümü](./media/storage-security-guide/image1.png)

Azure depolama 'ya yönelik her istek günlüğe kaydedilir. İlk birkaç alanı gösteren bir günlük dosyasının anlık görüntüsü aşağıda verilmiştir.

![Günlük dosyasının anlık görüntüsü](./media/storage-security-guide/image2.png)

Günlükleri bir depolama hesabına yönelik her türlü çağrı izlemek için kullanacağınızı görebilirsiniz.

#### <a name="what-are-all-of-those-fields-for"></a>Bu alanların tümü ne için?
Aşağıdaki kaynaklarda listelenen, günlüklerdeki birçok alanın listesini ve ne için kullanıldıklarınızı sağlayan bir makale vardır. Şu sırada alanların listesi aşağıdadır:

![Günlük dosyasındaki alanların anlık görüntüsü](./media/storage-security-guide/image3.png)

GetBlob girişleri ve bunların nasıl yetkilendirildikleri ile ilgileniyor, bu nedenle işlem türü "Get-blob" olan girdileri aramanız ve istek durumu (dördüncü </sup> sütunu) ve yetkilendirme türü (sekizinci </sup> sütunu) kontrol etmeniz gerekir.

Örneğin, yukarıdaki listede yer aldığı ilk birkaç satırda, istek durumu "başarılı" ve yetkilendirme türü "kimliği doğrulandı" olur. Bu, isteğin depolama hesabı anahtarı kullanılarak yetkilendirildiği anlamına gelir.

#### <a name="how-is-access-to-my-blobs-being-authorized"></a>Bloblarıma yetkilendirme erişimi nasıl sağlanır?
İlgilendiğimiz üç durum var.

1. Blob geneldir ve paylaşılan erişim Imzası olmadan bir URL kullanılarak erişilir. Bu durumda, istek durumu "AnonymousSuccess" ve yetkilendirme türü "anonim" olur.

   1.0; 2015-11-17T02:01:29.0488963 Z; GetBlob **Anonymoussuccess**; 200; 124; 37; **anonim**;; depolamam...
2. Blob özeldir ve paylaşılan erişim Imzasıyla birlikte kullanılır. Bu durumda, istek durumu "SASSuccess" ve yetkilendirme türü "SAS" olur.

   1.0; 2015-11-16T18:30:05.6556115 Z; GetBlob **Sassuccess**; 200; 416; 64; **SAS**;; depolamam...
3. Blob özeldir ve depolama anahtarı ona erişmek için kullanılır. Bu durumda, istek durumu "**başarılı**" ve yetkilendirme türü "**kimliği doğrulandı**" dır.

   1.0; 2015-11-16T18:32:24.3174537 Z; GetBlob **Başarılı**; 206; 59; 22; **kimliği doğrulandı**; depolamam...

Bu günlükleri görüntülemek ve analiz etmek için Microsoft Ileti Çözümleyicisi 'ni kullanabilirsiniz. Arama ve filtreleme özelliklerini içerir. Örneğin, bir kullanıcının depolama hesabınıza uygun bir şekilde erişmediğinden emin olmak için, kullanımın beklediğiniz gibi olup olmadığını görmek için GetBlob örnekleri aramak isteyebilirsiniz.

#### <a name="resources"></a>Kaynaklar
* [Depolama Analizi](../storage-analytics.md)

  Bu makalede, depolama analizine genel bakış ve bunların nasıl etkinleştirileceği açıklanır.
* [Depolama Analizi günlük biçimi](https://msdn.microsoft.com/library/azure/hh343259.aspx)

  Bu makalede, Depolama Analizi günlük biçimi gösterilmektedir ve istek için kullanılan kimlik doğrulaması türünü gösteren kimlik doğrulama türü de dahil olmak üzere, içinde bulunan alanların ayrıntıları verilmektedir.
* [Azure portal bir depolama hesabını izleme](../storage-monitor-storage-account.md)

  Bu makalede, bir depolama hesabı için ölçüm izlemenin ve günlüğe kaydetmenin nasıl yapılandırılacağı gösterilir.
* [Azure depolama ölçümlerini ve günlüğe kaydetme, AzCopy ve Ileti Çözümleyicisi kullanarak uçtan uca sorun giderme](../storage-e2e-troubleshooting.md)

  Bu makale, Depolama Analizi ile sorun giderme hakkında bilgi sağlar ve Microsoft Message Analyzer 'ın nasıl kullanılacağını gösterir.
* [Microsoft Ileti Çözümleyicisi Işletim kılavuzu](https://technet.microsoft.com/library/jj649776.aspx)

  Bu makale, Microsoft Ileti Çözümleyicisi 'nin başvurusudur ve bir öğretici, hızlı başlangıç ve özellik özetine bağlantılar içerir.

## <a name="cross-origin-resource-sharing-cors"></a>Çıkış Noktaları Arası Kaynak Paylaşımı (CORS)
### <a name="cross-domain-access-of-resources"></a>Kaynaklara etki alanları arası erişim
Bir etki alanında çalışan bir Web tarayıcısı, farklı bir etki alanından bir kaynak için HTTP isteği yaptığında, bu durum, çıkış noktaları arası HTTP isteği olarak adlandırılır. Örneğin, contoso.com ' dan sunulan bir HTML sayfası, fabrikam.blob.core.windows.net üzerinde barındırılan bir JPEG isteği oluşturur. Tarayıcılar, güvenlik nedenleriyle, JavaScript gibi betikler içinden başlatılan çıkış noktaları arası HTTP isteklerini kısıtlar. Yani, contoso.com üzerindeki bir Web sayfasındaki bazı JavaScript kodu, bu, tarayıcının isteğe izin vermeyeceği anlamına gelir.

Azure depolama ile bunun ne yapması gerekir? Ayrıca, Fabrikam adlı bir depolama hesabı kullanarak BLOB depolama alanında JSON veya XML veri dosyaları gibi statik varlıkları depoluyorsanız, varlıkların etki alanı fabrikam.blob.core.windows.net olur ve contoso.com Web uygulaması bunları kullanarak bunlara erişemez Etki alanları farklı olduğundan JavaScript. Bu aynı zamanda, JavaScript istemcisi tarafından işlenecek JSON verilerini döndüren tablo depolaması gibi Azure depolama hizmetlerinden birini çağırmaya çalışıyorsanız de geçerlidir.

#### <a name="possible-solutions"></a>Olası çözümler
Bunu çözmenin bir yolu, fabrikam.blob.core.windows.net ' a "storage.contoso.com" gibi özel bir etki alanı atacaktır. Bu sorun yalnızca özel etki alanını tek bir depolama hesabına atayabilmenizi sağlar. Varlıklar birden çok depolama hesabında depolanıyorsa ne olursa?

Bunu çözmenin bir diğer yolu, Web uygulamasının depolama çağrılarına yönelik bir ara sunucu görevi görmesini sağlar. Bu, blob depolamaya bir dosya yüklüyorsanız, Web uygulamasının bunu yerel olarak yazıp BLOB depolama alanına kopyalayacağından ya da tümünü bellekten okuyup blob depolamaya yazacak anlamına gelir. Alternatif olarak, dosyaları yerel olarak yükleyen ve BLOB depolamaya yazan özel bir Web uygulaması (örneğin, bir Web API 'SI) yazabilirsiniz. Her iki durumda da ölçeklenebilirlik ihtiyaçlarını belirlerken bu işlevi hesaba çıkarmanız gerekir.

#### <a name="how-can-cors-help"></a>CORS nasıl yardımcı olabilir?
Azure depolama, CORS-Cross-Origin kaynak paylaşımını etkinleştirmenizi sağlar. Her depolama hesabı için, bu depolama hesabındaki kaynaklara erişebilen etki alanlarını belirtebilirsiniz. Örneğin, yukarıda özetlenen örnekte, fabrikam.blob.core.windows.net depolama hesabında CORS 'yi etkinleştirebiliriz ve contoso.com öğesine erişime izin verecek şekilde yapılandırabiliriz. Daha sonra Web uygulaması contoso.com, fabrikam.blob.core.windows.net içindeki kaynaklara doğrudan erişebilir.

Bir şey, CORS 'nin erişime izin verdiğinden, ancak depolama kaynaklarına genel olmayan tüm erişim için gerekli olan kimlik doğrulaması sağlamadığına bir şeydir. Bu, bloblara yalnızca ortak olmaları durumunda erişebildikleri veya size uygun izinleri sunan bir paylaşılan erişim Imzası dahil ettiğiniz anlamına gelir. Tablolar, kuyruklar ve dosyalar ortak erişime sahip değildir ve SAS gerektirir.

Varsayılan olarak, CORS tüm hizmetlerde devre dışıdır. Hizmet ilkelerini ayarlamak için yöntemlerden birini çağırmak üzere REST API veya depolama istemci kitaplığını kullanarak CORS 'yi etkinleştirebilirsiniz. Bunu yaptığınızda, XML içinde olan bir CORS kuralı dahil edersiniz. Bir depolama hesabı için blob hizmeti için hizmet özelliklerini ayarla işlemini kullanarak ayarlanmış bir CORS kuralına örnek aşağıda verilmiştir. Bu işlemi depolama istemci kitaplığını veya Azure depolama için REST API 'Lerini kullanarak gerçekleştirebilirsiniz.

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

Her satırın anlamı şöyledir:

* **Allowedkaynakları** Bu, eşleşen olmayan etki alanlarının depolama hizmetinden veri isteyebilecekleri ve alabileceği anlamına gelir. Bu, hem contoso.com hem de fabrikam.com 'in belirli bir depolama hesabı için blob depolamadan veri isteyediğini söyler. Ayrıca, tüm etki alanlarının isteklere erişmesine izin vermek için bunu bir joker (\*) olarak ayarlayabilirsiniz.
* **AllowedMethods** Bu, isteği yaparken kullanılabilecek yöntemlerin listesidir (HTTP istek fiilleri). Bu örnekte yalnızca PUT ve GET değerlerine izin verilir. Tüm yöntemlerin kullanılmasına izin vermek için bunu bir joker (\*) olarak ayarlayabilirsiniz.
* **Allowedheaders** Bu, isteği yaparken kaynak etki alanının belirtebileceğiniz istek üst bilgileri. Bu örnekte, x-MS-Meta-Data, x-MS-meta-Target ve x-MS-meta-ABC ile başlayan tüm meta veri üst bilgilerine izin verilir. Joker karakter (\*) belirtilen önekle başlayan herhangi bir üst bilgiye izin verildiğini gösterir.
* **ExposedHeaders** Bu, hangi yanıt üst bilgilerinin tarayıcı tarafından istek veren tarafından sunulduğunu belirtir. Bu örnekte, "x-MS-meta-" ile başlayan herhangi bir başlık açığa alınacaktır.
* **Maxageınseconds** Bu, bir tarayıcının ön kontrol SEÇENEKLERI isteğini önbelleğe alacak en uzun süredir. (Ön kontrol isteği hakkında daha fazla bilgi için aşağıdaki ilk makaleyi kontrol edin.)

#### <a name="resources"></a>Kaynaklar
CORS ve nasıl etkinleştirileceği hakkında daha fazla bilgi için bu kaynaklara göz atın.

* [Azure.com üzerinde Azure depolama hizmetleri için çıkış noktaları arası kaynak paylaşımı (CORS) desteği](../storage-cors-support.md)

  Bu makalede CORS 'ye genel bir bakış ve farklı depolama hizmetleri için kuralların nasıl ayarlanacağı sunulmaktadır.
* [MSDN 'de Azure depolama hizmetleri için çıkış noktaları arası kaynak paylaşımı (CORS) desteği](https://msdn.microsoft.com/library/azure/dn535601.aspx)

  Bu, Azure depolama hizmetleri için CORS desteğine yönelik başvuru belgesidir. Bu, her depolama hizmetine uygulanan makalelerin bağlantılarını içerir ve bir örnek gösterir ve CORS dosyasındaki her bir öğeyi açıklar.
* [Microsoft Azure Depolama: CORS 'ye giriş](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

  Bu, CORS duyurduğunu ve nasıl kullanılacağını gösteren ilk blog makalesinin bir bağlantıdır.

## <a name="frequently-asked-questions-about-azure-storage-security"></a>Azure Depolama güvenliği hakkında sık sorulan sorular
1. **HTTPS protokolünü kullanmazsam Azure Storage 'a veya dışarı aktardığım Blobların bütünlüğünü nasıl doğrulayabilirim?**

   HTTPS yerine HTTP kullanmanız gerekirse ve blok Blobları ile çalışıyorsanız, aktarılmakta olan Blobların bütünlüğünü doğrulamaya yardımcı olması için MD5 denetimini kullanabilirsiniz. Bu, ağ/aktarım katmanı hatalarından koruma sağlanmasına yardımcı olur, ancak aracı saldırılarına gerek kalmaz.

   Aktarım düzeyi güvenliği sağlayan HTTPS 'yi kullanacaksanız, MD5 denetimini kullanmak gereksizdir ve gereksizdir.

   Daha fazla bilgi için lütfen [Azure Blob MD5 genel bakış ' a](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx)bakın.
2. **ABD hükümeti için FIPS uyumluluğu ne kadar?**

   Birleşik Devletler Federal bilgi Işleme standardı (FIPS), ABD Federal Kamu bilgisayar sistemleri tarafından hassas verilerin korunması için kullanılmak üzere onaylanan şifreleme algoritmalarını tanımlar. Windows Server veya masaüstü üzerinde FIPS modunu etkinleştirmek, işletim sistemine yalnızca FIPS tarafından doğrulanan şifreleme algoritmalarının kullanılması gerektiğini söyler. Bir uygulama uyumlu olmayan algoritmalar kullanıyorsa, uygulamalar kesilir. With.NET Framework sürümleri 4.5.2 veya üzeri olduğunda, uygulama otomatik olarak şifreleme algoritmalarından bilgisayar FIPS modundayken FIPS uyumlu algoritmalar kullanacak şekilde geçiş yapar.

   Microsoft, FIPS modunu etkinleştirip etkinleştirmeyeceğine karar vermek için sizi her müşteriye bırakır. Kamu düzenlemelerine tabi olmayan müşterilerin varsayılan olarak FIPS modunu etkinleştirmek için etkileyici bir neden olmadığını düşünmüyoruz.

### <a name="resources"></a>Kaynaklar
* [Neden "FIPS modunu" önermediğimiz](https://blogs.technet.microsoft.com/secguide/2014/04/07/why-were-not-recommending-fips-mode-anymore/)

  Bu blog makalesi FIPS 'ye genel bakış sağlar ve varsayılan olarak FIPS modunu etkinleştirmedikleri açıklanmaktadır.
* [FIPS 140 doğrulaması](https://technet.microsoft.com/library/cc750357.aspx)

  Bu makalede, Microsoft ürünlerinin ve şifreleme modüllerinin ABD Federal Kamu kamu için FIPS standardına uygun olduğu hakkında bilgi verilmektedir.
* ["Sistem şifrelemesi: Windows XP ve sonraki Windows sürümlerinde şifreleme, karma ve imzalama için FIPS uyumlu algoritmalar kullanın" güvenlik ayarları etkileri](https://support.microsoft.com/kb/811833)

  Bu makalede, daha eski Windows bilgisayarlarda FIPS modunun kullanımı hakkında bilgi oluşur.
