# PeopleApp

## Setup
- Maak een nieuwe ASP.NET Core Web API applicatie aan met de naam PeopleApp.Api.
- Zorg voor de volgende folder-structuur:
    ```
    PeopleApp.Api
    │- Controllers (empty)
    │- Data
        │- AppDbContext.cs    
    │- Entities
        │- Department.cs   
        │- Location.cs
        │- Person.cs
    ```
- Vervolledig de AppDbContext klasse in de Data folder en registreer deze in de DI container van de applicatie. Gebruik hiervoor de PeopleConnection uit het appsettings.json bestand.
	```csharp
    var connectionString = builder.Configuration.GetConnectionString("PeopleConnection");
    builder.Services.AddDbContext<AppDbContext>(options =>
    {
        options.UseSqlServer(connectionString);    
    });
    ```
- Vervolledig de entity classes
    ```csharp title="Department.cs"
    public class Department
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public IEnumerable<Person> People { get; set; }
    }
    ```
    ```csharp title="Department.cs"
    public class Location
    {
        public long Id { get; set; }
        public string City { get; set; }
        public string State { get; set; }
        public IEnumerable<Person> People { get; set; }
    }
    ```
    ```csharp title="Department.cs"
    public class Person
    {
        public long Id { get; set; }
        public string Firstname { get; set; }
        public string Surname { get; set; }
        public Department Department { get; set; }
        public long DepartmentId { get; set; }
        public Location Location { get; set; }
        public long LocationId { get; set; }
    }
    ```
