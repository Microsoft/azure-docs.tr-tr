---
title: SNMP MIB izlemesini ayarlama
description: SNMP kullanarak, algılayıcı sistem durumu izleme gerçekleştirebilirsiniz. Algılayıcı, yetkilendirilmiş bir izleme sunucusundan gönderilen SNMP sorgularına yanıt verir.
ms.date: 12/14/2020
ms.topic: how-to
ms.openlocfilehash: 1ba52236f65c6c5daba68c67677cdc6adfb699b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781679"
---
# <a name="set-up-snmp-mib-monitoring"></a>SNMP MIB izlemesini ayarlama

Basit Ağ Yönetim Protokolü 'Nü (SNMP) kullanarak, algılayıcı sistem durumu izlemesi gerçekleştirebilirsiniz. Algılayıcı, yetkilendirilmiş bir izleme sunucusundan gönderilen SNMP sorgularına yanıt verir. SNMP İzleyicisi, algılayıcı OID 'lerini düzenli aralıklarla yoklar (saniyenin 50 katına kadar).

SNMP 'nin desteklediği sürümler SNMP v2 veya SNMP v3. SNMP, bağlantı noktası 161 (SNMP) ile birlikte aktarım protokolü olarak UDP kullanır.

SNMP izlemesini yapılandırmaya başlamadan önce, güvenlik duvarında UDP 161 bağlantı noktasını açmanız gerekir.

## <a name="oids"></a>OID

| Yönetim Konsolu ve algılayıcı | ID | Biçimlendir | Açıklama |
|--|--|--|--|
| Gereç adı | 1.3.6.1.2.1.1.5.0 | DISPLAYSTRING | Şirket içi yönetim konsolu için Gereç adı |
| Satıcı | 1.3.6.1.2.1.1.4.0 | DISPLAYSTRING | Microsoft Desteği (support.microsoft.com) |
| Platform | 1.3.6.1.2.1.1.1.0 | DISPLAYSTRING | Algılayıcı veya şirket içi yönetim konsolu |
| Seri numarası | 1.3.6.1.4.1.9.9.53313.1 | DISPLAYSTRING | Lisansın kullandığı dize |
| Yazılım sürümü | 1.3.6.1.4.1.9.9.53313.2 | DISPLAYSTRING | Xsense tam sürüm dizesi ve yönetimi tam sürüm dizesi |
| CPU kullanımı | 1.3.6.1.4.1.9.9.53313.3.1 | GAUGE32 | 0 ile 100 arasında gösterge |
| CPU sıcaklık | 1.3.6.1.4.1.9.9.53313.3.2 | DISPLAYSTRING | Linux girişi tabanlı 100 ile arasında bir gösterge |
| Bellek kullanımı | 1.3.6.1.4.1.9.9.53313.3.3 | GAUGE32 | 0 ile 100 arasında gösterge |
| Disk kullanımı | 1.3.6.1.4.1.9.9.53313.3.4 | GAUGE32 | 0 ile 100 arasında gösterge |
| Hizmet Durumu | 1.3.6.1.4.1.9.9.53313.5 | DISPLAYSTRING | Dört önemli bileşenden biri kapalıysa çevrimiçi veya çevrimdışı |
| Bant genişliği | 2,4 için kapsam dışı |  | Her bir izleyici arabiriminde Xsense 'de alınan bant genişliği |

   - Mevcut olmayan anahtarlar [Stack Overflow](https://stackoverflow.com/questions/51419026/querying-for-non-existing-record-returns-null-with-http-200)göre null, http 200 ile yanıt verir.
    
   - Donanımla ilgili Mıbs (CPU kullanımı, CPU sıcaklığı, bellek kullanımı, disk kullanımı) tüm mimarilerde ve fiziksel sensörlerden test edilmelidir. Sanal makinelerdeki CPU sıcaklığının uygulanamaılması bekleniyor.

Paketin aldığı bağlantı verileri ve ham veriler de dahil olmak üzere algılayıcı tarafından alınan tüm SNMP sorgularını içeren günlüğü indirebilirsiniz.

SNMP v2 sistem durumu izlemeyi tanımlamak için:

1. Yan menüde **sistem ayarları**' nı seçin.

2. **Etkin bulma** BÖLMESINDE **SNMP MIB izleme** ' yi seçin :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="SNMP pencerenizi düzenleyin.":::

3. **Izin verilen konaklar** bölümünde **konak Ekle** ' yi seçin ve sistem durumu izlemeyi gerçekleştiren sunucunun IP adresini girin.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="İzin verilen konaklar için IP adresini girin.":::

4. **Kimlik doğrulaması** bölümünde, **SNMP v2 topluluk dizesi** kutusuna dizeyi girin. SNMP topluluk dizesi en fazla 32 karakter içerebilir ve alfasayısal karakterlerin herhangi bir birleşimini (büyük harfler, küçük harfler ve rakamlar) içerebilir. Boşluk kullanılamaz.

5. **Kaydet**’i seçin.

SNMP v3 sistem durumu izlemeyi tanımlamak için:

1. Yan menüde **sistem ayarları**' nı seçin.

2. **Etkin bulma** BÖLMESINDE **SNMP MIB izleme** ' yi seçin :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="SNMP pencerenizi düzenleyin.":::

3. **Izin verilen konaklar** bölümünde **konak Ekle** ' yi seçin ve sistem durumu izlemeyi gerçekleştiren sunucunun IP adresini girin.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="İzin verilen konaklar için IP adresini girin.":::

4. **Kimlik doğrulama** bölümünde aşağıdaki parametreleri ayarlayın:

    | Parametre | Açıklama |
    |--|--|
    | **Kullanıcı adı** | SNMP Kullanıcı adı en çok 32 karakter içerebilir ve alfasayısal karakterlerin herhangi bir birleşimini (büyük harfler, küçük harfler ve rakamlar) içerebilir. Boşluk kullanılamaz. <br /> <br />SNMP v3 kimlik doğrulaması için Kullanıcı adı sistemde ve SNMP sunucusunda yapılandırılmalıdır. |
    | **Parola** | Büyük/küçük harfe duyarlı bir kimlik doğrulama parolası girin. Kimlik doğrulama parolası 8-12 karakter içerebilir ve herhangi bir alfasayısal karakter (büyük harf, küçük harf ve sayı) birleşimini içerebilir. <br /> <br/>SNMP v3 kimlik doğrulaması için Kullanıcı adı sistemde ve SNMP sunucusunda yapılandırılmalıdır. |
    | **Kimlik Doğrulama Türü** | MD5 veya SHA seçin. |
    | **Şifreleme** | DES veya AES ' yi seçin. |
    | **Gizli anahtar** | Anahtar tam olarak sekiz karakter içermeli ve alfasayısal karakterlerin herhangi bir birleşimini (büyük harfler, küçük harfler ve rakamlar) içermelidir. |

5. **Kaydet**’i seçin.

## <a name="see-also"></a>Ayrıca bkz.

[Sorun giderme günlüklerini dışarı aktar](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
