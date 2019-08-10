## 问题

最近在 **Qt** 中使用 **SQLite** 数据库，其中用到批量插入，发现，总共30几条数据的插入，居然耗时3s，那么后面还有5000多条的数据，岂不是需要几分，这是不能接收的。

上网查询发现，**SQLite** 这种嵌入式的数据库（以文件的形式保存在本地），和 **MySQL** 等数据库的机制是不一样的，它的每条事务都有打开和关闭文件的步骤。而 **SQLite** 默认将每条语句当作单独的事务，所以，当批量插入的时候，会出现大量的 **IO** 操作，效率很低，耗时很长。

所以，这时候，就需要事务，在批量之前打开事务，批量插入结束后，提交事务。

实际测试结果，5000多条数据，耗时约700ms。

## 代码示例

```c++
bool PrivateUtil::InsertAPDMonitPointInfo(const QString &database_connect_name, const QList<DataInfo> &list)
{
    if(list.isEmpty())
    {
        return true;
    }
    QSqlDatabase database = QSqlDatabase::database(database_connect_name);
    if(database.isOpenError())
    {
        qWarning()<<QString("Open database fail:%1").arg(database.lastError().text());
        return false;
    }
    database.transaction();//打开事务
    QSqlQuery query(database);
    //批量插入
    query.prepare("INSERT INTO monit_point_info (monit_code, monit_name,"
                  "data_type, machine_dev_code, monit_point_dev_code, phase) "
                  "VALUES(?,?,?,?,?,?);");
    QVariantList code_list,name_list,type_list,machine_list,monit_list, phase_list;
    for(int index = 0; index < list.count(); index++)
    {
        DataInfo info = list.at(index);
        code_list.append(info.getCollectorPointCode());
        name_list.append(info.getDescriptioon());
        type_list.append("Data");
        machine_list.append(info.getMachineCode());
        monit_list.append(info.getMonitCode());
        phase_list.append(info.getPhase());
    }
    query.addBindValue(code_list);
    query.addBindValue(name_list);
    query.addBindValue(type_list);
    query.addBindValue(machine_list);
    query.addBindValue(monit_list);
    query.addBindValue(phase_list);
    
    if(!query.execBatch())
    {
        qWarning()<<QString("Exec batch fail:%1").arg(query.lastError().text());
        database.commit();
        return false;
    }
    database.commit();//提交事务

    return true;
}
```

