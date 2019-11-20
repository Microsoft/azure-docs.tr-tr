---
title: Azure 'da ağ sanal gereç sorunlarını giderme | Microsoft Docs
description: Azure 'da ağ sanal gereç sorunlarını nasıl giderebileceğinizi öğrenin.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2018
ms.author: genli
ms.openlocfilehash: b998043bc7d896989590ac21db5f309a81cc02bd
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056828"
---
# <a name="network-virtual-appliance-issues-in-azure"></a>Azure 'da ağ sanal gereç sorunları

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Microsoft Azure içinde üçüncü taraf ağ sanal gereci (NVA) kullanırken VM veya VPN bağlantı sorunları ve hatalarıyla karşılaşabilirsiniz. Bu makalede, NVA yapılandırmalarının temel Azure platform gereksinimlerini doğrulamanıza yardımcı olacak temel adımlar sağlanmaktadır.

Üçüncü taraf NVA 'lar için teknik destek ve Azure platformuyla tümleştirme, NVA satıcısı tarafından sağlanır.

> [!NOTE]
> Bir NVA içeren bir bağlantı veya yönlendirme sorununuz varsa, doğrudan [NVA satıcısına başvurmanız](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) gerekir.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>NVA satıcısı ile sorun giderme için denetim listesi

- NVA VM yazılımı için yazılım güncelleştirmeleri
- Hizmet hesabı kurulumu ve işlevleri
- Trafiği NVA 'ya yönlendiren sanal ağ alt ağlarında Kullanıcı tanımlı yollar (UDRs)
- NVA 'dan trafiği yönlendirecek sanal ağ alt ağlarında UDRs
- NVA içindeki tabloları ve kuralları yönlendirme (örneğin, NIC1 ile NIC2 arasında)
- Ağ trafiğinin alınmasını ve gönderilmesini doğrulamak için NVA NIC 'lerde izleme
- Standart SKU ve genel IP 'Ler kullanırken, trafiğin NVA 'ya yönlendirilmesine izin vermek için bir NSG oluşturulup oluşturulmuş ve açık bir kural olmalıdır.

## <a name="basic-troubleshooting-steps"></a>Temel sorun giderme adımları

- Temel yapılandırmayı denetleme
- NVA performansını denetle
- Gelişmiş ağ sorunlarını giderme

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Azure 'da NVA 'lar için en düşük yapılandırma gereksinimlerini denetleyin

Her NVA, Azure 'da doğru şekilde çalışması için temel yapılandırma gereksinimlerine sahiptir. Aşağıdaki bölümde, bu temel yapılandırmaların doğrulanması için gereken adımlar sağlanmaktadır. Daha fazla bilgi için, [NVA satıcısına başvurun](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

**NVA 'da IP iletmenin etkinleştirilip etkinleştirilmediğini denetleyin**

Azure portalı kullanma

1. [Azure Portal](https://portal.azure.com)NVA kaynağını bulun, ağ ' ı seçin ve ağ arabirimini seçin.
2. Ağ arabirimi sayfasında IP yapılandırması ' nı seçin.
3. IP iletimi özelliğinin etkinleştirildiğinden emin olun.

PowerShell kullanma

1. PowerShell 'i açın ve Azure hesabınızda oturum açın.
2. Aşağıdaki komutu çalıştırın (parantez içine alınmış değerleri, bilgilerinizi değiştirin):

   ```powershell
   Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   ```

3. **Enableipiletime** özelliğini denetleyin.
4. IP iletimi etkinleştirilmemişse, etkinleştirmek için aşağıdaki komutları çalıştırın:

   ```powershell
   $nic2 = Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   $nic2.EnableIPForwarding = 1
   Set-AzNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

**Standart SKU Pobilc IP 'si kullanılırken NSG 'Yi denetle** Standart SKU ve genel IP 'Ler kullanırken, NVA 'ya giden trafiğe izin vermek için bir NSG oluşturulup oluşturulmuş ve açık bir kural olmalıdır.

**Trafiğin NVA 'ya yönlendirilip yönlendirilmeyeceğini denetleyin**

1. [Azure Portal](https://portal.azure.com), **Ağ İzleyicisi**'Ni açın, **sonraki atlama**' yi seçin.
2. Trafiği NVA 'ya yönlendirmek üzere yapılandırılmış bir VM ve sonraki atlamanın görüntüleneceği bir hedef IP adresi belirtin. 
3. NVA bir **sonraki atlama**olarak listelenmiyorsa Azure yol tablolarını denetleyin ve güncelleştirin.

**Trafiğin NVA 'ya erişip erişemeyeceğini denetleyin**

1. [Azure Portal](https://portal.azure.com)' de, **Ağ İzleyicisi**' ni açın ve **IP akışı doğrulaması**' nı seçin. 
2. NVA 'nın sanal makinesini ve IP adresini belirtin ve ardından trafiğin herhangi bir ağ güvenlik grubu (NSG) tarafından engellenip engellenmediğini kontrol edin.
3. Trafiği engelleyen bir NSG kuralı varsa, NSG 'yi **etkin güvenlik** kuralları ' nda bulun ve trafiğin geçmesine izin vermek için güncelleştirin. Ardından **IP akışını doğrulayın** ve **bağlantı sorunlarını** , VM 'den iç veya dış IP adresinizden gelen TCP iletişimlerini test etmek için kullanın.

**NVA ve VM 'Lerin beklenen trafik için dinleme yapıp yapmadığını denetleyin**

1. RDP veya SSH kullanarak NVA 'ya bağlanın ve ardından aşağıdaki komutu çalıştırın:

    Windows için:

        netstat -an

    Linux için:

        netstat -an | grep -i listen
2. Sonuçlarda listelenen NVA yazılımı tarafından kullanılan TCP bağlantı noktasını görmüyorsanız, bu bağlantı noktalarına ulaşan trafiği dinlemek ve yanıtlamak için NVA ve VM 'de uygulamayı yapılandırmanız gerekir. [Gerektiğinde yardım almak için NVA satıcısına başvurun](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="check-nva-performance"></a>NVA performansını denetle

### <a name="validate-vm-cpu"></a>VM CPU 'sunu doğrulama

CPU kullanımı yüzde 100 ' e yakınsa, ağ paketi düşünü etkileyen sorunlarla karşılaşabilirsiniz. VM 'niz Azure portal belirli bir zaman aralığı için Ortalama CPU bildiriyor. CPU ani bir işlem sırasında, Konuk VM 'deki hangi işlemin yüksek CPU 'ya neden olduğunu araştırın ve mümkünse bu işlemi azaltabilirsiniz. Ayrıca, VM 'yi daha büyük bir SKU boyutu veya sanal makine ölçek kümesi için yeniden boyutlandırmanız, örnek sayısını artırmanız veya CPU kullanımında Otomatik ölçeklendirmeye ayarlamanız gerekebilir. Bu sorunlardan biri için gerektiğinde [Yardım için NVA satıcısına başvurun](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

### <a name="validate-vm-network-statistics"></a>VM ağ istatistiklerini doğrulama

VM ağı artışlar kullanıyorsa veya yüksek kullanım dönemlerini gösteriyorsa, daha yüksek aktarım hızı özellikleri elde etmek için VM 'nin SKU boyutunu artırmanız de gerekebilir. Ayrıca, hızlandırılmış ağ özelliğinin etkin olmasını sağlayarak VM 'yi yeniden dağıtabilirsiniz. NVA 'nın hızlandırılmış ağ özelliğini destekleyip desteklemediğini doğrulamak için, gerektiğinde [Yardım IÇIN NVA satıcısına başvurun](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="advanced-network-administrator-troubleshooting"></a>Gelişmiş Ağ Yöneticisi sorun giderme

### <a name="capture-network-trace"></a>Ağ izlemesini yakala
**[Psping](https://docs.microsoft.com/sysinternals/downloads/psping)** veya **Nmap**'i çalıştırırken kaynak VM 'de, NVA 'da ve hedef VM 'de eşzamanlı bir ağ izlemesi yakalayın ve ardından izlemeyi durdurun.

1. Eşzamanlı bir ağ izlemesi yakalamak için aşağıdaki komutu çalıştırın:

   **Windows için**

   Netsh Trace start Capture = Yes tracefile = C:\server_ip.exe = NetConnection

   **Linux için**

   sudo tcpdump-S0-ı eth0-X-w vmtrace. Cap

2. Kaynak VM 'den hedef VM 'ye **psping** veya **Nmap** kullanın (örneğin: `PsPing 10.0.0.4:80` veya `Nmap -p 80 10.0.0.4`).
3. [Ağ İzleyicisi](https://www.microsoft.com/download/details.aspx?id=4865) veya tcpdump kullanarak hedef VM 'den ağ izlemesini açın. `IPv4.address==10.0.0.4 (Windows netmon)` Ya`tcpdump -nn -r vmtrace.cap src or dst host 10.0.0.4` da (Linux) gibi, psping veya Nmap çalıştıran kaynak VM 'nin IP 'si için bir görüntüleme filtresi uygulayın.

### <a name="analyze-traces"></a>İzlemeleri çözümle

Arka uç VM izlemesine gelen paketleri görmüyorsanız, büyük olasılıkla bir NSG veya UDR engelliyor olabilir ya da NVA yönlendirme tabloları yanlış olur.

Gelen paketleri görüyorsanız ancak yanıt yoksa, VM uygulaması veya güvenlik duvarı sorununu çözmeniz gerekebilir. Bu sorunlardan biri için [gerektiğinde yardım almak üzere NVA satıcısına başvurun](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).