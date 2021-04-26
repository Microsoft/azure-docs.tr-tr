---
title: Azure Cosmos DB Öykünücüsü sertifikalarını dışarı aktarma
description: Java, Python ve Node.js uygulamalarıyla kullanmak üzere Azure Cosmos DB öykünücü sertifikasını dışarı aktarmayı öğrenin. Sertifikalar, Windows sertifika deposunu kullanmayan diller ve çalışma zamanı ortamları için verilmelidir ve kullanılmalıdır.
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/17/2020
author: deborahc
ms.author: dech
ms.custom: devx-track-python, devx-track-java, contperf-fy21q1
ms.openlocfilehash: 952be09662c2c74f883d63de72bba2b9cb58d0e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554012"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs-apps"></a>Java, Python ve Node.js uygulamalarıyla kullanmak üzere Azure Cosmos DB öykünücü sertifikalarını dışarı aktarın
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB Öykünücüsü, geliştirme amaçlı olarak Azure Cosmos DB hizmetine öykünen yerel bir ortam sağlar. Azure Cosmos DB öykünücü yalnızca TLS bağlantılarıyla güvenli iletişimi destekler.

Azure Cosmos DB yerel öykünücüdeki sertifikalar, öykünücüyü ilk kez çalıştırdığınızda oluşturulur. İki sertifika vardır. Bunlardan biri yerel öykünücüye bağlanmak için kullanılır ve diğeri öykünücü içindeki öykünücü verilerinin varsayılan şifrelemesini yönetmek için kullanılır. Dışarı aktarmak istediğiniz sertifika, "DocumentDBEmulatorCertificate" kısa adına sahip bağlantı sertifikasıdır.

Bir Java, Python veya Node.js gibi farklı dillerde uygulama geliştirmek için öykünücü kullandığınızda, öykünücü sertifikasını dışarı aktarmanız ve gerekli sertifika deposuna aktarmanız gerekir.

.NET dil ve çalışma zamanı, uygulama bir Windows işletim sistemi konağında çalıştırıldığında Azure Cosmos DB yerel öykünücüye güvenli bir şekilde bağlanmak için Windows sertifika deposunu kullanır. Diğer dillerin kendi sertifikaları yönetme ve kullanma yöntemi vardır. Örneğin, Java kendi [sertifika deposunu](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html)kullanır, Python [yuva sarmalayıcıları](https://docs.python.org/2/library/ssl.html)kullanır ve Node.js [tlssocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)kullanır.

Bu makalede, Windows sertifika deposu ile tümleştirilen farklı dillerde ve çalışma zamanı ortamlarında kullanılmak üzere TLS/SSL sertifikalarının nasıl dışarı aktarılacağı gösterilmektedir. [Geliştirme ve test için Azure Cosmos DB Emulator kullanma](./local-emulator.md) konusunda öykünücü hakkında daha fazla bilgi edinebilirsiniz.

## <a name="export-the-azure-cosmos-db-tlsssl-certificate"></a><a id="export-emulator-certificate"></a>Azure Cosmos DB TLS/SSL sertifikasını dışarı aktarma

Öykünücü sertifikasını, Windows sertifika deposuyla tümleştirilebilen diller ve çalışma zamanı ortamlarından başarılı bir şekilde kullanmak için dışarı aktarmanız gerekir. Windows Sertifika Yöneticisi 'Ni kullanarak sertifikayı dışarı aktarabilirsiniz. "DocumentDBEmulatorCertificate" sertifikasını BASE-64 kodlu bir X. 509.440 (. cer) dosyası olarak dışarı aktarmak için aşağıdaki adım adım yönergeleri kullanın:

1. Certlm.msc’yi çalıştırarak Windows Sertifika yöneticisini çalıştırın ve Kişisel->Sertifikalar klasörüne gidip **DocumentDbEmulatorCertificate** kısa adına sahip sertifikayı açın.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png" alt-text="Azure Cosmos DB yerel öykünücüsü dışarı aktarma adımı 1":::

1. **Ayrıntılar**’a ve ardından **Tamam**’a tıklayın.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png" alt-text="Azure Cosmos DB yerel öykünücüsü dışarı aktarma adımı 2":::

1. **Dosyaya Kopyala...** seçeneğini belirleyin.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png" alt-text="Azure Cosmos DB yerel öykünücüsü dışarı aktarma adımı 3":::

1. **İleri**’ye tıklayın.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png" alt-text="Azure Cosmos DB yerel öykünücüsü dışarı aktarma adımı 4":::

1. **Hayır, özel anahtarı dışarı aktarma**’ya tıklayın ve **İleri**’ye tıklayın.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png" alt-text="Azure Cosmos DB yerel öykünücüsü dışarı aktarma adımı 5":::

1. **Base-64 ile kodlanmış X.509 (.CER)** seçeneğine ve ardından **İleri**’ye tıklayın.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png" alt-text="Azure Cosmos DB yerel öykünücüsü dışarı aktarma adımı 6":::

1. Sertifikaya bir ad verin. Bu durumda **documentdbemulatorcert** dosyasını seçin ve **İleri**’ye tıklayın.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png" alt-text="Azure Cosmos DB yerel öykünücüsü dışarı aktarma adımı 7":::

1. **Finish (Son)** düğmesine tıklayın.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png" alt-text="Azure Cosmos DB yerel öykünücüsü dışarı aktarma adımı 8":::

## <a name="use-the-certificate-with-java-apps"></a>Java uygulamalarıyla sertifikayı kullanma

Java tabanlı bir istemci kullanan Java uygulamalarını veya MongoDB uygulamalarını çalıştırırken, bu sertifikayı, bayrakları geçirmeden Java varsayılan sertifika deposuna yüklemek daha kolay olur `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` . Örneğin, dahil edilen Java demo uygulaması ( `https://localhost:8081/_explorer/index.html` ) varsayılan sertifika deposuna bağlıdır.

X. 509.440 sertifikasını varsayılan Java sertifika deposuna aktarmak için [Java sertifikaları deposuna sertifika ekleme](https://docs.oracle.com/cd/E54932_01/doc.705/e54936/cssg_create_ssl_cert.htm) bölümündeki yönergeleri izleyin. Keytool çalıştırılırken *% JAVA_HOME%* dizininde çalıştığınızı aklınızda bulundurun. Sertifika, sertifika deposuna alındıktan sonra, SQL ve Azure Cosmos DB 'in MongoDB API 'SI için istemcileri Azure Cosmos DB öykünücüye bağlanabilirler.

Alternatif olarak, sertifikayı içeri aktarmak için aşağıdaki Bash betiğini çalıştırabilirsiniz:

```bash
#!/bin/bash

# If emulator was started with /AllowNetworkAccess, replace the below with the actual IP address of it:
EMULATOR_HOST=localhost
EMULATOR_PORT=8081
EMULATOR_CERT_PATH=/tmp/cosmos_emulator.cert
openssl s_client -connect ${EMULATOR_HOST}:${EMULATOR_PORT} </dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > $EMULATOR_CERT_PATH
# delete the cert if already exists
sudo $JAVA_HOME/bin/keytool -cacerts -delete -alias cosmos_emulator
# import the cert
sudo $JAVA_HOME/bin/keytool -cacerts -importcert -alias cosmos_emulator -file $EMULATOR_CERT_PATH
```

"CosmosDBEmulatorCertificate" TLS/SSL sertifikası yüklendikten sonra, uygulamanızın yerel Azure Cosmos DB öykünücüsünü bağlanabilmesi ve kullanabilmesi gerekir. Herhangi bir sorununuz varsa, [hata ayıklama SSL/TLS bağlantıları](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) makalesini takip edebilirsiniz. Çoğu durumda, sertifika *% JAVA_HOME%/JRE/lib/security/CAcert* deposuna yüklenmemiş olabilir. Örneğin, Java 'nın birden çok yüklü sürümü varsa, uygulamanız güncelleştirenden farklı bir CAcert deposu kullanıyor olabilir.

## <a name="use-the-certificate-with-python-apps"></a>Python uygulamalarıyla sertifikayı kullanma

Python uygulamalarından öykünücüye bağlanılırken, TLS doğrulaması devre dışı bırakılır. Varsayılan olarak, SQL API 'SI için [Python SDK (sürüm 2.0.0 veya üzeri)](sql-api-sdk-python.md) , yerel ÖYKÜNÜCÜSÜNE bağlanırken TLS/SSL sertifikasını kullanmayı denemez. Ancak TLS doğrulamasını kullanmak istiyorsanız, [Python yuva sarmalayıcıları](https://docs.python.org/2/library/ssl.html) belgelerindeki örnekleri izleyebilirsiniz.

## <a name="how-to-use-the-certificate-in-nodejs"></a>Node.js içinde sertifika kullanma

Node.js SDK 'lardan öykünücüye bağlanılırken, TLS doğrulaması devre dışı bırakılır. Varsayılan olarak, SQL API 'SI için [Node.js SDK (sürüm 1.10.1 veya üzeri)](sql-api-sdk-node.md) , yerel ÖYKÜNÜCÜSÜNE bağlanırken TLS/SSL sertifikasını kullanmayı denemez. Ancak TLS doğrulamasını kullanmak istiyorsanız, [Node.js belgelerindeki](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)örnekleri izleyebilirsiniz.

## <a name="rotate-emulator-certificates"></a>Öykünücü sertifikalarını döndürme

Windows tepsisinde çalışan Azure Cosmos DB öykünücünden **verileri Sıfırla** ' yı seçerek öykünücü sertifikalarını yeniden oluşturmanız zorunlu hale getirebilirsiniz. Bu eylemin, öykünücü tarafından yerel olarak depolanan tüm verileri de temizyacağını unutmayın.

:::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png" alt-text="Azure Cosmos DB yerel öykünücüsü sıfırlama verileri":::

Sertifikayı Java sertifika deposuna yüklediyseniz veya başka bir yerde kullandıysanız, geçerli sertifikaları kullanarak bunları yeniden içeri aktarmanız gerekir. Uygulamanız, sertifikaları güncelleştirene kadar yerel öykünücüsüne bağlanamaz.

## <a name="next-steps"></a>Sonraki adımlar

* [Öykünücüyü denetlemek için komut satırı parametrelerini ve PowerShell komutlarını kullanma](emulator-command-line-parameters.md)
* [Öykünücü ile ilgili sorunları ayıklama](troubleshoot-local-emulator.md)
