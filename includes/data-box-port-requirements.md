---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839756"
---
| Bağlantı noktası yok.| Daraltma veya genişletme | Bağlantı noktası kapsamı| Gerekli| Notlar |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|İçinde|LAN|Evet|Bu bağlantı noktası, HTTP üzerinden REST API'ler veri kutusu Blog depolama alanına bağlanmak için kullanılır. REST API'lere bağlanma değil, yerel web kullanıcı Arabirimine otomatik olarak tekrar 8443 yönlendirir. |
| TCP 443 (HTTPS)|İçinde|LAN|Evet|Bu bağlantı noktasını, HTTPS üzerinden REST API'ler veri kutusu Blog depolama alanına bağlanmak için kullanılır. REST API'lere bağlanma değil, yerel web kullanıcı Arabirimine otomatik olarak tekrar 8443 yönlendirir. |
| TCP 8443 (HTTPS-Alt)|İçinde|LAN|Evet|Bu, HTTPS için alternatif bir bağlantı noktası ve cihaz yönetimi için yerel web kullanıcı Arabirimi bağlanırken kullanılır. |
| TCP 445 (SMB)|Giden/içinde|LAN|Bazı durumlarda<br>Notlara bakın|SMB bağlanıyorsanız Bu bağlantı noktası gereklidir. |
| TCP 2049 (NFS)|Giden/içinde|LAN|Bazı durumlarda<br>Notlara bakın|NFS bağlanıyorsanız Bu bağlantı noktası gereklidir. |
| TCP 111 (NFS)|Giden/içinde|LAN|Bazı durumlarda<br>Notlara bakın|Bu bağlantı noktası rpcbind/bağlantı noktası eşleme için kullanılan ve NFS bağlanıyorsanız, yalnızca gerekli.  |
