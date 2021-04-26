---
title: Azure Veri Paylaşımı için güvenliğe genel bakış
description: Azure Veri Paylaşımı için güvenliğe genel bakış
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: 4e62645dd5a7a8336df4fccf12daebc730a91168
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97678434"
---
# <a name="security-overview-for-azure-data-share"></a>Azure Veri Paylaşımı için güvenliğe genel bakış

Bu makalede, Azure veri paylaşma hizmetine yönelik bir güvenliğe genel bakış sunulmaktadır.

## <a name="security-overview"></a>Güvenliğe genel bakış

Azure veri paylaşımında, Azure 'un bekleyen ve aktarım sırasında verileri korumak için sunduğu temel güvenlik yararlanır. Veriler, temel alınan veri deposu tarafından desteklenerek Rest 'de şifrelenir. Ayrıca, veriler TLS 1,2 kullanılarak iletimde şifrelenir. Veri paylaşımıyla ilgili meta veriler de REST ve aktarım sırasında şifrelenir. Azure veri paylaşım, paylaşılmakta olan müşteri verilerinin içeriğini depolamaz.

Azure veri paylaşımı, veri paylaşımı için kullanılmakta olan veri depolarına erişmek için yönetilen kimliğin (daha önce MSI olarak bilinirdi) yararlanır. Veri sağlayıcısı ve veri tüketicisi arasında kimlik bilgileri değişimi yoktur. Yönetilen kimlik hakkında daha fazla bilgi için bkz. [Azure kaynakları Için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). Veri paylaşmak için gereken roller ve izinler hakkında daha fazla bilgi için, [Roller ve gereksinimlere](concepts-roles-permissions.md)bakın.

## <a name="access-control"></a>Erişim denetimi

Azure veri paylaşımında erişim denetimleri, yetkilendirilmiş olanlar tarafından erişilebildiğinden emin olmak için veri paylaşımının kaynak düzeyinde ayarlanabilir. Veri Paylaşımı kaynağının sahibi ve katılımcısı, verileri paylaşabilir, paylaşımlar alabilir ve var olan paylaşımlara değişiklik yapabilirler. Bir veri paylaşımı kaynağının okuyucu paylaşımları görüntüleyebilir, ancak değişiklik yapamaz. 

Bir paylaşma oluşturulduktan veya alındıktan sonra, veri paylaşma kaynağı için uygun izne sahip kullanıcılar değişiklik yapabilir. Bir paylaşma oluşturan veya alan bir kullanıcı kuruluştan ayrılırsa, verilerin paylaşılması veya akışı durdurulmaz. Veri paylaşma kaynağı için uygun izne sahip diğer kullanıcılar, bu paylaşımın yönetimine devam edebilir.

## <a name="share-data-from-or-to-data-stores-with-firewall-enabled"></a>Güvenlik Duvarı etkinken veri depolarından veya veri depolarında veri paylaşma
Güvenlik Duvarı açıkken veya depolama hesaplarına veri paylaşmak için depolama hesabınızda **Güvenilen Microsoft hizmetlerine Izin ver** ' i etkinleştirmeniz gerekir. Ayrıntılar için bkz. [Azure Storage güvenlik duvarlarını ve sanal ağları yapılandırma](
https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services) .


## <a name="next-steps"></a>Sonraki adımlar

Verileri paylaşmaya nasıl başlayacağınızı öğrenmek için [verilerinizi paylaşma](share-your-data.md) öğreticiye geçin.
