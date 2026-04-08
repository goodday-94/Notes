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
