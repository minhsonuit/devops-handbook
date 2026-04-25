# Database Migration Strategies

> Ngay bat dau: ___

## Zero-downtime migration

### Expand-Contract pattern

```
1. EXPAND   — Them cot/table moi (backward compatible)
2. MIGRATE  — App doc/ghi ca cu va moi
3. BACKFILL — Chuyen du lieu tu cu sang moi
4. CONTRACT — Xoa cot/table cu khi da xong
```

Vi du: doi ten cot `Name` → `FullName`

```sql
-- Step 1: EXPAND
ALTER TABLE Customers ADD FullName NVARCHAR(200);

-- Step 2: BACKFILL (co the chay background)
UPDATE Customers SET FullName = Name WHERE FullName IS NULL;

-- Step 3: App doi sang dung FullName

-- Step 4: CONTRACT (sau khi tat ca app da cap nhat)
ALTER TABLE Customers DROP COLUMN Name;
```

### Blue-Green database

```
Blue DB (current) ← App reads/writes
Green DB (new)    ← Prepare + test

1. Tao Green DB
2. Sync data Blue → Green
3. Test Green
4. Switch app → Green
5. Keep Blue 24-48h de rollback
```

## EF Core Migrations

```bash
# Tao migration
dotnet ef migrations add AddFullNameColumn

# Xem SQL se chay
dotnet ef migrations script

# Apply
dotnet ef database update

# Rollback
dotnet ef database update PreviousMigrationName
```

### Best practices EF Core migrations

- Luon review SQL truoc khi apply production
- Khong dung `EnsureCreated()` trong production
- Tach migration thanh nhieu buoc nho
- Khong xoa cot ngay — deprecate truoc
- Test migration tren staging truoc

## Rollback strategies

| Strategy | Khi nao |
|----------|---------|
| EF rollback | Schema change don gian |
| Restore backup | Data corruption, sai lon |
| Feature flag | Tat feature, giu schema |
| Dual-write | Ghi ca cu va moi, switch khi san sang |

## Checklist truoc khi migrate production

- [ ] Backup database
- [ ] Test migration tren staging
- [ ] Review SQL script
- [ ] Estimate thoi gian chay (table lon?)
- [ ] Co plan rollback
- [ ] Thong bao team
- [ ] Chay ngoai gio peak
