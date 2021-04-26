---
title: Azure sözlüğü-Azure sözlüğü | Microsoft Docs
description: Azure platformunda bulut terminolojisini anlamak için Azure sözlüğünü kullanın. Bu kısa Azure sözlüğü, Azure için ortak bulut koşullarına yönelik tanımlar sağlar.
keywords: Azure sözlüğü, bulut terminolojisi, Azure sözlüğü, terminoloji tanımları, bulut koşulları
services: na
documentationcenter: na
author: MonicaRush
manager: jhubbard
editor: ''
ms.assetid: d7ac12f7-24b5-4bcd-9e4d-3d76fbd8d297
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: monicar
ms.openlocfilehash: 785c87e8136430557af819cd8c7ab1db8f54c060
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102507730"
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Microsoft Azure sözlüğü: Azure platformunda bulut terminolojisi sözlüğü

Microsoft Azure sözlüğü, Azure platformunun bulut terminolojisi için kısa bir sözlüktür. Ayrıca bkz:

* Azure hizmetlerinin ve AWS karşılıkları [Microsoft Azure ve Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) tanımları.<!-- I propose to link to https://azure.microsoft.com/services/ instead of this -->
* [Bulut bilgi işlem terimleri](https://azure.microsoft.com/overview/cloud-computing-dictionary/) -genel sektör bulutu koşulları.

## <a name="account"></a>account
Bir Azure aboneliğine erişmek ve bunları yönetmek için kullanılan bir hesap. Genellikle bir hesap şu şekilde olabilir: mevcut bir iş, okul veya kişisel Microsoft hesabı. [Ücretsiz denemeye](https://azure.microsoft.com)kaydolduğunuzda bir Azure aboneliğini yönetmek için de bir hesap oluşturabilirsiniz.  
[Oturum açmak için kullanabileceğiniz](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) [Microsoft 365 hesabı ve hesaplarınız ile bir Azure aboneliğine kaydolma](cost-management-billing/manage/microsoft-365-account-for-azure-subscription.md) konusuna bakın.

## <a name="api-app"></a>API uygulaması
[App Service uygulama](#app-service-app)için başka bir ad.

## <a name="app-service-app"></a>App Service uygulaması
[Azure App Service](app-service/overview.md) bir Web sitesi veya Web uygulaması, Web API 'si veya [mobil uygulama arka ucu](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop)barındırmak için sağladığı işlem kaynakları. App Service uygulamalar *uygulama hizmetleri*, *Web uygulamaları*, *API uygulamaları* ve *mobil uygulamalar* olarak da adlandırılır.

## <a name="availability-set"></a>kullanılabilirlik kümesi
Uygulama artıklığı ve güvenilirliği sağlamak için birlikte yönetilen bir sanal makine koleksiyonu. Kullanılabilirlik kümesi kullanımı, planlı veya plansız bir bakım olayı sırasında en az bir sanal makinenin kullanılabilir olmasını sağlar.  
Bkz [. Windows sanal makinelerinin kullanılabilirliğini yönetme](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ve [Linux sanal makinelerinin kullanılabilirliğini yönetme](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="azure-classic-deployment-model"></a><a name="classic-model"></a>Azure klasik dağıtım modeli
Azure 'da kaynak dağıtmak için kullanılan iki [dağıtım modelinden](./azure-resource-manager/management/deployment-models.md) biri (yeni model Azure Resource Manager). Bazı Azure hizmetleri yalnızca Kaynak Yöneticisi dağıtım modelini destekler, bazıları yalnızca klasik dağıtım modelini destekler ve her ikisini de destekler. Her Azure hizmeti için belgeler, hangi model destekler destekledikleri belirler.

## <a name="azure-command-line-interface-cli"></a><a name="cli"></a>Azure komut satırı arabirimi (CLı)
Windows, macOS ve Linux 'tan Azure hizmetlerini yönetmek için kullanılabilen bir komut satırı arabirimi.  Bazı hizmetler veya hizmet özellikleri yalnızca PowerShell veya CLı aracılığıyla yönetilebilir. Bkz. [Azure CLI](/cli/azure)

## <a name="azure-powershell"></a><a name="powershell"></a>Azure PowerShell
Windows bilgisayarlardan bir komut satırı aracılığıyla Azure hizmetlerini yönetmek için bir komut satırı arabirimi. Bazı hizmetler veya hizmet özellikleri yalnızca PowerShell veya CLı aracılığıyla yönetilebilir.
Bkz. [Azure PowerShell nasıl yüklenir ve yapılandırılır](/powershell/azure/)

## <a name="azure-resource-manager-deployment-model"></a><a name="arm-model"></a>Azure Resource Manager dağıtım modeli
Microsoft Azure içinde kaynak dağıtmak için kullanılan iki [dağıtım modelinden](./azure-resource-manager/management/deployment-models.md) biri (diğer klasik dağıtım modelidir). Bazı Azure hizmetleri yalnızca Kaynak Yöneticisi dağıtım modelini destekler, bazıları yalnızca klasik dağıtım modelini destekler ve her ikisini de destekler. Her Azure hizmeti için belgeler, hangi model destekler destekledikleri belirler.

## <a name="fault-domain"></a>hata etki alanı
Aynı anda başarısız olabilecek bir kullanılabilirlik kümesindeki sanal makinelerin toplanması. Bir raf içindeki ortak bir güç kaynağı ve ağ anahtarını paylaşan bir makine grubudur. Azure 'da, bir kullanılabilirlik kümesindeki sanal makineler birden çok hata etki alanı arasında otomatik olarak ayrılır.  
Bkz [. Windows sanal makinelerinin kullanılabilirliğini yönetme](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) veya [Linux sanal makinelerinin kullanılabilirliğini yönetme](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="geo"></a>Co
Genellikle iki veya daha fazla bölge içeren veri yerleşimi için tanımlanmış sınır. Sınırlar Ulusal kenarlıkların içinde veya ötesinde olabilir ve vergi Yönetmeliği etkilenebilir. Her coğrafi bölgede en az bir bölge vardır. Coğrafyalar örnekleri Asya Pasifik ve Japonya örnekleridir. *Coğrafya* da denir.  
Bkz. [Azure bölgeleri](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>coğrafi çoğaltma
Bir bölge çiftindeki Bloblar, tablolar ve kuyruklar gibi içeriği otomatik olarak çoğaltma işlemi.  
Bkz. [Azure SQL veritabanı Için Active Geo-Replication](./azure-sql/database/auto-failover-group-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>image
Herhangi bir sayıda sanal makine oluşturmak için kullanılabilen işletim sistemini ve uygulama yapılandırmasını içeren bir dosya. Azure 'da iki tür görüntü vardır: VM görüntüsü ve işletim sistemi görüntüsü. Bir VM görüntüsü, bir işletim sistemini ve görüntü oluşturulduğunda bir sanal makineye bağlı tüm diskleri içerir. Bir IŞLETIM sistemi görüntüsü yalnızca veri diski yapılandırmasına sahip olmayan genelleştirilmiş bir işletim sistemi içerir.  
Bkz. [PowerShell veya CLI Ile Azure 'Da Windows sanal makine görüntülerine gitme ve seçimi seçme](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>değerleri
Oluşturulabilen kaynak sayısı veya elde edilebilir performans kıyaslaması. Sınırlar genellikle abonelikler, hizmetler ve tekliflerle ilişkilendirilir.  
Bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="load-balancer"></a>yük dengeleyici
Bir ağdaki bilgisayarlar arasında gelen trafiği dağıtan bir kaynaktır. Azure 'da, yük dengeleyici trafiği bir yük dengeleyici kümesinde tanımlanan sanal makinelere dağıtır. [Yük dengeleyici](load-balancer/load-balancer-overview.md) internet 'e yönelik olabilir veya iç olabilir.  

## <a name="mobile-app"></a>Mobil uygulama
[App Service uygulama](#app-service-app)için başka bir ad.

## <a name="offer"></a>teklif
Fiyatlandırma, krediler ve bir Azure aboneliği için geçerli olan ilgili koşullar.  
Bkz. [Azure teklif ayrıntıları sayfası](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portal
Azure hizmetlerini dağıtmak ve yönetmek için kullanılan güvenli Web portalı.

## <a name="region"></a>region
Bir coğrafi alan içindeki ve bir veya daha fazla veri merkezi içeren bir alan. Fiyatlandırma, bölgesel hizmetler ve teklif türleri bölge düzeyinde sunulur. Bir bölge genellikle başka bir bölgeyle eşleştirilmiştir ve bu, birkaç yüz mil 'e kadar sürebilir. Bölgesel çiftler, olağanüstü durum kurtarma ve yüksek kullanılabilirlik senaryoları için bir mekanizma olarak kullanılabilir. *Konum* olarak da adlandırılır.  
Bkz. [Azure bölgeleri](best-practices-availability-paired-regions.md)

## <a name="resource"></a>kaynak
Azure çözümünüzün bir parçası olan bir öğe. Her Azure hizmeti, veritabanları veya sanal makineler gibi farklı türlerde kaynakları dağıtmanıza olanak sağlar.   
Bkz. [Azure Resource Manager genel bakış](azure-resource-manager/management/overview.md)

## <a name="resource-group"></a>kaynak grubu
Bir uygulama için ilgili kaynakları tutan Kaynak Yöneticisi bir kapsayıcı. Kaynak grubu bir uygulamanın tüm kaynaklarını veya yalnızca mantıksal olarak birlikte gruplanmış kaynakları içerebilir. Kuruluş için önemli olan faktörleri temel alarak kaynakları kaynak gruplarına nasıl ayıracağınızı belirleyebilirsiniz.  
Bkz. [Azure Resource Manager genel bakış](azure-resource-manager/management/overview.md)

## <a name="resource-manager-template"></a><a name="arm-template"></a>Resource Manager şablonu
Bir veya daha fazla Azure kaynağını bildirimli olarak tanımlayan ve dağıtılan kaynaklar arasındaki bağımlılıkları tanımlayan bir JSON dosyası. Şablon, kaynakları tutarlı ve sürekli olarak dağıtmak için kullanılabilir.  
Bkz. [yazma Azure Resource Manager şablonları](./azure-resource-manager/templates/template-syntax.md)

## <a name="resource-provider"></a>kaynak sağlayıcısı
Kaynak Yöneticisi aracılığıyla dağıtabileceğiniz ve yönetebileceğiniz kaynakları sağlayan bir hizmet. Her kaynak sağlayıcısı dağıtılan kaynaklarla çalışmaya yönelik işlemler sunar. Kaynak sağlayıcılarına Azure portal, Azure PowerShell ve birkaç programlama SDK 'Ları aracılığıyla erişilebilir.  
Bkz. [Azure Resource Manager genel bakış](azure-resource-manager/management/overview.md)

## <a name="role"></a>rol
Kullanıcılara, gruplara ve hizmetlere atanabilen erişimi denetlemek için bir yol. Roller, Azure kaynakları üzerinde oluşturma, yönetme ve okuma gibi eylemleri gerçekleştirebilir.  
Bkz [. RBAC: yerleşik roller](role-based-access-control/built-in-roles.md)

## <a name="service-level-agreement-sla"></a><a name="sla"></a>hizmet düzeyi sözleşmesi (SLA)
Microsoft 'un çalışma süresi ve bağlantı taahhütlerini açıklayan anlaşma. Her Azure hizmetinin belirli bir SLA 'sı vardır.  
Bkz. [hizmet düzeyi sözleşmeleri](https://azure.microsoft.com/support/legal/sla/)

## <a name="shared-access-signature-sas"></a><a name="sas"></a>paylaşılan erişim imzası (SAS)
Hesap anahtarınızı açığa çıkarmadan bir kaynağa sınırlı erişim vermenizi sağlayan bir imza. Örneğin, Azure depolama, blob 'lar gibi nesnelere istemci erişimi sağlamak için [SAS kullanır](./storage/common/storage-sas-overview.md) . IoT Hub, cihazları telemetri göndermek için izin vermek üzere [SAS kullanır](iot-hub/iot-hub-devguide-security.md#security-tokens) .

## <a name="storage-account"></a>depolama hesabı
Azure depolama 'da Azure blob, kuyruk, tablo ve dosya hizmetlerine erişmenizi sağlayan bir hesap. Depolama hesabı adı, Azure Storage veri nesneleri için benzersiz ad alanını tanımlar.  
Bkz. [Azure depolama hesapları hakkında](./storage/common/storage-account-create.md)

## <a name="subscription"></a>aboneliği
Microsoft 'un Azure hizmetlerini almasına olanak tanıyan bir müşterinin sözleşmesi. Abonelik fiyatlandırması ve ilgili şartlar, abonelik için seçilen teklif tarafından yönetilir.
Bkz. [Microsoft çevrimiçi abonelik sözleşmesi](https://azure.microsoft.com/support/legal/subscription-agreement/) ve [Azure abonelikleriyle ilişkili Azure Active Directory](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>etiket
Kaynakları yönetmek veya faturalandırmaya yönelik gereksinimlerinize göre sınıflandırmanızı sağlayan bir dizin oluşturma terimi. Karmaşık bir kaynak koleksiyonunuz olduğunda, bu varlıkları en anlamlı hale getiren şekilde görselleştirmek için Etiketler kullanabilirsiniz. Örneğin, kuruluşunuzda benzer görevleri üstlenen veya aynı departmana ait olan kaynakları etiketleyebilirsiniz.  
Bkz. [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](./azure-resource-manager/management/tag-resources.md)

## <a name="update-domain"></a>etki alanını güncelleştir
Aynı anda güncelleştirilmiş bir kullanılabilirlik kümesindeki sanal makinelerin toplanması. Aynı güncelleştirme etki alanındaki sanal makineler, planlı bakım sırasında birlikte yeniden başlatılır. Azure tek seferde birden çok güncelleştirme etki alanını yeniden başlatmaz. Yükseltme etki alanı da denir.  
Bkz [. Windows sanal makinelerinin kullanılabilirliğini yönetme](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ve [Linux sanal makinelerinin kullanılabilirliğini yönetme](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-machine"></a><a name="vm"></a>sanal makine
Bir işletim sistemi çalıştıran fiziksel bir bilgisayarın yazılım uygulamasıdır. Aynı donanımda birden çok sanal makine aynı anda çalışabilir. Azure 'da, sanal makineler çeşitli boyutlarda kullanılabilir.  
Bkz. [sanal makineler belgeleri](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="virtual-machine-extension"></a><a name="vm-extension"></a>sanal makine uzantısı
Diğer programların çalışmasına yardımcı olan veya çalışan bir bilgisayarla etkileşime girebilme olanağı sağlayan davranışlar veya özellikler uygulayan bir kaynaktır. Örneğin, bir Azure sanal makinesindeki uzaktan erişim değerlerini sıfırlamak veya değiştirmek için VM erişimi uzantısını kullanabilirsiniz.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
Bkz. [sanal makine uzantıları ve özellikleri (Windows)](./virtual-machines/extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) veya [sanal makine uzantıları ve özellikleri hakkında (Linux)](./virtual-machines/extensions/features-linux.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-network"></a><a name="vnet"></a>sanal ağ
Diğer tüm Azure kiracılarından yalıtılmış Azure kaynaklarınız arasında bağlantı sağlayan bir ağ. [Azure VPN Gateway](vpn-gateway/vpn-gateway-about-vpngateways.md) , sanal ağlar ve bir sanal ağ ile şirket içi ağ arasında bağlantı kurmanıza olanak tanır. IP adresi bloklarını, DNS ayarlarını, güvenlik ilkelerini ve bir sanal ağ içindeki yol tablolarını tam olarak denetleyebilirsiniz.  
Bkz. [sanal ağa genel bakış](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>Web uygulaması
[App Service uygulama](#app-service-app)için başka bir ad.

## <a name="see-also"></a>Ayrıca bkz.

* [Azure’ı kullanmaya başlama](https://azure.microsoft.com/get-started/)
* [Bulut kaynak merkezi](https://azure.microsoft.com/resources/)  
* [İş uygulamanız için Azure](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Veri merkezinizde Azure](https://azure.microsoft.com/overview/business-apps-on-azure/)