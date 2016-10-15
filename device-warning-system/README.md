#设备预警系统
##根据当前时间预警所有该检修的设备
###1、保养周期为年检的设备预警（提前一个月预警）
![image](https://github.com/mahuiking/mis/blob/master/device-warning-system/imgs/1.PNG)  
    SELECT d.* FROM device d LEFT JOIN device_type t on d.device_type_sn=t.device_type_sn where t.check_period=5 and DATE_ADD(d.latest_check_time, INTERVAL 11 MONTH) < NOW() and DATE_ADD(d.latest_check_time, INTERVAL 12 MONTH)>NOW();
    
    
###2、保养周期为月检的设备预警（提前一周预警）
SELECT d.* FROM device d LEFT JOIN device_type t on d.device_type_sn=t.device_type_sn where t.check_period=1 and   DATE_ADD(d.latest_check_time, INTERVAL 21 DAY) < NOW() and DATE_ADD(d.latest_check_time, INTERVAL 1 MONTH)>NOW();    
##根据设备编号，查询出设备检修报告（包括历史检修情况和材料消耗情况）

###1、根据设备号查询出所有的检修记录
![image](https://github.com/mahuiking/mis/blob/master/device-warning-system/imgs/2.PNG)  
    SELECT * FROM maintenance_record where device_sn='device-001';  
    
    
###2、选中其中一条检修记录，查询明细
![image](https://github.com/mahuiking/mis/blob/master/device-warning-system/imgs/3.PNG)  
    SELECT * FROM maintanance_details WHERE maintenance_record_sn =201610051753001;  
    
    
    
###3、生成检修报告

##SQL语句
/*  
Navicat MySQL Data Transfer  

Source Server         : localhost  
Source Server Version : 50712  
Source Host           : localhost:3306  
Source Database       : device  

Target Server Type    : MYSQL  
Target Server Version : 50712  
File Encoding         : 65001  

Date: 2016-10-08 19:35:55  
*/  

SET FOREIGN_KEY_CHECKS=0;  

-- ----------------------------  
-- Table structure for components  
-- ----------------------------  
DROP TABLE IF EXISTS `components`;  
CREATE TABLE `components` (  
  `id` int(11) NOT NULL AUTO_INCREMENT,  
  `components_sn` varchar(45) NOT NULL COMMENT '配件编号',  
  `components_name` varchar(100) NOT NULL COMMENT '配件名称',  
  `stock` bigint(20) NOT NULL COMMENT '库存',  
  PRIMARY KEY (`id`),  
  UNIQUE KEY `components` (`components_sn`) USING HASH  
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8;  

-- ----------------------------  
-- Records of components  
-- ----------------------------  
INSERT INTO `components` VALUES ('1', 'com-001', '配件A', '1000');  
INSERT INTO `components` VALUES ('2', 'com-002', '配件B', '800');  
INSERT INTO `components` VALUES ('3', 'com-003', '配件C', '600');  

-- ----------------------------  
-- Table structure for device  
-- ----------------------------  
DROP TABLE IF EXISTS `device`;  
CREATE TABLE `device` (  
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '设备ID',  
  `device_sn` varchar(40) NOT NULL COMMENT '设备编号',  
  `device_name` varchar(100) NOT NULL COMMENT '设备名称',  
  `device_type_sn` varchar(45) NOT NULL COMMENT '设备类别编号',  
  `latest_check_time` datetime DEFAULT NULL,  
  PRIMARY KEY (`id`),  
  UNIQUE KEY `device` (`device_sn`) USING BTREE,  
  KEY `fk_device_device_type` (`device_type_sn`),  
  CONSTRAINT `fk_device_device_type` FOREIGN KEY (`device_type_sn`) REFERENCES `device_type` (`device_type_sn`) ON DELETE CASCADE ON UPDATE CASCADE  
) ENGINE=InnoDB AUTO_INCREMENT=98 DEFAULT CHARSET=utf8;  

-- ----------------------------  
-- Records of device  
-- ----------------------------  
INSERT INTO `device` VALUES ('1', 'device-001', '设备1', '0001', '2016-10-03 17:48:23');  
INSERT INTO `device` VALUES ('2', 'device-002', '设备2', '0001', '2016-10-05 17:52:15');  
INSERT INTO `device` VALUES ('5', 'device-005', '设备5', '0002', '2016-10-05 17:52:18');  

-- ----------------------------  
-- Table structure for device_type  
-- ----------------------------  
DROP TABLE IF EXISTS `device_type`;  
CREATE TABLE `device_type` (  
  `id` int(11) NOT NULL AUTO_INCREMENT,  
  `device_type_sn` varchar(45) NOT NULL COMMENT '设备类别名称',  
  `device_type_name` varchar(100) NOT NULL COMMENT '设备类别名称',  
  `check_period` int(11) NOT NULL COMMENT '检查周期（0：周检，1：月检...）',  
  `maintanance_sn` varchar(45) NOT NULL,  
  PRIMARY KEY (`id`),  
  UNIQUE KEY `device_type_sn` (`device_type_sn`) USING BTREE,  
  KEY `fk_type_maintanance` (`maintanance_sn`),  
  CONSTRAINT `fk_type_maintanance` FOREIGN KEY (`maintanance_sn`) REFERENCES `maintenance` (`maintenance_sn`) ON DELETE CASCADE ON UPDATE CASCADE  
) ENGINE=InnoDB AUTO_INCREMENT=22 DEFAULT CHARSET=utf8;  

-- ----------------------------  
-- Records of device_type  
-- ----------------------------  
INSERT INTO `device_type` VALUES ('1', '0001', '6000V及以上电机', '5', '2016-dianji');  
INSERT INTO `device_type` VALUES ('2', '0002', '6000V以下不带振动电机', '5', '2016-buzhendong');  
INSERT INTO `device_type` VALUES ('3', '0003', '6000V以下带振动电机', '4', '2016-zhendong');  
INSERT INTO `device_type` VALUES ('4', '0004', 'CST', '1', '2016-CST');  
INSERT INTO `device_type` VALUES ('5', '0005', 'PLC', '1', '2016-PLC');  
INSERT INTO `device_type` VALUES ('6', '0006', '比重计、液位仪', '1', '2016-bizhongyi');  
INSERT INTO `device_type` VALUES ('7', '0007', '变频器（100KW以上皮带、泵电机驱动的）', '2', '2016-bianpinqi');  
INSERT INTO `device_type` VALUES ('8', '0008', '采样机', '1', '2016-caiyangji');  
INSERT INTO `device_type` VALUES ('9', '0009', '仓位传感器', '1', '2016-cangwei');  
INSERT INTO `device_type` VALUES ('10', '0010', '除铁器', '1', '2016-chutie');  
INSERT INTO `device_type` VALUES ('11', '0011', '防冻设施', '1', '2016-fangdong');  
INSERT INTO `device_type` VALUES ('12', '0012', '装车站防冻液喷洒系统', '0', '2016-zhuangche');  
INSERT INTO `device_type` VALUES ('13', '0013', '浓缩机', '4', '2016-nongsuoji');  
INSERT INTO `device_type` VALUES ('14', '0014', '皮带秤', '1', '2016-pidaicheng');  
INSERT INTO `device_type` VALUES ('15', '0015', '皮带运输机', '5', '2016-pidai');  
INSERT INTO `device_type` VALUES ('16', '0016', '球磨机', '5', '2016-qiumeji');  
INSERT INTO `device_type` VALUES ('17', '0017', '上位机', '1', '2016-shngweiji');  
INSERT INTO `device_type` VALUES ('18', '0018', '箱变', '4', '2016-xiangbian');  
INSERT INTO `device_type` VALUES ('19', '0019', '角执行机构、直执行机构', '1', '2016-jiaozhixing');  
INSERT INTO `device_type` VALUES ('20', '0020', '装车定量秤', '0', '2016-zhuangchdingliang');  
INSERT INTO `device_type` VALUES ('21', '0021', '装车站卸料闸板', '0', '2016-zhuangchezhan');  

-- ----------------------------  
-- Table structure for maintanance_details  
-- ----------------------------  
DROP TABLE IF EXISTS `maintanance_details`;  
CREATE TABLE `maintanance_details` (  
  `id` int(11) NOT NULL AUTO_INCREMENT,  
  `maintenance_record_sn` varchar(45) NOT NULL COMMENT '保养单编号',  
  `maintenance_list_sn` varchar(45) NOT NULL COMMENT '保养内容',  
  `has_finished` bit(1) NOT NULL DEFAULT b'0' COMMENT '是否完成',  
  `remark` varchar(255) DEFAULT NULL,  
  `components_sn` varchar(45) DEFAULT NULL COMMENT '消耗配件编号',  
  `components_number` bigint(20) DEFAULT NULL COMMENT '消耗配件数量',  
  PRIMARY KEY (`id`),  
  KEY `fk_maintanance_details_record` (`maintenance_record_sn`),  
  KEY `fk_maintanance_details_list` (`maintenance_list_sn`),  
  KEY `fk_maintanance_details_components` (`components_sn`),  
  CONSTRAINT `fk_maintanance_details_components` FOREIGN KEY (`components_sn`) REFERENCES `components` (`components_sn`) ON DELETE CASCADE ON UPDATE CASCADE,  
  CONSTRAINT `fk_maintanance_details_list` FOREIGN KEY (`maintenance_list_sn`) REFERENCES `maintenance_list` (`maintenance_list_sn`) ON DELETE CASCADE ON UPDATE CASCADE,   
  CONSTRAINT `fk_maintanance_details_record` FOREIGN KEY (`maintenance_record_sn`) REFERENCES `maintenance_record` (`maintenance_record_sn`) ON DELETE CASCADE ON UPDATE CASCADE  
) ENGINE=InnoDB AUTO_INCREMENT=8 DEFAULT CHARSET=utf8;  

-- ----------------------------  
-- Records of maintanance_details  
-- ----------------------------  
INSERT INTO `maintanance_details` VALUES ('1', '201610051753001', '2016-dianji-001', '', null, null, null);  
INSERT INTO `maintanance_details` VALUES ('2', '201610051753001', '2016-dianji-002', '\0', '未完成，消耗A材料3个', 'com-001', '3');  
INSERT INTO `maintanance_details` VALUES ('3', '201610051753001', '2016-dianji-003', '', null, null, null);  
INSERT INTO `maintanance_details` VALUES ('4', '201610051753001', '2016-dianji-004', '', null, 'com-002', '2');  
INSERT INTO `maintanance_details` VALUES ('5', '201610051753001', '2016-dianji-005', '', null, null, null);  
INSERT INTO `maintanance_details` VALUES ('6', '201610051753001', '2016-dianji-006', '', null, null, null);  
INSERT INTO `maintanance_details` VALUES ('7', '201610051753001', '2016-dianji-007', '', null, null, null);  

-- ----------------------------  
-- Table structure for maintenance  
-- ----------------------------  
DROP TABLE IF EXISTS `maintenance`;  
CREATE TABLE `maintenance` (  
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT 'ID',  
  `maintenance_sn` varchar(40) NOT NULL COMMENT '保养单编号',  
  `maintenance_name` varchar(100) NOT NULL COMMENT '保养单名称',  
  PRIMARY KEY (`id`),  
  UNIQUE KEY `maintenance_sn` (`maintenance_sn`) USING BTREE  
) ENGINE=InnoDB AUTO_INCREMENT=22 DEFAULT CHARSET=utf8;  

-- ----------------------------  
-- Records of maintenance  
-- ----------------------------  
INSERT INTO `maintenance` VALUES ('1', '2016-dianji', '6000V及以上电机保养单');  
INSERT INTO `maintenance` VALUES ('2', '2016-buzhendong', '6000V以下不带振动电机保养单');  
INSERT INTO `maintenance` VALUES ('3', '2016-zhendong', '6000V以下带振动电机保养单');  
INSERT INTO `maintenance` VALUES ('4', '2016-CST', 'CST保养单');  
INSERT INTO `maintenance` VALUES ('5', '2016-PLC', 'PLC保养单');  
INSERT INTO `maintenance` VALUES ('6', '2016-bizhongyi', '比重计、液位仪保养单');  
INSERT INTO `maintenance` VALUES ('7', '2016-bianpinqi', '变频器（100KW以上皮带、泵电机驱动的）保养单');  
INSERT INTO `maintenance` VALUES ('8', '2016-caiyangji', '采样机保养单');  
INSERT INTO `maintenance` VALUES ('9', '2016-cangwei', '仓位传感器保养单');  
INSERT INTO `maintenance` VALUES ('10', '2016-chutie', '除铁器保养单');  
INSERT INTO `maintenance` VALUES ('11', '2016-fangdong', '防冻设施保养单');  
INSERT INTO `maintenance` VALUES ('12', '2016-zhuangche', '装车站防冻液喷洒系统保养单');  
INSERT INTO `maintenance` VALUES ('13', '2016-nongsuoji', '浓缩机保养单');  
INSERT INTO `maintenance` VALUES ('14', '2016-pidaicheng', '皮带秤保养单');  
INSERT INTO `maintenance` VALUES ('15', '2016-pidai', '皮带运输机保养单');  
INSERT INTO `maintenance` VALUES ('16', '2016-qiumeji', '球磨机保养单');  
INSERT INTO `maintenance` VALUES ('17', '2016-shngweiji', '上位机保养单');  
INSERT INTO `maintenance` VALUES ('18', '2016-xiangbian', '箱变保养单');  
INSERT INTO `maintenance` VALUES ('19', '2016-jiaozhixing', '角执行机构、直执行机构保养单');  
INSERT INTO `maintenance` VALUES ('20', '2016-zhuangchdingliang', '装车定量秤保养单');  
INSERT INTO `maintenance` VALUES ('21', '2016-zhuangchezhan', '装车站卸料闸板保养单');  

-- ----------------------------  
-- Table structure for maintenance_list  
-- ----------------------------  
DROP TABLE IF EXISTS `maintenance_list`;  
CREATE TABLE `maintenance_list` (  
  `id` int(11) NOT NULL AUTO_INCREMENT,  
  `maintenance_list_sn` varchar(40) NOT NULL,  
  `maintenance_list_content` varchar(255) NOT NULL,  
  `maintenance_sn` varchar(40) NOT NULL,  
  PRIMARY KEY (`id`),  
  UNIQUE KEY `maintenance_list_sn` (`maintenance_list_sn`) USING BTREE,  
  KEY `fk_maintenance_list_maintenance` (`maintenance_sn`),  
  CONSTRAINT `fk_maintenance_list_maintenance` FOREIGN KEY (`maintenance_sn`) REFERENCES `maintenance` (`maintenance_sn`) ON DELETE CASCADE ON UPDATE CASCADE  
) ENGINE=InnoDB AUTO_INCREMENT=1000017 DEFAULT CHARSET=utf8;  

-- ----------------------------  
-- Records of maintenance_list  
-- ----------------------------
INSERT INTO `maintenance_list` VALUES ('1000000', '2016-dianji-001', '检查6000V接线盒内瓷瓶、端子；', '2016-dianji');  
INSERT INTO `maintenance_list` VALUES ('1000001', '2016-dianji-002', '接线盒内卫生清洁；', '2016-dianji');  
INSERT INTO `maintenance_list` VALUES ('1000002', '2016-dianji-003', '检查电缆引线、穿线管、接地线；', '2016-dianji');  
INSERT INTO `maintenance_list` VALUES ('1000003', '2016-dianji-004', '检查进线口密封情况；', '2016-dianji');  
INSERT INTO `maintenance_list` VALUES ('1000004', '2016-dianji-005', '检查前后轴承温度传感器的接线盒；', '2016-dianji');  
INSERT INTO `maintenance_list` VALUES ('1000005', '2016-dianji-006', '检查定子绕组温度传感器的接线盒。', '2016-dianji');  
INSERT INTO `maintenance_list` VALUES ('1000006', '2016-dianji-007', '检查防潮加热器的接线盒。', '2016-dianji');  
INSERT INTO `maintenance_list` VALUES ('1000007', '2016-CST-001', '检查温度传感器、加热器的完好情况。', '2016-CST');  
INSERT INTO `maintenance_list` VALUES ('1000008', '2016-CST-002', '检查压力（冷却、润滑、系统、工作）传感器的完好情况。', '2016-CST');  
INSERT INTO `maintenance_list` VALUES ('1000009', '2016-CST-003', '检查频率（速度）传感器的完好情况。', '2016-CST');  
INSERT INTO `maintenance_list` VALUES ('1000010', '2016-CST-004', '检查电磁阀的好坏。', '2016-CST');  
INSERT INTO `maintenance_list` VALUES ('1000011', '2016-CST-005', '检查线缆、穿线管的固定情况、线缆的磨损情况。', '2016-CST');  
INSERT INTO `maintenance_list` VALUES ('1000012', '2016-CST-006', '清扫控制箱，紧固接线。', '2016-CST');  
INSERT INTO `maintenance_list` VALUES ('1000013', '2016-CST-007', '检查按钮是否灵活工作可靠。', '2016-CST');  
INSERT INTO `maintenance_list` VALUES ('1000014', '2016-CST-008', '检查CST触摸屏操作参数是否合理。', '2016-CST');  
INSERT INTO `maintenance_list` VALUES ('1000015', '2016-CST-009', '检查CST控制箱内照明灯。', '2016-CST');  
INSERT INTO `maintenance_list` VALUES ('1000016', '2016-CST-010', '检查CST控制箱电缆头、门的密封。', '2016-CST');  

-- ----------------------------  
-- Table structure for maintenance_record  
-- ----------------------------  
DROP TABLE IF EXISTS `maintenance_record`;  
CREATE TABLE `maintenance_record` (  
  `id` int(11) NOT NULL AUTO_INCREMENT,  
  `maintenance_record_sn` varchar(255) NOT NULL,  
  `check_time` datetime NOT NULL COMMENT '检查时间',  
  `device_sn` varchar(45) NOT NULL,  
  PRIMARY KEY (`id`),  
  UNIQUE KEY `maintenance_record` (`maintenance_record_sn`) USING BTREE,  
  KEY `fk_maintenance_record_device` (`device_sn`),  
  CONSTRAINT `fk_maintenance_record_device` FOREIGN KEY (`device_sn`) REFERENCES `device` (`device_sn`) ON DELETE CASCADE ON UPDATE CASCADE  
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;  

-- ----------------------------  
-- Records of maintenance_record  
-- ----------------------------  
INSERT INTO `maintenance_record` VALUES ('1', '201610051753001', '2016-10-05 17:53:39', 'device-001');  

-- ----------------------------  
-- Table structure for maintenance_record_checkers  
-- ----------------------------  
DROP TABLE IF EXISTS `maintenance_record_checkers`;  
CREATE TABLE `maintenance_record_checkers` (  
  `id` int(11) NOT NULL AUTO_INCREMENT,  
  `person_sn` varchar(45) NOT NULL,  
  `maintenance_record_sn` varchar(45) NOT NULL,  
  PRIMARY KEY (`id`),  
  KEY `fk_checkers_person` (`person_sn`),  
  KEY `fk_checkers_record` (`maintenance_record_sn`),  
  CONSTRAINT `fk_checkers_person` FOREIGN KEY (`person_sn`) REFERENCES `person` (`person_sn`) ON DELETE CASCADE ON UPDATE CASCADE,  
  CONSTRAINT `fk_checkers_record` FOREIGN KEY (`maintenance_record_sn`) REFERENCES `maintenance_record` (`maintenance_record_sn`) ON DELETE CASCADE ON UPDATE CASCADE  
) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8;  

-- ----------------------------  
-- Records of maintenance_record_checkers  
-- ----------------------------  
INSERT INTO `maintenance_record_checkers` VALUES ('1', '09143690', '201610051753001');  
INSERT INTO `maintenance_record_checkers` VALUES ('2', '09143689', '201610051753001');  

-- ----------------------------  
-- Table structure for person  
-- ---------------------------- 
DROP TABLE IF EXISTS `person`;  
CREATE TABLE `person` (  
  `id` int(11) NOT NULL AUTO_INCREMENT,  
  `person_sn` varchar(45) NOT NULL,  
  `person_name` varchar(100) NOT NULL,  
  `password` varchar(45) NOT NULL,  
  PRIMARY KEY (`id`),  
  UNIQUE KEY `person_sn` (`person_sn`) USING BTREE  
) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8;  

-- ----------------------------  
-- Records of person  
-- ----------------------------  
INSERT INTO `person` VALUES ('1', '09143690', '马辉', '123456');  
INSERT INTO `person` VALUES ('2', '09143689', '苏雄伟', '123456');  


