---
title: Azure NetApp Files için NFSv 4.1 Kerberos birimi sorunlarını giderme | Microsoft Docs
description: Azure NetApp Files için NFSv 4.1 Kerberos birimi sorunlarını gidermenize yardımcı olabilecek hata iletileri ve çözümleri açıklanmaktadır.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/12/2021
ms.author: b-juche
ms.openlocfilehash: 638607da02b1db4842cdc04f86a4fed1860c243f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98134322"
---
# <a name="troubleshoot-nfsv41-kerberos-volume-issues"></a>NFSv 4.1 Kerberos birimi sorunlarını giderme 

Bu makalede, NFSv 4.1 Kerberos birimlerini oluştururken veya yönetirken hata koşullarının çözümleri açıklanmaktadır. 

## <a name="error-conditions-and-resolutions"></a>Hata koşulları ve çözümleri

|     Hata koşulları    |     Çözümler    |
|-|-|
|`Error allocating volume - Export policy rules does not match kerberosEnabled flag` | Azure NetApp Files, NFSv3 birimleri için Kerberos 'yi desteklemez. Kerberos yalnızca NFSv 4.1 protokolü için desteklenir.  |
|`This NetApp account has no configured Active Directory   connections`  |  NetApp hesabı için Active Directory **KDC IP** ve **ad sunucu adı** ile yapılandırın. Yönergeler için [Azure Portal yapılandırma](configure-kerberos-encryption.md#configure-the-azure-portal) konusuna bakın. |
|`Mismatch between KerberosEnabled flag value and ExportPolicyRule's access type parameter values.`  | Azure NetApp Files, düz bir NFSv 4.1 birimini Kerberos NFSv 4.1 birimine dönüştürmeyi desteklemez ve tam tersi de geçerlidir. |
|`mount.nfs: access denied by server when mounting volume <SMB_SERVER_NAME-XXX.DOMAIN_NAME>/<VOLUME_NAME>` <br>  Örnek: `smb-test-64d9.contoso.com:/nfs41-vol101` | <ol><li> A/PTR kayıtlarının düzgün kurulduğundan ve sunucu adı için Active Directory bulunduğundan emin olun `smb-test-64d9.contoso.com` . <br> NFS istemcisinde, `nslookup` `smb-test-64d9.contoso.com` IP adresi IP1 (yani,) olarak ÇÖZÜMLENIRSE `10.1.1.68` , `nslookup` IP1 ' nin yalnızca bir kayda (yani,) çözümlenmelidir `smb-test-64d9.contoso.com` . `nslookup`öğesinin ıP1 birden çok ada *çözümlenmemelidir.* </li>  <li>`NFS-<Smb NETBIOS NAME>-<few random characters>`PowerShell veya Kullanıcı arabirimini kullanarak ad üzerinde bulunan NFS makine hesabı IÇIN AES-256 ayarlayın. <br> Örnek komutlar: <ul><li>`Set-ADComputer <NFS_MACHINE_ACCOUNT_NAME> -KerberosEncryptionType AES256` </li><li>`Set-ADComputer NFS-SMB-TEST-64 -KerberosEncryptionType AES256` </li></ul> </li> <li>NFS istemcisi, AD ve Azure NetApp Files depolama yazılımlarının zamanının birbirleriyle eşitlendiğinden ve beş dakikalık bir eğriltme aralığında olduğundan emin olun. </li>  <li>Komutunu kullanarak NFS istemcisinde Kerberos bileti alın `kinit <administrator>` .</li> <li>NFS istemci ana bilgisayar adını 15 karakterden daha az bir şekilde azaltın ve bölgeye katılmayı yeniden gerçekleştirin. </li><li>NFS istemcisini ve `rpcgssd` hizmetini aşağıdaki şekilde yeniden başlatın. Komut, işletim sistemine bağlı olarak farklılık gösterebilir.<br> RHEL 7: <br> `service nfs restart` <br> `service rpcgssd restart` <br> CentOS 8: <br> `systemctl enable nfs-client.target && systemctl start nfs-client.target` <br> Ubuntu <br> (Hizmeti yeniden başlatın `rpc-gssd` .) <br> `sudo systemctl start rpc-gssd.service` </ul>| 
|`mount.nfs: an incorrect mount option was specified`   | Sorun, NFS istemci sorunuyla ilişkili olabilir. NFS istemcisini yeniden başlatın.    |
|`Hostname lookup failed`   | DNS sunucusunda bir geriye doğru arama bölgesi oluşturmanız ve ardından bu geriye doğru arama bölgesine AD ana makinesinin PTR kaydını eklemeniz gerekir. <br> Örneğin, AD makinenin IP adresinin olduğunu `10.1.1.4` , ad makinesinin ana bilgisayar adının (ana bilgisayar adı kullanılarak bulunduğu şekilde) olduğunu `AD1` ve etki alanı adının olduğunu varsayalım `contoso.com` . Geriye doğru arama bölgesine eklenen PTR kaydı olmalıdır `10.1.1.4 -> AD1.contoso.com` . |
|`Volume creation fails due to unreachable DNS server`  | Olası iki çözüm vardır: <br> <ul><li> Bu hata, DNS 'ye ulaşılamıyor olduğunu gösterir. Bunun nedeni yanlış bir DNS IP 'si veya bir ağ sorunu olabilir. AD bağlantısı ' nda girilen DNS IP 'sini kontrol edin ve IP 'nin doğru olduğundan emin olun. </li> <li> AD ve birimin aynı bölgede ve aynı VNet 'te bulunduğundan emin olun. Bunlar farklı VNET 'lerde yer alıyorsa, iki sanal ağ arasında VNet eşlemesi kurulduğundan emin olun. </li></ul> |
|NFSv 4.1 Kerberos birimi oluşturma, aşağıdaki örneğe benzer bir hata vererek başarısız olur: <br> `Failed to enable NFS Kerberos on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". Failed to bind service principal name on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". SecD Error: server create fail join user auth.` |KDC IP 'si yanlış ve Kerberos birimi oluşturuldu. KDC IP 'sini doğru bir adresle güncelleştirin. <br> KDC IP 'yi güncelleştirdikten sonra, hata kaybolur. Birimi yeniden oluşturmanız gerekiyor. |

## <a name="next-steps"></a>Sonraki adımlar  

* [Azure NetApp Files için NFSv4.1 Kerberos şifrelemesini yapılandırma](configure-kerberos-encryption.md)
