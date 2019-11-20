---
title: Azure Media Services hesapları ile Azure depolama hesapları | Microsoft Docs
description: Media Services hesabı oluştururken, bir Azure Depolama hesabı kaynağının adını sağlamanız gerekir. Belirtilen depolama hesabı, Media Services hesabınıza eklenir.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/01/2019
ms.author: juliako
ms.openlocfilehash: 5c5bfa224b87040f5142663e6adab01072c6e6ba
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619289"
---
# <a name="azure-storage-accounts"></a>Azure Depolama hesapları

Yönetmek, şifreleme, kodlama, çözümleme ve azure'da medya içeriği akışı başlatmak için bir Media Services hesabı oluşturmanız gerekir. Media Services hesabı oluştururken, bir Azure Depolama hesabı kaynağının adını sağlamanız gerekir. Belirtilen depolama hesabı, Media Services hesabınıza eklenir. 

Media Services hesabı ve onunla ilişkili tüm depolama hesaplarının aynı Azure aboneliğinde olması gerekir. Depolama hesapları Media Services hesabıyla aynı konumda ek gecikme süresi ve veri kullanım maliyetleri önlemek için önerilir

Tek bir **Birincil** depolama hesabınız olması gerekir, ancak Media Services hesabınızla ilişkili istediğiniz sayıda **İkincil** depolama hesabınız olabilir. Media Services, **General-purpose v2** (GPv2) veya **General-purpose v1** (GPv1) hesaplarını destekler. <br/>Yalnızca blob hesaplarının **Birincil** olmasına izin verilmez. 

Bu nedenle en son özellikler ve performans yararlanabilirsiniz GPv2 kullanmanızı öneririz. Depolama hesapları hakkında daha fazla bilgi için bkz. [Azure depolama hesabına genel bakışın](../../storage/common/storage-account-overview.md).

> [!NOTE]
> Etkin olarak kullanılmadığı içerik depolama maliyetlerini azaltmak için bir erişim katmanları kullanılabilir olsa da sık erişim katmanı Azure Media Services ile kullanmak için desteklenir.

Depolama hesabınız için seçebileceğiniz farklı SKU'ları vardır. Daha fazla bilgi için [depolama hesapları](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest). Depolama hesapları ile denemek istiyorsanız, kullanın `--sku Standard_LRS`. Ancak, üretim için bir SKU seçilmesi sırasında dikkate almanız gereken, `--sku Standard_RAGRS`, coğrafi çoğaltma için iş sürekliliği sağlar. 

## <a name="assets-in-a-storage-account"></a>Varlıkları bir depolama hesabı

Media Services v3 sürümünde depolama API'leri varlıklarına dosyaları karşıya yüklemek için kullanılır. Daha fazla bilgi için [varlıklar kavramı](assets-concept.md).

> [!Note]
> Media Services API'leri kullanmadan Media Services SDK'sı tarafından oluşturulan blob kapsayıcı içeriğini değiştirme denememeniz gerekir.
 
## <a name="storage-side-encryption"></a>Depolama tarafında şifreleme

Bekleyen veri varlıklarınızı korumanın varlıklar tarafından depolama tarafı şifrelemesi şifrelenmelidir. Aşağıdaki tabloda, depolama tarafı şifrelemesi Media Services v3 sürümünde nasıl çalıştığı gösterilmektedir:

|Şifreleme seçeneği|Açıklama|Media Services v3|
|---|---|---|
|Media Services'ı depolama şifrelemesi| Media Services tarafından yönetilen AES-256 şifreleme anahtarı|Desteklenmeyen<sup>(1)</sup>|
|[Bekleyen veriler için depolama hizmeti şifrelemesi](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Sunucu tarafı şifrelemesi, Azure Depolama tarafından sunulan anahtarı Azure tarafından veya müşteri tarafından yönetilen|Desteklenen|
|[Depolama istemci tarafı şifreleme](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure depolama, anahtar Kasası'nda müşteri tarafından yönetilen bir kiracı anahtarı tarafından sunulan istemci tarafı şifreleme|Desteklenmiyor|

<sup>1</sup> , Media Services v3 (AES-256 şifreleme) depolama şifrelemesi, yalnızca varlıklarınızı Media Services v2 ile oluşturulduğunda için geriye dönük uyumluluk desteklenir. Var olan depolama ile v3 çalışır anlamı varlıklar şifreli ancak yenilerini oluşturulmasına izin vermez.

## <a name="storage-account-errors"></a>Depolama hesabı hataları

Media Services hesabının durumunun "Bağlantı kesildi" olması, depolama erişim anahtarlarının değiştirilmesi nedeniyle hesabın bağlı depolama hesaplarından birine veya daha fazlasına erişim sahibi olmadığını gösterir. Media Services hesabındaki birçok görevin gerçekleştirilmesi için güncel depolama erişim anahtarlarına ihtiyaç duyulur.

Media Services hesabının bağlı depolama hesaplarına erişim sahibi olmamasına neden olabilecek temel senaryolar aşağıda verilmiştir. 

|Sorun|Çözüm|
|---|---|
|Media Services hesabı veya bağlı depolama hesapları ayrı aboneliklere geçirildi. |Depolama hesaplarını veya Media Services hesabını geçirerek ikisinin aynı abonelikte olmasını sağlayın. |
|Media Services hesabı, farklı bir abonelikteki bağlı depolama hesabını kullanıyor ve bu duruma eski Media Services hesaplarında izin veriliyordu. Tüm eski Media Services hesapları modern Azure Resource Manager (ARM) tabanlı hesaplara dönüştürüldü ve durumları "Bağlantı kesildi" şeklinde olacak. |Depolama hesabını veya Media Services hesabını geçirerek ikisinin aynı abonelikte olmasını sağlayın.|

## <a name="next-steps"></a>Sonraki adımlar

Media Services hesabınızla bir depolama hesabı ekleme konusunda bilgi almak için bkz: [hesap oluşturma](create-account-cli-quickstart.md).
