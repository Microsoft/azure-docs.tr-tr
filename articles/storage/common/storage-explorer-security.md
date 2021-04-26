---
title: Azure Depolama Gezgini Güvenlik Kılavuzu | Microsoft Docs
description: Azure Depolama Gezgini için Güvenlik Kılavuzu
services: storage
author: cralvord
ms.service: storage
ms.topic: best-practice
ms.date: 07/30/2020
ms.author: cralvord
ms.openlocfilehash: 283ec9999f9b4362035b6770383984efb0879d49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92783768"
---
# <a name="azure-storage-explorer-security-guide"></a>Azure Depolama Gezgini Güvenlik Kılavuzu

Microsoft Azure Depolama Gezgini, Windows, macOS ve Linux 'ta güvenli ve güvenli bir şekilde Azure Depolama verileriyle kolayca çalışmanıza olanak sağlar. Bu yönergeleri izleyerek verilerinizin korumalı kalmasını sağlayabilirsiniz.

## <a name="general"></a>Genel

- **Her zaman Depolama Gezgini en son sürümünü kullanın.** Depolama Gezgini sürümlerde güvenlik güncelleştirmeleri bulunabilir. Güncel kalarak genel güvenlik sağlanmasına yardımcı olur.
- **Yalnızca güvendiğiniz kaynaklara bağlanın.** Güvenilmeyen kaynaklardan indirmiş olduğunuz veriler kötü amaçlı olabilir ve verileri güvenilmeyen bir kaynağa yüklemek, kayıp veya çalınmış verilere neden olabilir.
- **Mümkün olduğunda HTTPS kullanın.** Depolama Gezgini varsayılan olarak HTTPS kullanır. Bazı senaryolar HTTP kullanmanıza izin verir, ancak HTTP yalnızca son çare olarak kullanılmalıdır.
- **İhtiyaç duyan kişilere yalnızca gerekli izinlerin verildiğinden emin olun.** Kaynaklarınıza erişim verirken aşırı derecede izin vermeyerek kaçının.
- **Kritik işlemleri yürütürken dikkatli olun.** Silme ve üzerine yazma gibi bazı işlemler geri alınamaz ve veri kaybına neden olabilir. Bu işlemleri yürütmeden önce doğru kaynaklarla çalıştığınızdan emin olun.

## <a name="choosing-the-right-authentication-method"></a>Doğru kimlik doğrulama yöntemini seçme

Depolama Gezgini, Azure depolama kaynaklarınıza erişmek için çeşitli yollar sağlar. Hangi yöntemi seçerseniz önerilerimiz aşağıda verilmiştir.

### <a name="azure-ad-authentication"></a>Azure AD kimlik doğrulaması

Azure depolama kaynaklarınıza erişmenin en kolay ve en güvenli yolu Azure hesabınızla oturum açmanızı sağlar. Oturum açmak, Azure AD kimlik doğrulamasını kullanır ve şunları yapmanıza olanak sağlar:

- Belirli kullanıcılara ve gruplara erişim izni verin.
- Belirli kullanıcılara ve gruplara istediğiniz zaman erişimi iptal edin.
- Multi-Factor Authentication gerektirme gibi erişim koşullarını zorunlu tutun.

Mümkün olduğunda Azure AD kimlik doğrulaması kullanmanızı öneririz.

Bu bölümde, depolama kaynaklarınızın güvenliğini sağlamak için kullanılabilecek iki Azure AD tabanlı teknoloji açıklanmaktadır.

#### <a name="azure-role-based-access-control-azure-rbac"></a>Azure rol tabanlı erişim denetimi (Azure RBAC)

Azure [rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md) , Azure kaynaklarınız üzerinde ayrıntılı erişim denetimi sağlar. Azure rolleri ve izinleri Azure portal yönetilebilir.

Depolama Gezgini, depolama hesaplarına, bloblara ve kuyruklara Azure RBAC erişimini destekler. Dosya paylaşımlarına veya tablolara erişmeniz gerekiyorsa, depolama hesabı anahtarlarını listeleme izni veren Azure rolleri atamanız gerekir.

#### <a name="access-control-lists-acls"></a>Erişim denetim listeleri (ACL’ler)

[Erişim denetim listeleri (ACL 'ler)](../blobs/data-lake-storage-access-control.md) ADLS 2. blob kapsayıcılarında dosya ve klasör düzeyinde erişimi denetlemenize olanak tanır. Depolama Gezgini kullanarak ACL 'Leri yönetebilirsiniz.

### <a name="shared-access-signatures-sas"></a>Paylaşılan erişim imzaları (SAS)

Azure AD kimlik doğrulamasını kullanmıyorsanız, paylaşılan erişim imzalarının kullanılması önerilir. Paylaşılan erişim imzaları ile şunları yapabilirsiniz:

- Güvenli kaynaklara anonim sınırlı erişim sağlayın.
- Paylaşılan erişim ilkesinden (SAP) oluşturulmuşsa, bir sa 'yı hemen iptal edin.

Ancak, paylaşılan erişim imzaları ile şunları yapamazsınız:

- SAS kullanabilecek kişileri kısıtlayın. Geçerli bir SAS, sahip olduğu herkes tarafından kullanılabilir.
- Bir paylaşılan erişim ilkesinden (SAP) oluşturulmayan bir sa 'yı iptal edin.

Depolama Gezgini içinde SAS kullanırken aşağıdaki yönergeleri öneriyoruz:

- **SAS belirteçlerinin ve URI 'lerin dağıtımını sınırlayın.** Yalnızca SAS belirteçlerini ve URI 'Leri güvenilir kişilere dağıtın. SAS dağıtımını kısıtlamak, SAS 'nin kötüye kullanılması olasılığını azaltır.
- **Yalnızca güvendiğiniz varlıklardan SAS belirteçlerini ve URI 'Leri kullanın.**
- **Mümkünse SAS belirteçlerini ve URI 'Leri oluştururken paylaşılan erişim ilkeleri (SAP) kullanın.** SAP silinerek SAS iptal edilebildiğinden, paylaşılan erişim ilkesini temel alan bir SAS, çıplak bir sa 'dan daha güvenlidir.
- **Minimum kaynak erişimi ve izinleriyle belirteçler oluşturun.** Minimum izinler, SAS kötüye gerekliyse oluşabilecek olası hasar sayısını sınırlar.
- **Yalnızca gerekli olduğu sürece geçerli olan belirteçleri oluşturun.** Kısa bir süre önce çıplak SAS için önemli bir değer olduğundan, bunları iptal etmenin bir yolu yoktur.

> [!IMPORTANT]
> Hizmet istekleri veya hata raporlarında olduğu gibi, sorun giderme amacıyla SAS belirteçlerini ve URI 'Leri paylaşırken, her zaman SAS 'nin en azından imza bölümünü redaksiyonun.

### <a name="storage-account-keys"></a>Depolama hesabı anahtarları

Depolama hesabı anahtarları, depolama hesabındaki hizmetlere ve kaynaklara sınırsız erişim hakkı verir. Bu nedenle, Depolama Gezgini kaynaklara erişmek için anahtarların kullanımını sınırlandırmaya öneririz. Bunun yerine erişim sağlamak için Azure RBAC özelliklerini veya SAS 'yi kullanın.

Bazı Azure rolleri, depolama hesabı anahtarlarını alma izni verir. Bu rollere sahip kişiler, Azure RBAC tarafından verilen veya reddedilen izinleri etkili bir şekilde atlayabilirler. Gerekli olmadığı takdirde bu izni vermemenizi öneririz.

Depolama Gezgini isteklerin kimliğini doğrulamak için, varsa depolama hesabı anahtarlarını kullanmaya çalışacaktır. Ayarlar ' da bu özelliği devre dışı bırakabilirsiniz (**hizmetler > depolama hesapları > anahtarların kullanımını devre dışı bırakın**). Bazı özellikler, klasik depolama hesaplarıyla çalışma gibi Azure RBAC 'yi desteklemez. Bu tür özellikler hala anahtar gerektirir ve bu ayardan etkilenmez.

Depolama kaynaklarınıza erişmek için anahtarları kullanmanız gerekiyorsa aşağıdaki yönergeleri öneririz:

- **Hesap anahtarlarınızı hiç kimseyle paylaşmayın.**
- **Depolama hesabı anahtarlarınızı parolalar gibi değerlendirin.** Anahtarlarınızı erişilebilir yapmanız gerekiyorsa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)gibi güvenli depolama çözümlerini kullanın.

> [!NOTE]
> Bir depolama hesabı anahtarının yanlışlıkla paylaşılıp paylaşıldığını veya dağıtıldığını düşünüyorsanız, Azure portal depolama hesabınız için yeni anahtarlar oluşturabilirsiniz.

### <a name="public-access-to-blob-containers"></a>Blob kapsayıcılarına genel erişim

Depolama Gezgini, Azure Blob depolama kapsayıcılarınızın erişim düzeyini değiştirmenize izin verir. Özel olmayan BLOB kapsayıcıları, bu kapsayıcılardaki verilere anonim okuma erişimi sağlar.

Bir blob kapsayıcısı için genel erişimi etkinleştirirken aşağıdaki yönergeleri öneriyoruz:

- **Potansiyel olabilecek duyarlı veriler içerebilen bir blob kapsayıcısına genel erişimi etkinleştirmeyin.** Blob kabın tüm özel verilerden boş olduğundan emin olun.
- **Potansiyel olarak gizli olmayan verileri blob veya kapsayıcı erişimi olan bir blob kapsayıcısına yüklemeyin.** 

## <a name="next-steps"></a>Sonraki adımlar

- [Güvenlik önerileri](../blobs/security-recommendations.md)