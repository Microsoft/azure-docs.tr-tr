---
title: Azure Key Vault gizli dizileri hakkında-Azure Key Vault
description: Azure Key Vault Gizlilike genel bakış.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: dc4368075e1cecb43887c9fab21e55b23208dbab
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814937"
---
# <a name="about-azure-key-vault-secrets"></a>Azure Key Vault gizli dizileri hakkında

[Key Vault](../general/overview.md) , parolalar ve veritabanı bağlantı dizeleri gibi genel gizliliklerin güvenli bir şekilde depolanmasını sağlar.

Geliştirici perspektifinden Key Vault API 'Leri, gizli değerleri dizeler olarak kabul eder ve döndürür. Dahili olarak Key Vault, gizli dizileri, her biri en fazla 25k bayt boyutunda olan sekizlik (8 bit bayt) dizileri olarak depolar ve yönetir. Key Vault hizmeti gizli dizileri için semantik bir sağlamaz. Yalnızca verileri kabul eder, şifreler, kaydeder ve gizli bir tanımlayıcı ("kimlik") döndürür. Tanımlayıcı, daha sonraki bir zamanda gizli dizi almak için kullanılabilir.  

Son derece hassas veriler söz konusu olduğunda müşterilerin veriler için ek koruma katmanlarını göz önünde bulundurması gerekir. Anahtarı Anahtar Kasasında depolamadan önce ayrı bir koruma anahtarı kullanarak verileri şifrelemek bunun bir örneğidir.  

Key Vault, gizlilikler için bir contentType alanını da destekler. İstemciler, alındığı sırada gizli verilerin yorumlanmasına yardımcı olmak için bir gizli dizi içerik türünü belirtebilir. Bu alanın uzunluk üst sınırı 255 karakterdir. Önceden tanımlı değer yok. Önerilen kullanım, gizli verileri yorumlamak için bir ipucu olarak kullanılır. Örneğin, bir uygulama parolaları ve sertifikaları gizli dizi olarak saklayabilir, ardından bu alanı ayırt etmek için kullanabilirsiniz. Önceden tanımlanmış değer yok.  

## <a name="encryption"></a>Şifreleme

Key Vault tüm gizli dizileri şifreli olarak depolanır. Bu şifreleme saydamdır ve kullanıcıdan hiçbir eylem gerektirmez. Azure Key Vault hizmeti, bunları eklediğinizde sırlarınızı şifreler ve bunları okurken otomatik olarak çözer. Şifreleme anahtarı her Anahtar Kasası için benzersizdir.

## <a name="secret-attributes"></a>Gizli dizi öznitelikleri

Gizli verilerin yanı sıra, aşağıdaki öznitelikler de belirtilebilir:  

- *Exp*: intdate, isteðe baðlý, Default, **sonsuza** kadar. *Exp* (sona erme saati) özniteliği, [belirli durumlar](#date-time-controlled-operations)dışında, gizli verilerin alınmayabileceği veya sonrasında sona erme süresini belirler. Bu alan yalnızca, Anahtar Kasası Hizmeti kullanıcılarına belirli bir parolanın kullanılamayacağını bildiren **bilgilendirme** amaçlıdır. Değeri, bir IntDate değeri içeren bir sayı OLMALıDıR.   
- *NBF*: intdate, isteðe baðlý, Default **artık**. *NBF* (before) özniteliği, [belirli durumlar](#date-time-controlled-operations)dışında gizli verilerin alınamadığı süreyi tanımlar. Bu alan yalnızca **bilgilendirme** amaçlıdır. Değeri, bir IntDate değeri içeren bir sayı OLMALıDıR. 
- *etkin*: Boolean, isteğe bağlı, varsayılan değer **true**'dur. Bu öznitelik, gizli verilerin alınıp alınamayacağını belirtir. Enabled özniteliği *, NBF ve* *Exp* arasında bir işlem olduğunda, *NBF* *ve exp* ile birlikte kullanıldığında, yalnızca Enabled **değeri true** olarak ayarlandığında izin verilir. [Belirli durumlar](#date-time-controlled-operations)dışında, *NBF* ve *Exp* penceresinin dışındaki işlemlere otomatik olarak izin verilmez.  

Gizli öznitelikler içeren herhangi bir yanıta dahil olan ek salt okuma öznitelikleri vardır:  

- *oluşturma*: Int32 Tarih, isteğe bağlı. Oluşturulan öznitelik, bu gizli anahtar sürümünün ne zaman oluşturulduğunu gösterir. Bu değer, bu özniteliğin eklenmesinden önce oluşturulan gizli diziler için null. Değeri, bir IntDate değeri içeren bir sayı olmalıdır.  
- *güncelleştirildi*: Int32 tarihi, isteğe bağlı. Updated özniteliği, gizli anahtar sürümünün ne zaman güncelleştirildiğini gösterir. Bu değer, bu özniteliğin eklenmesinden önce son güncellenen gizli diziler için null. Değeri, bir IntDate değeri içeren bir sayı olmalıdır.

Her bir Anahtar Kasası nesne türü için ortak öznitelikler hakkında daha fazla bilgi için bkz. [Azure Key Vault anahtarlar, gizlilikler ve sertifikalara genel bakış](../general/about-keys-secrets-certificates.md)

### <a name="date-time-controlled-operations"></a>Tarih-saat denetimli işlemler

Bir gizli anahtar **Al** işlemi, *NBF*  /  *Exp* penceresi dışında, henüz geçerli olmayan ve süresi geçmiş gizli diziler için çalışacaktır. Gizli olmayan bir gizli dizi için bir gizli dizi **Al** işlemi çağırmak, test amacıyla kullanılabilir. Bir zaman aşımına uğradı (**alma**), kurtarma işlemleri için kullanılabilir.

## <a name="secret-access-control"></a>Parola erişim denetimi

Key Vault yönetilen gizli dizileri için Access Control, bu gizli dizileri içeren Key Vault düzeyinde sağlanır. Gizli dizileri için erişim denetimi ilkesi, aynı Key Vault anahtarlar için erişim denetimi ilkesinden farklıdır. Kullanıcılar, gizli dizileri tutmak için bir veya daha fazla kasa oluşturabilir ve senaryoya uygun bölümlemeye ve gizli dizi yönetimine devam etmek için gereklidir.   

Aşağıdaki izinler, her bir kasada, bir kasadaki gizli dizi erişim denetimi girdisinde kullanılabilir ve gizli bir nesne üzerinde izin verilen işlemleri yakından yansıtır:  

- Gizli dizi yönetimi işlemlerine yönelik izinler
  - *Al*: gizli dizi oku  
  - *liste*: Key Vault depolanan bir gizli dizinin gizli dizilerini veya sürümlerini listeleyin  
  - *Ayarla*: gizli dizi oluştur  
  - *Sil*: gizli dizi silme  
  - *kurtar*: silinen gizli dizi kurtarma
  - *yedekleme*: anahtar kasasında gizli dizi yedekleme
  - *geri yükleme*: yedeklenen gizli anahtarı bir anahtar kasasına geri yükleme

- Ayrıcalıklı işlemler için izinler
  - *Temizle*: silinen bir parolayı temizle (kalıcı olarak sil)

Gizli dizileri ile çalışma hakkında daha fazla bilgi için, [Key Vault REST API başvurusunda gizli işlemler](/rest/api/keyvault)bölümüne bakın. İzinleri oluşturma hakkında bilgi için bkz. [kasa-oluşturma veya güncelleştirme](/rest/api/keyvault/vaults/createorupdate) ve [kasa-güncelleştirme erişim ilkesi](/rest/api/keyvault/vaults/updateaccesspolicy). 

Key Vault erişimi denetlemek için nasıl yapılır kılavuzlarından:
- [CLı kullanarak Key Vault erişim ilkesi atama](../general/assign-access-policy-cli.md)
- [PowerShell kullanarak Key Vault erişim ilkesi atama](../general/assign-access-policy-powershell.md)
- [Azure portal kullanarak Key Vault erişim ilkesi atama](../general/assign-access-policy-portal.md)
- [Azure rol tabanlı erişim denetimi ile Key Vault anahtarlarına, sertifikalara ve gizli anahtarlara erişim sağlama](../general/rbac-guide.md)

## <a name="secret-tags"></a>Gizli Etiketler  
Etiketler biçiminde uygulamaya özgü ek meta verileri belirtebilirsiniz. Key Vault, her birinin 256 karakter adı ve 256 karakter değeri olabilen en fazla 15 etiketi destekler.  

>[!Note]
>Etiketler, *liste* veya *Al* iznine sahip olmaları durumunda bir arayan tarafından okunabilir.

## <a name="azure-storage-account-key-management"></a>Azure depolama hesabı anahtar yönetimi

Key Vault, [Azure depolama hesabı](../../storage/common/storage-account-overview.md) anahtarlarını yönetebilir:

- Dahili olarak, Key Vault anahtarları Azure Storage hesabıyla listeleyebilir (eşitleyebilir). 
- Anahtarları düzenli aralıklarla yeniden üretir Key Vault (döndürür).
- Anahtar değerleri, çağırana yanıt olarak hiçbir şekilde döndürülmez.
- Key Vault hem depolama hesaplarının hem de klasik depolama hesaplarının anahtarlarını yönetir.

Daha fazla bilgi için bkz.
- [Depolama hesabı erişim anahtarları](../../storage/common/storage-account-keys-manage.md)
- [Azure Key Vault) depolama hesabı anahtarları yönetimi](../secrets/overview-storage-keys.md)


## <a name="storage-account-access-control"></a>Depolama hesabı erişim denetimi

Bir kullanıcı veya uygulama sorumlusu yönetilen bir depolama hesabında işlem gerçekleştirmek üzere yetkilendirirken aşağıdaki izinler kullanılabilir:  

- Yönetilen depolama hesabı ve SaS tanım işlemlerine yönelik izinler
  - *Get*: bir depolama hesabı hakkında bilgi alır 
  - *liste*: Key Vault tarafından yönetilen depolama hesaplarını listeleyin
  - *güncelleştirme*: depolama hesabını güncelleştirme
  - *Sil*: depolama hesabını silme  
  - *kurtar*: silinen bir depolama hesabını kurtar
  - *yedekleme*: bir depolama hesabını yedekleme
  - *geri yükle*: yedeklenen bir depolama hesabını bir Key Vault geri yükleme
  - *Ayarla*: depolama hesabı oluşturun veya güncelleştirin
  - *RegenerateKey*: bir depolama hesabı için belirtilen anahtar değerini yeniden oluştur
  - *getsas*: bir depolama HESABı için SAS tanımı hakkında bilgi alın
  - *listsas*: depolama hesabı IÇIN depolama SAS tanımlarını listeleyin
  - *deletesas*: bir depolama hesabından SAS tanımını silme
  - *setsas*: depolama hesabı IÇIN yeni SAS tanımı/öznitelikleri oluşturma veya güncelleştirme

- Ayrıcalıklı işlemler için izinler
  - *Temizle*: yönetilen bir depolama hesabını temizle (kalıcı olarak sil)

Daha fazla bilgi için [Key Vault REST API başvurusu Içindeki depolama hesabı işlemlerine](/rest/api/keyvault)bakın. İzinleri oluşturma hakkında bilgi için bkz. [kasa-oluşturma veya güncelleştirme](/rest/api/keyvault/vaults/createorupdate) ve [kasa-güncelleştirme erişim ilkesi](/rest/api/keyvault/vaults/updateaccesspolicy).

Key Vault erişimi denetlemek için nasıl yapılır kılavuzlarından:
- [CLı kullanarak Key Vault erişim ilkesi atama](../general/assign-access-policy-cli.md)
- [PowerShell kullanarak Key Vault erişim ilkesi atama](../general/assign-access-policy-powershell.md)
- [Azure portal kullanarak Key Vault erişim ilkesi atama](../general/assign-access-policy-portal.md)
- [Azure rol tabanlı erişim denetimi ile Key Vault anahtarlarına, sertifikalara ve gizli anahtarlara erişim sağlama](../general/rbac-guide.md)


## <a name="next-steps"></a>Sonraki adımlar

- [Key Vault Hakkında](../general/overview.md)
- [Anahtarlar, gizli diziler ve sertifikalar hakkında](../general/about-keys-secrets-certificates.md)
- [Anahtarlar hakkında](../keys/about-keys.md)
- [Sertifikalar hakkında](../certificates/about-certificates.md)
- [Anahtar kasasına erişimin güvenliğini sağlama](../general/security-features.md)
- [Key Vault Geliştirici Kılavuzu](../general/developers-guide.md)