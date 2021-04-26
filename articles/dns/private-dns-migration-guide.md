---
title: Eski Azure DNS Özel Bölgeleri yeni kaynak modeline geçiriliyor
titleSuffix: Azure DNS
description: Bu kılavuz, eski özel DNS bölgelerini en son kaynak modeline geçirme hakkında adım adım yönergeler sağlar
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: 6bb828aaff0c1d026e977863a6e224aaea81b629
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729244"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>Eski Azure DNS özel bölgelerini yeni kaynak modeline geçirme

Genel Önizleme sırasında özel DNS bölgeleri "bölge ETYPE" özelliği "özel" olarak ayarlanmış "dnszones" kaynağı kullanılarak oluşturulmuştur. Bu tür bölgeler 31 Aralık 2019 ' den sonra desteklenmeyecektir ve "dnszones" yerine "privateDnsZones" kaynak türünü kullanan GA kaynak modeline geçirilmesi gerekir. Geçiş işlemi basittir ve bu işlemi otomatikleştirmek için bir PowerShell betiği sağladık. Bu kılavuzda, Azure DNS özel bölgelerinizi yeni kaynak modeline geçirmek için adım adım yönergeler sunulmaktadır.

Geçiş gerektiren dnszones kaynaklarını bulmak için; Azure CLı 'de aşağıdaki komutu yürütün.
```azurecli
az account set --subscription <SubscriptionId>
az network dns zone list --query "[?zoneType=='Private']"
```

## <a name="prerequisites"></a>Önkoşullar

Azure PowerShell 'ın en son sürümünü yüklediğinizden emin olun. Azure PowerShell (az) ve nasıl yükleneceğine ilişkin daha fazla bilgi için ziyaret edin https://docs.microsoft.com/powershell/azure/new-azureps-module-az

Azure PowerShell için az. PrivateDns modülünün yüklü olduğundan emin olun. Bu modülü yüklemek için yükseltilmiş bir PowerShell penceresi (yönetim modu) açın ve aşağıdaki komutu girin

```powershell
Install-Module -Name Az.PrivateDns
```

>[!IMPORTANT]
>Geçiş işlemi tamamen otomatikleştirilmiştir ve herhangi bir kapalı kalma süresine neden olması beklenmez. Ancak, kritik bir üretim ortamında Azure DNS özel bölgeleri (Önizleme) kullanıyorsanız, planlı bakım süresi penceresi sırasında aşağıdaki geçiş işlemini yürütmelisiniz. Geçiş betiğini çalıştırırken özel bir DNS bölgesinin yapılandırma veya kayıt kümelerini değiştirdiğinizden emin olun.

## <a name="installing-the-script"></a>Betiği yükleme

Yükseltilmiş bir PowerShell penceresi (yönetim modu) açın ve aşağıdaki komutu çalıştırın

```powershell
install-script PrivateDnsMigrationScript
```

Komut dosyasını yüklemek isteyip istemediğiniz sorulduğunda "A" yazın

![Betiği yükleme](./media/private-dns-migration-guide/install-migration-script.png)

Ayrıca, PowerShell betiğinin en son sürümünü şurada el ile edinebilirsiniz: https://www.powershellgallery.com/packages/PrivateDnsMigrationScript

>[!IMPORTANT]
>Geçiş betiği Azure Cloud Shell 'de çalıştırılmamalıdır ve internet 'e bağlı bir VM veya yerel makinede yürütülmesi gerekir.

## <a name="running-the-script"></a>Betiği çalıştırma

Betiği çalıştırmak için aşağıdaki komutu yürütün

```powershell
PrivateDnsMigrationScript.ps1
```

![Betiği çalıştırma](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>Abonelik KIMLIĞINI girin ve Azure 'da oturum açın

Geçirmeyi düşündüğünüz özel DNS bölgelerini içeren abonelik KIMLIĞINI girmeniz istenir. Azure hesabınızda oturum açmanız istenir. Komut dosyasının abonelikteki özel DNS bölge kaynaklarına erişebilmesi için oturum açma işleminin tamamını doldurun.

![Azure'da oturum açma](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>Geçirmek istediğiniz DNS bölgelerini seçin

İle komut dosyası, abonelikteki tüm özel DNS bölgelerinin listesini alır ve hangi hangilerinin geçirmek istediğinizi onaylamanız istenir. Tüm özel DNS bölgelerini geçirmek için "A" yazın. Bu adımı çalıştırdığınızda, komut dosyası yeni kaynak modeli kullanarak yeni özel DNS bölgeleri oluşturacak ve verileri yeni DSN bölgesine kopyalayacaktır. Bu adım, içindeki mevcut özel DNS bölgelerinizi de değiştirmez.

![DNS bölgelerini seçin](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>DNS çözümlemesini yeni DNS bölgelerine değiştirme

Bölgeler ve kayıtlar yeni kaynak modeline kopyalandıktan sonra, betik DNS çözümlemesini yeni DNS bölgelerine geçirmek isteyip istemeyecektir. Bu adım eski özel DNS bölgeleri ve sanal ağlarınız arasındaki ilişkilendirmeyi kaldırır. Eski bölge sanal ağlardan bağlantısı kesildiğinde, yukarıdaki adımda oluşturulan yeni DNS bölgeleri bu sanal ağların DNS çözümlemesini otomatik olarak alır.

Tüm sanal ağların DNS çözünürlüğünü değiştirmek için ' A ' seçeneğini belirleyin.

![Ad çözümlemesini değiştirme](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>DNS çözümlemesini doğrulama

Devam etmeden önce DNS bölgelerinizin DNS çözümlemesinin beklendiği gibi çalıştığını doğrulayın. Azure sanal makinelerinize oturum açabilir ve DNS çözümünün çalıştığını doğrulamak üzere geçirilmiş bölgelerde nslookup sorgusu verebilirsiniz.

![Ad çözümlemesini doğrulama](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

DNS sorgularının çözümlenmiyor olduğunu fark ederseniz, birkaç dakika bekleyip sorguları yeniden deneyin. DNS sorguları beklendiği gibi çalışıyorsa, komut dosyası özel DNS bölgesinden sanal ağı kaldırmanızı isterse, ' Y ' girin.

![Ad çözümlemesini Onayla](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>Geçirilen bölgelere karşı DNS çözümünden herhangi bir nedenden dolayı, yukarıdaki adımda ' N ' yazın ve betik DNS çözümlemesini eski bölgelere geri girer. Bir destek bileti oluşturun ve DNS bölgelerinizi geçiş konusunda size yardımcı olabilir.

## <a name="cleanup"></a>Temizleme

Bu adım eski DNS bölgelerini silecek ve yalnızca DNS çözümünün beklenen şekilde çalıştığını doğruladıktan sonra yürütülmesi gerekir. Her bir özel DNS bölgesini silmeniz istenecektir. Bu bölgelerin DNS çözümlemesinin düzgün çalıştığını doğruladıktan sonra her sorulduğunda ' Y ' girin.

![Temizleme](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>Otomasyonunuzu güncelleştirme

Şablonlar, PowerShell betikleri veya SDK kullanılarak geliştirilen özel kod dahil Otomasyon kullanıyorsanız, özel DNS bölgeleri için yeni kaynak modelini kullanmak üzere otomasyon hesabınızı güncelleştirmeniz gerekir. Aşağıda, yeni özel DNS CLı/PS/SDK belgelerinin bağlantıları verilmiştir.
* [Özel bölgeler Azure DNS REST API](/rest/api/dns/privatedns/privatezones)
* [Özel bölgeler CLı Azure DNS](/cli/azure/network/private-dns/link/vnet)
* [Özel bölgeler PowerShell Azure DNS](/powershell/module/az.privatedns/)
* [Özel bölgeler SDK Azure DNS](/dotnet/api/overview/azure/privatedns/management)

## <a name="need-further-help"></a>Daha fazla yardım gerekiyor

Geçiş işlemiyle ilgili daha fazla yardıma ihtiyacınız varsa veya yukarıdaki listelenen adımların sizin için çalışmamasının nedeni nedeniyle bir destek bileti oluşturun. PowerShell betiği tarafından oluşturulan döküm dosyası dosyasını destek biletle birlikte ekleyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure PowerShell](./private-dns-getstarted-powershell.md) veya [Azure CLI](./private-dns-getstarted-cli.md)kullanarak Azure DNS bir özel bölge oluşturmayı öğrenin.

* Azure DNS özel bölgelerle gerçekleştirilen bazı yaygın [özel bölge senaryoları](./private-dns-scenarios.md) hakkında bilgi edinin.

* Belirli işlemler için bekleneceğiniz belirli davranış dahil olmak üzere Azure DNS özel bölgeler hakkında sık sorulan sorular ve yanıtlar için, bkz. [özel DNS SSS](./dns-faq-private.md).

* DNS bölgelerini ve [kayıtları](dns-zones-records.md)ziyaret ederek DNS bölgeleri ve kayıtları hakkında bilgi edinin.

* Azure'un diğer önemli [ağ özelliklerinden](../networking/networking-overview.md) bazıları hakkında bilgi edinin.
