## 地址树修改

### python 脚本预处理

```python
def sql_cm():
    file4 = "C:\\Users\\Caolp\\Desktop\\喀麦隆万村通地址调整.xlsx"
    cm_address = pd.DataFrame(
        pd.read_excel(file4, sheet_name="result 1", converters={u'old_full_code': str, u'new_full_code': str}))
    cm_address['sql_text'] = 'select ' + "'" + cm_address['old_full_code'] + "'" + ' full_code, ' + "'" + cm_address[
        'new_full_code'] + "'" + ' parent_full_code' + '\n' + 'from dual' + '\n' + 'union all'
    with open("cm_address_sql.txt", 'w') as f:
        f.write('\n'.join(cm_address['sql_text']))


if __name__ == '__main__':
    sql_cm()
```
---
### 存储过程书写

1. 将上述结果连接为一个临时表
2. 与地址表进行关联，*full_code* 对应 *old_address_id*, *parent_full_code* 对应 *new_address_id*, 并取出 *new_address_id* 对应的 *full_name*.
3. 通过 *old_address_id* 来对客户，用户地址进行更新，对于用户来说只需要更新 *address_id* 字段即可，对于客户来说除 *address_id* 外，还需要对客户的联系地址进行更新，联系地址更新规则：*detail_address, full_name*

```sql
CREATE PROCEDURE address_exchange_todo3669 IS
    v_mem        varchar2(100);
    v_company_id number;
    CURSOR c_address IS
        WITH ar AS (
            SELECT '00052002001002001001' full_code, '00052002001002001' parent_full_code
            FROM dual
            UNION ALL
            SELECT '00052002001001001001' full_code, '00052002001001001' parent_full_code
            FROM dual
            UNION ALL
            SELECT '00052002004001001001' full_code, '00052002004001001' parent_full_code
            FROM dual
        )
        SELECT oa.id old_address_id, na.id new_address_id, na.full_name new_full_name
        FROM ar
                 JOIN boss_area.address oa
                      ON ar.full_code = oa.full_code
                 JOIN boss_area.address na
                      ON ar.parent_full_code = na.full_code
        WHERE oa.company_id = v_company_id
          AND na.company_id = v_company_id
          AND oa.status = 1
          AND na.status = 1;
BEGIN
    v_company_id := 23; ---------------------------
    v_mem := ',BOSSTODO-3669_exchange'; -----------------------------

    FOR v_address IN c_address
        LOOP
            -- 万村通修改为否
            UPDATE boss_area.address
            SET is_all_village_online = 0,
                modify_instant        = systimestamp,
                mem                   = mem || v_mem
            WHERE company_id = v_company_id
              AND id = v_address.old_address_id;
            COMMIT;
            --客户地址
            UPDATE boss_customer.customer
            SET address_id      = v_address.new_address_id,
                modify_instant  = systimestamp,
                contact_address = detail_address || ',' || v_address.new_full_name,
                mem             = mem || v_mem || '_old_address_id=' || address_id
            WHERE company_id = v_company_id
              AND address_id = v_address.old_address_id;
            COMMIT;
            --用户地址
            UPDATE boss_customer.subscriber
            SET address_id     = v_address.new_address_id,
                modify_instant = systimestamp,
                mem            = mem || v_mem || '_old_address_id=' || address_id
            WHERE company_id = v_company_id
              AND address_id = v_address.old_address_id;
            COMMIT;
        END LOOP;
END;
/

BEGIN 
    address_exchange_todo3669();
END;
```
---
### 存储过程执行
执行
```sql
CREATE PROCEDURE address_exchange_todo3669 IS
BEGIN 
address_exchange_todo3669();
END;
```
调试
```sql
DECLARE PROCEDURE address_exchange_todo3669 IS
```