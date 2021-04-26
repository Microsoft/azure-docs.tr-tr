---
title: Azure DNS nedir?
description: Microsoft Azure'deki DNS barındırma hizmetine genel bakış. Etki alanınızı Microsoft Azure'da barındırın.
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 4/20/2021
ms.author: rohink
ms.openlocfilehash: a04e9aa2a0c342ff86b8f603c02a78952c55af88
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817657"
---
# <a name="what-is-azure-dns"></a>Azure DNS nedir?

Azure DNS, Microsoft Azure altyapısını kullanarak ad çözümlemesi sağlayan DNS etki alanları için bir barındırma hizmetidir. Etki alanlarınızı Azure'da barındırarak DNS kayıtlarınızı diğer Azure hizmetlerinde kullandığınız kimlik bilgileri, API’ler, araçlar ve faturalarla yönetebilirsiniz.

Azure DNS'yi kullanarak etki alanı adı satın alamazsınız. Yıllık bir ücret karşılığında, [App Service](../app-service/manage-custom-dns-buy-domain.md#buy-an-app-service-domain) etki alanı veya üçüncü taraf etki alanı adı Kaydedicisi kullanarak bir etki alanı adı satın alabilirsiniz. Ardından etki alanlarınızı kayıt yönetimi için Azure DNS'de barındırabilirsiniz. Daha fazla bilgi için bkz. [Bir etki alanını Azure DNS'ye devretme](dns-domain-delegation.md).

Azure DNS aşağıdaki özellikleri içerir.

## <a name="reliability-and-performance"></a>Güvenilirlik ve performans

Azure DNS içindeki DNS etki alanları Azure'un global DNS ad sunucusu ağında barındırılır. Azure DNS ağı her noktaya yayın özelliğine sahiptir. DNS sorguları en yakın DNS sunucusu tarafından yanıtlanır ve bu sayede etki alanınız için yüksek performans ve kullanılabilirlik sağlar.

## <a name="security"></a>Güvenlik

 Azure DNS, Azure Resource Manager'ı temel alır ve aşağıdaki gibi özellikler sunar:

* Kuruluşunuzda belirli eylemlere kimlerin erişebileceğini denetlemek için [Azure rol tabanlı erişim denetimi (Azure RBAC)](../azure-resource-manager/management/overview.md) .

* [Etkinlik günlükleri](../azure-resource-manager/management/overview.md): Sorun giderme sırasında bir hata bulmak veya kuruluşunuzdaki kullanıcının bir kaynağı nasıl değiştirdiğini izlemek için etkinlik günlüklerini kullanın.

* [Kaynak kilitleme](../azure-resource-manager/management/lock-resources.md): Bir aboneliği, kaynak grubunu veya kaynağı kilitlemenizi sağlar. Kilitleme işlemi kuruluşunuzdaki kullanıcıların kritik kaynakları yanlışlıkla silmesini veya değiştirmesini önler.

Daha fazla bilgi için bkz. [DNS bölgelerini ve kayıtlarını koruma](dns-protect-zones-recordsets.md). 

## <a name="dnssec"></a>DNSSEC

Azure DNS şu anda DNSSEC 'yi desteklemiyor. Çoğu durumda, uygulamalarınızda HTTPS/TLS kullanarak DNSSEC gereksinimini sürekli olarak azaltabilirsiniz. DNSSEC, DNS bölgeleriniz için kritik bir gereksinimle karşılaşırsanız, bu bölgeleri üçüncü taraf DNS barındırma sağlayıcılarıyla barındırabilirsiniz.

## <a name="ease-of-use"></a>Kullanım kolaylığı

 Azure DNS, Azure hizmetlerinizin DNS kayıtlarını yönetebilir ve Azure dışı kaynaklar için de DNS hizmeti sunabilir. Azure DNS, Azure portala tümleştirilmiştir ve diğer Azure hizmetlerinizle aynı kimlik bilgilerini, destek ekibini ve faturalandırma özelliklerini kullanır. 

DNS faturalarında, Azure’da barındırılan DNS bölgelerinin sayısı ve alınan DNS sorgularının sayısı temel alınır. Fiyatlandırma hakkında daha fazla bilgi için bkz. [Azure DNS fiyatlandırması](https://azure.microsoft.com/pricing/details/dns/).

Etki alanlarınızı ve kayıtlarınızı yönetmek için Azure portalı, Azure PowerShell cmdlet'lerini ve farklı platformlarda Azure CLI özelliklerini kullanabilirsiniz. Otomatik DNS yönetimi gerektiren uygulamalar, REST API ve SDK’ları kullanarak hizmetle tümleştirilebilir.

## <a name="customizable-virtual-networks-with-private-domains"></a>Özel etki alanlarıyla özelleştirilebilir sanal ağlar

Ayrıca, özel DNS etki alanlarını da destekler Azure DNS. Bu özellik, özel sanal ağlarınızda Azure tarafından sağlanan adların yerine özel etki alanı adlarınızı kullanmanızı sağlar.

Daha fazla bilgi için bkz. [Azure DNS'yi özel etki alanları için kullanma](private-dns-overview.md).

## <a name="alias-records"></a>Diğer ad kayıtları

Azure DNS, diğer adı kayıt kümelerini destekler. Azure genel IP adresi, Azure Traffic Manager profili veya Azure Content Delivery Network (CDN) uç noktası gibi bir Azure kaynağına başvurmak için bir diğer ad kayıt kümesi kullanabilirsiniz. Temel alınan kaynağın IP adresi değişirse, diğer ad kayıt kümesi DNS çözümlemesi sırasında kendisini sorunsuz bir şekilde güncelleştirir. Diğer ad kaydı kümesi, hizmet örneğini işaret eder ve hizmet örneği bir IP adresi ile ilişkilidir.

Ayrıca, artık tepesinde veya çıplak etki alanınızı bir diğer ad kaydı kullanarak bir Traffic Manager profile veya CDN uç noktasına ekleyebilirsiniz. Örneğin: contoso.com.

Daha fazla bilgi için bkz. [Azure DNS diğer ad kayıtlarına genel bakış](dns-alias.md).

## <a name="next-steps"></a>Sonraki adımlar

* DNS bölgeleri ve kayıtları hakkında bilgi edinmek için bkz. [DNS bölgeleri ve kayıtlarına genel bakış](dns-zones-records.md).

* Azure DNS'de bölge oluşturmayı öğrenmek için bkz. [DNS bölgesi oluşturma](./dns-getstarted-portal.md).

* Azure DNS hakkında sık sorulan sorular için bkz. [Azure DNS Hakkında SSS](dns-faq.md).
