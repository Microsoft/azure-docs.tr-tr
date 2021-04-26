---
title: MariaDB için Azure veritabanı sertifika dönüşü
description: MariaDB için Azure veritabanı 'nı etkileyecek kök sertifika değişikliklerinin yakında değişiklikler hakkında bilgi edinin
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: 105bc7f14f9ddcc4a64564edc1eebcd17b898bc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101699003"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mariadb"></a>MariaDB için Azure veritabanı 'nda kök CA değişikliği değişikliklerini anlama

MariaDB için Azure veritabanı, standart bakım ve güvenlik en iyi uygulamalarının bir parçası olarak **15 şubat 2021 (02/15/2021)** tarihinde kök sertifika değişikliğini başarıyla tamamladı. Bu makalede, değişiklikler hakkında daha fazla ayrıntı, etkilenen kaynaklar ve uygulamanızın veritabanı sunucunuza bağlantıyı korumasından emin olmak için gereken adımlar verilmektedir.

> [!NOTE]
> Bu makale, Microsoft 'un artık kullandığı bir terim olan _bağımlı_ dönem başvuruları içerir. Terim yazılımlardan kaldırıldığında, bu makaleden kaldıracağız.
>

## <a name="why-root-certificate-update-is-required"></a>Kök sertifika güncelleştirmesi neden gereklidir?

MariaDB kullanıcıları için Azure veritabanı, [burada](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)bulunan MariaDB sunucusu Için Azure veritabanı 'na bağlanmak üzere yalnızca önceden tanımlanmış sertifikayı kullanabilir. Bununla birlikte, [sertifika yetkilisi (CA) tarayıcı Forumu](https://cabforum.org/),   CA satıcıları tarafından verilen birden çok sertifikanın uyumsuz olması için en son yayımlanmış raporlar.

Sektörün uyumluluk gereksinimlerine göre, CA satıcıları uyumlu olmayan CA 'Lar için CA sertifikalarını iptal etmeyi, sunucuların uyumlu CA 'lar tarafından verilen sertifikaları kullanmalarını ve bu uyumlu CA 'lardan CA sertifikaları kullanmasını gerektirir. MariaDB için Azure veritabanı bu uyumlu olmayan sertifikalardan birini kullandığından, MySQL sunucularınızda olası tehdidi en aza indirmek için sertifikayı uyumlu sürüme döndürmemiz gerekiyordu.

Yeni sertifika alınır ve 15 Şubat 2021 (02/15/2021) tarihinden itibaren geçerli olur. 

## <a name="what-change-was-performed-on-february-15-2021-02152021"></a>15 Şubat 2021 ' de hangi değişiklik yapıldı (02/15/2021)?

15 Şubat 2021 ' de, [baltimorecerbertrustroot kök](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) sertifikası aynı [baltimorecerbertrustroot kök sertifikası](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) ile değiştirilmiştir ve bu da mevcut müşterilerin sunucu bağlantılarının hiçbir etkisi olmadığından emin olun.  Bu değişiklik sırasında, [Baltimorecyıbertrustroot kök sertifikası](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) ile **değiştirilmez** ve bu değişiklik, müşterilerin değişikliği yapmasına daha fazla zaman tanımak için ertelenir.

## <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>Bağlantıyı sürdürmek için istemcimde herhangi bir değişiklik yapmam gerekiyor mu?

İstemci tarafında bir değişiklik yapılması gerekmez. Aşağıdaki önceki önerimizi izlediyseniz, **Baltimorecyıbertrustroot sertifikası** birleştirilmiş CA sertifikasından kaldırılmadığı sürece yine de bağlanmaya devam edersiniz. **Bağlantıyı sürdürmeye yönelik daha fazla bildirimde bulununcaya kadar, birleştirilmiş CA sertifikanızdan Baltimorecyıbertrustroot 'yi kaldırmadık.**

### <a name="previous-recommendation"></a>Önceki öneri

- Aşağıdaki bağlantılardan **baltimorecybertrustroot**  &  **DigiCertGlobalRootG2** CA 'sını indirin:

  - [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)
  - [https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)

- Hem **Baltimorecyıbertrustroot** hem de **DigiCertGlobalRootG2** sertifikaları dahil olmak üzere bir birleştirilmiş CA sertifika deposu oluşturun.

  - Java (MariaDB Bağlayıcısı/J) kullanıcıları için şunu yürütün:

    ```console
    keytool -importcert -alias MariaDBServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
    ```

    ```console
    keytool -importcert -alias MariaDBServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
    ```

    Ardından orijinal anahtar deposu dosyasını yeni oluşturulan yeni bir anahtar ile değiştirin:

    - System. setProperty ("javax. net. SSL. trustStore", "path_to_truststore_file");
    - System. setProperty ("javax. net. SSL. trustStorePassword", "Password");

  - .NET (MariaDB Bağlayıcısı/NET, MariaDBConnector) kullanıcıları için, Windows sertifika deposunda, güvenilen kök sertifika yetkililerinde **Baltimorecyıbertrustroot** ve **DigiCertGlobalRootG2** 'ın aynı olduğundan emin olun. Herhangi bir sertifika yoksa, eksik sertifikayı içeri aktarın.

    [![MariaDB .net sertifikası için Azure veritabanı](media/overview/netconnecter-cert.png)](media/overview/netconnecter-cert.png#lightbox)

  - Linux üzerinde SSL_CERT_DIR kullanarak .NET kullanıcıları için, SSL_CERT_DIR tarafından belirtilen dizinde **Baltimorecrivbertrustroot** ve **DigiCertGlobalRootG2** 'ın aynı olduğundan emin olun. Herhangi bir sertifika yoksa, eksik sertifika dosyasını oluşturun.

  - Diğer (MariaDB Istemcisi/MariaDB çalışma ekranı/C/C++/Go/Python/Ruby/PHP/NodeJS/Perl/Swift) kullanıcıları için aşağıdaki biçimde iki CA sertifika dosyasını birleştirebilirsiniz

   ```
   -----BEGIN CERTIFICATE-----
   (Root CA1: BaltimoreCyberTrustRoot.crt.pem)
   -----END CERTIFICATE-----
   -----BEGIN CERTIFICATE-----
   (Root CA2: DigiCertGlobalRootG2.crt.pem)
   -----END CERTIFICATE-----
   ```

- Özgün kök CA ped dosyasını birleştirilmiş kök CA dosyası ile değiştirin ve uygulamanızı/istemcinizi yeniden başlatın.
- Daha sonra, yeni sertifika sunucu tarafında dağıtıldıktan sonra, CA PEI dosyanızı DigiCertGlobalRootG2. CRT. ped olarak değiştirebilirsiniz.

## <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>15 Şubat 2021 ' de bu değişiklik sırasında Baltimorecyıbertrustroot sertifikası neden DigiCertGlobalRootG2 ile değiştirilmedi?

Bu değişiklik için müşteri hazırlığını değerlendirdik ve çok sayıda müşteri bu değişikliği yönetmek için ek sağlama süresi arıyor. Müşterilere hazırlık sağlamak için daha fazla müşteri adayı süresi sunmanın yanı sıra, müşterilere ve son kullanıcılara yeterli sağlama süresi sağlayan en az bir yılda sertifika değişikliğini DigiCertGlobalRootG2 olarak ertelemenizi karardık. 

Kullanıcılara yönelik önerilerimiz, birleştirilmiş bir sertifika oluşturmak ve sunucunuza bağlanmak için bir iletişim gönderene kadar Baltimoreconbertrustroot sertifikasını kaldırmayın. 

## <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>Baltimorecyıbertrustroot sertifikasını kaldırdık ne olursa?

MariaDB sunucusu için Azure veritabanınıza bağlanılırken bağlantı hatalarıyla başlayacaksınız. Bağlantıyı sürdürmek için [Baltimorecyıbertrustroot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) SERTIFIKASı ile [SSL 'yi yapılandırmanız](howto-configure-ssl.md) gerekir.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="1-if-im-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. SSL/TLS kullanmıyorsanız yine de kök CA 'yı güncelleştirmem gerekir mi?

SSL/TLS kullanmıyorsanız hiçbir eylem gerekmez.

### <a name="2-if-im-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. SSL/TLS kullanıyorum, kök CA 'yı güncelleştirmek için veritabanı sunucusunu yeniden başlatmem gerekir mi?

Hayır, yeni sertifikayı kullanmaya başlamak için veritabanı sunucusunu yeniden başlatmanız gerekmez. Sertifika güncelleştirme, istemci tarafı değişikdir ve gelen istemci bağlantılarının veritabanı sunucusuna bağlanabildiklerinden emin olmak için yeni sertifikayı kullanması gerekir.

### <a name="3-how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>3. Nasıl yaparım? kök sertifika doğrulaması ile SSL/TLS kullanıp kullandığımı öğrenin.

Bağlantı dizenizi inceleyerek bağlantılarınızın kök sertifikayı doğrulayıp doğrulamayıp belirleyemeyeceğini belirleyebilirsiniz.

- Bağlantı dizeniz veya içeriyorsa `sslmode=verify-ca` `sslmode=verify-identity` , sertifikayı güncelleştirmeniz gerekir.
- Bağlantı dizeniz,, `sslmode=disable` veya içerdiğinde, `sslmode=allow` `sslmode=prefer` `sslmode=require` sertifikaları güncelleştirmeniz gerekmez.
- Bağlantı dizeniz sslmode 'u belirtmezse, sertifikaları güncelleştirmeniz gerekmez.

Bağlantı dizesini soyutlayan bir istemci kullanıyorsanız, sertifikaları doğrulayıp doğrulamadığını anlamak için istemci belgelerini gözden geçirin.

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-mariadb"></a>4. MariaDB için Azure veritabanı ile App Service kullanılıyorsa etkisi nedir?

Azure Uygulama Hizmetleri için, MariaDB için Azure veritabanı 'na bağlanırken, uygulamanızla birlikte SSL kullanma yönteminize bağlı olarak iki olası senaryo vardır.

- Bu yeni sertifika platform düzeyinde App Service eklendi. Uygulamanızdaki App Service platforma dahil olan SSL sertifikalarını kullanıyorsanız hiçbir işlem gerekmez. En yaygın senaryo budur. 
- Kodunuzda SSL sertifika dosyasının yolunu açıkça dahil ediyorsanız, yeni sertifikayı indirmeniz ve kodu yeni sertifikayı kullanacak şekilde güncelleştirmeniz gerekir. Bu senaryonun iyi bir örneği, [App Service belgelerinde](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress)paylaşıldığından App Service özel kapsayıcılar kullandığınızda oluşur. Bu, yaygın olmayan bir senaryodur ancak bunu kullanan bazı kullanıcıları gördük.

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-mariadb"></a>5. MariaDB için Azure veritabanı ile Azure Kubernetes Hizmetleri (AKS) kullanılıyorsa etkisi nedir?

Azure Kubernetes Services (AKS) kullanarak MariaDB için Azure veritabanı 'na bağlanmaya çalışıyorsanız, adanmış bir müşterilerin ana bilgisayar ortamından erişime de benzer. [Buradaki](../aks/ingress-own-tls.md)adımlara bakın.

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-mariadb"></a>6. MariaDB için Azure veritabanı 'na bağlanmak üzere Azure Data Factory kullanıyorsanız etkisi nedir?

Bağlayıcı Azure Integration Runtime kullanan bağlayıcı için Azure 'da barındırılan ortamdaki Windows sertifika deposundaki sertifikaları kullanır. Bu sertifikalar, yeni uygulanan sertifikalarla zaten uyumlu olduğundan herhangi bir eylem gerekmiyor.

Şirket içinde barındırılan Integration Runtime kullanan bağlayıcı için, bağlantı dizinizdeki SSL sertifikası dosyasının yolunu açıkça eklediğiniz yerde, [yeni sertifikayı](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) indirip kullanılacak bağlantı dizesini güncelleştirmeniz gerekir.

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7. bu değişiklik için bir veritabanı sunucusu bakım kapalı kalma süresi planlamanız gerekir mi?

Hayır. Buradaki değişiklik yalnızca istemci tarafında veritabanı sunucusuna bağlanmak için olduğundan, bu değişiklik için veritabanı sunucusu için gerekli bakım kapalı kalma süresi yoktur.

### <a name="8-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>8.15 Şubat 2021 (02/15/2021) sonrasında yeni bir sunucu oluştururum, bundan etkilenecek mıyım?

15 Şubat 2021 (02/15/2021) ' den sonra oluşturulan sunucular için, uygulamalarınızın SSL kullanarak bağlanması için [Baltimorecyıbertrustroot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) 'yi kullanmaya devam edersiniz.

### <a name="9-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>9. Microsoft sertifikalarını ne sıklıkla güncelleştiriyor veya süre sonu ilkesi nedir?

MariaDB için Azure veritabanı tarafından kullanılan bu sertifikalar, güvenilen sertifika yetkilileri (CA) tarafından sağlanır. Bu nedenle, bu sertifikaların desteklenmesi CA tarafından bu sertifikaların desteğine bağlıdır. [Baltimorecerbertrustroot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) sertifikası 2025 ' de sona ermek üzere zamanlandı. bu nedenle, Microsoft 'un süre sonu öncesinde bir sertifika değişikliği gerçekleştirmesi gerekir. Ayrıca, bu önceden tanımlanmış sertifikalarda öngörülemeyen hatalar varsa, Microsoft 'un her zaman güvenli ve uyumlu olduğundan emin olmak için, 15 Şubat 2021 ' de gerçekleştirilen değişikliğe benzer şekilde Microsoft 'un sertifika döndürmesini yapması gerekir.

### <a name="10-if-im-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>10. okuma çoğaltmaları kullanıyorsam, bu güncelleştirmeyi yalnızca kaynak sunucuda veya okuma çoğaltmalarıyla gerçekleştirmem gerekir mi?

Bu güncelleştirme istemci tarafı değişikliği olduğundan, istemci Çoğaltma sunucusundan veri okumak için kullanılıyorsa, bu istemciler için değişiklikleri de uygulamanız gerekir.

### <a name="11-if-im-using-data-in-replication-do-i-need-to-perform-any-action"></a>11. veri çoğaltma 'yı kullanıyorsam herhangi bir eylem gerçekleştirmem gerekir mi?

- Veri çoğaltma bir sanal makineden (Şirket içi veya Azure sanal makinesi) MySQL için Azure veritabanı 'na ait ise, çoğaltmayı oluşturmak için SSL 'nin kullanıldığını denetlemeniz gerekir. **BAĞıMLı durumu göster** ' i çalıştırın ve aşağıdaki ayarı denetleyin.

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

MySQL için Azure veritabanı 'na bağlanmak üzere [veri çoğaltma](concepts-data-in-replication.md) 'yı kullanıyorsanız, göz önünde bulundurmanız gereken iki şey vardır:

- Veri çoğaltma bir sanal makineden (Şirket içi veya Azure sanal makinesi) MySQL için Azure veritabanı 'na ait ise, çoğaltmayı oluşturmak için SSL 'nin kullanıldığını denetlemeniz gerekir. **BAĞıMLı durumu göster** ' i çalıştırın ve aşağıdaki ayarı denetleyin. 

  ```azurecli-interactive
  Master_SSL_Allowed            : Yes
  Master_SSL_CA_File            : ~\azure_mysqlservice.pem
  Master_SSL_CA_Path            :
  Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
  Master_SSL_Cipher             :
  Master_SSL_Key                : ~\azure_mysqlclient_key.pem
  ```

  CA_file, SSL_Cert ve SSL_Key için sertifika sağlandığını görürseniz, [yeni sertifikayı](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) ekleyerek ve birleştirilmiş bir sertifika dosyası oluşturarak dosyayı güncelleştirmeniz gerekir.

- Veri çoğaltma, MySQL için iki Azure veritabanı arasındaysa, **çağrı MySQL.az_replication_change_master** yürüterek çoğaltmayı sıfırlamanız ve yeni çift kök sertifikayı son parametre [master_ssl_ca](howto-data-in-replication.md#link-the-source-and-replica-servers-to-start-data-in-replication)olarak sağlamanız gerekir.

### <a name="12-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>12. SSL 'nin kullanıldığını doğrulamak için sunucu tarafı sorgumuz var mı?

Sunucuya bağlanmak için SSL bağlantısı kullanıp kullandığınızı doğrulamak için [SSL doğrulaması](howto-configure-ssl.md#verify-the-ssl-connection)' na başvurun.

### <a name="13-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>13. sertifika dosyasında DigiCertGlobalRootG2 zaten varsa gerekli bir eylem var mı?

Hayır. Sertifika dosyanızda zaten **DigiCertGlobalRootG2** varsa herhangi bir işlem yapmanız gerekmez.

### <a name="14-what-if-i-have-further-questions"></a>14. daha fazla sorunuz varsa ne yapmalıyım?

Sorularınız varsa, [Microsoft Q&A](mailto:AzureDatabaseformariadb@service.microsoft.com)'daki topluluk uzmanlarının yanıtlarını alın. Destek planınız varsa ve teknik yardıma ihtiyacınız varsa [bizimle iletişime geçin](mailto:AzureDatabaseformariadb@service.microsoft.com).
