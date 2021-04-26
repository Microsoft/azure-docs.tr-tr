---
title: Azure 'da giden SMTP bağlantısı sorunlarını giderme | Microsoft Docs
description: E-posta göndermek için önerilen yöntemi ve Azure 'da giden SMTP bağlantısıyla ilgili sorunları nasıl giderebileceğinizi öğrenin.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: genli
ms.openlocfilehash: c28790b2ef423a3d0f996d7c6030b04198756eb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102607620"
---
# <a name="troubleshoot-outbound-smtp-connectivity-problems-in-azure"></a>Azure'da giden SMTP bağlantı sorunlarını giderme

15 Kasım 2017 tarihinden itibaren, bir sanal makineden (VM) doğrudan dış etki alanlarına (outlook.com ve gmail.com gibi) gönderilen giden e-posta iletileri yalnızca Azure 'daki belirli Abonelik türleri için kullanılabilir hale getirilir. TCP bağlantı noktası 25 kullanan giden SMTP bağlantıları engellendi. (Bağlantı noktası 25, genellikle kimliği doğrulanmamış e-posta teslimi için kullanılır.)

Bu davranış değişikliği yalnızca 15 Kasım 2017 ' den sonra oluşturulan abonelikler ve dağıtımlar için geçerlidir.

## <a name="recommended-method-of-sending-email"></a>E-posta göndermek için önerilen yöntem

Azure VM 'lerinden veya Azure App Service 'den e-posta göndermek için kimliği doğrulanmış SMTP geçiş hizmetlerini kullanmanızı öneririz. (Bu geçiş Hizmetleri genellikle TCP bağlantı noktası 587 üzerinden bağlanır, ancak diğer bağlantı noktalarını destekler.) Bu hizmetler, üçüncü taraf e-posta sağlayıcılarının iletileri reddetme olasılığını en aza indirmek için IP veya etki alanı saygınlığını sürdürmek üzere kullanılır. [SendGrid](https://sendgrid.com/partners/azure/) , bu tür bir SMTP geçiş hizmetidir, ancak diğerleri vardır. Ayrıca, şirket içinde kullanabileceğiniz güvenli bir SMTP geçiş hizmetiniz de olabilir.

Bu e-posta teslim hizmetlerinin kullanılması, abonelik türünden bağımsız olarak Azure 'da kısıtlı değildir.

## <a name="enterprise-agreement"></a>Kurumsal Anlaşma

Kurumsal Anlaşma Azure kullanıcıları için, kimlik doğrulamalı geçiş kullanmadan e-posta gönderme teknik özelliğinin bir değişikliği yoktur. Hem yeni hem de mevcut Kurumsal Anlaşma kullanıcılar Azure platformundan herhangi bir kısıtlama olmadan doğrudan Azure VM 'lerinden giden e-posta teslimini doğrudan dış e-posta sağlayıcılarına deneyebilir. E-posta sağlayıcılarının belirli bir kullanıcıdan gelen e-postayı kabul edeceğini garanti edilmez. Ancak Azure platformu, Kurumsal Anlaşma abonelikler içindeki VM 'Lere yönelik teslim girişimlerini engellemez. Herhangi bir ileti teslimini veya belirli sağlayıcıları içeren istenmeyen posta filtreleme sorunlarını gidermek için doğrudan e-posta sağlayıcılarıyla çalışmanız gerekir.

## <a name="pay-as-you-go"></a>Kullandıkça öde

15 Kasım 2017 tarihinden önce kaydolduysanız, Kullandıkça Öde aboneliği için, teknik bir e-posta teslimini deneyebilmeniz için hiçbir değişiklik olmayacaktır. Azure platformundan hiçbir kısıtlama olmadan bu aboneliklerdeki Azure VM 'lerinden gelen e-posta teslimini doğrudan dış e-posta sağlayıcılarına deneyebileceksiniz. Yine de, e-posta sağlayıcılarının belirli bir kullanıcıdan gelen e-postayı kabul edeceğini garanti edilmez. Tüm ileti teslimini veya belirli sağlayıcıları içeren istenmeyen posta filtreleme sorunlarını gidermek için kullanıcıların e-posta sağlayıcılarıyla doğrudan çalışması gerekir.

15 Kasım 2017 ' den sonra oluşturulan Kullandıkça Öde abonelikleri için, abonelikler içindeki VM 'lerden doğrudan gönderilen e-postayı engelleyen teknik kısıtlamalar olacaktır. Azure VM 'lerinden doğrudan dış e-posta sağlayıcılarına (kimliği doğrulanmış SMTP geçişi kullanmadan) e-posta gönderebilmek istiyorsanız ve bir hesap ödeme geçmişiyle iyi bir şekilde devam ediyorsanız, kısıtlamanın kaldırılmasını isteyebilirsiniz. Bunu, Azure portal bir Azure sanal ağ kaynağı için **Tanılama ve çözme** dikey penceresinin **bağlantı** bölümünde yapabilirsiniz. İsteğiniz kabul edilirse, aboneliğiniz etkinleştirilir veya sonraki adımlar için yönergeler alırsınız. 

Kullandıkça Öde aboneliği dışarıda bırakılır ve VM 'Ler Azure portal durdurulduğunda ve yeniden başlatıldıktan sonra, Bu abonelikteki tüm VM 'Ler ileri doğru tutulur. Muafiyet yalnızca, istenen abonelik ve yalnızca doğrudan internet 'e yönlendirilen VM trafiği için geçerlidir.

> [!NOTE]
> Microsoft, hizmet koşulları ihlali oluştuğunu tespit ettiyse bu muafiyetleri iptal etme hakkını saklı tutar.

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-visual-studio-and-free-trial"></a>MSDN, Azure Pass, Open ile Azure, eğitim, öğrenciler için Azure, Visual Studio ve ücretsiz deneme

15 Kasım 2017 ' den sonra aşağıdaki abonelik türlerinden birini oluşturduysanız, abonelik içindeki VM 'lerden doğrudan e-posta sağlayıcılarına gönderilen e-postayı engelleyen teknik kısıtlamalara sahip olursunuz:
- MSDN
- Azure Pass
- Open ile Azure
- Eğitim
- Öğrenciler için Azure
- Ücretsiz Deneme
- Herhangi bir Visual Studio aboneliği  

Kötüye kullanımı engellemek için kısıtlamalar uygulanır. Bu kısıtlamaları kaldırma istekleri verilmez.

Bu abonelik türlerini kullanıyorsanız, bu makalenin önceki kısımlarında açıklandığı gibi SMTP geçiş hizmetlerini kullanmanızı veya abonelik türünü değiştirmenizi öneririz.

## <a name="cloud-solution-provider"></a>Bulut çözümü sağlayıcısı

Azure kaynaklarını bir bulut çözümü sağlayıcısı aracılığıyla kullanıyorsanız, Azure portal bir sanal ağ kaynağı için **Tanılama ve çözme** bölmesinin **bağlantı** bölümünde kısıtlamayı kaldırmak için bir istek yapabilirsiniz. İsteğiniz kabul edilirse, aboneliğiniz etkinleştirilir veya sonraki adımlar için yönergeler alırsınız.

## <a name="microsoft-partner-network-bizspark-plus-or-azure-sponsorship"></a>Microsoft İş Ortağı Ağı, BizSpark Plus veya Azure Sponsorluğu

15 Kasım 2017 ' den sonra oluşturulan aşağıdaki türlerin abonelikleri için, abonelikler içindeki VM 'lerden doğrudan gönderilen e-postayı engelleyen teknik kısıtlamalar olacaktır:

- Microsoft İş Ortağı Ağı (MPN)
- BizSpark Plus
- Azure Sponsorluğu

Azure VM 'lerinden doğrudan dış e-posta sağlayıcılarına (kimliği doğrulanmış SMTP geçişi kullanmadan) e-posta gönderebilmek istiyorsanız, aşağıdaki sorun türünü kullanarak bir destek talebi açarak bir istek yapabilirsiniz: **Teknik**  >  **sanal ağ**  >  **bağlantısı**  >  **e-posta gönderemez (SMTP/bağlantı noktası 25)**. Dağıtımınızın, kimliği doğrulanmış bir geçiş kullanmak yerine doğrudan posta sağlayıcılarına e-posta göndermek zorunda olduğuna ilişkin ayrıntıları eklediğinizden emin olun. İstekler, Microsoft 'un yanı sıra incelenip onaylanacaktır. İstekler, yalnızca ek antılanrat denetimleri tamamlandıktan sonra verilecek. 

Bir abonelik dışlandıktan ve VM 'Ler Azure portal durdurulduğunda ve yeniden başlatıldıktan sonra, Bu abonelikteki tüm VM 'Ler ileride tutulur. Muafiyet yalnızca, istenen abonelik ve yalnızca doğrudan internet 'e yönlendirilen VM trafiği için geçerlidir.

## <a name="changing-subscription-type"></a>Abonelik türü değiştiriliyor

Abonelik türünü veya bulut çözümü sağlayıcınızı veya kullandıkça öde aboneliğiniz onaylanmışsa, yeni ilkenin etkili olması için sanal makineyi durdurmanız, serbest bırakma ve sonra yeniden başlatmanız gerekir. Benzer şekilde, varsayılan olarak izin verilen bir abonelik türüne sahipseniz ve izin verilmeyen bir abonelik türüne geçiş yaparsanız, dağıtımdaki değişiklikler nedeniyle bağlantı noktası 25 daha sonra engellenebilir.

## <a name="need-help-contact-support"></a>Yardıma mı ihtiyacınız var? Desteğe başvurun

Hala yardıma ihtiyacınız varsa, sorununuzun hızla çözülmesini sağlamak için [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) . Bu sorun türünü kullanın: **Teknik**  >  **sanal ağ**  >  **bağlantısı**  >  **e-posta gönderemez (SMTP/bağlantı noktası 25)**.
