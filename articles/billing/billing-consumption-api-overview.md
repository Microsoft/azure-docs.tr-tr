---
title: Azure tüketim API'sine genel bakış | Microsoft Docs
description: Azure Tüketim API'lerinin Azure kaynaklarınızla ilgili maliyet ve kullanım verilerinize program aracılığıyla erişmenizi nasıl sağladığını öğrenin.
services: billing
documentationcenter: ''
author: bandersmsft
manager: dougeby
editor: ''
tags: billing
ms.assetid: 68825e85-de64-466d-b11a-8baffde836b5
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: f867cc954bcdf8cb2fc7ebf37ff19e52db485376
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719768"
---
# <a name="azure-consumption-api-overview"></a>Azure tüketim API'sine genel bakış 

Azure Tüketim API'leri, Azure kaynaklarınızla ilgili maliyet ve kullanım verilerinize program aracılığıyla erişmenizi sağlar. Bu API'ler şu anda yalnızca Enterprise Kayıtlarını ve Web Direct Aboneliklerini (birkaç özel durum dışında) desteklemektedir. API'ler diğer Azure abonelik türlerini desteklemek için sürekli güncelleştirilir.

Azure Tüketim API'leri şu verilere erişim sunar:
- Kurumsal ve Doğrudan Web Müşterileri 
    - Kullanım Ayrıntıları 
    - Market Ücretleri 
    - Rezervasyon Önerileri 
    - Rezervasyon Ayrıntıları 
    - Rezervasyon Özetleri 
- Yalnızca Kurumsal Müşteriler 
    - Fiyat listesi 
    - Bütçeler 
    - Bakiyeler 

## <a name="usage-details-api"></a>Kullanım Ayrıntıları API'si

Tüm Azure birinci taraf kaynaklarının ücret ve kullanım verilerini almak için Kullanım Ayrıntıları API'sini kullanın. Bilgiler şu anda her kaynak için ölçüm başına günde bir kez gösterilen kullanım ayrıntısı kayıtları biçimindedir. Bilgiler tüm kaynakların maliyetlerini toplamak veya belirli kaynakların maliyet / kullanım durumunu araştırmak için kullanılabilir. 

API şunları içerir:

-   **Ölçüm Düzeyi Tüketim Verileri**: Kullanım maliyeti, ücreti yayan ölçüm ve bu ücretin ait olduğu Azure kaynağı gibi verilere bakın. Tüm kullanım ayrıntısı kayıtları günlük bir demete eşlenir.
-   **Azure Rol Tabanlı Erişim Denetimi**: Hangi kullanıcıların veya uygulamaların aboneliğin kullanım verilerine erişim elde edebileceğini belirtmek için [Azure portalında](https://portal.azure.com) [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)'da veya [Azure PowerShell cmdlet’lerinde](https://docs.microsoft.com/powershell/azure/overview) erişim ilkelerini yapılandırın. Çağıranlar, kimlik doğrulaması için standart Azure Active Directory belirteçlerini kullanmalıdır. Belirli bir Azure aboneliğine ilişkin kullanım verilerine erişim elde etmek için çağıranı Faturalama Okuyucusu, Okuyucu, Sahip veya Katkıda Bulunan rolüne ekleyin. 
-   **Filtreleme**: Aşağıdaki filtreleri kullanarak API yanıt kümenizi daha küçük bir kullanım ayrıntısı kayıtları kümesine daraltın:
    - Kullanım sonu / Kullanım başlangıcı
    - Kaynak Grubu
    - Kaynak Adı
-   **Veri Toplama**: İfadeler uygulayarak kullanım ayrıntılarını etiketlere veya filtre özelliklerine göre toplamak için OData kullanın
-   **Farklı teklif türleri için kullanım**: Kullanım ayrıntısı bilgileri şu anda Enterprise ve Web Direct müşterilerine sağlanmaktadır.

Daha fazla bilgi için [Kullanım Ayrıntıları API'sinin](https://docs.microsoft.com/rest/api/consumption/usagedetails) teknik belirtimlerine bakın.

## <a name="marketplace-charges-api"></a>Market Ücretleri API'si

Tüm Market kaynaklarıyla (Azure üçüncü taraf teklifleri) ilgili ücret ve kullanım verilerini almak için Market Ücretleri API'sini kullanın. Bu veriler tüm Market kaynaklarının maliyetlerini toplamak veya belirli kaynakların maliyet / kullanım durumunu araştırmak için kullanılabilir. 

API şunları içerir:

-   **Ölçüm Düzeyi Tüketim Verileri**: Market kullanım maliyeti, ücreti yayan ölçüm ve bu ücretin ait olduğu kaynak gibi verilere bakın. Tüm kullanım ayrıntısı kayıtları günlük bir demete eşlenir.
-   **Azure Rol Tabanlı Erişim Denetimi**: Hangi kullanıcıların veya uygulamaların aboneliğin kullanım verilerine erişim elde edebileceğini belirtmek için [Azure portalında](https://portal.azure.com) [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)'da veya [Azure PowerShell cmdlet’lerinde](https://docs.microsoft.com/powershell/azure/overview) erişim ilkelerini yapılandırın. Çağıranlar, kimlik doğrulaması için standart Azure Active Directory belirteçlerini kullanmalıdır. Belirli bir Azure aboneliğine ilişkin kullanım verilerine erişim elde etmek için çağıranı Faturalama Okuyucusu, Okuyucu, Sahip veya Katkıda Bulunan rolüne ekleyin. 
-   **Filtreleme**: Aşağıdaki filtreleri kullanarak API yanıt kümenizi daha küçük bir market kayıtları kümesine daraltın:
    - Kullanım başlangıcı / Kullanım sonu
    - Kaynak Grubu
    - Kaynak Adı
-   **Farklı teklif türleri için kullanım**: Market bilgileri şu anda Enterprise ve Web Direct müşterilerine sağlanmaktadır.

Daha fazla bilgi için [Market Ücretleri API'sinin](https://docs.microsoft.com/rest/api/consumption/marketplaces) teknik belirtimlerine bakın.

## <a name="balances-api"></a>Bakiyeler API'si

Enterprise müşterileri bakiyeler, yeni satın almalar, Azure Market hizmeti ücretleri, düzeltmeler ve fazla kullanım ücretleri hakkındaki bilgilerin aylık özetini almak için Bakiyeler API'sini kullanabilir. Bu bilgileri geçerli faturalama dönemi veya geçmişe ait bir dönem için alabilirsiniz. Kuruluşlar el ile hesaplanan özet ücretleriyle karşılaştırma yapmak için bu verileri kullanabilir. Bu API kaynağa özgü bilgileri ve maliyetlerin toplam görünümünü sağlamaz. 

API şunları içerir:

-   **Azure Rol Tabanlı Erişim Denetimi**: Hangi kullanıcıların veya uygulamaların aboneliğin kullanım verilerine erişim elde edebileceğini belirtmek için [Azure portalında](https://portal.azure.com) [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)'da veya [Azure PowerShell cmdlet’lerinde](https://docs.microsoft.com/powershell/azure/overview) erişim ilkelerini yapılandırın. Çağıranlar, kimlik doğrulaması için standart Azure Active Directory belirteçlerini kullanmalıdır. Belirli bir Azure aboneliğine ilişkin kullanım verilerine erişim elde etmek için çağıranı Faturalama Okuyucusu, Okuyucu, Sahip veya Katkıda Bulunan rolüne ekleyin. 
-   **Yalnızca Enterprise Müşterileri** Bu API yalnızca EA müşterilerine sağlanır. 
    - Bu API'yi çağırmak için müşterilerin Kuruluş Yöneticisi izinleri olmalıdır 

Daha fazla bilgi için [Bakiyeler API'sinin](https://docs.microsoft.com/rest/api/consumption/balances) teknik belirtimlerine bakın.

## <a name="budgets-api"></a>Bütçeler API'si

Enterprise müşterileri bu API'yi kullanarak kaynaklar, kaynak grupları veya faturalama ölçümleri için maliyet veya kullanım bütçeleri oluşturabilir. Bu bilgiler belirlendikten sonra, kullanıcı tanımlı bütçe eşikleri aşıldığında bildirim almak için uyarılar yapılandırılabilir. 

API şunları içerir:

-   **Azure Rol Tabanlı Erişim Denetimi**: Hangi kullanıcıların veya uygulamaların aboneliğin kullanım verilerine erişim elde edebileceğini belirtmek için [Azure portalında](https://portal.azure.com) [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)'da veya [Azure PowerShell cmdlet’lerinde](https://docs.microsoft.com/powershell/azure/overview) erişim ilkelerini yapılandırın. Çağıranlar, kimlik doğrulaması için standart Azure Active Directory belirteçlerini kullanmalıdır. Belirli bir Azure aboneliğine ilişkin kullanım verilerine erişim elde etmek için çağıranı Faturalama Okuyucusu, Okuyucu, Sahip veya Katkıda Bulunan rolüne ekleyin. 
-   **Yalnızca Enterprise Müşterileri**: Bu API yalnızca EA müşterilerine sağlanır.
-   **Yapılandırılabilir Bildirimler**: Bütçeye uyulmadığında bildirilecek kullanıcıları belirtin.
-   **Kullanım ve Maliyet Tabanlı Bütçeler** - Senaryonuzun gereksinimlerine göre tüketime veya maliyete dayalı olarak bütçenizi oluşturun.
-   **Filtreleme**: Aşağıdaki yapılandırılabilir filtreleri kullanarak bütçenizi kaynakların daha küçük bir alt kümesine filtreleyin
    - Kaynak Grubu
    - Kaynak Adı
    - Ölçüm
-   **Yapılandırılabilir bütçe dönemleri**: Bütçenin ne sıklıkta sıfırlanacağını ve ne kadar süreyle geçerli olacağını belirtin.

Daha fazla bilgi için [Bütçeler API'sinin](https://docs.microsoft.com/rest/api/consumption/budgets) teknik belirtimlerine bakın.

## <a name="reservation-recommendations-api"></a>Rezervasyon Önerileri API'si

Ayrılmış VM Örnekleri satın alma önerileri almak için bu API'yi kullanın. Öneriler, müşterilerin beklenen maliyet tasarruflarını ve satın alma tutarlarını analiz etmesine olanak tanıyacak şekilde tasarlanmıştır. 

API şunları içerir:

-   **Azure Rol Tabanlı Erişim Denetimi**: Hangi kullanıcıların veya uygulamaların aboneliğin kullanım verilerine erişim elde edebileceğini belirtmek için [Azure portalında](https://portal.azure.com) [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)'da veya [Azure PowerShell cmdlet’lerinde](https://docs.microsoft.com/powershell/azure/overview) erişim ilkelerini yapılandırın. Çağıranlar, kimlik doğrulaması için standart Azure Active Directory belirteçlerini kullanmalıdır. Belirli bir Azure aboneliğine ilişkin kullanım verilerine erişim elde etmek için çağıranı Faturalama Okuyucusu, Okuyucu, Sahip veya Katkıda Bulunan rolüne ekleyin. 
-   **Filtreleme**: Öneri sonuçlarınızı ihtiyaçlarınıza uyarlamak için aşağıdaki filtreleri kullanın:
    - Kapsam
    - Geriye dönük zaman aralığı
-   **Farklı teklif türleri için rezervasyon bilgileri**: Rezervasyon bilgileri şu anda Enterprise ve Web Direct müşterilerine sağlanmaktadır.

Daha fazla bilgi için [Rezervasyon Önerileri API'sinin](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations) teknik belirtimlerine bakın.

## <a name="reservation-details-api"></a>Rezervasyon Ayrıntıları API'si

Daha önce satın alınmış VM rezervasyonlarıyla ilgili ayrılan tüketimle gerçekten kullanılan tüketim karşılaştırması gibi bilgileri görmek için Rezervasyon Ayrıntıları API'sini kullanın. Verileri VM ayrıntı düzeyinde görebilirsiniz. 

API şunları içerir:

-   **Azure Rol Tabanlı Erişim Denetimi**: Hangi kullanıcıların veya uygulamaların aboneliğin kullanım verilerine erişim elde edebileceğini belirtmek için [Azure portalında](https://portal.azure.com) [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)'da veya [Azure PowerShell cmdlet’lerinde](https://docs.microsoft.com/powershell/azure/overview) erişim ilkelerini yapılandırın. Çağıranlar, kimlik doğrulaması için standart Azure Active Directory belirteçlerini kullanmalıdır. Belirli bir Azure aboneliğine ilişkin kullanım verilerine erişim elde etmek için çağıranı Faturalama Okuyucusu, Okuyucu, Sahip veya Katkıda Bulunan rolüne ekleyin. 
-   **Filtreleme**: Aşağıdaki filtreleri kullanarak API yanıt kümenizi daha küçük bir rezervasyon kümesine daraltın:
    - Tarih aralığı
-   **Farklı teklif türleri için rezervasyon bilgileri**: Rezervasyon bilgileri şu anda Enterprise ve Web Direct müşterilerine sağlanmaktadır.

Daha fazla bilgi için [Rezervasyon Ayrıntıları API'sinin](https://docs.microsoft.com/rest/api/consumption/reservationsdetails) teknik belirtimlerine bakın.

## <a name="reservation-summaries-api"></a>Rezervasyon Özetleri API'si

Daha önce satın alınmış VM rezervasyonlarıyla ilgili toplamda ayrılan tüketimle gerçekten kullanılan tüketim karşılaştırması gibi toplam bilgilerini görmek için Rezervasyon Özetleri API'sini kullanın. 

API şunları içerir:

-   **Azure Rol Tabanlı Erişim Denetimi**: Hangi kullanıcıların veya uygulamaların aboneliğin kullanım verilerine erişim elde edebileceğini belirtmek için [Azure portalında](https://portal.azure.com) [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)'da veya [Azure PowerShell cmdlet’lerinde](https://docs.microsoft.com/powershell/azure/overview) erişim ilkelerini yapılandırın. Çağıranlar, kimlik doğrulaması için standart Azure Active Directory belirteçlerini kullanmalıdır. Belirli bir Azure aboneliğine ilişkin kullanım verilerine erişim elde etmek için çağıranı Faturalama Okuyucusu, Okuyucu, Sahip veya Katkıda Bulunan rolüne ekleyin. 
-   **Filtreleme**: Günlük ayrıntı düzeyini kullanırken sonuçları ihtiyaçlarınıza uyarlamak için aşağıdaki filtreyi kullanın:
    - Kullanım Tarihi
-   **Farklı teklif türleri için rezervasyon bilgileri**: Rezervasyon bilgileri şu anda Enterprise ve Web Direct müşterilerine sağlanmaktadır.
-   **Günlük ve aylık toplamalar**: Çağrıyı yapanlar rezervasyon özeti verilerini günlük veya aylık ayrıntı düzeyinde istediklerini belirtebilir.

Daha fazla bilgi için [Rezervasyon Özetleri API'sinin](https://docs.microsoft.com/rest/api/consumption/reservationssummaries) teknik belirtimlerine bakın.

## <a name="price-sheet-api"></a>Fiyat Listesi API'si
Enterprise müşterileri tüm ölçümlere ilişkin özel fiyatlandırmalarını almak için bu API'yi kullanabilir. Kuruluşlar bu verileri kullanım ayrıntılarıyla ve market kullanım bilgileriyle birlikte kullanarak, kullanım ve market verileri aracılığıyla maliyet hesaplamaları yapabilir. 

API şunları içerir:

-   **Azure Rol Tabanlı Erişim Denetimi**: Hangi kullanıcıların veya uygulamaların aboneliğin kullanım verilerine erişim elde edebileceğini belirtmek için [Azure portalında](https://portal.azure.com) [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)'da veya [Azure PowerShell cmdlet’lerinde](https://docs.microsoft.com/powershell/azure/overview) erişim ilkelerini yapılandırın. Çağıranlar, kimlik doğrulaması için standart Azure Active Directory belirteçlerini kullanmalıdır. Belirli bir Azure aboneliğine ilişkin kullanım verilerine erişim elde etmek için çağıranı Faturalama Okuyucusu, Okuyucu, Sahip veya Katkıda Bulunan rolüne ekleyin. 
-   **Yalnızca Enterprise Müşterileri**: Bu API yalnızca EA müşterilerine sağlanır. Web Direct müşterilerinin fiyatlandırmayı almak için RateCard API'sini kullanmaları gerekir. 

Daha fazla bilgi için [Fiyat Listesi API'sinin](https://docs.microsoft.com/rest/api/consumption/pricesheet) teknik belirtimlerine bakın.

## <a name="scenarios"></a>Senaryolar

Burada tüketim API'leriyle mümkün kılınan bazı senaryolar verilmiştir:

-   **Fatura Mutabakatı**: Microsoft beni doğru miktarda mı ücretlendirdi?  Faturam ne kadardır ve bunu kendim hesaplayabilir miyim?
-   **Çapraz Ücretlendirmeler**: Artık ne kadar ücretlendirildiğimi biliyorum, kuruluşumda kimin ödemesi gerekiyor?
-   **Maliyet İyileştirmesi**: Ne kadar ücretlendirildiğimi biliyorum. Azure'a yaptığım harcamadan nasıl daha iyi yararlanabilirim?
-   **Maliyet İzleme**: Zaman içinde Azure'a ne kadar harcama yaptığımı ve Azure'ı ne kadar kullandığımı görmek istiyorum. Eğilimler nelerdir? Durumumu nasıl daha iyi hale getirebilirim?
-   **Ay boyunca Azure harcaması**: Ay başından bugüne kadarki harcamam ne kadardır? Azure harcamamda ve/veya kullanımında herhangi bir düzeltme yapmak gerekiyor mu? Ay boyunca ne zaman Azure tüketimim en yüksek düzeye çıkıyor?
-   **Uyarıları ayarlama**: Bir bütçeye göre kaynak tabanlı tüketim veya parasal tabanlı uyarılar ayarlamak istiyorum.

## <a name="next-steps"></a>Sonraki Adımlar

- Azure Faturalama API'lerini kullanarak program aracılığıyla Azure kullanımınızla ilgili içgörü elde etme hakkında bilgi için bkz. [Azure Faturalama API'sine Genel Bakış](billing-usage-rate-card-overview.md).



