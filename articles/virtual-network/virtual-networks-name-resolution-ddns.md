---
title: Azure 'da ana bilgisayar adlarını kaydettirmek için dinamik DNS kullanma | Microsoft Docs
description: Kendi DNS sunucularınızda ana bilgisayar adlarını kaydettirmek için dinamik DNS ayarlamayı öğrenin.
services: dns
documentationcenter: na
author: subsarma
manager: vitinnan
editor: ''
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: subsarma
ms.openlocfilehash: ad91eb94aedcdd0e4e715162e3ae064a1d2fb1ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98220431"
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Kendi DNS sunucunuzda ana bilgisayar adlarını kaydetmek için dinamik DNS kullanma

Azure, sanal makineler (VM) ve rol örnekleri için [ad çözümlemesi sağlar](virtual-networks-name-resolution-for-vms-and-role-instances.md) . Ad çözünürlüğünüz, Azure 'un varsayılan DNS DNS 'in sağladığı özellikleri aştığında kendi DNS sunucularınızı sağlayabilirsiniz. Kendi DNS sunucularınızı kullanarak, DNS çözümünüzü kendi özel gereksinimlerinize uyacak şekilde uyarlayabilme olanağı elde edersiniz. Örneğin, Active Directory etki alanı denetleyiciniz aracılığıyla şirket içi kaynaklara erişmeniz gerekebilir.

Özel DNS sunucularınız Azure sanal makineleri olarak barındırılıyorsa, ana bilgisayar adlarını çözümlemek için aynı sanal ağa ait konak sorgularını Azure 'a iletebilirsiniz. Bu seçeneği kullanmak istemiyorsanız, sanal makine ana bilgisayar adlarını dinamik DNS (DDNS) kullanarak DNS sunucunuza kaydedebilirsiniz. Azure, DNS sunucularınızda kayıtları doğrudan oluşturmak için kimlik bilgilerine sahip değildir, bu nedenle diğer düzenlemeler genellikle gereklidir. Bazı yaygın senaryolar, alternatiflerle birlikte şunları izler:

## <a name="windows-clients"></a>Windows istemcileri
Etki alanına katılmış olmayan Windows istemcileri, önyüklenirken veya IP adresleri değiştiğinde güvenli olmayan DDNS güncelleştirmelerini dener. DNS adı, ana bilgisayar adı artı birincil DNS son ekidir. Azure birincil DNS sonekini boş bırakır, ancak son eki [Kullanıcı arabirimi](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794784(v=ws.10)) veya [PowerShell](/powershell/module/dnsclient/set-dnsclient)aracılığıyla sanal makinede ayarlayabilirsiniz.

Etki alanına katılmış Windows istemcileri, IP adreslerini güvenli DDNS kullanarak etki alanı denetleyicisi ile kaydeder. Etki alanına ekleme işlemi, istemcideki birincil DNS sonekini ayarlar ve güven ilişkisini oluşturur ve korur.

## <a name="linux-clients"></a>Linux istemcileri
Linux istemcileri başlangıçta DNS sunucusuna kayıt yaptırmaz, DHCP sunucusunun bunu yaptığı varsayılmaktadır. Azure 'un DHCP sunucularının, DNS sunucunuzdaki kayıtları kaydetme kimlik bilgileri yoktur. `nsupdate`DDNS güncelleştirmelerini göndermek için, bağlama paketine dahil edilen adlı bir aracı kullanabilirsiniz. DDNS Protokolü standartlaştırılmış olduğundan, `nsupdate` DNS sunucusunda bağlama kullanmadığınız durumlarda bile kullanabilirsiniz.

DHCP istemcisi tarafından, DNS sunucusunda ana bilgisayar adı girişini oluşturmak ve korumak için sunulan kancaları kullanabilirsiniz. DHCP çevrimi sırasında istemci, komut dosyalarını */etc/DHCP/dhclient-Exit-kancas.d/* içinde yürütür. Kullanarak yeni IP adresini kaydetmek için kancaları kullanabilirsiniz `nsupdate` . Örnek:

```bash
#!/bin/sh
requireddomain=mydomain.local

# only execute on the primary nic
if [ "$interface" != "eth0" ]
then
    return
fi

# When you have a new IP, perform nsupdate
if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
then
   host=`hostname`
   nsupdatecmds=/var/tmp/nsupdatecmds
     echo "update delete $host.$requireddomain a" > $nsupdatecmds
     echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
     echo "send" >> $nsupdatecmds

     nsupdate $nsupdatecmds
fi
```

`nsupdate`Güvenli DDNS güncelleştirmeleri gerçekleştirmek için komutunu da kullanabilirsiniz. Örneğin, bir BIND DNS sunucusu kullanırken, ortak özel anahtar çifti oluşturulur ( `http://linux.yyz.us/nsupdate/` ). DNS sunucusu, `http://linux.yyz.us/dns/ddns-server.html` anahtarın ortak parçası ile yapılandırılır () ve bu sayede istekteki imzayı doğrulayabilirler. İçin anahtar çiftini sağlamak üzere, `nsupdate` `-k` DDNS Güncelleştirme isteğinin imzalanabilmesi için seçeneğini kullanın.

Bir Windows DNS sunucusu kullanırken, içindeki parametresiyle Kerberos kimlik doğrulamasını kullanabilirsiniz `-g` `nsupdate` , ancak Windows sürümünde kullanılamaz `nsupdate` . Kerberos 'u kullanmak için `kinit` kimlik bilgilerini yüklemek üzere kullanın. Örneğin, bir [keytab dosyasından](https://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)kimlik bilgilerini yükleyebilir ve sonra `nsupdate -g` önbellekten kimlik bilgilerini alabilir.

Gerekirse, sanal makinelerinize bir DNS arama son eki ekleyebilirsiniz. DNS son eki, */etc/resolv.exe* dosyasında belirtilir. Çoğu Linux, bu dosyanın içeriğini otomatik olarak yönetir, bu nedenle genellikle düzenleyemezsiniz. Ancak, DHCP istemcisinin komutunu kullanarak soneki geçersiz kılabilirsiniz `supersede` . Son eki geçersiz kılmak için, */etc/DHCP/dhclient.exe* dosyasına şu satırı ekleyin:

```
supersede domain-name <required-dns-suffix>;
```