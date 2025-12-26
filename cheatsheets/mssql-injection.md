# MsSQL Injections
Bu cheatsheet, eski ve güncel MsSQL injectionlarını içermektedir.

İlham Kaynaklarım: pentestmonkey MSSQL Injection Cheat Sheet, HackTheBox cheatsheet-sql-injection-fundamentals, dev.mysql.com/doc/

## Yaygın MsSQL Komutları

### Genel Kullanım
MySQL database'ine giriş yapma:
```sql
mysql -u [kullanıcı_adı] root -h [host_adresi] example.com -P [port_adresi] 3306 -p
```
- **[kullanıcı_adı]**: Genellikle 'root' gibi bir user ismiyle değiştir (örneğin: -u root).
- **[host_adresi]**: Sunucu adresi, mesela 'example.com' veya 'localhost' (örneğin: -h example.com).
- **[port_numarası]**: Varsayılan 3306, ama değiştirilebilir (örneğin: -P 3306).
- -p: Password prompt'u açar, şifreyi direkt yazma.

Tam örnek:
```sql
mysql -u root -h example.com -P 3306 -p
```
Kullanılabilir database'leri listeleme:
```sql
SHOW DATABASES
```
Database değiştirme:
```sql
USE users
```

### Tablolar

Yeni tablo oluşturma:
```sql
CREATE TABLE logins (id INT, ...)
```

Mevcut database'te kullanılabilir tabloları listeleme:
```sql
SHOW TABLES
```

Tablo özelliklerini ve satırlarını görme:
```sql
DESCRIBE logins
```

Tabloya değer ekleme:
```sql
INSERT INTO table_name VALUES (value_1, ...)
```

Tabloda spesifik bir satıra değer ekleme:
```sql
INSERT INTO table_name(column2, ...) VALUES (column2_value, ...)
```

Tabloda satır değerini değiştirme:
```sql
UPDATE table_name SET column1=newvalue1, ... WHERE <condition>
```
Tablodaki bütün satırları görme:
```sql
SELECT * FROM  table_name
```
Tabloda spesifik bir satır görme:
```sql
SELECT column1, column2 FROM table_name
```
Tablo silme:
```sql
DROP TABLE logins
```

Tabloya yeni satır ekleme:
```sql
ALTER TABLE logins ADD newColumn INT
```
Satırın ismini değiştirme:
```sql
ALTER TABLE logins RENAME COLUMN newColumn TO oldColumn
```
Satırın veri türünü değiştirme:
```sql
ALTER TABLE logins MODIFY oldcolumn DATE
```
Satır silme:
```sql
ALTER TABLE logins DROP oldColumn
```

### Veri Bastırma

Satıra göre sıralama:
```sql
SELECT * FROM logins ORDER BY column_1
```

Satırın içindeki verileri azalana göre sıralama:
```sql
SELECT * FROM logins ORDER BY column_1 DESC
```

İki satıra göre sıralama:
```sql
SELECT * FROM logins ORDER BY column_1 DESC, id ASC
```
Sadece ilk iki satırı görme:
```sql
SELECT * FROM logins LIMIT 2
```

Index 2'den başlayarak sadece ilk iki satırı görme:
```sql
SELECT * FROM logins LIMIT 1, 2
```
Koşulu sağlayan sonuçları görme:

```sql
SELECT * FROM table_name WHERE <condition>
```

Verilen ifadeye göre benzer addaki verileri görme:

```sql
SELECT * FROM logins WHERE username LIKE 'admin%' 
```

## MySQL Operatörlerinin Öncelik Sırası

| Öncelik Seviyesi | Operatörler | Açıklama/Notlar |
|------------------|-------------|-----------------|
| En Yüksek | INTERVAL | Zaman aralıkları için kullanılır, karşılaştırmalarda. |
| | BINARY, COLLATE | BINARY ikili string karşılaştırması; COLLATE collation belirtir. |
| | ! | Mantıksal NOT (logical, SQL NOT değil). |
| | - (unary minus), ~ (unary bit inversion) | Tek operand'a unary minus ve bitwise NOT. |
| | ^ | Bitwise XOR. |
| | *, /, DIV, %, MOD | Çarpma, bölme, tamsayı bölme, modulus. |
| | -, + | Toplama ve çıkartma (ikili). |
| | <<, >> | Sol ve sağ shift. |
| | & | Bitwise AND. |
| | II | Bitwise OR   . |
| | =, <=, >=, >, <, <>, !=, IS, LIKE, REGEXP, IN, MEMBER OF | Karşılaştırma ve üyelik operatörleri. = karşılaştırmada aynı öncelikte. |
| | BETWEEN, CASE, WHEN, THEN, ELSE | BETWEEN aralık kontrolü; CASE ifadesi bileşenleri. |
| | NOT | Mantıksal olumsuzluk. |
| | AND, && | Mantıksal AND. |
| | XOR | Mantıksal XOR. |
| | OR, II | Mantıksal OR. || varsayılan OR; PIPES_AS_CONCAT modunda string birleştirme olur. |
| En Düşük | =, := | Atama operatörleri. = atamada := ile aynı; sağdan sola değerlendirilir. |
