## ＊ 省份

```sql
DELETE FROM `directory_country_region`  WHERE country_id = 'CN';
INSERT INTO  `directory_country_region` (`region_id`, `country_id`, `code`, `default_name`) VALUES
(10000, 'CN', 'BJ', 'Beiking'),
(10001, 'CN', 'SH', 'Shanghai'),
(10002, 'CN', 'TJ', 'Tianjin'),
(10003, 'CN', 'CQ', 'Chongqing'),
(10004, 'CN', 'HE', 'Hebei'),
(10005, 'CN', 'SX', 'Shanxi'),
(10006, 'CN', 'NM', 'Neimenggu'),
(10007, 'CN', 'LN', 'Liaoning'),
(10008, 'CN', 'JL', 'Jilin'),
(10009, 'CN', 'HL', 'Heilongjiang'),
(10010, 'CN', 'JS', 'Jiansu'),
(10011, 'CN', 'ZJ', 'Zhejiang'),
(10012, 'CN', 'AH', 'Anhui'),
(10013, 'CN', 'FJ', 'Fujian'),
(10014, 'CN', 'JX', 'Jiangxi'),
(10015, 'CN', 'SD', 'Shandong'),
(10016, 'CN', 'HA', 'Henan'),
(10017, 'CN', 'HB', 'Hubei'),
(10018, 'CN', 'HN', 'Hunan'),
(10019, 'CN', 'GD', 'Guangdong'),
(10020, 'CN', 'GX', 'Guangxi'),
(10021, 'CN', 'HI', 'Hainan'),
(10022, 'CN', 'SC', 'Sichuan'),
(10023, 'CN', 'GZ', 'Guizhou'),
(10024, 'CN', 'YN', 'Yunnan'),
(10025, 'CN', 'XZ', 'Xizang'),
(10026, 'CN', 'SN', 'Shangxi'),
(10027, 'CN', 'GS', 'Gansu'),
(10028, 'CN', 'QH', 'Qinghai'),
(10029, 'CN', 'NX', 'Ningxia'),
(10030, 'CN', 'XJ', 'Xinjiang'),
(10031, 'CN', 'HK', 'Hongkong'),
(10032, 'CN', 'AM', 'Aomen'),
(10033, 'CN', 'TW', 'Taiwan'); 
SELECT * FROM `directory_country_region`  WHERE country_id = 'CN';


DELETE FROM `directory_country_region_name` WHERE region_id >=10000 AND region_id <= 10033;
INSERT INTO `directory_country_region_name` (`locale`, `region_id`, `name`) VALUES
('zh_CN', 10000, '北京'),
('zh_CN', 10001, '上海'),
('zh_CN', 10002, '天津'),
('zh_CN', 10003, '重庆'),
('zh_CN', 10004, '河北'),
('zh_CN', 10005, '山西'),
('zh_CN', 10006, '内蒙古'),
('zh_CN', 10007, '辽宁'),
('zh_CN', 10008, '吉林'),
('zh_CN', 10009, '黑龙江'),
('zh_CN', 10010, '江苏'),
('zh_CN', 10011, '浙江'),
('zh_CN', 10012, '安徽'),
('zh_CN', 10013, '福建'),
('zh_CN', 10014, '江西'),
('zh_CN', 10015, '山东'),
('zh_CN', 10016, '河南'),
('zh_CN', 10017, '湖北'),
('zh_CN', 10018, '湖南'),
('zh_CN', 10019, '广东'),
('zh_CN', 10020, '广西'),
('zh_CN', 10021, '海南'),
('zh_CN', 10022, '四川'),
('zh_CN', 10023, '贵州'),
('zh_CN', 10024, '云南'),
('zh_CN', 10025, '西藏'),
('zh_CN', 10026, '陕西'),
('zh_CN', 10027, '甘肃'),
('zh_CN', 10028, '青海'),
('zh_CN', 10029, '宁夏'),
('zh_CN', 10030, '新疆'),
('zh_CN', 10031, '香港'),
('zh_CN', 10032, '澳门'),
('zh_CN', 10033, '台湾');
SELECT * FROM `directory_country_region_name` WHERE region_id >=10000 AND region_id <= 10033;
```

请注意第二段SQL语句中的region_id跟directory_country_region表的主键相关联。

## ＊ 城市