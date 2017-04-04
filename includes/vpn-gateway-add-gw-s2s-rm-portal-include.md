1. Portal sayfasının sol tarafına **+** simgesine tıklayın ve arama alanına ‘Sanal Ağ Geçidi’ yazın. **Sonuçlar** alanında **Sanal Ağ Geçidi**’ni bulup tıklayın. **Sanal ağ geçidi** dikey penceresinin altındaki **Oluştur**’a tıklayın. Bu işlem **Sanal ağ geçidi oluştur** dikey penceresini açar.
2. **Sanal ağ geçidi oluştur** dikey penceresinde, sanal ağ geçidinize ait değerleri girin.

    ![Sanal ağ geçidi oluştur dikey penceresinin alanları](./media/vpn-gateway-add-gw-s2s-rm-portal-include/newgw.png "Yeni ağ geçidi")
3. **Ad**: Ağ geçidinizi adlandırın. Bir ağ geçidi alt ağını adlandırmayla aynı değildir. Bu ad, oluşturduğunuz ağ geçidi nesnesinin adıdır.
4. **Ağ geçidi türü**: **VPN**’i seçin. VPN ağ geçitleri, **VPN** sanal ağ geçidi türünü kullanır. 
5. **VPN türü**: Yapılandırmanızla ilgili belirtilen VPN türünü seçin. Çoğu yapılandırma, Yol Tabanlı bir VPN türü gerektirir.
6. **SKU**: Açılır listeden ağ geçidi SKU’sunu seçin. Açılır listede sıralanan SKU’lar seçtiğiniz VPN türüne bağlıdır.
7. **Konum**: Konumu görmek için kaydırmanız gerekebilir. **Konum** alanını, sanal ağınızın bulunduğu konumu işaret edecek şekilde ayarlayın. Konum, sanal ağınızın bulunduğu bölgeye işaret etmiyorsa, sonraki adım olan 'Sanal ağ seçin' açılır menüsünde sanal ağ görüntülenmez.
8. **Sanal ağ**: Bu ağ geçidini eklemek istediğiniz sanal ağı seçin. **Sanal ağ**'a tıklayarak **Sanal ağ seçin** dikey penceresini açın. VNet'i seçin. Sanal ağınızı görmüyorsanız **Konum** alanının, sanal ağınızın bulunduğu bölgeyi işaret ettiğinden emin olun.
9. **Genel IP adresi**: Bu dikey pencere bir genel IP adresi nesnesi oluşturur ve daha sonra bu nesneye dinamik olarak bir genel IP adresi atanır. **Genel IP adresi**'ne tıklayarak **Genel IP adresi seçin** dikey penceresini açın. **+Yeni Oluştur**'a tıklayarak **Genel IP adresi oluştur** dikey penceresini açın. Genel IP adresiniz için bir ad girin. Bu dikey penceredeki değişiklikleri kaydetmek için **Tamam**’a tıklayın.

    ![Genel IP oluşturma](./media/vpn-gateway-add-gw-s2s-rm-portal-include/createpip.png "PIP oluşturma")
10. **Abonelik**: Doğru aboneliğin seçildiğini doğrulayın.
11. **Kaynak grubu**: Bu ayar, seçtiğiniz Sanal Ağ tarafından belirlenir. 
12. Önceki ayarları belirttikten sonra **Konum**'u ayarlamayın.
13. Ayarları doğrulayın. Ağ geçidinizin panoda görünmesini istiyorsanız dikey pencerenin altında yer alan **Panoya sabitle** seçeneğini belirleyebilirsiniz.
14. Ağ geçidi oluşturmaya başlamak için **Oluştur**’a tıklayın. Ayarlar doğrulandıktan sonra, panoda "Sanal ağ geçidi dağıtma" kutucuğunu görürsünüz. Bir ağ geçidi oluşturma 45 dakika kadar sürebilir. Tamamlanma durumunu görmek için portal sayfanızı yenilemeniz gerekebilir.
    
    ![Ağ geçidi oluşturma](./media/vpn-gateway-add-gw-s2s-rm-portal-include/creategw.png "Ağ geçidi oluşturma")
15. Ağ geçidi oluşturulduktan sonra, portalda sanal ağa bakarak bu ağ geçidine atanmış IP adresini görüntüleyin. Ağ geçidi bağlı bir cihaz gibi görüntülenir. Daha fazla bilgi görüntülemek için bağlı cihaza (sanal ağ geçidiniz) tıklayabilirsiniz.