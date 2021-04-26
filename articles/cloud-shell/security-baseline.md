---
title: Cloud Shell için Azure Güvenlik temeli
description: Cloud Shell güvenlik temeli, Azure Güvenlik kıyaslaması 'nda belirtilen güvenlik önerilerini uygulamaya yönelik yordamsal kılavuz ve kaynaklar sağlar.
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b0045eacea7fdc13abdad35b5567eba3e994dcf9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558882"
---
# <a name="azure-security-baseline-for-cloud-shell"></a>Cloud Shell için Azure Güvenlik temeli

Bu güvenlik temeli, Cloud Shell için [Azure Güvenlik kıyaslama sürüm 1,0](../security/benchmarks/overview-v1.md) ' den rehberlik uygular. Azure Güvenlik Karşılaştırması, Azure üzerindeki bulut çözümlerinizin güvenliğini sağlamaya yönelik öneriler sunar.
İçerik, Azure Güvenlik kıyaslaması tarafından tanımlanan **güvenlik denetimlerine** ve Cloud Shell için geçerli olan ilgili kılavuza göre gruplandırılır. Cloud Shell için geçerli olmayan **denetimler** dışlandı.

 
Cloud Shell Azure Güvenlik kıyaslaması ile tamamen nasıl eşlendiğini görmek için, [tam Cloud Shell güvenlik temeli eşleme dosyasına](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)bakın.

## <a name="network-security"></a>Ağ Güvenliği

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Ağ Güvenliği](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: sanal ağlar içindeki Azure kaynaklarını koruma

**Rehberlik**: müşteriler, müşterinin sahip olduğu sanal ağa Azure Cloud Shell dağıtabilir.

Azure Cloud Shell müşterinin sahip olduğu sanal ağa dağıttığınızda, var olan bir sanal ağ oluşturmanız veya kullanmanız gerekir. Seçilen sanal ağın alt ağlarına ve uygulamanızın güvenilen bağlantı noktalarına ve kaynaklarına özgü olarak yapılandırılmış ağ erişim denetimlerine uygulanmış bir ağ güvenlik grubu olduğundan emin olun.

- [Azure sanal ağına Cloud Shell dağıtma](private-vnet.md)

- [Güvenlik kuralları ile ağ güvenlik grubu oluşturma](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Güvenlik duvarını dağıtma ve yapılandırma](../firewall/tutorial-firewall-deploy-portal.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="identity-and-access-control"></a>Kimlik ve Erişim Denetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kimlik ve Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Azure Active Directory ile çoklu oturum açma (SSO) kullanın

**Rehberlik**: Azure Cloud Shell, Azure Portal erişmek için kullanılan yetkilendirmeyi kullanan tarayıcı tabanlı bir komut satırı deneyimidir, bu durumda Azure Portal ' de bir SSO da Cloud Shell ile kimliğinizi doğrular.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: tüm Azure Active Directory tabanlı erişim için Multi-Factor Authentication kullanın

**Rehberlik**: Azure Cloud Shell, Azure Portal erişmek için kullanılan yetkilendirmeyi kullanan tarayıcı tabanlı bir komut satırı deneyimidir, bu durumda Azure Portal bağlanmak için gereken tüm çok faktörlü kimlik doğrulaması Cloud Shell için de gerekecektir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="vulnerability-management"></a>Güvenlik Açığı Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenlik açığı yönetimi](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: otomatikleştirilmiş güvenlik açığı tarama araçlarını çalıştırma

**Rehberlik**: Azure Cloud Shell, bulut kaynaklarının etkileşimli yönetimi için kullanılan tarayıcı tabanlı bir komut satırı deneyimidir.  Her bir müşteri kapsayıcısı kısa ömürlü, her oturum için yeni bir kapsayıcı kullanılır.  Kapsayıcı görüntüleri Cloud Shell ekibi tarafından izlenir ve güncelleştirilir.

Azure Cloud Shell, müşterilerin kendi araçlarını veya yazılımlarını kurumsal ihtiyaçlarına göre kendi görüntüsüne yüklemesine olanak tanır.

Müşteriler, ortamda çalışan yazılımlara karşı otomatik güvenlik açığı tarama araçları 'nı çalıştırmaktan sorumludur.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: üçüncü taraf yazılım başlıkları için otomatik düzeltme eki yönetimi çözümü dağıtma

**Rehberlik**: uygulanamaz; Azure Cloud Shell, bulut kaynaklarının etkileşimli yönetimi için kullanılan tarayıcı tabanlı bir komut satırı deneyimidir.  Her bir müşteri kapsayıcısı kısa ömürlü, her oturum için yeni bir kapsayıcı kullanılır.  Kapsayıcı görüntüleri Cloud Shell ekibi tarafından izlenir ve güncelleştirilir.

Azure Cloud Shell, müşterilerin kendi araçlarını veya yazılımlarını kurumsal ihtiyaçlarına göre kendi görüntüsüne yüklemesine olanak tanır.

Müşteriler, ortamlarında çalışan yazılım düzeltme eki yönetiminden sorumludur.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: geri dönüş güvenlik açığı taramalarını karşılaştırın

**Rehberlik**: Azure Cloud Shell, bulut kaynaklarının etkileşimli yönetimi için kullanılan tarayıcı tabanlı bir komut satırı deneyimidir.  Her bir müşteri kapsayıcısı kısa ömürlü, her oturum için yeni bir kapsayıcı kullanılır.  Kapsayıcı görüntüleri Cloud Shell ekibi tarafından izlenir ve güncelleştirilir.

Azure Cloud Shell, müşterilerin kendi araçlarını veya yazılımlarını kurumsal ihtiyaçlarına göre kendi görüntüsüne yüklemesine olanak tanır.

Müşteriler, yazılım güvenlik açıkları aracılığıyla bulunan güvenlik açıklarını düzeltmekten sorumludur. Bir güvenlik açığının düzeltildiğini doğrulamak için tarama sonuçlarını tutarlı aralıklarla dışarı aktarın ve sonuçları önceki taramalarla karşılaştırın. Azure Güvenlik Merkezi tarafından önerilen güvenlik açığı yönetimi önerilerini kullanırken, geçmiş tarama verilerini görüntülemek için seçili çözümün portalına de Pivot ekleyebilirsiniz.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: bulunan güvenlik açıklarının düzeltilmesine öncelik vermek için risk derecelendirme işlemi kullanın

**Rehberlik**: Azure Cloud Shell, bulut kaynaklarının etkileşimli yönetimi için kullanılan tarayıcı tabanlı bir komut satırı deneyimidir.  Her bir müşteri kapsayıcısı kısa ömürlü, her oturum için yeni bir kapsayıcı kullanılır.  Kapsayıcı görüntüleri Cloud Shell ekibi tarafından izlenir ve güncelleştirilir.

Azure Cloud Shell, müşterilerin kendi araçlarını veya yazılımlarını kurumsal ihtiyaçlarına göre kendi görüntüsüne yüklemesine olanak tanır.

Müşteriler, yazılım güvenlik açıkları aracılığıyla bulunan güvenlik açıklarını düzeltmekten sorumludur.  Ortak risk Puanlama programını (örneğin, ortak güvenlik açığı Puanlama sistemi) veya üçüncü taraf tarama aracınız tarafından sunulan varsayılan risk derecelendirmelerini kullanın. 

- [NıST yayını--ortak güvenlik açığı Puanlama sistemi](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="inventory-and-asset-management"></a>Envanter ve Varlık Yönetimi

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: envanter ve varlık yönetimi](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: onaylanan Azure kaynakları envanterini tanımlama ve sürdürme

**Rehberlik**: Azure Cloud Shell, bulut kaynaklarının etkileşimli yönetimi için kullanılan tarayıcı tabanlı bir komut satırı deneyimidir.  Her bir müşteri kapsayıcısı kısa ömürlü, her oturum için yeni bir kapsayıcı kullanılır.  Kapsayıcı görüntüleri ve araçları Cloud Shell ekibi tarafından izlenir ve güncelleştirilir.  Müşteri, kendi araçlarını kurumsal ihtiyaçlarına göre kendi görüntüsüne yükleyebiliyor ve Araçlar `sudo` yüklemesi sırasında izin gerektirmez.

Müşteriler, Kurumsal gereksinimlerinize göre Azure Cloud Shell aracılığıyla yüklenen onaylanan yazılımın envanterini oluşturmak için önerilir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: işlem kaynakları içindeki onaylanmamış yazılım uygulamaları için izleyici

**Rehberlik**: Azure Cloud Shell, müşterinin sahip olduğu varlıkları olmayan ücretsiz bir hizmettir.  Kapsayıcı görüntüleri ve araçları Cloud Shell ekibi tarafından izlenir ve güncelleştirilir. 

Azure Cloud Shell, müşterilerin kendi araçlarını veya yazılımlarını kurumsal ihtiyaçlarına göre kendi görüntüsüne yüklemesine olanak tanır.

Müşteriler, kuruluş ilkesi başına onaylandıklarından emin olmak için ortamda çalışan yazılım uygulamalarını izlemeye sorumludur.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: onaylanmamış Azure kaynaklarını ve yazılım uygulamalarını kaldırma

**Rehberlik**: Azure Cloud Shell, müşterinin sahip olduğu varlıkları olmayan ücretsiz bir hizmettir.  Kapsayıcı görüntüleri ve araçları Cloud Shell ekibi tarafından izlenir ve güncelleştirilir. 

Azure Cloud Shell, müşterilerin kendi araçlarını veya yazılımlarını kurumsal ihtiyaçlarına göre kendi görüntüsüne yüklemesine olanak tanır.

Müşteriler, onaylanmamış yazılımın kuruluş ilkesi başına yönetilmesinden emin olmak için ortamda çalışan yazılım uygulamalarını izlemeye sorumludur.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="68-use-only-approved-applications"></a>6,8: yalnızca onaylanan uygulamaları kullan

**Rehberlik**: Azure Cloud Shell, müşterinin sahip olduğu varlıkları olmayan ücretsiz bir hizmettir.  Kapsayıcı görüntüleri ve araçları Cloud Shell ekibi tarafından izlenir ve güncelleştirilir.  Belirli araçlar müşteri tarafından kaldırılmayabilir.

Azure Cloud Shell, müşterilerin kendi araçlarını veya uygulamalarını kuruluş gereksinimlerine göre kendi görüntüsüne yüklemesine olanak tanır.

Müşteriler, kuruluş ilkesi başına onaylandıklarından emin olmak için ortamda çalışan uygulamaları izlemeye sorumludur.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: onaylanan yazılım başlıkları envanterini koruyun

**Rehberlik**: uygulanamaz; Azure Cloud Shell, bulut kaynaklarının etkileşimli yönetimi için kullanılan tarayıcı tabanlı bir komut satırı deneyimidir.  Her bir müşteri kapsayıcısı kısa ömürlü, her oturum için yeni bir kapsayıcı kullanılır.  Kapsayıcı görüntüleri Cloud Shell ekibi tarafından izlenir ve güncelleştirilir.

Azure Cloud Shell, müşterilerin kendi araçlarını veya yazılımlarını kurumsal ihtiyaçlarına göre kendi görüntüsüne yüklemesine olanak tanır.

Müşteriler, kuruluş ilkesi başına yazılım onaylı olduklarından emin olmak için ortamda çalışan onaylanan yazılımın envanterini sürdürmekten sorumludur.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: kullanıcıların işlem kaynaklarında betikleri yürütme yeteneğini sınırlayın

**Rehberlik**: Azure Cloud Shell, bulut kaynaklarının etkileşimli yönetimi için kullanılan tarayıcı tabanlı bir komut satırı deneyimidir.  Cloud Shell işlevi içinde gerçekleştirilen eylemler, yerel bir ortamda çalıştırılan aynı araçlardan veya dillerden alınan eylemlerle aynı şekilde çalışır.  Bireysel araçlar ve dillerin eylemleri sınırlandırılmalıdır, müşteriler Cloud Shell erişimi kısıtlayamaz veya bir kullanıcının kullanımına açık olanları kısıtlayabilir.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: yüksek riskli uygulamaları fiziksel olarak veya mantıksal olarak ayırt edin

**Rehberlik**: Azure Cloud Shell müşteri sanal ağında yalıtılabilir.

- [Azure sanal ağına Cloud Shell dağıtma](private-vnet.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="secure-configuration"></a>Güvenli Yapılandırma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: güvenli yapılandırma](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: istenmeyen kimlik bilgisi pozlamasını ortadan kaldırın

**Rehberlik**: Cloud Shell, betiklerin ' de çalıştırılmasını, içinde yazılmasını ve Cloud Shell ortamına yüklenmesini sağlar.  Kimlik bilgilerinin Azure Key Vault içine taşınması önerimiz olur.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="malware-defense"></a>Kötü Amaçlı Yazılımdan Koruma

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: kötü amaçlı yazılımdan koruma](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: merkezi olarak yönetilen kötü amaçlı yazılımdan koruma yazılımı kullanma

**Rehberlik**: Azure Cloud Shell, bulut kaynaklarının etkileşimli yönetimi için kullanılan tarayıcı tabanlı bir komut satırı deneyimidir.  Her bir müşteri kapsayıcısı kısa ömürlü, her oturum için yeni bir kapsayıcı kullanılır.  Kapsayıcı görüntüleri ve araçları Cloud Shell ekibi tarafından izlenir ve güncelleştirilir.  Müşteri, kendi araçlarını kurumsal ihtiyaçlarına göre kendi görüntüsüne yükleyebiliyor ve Araçlar `sudo` yüklemesi sırasında izin gerektirmez.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: kötü amaçlı yazılımdan koruma yazılımlarının ve imzaların güncelleştirildiğinden emin olun

**Rehberlik**: Azure Cloud Shell, bulut kaynaklarının etkileşimli yönetimi için kullanılan tarayıcı tabanlı bir komut satırı deneyimidir.  Her bir müşteri kapsayıcısı kısa ömürlü, her oturum için yeni bir kapsayıcı kullanılır.  Kapsayıcı görüntüleri ve araçları Cloud Shell ekibi tarafından izlenir ve güncelleştirilir.  Müşteri, kendi araçlarını kurumsal ihtiyaçlarına göre kendi görüntüsüne yükleyebiliyor ve Araçlar `sudo` yüklemesi sırasında izin gerektirmez.

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="incident-response"></a>Olay Yanıtı

*Daha fazla bilgi için bkz. [Azure Güvenlik Karşılaştırması: Olay Yanıtı](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: olay yanıtı kılavuzu oluşturma

**Rehberlik**: Kuruluşunuz için bir olay yanıt kılavuzu oluşturun. Tüm personelin rollerine ek olarak algılama aşamasından olay sonrası gözden geçirme aşamasına kadar tüm olay işleme/yönetim aşamalarını tanımlayan yazılı olay yanıt planları bulunduğundan emin olun.

- [Azure Güvenlik Merkezi 'nde Iş akışı otomasyonlarını yapılandırma](../security-center/security-center-planning-and-operations-guide.md) 

- [Kendi güvenlik olay yanıtı işleminizi oluşturma kılavuzu](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Güvenlik Yanıt Merkezi 'nin bir olayın anatomisi](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Müşteri, kendi olay yanıt planının oluşturulmasına yardımcı olması için NıST 'nin bilgisayar güvenliği olay Işleme kılavuzunu da kullanabilir](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: olay Puanlama ve öncelik belirlemesi prosedürü oluşturma

**Rehberlik**: Güvenlik Merkezi, ilk olarak hangi uyarıların araştırılması gerektiğini önceliklendirmenize yardımcı olmak için her bir uyarıya önem derecesi atar. Önem derecesi, uyarı veren etkinliğin arkasında kötü amaçlı bir amaç olduğunu ve uyarıyı vermek için ne kadar uygun güvenlik merkezi 'nin bulunması ya da analizin olduğunu temel alır.

Ayrıca, abonelikleri açıkça işaretleyin (örn. üretim, üretim dışı) ve Azure kaynaklarını net bir şekilde tanımlamak ve kategorilere ayırmak için bir adlandırma sistemi oluşturun.

- [Azure Güvenlik Merkezi'nde güvenlik uyarıları](../security-center/security-center-alerts-overview.md) 

- [Azure kaynaklarınızı düzenlemek için etiketleri kullanma](../azure-resource-manager/management/tag-resources.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="103-test-security-response-procedures"></a>10,3: test Güvenliği Yanıt yordamları

**Rehberlik**: sistem olay yanıt yeteneklerini düzenli bir temposunda test etmek için alıştırmaları gerçekleştirin. Zayıf noktaları ve açıkları belirleyip planı gerektiği şekilde gözden geçirin.

- [NıST 'nin yayını: BT planları ve özellikleri için test, eğitim ve alıştırma programlarını inceleyin](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: güvenlik olaylarına ilişkin iletişim ayrıntılarını sağlayın ve güvenlik olayları için uyarı bildirimleri yapılandırın

**Rehberlik**: Microsoft Güvenlik Yanıt MERKEZI (MSRC), müşterinin verilerine izinsiz veya yetkisiz bir taraf tarafından erişildiğini belirlerse, Microsoft tarafından sizinle iletişim kurmak için güvenlik olayı iletişim bilgileri kullanılacaktır. Sorunların çözümlendiğinden emin olmak için gerçesonra olayları gözden geçirin.

- [Azure Güvenlik Merkezi güvenlik Ilgili kişisini ayarlama](../security-center/security-center-provide-security-contact-details.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: güvenlik uyarılarını olay yanıt sisteminizle birleştirme

**Rehberlik**: sürekli dışa aktarma özelliğini kullanarak Azure Güvenlik Merkezi uyarılarınızı ve önerilerinizi dışarı aktarın. Sürekli dışa aktarma, uyarıları ve önerileri el ile veya devam eden sürekli bir biçimde dışa aktarmanız sağlar. Azure Güvenlik Merkezi veri bağlayıcısını kullanarak uyarıları Azure Sentinel 'e akışını sağlayabilirsiniz.

- [Sürekli dışarı aktarmayı yapılandırma](../security-center/continuous-export.md) 

- [Uyarıların Azure Sentinel’e akışını yapma](../sentinel/connect-azure-security-center.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: güvenlik uyarılarına yanıtı otomatikleştirme

**Rehberlik**: güvenlik uyarılarında ve önerilerinde "Logic Apps" aracılığıyla yanıtları otomatik olarak tetiklemek Için Azure Güvenlik Merkezi 'Nde Iş akışı Otomasyonu özelliğini kullanın.

- [Iş akışı otomasyonu ve Logic Apps yapılandırma](../security-center/workflow-automation.md)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="penetration-tests-and-red-team-exercises"></a>Sızma Testleri ve Red Team Alıştırmaları

*Daha fazla bilgi için bkz. [Azure Güvenlik kıyaslaması: Penetme testleri ve Red ekibi alıştırmaları](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: Azure kaynaklarınızın düzenli olarak sızma testini gerçekleştirin ve tüm kritik güvenlik bulgularını düzeltmeye dikkat edin

**Rehberlik**: Penettim testlerinizin Microsoft ilkelerini ihlal etmediğinden emin olmak Için Microsoft bulut Penme test kurallarını izleyin. Microsoft tarafından yönetilen bulut altyapısına, hizmetlere ve uygulamalara yönelik kırmızı takım ve canlı site sızma testi gerçekleştirmek için Microsoft'un stratejisini ve yürütme sürecini kullanın.

- [Sızma Testi Etkileşim Kuralları](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Bulut ile Kırmızı Takım Oluşturma](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Sorumluluk**: Müşteri

**Azure Güvenlik Merkezi izleme**: yok

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Azure Güvenlik Karşılaştırması 2.0 sürümüne genel bakış](../security/benchmarks/overview.md)
- [Azure güvenlik temelleri](../security/benchmarks/security-baselines-overview.md) hakkında daha fazla bilgi edinin