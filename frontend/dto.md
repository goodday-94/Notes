
# How dto works?

# Frontend JSON <-> DTOs <-> ModelFactory <-> MLModels (Entities) <-> Database

# DTO → Model & Model → DTO; through ModelFactory

# Examples:
# 1. Model (database/entity)
public class DataSet
{
    public long Id { get; set; }
    public string Name { get; set; }
    public DateTime CreatedAt { get; set; }
}
# 2. DTO (API transfer)
public class DataSetDto
{
    public long Id { get; set; }
    public string Name { get; set; }
}

# Notice:

DataSet has CreatedAt
DataSetDto does not

So conversion decides what to keep.

DTO → Model

Example: frontend sends a DTO to backend, backend converts it into entity for database.

public DataSet Create(DataSetDto dto)
{
    return new DataSet
    {
        Id = dto.Id,
        Name = dto.Name,
        CreatedAt = DateTime.Now
    };
}

Meaning:

read values from dto
create a new DataSet
Model → DTO

Example: backend reads entity from database, then converts it into DTO for frontend.

public DataSetDto Create(DataSet model)
{
    return new DataSetDto
    {
        Id = model.Id,
        Name = model.Name
    };
}

Meaning:

read values from model
create a new DataSetDto

# Typical place: ModelFactory.cs

Very often this file contains methods like:

public class ModelFactory
{
    public DataSet Create(DataSetDto dto)
    {
        return new DataSet
        {
            Id = dto.Id,
            Name = dto.Name,
            CreatedAt = DateTime.Now
        };
    }

    public DataSetDto Create(DataSet model)
    {
        return new DataSetDto
        {
            Id = model.Id,
            Name = model.Name
        };
    }
}

So ModelFactory is just a conversion helper.

# How it is used in controller
POST example
[HttpPost]
public async Task<ActionResult<DataSetDto>> PostDataSet(DataSetDto dataSetDto)
{
    var dataSet = _modelFactory.Create(dataSetDto); // DTO -> Model

    _context.DataSets.Add(dataSet);
    await _context.SaveChangesAsync();

    var resultDto = _modelFactory.Create(dataSet); // Model -> DTO
    return resultDto;
}
