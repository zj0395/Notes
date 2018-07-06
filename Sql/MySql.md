# MySql


# Oracle
- 别名 oracle中不能用 file 做别名


- Oracle中长字符串处理方法
```
update mall_config set category_info='安全防护:3003,' where id=1
update mall_config set category_info=category_info||'|标准件:1040140,1035382,' where id=1 
或者
update mall_config set category_info=concat(category_info,'|标准件:1040140,1035382,') where id=1
```

- oracle实现mysql中的limit
```
mysql:
SELECT * FROM basedata_equ_file WHERE etag_code='0000-1306-0110-0100-0102-0000' AND organization_id=2 limit 1
oracle:
SELECT * FROM basedata_equ_file WHERE etag_code='0000-1306-0110-0100-0102-0000' AND organization_id=2 AND rownum=1;
```

- oracle 中时间需要 to_timestamp
```
const char* TIME_STR_FORMAT = "yyyy-mm-dd hh24:mi:ss";
return "to_timestamp('" + src + "', '" + TIME_STR_FORMAT + "')";
```

- 提升为管理员: `conn sys/oracle as sysdba`

- 创建用户:
```
create user aims_test identified by 12345;
grant create session,resource to aims_test ;
```

- 删除用户: `drop user aims_test cascade;`

- 删除列: `alter table test_position_list drop column test_id;`

- 新建列: `alter table table_name add (rowName number(11));`

- 带外键:
```
ALTER table Organ_position_list ADD CONSTRAINT organ_position_list_ordId
FOREIGN KEY (organization_id)
REFERENCES Organ_organization (id);
```

- 查看约束: `select table_name from user_constraints where constraint_name='SYS_C0024257';`

- 重命名列: `alter table test_table rename column "ORDER" to order_sort;`

- 选择序列:
```
select trigger_name from all_triggers where table_name='BASEDATA_RFID';
select trigger_body from user_triggers where trigger_name='BASEDATA_RFID_TR';
```

- 查看序列:
```
select max(id) from patrol_list;
desc user_sequences;
select sequence_name from user_sequences;
```

- 创建序列：
`create sequence prtrolSeq minvalue 1 maxvalue 99999999999999 start with 1 increment by 1 cache 20;`

- 应用序列：
```
CREATE OR REPLACE TRIGGER testSeq
BEFORE INSERT ON patrol_list FOR EACH ROW WHEN (new.id is null)
begin
select testSeq.nextval into:new.id from dual;
end;
/
```

- 修改序列：
```
ALTER SEQUENCE testSeq INCREMENT BY 1000;
SELECT testSeq.NEXTVAL FROM DUAL;
ALTER SEQUENCE testSeq INCREMENT BY 1;
```