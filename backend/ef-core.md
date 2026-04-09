# Key idea: C# -> EF Core -> SQL -> Dataset

# 1. SELECT

// SQL: 
SELECT * FROM DataSets;
// C#: 
var list = await _context.DataSets.ToListAsync();

// SQL: 
SELECT * FROM DataSets WHERE ID = 5;
// C#: 
var list = await _context.DataSets.Where(x => x.ID == 5).ToListAsync();

// SQL: 
SELECT TOP 1 * FROM DataSets WHERE ID = 5; (Single row)
// C#: 
var item = await _context.DataSets.FirstOrDefaultAsync(x => x.ID ==5);

# 2. INSERT

// SQL:
INSERT INTO DataSets (Name) VALUES ('Test');
// C#
var dataSet = new dataSet
{
  Name = "Test"
};
_content.DataSets.Add(dataSet);
await _content.SaveChangeAsync();

# 3. UPDATE
// SQL:
UPDATE DataSets SET Name = 'NewName' WHERE Id = 5;
// C#
var data = _content.DataSets.FindAsync(5);
data.Name = 'NewName';

await _content.SaveChangeAsync();



