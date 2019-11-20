---
title: Azure 'da giden SMTP bağlantısı sorunlarını giderme | Microsoft Docs
description: Azure 'da giden SMTP bağlantısı sorunlarını giderme hakkında bilgi edinin.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: b8acb50978c5932fe6e6838be86b65c12a0984ac
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058947"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Azure 'da giden SMTP bağlantısı sorunlarını giderme

15 Kasım 2017 tarihinden itibaren, bir sanal makineden (VM) doğrudan dış etki alanlarına (outlook.com ve gmail.com) gönderilen giden e-posta iletileri yalnızca Microsoft Azure içindeki belirli Abonelik türleri için kullanılabilir hale getirilir. TCP bağlantı noktası 25 kullanan giden SMTP bağlantıları engellendi. (Bağlantı noktası 25 öncelikle kimliği doğrulanmamış e-posta teslimi için kullanılır.)

Bu davranış değişikliği, 15 Kasım 2017 ' den beri yalnızca yeni abonelikler ve Yeni dağıtımlar için geçerlidir.

## <a name="recommended-method-of-sending-email"></a>E-posta göndermek için önerilen yöntem
Azure VM 'lerinden veya Azure Uygulama hizmetlerinden e-posta göndermek için kimliği doğrulanmış SMTP geçiş hizmetlerini (genellikle TCP bağlantı noktası 587 veya 443 üzerinden bağlantı, ancak diğer bağlantı noktalarını da destekler) kullanmanızı öneririz. Bu hizmetler, üçüncü taraf e-posta sağlayıcılarının iletiyi reddetme olasılığını en aza indirmek için IP veya etki alanı saygınlığını sürdürmek üzere kullanılır. Bu tür SMTP geçiş Hizmetleri, [SendGrid](https://sendgrid.com/partners/azure/)'e dahil değildir ancak bunlarla sınırlı değildir. Ayrıca, şirket içi olarak çalışan ve kullanabileceğiniz güvenli bir SMTP geçiş hizmetiniz de mümkündür.

Bu e-posta teslim hizmetlerinin kullanılması, abonelik türünden bağımsız olarak Azure 'da kısıtlı değildir.

## <a name="enterprise-agreement"></a>Kurumsal Anlaşma
Kurumsal Anlaşma Azure kullanıcıları için, kimlik doğrulamalı geçiş kullanmadan e-posta gönderme teknik özelliğinin bir değişikliği yoktur. Hem yeni hem de mevcut Kurumsal Anlaşma kullanıcılar Azure platformundan herhangi bir kısıtlama olmadan doğrudan Azure VM 'lerinden giden e-posta teslimini doğrudan dış e-posta sağlayıcılarına deneyebilir. E-posta sağlayıcılarının belirli bir kullanıcıdan gelen e-postayı kabul edeceğini garanti etmese de, teslim denemeleri Kurumsal Anlaşma abonelikleri içindeki VM 'Ler için Azure platformu tarafından engellenmeyecektir. Herhangi bir ileti teslimini veya belirli sağlayıcıları içeren istenmeyen posta filtreleme sorunlarını gidermek için doğrudan e-posta sağlayıcılarıyla çalışmanız gerekir.

## <a name="pay-as-you-go"></a>Kullandıkça Öde
Kullandıkça Öde veya Microsoft İş Ortağı Ağı abonelik teklifleri için 15 Kasım 2017 tarihinden önce kaydolduysanız, giden e-posta teslimini deneyebilme özelliği hiçbir değişiklik olmayacaktır. Azure platformundan herhangi bir kısıtlama olmadan bu aboneliklerdeki Azure VM 'lerinden gelen e-posta teslimini doğrudan dış e-posta sağlayıcılarına deneyebileceksiniz. Bu, e-posta sağlayıcılarının belirli bir kullanıcıdan gelen e-postayı kabul edeceğini garanti etmez ve kullanıcıların herhangi bir ileti teslimini veya belirli sağlayıcıları içeren istenmeyen posta filtreleme sorunlarını gidermek için doğrudan e-posta sağlayıcılarıyla çalışması gerekecektir.

15 Kasım 2017 ' den sonra oluşturulan Kullandıkça Öde veya Microsoft İş Ortağı Ağı abonelikler için, bu abonelikler içindeki VM 'lerden doğrudan gönderilen e-postaları engelleyen teknik kısıtlamalar olacaktır. Azure VM 'lerinden doğrudan dış e-posta sağlayıcılarına (kimliği doğrulanmış SMTP geçişi kullanmadan) e-posta gönderebilme olanağı istiyorsanız, kısıtlamayı kaldırmak için bir istek yapabilirsiniz. İstekler, Microsoft 'un kararına göre incelenip onaylanacaktır ve yalnızca ek sahtekarlık denetimleri yapıldıktan sonra verilecek. Bir istek yapmak için aşağıdaki sorun türünü kullanarak bir destek talebi açın: **Teknik** **sanal ağ** **bağlantısı** **e-posta gönderemez (SMTP/bağlantı noktası 25).**  >  >  >  Dağıtımınızın neden kimliği doğrulanmış bir geçiş kullanmak yerine doğrudan posta sağlayıcılarına e-posta göndermek zorunda olduğuna ilişkin ayrıntıları eklediğinizden emin olun.

Kullandıkça Öde veya Microsoft İş Ortağı Ağı abonelik dışlandıktan sonra ve VM 'Ler Azure portal ' & ' başlatıldı ' olarak durdurulduktan sonra, Bu abonelikteki tüm VM 'Ler ileri alınır. Muafiyet yalnızca istenen aboneliğe uygulanabilir ve yalnızca doğrudan internet 'e yönlendirilen sanal makine trafiği için geçerlidir. Azure [güvenlik duvarı](https://azure.microsoft.com/services/azure-firewall/) gibi Azure PaaS hizmetleri aracılığıyla yönlendirme bağlantı noktası 25 trafiği desteklenmez.

> [!NOTE]
> Microsoft, hizmet koşullarının ihlal edildiğini tespit ettiyse bu muafiyeti iptal etme hakkını saklı tutar.

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>MSDN, Azure Pass, Open ile Azure, eğitim, BizSpark ve ücretsiz deneme
15 Kasım 2017 ' den sonra bir MSDN, Azure Pass, Open ile Azure, eğitim, BizSpark, Azure Sponsorluğu, Azure öğrenci, ücretsiz deneme veya herhangi bir Visual Studio aboneliği oluşturduysanız, bunlar içindeki VM 'lerden gönderilen e-postayı engelleyen teknik kısıtlamalara sahip olursunuz. Abonelikler doğrudan e-posta sağlayıcılarına. Kötüye kullanımı engellemek için kısıtlamalar yapılır. Bu kısıtlamayı kaldırma isteği verilecek.

Bu abonelik türlerini kullanıyorsanız, bu makalenin önceki kısımlarında açıklandığı gibi SMTP geçiş hizmetlerini kullanmanız veya abonelik türünü değiştirmeniz önerilir.

## <a name="cloud-service-provider-csp"></a>Bulut hizmeti sağlayıcısı (CSP)

Azure kaynaklarını CSP aracılığıyla kullanıyorsanız, güvenli bir SMTP geçişi kullanılmıyorsa, CSP 'yi sizin adınıza Microsoft ile bir kaldırma muafiyeti isteği oluşturmak üzere isteyebilirsiniz.

## <a name="need-help-contact-support"></a>Yardım mı gerekiyor? Desteğe başvurun

Hala yardıma ihtiyacınız varsa, aşağıdaki sorun türünü kullanarak sorununuzun hızlı bir şekilde çözülmesini sağlamak için [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) : **Abonelik yönetimi** Sorun türü: **Bağlantı noktası 25 e-posta akışını etkinleştirme isteği**.
