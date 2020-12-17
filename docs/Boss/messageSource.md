## 国际化内容

### 国际化内容携带参数

```properties
problemDomain.call.out.task.assign.adjust.number.than.max= {0} 分配失败，因为可分配数量已经超过上限，请重新分配。
```

service 抛出异常
```java
callOutTaskAssigns.forEach(cta -> {
    if(cta.getAllocateNumber() + adjustAllocateNumber > 99999){
        userIds.add(cta.getEmployeeId());
    }
});
if(!userIds.isEmpty()){
    Map<Long, String> userNames = SystemCacheUtil.getInstance().getEmployeeNames(userIds);
    throw new MsrRuntimeException("problemDomain.call.out.task.assign.adjust.number.than.max", true, false, null, userNames.values().toString());
}
```


### 国际化内容注意事项

```properties
problemDomain.call.out.task.assign.adjust.number.than.max=L\'\'allocation de {0} a échoué car le nombre d\'\'éléments pouvant être alloués a dépassé la limite supérieure, veuillez redistribuer.
```
当为法语的时候，需要对 **'** 符号进行转义，转义为 **\\'\\'**, 否则 {} 中的数据无法展示出来