---
title: Stream Analytics işlerini bir Azure sanal ağındaki (VNET) kaynaklara bağlama
description: Bu makalede, bir sanal ağ içindeki kaynaklarla bir Azure Stream Analytics işinin nasıl bağlanacağı açıklanır.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/04/2021
ms.custom: devx-track-js
ms.openlocfilehash: 4701cb4122b4196b08b2a427b34d49c7784b91a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878248"
---
# <a name="connect-stream-analytics-jobs-to-resources-in-an-azure-virtual-network-vnet"></a>Stream Analytics işlerini bir Azure sanal ağındaki (VNet) kaynaklara bağlama

Stream Analytics işleriniz, verileri gerçek zamanlı olarak işlemek ve sonuç üretmek için giriş ve çıkış Azure kaynaklarınızla giden bağlantılar oluşturur. Bu giriş ve çıkış kaynakları (örneğin, Azure Event Hubs ve Azure SQL veritabanı) bir Azure Güvenlik duvarının arkasında veya bir Azure sanal ağında (VNet) olabilir. Stream Analytics hizmet, ağ kurallarınıza doğrudan dahil olmayan ağlardan çalışır.

Ancak, bu senaryolarda Stream Analytics işlerinizi giriş ve çıkış kaynaklarınıza güvenli bir şekilde bağlamaya yönelik iki yol vardır.
* Stream Analytics kümelerinde özel uç noktaları kullanma.
* Yönetilen kimlik kimlik doğrulama modu, ' güvenilen hizmetlere Izin ver ' ağ ayarıyla birlikte kullanılıyor.

Stream Analytics işiniz herhangi bir gelen bağlantıyı kabul etmiyor.

## <a name="private-endpoints-in-stream-analytics-clusters"></a>Stream Analytics kümelerindeki özel uç noktalar.
[Stream Analytics kümeler](./cluster-overview.md) , Stream Analytics işlerinizi çalıştırabileceğiniz tek bir kiracı adanmış işlem kümesidir. Stream Analytics kümenizde yönetilen özel uç noktalar oluşturabilirsiniz. Bu, kümenizde çalışan tüm işlerin giriş ve çıkış kaynaklarınıza güvenli bir giden bağlantı yapmasına olanak tanır.

Stream Analytics kümenizdeki özel uç noktaların oluşturulması [iki adımlı bir işlemdir](./private-endpoints.md). Bu seçenek, Stream Analytics kümesinin minimum boyutu 36 SUs olduğundan, orta ve büyük akış iş yükleri için idealdir (36 SUs, çeşitli aboneliklerde veya geliştirme, test ve üretim gibi ortamlarda farklı işler tarafından paylaşılabilir olmasına rağmen).

## <a name="managed-identity-authentication-with-allow-trusted-services-configuration"></a>' Güvenilen hizmetlere Izin ver ' yapılandırmasıyla yönetilen kimlik kimlik doğrulaması
Bazı Azure Hizmetleri, etkin olduğunda **güvenilir Microsoft Hizmetleri ağlarına Izin ver** ayarını sağlar, Stream Analytics işlerinizin güçlü kimlik doğrulaması kullanarak verilerinize güvenli bir şekilde bağlanmasını sağlar. Bu seçenek, Stream Analytics kümesi ve özel uç noktaları gerekmeden işlerinizi giriş ve çıkış kaynaklarınıza bağlamanıza olanak tanır. İşinizi bu tekniği kullanacak şekilde yapılandırmak 2 adımlı bir işlemdir:
* Stream Analytics uygulamanızda giriş veya çıkış yapılandırırken yönetilen kimlik kimlik doğrulama modunu kullanın.
* Belirli Stream Analytics işleriniz, iş kaynaklarına sistem tarafından atanan yönetilen kimliğe bir Azure rolü atayarak hedef kaynaklarınıza açık erişim izni verin. 

**Güvenilen Microsoft hizmetlerinin erişimine Izin ver** ' i etkinleştirmek, hiçbir işe yönelik paket erişimi vermez. Bu, kaynaklarınıza güvenli şekilde hangi Stream Analytics işlerin erişebileceği hakkında tam denetim sağlar. 

İşleriniz bu tekniği kullanarak aşağıdaki Azure hizmetlerine bağlanabilir:
1. [BLOB depolama veya Azure Data Lake Storage 2.](./blob-output-managed-identity.md) -işinizin depolama hesabı, akış girişi veya çıkışı olabilir.
2. [Azure Event Hubs](./event-hubs-managed-identity.md) -işinizin akış girişi veya çıkışı olabilir.

İşlerinizin diğer giriş veya çıkış türlerine bağlanması gerekiyorsa, önce Stream Analytics ve ardından Azure Işlevleri 'ni kullanarak tercih ettiğiniz herhangi bir hedefe Event Hubs yazabilirsiniz. Stream Analytics 'ten VNet veya güvenlik duvarında güvenliği sağlanmış diğer çıkış türlerine doğrudan yazmak isterseniz, tek seçenek Stream Analytics kümelerinde özel uç noktaları kullanmaktır.

## <a name="next-steps"></a>Sonraki adımlar

* [Stream Analytics kümelerinde özel uç noktalar oluşturma ve kaldırma](./private-endpoints.md)
* [Yönetilen kimlik kimlik doğrulaması kullanarak VNet 'teki Event Hubs bağlanma](./event-hubs-managed-identity.md)
* [Yönetilen kimlik kimlik doğrulaması kullanarak VNet 'te blob depolamaya ve ADLS 2. bağlanma](./blob-output-managed-identity.md)