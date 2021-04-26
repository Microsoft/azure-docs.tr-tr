---
title: Kuyruk depolama için güvenlik önerileri
titleSuffix: Azure Storage
description: Kuyruk depolama için güvenlik önerileri hakkında bilgi edinin. Bu kılavuzun uygulanması, paylaşılan sorumluluk modelinizde açıklandığı gibi güvenlik yükümlülüklerinizi karşılamanız için yardımcı olur.
author: tamram
services: storage
ms.author: tamram
ms.date: 03/11/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: queues
ms.custom: security-recommendations
ms.openlocfilehash: db0e033adf553c25c6b7b401f8d0df1a2cd5995f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97592169"
---
# <a name="security-recommendations-for-queue-storage"></a>Kuyruk depolama için güvenlik önerileri

Bu makale kuyruk depolaması için güvenlik önerileri içerir. Bu önerilerin uygulanması, paylaşılan sorumluluk modelinizde açıklandığı gibi güvenlik yükümlülüklerinizi karşılaalmanıza yardımcı olur. Microsoft 'un hizmet sağlayıcısı sorumluluklarını karşılama hakkında daha fazla bilgi için bkz. [bulut bilgi işlem Için paylaşılan sorumluluklar](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf).

Bu makaleye eklenen önerilerden bazıları Azure Güvenlik Merkezi tarafından otomatik olarak izlenebilir. Azure Güvenlik Merkezi, Azure 'daki kaynaklarınızı korumaya yönelik ilk savunma hattınızdır. Azure Güvenlik Merkezi hakkında bilgi için bkz. [Azure Güvenlik Merkezi nedir?](../../security-center/security-center-introduction.md).

Azure Güvenlik Merkezi, olası güvenlik açıklarını belirlemek için Azure kaynaklarınızın güvenlik durumunu düzenli olarak analiz eder. Daha sonra bunları nasıl ele almak için öneriler sağlar. Azure Güvenlik Merkezi önerileri hakkında daha fazla bilgi için bkz. [Azure Güvenlik Merkezi 'Nde güvenlik önerileri](../../security-center/security-center-recommendations.md).

## <a name="data-protection"></a>Veri koruma

| Öneri | Yorumlar | Güvenlik Merkezi |
|-|----|--|
| Azure Resource Manager dağıtım modelini kullanma | Azure Resource Manager dağıtım modelini kullanarak, üstün Azure rol tabanlı erişim denetimi (Azure RBAC) ve denetim, Kaynak Yöneticisi tabanlı dağıtım ve idare, yönetilen kimliklere erişim, gizli dizi Azure Key Vault erişimi, Azure depolama verilerine ve kaynaklarına erişim için Azure AD tabanlı kimlik doğrulaması ve yetkilendirme gibi önemli güvenlik geliştirmeleri için yeni depolama hesapları oluşturun. Mümkünse, Azure Resource Manager kullanmak için klasik dağıtım modelini kullanan mevcut depolama hesaplarını geçirin. Azure Resource Manager hakkında daha fazla bilgi için bkz. [Azure Resource Manager genel bakış](../../azure-resource-manager/management/overview.md). | - |
| Tüm depolama hesaplarınız için Gelişmiş tehdit korumasını etkinleştirin | Azure depolama için Gelişmiş tehdit koruması, depolama hesaplarına yönelik olağan dışı ve potansiyel olarak zararlı girişimleri algılayan ek bir güvenlik zekası katmanı sağlar. Güvenlik uyarıları, etkinlik gerçekleştiğinde Azure Güvenlik Merkezi 'nde tetiklenir ve ayrıca, şüpheli etkinliklerin ayrıntıları ve tehditleri araştırıp düzeltilmesi ile ilgili öneriler ile abonelik yöneticilerine e-posta aracılığıyla gönderilir. Daha fazla bilgi için bkz. [Azure depolama Için Gelişmiş tehdit koruması](../common/azure-defender-storage-configure.md). | [Evet](../../security-center/security-center-remediate-recommendations.md) |
| Paylaşılan erişim imzası (SAS) belirteçlerini yalnızca HTTPS bağlantılarıyla sınırla | İstemci kuyruk verilerine erişmek için SAS belirteci kullandığında HTTPS gerektirme, gizlice dinleme riskini en aza indirmenize yardımcı olur. Daha fazla bilgi için bkz. [paylaşılan erişim imzaları (SAS) kullanarak Azure depolama kaynaklarına sınırlı erişim verme](../common/storage-sas-overview.md). | - |

## <a name="identity-and-access-management"></a>Kimlik ve erişim yönetimi

| Öneri | Yorumlar | Güvenlik Merkezi |
|-|----|--|
| Kuyruk verilerine erişim yetkisi vermek için Azure Active Directory (Azure AD) kullanın | Azure AD, istekleri kuyruk depolamaya yetkilendirmek için paylaşılan anahtar yetkilendirmesi üzerinde üstün güvenlik ve kullanım kolaylığı sağlar. Daha fazla bilgi için bkz. [Azure Active Directory kullanarak Azure bloblarına ve kuyruklara erişim yetkisi verme](../common/storage-auth-aad.md). | - |
| Azure RBAC aracılığıyla bir Azure AD güvenlik sorumlusuna izin atarken en az ayrıcalık sorumlusunu göz önünde bulundurun | Bir Kullanıcı, Grup veya uygulamaya bir rol atarken, bu güvenlik sorumlusuna yalnızca onların görevlerini gerçekleştirmesi için gerekli olan izinleri verin. Kaynaklara erişimi kısıtlamak, verilerinizin yanlışlıkla ve kötü amaçlı olarak kötüye kullanımını önlemeye yardımcı olur. | - |
| Hesap erişim anahtarlarınızı Azure Key Vault ile güvenli hale getirin | Microsoft, Azure depolama 'ya istekleri yetkilendirmek için Azure AD kullanılmasını önerir. Ancak, paylaşılan anahtar yetkilendirmesi kullanmanız gerekiyorsa, hesap anahtarlarınızın Azure Key Vault sağlayın. Anahtar kasasındaki anahtarları uygulamanıza kaydetmek yerine çalışma zamanında alabilirsiniz. | - |
| Hesap anahtarlarınızı düzenli aralıklarla yeniden oluşturun | Hesap anahtarlarını döndürmek, verilerinizi kötü amaçlı aktörlere sunma riskini düzenli aralıklarla azaltır. | - |
| SAS 'ye izin atarken en az ayrıcalık sorumlusunu göz önünde bulundurun | SAS oluştururken, yalnızca istemcisinin işlevini gerçekleştirmesi için gerekli olan izinleri belirtin. Kaynaklara erişimi kısıtlamak, verilerinizin yanlışlıkla ve kötü amaçlı olarak kötüye kullanımını önlemeye yardımcı olur. | - |
| İstemcilere yaptığınız tüm SAS için bir iptal planı vardır | SAS tehlikeye girerse, bu SAS 'ı mümkün olan en kısa sürede iptal etmek isteyeceksiniz. Bir Kullanıcı temsilciliğini iptal etmek için, bu anahtarla ilişkili tüm imzaları hızlıca geçersiz kılmak üzere Kullanıcı temsili anahtarını iptal edin. Depolanan bir erişim ilkesiyle ilişkili bir hizmet SAS 'sini iptal etmek için, saklı erişim ilkesini silebilir, ilkeyi yeniden adlandırabilir veya süre sonu saatini geçmişteki bir zamana dönüştürebilirsiniz. Daha fazla bilgi için bkz. [paylaşılan erişim imzaları (SAS) kullanarak Azure depolama kaynaklarına sınırlı erişim verme](../common/storage-sas-overview.md).  | - |
| Bir hizmet SAS, depolanan bir erişim ilkesiyle ilişkilendirilmediği zaman aşımı süresini bir saat veya daha az olarak ayarlayın | Depolanan bir erişim ilkesiyle ilişkilendirilmemiş bir hizmet SAS iptal edilemez. Bu nedenle, süre sonu süresini kısıtlamak için SAS bir saat için geçerli veya daha az önerilir. | - |

## <a name="networking"></a>Ağ

| Öneri | Yorumlar | Güvenlik Merkezi |
|-|----|--|
| Depolama hesabı için gerekli minimum Aktarım Katmanı Güvenliği (TLS) sürümünü yapılandırın.  | İstemcilerin, bu hesap için en düşük TLS sürümünü yapılandırarak bir Azure depolama hesabına yönelik istekler yapması için daha güvenli bir TLS sürümü kullanmasını gerektir. Daha fazla bilgi için bkz. [depolama hesabı için gereken minimum Aktarım Katmanı Güvenliği (TLS) sürümünü yapılandırma](../common/transport-layer-security-configure-minimum-version.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)| - |
| Tüm depolama hesaplarınızda **Güvenli aktarım gerekli** seçeneğini etkinleştirin | **Güvenli aktarım gerekli** seçeneğini etkinleştirdiğinizde, depolama hesabında yapılan tüm isteklerin güvenli bağlantılar üzerinden gerçekleşmesi gerekir. HTTP üzerinden yapılan tüm istekler başarısız olur. Daha fazla bilgi için bkz. [Azure Storage 'da güvenli aktarım gerektir](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). | [Evet](../../security-center/security-center-remediate-recommendations.md) |
| Güvenlik duvarı kurallarını etkinleştirme | Depolama hesabınıza erişimi, belirtilen IP adreslerinden veya aralıklarından kaynaklanan isteklerle veya bir Azure sanal ağı 'ndaki (VNet) bir alt ağ listesinden oluşan isteklerle sınırlamak için güvenlik duvarı kurallarını yapılandırın. Güvenlik duvarı kurallarını yapılandırma hakkında daha fazla bilgi için bkz. [Azure Storage güvenlik duvarlarını ve sanal ağları yapılandırma](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). | - |
| Güvenilen Microsoft hizmetlerinin depolama hesabına erişmesine izin ver | İstekler bir Azure VNet içinde veya izin verilen ortak IP adreslerinden itibaren çalışan bir hizmetten kaynaklanmadığı takdirde, depolama hesabınız için Güvenlik Duvarı kurallarının etkinleştirilmesi, varsayılan olarak gelen istekleri engeller. Engellenen istekler diğer Azure hizmetlerinden, Azure portal, günlük ve ölçüm hizmetlerinden ve bu şekilde devam eder. Güvenilen Microsoft hizmetlerinin depolama hesabına erişmesine izin vermek için bir özel durum ekleyerek diğer Azure hizmetlerinden gelen isteklere izin verebilirsiniz. Güvenilen Microsoft Hizmetleri için bir özel durum ekleme hakkında daha fazla bilgi için bkz. [Azure Storage güvenlik duvarlarını ve sanal ağları yapılandırma](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).| - |
| Özel uç noktaları kullanma | Özel bir uç nokta, Azure VNet 'ten depolama hesabına özel bir IP adresi atar. Sanal ağınız ile depolama hesabı arasındaki tüm trafiğin özel bağlantı üzerinden güvenliğini sağlar. Özel uç noktalar hakkında daha fazla bilgi için bkz. [Azure özel uç noktası kullanarak bir depolama hesabına özel olarak bağlanma](../../private-link/tutorial-private-endpoint-storage-portal.md). | - |
| VNet hizmet etiketlerini kullanma | Hizmet etiketi, belirli bir Azure hizmetinden bir IP adresi önekleri grubunu temsil eder. Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir. Azure depolama tarafından desteklenen hizmet etiketleri hakkında daha fazla bilgi için bkz. [Azure hizmet etiketlerine genel bakış](../../virtual-network/service-tags-overview.md). Giden ağ kuralları oluşturmak için hizmet etiketlerinin nasıl kullanılacağını gösteren bir öğretici için bkz. [PaaS kaynaklarına erişimi kısıtlama](../../virtual-network/tutorial-restrict-network-access-to-resources.md). | - |
| Belirli ağlarla ağ erişimini sınırlandırma | Erişim gerektiren istemcileri barındıran ağlarla ağ erişimini kısıtlamak, kaynaklarınızın ağ saldırılarına maruz kalmasını azaltır. | [Evet](../../security-center/security-center-remediate-recommendations.md) |

## <a name="logging-and-monitoring"></a>Günlüğe kaydetme ve izleme

| Öneri | Yorumlar | Güvenlik Merkezi |
|-|----|--|
| İsteklerin nasıl yetkilendirildiğini izleme | Azure depolama 'ya karşı yapılan her isteği nasıl yetkilendirdiğini izlemek için Azure depolama günlüğü 'nü etkinleştirin. Günlükler, bir isteğin bir OAuth 2,0 belirteci kullanılarak, bir paylaşılan anahtar kullanılarak veya paylaşılan erişim imzası (SAS) kullanılarak mı adsız olarak yapılıp yapılmadığını belirtir. Daha fazla bilgi için bkz. [Azure Storage Analytics günlüğü](../common/storage-analytics-logging.md). | - |

## <a name="next-steps"></a>Sonraki adımlar

- [Azure güvenlik belgeleri](../../security/index.yml)
- [Güvenli Geliştirme belgeleri](../../security/develop/index.yml).
