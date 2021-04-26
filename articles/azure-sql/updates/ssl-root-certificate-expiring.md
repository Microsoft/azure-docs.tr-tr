---
title: Azure SQL veritabanı & SQL yönetilen örneği için sertifika döndürme
description: Azure SQL veritabanı ve Azure SQL yönetilen örneği 'ni etkileyecek kök sertifika değişikliklerinin yakında değişiklikler hakkında bilgi edinin
author: srdan-bozovic-msft
ms.author: srbozovi
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
ms.date: 09/13/2020
ms.openlocfilehash: 96367b143711c4ec5f3f8d609f048c72c6fded16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590860"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-sql-database--sql-managed-instance"></a>Azure SQL veritabanı & SQL yönetilen örneği için kök CA değişikliği değişikliklerini anlama

Azure SQL veritabanı & SQL yönetilen örneği, güvenli TDS bağlantısı kurmak için kullanılan, SSL ile etkinleştirilen istemci uygulaması/sürücüsü için kök sertifikayı değiştiriyor. [Geçerli kök sertifika](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) , standart bakım ve güvenlik en iyi yöntemlerinin bir parçası olarak 26 Ekim 2020 tarihinde sona ermek üzere ayarlanmıştır. Bu makale, yaklaşan değişiklikler hakkında daha fazla ayrıntıyı, etkilenecek kaynakları ve uygulamanızın veritabanı sunucunuza bağlantıyı korumasından emin olmak için gerekli adımları sağlar.

## <a name="what-update-is-going-to-happen"></a>Hangi güncelleştirme gerçekleşecektir?

[Sertifika yetkilisi (CA) tarayıcı Forumu](https://cabforum.org/) , CA satıcıları tarafından verilen birden çok sertifikanın uyumsuz olması için en son yayımlanmış raporlar.

Sektörün uyumluluk gereksinimlerine göre, CA satıcıları uyumlu olmayan CA 'Lar için CA sertifikalarını iptal etmeyi, sunucuların uyumlu CA 'lar tarafından verilen sertifikaları kullanmalarını ve bu uyumlu CA 'lardan CA sertifikaları kullanmasını gerektirir. Azure SQL veritabanı & SQL yönetilen örneği şu anda bu uyumlu olmayan sertifikalardan birini kullandığından, istemci uygulamalarının SSL bağlantılarını doğrulamak için kullandığı, Azure SQL sunucularınız için olası etkiyi en aza indirmek için uygun eylemlerin (aşağıda açıklanmıştır) yapıldığından emin olmamız gerekir.

Yeni sertifika 26 Ekim 2020 ' den itibaren kullanılacaktır. Bir SQL istemcisinden (TrustServerCertificate = true) bağlanırken sunucu sertifikasının tam doğrulamasını kullanırsanız, SQL istemcinizin, 26 Ekim 2020 tarihinden önce yeni kök sertifikayı doğrulayacağından emin olmanız gerekir.

## <a name="how-do-i-know-if-my-application-might-be-affected"></a>Nasıl yaparım? uygulamamın etkilenip etkilenmediğini öğrensin mi?

SSL/TLS kullanan tüm uygulamalar ve SQL yönetilen örneği & Azure SQL veritabanı 'na bağlanmak için kök sertifikayı doğrulamak için kök sertifikayı güncelleştirmeniz gerekir. 

Şu anda SSL/TLS kullanmıyorsanız, uygulamanızın kullanılabilirliğine hiçbir etkisi yoktur. Bağlantı dizesine bakarak istemci uygulamanızın kök sertifikayı doğrulamaya çalıştığından emin olabilirsiniz. TrustServerCertificate açıkça true olarak ayarlandıysa, bundan etkilenmezsiniz.

İstemci sürücünüz, işletim sistemi sertifika deposu kullanıyorsa, sürücülerin büyük bölümü, işletim SISTEMI tarafından düzenli olarak tutuluyorsa, geçiş yaptığımız kök sertifika, güvenilen kök sertifika deponuzda zaten kullanılabilir olmalıdır. Baltimore CyberDigiCert GlobalRoot G2 olup olmadığını denetleyin ve olduğunu doğrulayın.

İstemci sürücünüz yerel dosya sertifika deposundan yararlanıyorsa, sertifikaların beklenmedik şekilde iptal edildiği veya bir sertifikayı güncelleştirme, iptal edilmiş olması nedeniyle uygulamanızın kullanılabilirliğinin kesintiye uğramasını önlemek için [**bağlantıyı sürdürmek Için ne yapmam gerekir**](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity) bölümüne başvurun.

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>Bağlantıyı sürdürmek için ne yapmam gerekir?

Sertifikaların beklenmedik şekilde iptal edildiği veya bir sertifikayı güncelleştirme, iptal edilmiş olması nedeniyle uygulamanızın kullanılabilirliğinin kesintiye uğramasını önlemek için aşağıdaki adımları izleyin:

*   Aşağıdaki bağlantılardan Baltimore CyberTrust kökünü & DigiCert GlobalRoot G2 kök CA 'sını indirin:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Hem **Baltimorecyıbertrustroot** hem de **DigiCertGlobalRootG2** sertifikaları dahil olmak üzere bir birleştirilmiş CA sertifika deposu oluşturun.

## <a name="what-can-be-the-impact"></a>Ne etkisi olabilir?
Burada belgelenen sunucu sertifikalarını doğrulayadıysanız, veritabanının ulaşılamaz olması nedeniyle uygulamanızın kullanılabilirliği kesintiye uğramış olabilir. Uygulamanıza bağlı olarak, aşağıdakiler dahil ancak bunlarla sınırlı olmamak üzere çeşitli hata iletileri alabilirsiniz:
*   Geçersiz sertifika/iptal edilmiş sertifika
*   Bağlantı zaman aşımına uğradı
*   Geçerliyse hata

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>SSL/TLS kullanmıyorum, yine de kök CA 'yı güncelleştirmem gerekir mi?
SSL/TLS kullanmıyorsanız, bu değişikliğe ilişkin hiçbir eylem gerekli değildir. Yine de yakında TLS zorlaması planlaması yaptığımız için en son TLS sürümünü kullanmaya başlamak için bir plan ayarlamanız gerekir.

### <a name="what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020"></a>Kök sertifikayı 26 Ekim 2020 ' den önce güncelleştirdiğimde ne olur?
Kök sertifikayı 30 Kasım 2020 tarihinden önce güncelleştirmediğinizi, SSL/TLS aracılığıyla bağlanan ve kök sertifika için doğrulama yapan uygulamalarınız Azure SQL veritabanı ile iletişim kuramayacaksınız & SQL yönetilen örneği ve uygulama Azure SQL veritabanı & SQL yönetilen örneği ile bağlantı sorunlarıyla karşılaşacaktır.

### <a name="do-i-need-to-plan-a-maintenance-downtime-for-this-changebr"></a>Bu değişiklik için bir bakım kapalı kalma süresi planlamanız gerekiyor mu?<BR>
Hayır. Buradaki değişiklik yalnızca sunucuya bağlanacak istemci tarafında olduğundan, bu değişiklik için burada gereken bakım kapalı kalma süresi yoktur.

### <a name="what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020"></a>Bu değişiklik için 26 Ekim 2020 tarihinden önce zamanlanan kapalı kalma süresi alamazsanız ne olur?
Sunucuya bağlanmak için kullanılan istemcilerin [buradaki](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity)çözüm bölümünde açıklandığı gibi sertifika bilgilerini güncelleştirmesi gerektiğinden, bu durumda sunucu için kapalı kalma süresine gerek kalmaz.

### <a name="if-i-create-a-new-server-after-november-30-2020-will-i-be-impacted"></a>30 Kasım 2020 ' den sonra yeni bir sunucu oluştururum, bundan etkilenmem gerekir mi?
26 Ekim 2020 ' den sonra oluşturulan sunucular için, SSL kullanarak bağlanmak üzere uygulamalarınız için yeni verilen sertifikayı kullanabilirsiniz.

### <a name="how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>Microsoft sertifikalarını ne sıklıkla güncelleştiriyor veya süre sonu ilkesi nedir?
Azure SQL veritabanı & SQL yönetilen örneği tarafından kullanılan bu sertifikalar, güvenilen sertifika yetkilileri (CA) tarafından sağlanır. Bu nedenle, Azure SQL veritabanı & SQL yönetilen örneği 'nde bu sertifikaların desteklenmesi, CA tarafından bu sertifikaların desteğine bağlıdır. Bununla birlikte, bu örnekte olduğu gibi, önceden tanımlanmış bu sertifikalarda, en erken düzeltilmelidir.

### <a name="if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-primary-server-or-all-the-read-replicas"></a>Okuma çoğaltmaları kullanıyorum, bu güncelleştirmeyi yalnızca birincil sunucuda mi yoksa tüm okuma çoğaltmalarıyla mi gerçekleştirmem gerekir?
Bu güncelleştirme bir istemci tarafı değişikliği olduğundan, istemci Çoğaltma sunucusundan veri okumak için kullanılıyorsa, bu istemciler için değişiklikleri de uygulamamız gerekecektir. 

### <a name="do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>SSL kullanıldığını doğrulamak için sunucu tarafı sorgumuz var mı?
Bu yapılandırma istemci tarafı olduğundan, bilgi sunucu tarafında kullanılamaz.

### <a name="what-if-i-have-further-questions"></a>Başka sorularım varsa ne yapmalıyım?
Destek planınız varsa ve teknik yardıma ihtiyacınız varsa Azure destek isteği oluşturun, bkz. [Azure destek isteği oluşturma](../../azure-portal/supportability/how-to-create-azure-support-request.md).