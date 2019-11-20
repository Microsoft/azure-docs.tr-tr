---
title: Azure Faturalama API’leri ile Azure kullanım bilgilerini alma | Microsoft Docs
description: Azure kaynak tüketimi ve eğilimlerine ilişkin içgörüler sağlamak için kullanılan Azure Faturalama Kullanımı ve RateCard API’leri hakkında bilgi edinin.
services: ''
documentationcenter: ''
author: tonguyen
manager: mumami
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/01/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: f8a70c5cf233212412261eb48c85ae3a1dd6c94d
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718571"
---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Azure kullanımınıza ilişkin program aracılığıyla içgörü elde etmek için Azure Faturalama API’lerini kullanın
Kullanım ve kaynak verilerini tercih ettiğiniz veri analizi aracına almak için Azure Faturalama API'lerini kullanın. Azure Kaynak Kullanımı ve RateCard API'leri maliyetlerinizi doğru tahmin etmenize ve yönetmenize yardımcı olabilir. API’ler Kaynak Sağlayıcısı olarak ve Azure Resource Manager tarafından kullanıma sunulan API ailesi kapsamında uygulanır.  

## <a name="azure-invoice-download-api-preview"></a>Azure Fatura İndirme API’si (Önizleme)
[Kabul etme süreci tamamlandıktan](billing-manage-access.md#opt-in) sonra, [Fatura API’sinin](/rest/api/billing) önizleme sürümünü kullanarak faturaları indirin. Özellikler şunlardır:

* **Azure Rol Tabanlı Erişim Denetimi**: Hangi kullanıcıların veya uygulamaların, aboneliğin kullanım verilerine erişim elde edebileceğini belirtmek için [Azure portalından](https://portal.azure.com) veya [Azure PowerShell cmdlet’leri](/powershell/azure/overview) aracılığıyla erişim ilkelerini yapılandırın. Çağıranlar, kimlik doğrulaması için standart Azure Active Directory belirteçlerini kullanmalıdır. Belirli bir Azure aboneliğine ilişkin kullanım verilerine erişim elde etmek için çağıranı Faturalama Okuyucusu, Okuyucu, Sahip veya Katkıda Bulunan rolüne ekleyin.
* **Tarih Filtreleme**: Fatura dönemi bitiş tarihine kadar tüm faturaları ters kronolojik sırayla almak için `$filter` parametresini kullanın.

> [!NOTE]
> Bu özellik ilk önizleme sürümündedir ve ileride yapılacak değişiklikler geriye dönük uyumluluk sağlamayabilir. Şu anda belirli abonelik tekliflerinde (EA, CSP, AIO desteklenmez) ve Azure Almanya'da kullanım dışıdır.

## <a name="azure-resource-usage-api-preview"></a>Azure Kaynak Kullanımı API’si (Önizleme)
Azure [Kaynak Kullanım API’si](/previous-versions/azure/reference/mt219003(v=azure.100)) ile tahmini Azure tüketim verilerinizi öğrenebilirsiniz. API şunları içerir:

* **Azure Rol Tabanlı Erişim Denetimi**: Hangi kullanıcıların veya uygulamaların, aboneliğin kullanım verilerine erişim elde edebileceğini belirtmek için [Azure portalından](https://portal.azure.com) veya [Azure PowerShell cmdlet’leri](/powershell/azure/overview) aracılığıyla erişim ilkelerini yapılandırın. Çağıranlar, kimlik doğrulaması için standart Azure Active Directory belirteçlerini kullanmalıdır. Belirli bir Azure aboneliğine ilişkin kullanım verilerine erişim elde etmek için çağıranı Faturalama Okuyucusu, Okuyucu, Sahip veya Katkıda Bulunan rolüne ekleyin.
* **Saatlik veya Günlük Toplamalar**: Çağıranlar, Azure kullanım verilerinin saatlik demetler halinde mi yoksa günlük demetler halinde mi istediğini belirtebilir. Varsayılan değer günlük değeridir.
* **Örnek meta verileri (kaynak etiketlerini içerir)** : Tam kaynak URI’si (/subscriptions/{subscription-id}/..), kaynak grubu bilgileri ve kaynak etiketleri gibi örnek düzeyinde ayrıntıları alın. Bu meta veriler, belirlenimci şekilde ve program aracılığıyla çapraz ücretlendirme gibi kullanım senaryoları için etiketlere göre kullanımı ayırmanıza yardımcı olur.
* **Kaynak meta verileri**: Ölçüm adı, ölçüm kategorisi, ölçüm alt kategorisi, birim ve bölge gibi kaynak ayrıntıları, çağıranın nelerin kullanıldığını daha iyi anlamasını sağlar. Ayrıca deneyimler genelinde verilerin bağıntısını oluşturmanıza yardımcı olması için Azure portalı, Azure kullanım CSV’si, EA faturalama CSV’si ve diğer kamuya açık deneyimler arasında kaynak meta verileri terminolojisini hizalamak için de çalışıyoruz.
* **Farklı teklif türleri için kullanım**: [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-invoice#retrieve-usage-data-for-a-specific-subscription) dışında, Kullandıkça öde, MSDN, Parasal taahhüt, kredi ve EA gibi teklif türleri için kullanım verileri mevcuttur.

## <a name="azure-resource-ratecard-api-preview"></a>Azure Kaynak RateCard API’si (Önizleme)
[Azure Kaynak RateCard API’sini](/previous-versions/azure/reference/mt219005(v=azure.100)) kullanarak mevcut Azure kaynaklarının listesine ve her biri için tahmini fiyat bilgisine ulaşabilirsiniz. API şunları içerir:

* **Azure Rol Tabanlı Erişim Denetimi**: Hangi kullanıcıların veya uygulamaların RateCard verilerine erişim elde edebileceğini belirtmek için [Azure portalından](https://portal.azure.com) veya [Azure PowerShell cmdlet’leri](/powershell/azure/overview) aracılığıyla erişim ilkelerinizi yapılandırın. Çağıranlar, kimlik doğrulaması için standart Azure Active Directory belirteçlerini kullanmalıdır. Belirli bir Azure aboneliğine ilişkin kullanım verilerine erişim elde etmek için çağıranı Okuyucu, Sahip veya Katkıda Bulunan rolüne ekleyin.
* **Kullandıkça öde, MSDN, Parasal taahhüt ve Kredi teklifleri desteği (EA ve [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-pricelist#get-prices-by-using-the-azure-rate-card) desteklenmez)** : Bu API, Azure teklif düzeyinde fiyat bilgilerini sağlar.  Bu API’nin çağıranı, kaynak ayrıntılarını ve fiyatlarını almak için teklif bilgilerini iletmelidir. EA teklifleri, kayıt başına özelleştirilmiş fiyatlar içerdiğinden şu anda EA fiyatları sağlayamıyoruz.

## <a name="scenarios"></a>Senaryolar
Kullanım ve RateCard API’lerinin birleşimiyle mümkün olan bazı senaryolar aşağıda verilmiştir:

* **Ay boyunca Azure harcaması**: Ay boyunca bulut harcamalarınıza yönelik daha iyi içgörüler elde etmek için Kullanım ve RateCard API’leri birleşimini kullanın. Kullanım ve ücret tahminleri için saatlik ve günlük demetleri analiz edebilirsiniz.
* **Uyarılar ayarlama**: Tahmini bulut tüketimi ve ücretlerini öğrenmek ve kaynak tabanlı veya parasal tabanlı uyarılar ayarlamak için Kullanım ve RateCard API’lerini kullanın.
* **Fatura tahmini**: Tahmini tüketim ve bulut harcamalarınızı öğrenin ve hangi faturanın faturalama dönemi sonunda olduğunu tahmin etmek için makine öğrenimi algoritmaları uygulayın.
* **Ön tüketim maliyet analizi**: İş yüklerinizi Azure’a taşıdığınızda beklenen kullanımınız için ne kadar faturanız olacağını tahmin etmek için RateCard API’sini kullanın. Diğer bulutlarda veya özel bulutlarda mevcut iş yükleriniz varsa, Azure harcamasının daha iyi bir tahminini elde etmek için kullanımınızı Azure ücretleriyle de eşleyebilirsiniz. Bu tahmin size teklif yapma ve Kullandıkça Öde dışında Parasal taahhüt ve Kredi gibi farklı teklif türlerini karşılaştırma olanağı sağlar. Ayrıca API, bölgeye göre maliyet farklarını görmenizi sağlar ve dağıtım kararları almanıza yardımcı olması için bir olasılık analizi yapmanıza imkan tanır.
* **Olasılık analizi** -

  * İş yüklerinin başka bir bölgede mi yoksa Azure kaynağının başka bir yapılandırmasında mı çalıştırılmasının daha uygun maliyetli olduğunu belirleyebilirsiniz. Azure kaynak maliyetleri, kullandığınız Azure bölgesine bağlı olarak değişiklik gösterebilir.
  * Ayrıca Azure kaynağında başka bir Azure teklif türünün daha iyi bir fiyat sunup sunmadığını da belirleyebilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar
* GitHub’da kod örneklerine göz atın:
  * [Fatura API’si kod örneği](https://go.microsoft.com/fwlink/?linkid=845124)

  * [Kullanım API’si kod örneği](https://github.com/Azure-Samples/billing-dotnet-usage-api)

  * [RateCard API’si kod örneği](https://github.com/Azure-Samples/billing-dotnet-ratecard-api)

* Azure Resource Manager hakkında daha fazla bilgi edinmek için bkz [Azure Resource Manager’a Genel Bakış](../azure-resource-manager/resource-group-overview.md).
