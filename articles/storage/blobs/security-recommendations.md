---
title: BLOB depolama için güvenlik önerileri
titleSuffix: Azure Storage
description: BLOB depolama için güvenlik önerileri hakkında bilgi edinin. Bu kılavuzun uygulanması, paylaşılan sorumluluk modelinizde açıklandığı gibi güvenlik yükümlülüklerinizi karşılamanız için yardımcı olur.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: tamram
ms.custom: security-recommendations
ms.openlocfilehash: 389d3507c3406743e14324c7b4667e045cfdb3b5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048043"
---
# <a name="security-recommendations-for-blob-storage"></a>BLOB depolama için güvenlik önerileri

Bu makale, blob depolamaya yönelik güvenlik önerilerini içerir. Bu önerilerin uygulanması, paylaşılan sorumluluk modelinizde açıklandığı gibi güvenlik yükümlülüklerinizi karşılaalmanıza yardımcı olur. Microsoft 'un hizmet sağlayıcısı sorumluluklarını nasıl karşılayan hakkında daha fazla bilgi için bkz. [bulutta paylaşılan sorumluluk](../../security/fundamentals/shared-responsibility.md).

Bu makaleye eklenen önerilerden bazıları Azure Güvenlik Merkezi tarafından otomatik olarak izlenebilir. Azure Güvenlik Merkezi, Azure 'daki kaynaklarınızı korumaya yönelik ilk savunma hattınızdır. Azure Güvenlik Merkezi hakkında bilgi için bkz. [Azure Güvenlik Merkezi nedir?](../../security-center/security-center-introduction.md)

Azure Güvenlik Merkezi, olası güvenlik açıklarını belirlemek için Azure kaynaklarınızın güvenlik durumunu düzenli olarak analiz eder. Daha sonra bunları nasıl ele almak için öneriler sağlar. Azure Güvenlik Merkezi önerileri hakkında daha fazla bilgi için bkz. [Azure Güvenlik Merkezi 'Nde güvenlik önerileri](../../security-center/security-center-recommendations.md).

## <a name="data-protection"></a>Veri koruma

| Öneri | Yorumlar | Güvenlik Merkezi |
|-|----|--|
| Azure Resource Manager dağıtım modelini kullanma | Azure Resource Manager dağıtım modelini kullanarak, üstün Azure rol tabanlı erişim denetimi (Azure RBAC) ve denetim, Kaynak Yöneticisi tabanlı dağıtım ve idare, yönetilen kimliklere erişim, gizli dizi Azure Key Vault erişimi, Azure depolama verilerine ve kaynaklarına erişim için Azure AD tabanlı kimlik doğrulaması ve yetkilendirme gibi önemli güvenlik geliştirmeleri için yeni depolama hesapları oluşturun. Mümkünse, Azure Resource Manager kullanmak için klasik dağıtım modelini kullanan mevcut depolama hesaplarını geçirin. Azure Resource Manager hakkında daha fazla bilgi için bkz. [Azure Resource Manager genel bakış](../../azure-resource-manager/management/overview.md). | - |
| Tüm depolama hesaplarınız için Azure Defender 'ı etkinleştirin | Azure depolama için Azure Defender, depolama hesaplarına yönelik olağan dışı ve zararlı olabilecek girişimleri algılayan ek bir güvenlik zekası katmanı sağlar. Güvenlik uyarıları, etkinlik gerçekleştiğinde Azure Güvenlik Merkezi 'nde tetiklenir ve ayrıca, şüpheli etkinliklerin ayrıntıları ve tehditleri araştırıp düzeltilmesi ile ilgili öneriler ile abonelik yöneticilerine e-posta aracılığıyla gönderilir. Daha fazla bilgi için bkz. Azure [Defender for Azure Storage 'ı yapılandırma](../common/azure-defender-storage-configure.md). | [Evet](../../security-center/security-center-remediate-recommendations.md) |
| Bloblar için geçici silmeyi etkinleştir | Bloblar için geçici silme, blob verilerini silindikten sonra kurtarmanıza olanak sağlar. Bloblar için geçici silme hakkında daha fazla bilgi için bkz. [Azure Storage Blobları Için geçici silme](./soft-delete-blob-overview.md). | - |
| Kapsayıcılar için geçici silmeyi aç | Kapsayıcılar için geçici silme, bir kapsayıcıyı silindikten sonra kurtarmanızı sağlar. Kapsayıcılar için geçici silme hakkında daha fazla bilgi için bkz. [kapsayıcılar Için geçici silme (Önizleme)](./soft-delete-container-overview.md). | - |
| Yanlışlıkla veya kötü amaçlı silme veya yapılandırma değişikliklerini engellemek için depolama hesabını kilitle | Hesabı yanlışlıkla veya kötü amaçlı silme veya yapılandırma değişikliğinden korumak için depolama hesabınıza bir Azure Resource Manager kilidi uygulayın. Bir depolama hesabının kilitlenmesi, bu hesabın içindeki verilerin silinmesini engellemez. Yalnızca hesabın silinmesini engeller. Daha fazla bilgi için bkz. [depolama hesabına Azure Resource Manager kilidi uygulama](../common/lock-account-resource.md).
| İş açısından kritik verileri sabit bloblarda depolayın | Blob verilerini bir solucan içinde depolamak için yasal ayrı tutma ve zaman tabanlı bekletme ilkeleri yapılandırın (bir kez yaz, çok oku) durumu. Imune sürekliliği bulunan Bloblar okunabilir, ancak saklama aralığı süresince değiştirilemez veya silinemez. Daha fazla bilgi için bkz. [sabit depolamayla iş açısından kritik blob verilerini depolama](storage-blob-immutable-storage.md). | - |
| Depolama hesabına güvenli aktarım (HTTPS) gerektir | Bir depolama hesabı için güvenli aktarım gerektiğinde, depolama hesabına yönelik tüm isteklerin HTTPS üzerinden yapılması gerekir. HTTP üzerinden yapılan tüm istekler reddedilir. Microsoft, tüm depolama hesaplarınız için her zaman güvenli aktarım gerektirmenizi önerir. Daha fazla bilgi için bkz. [Güvenli aktarım gerektir](../common/storage-require-secure-transfer.md). | - |
| Paylaşılan erişim imzası (SAS) belirteçlerini yalnızca HTTPS bağlantılarıyla sınırla | İstemci blob verilerine erişmek için SAS belirteci kullandığında HTTPS gerektirme, gizlice dinleme riskini en aza indirmenize yardımcı olur. Daha fazla bilgi için bkz. [paylaşılan erişim imzaları (SAS) kullanarak Azure depolama kaynaklarına sınırlı erişim verme](../common/storage-sas-overview.md). | - |

## <a name="identity-and-access-management"></a>Kimlik ve erişim yönetimi

| Öneri | Yorumlar | Güvenlik Merkezi |
|-|----|--|
| Blob verilerine erişim yetkisi vermek için Azure Active Directory (Azure AD) kullanın | Azure AD, istekleri blob depolamaya yetkilendirmek için paylaşılan anahtar üzerinde üstün güvenlik ve kullanım kolaylığı sağlar. Daha fazla bilgi için bkz. [Azure Active Directory kullanarak Azure bloblarına ve kuyruklara erişim yetkisi verme](../common/storage-auth-aad.md). | - |
| Azure RBAC aracılığıyla bir Azure AD güvenlik sorumlusuna izin atarken en az ayrıcalık sorumlusunu göz önünde bulundurun | Bir Kullanıcı, Grup veya uygulamaya bir rol atarken, bu güvenlik sorumlusuna yalnızca onların görevlerini gerçekleştirmesi için gerekli olan izinleri verin. Kaynaklara erişimi kısıtlamak, verilerinizin yanlışlıkla ve kötü amaçlı olarak kötüye kullanımını önlemeye yardımcı olur. | - |
| Kullanıcılara blob verilerine sınırlı erişim vermek için bir Kullanıcı temsili SAS kullanın | Kullanıcı temsili SAS, Azure Active Directory (Azure AD) kimlik bilgileriyle ve SAS için belirtilen izinlerle korunmaktadır. Kullanıcı temsilciliğini, kapsamı ve işlevi açısından bir hizmet sa 'sı ile benzerdir, ancak hizmet SAS üzerinden güvenlik avantajları sunar. Daha fazla bilgi için bkz. [paylaşılan erişim imzaları (SAS) kullanarak Azure depolama kaynaklarına sınırlı erişim verme](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). | - |
| Hesap erişim anahtarlarınızı Azure Key Vault ile güvenli hale getirin | Microsoft, Azure depolama 'ya istekleri yetkilendirmek için Azure AD kullanılmasını önerir. Ancak, paylaşılan anahtar yetkilendirmesi kullanmanız gerekiyorsa, hesap anahtarlarınızın Azure Key Vault sağlayın. Anahtar kasasındaki anahtarları uygulamanıza kaydetmek yerine çalışma zamanında alabilirsiniz. Azure Key Vault hakkında daha fazla bilgi için bkz. [Azure Key Vault genel bakış](../../key-vault/general/overview.md). | - |
| Hesap anahtarlarınızı düzenli aralıklarla yeniden oluşturun | Hesap anahtarlarını döndürmek, verilerinizi kötü amaçlı aktörlere sunma riskini düzenli aralıklarla azaltır. | - |
| Paylaşılan anahtar yetkilendirmesine izin verme | Bir depolama hesabı için paylaşılan anahtar yetkilendirmesinin yetkisini kaldırdığınızda, Azure depolama, hesap erişim anahtarlarıyla yetkilendirilmiş olan bu hesaba yapılan tüm sonraki istekleri reddeder. Yalnızca Azure AD ile yetkilendirilmiş güvenli istekler başarılı olur. Daha fazla bilgi için bkz. [Azure depolama hesabı Için paylaşılan anahtar yetkilendirmesini engelleme](../common/shared-key-authorization-prevent.md). | - |
| SAS 'ye izin atarken en az ayrıcalık sorumlusunu göz önünde bulundurun | SAS oluştururken, yalnızca istemcisinin işlevini gerçekleştirmesi için gerekli olan izinleri belirtin. Kaynaklara erişimi kısıtlamak, verilerinizin yanlışlıkla ve kötü amaçlı olarak kötüye kullanımını önlemeye yardımcı olur. | - |
| İstemcilere yaptığınız tüm SAS için bir iptal planı vardır | SAS tehlikeye girerse, bu SAS 'ı mümkün olan en kısa sürede iptal etmek isteyeceksiniz. Bir Kullanıcı temsilciliğini iptal etmek için, bu anahtarla ilişkili tüm imzaları hızlıca geçersiz kılmak üzere Kullanıcı temsili anahtarını iptal edin. Depolanan bir erişim ilkesiyle ilişkili bir hizmet SAS 'sini iptal etmek için, saklı erişim ilkesini silebilir, ilkeyi yeniden adlandırabilir veya süre sonu saatini geçmişteki bir zamana dönüştürebilirsiniz. Daha fazla bilgi için bkz. [paylaşılan erişim imzaları (SAS) kullanarak Azure depolama kaynaklarına sınırlı erişim verme](../common/storage-sas-overview.md).  | - |
| Bir hizmet SAS, depolanan bir erişim ilkesiyle ilişkilendirilmediği zaman aşımı süresini bir saat veya daha az olarak ayarlayın | Depolanan bir erişim ilkesiyle ilişkilendirilmemiş bir hizmet SAS iptal edilemez. Bu nedenle, süre sonu süresini kısıtlamak için SAS bir saat için geçerli veya daha az önerilir. | - |
| Kapsayıcılara ve bloblara anonim genel okuma erişimini devre dışı bırakma | Bir kapsayıcıya ve bloblarına anonim genel okuma erişimi, herhangi bir istemciye bu kaynaklara salt okuma erişimi verir. Senaryonuz için gerekli değilse, genel okuma erişimini etkinleştirmemeye özen gösterin. Bir depolama hesabı için anonim genel erişimi devre dışı bırakma hakkında bilgi edinmek için bkz. [kapsayıcılar ve Bloblar için anonim genel okuma erişimini yapılandırma](anonymous-read-access-configure.md).  | - |

## <a name="networking"></a>Ağ

| Öneri | Yorumlar | Güvenlik Merkezi |
|-|----|--|
| Depolama hesabı için gerekli minimum Aktarım Katmanı Güvenliği (TLS) sürümünü yapılandırın.  | İstemcilerin, bu hesap için en düşük TLS sürümünü yapılandırarak bir Azure depolama hesabına yönelik istekler yapması için daha güvenli bir TLS sürümü kullanmasını gerektir. Daha fazla bilgi için bkz. [depolama hesabı için gereken minimum Aktarım Katmanı Güvenliği (TLS) sürümünü yapılandırma](../common/transport-layer-security-configure-minimum-version.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)| - |
| Tüm depolama hesaplarınızda **Güvenli aktarım gerekli** seçeneğini etkinleştirin | **Güvenli aktarım gerekli** seçeneğini etkinleştirdiğinizde, depolama hesabında yapılan tüm isteklerin güvenli bağlantılar üzerinden gerçekleşmesi gerekir. HTTP üzerinden yapılan tüm istekler başarısız olur. Daha fazla bilgi için bkz. [Azure Storage 'da güvenli aktarım gerektir](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). | [Evet](../../security-center/security-center-remediate-recommendations.md) |
| Güvenlik duvarı kurallarını etkinleştirme | Depolama hesabınıza erişimi, belirtilen IP adreslerinden veya aralıklarından kaynaklanan isteklerle veya bir Azure sanal ağı 'ndaki (VNet) bir alt ağ listesinden oluşan isteklerle sınırlamak için güvenlik duvarı kurallarını yapılandırın. Güvenlik duvarı kurallarını yapılandırma hakkında daha fazla bilgi için bkz. [Azure Storage güvenlik duvarlarını ve sanal ağları yapılandırma](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). | - |
| Güvenilen Microsoft hizmetlerinin depolama hesabına erişmesine izin ver | İstekler bir Azure sanal ağı (VNet) içinde veya izin verilen ortak IP adreslerinden bir hizmetten kaynaklanmadığı takdirde, depolama hesabınız için Güvenlik Duvarı kurallarının etkinleştirilmesi, varsayılan olarak gelen istekleri engeller. Engellenen istekler diğer Azure hizmetlerinden, Azure portal, günlük ve ölçüm hizmetlerinden ve bu şekilde devam eder. Güvenilen Microsoft hizmetlerinin depolama hesabına erişmesine izin vermek için bir özel durum ekleyerek diğer Azure hizmetlerinden gelen isteklere izin verebilirsiniz. Güvenilen Microsoft Hizmetleri için bir özel durum ekleme hakkında daha fazla bilgi için bkz. [Azure Storage güvenlik duvarlarını ve sanal ağları yapılandırma](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).| - |
| Özel uç noktaları kullanma | Özel bir uç nokta, Azure sanal ağınızdan (VNet) depolama hesabına özel bir IP adresi atar. VNet ve depolama hesabı arasındaki tüm trafiğin özel bir bağlantı üzerinden güvenliğini sağlar. Özel uç noktalar hakkında daha fazla bilgi için bkz. [Azure özel uç nokta kullanarak bir depolama hesabına özel olarak bağlanma](../../private-link/tutorial-private-endpoint-storage-portal.md). | - |
| VNet hizmet etiketlerini kullanma | Hizmet etiketi, belirli bir Azure hizmetinden bir IP adresi önekleri grubunu temsil eder. Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir. Azure depolama tarafından desteklenen hizmet etiketleri hakkında daha fazla bilgi için bkz. [Azure hizmet etiketlerine genel bakış](../../virtual-network/service-tags-overview.md). Giden ağ kuralları oluşturmak için hizmet etiketlerinin nasıl kullanılacağını gösteren bir öğretici için bkz. [PaaS kaynaklarına erişimi kısıtlama](../../virtual-network/tutorial-restrict-network-access-to-resources.md). | - |
| Belirli ağlarla ağ erişimini sınırlandırma | Erişim gerektiren istemcileri barındıran ağlarla ağ erişimini kısıtlamak, kaynaklarınızın ağ saldırılarına maruz kalmasını azaltır. | [Evet](../../security-center/security-center-remediate-recommendations.md) |
| Ağ yönlendirme tercihini yapılandırma | Azure depolama hesabınız için ağ trafiği, Microsoft küresel ağ veya Internet yönlendirmesi kullanarak Internet üzerinden istemcilerden hesabınıza nasıl yönlendirildiğini belirtmek için ağ yönlendirme tercihini yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Azure depolama için ağ yönlendirme tercihini yapılandırma](../common/network-routing-preference.md). | - |

## <a name="loggingmonitoring"></a>Günlüğe kaydetme/Izleme

| Öneri | Yorumlar | Güvenlik Merkezi |
|-|----|--|
| İsteklerin nasıl yetkilendirildiğini izleme | Azure depolama 'ya karşı yapılan her isteği nasıl yetkilendirdiğini izlemek için Azure depolama günlüğü 'nü etkinleştirin. Günlükler, bir isteğin bir OAuth 2,0 belirteci kullanılarak, paylaşılan anahtar kullanılarak veya paylaşılan erişim imzası (SAS) kullanılarak yapılıp yapılmadığını belirtir. Daha fazla bilgi için bkz. Azure [izleyici ile Azure Blob depolamayı izleme](monitor-blob-storage.md) veya [Klasik Izleme ile Azure depolama Analizi günlüğü](../common/storage-analytics-logging.md). | - |
| Azure Izleyici 'de uyarıları ayarlama | Günlük uyarılarını, bir küme sıklığında kaynak günlüklerini değerlendirmek ve sonuçlara göre bir uyarı tetiklemek için yapılandırın. Daha fazla bilgi için bkz. [Azure izleyici 'de günlük uyarıları](../../azure-monitor/alerts/alerts-unified-log.md). | - |

## <a name="next-steps"></a>Sonraki adımlar

- [Azure güvenlik belgeleri](../../security/index.yml)
- [Güvenli Geliştirme belgeleri](../../security/develop/index.yml).