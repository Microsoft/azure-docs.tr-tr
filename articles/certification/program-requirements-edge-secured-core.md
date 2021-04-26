---
title: Edge güvenli-çekirdek sertifika gereksinimleri
description: Edge güvenli-çekirdek sertifika programı gereksinimleri
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Edge Secured-core Certification Requirements
ms.service: certification
ms.openlocfilehash: 5bb02f939bb63fd1c6365fd4570996f09119e958
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166913"
---
# <a name="azure-certified-device---edge-secured-core-preview"></a>Azure Sertifikalı cihaz-Edge güvenli-çekirdek (Önizleme) #

## <a name="edge-secured-core-certification-requirements"></a>Edge Secured-Core sertifika gereksinimleri ##

Bu belgede, sertifikayı tamamlamaya ve Azure IoT cihaz kataloğunda uç güvenli çekirdek etiketiyle bir cihaz listelemesi için karşılanacak cihaza özgü yetenekler ve gereksinimler özetlenmektedir.

### <a name="program-purpose"></a>Program amacı ###
Edge güvenli-çekirdek, Linux veya Windows 10 IoT gibi tam bir işletim sistemi çalıştıran IoT cihazları için Azure Sertifikalı cihaz programında artımlı bir sertifikadır. Bu program, cihaz ortaklarının ek bir güvenlik ölçütü sunarak cihazlarını ayırt etmesini sağlar. Bu ölçütlere uyan cihazlar bu taahhüt sağlar:
1. Donanım tabanlı cihaz kimliği 
2. Sistem bütünlüğünü zorlama özelliği 
3. Güncel kalır ve uzaktan yönetilebilir
4. Verilerin Rest korumasını sağlar
5. Verilerin aktarım sırasında korunmasını sağlar
6. Yerleşik güvenlik Aracısı ve sağlamlaştırma
### <a name="requirements"></a>Gereksinimler ###

---
|Name|SecuredCore. yerleşik. güvenlik|
|:---|:---|
|Durum|Gerekli|
|Açıklama|Testin amacı, cihazların IoT için Azure Defender 'a veri göndererek güvenlik bilgilerini ve olayları bildirebileceği şekilde emin olmak içindir.|
|Hedef kullanılabilirliği|2021|
|Uygulanan Öğe|Herhangi bir cihaz|
|İşletim Sistemi|Belirsiz|
|Doğrulama türü|El ile/araçlar|
|Doğrulama |Cihaz güvenlik günlükleri ve uyarılar üretmelidir. Azure Güvenlik Merkezi 'ne cihaz günlükleri ve uyarı iletileri.<ol><li>GitHub 'dan güvenlik aracısını indirme ve dağıtma</li><li>IoT için Azure Defender 'dan uyarı iletisini doğrulayın.</li></ol>|
|Kaynaklar|[IoT için Azure docs IoT Defender](../defender-for-iot/how-to-configure-agent-based-solution.md)|

---
|Name|SecuredCore. encryption. Storage|
|:---|:---|
|Durum|Gerekli|
|Açıklama|Gizli verilerin doğrulanması için testin amacı, geçici olmayan depolamada şifrelenebilir.|
|Hedef kullanılabilirliği|2021|
|Uygulanan Öğe|Herhangi bir cihaz|
|İşletim Sistemi|Belirsiz|
|Doğrulama türü|El ile/araçlar|
|Doğrulama|Depolama şifrelemenin etkinleştirildiğinden ve varsayılan algoritmanın, anahtar uzunluğu 128 bit veya daha yüksek olan XTS-AES olduğundan emin olmak için bir cihazın araç kümesi aracılığıyla doğrulanması.|
|Kaynaklar||

---
|Name|SecuredCore. Hardware. SecureEnclave|
|:---|:---|
|Durum|İsteğe Bağlı|
|Description|Güvenli bir kuşın varlığını doğrulayan ve şifreleme güvenli bir aracıdan erişilebilen test amacı.|
|Hedef kullanılabilirliği|2021|
|Uygulanan Öğe|Herhangi bir cihaz|
|İşletim Sistemi|Belirsiz|
|Doğrulama türü|El ile/araçlar|
|Doğrulama|Azure Güvenlik aracısının güvenli şifreleme ile iletişim kurabildiğinden emin olmak için cihazın araç seti aracılığıyla doğrulanması|
|Kaynaklar|https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md|

---
|Name|SecuredCore. Hardware. Identity|
|:---|:---|
|Durum|Gerekli|
|Açıklama|Testin amacı, cihaz tanımladığını doğrulamak için donanımdır.|
|Hedef kullanılabilirliği|2021|
|Uygulanan Öğe|Herhangi bir cihaz|
|İşletim Sistemi|Belirsiz|
|Doğrulama türü|El ile/araçlar|
|Doğrulama|Cihazın bir TPM 'nin mevcut olduğundan ve TPM onay anahtarı kullanılarak IoT Hub aracılığıyla sağlanabildiğinden emin olmak için cihazın araç kümesi aracılığıyla doğrulanması.|
|Kaynaklar|[DPS ile otomatik sağlamayı kurma](../iot-dps/quick-setup-auto-provision.md)|

---
|Name|SecuredCore. Update|
|:---|:---|
|Durum|Gerekli|
|Açıklama|Testin amacı, cihazın üretici yazılımını ve yazılımlarını alabileceğini ve güncelleştirediğini doğrulamaktır.|
|Hedef kullanılabilirliği|2021|
|Uygulanan Öğe|Herhangi bir cihaz|
|İşletim Sistemi|Belirsiz|
|Doğrulama türü|El ile/araçlar|
|Doğrulama|İş ortağı onayı, Microsoft Update, Azure cihaz güncelleştirmesi veya diğer onaylanmış hizmetler aracılığıyla cihaza bir güncelleştirme gönderebildiğine ilişkin bir onaylama.|
|Kaynaklar|[IoT Hub için cihaz güncelleştirmesi](../iot-hub-device-update/index.yml)|

---
|Name|SecuredCore.Manageability.Configurlama|
|:---|:---|
|Durum|Gerekli|
|Açıklama|Testin amacı, cihazların uzaktan güvenlik yönetimini desteklediğini doğrulamaktır.|
|Hedef kullanılabilirliği|2021|
|Uygulanan Öğe|Herhangi bir cihaz|
|İşletim Sistemi|Belirsiz|
|Doğrulama türü|El ile/araçlar|
|Doğrulama|Cihazın uzaktan yönetilebilir ve özellikle güvenlik yapılandırmalarına olanak sağlamak için araç seti aracılığıyla doğrulanacak cihaz. Ve durum, IoT için IoT Hub/Azure Defender 'a geri bildirilir.|
|Kaynaklar||

---
|Name|SecuredCore. yönetilebilirlik. Reset|
|:---|:---|
|Durum|Gerekli|
|Açıklama|Bu testin amacı, cihazı iki kullanım örneğine karşı doğrulamak: a) bir sıfırlama (Kullanıcı verilerini kaldırma, Kullanıcı yapılandırmalarını kaldırma), b) cihazı bir güncelleştirme durumunda en son bilinen duruma geri yükleme olanağı.|
|Hedef kullanılabilirliği|2021|
|Uygulanan Öğe|Herhangi bir cihaz|
|İşletim Sistemi|Belirsiz|
|Doğrulama türü|El ile/araçlar|
|Doğrulama|Aygıtın bu işlevselliği desteklediği bir araç takımı ve gönderilen belgelerin bir birleşimi aracılığıyla doğrulanacak cihaz. Cihaz üreticisi, uzaktan sıfırlamayı veya yalnızca yerel sıfırlamayı desteklemek üzere bu özellikleri uygulayıp uygulamamayı belirleyebilir.|
|Kaynaklar||

---
|Name|SecuredCore. Updates. Duration|
|:---|:---|
|Durum|Gerekli|
|Açıklama|Bu ilkenin amacı, cihazın güvende kalmasını sağlamaktır.|
|Hedef kullanılabilirliği|2021|
|Uygulanan Öğe|Herhangi bir cihaz|
|İşletim Sistemi|Belirsiz|
|Doğrulama türü|El ile|
|Doğrulama|Cihazların sertifikalandırılması, gönderim tarihinden itibaren 60 ay boyunca cihazların güncel tutulması için gerekli olacaktır. Satınalmacının ve cihazların tek bir şekilde kullanabildiği belirtimler, yazılımlarının güncelleştirileceği süreyi göstermelidir.|
|Kaynaklar||

---
|Name|SecuredCore. Policy. vuln. açıklanması|
|:---|:---|
|Durum|Gerekli|
|Açıklama|Bu ilkenin amacı, üründe güvenlik açıklarına ilişkin raporların toplanması ve dağıtılması için bir mekanizma olduğundan emin olmak içindir.|
|Hedef kullanılabilirliği|2021|
|Uygulanan Öğe|Herhangi bir cihaz|
|İşletim Sistemi|Belirsiz|
|Doğrulama türü|El ile|
|Doğrulama|Sertifikalı cihazlara yönelik güvenlik açığı raporları gönderme ve alma işlemindeki belgeler gözden geçirilir.|
|Kaynaklar||

---
|Name|SecuredCore. Policy. vuln. düzeltmelerde|
|:---|:---|
|Durum|Gerekli|
|Açıklama|Bu ilkenin amacı, yüksek/kritik güvenlik açıklarının (CVSS 3,0 kullanılarak), düzeltilmesi gereken 180 gün içinde ele aldığından emin olmak için geçerlidir.|
|Hedef kullanılabilirliği|2021|
|Uygulanan Öğe|Herhangi bir cihaz|
|İşletim Sistemi|Belirsiz|
|Doğrulama türü|El ile|
|Doğrulama|Sertifikalı cihazlara yönelik güvenlik açığı raporları gönderme ve alma işlemindeki belgeler gözden geçirilir.|
|Kaynaklar||


---
|Name|SecuredCore. encryption. TLS|
|:---|:---|
|Durum|Gerekli|
|Açıklama|Testin amacı, gerekli TLS sürümleri ve şifre paketleri için desteği doğrulamak içindir.|
|Hedef kullanılabilirliği|2021|
|Uygulanan Öğe|Herhangi bir cihaz|
|İşletim Sistemi|Belirsiz|
|Doğrulama türü|El ile/araçlar|
Doğrulama|Cihazın en düşük TLS 1,2 sürümünü desteklediğinden emin olmak ve aşağıdaki gerekli TLS şifre paketlerini desteklemesi için araç seti aracılığıyla doğrulanacak cihaz.<ul><li>TLS_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_RSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256</li></ul>|
|Kaynaklar| [IoT Hub 'de TLS desteği](../iot-hub/iot-hub-tls-support.md) <br /> [Windows 10 ' da TLS şifre paketleri](https://docs.microsoft.com/windows/win32/secauthn/tls-cipher-suites-in-windows-10-v1903) |

---
|Name|SecuredCore. Protection. SignedUpdates|
|:---|:---|
|Durum|Gerekli|
|Açıklama|Testin amacı, güncelleştirmelerin imzalanması gerektiğini doğrulamaktır.|
|Hedef kullanılabilirliği|2021|
|Uygulanan Öğe|Herhangi bir cihaz|
|İşletim Sistemi|Belirsiz|
|Doğrulama türü|El ile/araçlar|
|Doğrulama|Düzgün imzalanmadığı ve onaylanmadığı takdirde, işletim sistemi, sürücüler, uygulama yazılımı, kitaplıklar, paketler ve bellenim güncelleştirmelerinin uygulanmadığından emin olmak üzere araç kümesi aracılığıyla doğrulanacak cihaz.
|Kaynaklar||

---
|Name|SecuredCore. bellenim. SecureBoot|
|:---|:---|
|Durum|Gerekli|
|Açıklama|Testin amacı, cihazın önyükleme bütünlüğünü doğrulamaktır.|
|Hedef kullanılabilirliği|2021|
|Uygulanan Öğe|Herhangi bir cihaz|
|İşletim Sistemi|Belirsiz|
|Doğrulama türü|El ile/araçlar|
|Doğrulama|Cihaz her önyüklendiğinde bellenim ve çekirdek imzalarının doğrulanması için araç seti aracılığıyla doğrulanacak cihaz. <ul><li>UEFı: güvenli önyükleme etkin</li><li>Uıboot: doğrulanan önyükleme etkin</li></ul>|
|Kaynaklar||

---
|Name|SecuredCore. Protection. CodeIntegrity|
|:---|:---|
|Durum|Gerekli|
|Açıklama|Bu testin amacı, bu cihazda kod bütünlüğünün kullanılabilir olduğunu doğrulamaktır.|
|Hedef kullanılabilirliği|2021|
|Uygulanan Öğe|Herhangi bir cihaz|
|İşletim Sistemi|Belirsiz|
|Doğrulama türü|El ile/araçlar|
|Doğrulama|Kod bütünlüğünün etkinleştirildiğinden emin olmak üzere araç kümesi aracılığıyla doğrulanacak cihaz. </br> Windows: HVCı </br> Linux: DM-Verity ve ıMA|
|Kaynaklar||

---
|Name|SecuredCore. Protection. NetworkServices|
|:---|:---|
|Durum|Gerekli|
|Açıklama|Testin amacı, ağdan girişi kabul eden uygulamaların yükseltilmiş ayrıcalıklarla çalışmadığını doğrulamaktır.|
|Hedef kullanılabilirliği|2021|
|Uygulanan Öğe|Herhangi bir cihaz|
|İşletim Sistemi|Belirsiz|
|Doğrulama türü|El ile/araçlar|
|Doğrulama|Ağ bağlantılarını kabul eden hizmetlerin SISTEM veya kök ayrıcalıklarıyla kullanılmadığından emin olmak için cihazın araç kümesi aracılığıyla doğrulanması.|
|Kaynaklar||

---
|Name|SecuredCore. Protection. temeller|
|:---|:---|
|Durum|Gerekli|
|Açıklama|Testin amacı sistemin bir temel güvenlik yapılandırmasına uygun olduğunu doğrulamaktır.|
|Hedef kullanılabilirliği|2021|
|Uygulanan Öğe|Herhangi bir cihaz|
|İşletim Sistemi|Belirsiz|
|Doğrulama türü|El ile/araçlar|
|Doğrulama|Defender ıOT sistem yapılandırmalarının kıyaslamalarını sağlamak üzere araç kümesi aracılığıyla doğrulanacak cihaz.|
|Kaynaklar| https://techcommunity.microsoft.com/t5/microsoft-security-baselines/bg-p/Microsoft-Security-Baselines <br> https://www.cisecurity.org/cis-benchmarks/ |

---
|Name|SecuredCore. bellenim. Protection|
|:---|:---|
|Durum|Gerekli|
|Açıklama|Testin amacı, cihazın üretici yazılımı güvenlik tehditlerine karşı yeterli düzeyde azaltmalarını sağlamaktır.|
|Hedef kullanılabilirliği|2021|
|Uygulanan Öğe|Herhangi bir cihaz|
|İşletim Sistemi|Belirsiz|
|Doğrulama türü|El ile/araçlar|
|Doğrulama|Aşağıdaki yaklaşımlardan biri aracılığıyla bellenim güvenlik tehditlerine karşı korunduğundan emin olmak için cihazın araç kümesi aracılığıyla doğrulanması: <ul><li>DRTM + UEFı yönetim modu azaltmaları</li><li>DRTM + UEFı yönetim modu sağlamlaştırma</li><li>SRTM + Runtime bellenim sağlamlaştırma kullanan onaylanan ILT</li></ul> |
|Kaynaklar| https://trustedcomputinggroup.org/ |

---
|Name|SecuredCore. bellenim. kanıtlama|
|:---|:---|
|Durum|Gerekli|
|Açıklama|Testin amacı, cihazın Microsoft Azure kanıtlama hizmetine uzaktan test kurmasını sağlamaktır.|
|Hedef kullanılabilirliği|2021|
|Uygulanan Öğe|Herhangi bir cihaz|
|İşletim Sistemi|Belirsiz|
|Doğrulama türü|El ile/araçlar|
|Doğrulama|Platform önyükleme günlüklerinin ve önyükleme etkinliğinin ölçümlerinin toplanabilmesi ve Microsoft Azure kanıtlama hizmetine uzaktan onaylanabilmesi için araç seti aracılığıyla doğrulanacak cihaz.|
|Kaynaklar| [Microsoft Azure Doğrulama](../attestation/index.yml) |

---
|Name|SecuredCore. Hardware. MemoryProtection|
|:---|:---|
|Durum|Gerekli|
|Açıklama|Testin amacı, DMA 'nın dışarıdan erişilebilen bağlantı noktalarında etkin olduğunu doğrulamaktır.|
|Hedef kullanılabilirliği|2021|
|Uygulanan Öğe|Herhangi bir cihaz|
|İşletim Sistemi|Belirsiz|
|Doğrulama türü|El ile/araçlar|
|Doğrulama|Cihazda DMA özellikli dış bağlantı noktaları varsa, ıOMMU veya SMMU 'nın etkin olduğunu ve bu bağlantı noktaları için yapılandırıldığını doğrulamak üzere araç kümesi.|
|Kaynaklar||

---
|Name|SecuredCore. Protection. Debug|
|:---|:---|
|Durum|Gerekli|
|Açıklama|Testin amacı, cihazdaki hata ayıklama işlevselliğinin devre dışı olduğunu doğrulamaktır.|
|Hedef kullanılabilirliği|2021|
|Uygulanan Öğe|Herhangi bir cihaz|
|İşletim Sistemi|Belirsiz|
|Doğrulama türü|El ile/araçlar|
|Doğrulama|Hata ayıklama işlevselliğinin etkinleştirmek için yetkilendirme gerektirdiğinden emin olmak üzere araç kümesi aracılığıyla doğrulanacak cihaz.|
|Kaynaklar||
