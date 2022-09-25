# Gorm框架的使用注意事项


<!--more-->

# 1.删除指令

```go
//1.软删除----模型包含了一个 gorm.deletedat 字段
db.Delete(&user)
// UPDATE users SET deleted_at="2013-10-29 10:23" WHERE id = 111;

//2.永久删除
db.Unscoped().Delete(&order)
// DELETE FROM orders WHERE id=10;
```

# 2.ErrRecordNotFound

> Gorm框架有时，查不到数据会返回RecordNotFound错误，但并不是真的发生逻辑错误

```go
user = &User{}
res := db.First(user)

//可以通过user==null来判断是否查到数据
//配置文件忽略查询结果为空的错误
ignore_record_not_found_error: true
```

# 3.SkipDefaultTransaction

> 在事务内执行写入（创建/更新/删除）操作以确保数据一致性，这对性能不利，可以在关闭默认事务

```go
db, err := gorm.Open(sqlite.Open("gorm.db"), &gorm.Config{
  SkipDefaultTransaction: true,
})
```

# 4.事务里的find

> 事务里的find语句是默认不加锁，使用以下语句才会加锁

`tx.Clauses(clause.Locking{Strength: "UPDATE"}).Find(&acc, "id=?", id)`

```go
for i := 0; i < 100; i++ {
	go func() {
		defer func() {
			wg.Done()
		}()
		db.Transaction(func(tx *gorm.DB) error {
			acc := Account{}
                           //1.先Find数据
			res := tx.Clauses(clause.Locking{Strength: "UPDATE"}).Find(&acc, "id=?", id)
			if res.Error != nil {
				return res.Error
			}
                           //2.库存加一
			acc.Balance += 1
                           //3.保存
			res = tx.Save(&acc)
			if res.Error != nil {
				return res.Error
			}
			return nil
		})

	}()
}
wg.Wait()
```

# 5.事务里发生异常

> gorm发生异常会回滚，此次操作相当于没有

```go
for i := 0; i < 100; i++ {
	j := i
	go func() {
                //1.此处recover，使异常止于此goroutine
		defer func() {
			if err := recover(); err != nil {
			}
			wg.Done()
		}()
		db.Transaction(func(tx *gorm.DB) error {
			acc := Account{}
			res := tx.Clauses(clause.Locking{Strength: "UPDATE"}).Find(&acc, "id=?", id)
			if res.Error != nil {
				return res.Error
			}
			acc.Balance += 1
			res = tx.Save(&acc)
			if res.Error != nil {
				return res.Error
			}
                       //2.手工制造异常，此两次操作会被回滚
			if j == 2 || j == 4 {
				panic("j = 2 or 4 exit")
			}
			return nil
		})
	}()
}
wg.Wait()
```

