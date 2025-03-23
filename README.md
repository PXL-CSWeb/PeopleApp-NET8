﻿# PeopleApp

## Setup
### Project
- Maak een nieuwe ASP.NET Core Web API applicatie aan met de naam PeopleApp.Api.

| Template    | Configure   | Additional info |
| ----------- | ----------- | --------------- |
|![project template](media/createnewproject.png)|![configure template](media/configurenewproject.png)|![project additional info](media/projectinfo.png)|

### Folders & bestanden
- Verwijder de bestaande WeatherForecastController.cs en WeatherForecast.cs bestanden
- Zorg voor de volgende folder-structuur en bestanden:
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

### Packages
- Installeer onderstaande NuGet packages:
    -   ```
        Microsoft.EntityFrameworkCore.SqlServer
        ``` 
    -   ```
        Microsoft.EntityFrameworkCore.Tools
        ```
> [!CAUTION]
> Let op dat je de juiste versie selecteert!

![nuget packages to install](media/nuget.png)

### DbContext
- Vervolledig de AppDbContext klasse in de Data folder en registreer deze in de DI container van de applicatie. Gebruik hiervoor de PeopleConnection uit het appsettings.json bestand.
    ```csharp
    public class AppDbContext : DbContext
    {
        public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) { }

        public DbSet<Department> Departments { get; set; }
        public DbSet<Location> Locations { get; set; }
        public DbSet<Person> People { get; set; }
    }
    ```
	```csharp
    var connectionString = builder.Configuration.GetConnectionString("PeopleConnection");
    builder.Services.AddDbContext<AppDbContext>(options =>
    {
        options.UseSqlServer(connectionString);    
    });
    ```

### Entities
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
    ```cs title="Department.cs"
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
### Migrations
- Voer de volgende commando's uit in de Package Manager Console:
    ```
    Add-Migration Initial
    Update-Database
    ```

### Seed Data
- Maak een nieuw class aan in de Data folder met de naam ```DbInitializer```:
    ```
    public static class DbInitializer
    {
        public static void SeedData(this WebApplication app)
        {
            using (var scope = app.Services.CreateScope())
            {
                AppDbContext context = scope.ServiceProvider.GetRequiredService<AppDbContext>();
                context.SeedPeopleData();
            }
        }

        private static void SeedPeopleData(this AppDbContext context)
        {
            context.Database.Migrate();
            if (!context.People.Any() && !context.Departments.Any() && !context.Locations.Any())
            {

                Department d1 = new Department { Name = "Sales" };
                Department d2 = new Department { Name = "Development" };
                Department d3 = new Department { Name = "Support" };
                Department d4 = new Department { Name = "Facilities" };

                context.Departments.AddRange(d1, d2, d3, d4);
                context.SaveChanges();

                Location l1 = new Location { City = "Oakland", State = "CA" };
                Location l2 = new Location { City = "San Jose", State = "CA" };
                Location l3 = new Location { City = "New York", State = "NY" };
                context.Locations.AddRange(l1, l2, l3);

                context.People.AddRange(
                    new Person
                    {
                        Firstname = "Francesca",
                        Surname = "Jacobs",
                        Department = d2,
                        Location = l1
                    },
                    new Person
                    {
                        Firstname = "Charles",
                        Surname = "Fuentes",
                        Department = d2,
                        Location = l3
                    },
                    new Person
                    {
                        Firstname = "Bright",
                        Surname = "Becker",
                        Department = d4,
                        Location = l1
                    },
                    new Person
                    {
                        Firstname = "Murphy",
                        Surname = "Lara",
                        Department = d1,
                        Location = l3
                    },
                    new Person
                    {
                        Firstname = "Beasley",
                        Surname = "Hoffman",
                        Department = d4,
                        Location = l3
                    },
                    new Person
                    {
                        Firstname = "Marks",
                        Surname = "Hays",
                        Department = d4,
                        Location = l1
                    },
                    new Person
                    {
                        Firstname = "Underwood",
                        Surname = "Trujillo",
                        Department = d2,
                        Location = l1
                    },
                    new Person
                    {
                        Firstname = "Randall",
                        Surname = "Lloyd",
                        Department = d3,
                        Location = l2
                    },
                    new Person
                    {
                        Firstname = "Guzman",
                        Surname = "Case",
                        Department = d2,
                        Location = l2
                    });
                context.SaveChanges();
            }
        }
    }
    ```
- Zorg nu dat deze methode wordt aangeroepen in de ```Program``` class net voor de applicatie wordt gestart:
    ```csharp
    app.SeedData();
    app.Run();
    ```
### Run!
De applicatie kan nu gestart worden zonder fouten **MAAR** de API heeft nog geen functionaliteit.

![empty swagger page](media/swagger_empty.png)

