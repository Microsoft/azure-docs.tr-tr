---
title: Azure DNS nedir?
description: Microsoft Azure 'de DNS barındırma hizmetine genel bakış. Etki alanınızı Microsoft Azure barındırın.
author: vhorne
ms.service: dns
ms.topic: overview
ms.date: 3/21/2019
ms.author: victorh
ms.openlocfilehash: 5055734ae3529d7fca87688c5f9410f38368a634
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959362"
---
# <a name="what-is-azure-dns"></a>Azure DNS nedir?

Azure DNS, Microsoft Azure altyapısını kullanarak ad çözümlemesi sağlayan DNS etki alanları için bir barındırma hizmetidir. Etki alanlarınızı Azure 'da barındırarak DNS kayıtlarınızı, diğer Azure hizmetlerinizle aynı kimlik bilgilerini, API 'Leri, araçları ve faturalandırmayı kullanarak yönetebilirsiniz.

Bir etki alanı adı satın almak için Azure DNS kullanamazsınız. Yıllık bir ücret karşılığında, [App Service](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain#buy-the-domain) etki alanı veya üçüncü taraf etki alanı adı Kaydedicisi kullanarak bir etki alanı adı satın alabilirsiniz. Etki alanlarınız, kayıt yönetimi için Azure DNS içinde barındırılabilir. Daha fazla bilgi için bkz. [Azure DNS etki alanı verme](dns-domain-delegation.md).

Aşağıdaki özellikler Azure DNS eklenmiştir.

## <a name="reliability-and-performance"></a>Güvenilirlik ve performans

Azure DNS 'deki DNS etki alanları, Azure 'un DNS ad sunucularının küresel ağında barındırılır. Azure DNS, her noktaya yayın ağını kullanır. Her DNS sorgusu, etki alanınız için hızlı performans ve yüksek kullanılabilirlik sağlamak üzere en yakın kullanılabilir DNS sunucusu tarafından yanıtlanır.

## <a name="security"></a>Güvenlik

 Azure DNS, şu gibi özellikler sağlayan Azure Resource Manager tabanlıdır:

* Kuruluşunuza yönelik belirli eylemlere kimlerin erişebileceğini denetlemek için [rol tabanlı erişim denetimi](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) .

* Kuruluşunuzdaki bir kullanıcının bir kaynağı nasıl değiştirtiğini ve sorun giderirken hata bulmasını izlemek için [etkinlik günlükleri](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) .

* Bir aboneliği, kaynak grubunu veya kaynağı kilitlemek için [kaynak kilitleme](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) . Kilitleme, kuruluşunuzdaki diğer kullanıcıların yanlışlıkla önemli kaynakları silmesini veya değiştirmesini önler.

Daha fazla bilgi için bkz. [DNS bölgelerini ve kayıtlarını koruma](dns-protect-zones-recordsets.md). 

## <a name="dnssec"></a>DNSSEC

Azure DNS şu anda DNSSEC 'yi desteklemiyor. Çoğu durumda, uygulamalarınızda HTTPS/TLS kullanarak DNSSEC gereksinimini sürekli olarak azaltabilirsiniz. DNSSEC, DNS bölgeleriniz için kritik bir gereksinimle karşılaşırsanız, üçüncü taraf DNS barındırma sağlayıcılarıyla bu bölgeleri barındırabilirsiniz.

## <a name="ease-of-use"></a>Kullanım kolaylığı

 Azure DNS, Azure hizmetlerinize yönelik DNS kayıtlarını yönetebilir ve dış kaynaklarınız için DNS sağlayabilir. Azure DNS, Azure portal tümleşiktir ve diğer Azure hizmetlerinize göre aynı kimlik bilgilerini, destek sözleşmesini ve faturalandırmayı kullanır. 

DNS faturalandırma, Azure 'da barındırılan DNS bölgesinin sayısını ve alınan DNS sorgularının sayısını temel alır. Fiyatlandırma hakkında daha fazla bilgi için bkz. [Azure DNS fiyatlandırması](https://azure.microsoft.com/pricing/details/dns/).

Etki alanlarınız ve kayıtlarınız Azure portal, Azure PowerShell cmdlet 'leri ve platformlar arası Azure CLı kullanılarak yönetilebilir. Otomatik DNS yönetimi gerektiren uygulamalar, REST API ve SDK 'Ları kullanarak hizmetle tümleştirilebilir.

## <a name="customizable-virtual-networks-with-private-domains"></a>Özel etki alanları içeren özelleştirilebilir sanal ağlar

Ayrıca, özel DNS etki alanlarını da destekler Azure DNS. Bu özellik, bugün kullanılabilen Azure tarafından sağlanan adlar yerine özel sanal ağlarınızda kendi özel etki alanı adlarınızı kullanmanıza olanak sağlar.

Daha fazla bilgi için bkz. [özel etki alanları için Azure DNS kullanma](private-dns-overview.md).

## <a name="alias-records"></a>Diğer ad kayıtları

Azure DNS diğer ad kayıt kümelerini destekler. Azure genel IP adresi, Azure Traffic Manager profili veya Azure Content Delivery Network (CDN) uç noktası gibi bir Azure kaynağına başvurmak için bir diğer ad kayıt kümesi kullanabilirsiniz. Temel alınan kaynağın IP adresi değişirse, diğer ad kayıt kümesi DNS çözümlemesi sırasında kendisini sorunsuz bir şekilde günceller. Diğer ad kayıt kümesi hizmet örneğine işaret eder ve hizmet örneği bir IP adresi ile ilişkilendirilir.

Ayrıca, artık tepesinde veya çıplak etki alanınızı bir diğer ad kaydı kullanarak bir Traffic Manager profile veya CDN uç noktasına ekleyebilirsiniz. Örnek olarak contoso.com.

Daha fazla bilgi için bkz. [Azure DNS diğer ad kayıtlarına genel bakış](dns-alias.md).

## <a name="next-steps"></a>Sonraki adımlar

* DNS bölgeleri ve kayıtları hakkında bilgi edinmek için bkz. [DNS bölgeleri ve kayıtlarına genel bakış](dns-zones-records.md).

* Azure DNS bir bölgenin nasıl oluşturulacağını öğrenmek için bkz. [DNS bölgesi oluşturma](./dns-getstarted-create-dnszone-portal.md).

* Azure DNS hakkında sık sorulan sorular için bkz. [Azure DNS SSS](dns-faq.md).

