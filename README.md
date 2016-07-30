# MySQL Sıkılaştırma

Bu döküman, MySQL kullanmayı planlayan kişilere yöneliktir. Özellikle bilgi varlığı güvenliğinin kritik olduğu projelerde, MySQL'in sıkılaştırma ayarları kesinlikle yapılmalıdır. Yapılmadığı durumlarda, MySQL de tutulan bilgilerin çalınması, değiştirilmesi gibi durumlarla karşı karşıya kalınabilir. 

Bu linkte MySQL'de bulunan ve bilinen açıklıkların listesini içermektedir. https://www.cvedetails.com/vulnerability-list/vendor_id-93/product_id-21801/Oracle-Mysql.html 

Döküman hazırlanırken birçok kaynak incelenmiş ve ortaya çıkan çalışma taranan kaynakların birleşimi olarak düşünülebilir.

##### 1) MySQL Veritabanı Çalışma Ortamı Güvenliğinin Sağlanması	
   MySQL sunucu servisinin chroot ortamı altında çalışması sağlanarak, MySQL sunucu servisinin işletim sistemi     kaynaklarının tümüne erişimi kısıtlanmalıdır. Bu şekilde MySQL sunucu servisi sadece ihtiyaç duyacağı kaynaklara erişim sağlanmış olacaktır.
   
 ##### 2) MySQL Veritabanını Süper Kullanıcı Harici Bir Kullanıcı Hakları ile Çalıştırmak 	
 MySQL'i çalıştırırken süper kullanıcı kullanılmamalıdır. MySQL için sistemde ayrı bir kullanıcı oluşturulmalıdır. Oluşturulan kullanıcı bir gruba yerleştirilmelidir. 
 
 	'group' ile belirtilen değer yapılandırması gerçekleştirilmek istenen programın adı ya da group.
Yapılandırma seçenekleri [group] ile başlayıp bir sonraki [group] adına kadar geçerli
olmaktadır. Örneğin [mysqld] ile mysqld sunucu yazılımı için etkinleştirilmek istenen değerler
belirtilecektir.  
	
	“user = kullanıcı_adı” değeri ile MySQL sunucu servisinin hangi kullanıcı hakları ile
çalıştırılacağı belirlenmektedir. Burada root kullanıcı haklarına sahip olmayan MySQL
grubuna üye MySQL kullanıcısı belirtilmiştir. 
  

##### 3)  MySQL Veritabanı Dosya ve Dizin Erişim Güvenliği	
	MySQL sunucu servisi tarafından kullanılan dizin ve dosya sahipliği hakları root kullanıcısına,
grup sahipliği hakları ise MySQL sunucu servisini çalıştıran kullanıcının dahil olduğu gruba ait
olmalıdır. MySQL sunucu servisinin mysql/mysql kullanıcı, grup ikilisi hakları ile çalıştırıldığı
düşünüldüğünde örnek bir dosya/dizin sahipliği hakları aşağıdaki gibi olmalıdır. 

	```javascript
	chown root:mysql [dosya/dizin]
     ```		
    my.cnf” dosyasının sahipliği root kullanıcısı, grup sahipliği olarak ise MySQL sunucu
servisini çalıştıran kullanıcının ( Örneğin “mysql/mysql” kullanıcı/grup ikilisi ) dahil olduğu
grup olarak yapılandırmalı ve erişim izinleri için ise sayısal notasyon olarak 440 olarak
belirlenmelidir. 

	```javascript
	chown root:mysql my.cnf 
    chmod 440 my.cnf 
	 ```   
##### 4) MySQL Veritabanı Servisinin Çalışacağı IP Adres Bilgisinin Belirlenmesi

