---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 27a4d775b8d88e02be69655e5adfc6155f867ef6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582747"
---
Düzgün bir SSL sertifikası şifrelenmiş bilgileri doğru sunucuya gönderdiğinizden emin olmanızı sağlar. Şifrelemenin yanı sıra, sertifika kimlik doğrulamasına da olanak tanır. Aygıtın PowerShell arabirimi aracılığıyla kendi güvenilen SSL sertifikanızı karşıya yükleyebilirsiniz.

1. [PowerShell arabirimine bağlanın](#connect-to-the-powershell-interface).
2. `Set-HcsCertificate`Sertifikayı karşıya yüklemek için cmdlet 'ini kullanın. İstendiğinde, aşağıdaki parametreleri sağlayın:

   - `CertificateFilePath` - *. Pfx* biçiminde sertifika dosyasını içeren paylaşımın yolu.
   - `CertificatePassword` -Sertifikayı korumak için kullanılan parola.
   - `Credentials` -Sertifikayı içeren paylaşıma erişmek için Kullanıcı adı. İstendiğinde ağ paylaşımının parolasını girin.

     Aşağıdaki örnek, bu cmdlet 'in kullanımını gösterir:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```
