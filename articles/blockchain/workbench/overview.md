---
title: Azure blok zinciri çalışma ekranı önizlemesine genel bakış
description: Azure blok zinciri çalışma ekranı önizlemesine ve özelliklerine genel bakış.
ms.date: 05/22/2020
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: fbd6be3907dbd10b003d065dfb14031a0e378478
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87003165"
---
# <a name="what-is-azure-blockchain-workbench"></a>Azure Blockchain Workbench nedir?

Azure blok zinciri çalışma ekranı önizlemesi, iş süreçlerini ve verileri diğer kuruluşlarla paylaşmak üzere blok zinciri uygulamaları oluşturmanıza ve dağıtmanıza yardımcı olmak üzere tasarlanmış bir Azure hizmetleri ve özellikleri koleksiyonudur. Azure Blockchain Workbench, blok zinciri uygulamaları derlemeye ilişkin altyapı iskeleti sağlayarak geliştiricilerin iş mantığı ve akıllı sözleşmeler oluşturmaya odaklanmasına imkan tanır. Ayrıca genel geliştirme görevlerinin otomatikleştirilmesine yardımcı olacak birçok Azure hizmetini ve özelliğini tümleştirerek blok zinciri uygulamaları oluşturulmasını da kolaylaştırır.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="create-blockchain-applications"></a>Blok zinciri uygulamaları oluşturma

Blockchain Workbench ile yapılandırmayı kullanarak ve akıllı sözleşme kodu yazarak blok zinciri uygulamaları tanımlayabilirsiniz. Blok zinciri uygulama geliştirme aşamasına atlayabilir ve iskele derleme ve destekleyici hizmetler ayarlama yerine sözleşmenizi tanımlamaya ve iş mantığı yazmaya odaklanabilirsiniz.

## <a name="manage-applications-and-users"></a>Uygulamaları ve kullanıcıları yönetme

Azure Blockchain Workbench, blok zinciri uygulamalarını ve kullanıcılarını yönetmek için bir web uygulaması ve REST API’leri sağlar. Blockchain Workbench yöneticileri, uygulama erişimini yönetebilir ve kullanıcılarınızı uygulama rollerine atayabilir. Azure AD kullanıcıları otomatik olarak uygulamadaki üyelere eşlenir.

## <a name="integrate-blockchain-with-applications"></a>Blok zincirini uygulamalarla tümleştirme

Mevcut sistemlerle tümleştirmek için Blockchain Workbench REST API’lerini ve ileti tabanlı API’leri kullanabilirsiniz. API’ler, birden fazla dağıtılmış defter teknolojisinin, depolama ve veritabanı teklifinin değiştirilmesine veya kullanılmasına imkan tanıyacak bir arabirim sağlar.

Blok zinciri çalışma ekranı, bu blok zincirinin yerel API 'SI tarafından beklenen biçimde işlem oluşturmak için ileti tabanlı API 'sine gönderilen iletileri dönüştürebilir.  Workbench, işlemleri imzalayıp uygun blok zincirine yönlendirebilir. 

Workbench, aşağı akış tüketicilerine ileti göndermek için Service Bus ve Event Grid’e otomatik olarak olaylar sunar. Geliştiriciler, işlemleri desteklemek ve sonuçlara bakmak için bu ileti sistemlerinin herhangi biriyle tümleştirme gerçekleştirebilir.

## <a name="deploy-a-blockchain-network"></a>Blok zinciri ağını dağıtma

Azure Blockchain Workbench, Azure Resource Manager çözüm şablonuyla önceden yapılandırılmış bir çözüm olarak konsorsiyum blok zinciri ağının kurulumunu kolaylaştırır. Şablon, bir konsorsiyumu çalıştırmak için gereken tüm bileşenleri dağıtan, basitleştirilmiş dağıtım sağlar. Şu anda Blockchain Workbench, Ethereum’u desteklemektedir.

## <a name="use-active-directory"></a>Active Directory Kullan

Mevcut blok zinciri protokolleri ile blok zinciri kimlikleri, ağ üzerindeki bir adres olarak temsil edilir. Azure Blockchain Workbench, blok zinciri kimliğini bir Active Directory kimliğiyle ilişkilendirerek soyutlar ve böylece Active Directory kimlikleriyle kurumsal uygulamalar derlenmesini kolaylaştırır.

## <a name="synchronize-on-chain-data-with-off-chain-storage"></a>Zincir üzerindeki verileri, zincir dışı depolama ile eşitleme

Azure Blockchain Workbench, blok zinciri üzerindeki verileri otomatik olarak zincir dışı depolama ile eşitleyerek blok zinciri olaylarının ve verilerinin analiz edilmesini kolaylaştırır. Doğrudan blok zincirinden verileri ayıklamak yerine, SQL Server gibi zincir dışı veritabanı sistemlerini sorgulayabilirsiniz. Blok zinciri uzmanlığı, veri analizi görevleri gerçekleştiren son kullanıcılar için gerekli değildir.

## <a name="support-and-feedback"></a>Destek ve geri bildirim

Azure blok zinciri haberleri için Azure blok zinciri [blogu](https://azure.microsoft.com/blog/topics/blockchain/) ' nı ziyaret ederek, blok zinciri hizmeti tekliflerini ve Azure blok zinciri Mühendisliği ekibinin bilgilerini güncel tutun.

Ürün geri bildirimi sağlamak veya yeni özellikler istemek için, [blok zinciri Için Azure geri bildirim Forumu](https://aka.ms/blockchainuservoice)aracılığıyla bir fikir gönderin veya oylayın.

### <a name="community-support"></a>Topluluk desteği

Microsoft mühendisleri ve Azure blok zinciri topluluk uzmanlarıyla birlikte katılın.

* [Microsoft Q&Azure blok zinciri için soru sayfası çalışma ekranı](/answers/topics/azure-blockchain-workbench.html)
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench mimarisi](architecture.md)
