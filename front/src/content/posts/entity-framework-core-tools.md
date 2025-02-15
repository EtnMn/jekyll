---
title: "Entity Framework Core tools reference"
published: 2024-10-16
subTitle: "Tools that facilitate the use of Entity Framework."
tags: ["Entity Framework", "Visual Studio"]
draft: false
---

<mark>Entity Framework</mark> (EF) Core tools are available in two main forms: the _.NET Core CLI tools_ (dotnet-ef) and the _Package Manager Console_ (PMC) tools in <mark>Visual Studio</mark>. Both tools provide similar functionality but are used in different environments and have different workflows.

_EF Core tools_ are essential for developers working with _Entity Framework Core_, providing powerful commands to manage database migrations, scaffold models, and maintain the database schema in sync with the application's data model.

## .NET Core CLI

The _.NET Core CLI (dotnet-ef)_ offers the advantage of being cross-platform, working seamlessly on Windows, macOS, and Linux, making it ideal for diverse development environments. It is highly suitable for automation and scripting, allowing easy integration into CI/CD pipelines and batch operations. They provide IDE independence, enabling use with any text editor or IDE, and are particularly efficient for command-line operations.

### Installing dotnet-ef

_dotnet ef_ can be [installed](https://learn.microsoft.com/en-us/ef/core/cli/dotnet) as either a global or local tool. Most developers prefer installing dotnet ef as a global tool using the following command:

```shell
dotnet tool install --global dotnet-ef
```

Or updated with:

```shell
dotnet tool update --global dotnet-ef
```

Before you can use the tools on a specific project, you'll need to add the <mark>Microsoft.EntityFrameworkCore.Design</mark> package to it:

```shell
dotnet add package Microsoft.EntityFrameworkCore.Design
```

To verify that EF Core CLI tools are correctly installed, you can use:

```shell
dotnet ef
```

### Add a new migration

A <mark>migration</mark> allows you to incrementally update the database schema to keep it in sync with the application's data model. Migrations provide a way to apply changes to the database schema over time as the data model evolves, without losing existing data. The following command allows you to add a new migration:

```shell
dotnet ef migrations add MigrationName
```

If the solution contains several projects, [command line options](https://learn.microsoft.com/en-us/ef/core/cli/dotnet#common-options) must be used to specify locations:

* `--project <PROJECT> / -p`: Relative path to the project folder of the target project. Default value is the current folder
* `--startup-project <PROJECT> / -s`: Relative path to the project folder of the startup project. Default value is the current folder
* `--output-dir <PATH> / -o`: The directory to put files in. Paths are relative to the project directory

For example:

```shell
dotnet ef migrations add NewMigration -s ".\src\API" -p ".\src\Infrastructure" -o "Data/Migrations"
```

This command adds a new migration named _NewMigration_ to the project located in _.\src\Infrastructure_, using the startup project located in _.\src\API_. The migration files will be placed in the _Data/Migrations_ directory within the _.\src\Infrastructure_ project.

### Update the Database

To apply the pending migrations to the database, and update the schema to match the current data model, you have to use the `update` command:

```shell
dotnet ef database update
```

As for the migration and regarding to your project, options can also be used:

```shell
dotnet ef database update -s ".\src\API" -p ".\src\Infrastructure"
```

The migration state is saved in a special tabgitle within the database called `__EFMigrationsHistory`. This table keeps track of all the migrations that have been applied to the database, ensuring that the database schema is in sync with the application's data model.

### Update an existing migration

When changes require updating an __existing migration__, you have to remove it and then create a new correct one:

```shell
dotnet ef migrations remove -s ".\src\API" -p ".\src\Infrastructure"
```

You can remove multiple migrations in Entity Framework Core, but you need to do it one at a time.

If the migration to remove has already been applied to the database, it will return the following error: `The migration '...' has already been applied to the database. Revert it and try again.[...]`. To revert to a previous migration, you need to update the database to a specific migration. This process involves applying all the migrations up to and including the specified migration, effectively rolling back any migrations that were applied after it. List all migrations to find the targeted one:

```shell
dotnet ef migrations list
```

Then revert to the migration to update and remove it:

```shell
dotnet ef database update MigrationToUpdate
dotnet ef migrations remove
```

## Package Manager Console in Visual Studio

When using Visual Studio, consider using the <mark>Package Manager Console</mark> tools instead of the CLI tools. [Package Manager Console tools for Entity Framwork](https://learn.microsoft.com/en-us/ef/core/cli/powershell) automatically:

* Works with the current project selected in the Package Manager Console without requiring that you manually switch directories
* Opens files generated by a command after the command is completed
* Provides tab completion of commands, parameters, project names, context types, and migration names

### Installing The Package Manager Console tools for Entity Framework Core

Install or update the Package Manager Console tools by running the following command in <mark>Package Manager Console</mark>:

```shell
Install-Package Microsoft.EntityFrameworkCore.Tools
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### Using the tools

It is similar to the _dotnet-ef_ tool.

1. Add a migration:

```shell
Add-Migration <MigrationName>
```

2. Update the database:

```shell
Update-Database [-Migration <MigrationName>]
```

3. Remove a migration:

```shell
Remove-Migration
```
