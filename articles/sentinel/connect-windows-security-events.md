---
title: Windows Güvenliği olay verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Windows Güvenliği olay verilerini Azure Sentinel 'e bağlamayı öğrenin.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 34762d561edfa5b37b1841c55f3fff6a4b8337a3
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240513"
---
# <a name="connect-windows-security-events"></a>Windows güvenlik olaylarını bağlama 



Azure Sentinel çalışma alanınıza bağlı Windows sunucularından tüm güvenlik olaylarını akışla aktarabilirsiniz. Bu bağlantı, panoları görüntülemenizi, özel uyarılar oluşturmanızı ve araştırmanızı sağlar. Bu, kuruluşunuzun ağı hakkında daha ayrıntılı bilgi verir ve güvenlik işlemi yeteneklerini geliştirir.  Hangi olayların akışını seçebilirsiniz:

- **Tüm olaylar** -tüm Windows güvenliği ve AppLocker olayları.
- **Ortak** -denetim amaçlarıyla standart bir olay kümesi. Bu küme, tam Kullanıcı denetim izi içerir. Örneğin, bu küme hem Kullanıcı oturum açma hem de Kullanıcı oturumu kapatma olaylarını içerir (olay KIMLIĞI 4634). Güvenlik grubu değişikliklerini, anahtar etki alanı denetleyicisi Kerberos işlemleri ve sektör kuruluşlar tarafından önerilen diğer olaylar gibi eylemleri denetimi ekliyoruz.

Tüm olayları üzerine miktarının azaltılmasını ve belirli olay filtre için olduğunu seçin için ana motivasyon olarak ortak çok düşük bir birime sahip olayları dahil edilmişti.
- **Minimal** -olası tehditleri gösterebilen küçük bir olay kümesi. Bu seçeneği etkinleştirerek, tam denetim izinin olması mümkün olmayacaktır.  Bu küme, yalnızca başarılı bir ihlal ve çok düşük bir birimi olan önemli olayları gösterebilen olayları ele alır. Örneğin, bu küme kullanıcı başarılı ve başarısız oturum açma bilgilerini (olay kimlikleri 4624, 4625) içerir, ancak bu, denetim için önemli olmayan, algılama için anlamlı olan ve görece yüksek hacimde olan oturum kapatma bilgilerini içermez. Bu küme veri hacminin çoğu, oturum açma olayları ve işlem oluşturma olayıdır (olay KIMLIĞI 4688).
- **Hiçbiri** -güvenlik veya AppLocker olayı yok.

> [!NOTE]
> 
> - Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.
> - Azure Güvenlik Merkezi ve Azure Sentinel aynı çalışma alanında çalışıyorsa, güvenlik olayları Bağlayıcısı yalnızca Azure Güvenlik Merkezi 'nden veya Azure Sentinel 'ten bağlanabilir. Bu olayları Azure Sentinel 'ten yönetmek için, Azure Güvenlik Merkezi bağlantısını kesmeniz ve yalnızca Azure Sentinel 'e bağlamanız önerilir.


Aşağıdaki listede her bir küme için güvenlik ve uygulama dolabı olay kimliklerinin tamamen bir dökümü verilmiştir:

| Veri katmanı | Toplanan olay göstergeleri |
| --- | --- |
| En az | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Common | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

## <a name="set-up-the-windows-security-events-connector"></a>Windows güvenlik olayları bağlayıcısını ayarlama

Windows Güvenlik olaylarınızı Azure Sentinel ile tam olarak bütünleştirmek için:

1. Azure Sentinel portalında, **veri bağlayıcıları** ' nı seçin ve ardından **Windows güvenlik olayları** kutucuğuna tıklayın. 
1. Hangi veri türlerini akışa almak istediğinizi seçin.
1. Tıklayın **güncelleştirme**.
6. Windows güvenlik olayları için Log Analytics ilgili şemayı kullanmak için, **Securityevent**araması yapın.

## <a name="validate-connectivity"></a>Bağlantıyı doğrula

Günlüklerinizin Log Analytics görünmeye başlaması 20 dakika kadar sürebilir. 



## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, Windows Güvenlik olaylarını Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.

