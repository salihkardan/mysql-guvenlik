# MySQL Sıkılaştırma

Bu döküman, MySQL kullanmayı planlayan kişilere yöneliktir. Özellikle bilgi varlığı güvenliğinin kritik olduğu projelerde, MySQL'in sıkılaştırma ayarları kesinlikle yapılmalıdır. Yapılmadığı durumlarda, MySQL de tutulan bilgilerin çalınması, değiştirilmesi gibi durumlarla karşı karşıya kalınabilir. 

Bu linkte MySQL'de bulunan ve bilinen açıklıkların listesini içermektedir. https://www.cvedetails.com/vulnerability-list/vendor_id-93/product_id-21801/Oracle-Mysql.html 

Döküman hazırlanırken birçok kaynak incelenmiş ve ortaya çıkan çalışma taranan kaynakların birleşimi olarak düşünülebilir.

###### 1) MySQL Veritabanı Çalışma Ortamı Güvenliğinin Sağlanması	
   MySQL sunucu servisinin chroot ortamı altında çalışması sağlanarak, MySQL sunucu servisinin işletim sistemi     kaynaklarının tümüne erişimi kısıtlanmalıdır. Bu şekilde MySQL sunucu servisi sadece ihtiyaç duyacağı kaynaklara erişim sağlanmış olacaktır.
   
 ###### 2) MySQL Veritabanını Süper Kullanıcı Harici Bir Kullanıcı Hakları ile Çalıştırmak 	
 MySQL'i çalıştırırken süper kullanıcı kullanılmamalıdır. MySQL için sistemde ayrı bir kullanıcı oluşturulmalıdır. Oluşturulan kullanıcı bir gruba yerleştirilmelidir. 
 
 	'group' ile belirtilen değer yapılandırması gerçekleştirilmek istenen programın adı ya da group.
Yapılandırma seçenekleri [group] ile başlayıp bir sonraki [group] adına kadar geçerli
olmaktadır. Örneğin [mysqld] ile mysqld sunucu yazılımı için etkinleştirilmek istenen değerler
belirtilecektir.  
	
	“user = kullanıcı_adı” değeri ile MySQL sunucu servisinin hangi kullanıcı hakları ile
çalıştırılacağı belirlenmektedir. Burada root kullanıcı haklarına sahip olmayan MySQL
grubuna üye MySQL kullanıcısı belirtilmiştir. 
  

###### 3)  MySQL Veritabanı Dosya ve Dizin Erişim Güvenliği	
	MySQL sunucu servisi tarafından kullanılan dizin ve dosya sahipliği hakları root kullanıcısına,
grup sahipliği hakları ise MySQL sunucu servisini çalıştıran kullanıcının dahil olduğu gruba ait
olmalıdır. MySQL sunucu servisinin mysql/mysql kullanıcı, grup ikilisi hakları ile çalıştırıldığı
düşünüldüğünde örnek bir dosya/dizin sahipliği hakları aşağıdaki gibi olmalıdır. 

	```
	chown root:mysql [dosya/dizin]
     ```		
    my.cnf” dosyasının sahipliği root kullanıcısı, grup sahipliği olarak ise MySQL sunucu
servisini çalıştıran kullanıcının ( Örneğin “mysql/mysql” kullanıcı/grup ikilisi ) dahil olduğu
grup olarak yapılandırmalı ve erişim izinleri için ise sayısal notasyon olarak 440 olarak
belirlenmelidir. 

	```
	chown root:mysql my.cnf 
    chmod 440 my.cnf 
	 ```   
###### 4) MySQL Veritabanı Servisinin Çalışacağı IP Adres Bilgisinin Belirlenmesi	
“bind-address= ip adres bilgisi” değeri ile MySQL sunucu servisinin bağlantıları dinleyeceği
ip adres bilgisi tanımlanmaktadır. Ön tanımlı olarak MySQL sunucu yazılımı bütün ağ
arayüzlerinden gelen bağlantıları kabul edecek şekilde yapılandırılmaktadır. Eğer MySQL
sunucu servisi ve web uygulaması aynı sunucu makine üzerinde çalışacak ise bu değer
“127.0.0.1” olarak belirtilmelidir. Aslında bu durumda “skip-networking” değeri
kullanılabilir. “skip-networking” değeri ile MySQL sunucu yazılımı hiçbir şekilde TCP/IP
bağlantılarını dinlemez. Bütün etkileşim (Linux/Unix) unix soketleri aracılığı ile
gerçekleştirilir. Ancak burada Apache ve MySQL sunucu servisleri için ayrı ayrı chroot
dizinleri oluşturulmuş ise, bu seçenek kullanılamamaktadır. Bu seçeneğin yerine “bindaddress=127.0.0.1”
değeri ile sadece belirtilen ip adreslerinden gelen bağlantıları kabul edecek
şekilde yapılandırılmalıdır. Eğer Apache ve MySQL sunucu servisleri aynı sunucu makine
üzerinde değil iseler, güvenlik duvarı ya da tcp_wrapper gibi uygulamalar aracılığı ile ip adres
kısıtlaması gerçekleştirilmelidir. Bu şekilde sadece belirtilen ip adreslerinden bağlantı kabul
edilecektir. Bunun yanında iletişimin açık bir şekilde değil, şifreli olarak ağ üzerinden
gerçekleştirilmesi sağlanmalıdır.

```
...
bind-address=127.0.0.1
...
```

###### 5) Yerel Erişim Güvenliğinin Artırılması
“LOAD DATA LOCAL INFILE” komutları engellenerek, yerel dosyaların yetkisiz erişimlerden
korunması sağlanmalıdır. Bu şekilde özellikle php ile geliştirilen web uygulamalarında
bulunabilecek olan, sql enjeksiyonu saldırılarından kaynaklanan yetkisiz yerel dosyaların
okunmasını engellemektedir. Bunun için MySQL sunucu servisi yapılandırma dosyasında
aşağıdaki değer bulunmalıdır. 
```
...
local-infile = 0 
...
```
###### 6) Yerel Erişim Güvenliğinin Artırılması

###### 7) Kullanıcıları Erişim Güvenliği

MySQL kullanıcıların erişim izinleri MySQL veritabanındaki db tablosuna göre
belirlenmektedir. Öncelikle bu tabloya MySQL root kullanıcısı haricinde kimsenin erişim izni
olmamalıdır. MySQL erişim kontrolleri GRANT ve REVOKE deyimleri ile belirlenmektedir. GRANT deyimi
ile hangi kullanıcının hangi veritabanına hangi ip adresi ya da ip adreslerinden erişim
sağlayabileceği belirlenmektedir. MySQL root kullanıcısının uzak erişimine izin verilmemeli,
sadece yerel erişimlere tam makine adı ya da ip adres bilgisi belirtilerek izin verilmelidir.
Örneğin “kullanıcı1” MySQL kullanıcısının 192.168.8.98 ip adresinden veritabanı1
veritabanına “şifre” şifresi ile erişim sağlaması için aşağıdaki SQL sorgusu çalıştırılmalıdır. 

```
mysql > GRANT ALL ON veritabanı1.* TO kullanıcı1@192.168.8.98 IDENTIFIED BY 'sifre'; 
```

Aynı şekilde “kullanıcı2” MySQL kullanıcısının yerel olarak “veritabanı2” isimli veritabanı
üzerinde sadece INSERT, SELECT ve DELETE yetkilerine sahip olabilmesi için aşağıdaki sql
sorgusu çalıştırılmalıdır. 

```
mysql > GRANT SELECT,INSERT,DELETE ON veritabanı2.* TO kullanıcı2@localhost IDENTIFIED BY 'sifre'; 
```
Yukarıda belirtilen kullanıcıların ilgili veritabanları üzerindeki erişim izinlerinin
yapılandırılmasının ardından, Db ve User tablosundaki ilgili alanları ortak olarak görüntülemek
için aşağıda belirtilen SQL sorgusu çalıştırılmalıdır. 

```
mysql > select db.Host,db.Db,db.User,user.Password from db,user where db.Host=user.Host and db.User=user.User ; 
```

###### 8) Parola Güvenliği 
Hemen hemen her uygulamada olduğu gibi MySQL sunucu servisi için de kullanıcı
parolalarının güvenliği çoğu zaman en kritik durum olarak göze batmaktadır. MySQL
kullanıcıları MySQL veritabanı içerisinde bulunan User tablosunda tutulmaktadır. MySQL
kullanıcı şifreleri açık olarak bu tabloda yer almazlar. Şifreler tek yönlü geri döndürülemez bir
algoritma ile şifrelenerek tutulmaktadırlar.
Şifrelenmiş olarak User tablosunda tutulan özet (hash) bilgilerinden, deneme yanılma (brute
force) yolu ile ya da farklı yöntemler uygulanarak parola bilgisi elde edilebilmektedir. Şekil
3-8’de görüldüğü gibi MySQL root kullanıcısının şifrelenmiş parola bilgileri elde edilmiş ve
ardından deneme yanılma (brute force) yolu ile parola bilgisi, görüldüğü gibi açıkça
görüntülenmiştir. 

Belirli aralıklar kullanıcı şifrelerinin hash (özet ) bilgileri brute force (deneme yanılma) yolu ile
açık olarak görüntülenmeye çalışılmalı, eğer parola bilgileri kolayca elde edilebilecek parolalar
varsa bu parola bilgisine sahip kullanıcıların tahmin edilmesi zor parola seçimleri yapmaları
sağlanmalıdır. 

###### 9) MySQL Süper Kullanıcı Güvenliği 
MySQL root kullanıcısının adı tahmin edilmesi zor, farklı bir isim ile değiştirilmelidir. Bu
şekilde MySQL root kullanıcısının parola bilgisi deneme yanılma yolu ile tahmin edilmesi
zorlaşacaktır. Bu şekilde sadece MySQL root kullanıcısının sadece şifresini değil aynı
zamanda kullanınıcı adını da tahmin etmelidir. MySQL root kullanıcısının adını değiştirmek
için aşağıdaki adımlar sırası ile uygulanmalıdır. 

```
mysql > update user set user="dbadmin" where user="root"; 
```
MySQL root kullanıcısının parola bilgisi ön tanımlı olarak boş gelmektedir. Yani hiçbir parola
bilgisi olmaksızın MySQL root kullanıcısı ile erişim hakkı elde edilebilmektedir. MySQL root
kullanıcısının parola bilgisi tahmin edilmesi zor bir parola ile değiştirilmelidir. Bunun için
aşağıdaki adımlar uygulanmalıdır. 

```
 mysql > SET PASSWORD FOR root@localhost=PASSWORD('yenisifre');
```

MySQL root kullanıcı hesabının komut satırından değiştirilmesinin doğuracağı güvenlik
açıklıkları olabilmektedir. Örneğin yukarıdaki işlem sonrası “.mysql_history” dosyasında bu
işlemin kayıtları aşağıdaki şekilde görülmektedir. 

```
# cat .mysql_history
…
UPDATE\040mysql.user\040SET\040Password=PASSWORD('yenisifre')\040WHERE\040Us
er='root';
# 
```
 Bu dosyanın erişim izinlerinden kaynaklanan bir durumda MySQL root kullanıcısının parolası
görülebilmektedir. Ya da parolanın “mysqladmin” gibi komut satırı araçları ile değiştirilmesi
sırasında, sistem üzerinde oturum açmış bir kullanıcı ps komutu yardımı ile şifreyi elde
edebilir. Bu duruma örnek bir durum aşağıda gösterilmiştir. User kullanıcısının sistemde
“Mysqladmin” komutunun çalışmasını gözlemlediği sırada, root kullanıcısının MySQL root
şifresi mysqladmin komutu yardımı ile değiştirilmektedir. 

```
$ while [ 1 ]; do ps auwx | grep "mysqladmin"| grep -v "grep"; done 
```
MySQL root kullanıcısına yeni şifre atama işleminin gerçekleştirildiği sırada mysqladmin
komutunu gözlemleme olayının sonucu aşağıdaki şekilde gerçekleşmiştir. 

```
…
root 15823 0.0 0.2 7748 1740 pts/0 S+ 09:55 0:00 MySQLadmin
-u root -p password sifre
… 
```
Görüldüğü gibi MySQL root kullanıcısına atanan yeni şifre açık olarak görülmektedir. 

###### 10) Örnek Veritabanı ve Tabloların Güvenliği 
Kurulum ile birlikte ön tanımlı olarak gelen örnek veritabanı ve MySQL root kullanıcısı
haricindeki kullanıcıların silinmesi gerekmektedir. Bunun için aşağıdaki adımların sırası ile
uygulanması gerekmektedir. Bu şekilde anonim kullanıcıların da sistemden kaldırılması
sağlanmış olacaktır. 

###### 11) Yama Kontrolü
Herhangi bir uygulama için en önemli güvenlik açığı, uygulama için yayınlanmış güvenlik
yamalarıdır. Düzenli olarak kullanımak istenen uyguglma için yayınlanmış güvenlik yamaları
takip edilmeli ve uygulanmalıdır. MySQL sunucu servisi içinde bu tehlike Şekil 2-1’de bugüne
kadar MySQL sunucu servisi için yayınlanmış güvenlik yamaları ile gösterilmiştir. İlgili
güvenlik yamaları http://www.mysql.com/ adresinden ya da bu konu üzerinde çalışma gösteren
sitelerin e-posta listelerinden düzenli olarak takip edilmeli ve uygulanmalıdır. 

###### 12) DNS Güvenliği 
DNS ön bellek zehirlenmesi gibi DNS saldırılarını önlemek için, kullanıcı yetkilendirmesi
esnasında kullanılan DNS isimleri yerine ip adres bilgisi kullanılmalıdır. Bu duruma örnek bir
kullanım aşağıda gösterilmiştir. 
