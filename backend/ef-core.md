# Key idea: C# -> EF Core -> SQL -> Dataset

## 1. SELECT

SQL:
```SQL
SELECT * FROM DataSets;
```
C#:
```C# 
var list = await _context.DataSets.ToListAsync();
```
SQL: 
```SQL
SELECT * FROM DataSets WHERE ID = 5;
```
C#:
```C#  
var list = await _context.DataSets.Where(x => x.ID == 5).ToListAsync();
```

SQL:
```SQL 
SELECT TOP 1 * FROM DataSets WHERE ID = 5; (Single row)
```
C#:
```C# 
var item = await _context.DataSets.FirstOrDefaultAsync(x => x.ID ==5);
```

## 2. INSERT

SQL:
```SQL
INSERT INTO DataSets (Name) VALUES ('Test');
```
C#
```C# 
var dataSet = new dataSet
{
  Name = "Test"
};
_content.DataSets.Add(dataSet);
await _content.SaveChangeAsync();
```

## 3. UPDATE
SQL:
```SQL
UPDATE DataSets SET Name = 'NewName' WHERE Id = 5;
```
C#
```C# 
var data = _content.DataSets.FindAsync(5);
data.Name = 'NewName';

await _content.SaveChangeAsync();
```

## 4.DELETE
SQL:
```SQL
DELETE FROM DataSets WHERE Id =5;
```
C#:
```C# 
var data = await _Context.Datasets.FindAsync(5);
_content.DataSets.Remove(data);
await _context.SaveChangeAsync();
```

## 5.COUNT
SQL:
```SQL
SELECT COUNT(*) FROM DataSets WHERE DataSetId = 5;
```
C#:
```C# 
var count = await _Content.Datasets.CountAsync(x => x.DataSetId == 5);
```

## 6.EXISTS
SQL:
```SQL
SELECT 1 FROM DataSets WHERE DataSetId =5;
```
C#:
```C# 
var exists = await _Content.DataSets.AnyAsync (x => x. DataSetId ==5);
```

## 7. SELECT specific fields
SQL:
```SQL
SELECT Name FROM DataSets;
```
C#:
```C# 
var names = await _Content.DataSets.Select(x => x.Name).ToListAsync();
```

## 8. ORDER BY
SQL:
```SQL
SELECT * FROM DataSets ORDER BY CreateAt DESC;
```
C#:
```C# 
var order = await _Content.DataSets.OrderByDescending (x => x.CreateAt).ToListAsync();
```

## 9 JOIN (relationship)
SQL:
```SQL
SELECT * 
FROM DataSetImages dsi
JOIN DataSets ds ON dsi.DataSet_Id = ds.Id;
```
C#:
```C# 
var data = await _context.DataSetImages
    .Include(x => x.DataSet)
    .ToListAsync();
```

## 10 WHERE + AND
SQL:
```SQL
SELECT * FROM DataSetImages 
WHERE DataSet_Id = 5 AND DeletedAt IS NULL;
```
C#:
```C# 
var list = await _context.DataSetImages
    .Where(x => x.DataSet_Id == 5 && x.DeletedAt == null)
    .ToListAsync();
```




