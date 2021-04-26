---
title: Azure HPC önbellek ayarlarını yapılandırma
description: MTU, özel NTP ve DNS yapılandırması gibi önbellek için ek ayarların nasıl yapılandırılacağını ve Azure Blob depolama hedeflerinden hızlı anlık görüntülere nasıl erişebileceğinizi açıklar.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/08/2021
ms.author: v-erkel
ms.openlocfilehash: 0b3996df3c75ff31d0825be1d332dbd055305963
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259770"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Ek Azure HPC önbellek ayarlarını yapılandırma

Azure portal **ağ** sayfasında çeşitli ayarları özelleştirmeye yönelik seçenekler vardır. Çoğu kullanıcının bu ayarları varsayılan değerlerinden değiştirmesi gerekmez.

Bu makalede ayrıca Azure Blob depolama hedefleri için anlık görüntü özelliğinin nasıl kullanılacağı açıklanır. Anlık görüntü özelliğinin yapılandırılabilir ayarları yok.

Ayarları görmek için, önbelleğin **ağ** sayfasını Azure Portal açın.

![Azure portal ağ sayfasının ekran görüntüsü](media/networking-page.png)

> [!NOTE]
> Bu sayfanın önceki bir sürümü, önbellek düzeyinde bir kök sıkıştırma ayarı içeriyordu, ancak bu ayar [istemci erişim ilkelerine](access-policies.md)taşındı.

<!-- >> [!TIP]
> The [Managing Azure HPC Cache video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) shows the networking page and its settings. -->

## <a name="adjust-mtu-value"></a>MTU değerini ayarla
<!-- linked from troubleshoot-nas article -->

**MTU boyutu** etiketli açılan menüyü kullanarak önbelleğin en büyük iletim birimi boyutunu seçebilirsiniz.

Varsayılan değer 1500 bayttır, ancak bunu 1400 olarak değiştirebilirsiniz.

> [!NOTE]
> Önbelleğin MTU boyutunu düşürürseniz, önbellekle iletişim kuran istemcilerin ve depolama sistemlerinin aynı MTU ayarına veya daha düşük bir değere sahip olduğundan emin olun.

Önbellek MTU değerini düşürmek, önbelleğin ağının geri kalanında paket boyutu kısıtlamalarına geçici çözüm sağlamanıza yardımcı olabilir. Örneğin, bazı VPN 'Ler tam boyutlu 1500 baytlık paketleri başarıyla iletebilir. VPN üzerinden gönderilen paketlerin boyutunu azaltmak bu sorunu ortadan kaldırabilir. Ancak, daha düşük bir önbellek MTU ayarı, istemci ve depolama sistemleri dahil olmak üzere önbelleğiyle iletişim kuran diğer tüm bileşenlerin Ayrıca iletişim sorunlarından kaçınmak için daha düşük bir MTU ayarı olması gerektiğini gösterir.

Diğer sistem bileşenlerinde MTU ayarlarını değiştirmek istemiyorsanız, önbelleğin MTU ayarını düşürmemelisiniz. VPN paket boyutu kısıtlamalarına geçici bir çözüm bulabilirsiniz. Bu sorunu tanılama ve ele alma hakkında daha fazla bilgi edinmek için, NAS sorun giderme makalesindeki [VPN paket boyutu kısıtlamalarını ayarla](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) makalesini okuyun.

Azure [VM 'leri Için TCP/IP performans ayarlamayı](../virtual-network/virtual-network-tcpip-performance-tuning.md)okuyarak Azure sanal ağlarında MTU ayarları hakkında daha fazla bilgi edinin.

## <a name="customize-ntp"></a>NTP 'yi özelleştirme

Önbelleğiniz, varsayılan olarak Azure tabanlı saat sunucusu time.windows.com kullanır. Önbelleğinizin farklı bir NTP sunucusu kullanmasını istiyorsanız, bunu **NTP yapılandırma** bölümünde belirtin. Tam etki alanı adı veya IP adresi kullanın.

## <a name="set-a-custom-dns-configuration"></a>Özel bir DNS yapılandırması ayarlama

> [!CAUTION]
> İhtiyacınız yoksa önbellek DNS yapılandırmanızı değiştirmeyin. Yapılandırma hataları d sonuçlara sahip olabilir. Yapılandırmanız Azure hizmet adlarını çözümleyemezse, HPC önbellek örneğine kalıcı olarak ulaşılamayan olur.
>
> Özel bir DNS yapılandırması ayarlamayı denemeden önce Azure temsilcileriniz ile görüşün.

Azure HPC önbelleği, güvenli ve kullanışlı Azure DNS sistemi kullanacak şekilde otomatik olarak yapılandırılır. Ancak, birkaç olağandışı yapılandırma önbelleğin Azure sistemi yerine ayrı, şirket içi bir DNS sistemi kullanmasını gerektirir. **Ağ** sayfasının **DNS yapılandırması** bölümü, bu tür bir sistem belirtmek için kullanılır.

Azure temsilcileriniz ile görüşün veya özel bir önbellek DNS yapılandırması kullanıp kullanmayacağınızı öğrenmek için Microsoft hizmet ve destek 'e başvurun.

Azure HPC önbelleğinin kullanması için kendi şirket içi DNS sisteminizi yapılandırırsanız, yerel DNS sunucunuzun Azure hizmet uç noktası adlarını doğrudan çözümleyebildiğinizden emin olmanız gerekir. DNS sunucunuz ortak ad çözümlenme kısıtlamışsa HPC önbelleği çalışmaz.

DNS yapılandırmanızın bir Azure HPC önbelleği için kullanmadan önce bu öğeleri başarıyla çözümleyediğinden emin olun:

* ``*.core.windows.net``
* Sertifika iptal listesi (CRL) indirme ve çevrimiçi sertifika durumu Protokolü (OCSP) doğrulama hizmetleri. Bu [Azure TLS makalesinin](../security/fundamentals/tls-certificate-changes.md)sonundaki [güvenlik duvarı kuralları öğesinde](../security/fundamentals/tls-certificate-changes.md#will-this-change-affect-me) kısmi bir liste sağlanır, ancak tüm gereksinimleri anlamak için bir Microsoft Teknik temsilcisine danışmanız gerekir.
* NTP sunucunuzun tam etki alanı adı (time.windows.com veya özel bir sunucu)

Önbelleğiniz için özel bir DNS sunucusu ayarlamanız gerekiyorsa, belirtilen alanları kullanın:

* **DNS arama etki alanı** (isteğe bağlı)-arama etki alanınızı girin, örneğin ``contoso.com`` . Tek bir değere izin verilir veya boş bırakabilirsiniz.
* **DNS** sunucuları-en fazla üç DNS sunucusu girin. Bunları IP adresine göre belirtin.

<!-- 
  > [!NOTE]
  > The cache will use only the first DNS server it successfully finds. -->

Bir üretim ortamında kullanmadan önce DNS kurulumunuzu denetlemek ve iyileştirmek için bir test önbelleği kullanmayı göz önünde bulundurun.

### <a name="refresh-storage-target-dns"></a>Depolama hedefi DNS 'yi Yenile

DNS sunucunuz IP adreslerini güncelleştirirse, ilişkili NFS depolama hedefleri geçici olarak kullanılamıyor olur. [Depolama hedefleri düzenleme](hpc-cache-edit-storage.md#update-ip-address-custom-dns-configurations-only)bölümünde özel DNS sistem IP adreslerinizi güncelleştirme hakkında bilgi edinin.

## <a name="view-snapshots-for-blob-storage-targets"></a>BLOB depolama hedeflerinin anlık görüntülerini görüntüleme

Azure HPC Cache, Azure Blob depolama hedeflerine yönelik depolama anlık görüntülerini otomatik olarak kaydeder. Anlık görüntüler, arka uç depolama kapsayıcısının içeriği için hızlı bir başvuru noktası sağlar.

Anlık görüntüler veri yedeklemelerinin yerini almaz ve önbelleğe alınmış verilerin durumu hakkında bilgi içermez.

> [!NOTE]
> Bu anlık görüntü özelliği, NetApp veya ıconnectionstorage yazılımında bulunan Snapshot özelliğinden farklıdır. Bu anlık görüntü uygulamaları, anlık görüntüyü almadan önce önbellekteki değişiklikleri arka uç depolama sistemine temizler.
>
> Verimlilik için, Azure HPC önbellek anlık görüntüsü önce değişiklikleri temizlemez ve yalnızca blob kapsayıcısına yazılmış verileri kaydeder. Bu anlık görüntü, önbelleğe alınmış verilerin durumunu temsil etmez, bu nedenle son değişiklikleri içermeyebilir.

Bu özellik yalnızca Azure Blob depolama hedefleri için kullanılabilir ve yapılandırması değiştirilemez.

Anlık görüntüler, UTC 0:00, 08:00 ve 16:00 ' de sekiz saatte bir alınır.

Azure HPC Cache, günlük, haftalık ve aylık anlık görüntüleri yeni olanlar tarafından değiştirilene kadar depolar. Anlık görüntü bekletme sınırları şunlardır:

* En fazla 20 günlük anlık görüntü
* 8 haftalık anlık görüntüye kadar
* En fazla 3 aylık anlık görüntü

`.snapshot`Bağlı BLOB depolama hedefinin kökündeki dizinden anlık görüntülere erişin.
