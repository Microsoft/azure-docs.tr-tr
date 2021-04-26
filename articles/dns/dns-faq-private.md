---
title: Azure Özel DNS hakkında SSS
description: Bu makalede, Azure Özel DNS hakkında sık sorulan sorular hakkında bilgi edinin
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 01/15/2021
ms.author: rohink
ms.openlocfilehash: 3272f4fea4169fd5e91b1ba2b97ad72dfbb49080
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98247395"
---
# <a name="azure-private-dns-faq"></a>Azure Özel DNS hakkında SSS

Azure özel DNS hakkında sık sorulan sorular aşağıda verilmiştir.

## <a name="does-azure-dns-support-private-domains"></a>Özel etki alanlarını desteklemek Azure DNS mı?

Özel etki alanları Azure Özel DNS bölgeleri özelliği kullanılarak desteklenir. Özel DNS bölgeler yalnızca belirtilen sanal ağların içinden çözülebilir. Daha fazla bilgi için bkz. [genel bakış](private-dns-overview.md).

Azure 'daki diğer iç DNS seçenekleri hakkında daha fazla bilgi için bkz. [VM 'ler ve rol örnekleri Için ad çözümlemesi](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>Azure Özel DNS bölgeleri, Azure bölgelerinde çalışır mi?

Evet. Özel bölgeler, Azure bölgelerindeki sanal ağlar arasındaki DNS çözümlemesi için desteklenir. Özel bölgeler, sanal ağları açıkça eşlemeye gerek kalmadan bile çalışmaktadır. Tüm sanal ağların özel DNS bölgesine bağlı olması gerekir.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Özel bölgeler için gereken sanal ağlardan internet bağlantısı var mı?

Hayır. Özel bölgeler, sanal ağlarla birlikte çalışır. Sanal makineler veya sanal ağlar içindeki ve içindeki diğer kaynaklar için etki alanlarını yönetmek üzere bunları kullanabilirsiniz. Ad çözümlemesi için Internet bağlantısı gerekli değildir.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>Aynı özel bölge, çözümlenmek üzere birkaç sanal ağ için de kullanılabilir mi?

Evet. Bir özel DNS bölgesini binlerce sanal ağla bağlayabilirsiniz. Daha fazla bilgi için bkz. [Azure DNS sınırları](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits)

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>Farklı bir aboneliğe ait olan bir sanal ağ, özel bir bölgeye bağlanabilir mi?

Evet. Sanal ağlarda ve özel DNS bölgesinde yazma işlemi izninizin olması gerekir. Yazma izni birkaç Azure rolüne verilebilir. Örneğin, klasik ağ katılımcısı Azure rolü sanal ağlara yazma izinlerine sahiptir ve Özel DNS bölgeleri katkıda bulunan rolü, özel DNS bölgelerinde yazma izinlerine sahiptir. Azure rolleri hakkında daha fazla bilgi için bkz. [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Sanal makineyi sildiğinizde özel bir bölgedeki otomatik olarak kaydettirilen sanal makine DNS kayıtları otomatik olarak silinir mi?

Evet. Otomatik kayıt etkin olan bağlı bir sanal ağ içindeki bir sanal makineyi silerseniz, kayıtlı kayıtlar otomatik olarak silinir.

## <a name="ive-reconfigured-the-os-in-my-virtual-machine-to-have-a-new-host-name-or-static-ip-address-why-dont-i-see-that-change-reflected-in-the-private-zone"></a>Sanal makinemdeki işletim sistemini yeni bir ana bilgisayar adı veya statik IP adresine sahip olacak şekilde yapılandırdım. Bu değişikliğin özel bölgede yansıtıldığını neden görmüyorum?

Özel bölgenin kayıtları Azure DHCP hizmeti tarafından doldurulur; istemci kayıt iletileri yok sayılır. Statik bir IP adresi yapılandırarak VM 'de DHCP istemci desteğini devre dışı bırakırsanız, ana bilgisayar adı veya VM 'deki statik IP değişiklikleri bölgede yansıtılmaz.

## <a name="i-have-configured-a-preferred-dns-suffix-in-my-windows-virtual-machine-why-are-my-records-still-registered-in-the-zone-linked-to-the-virtual-network"></a>Windows sanal makinmda tercih edilen bir DNS son eki yapılandırdım. Kayıtlarım neden hala sanal ağa bağlı bölgede kayıtlı?

Azure DHCP hizmeti, özel DNS bölgesini kaydettiğinde DNS son ekini yoksayar. Örneğin, sanal makineniz `contoso.com` BIRINCIL DNS son eki olarak yapılandırılmışsa, ancak sanal ağ `fabrikam.com` özel DNS bölgesine bağlıysa, sanal makinenin kaydı `fabrikam.com` özel DNS bölgesinde görüntülenir.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Bağlı bir sanal ağdan özel bir bölgede otomatik olarak kaydedilmiş bir sanal makine kaydı el ile silinebilir mi?

Evet. Otomatik olarak kayıtlı DNS kayıtlarının, bölgede el ile oluşturulan bir DNS kaydıyla üzerine yazabilirsiniz. Aşağıdaki soru ve yanıt bu konuyu ele almaktadır.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Bağlı bir sanal ağda otomatik olarak kaydedilmiş var olan bir sanal makine ile aynı ana bilgisayar adına sahip özel bir bölgede yeni bir DNS kaydını el ile oluşturmayı denediğimde ne olur?

Bağlı bir sanal ağda, mevcut, otomatik olarak kaydedilmiş bir sanal makineyle aynı ana bilgisayar adına sahip özel bir bölgede el ile yeni bir DNS kaydı oluşturmaya çalışırsınız. Bunu yaptığınızda, yeni DNS kaydı otomatik olarak kaydedilen sanal makine kaydının üzerine yazar. Bu elle oluşturulan DNS kaydını bölgeden yeniden silmeye çalışırsanız, silme işlemi başarılı olur. Otomatik kayıt, sanal makine hala varolduğu ve kendisine bağlı özel bir IP 'si olduğu sürece yeniden gerçekleşir. DNS kaydı, bölgede otomatik olarak yeniden oluşturulur.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Bağlı bir sanal ağın özel bir bölgeden bağlantısını kaldırdığınızda ne olur? Sanal ağdan otomatik olarak kaydedilen sanal makine kayıtları bölgeden da kaldırılacak mı?

Evet. Bağlı bir sanal ağın özel bir bölgeden bağlantısını kaldırmak için, DNS bölgesini, ilişkili sanal ağ bağlantısını kaldıracak şekilde güncelleştirin. Bu işlemde, otomatik olarak kaydedilen sanal makine kayıtları bölgeden kaldırılır.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Özel bir bölgeyle bağlantılı bağlı bir sanal ağı silediğimiz zaman ne olur? Sanal ağın bölge üzerinden bağlı bir sanal ağ olarak bağlantısını kaldırmak için özel bölgeyi el ile güncelleştirmemiz gerekir mi?

Hayır. Önce özel bir bölgeden bağlantısını kaldırmadan bağlı bir sanal ağı sildiğinizde, silme işlemi başarılı olur ve DNS bölgesinin bağlantıları otomatik olarak temizlenir.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Özel bir bölge (örneğin, private.contoso.com) bir sanal ağla bağlantılı olduğunda bile varsayılan FQDN (internal.cloudapp.net) kullanılarak DNS çözümlemesi devam eder mi?

Evet. Özel bölgeler, varsayılan Azure tarafından sağlanmış internal.cloudapp.net bölgesinin yerini alır. Azure tarafından sağlanmış internal.cloudapp.net veya kendi özel bölgeniz olsun, çözümlemek istediğiniz bölgenin FQDN 'sini kullanın.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Bağlı bir sanal ağ içindeki sanal makinelerdeki DNS son eki özel bölgeye göre değiştirilsin mi?

Hayır. Bağlı sanal ağınızdaki sanal makinelerdeki DNS son eki, varsayılan olarak Azure tarafından belirtilen sonek ("*. internal.cloudapp.net") olarak kalır. Sanal makinelerinizdeki bu DNS sonekini özel bölgeye el ile değiştirebilirsiniz.
Bu sonekin nasıl değiştirileceği hakkında yönergeler için [, DNS sunucunuza ana bilgisayar adlarını kaydetmek üzere dınamık DNS kullanma](../virtual-network/virtual-networks-name-resolution-ddns.md#windows-clients) konusuna bakın

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>Azure DNS özel bölgelerin kullanım sınırları nelerdir?

Azure DNS özel bölgelerin kullanım limitleriyle ilgili ayrıntılar için [Azure DNS sınırlara](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits) bakın.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Var olan özel DNS bölgelerimin yeni Portal deneyiminde neden gösterilmeliyim?

Varolan özel DNS bölgeniz önizleme API 'SI kullanılarak oluşturulduysa, bu bölgeleri yeni kaynak modeline geçirmeniz gerekir. Preview API kullanılarak oluşturulan Özel DNS bölgeler, yeni Portal deneyiminde gösterilmez. Yeni kaynak modeline geçiş hakkında yönergeler için aşağıya bakın.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Var olan özel DNS bölgelerinizi yeni modele geçirmek Nasıl yaparım? mi?

Yeni kaynak modeline mümkün olan en kısa sürede geçiş yapmanızı önemle tavsiye ederiz. Ancak, eski kaynak modeli desteklenecektir, ancak daha fazla özellik bu modelin üzerine geliştirilecektir. Daha sonra, yeni kaynak modeli yerine kullanımdan kaldırmayı amaçlıyoruz. Mevcut özel DNS bölgelerinizi yeni kaynak modeline geçirme hakkında yönergeler için bkz.[Azure DNS özel bölgeler için geçiş kılavuzu](private-dns-migration-guide.md).

### <a name="does-azure-dns-private-zones-store-any-customer-content"></a>Azure DNS özel bölgeler tüm müşteri içeriğini mi depolar?

Hayır, Azure DNS özel bölgeler hiçbir müşteri içeriğini depolamaz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Özel DNS hakkında daha fazla bilgi](private-dns-overview.md)
