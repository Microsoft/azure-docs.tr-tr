---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fa17223789779390dabc6c88df02824882882128
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106073676"
---
Bir istemci sertifikası oluşturduğunuzda, bu, oluşturmak için kullandığınız bilgisayara otomatik olarak yüklenir. İstemci sertifikasını başka bir istemci bilgisayara yüklemek istiyorsanız, oluşturduğunuz istemci sertifikasını dışarı aktarmanız gerekir.

1. Bir istemci sertifikasını dışarı aktarmak için **Kullanıcı sertifikalarını yönet** menüsünü açın. Oluşturduğunuz istemci sertifikaları varsayılan olarak ' sertifikalar-geçerli kullanıcı \ kişisel \ Sertifikalar ' ' da bulunur. Dışarı aktarmak istediğiniz istemci sertifikasına sağ tıklayın, **Tüm görevler**' e tıklayın ve ardından **dışarı aktar** ' a tıklayarak **Sertifika Verme Sihirbazı**' nı açın.

   ![Ekran görüntüsü, geçerli kullanıcı için sertifikalar penceresini gösterir ve seçili sertifikaların seçili ve dışarı aktarma Işlemleri tüm görevlerden seçilir.](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. Sertifika dışarı aktarma sihirbazında, devam etmek için **İleri** ' ye tıklayın.

   ![Ekran görüntüsü, sertifika dışarı aktarma sihirbazına hoş geldiniz iletisini gösterir.](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. **Evet, özel anahtarı dışarı aktar**' ı seçin ve ardından **İleri**' ye tıklayın.

   ![özel anahtarı dışarı aktar](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. **Dışarı Aktarma Dosyası Biçimi** sayfasında, varsayılan ayarları seçili bırakın. **Mümkünse sertifika yolundaki tüm sertifikaları ekle** seçeneğinin işaretli olduğundan emin olun. Bu ayar ayrıca, başarılı istemci kimlik doğrulaması için gerekli olan kök sertifika bilgilerini dışarı aktarır. Bu olmadan istemci, güvenilen kök sertifikaya sahip olmadığından istemci kimlik doğrulaması başarısız olur. Ardından **İleri**' ye tıklayın.

   ![dışarı aktarma dosyası biçimi](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. **Güvenlik** sayfasında, özel anahtarı korumanız gerekir. Bir parola kullanmayı seçerseniz, bu sertifika için ayarladığınız parolayı kaydettiğinizden ya da unutmayacağınızdan emin olun. Ardından **İleri**' ye tıklayın.

   ![Ekran görüntüsü, girilen ve onaylanan parolanın yanı sıra bir sonraki vurgulanmış olan sertifika dışarı aktarma Sihirbazı Güvenlik sayfasını gösterir.](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. **Dışarı Aktarılan Dosya** sayfasında **Gözat**'a tıklayarak sertifika için dışarı aktarma konumunu seçin. **Dosya adı** alanına, sertifika dosyası için bir ad girin. Ardından **İleri**' ye tıklayın.

   ![dışarı aktarılacak dosya](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Sertifikayı dışarı aktarmak için **Son**'a tıklayın.

   ![Ekran görüntüsü, girilen ayarlarla sertifika dışarı aktarma Sihirbazı ' nı gösterir.](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)