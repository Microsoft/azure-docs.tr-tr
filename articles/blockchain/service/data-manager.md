---
title: Azure blok zinciri hizmeti için blok zinciri Veri Yöneticisi nedir?
description: Blok zinciri, Event Grid konularına blok zinciri verilerini yakalayan, dönüştüren ve teslim eden Veri Yöneticisi.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: overview
ms.service: azure-blockchain
ms.reviewer: chroyal
ms.openlocfilehash: 94534d9b98f6c93d1d439775d3269df71f587ca4
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577496"
---
# <a name="what-is-blockchain-data-manager-for-azure-blockchain-service"></a>Azure blok zinciri hizmeti için blok zinciri Veri Yöneticisi nedir?

Blok Veri Yöneticisi zinciri, Azure hizmetleri ile güvenilir ve ölçeklenebilir blok zinciri ile tümleştirme sağlayan Azure Event Grid konularda Azure blok zinciri hizmeti işlem verilerini yakalar, dönüştürür ve sunar.

Çoğu kurumsal blok zinciri senaryosunda bir blok zinciri defteri bir çözümün bir parçasıdır. Örneğin, bir varlığı bir varlıktan diğerine aktarmak için, işlemi göndermek için bir mekanizmaya ihtiyacınız vardır. Daha sonra işlemin gerçekleşdiğine, kabul edildiğinden ve sonuçta elde edilen durum değişikliklerinin uçtan uca çözümünüzle tümleştirileceği için, defter verilerini okumak için bir mekanizmaya ihtiyacınız vardır. Bu örnekte, varlıkları aktarmak için bir akıllı sözleşme yazarsanız, zincir dışı uygulamaları ve veri depolarını bütünleştirmek için Blockzincirini Veri Yöneticisi kullanabilirsiniz. Varlık aktarımı örneği için, blok zincirinde bir varlık aktarıldığında, olaylar ve özellik durumu değişiklikleri blok zinciri tarafından Event Grid aracılığıyla alınır Veri Yöneticisi. Daha sonra blok zinciri verilerini zincirden depolamak veya gerçek zamanlı olarak durum değişikliklerine tepki vermek için Event Grid için birden çok olası olay işleyicisi kullanabilirsiniz.

Blok zinciri Veri Yöneticisi üç ana işlev gerçekleştirir: yakalama, dönüştürme ve sunma.

![Blok zinciri Veri Yöneticisi işlevleri](./media/data-manager/functions.png)

## <a name="capture"></a>Capture

Her blok zinciri Veri Yöneticisi örneği, bir Azure blok zinciri hizmeti üye işlem düğümüne bağlanır. Yalnızca işlem düğümüne erişimi olan kullanıcılar, müşteri verilerine doğru erişim denetimi sağlayan bir bağlantı oluşturabilir. Bir blok zinciri Veri Yöneticisi örneği, işlem düğümünden tüm ham blok ve ham işlem verilerini güvenilir bir şekilde yakalar ve kurumsal iş yüklerini destekleyecek şekilde ölçeklendirebilir.

## <a name="transform"></a>Dönüşüm

Blok zinciri Veri Yöneticisi içindeki akıllı sözleşme uygulamalarını yapılandırarak olay ve özellik durumunun kodunu çözmek için Blockzincirini Veri Yöneticisi kullanabilirsiniz. Akıllı sözleşme eklemek için sözleşme ABı ve bayt kodu sağlarsınız. Blok zinciri Veri Yöneticisi, anlaşma adreslerini çözmek ve çözmek için akıllı sözleşme yapıtlarını kullanır. Blok zinciri uygulamasını örneğe ekledikten sonra, blok Veri Yöneticisi zinciri akıllı sözleşme Consortium 'a dağıtıldığında akıllı sözleşme adresini dinamik olarak bulur ve yapılandırılan hedeflere kodu çözülmüş olay ve özellik durumunu gönderir.

## <a name="deliver"></a>Teslim etme

Blok zinciri Veri Yöneticisi, belirli bir blok zinciri Veri Yöneticisi örneği için birden çok Event Grid konu giden bağlantısını destekler. Blok zinciri verilerini tek bir hedefe gönderebilir veya birden çok hedefe blok zinciri verileri gönderebilirsiniz. Blok zinciri Veri Yöneticisi kullanarak, herhangi bir blok zinciri dağıtımı için ölçeklenebilir bir olay tabanlı veri yayımlama çözümü oluşturabilirsiniz.

## <a name="configuration-options"></a>Yapılandırma seçenekleri

Blockzincirini, çözümünüzün ihtiyaçlarını karşılayacak şekilde yapılandırabilirsiniz Veri Yöneticisi. Örneğin, şunları sağlayabilirsiniz:

* Azure blok zinciri hizmeti üyesi için tek bir blok zinciri Veri Yöneticisi örneği.
* Azure blok zinciri hizmeti işlem düğümü başına bir blok zinciri Veri Yöneticisi örneği. Örneğin, özel işlem düğümleri, gizliliği korumak için kendi blok zinciri Veri Yöneticisi örneğine sahip olabilir.
* Bir blok zinciri Veri Yöneticisi örneği, birden çok çıkış bağlantısını destekleyebilir. Bir blok zinciri Veri Yöneticisi örneği, bir Azure blok zinciri hizmeti üyesine yönelik tüm veri yayımlama tümleştirme noktalarını yönetmek için kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

Azure blok zinciri hizmeti üyesi için [bir blok zinciri veri Yöneticisi örneği oluşturmayı](data-manager-portal.md) deneyin.
