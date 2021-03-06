# ASP.NET Boilerplate v5+ to ABP Framework Migration

ABP Framework is **the successor** of the open source [ASP.NET Boilerplate](https://aspnetboilerplate.com/) framework. This guide aims to help you to **migrate your existing solutions** (you developed with the ASP.NET Boilerplate framework) to the ABP Framework.

## Introduction

**ASP.NET Boilerplate** is being **actively developed** [since 2013](https://github.com/aspnetboilerplate/aspnetboilerplate/graphs/contributors). It is loved, used and contributed by the community. It started as a side project of [a developer](http://halilibrahimkalkan.com/), but now it is officially maintained and improved by the company [Volosoft](https://volosoft.com/) in addition to the great community support.

ABP Framework has the same goal of the ASP.NET Boilerplate framework: **Don't Repeat Yourself**! It provides infrastructure, tools and startup templates to make a developer's life easier while developing enterprise software solutions.

See [the introduction blog post](https://blog.abp.io/abp/Abp-vNext-Announcement) if you wonder why we needed to re-write the ASP.NET Boilerplate framework.

### Should I Migrate?

No, you don't have to!

* ASP.NET Boilerplate is still in active development and maintenance.
* It also works on the latest ASP.NET Core and related libraries and tools. It is up to date.

However, if you want to take the advantage of the new ABP Framework [features](https://abp.io/features) and the new architecture opportunities (like support for NoSQL databases, microservice compatibility, advanced modularity), you can use this document as a guide.

### What About the ASP.NET Zero?

[ASP.NET Zero](https://aspnetzero.com/) is a commercial product developed by the core ASP.NET Boilerplate team, on top of the ASP.NET Boilerplate framework. It provides pre-built application [features](https://aspnetzero.com/Features), code generation tooling and a nice looking modern UI. It is trusted and used by thousands of companies from all around the World.

We have created the [ABP Commercial](https://commercial.abp.io/) as an alternative to the ASP.NET Zero. ABP Commercial is more modular and upgradeable compared to the ASP.NET Zero. It currently has less features compared to ASP.NET Zero, but the gap will be closed by the time (it also has some features don't exist in the ASP.NET Zero).

We think ASP.NET Zero is still a good choice while starting a new application. It is production ready and mature solution delivered as a full source code. It is being actively developed and we are constantly adding new features.

We don't suggest to migrate your ASP.NET Zero based solution to the ABP Commercial if;

* Your ASP.NET Zero solution is mature and it is in maintenance rather than a rapid development.
* You don't have enough development time to perform the migration.
* A monolithic solution fits in your business.
* You've customized existing ASP.NET Zero features too much based on your requirements.

We also suggest you to compare the features of two products based on your needs.

If you have an ASP.NET Zero based solution and want to migrate to the ABP Commercial, this guide will also help you.

## The Migration Progress

We've designed the ABP Framework by **getting the best parts** of the ASP.NET Boilerplate framework, so it will be familiar to you if you've developed ASP.NET Boilerplate based applications.

In the ASP.NET Boilerplate, we have not worked much on the UI side, but used some free themes (we've used [metronic theme](https://keenthemes.com/metronic/) for ASP.NET Zero on the other side). In the ABP Framework, we worked a lot on the UI side (especially for the MVC / Razor Pages UI, because Angular already has a good modular system of its own). So, the **most challenging part** of the migration will be the **User Interface** of your solution.

ABP Framework is (and ASP.NET Boilerplate was) designed based on the [Domain Driven Design](https://docs.abp.io/en/abp/latest/Domain-Driven-Design) patterns & principles and the startup templates are layered based on the DDD layers. So, this guide respects to that layering model and explains the migration layer by layer.

## Creating the Solution

First step of the migration is to create a new solution. We suggest you to create a fresh new project using [the startup templates](https://abp.io/get-started) (see [this document](https://docs.abp.io/en/commercial/latest/getting-started) for the ABP Commercial).

After creating the project and running the application, you can copy your code from your existing solution to the new solution step by step, layer by layer.

### About Pre-Built Modules

The startup projects for the ABP Framework use the [pre-built modules](https://docs.abp.io/en/abp/latest/Modules/Index) (not all of them, but the essentials) and themes as NuGet/NPM packages. So, you don't see the source code of the modules/themes in your solution. This has an advantage that you can easily update these packages when a new version is released. However, you can not easily customize them as their source code in your hands.

We suggest to continue to use these modules as package references, in this way you can get new features easily (see [abp update command](https://docs.abp.io/en/abp/latest/CLI#update)). In this case, you have a few options to customize or extend the functionality of the used modules;

* You can create your own entity and share the same database table with an entity in a used module. An example of this is the `AppUser` entity comes in the startup template.
* You can [replace](https://docs.abp.io/en/abp/latest/Dependency-Injection#replace-a-service) a domain service, application service, controller, page model or other types of services with your own implementation. We suggest you to inherit from the existing implementation and override the method you need.
* You can replace a `.cshtml` view, page, view component, partial view... with your own one using the [Virtual File System](https://docs.abp.io/en/abp/latest/Virtual-File-System).
* You can override javascript, css, image or any other type of static files using the [Virtual File System](https://docs.abp.io/en/abp/latest/Virtual-File-System).

More extend/customization options will be developed and documented by the time. However, if you need to fully change the module implementation, it is best to add the [source code](https://github.com/abpframework/abp/tree/dev/modules) of the related module into your own solution and remove the package dependencies.

The source code of the modules and the themes are [MIT](https://opensource.org/licenses/MIT) licensed, you can fully own and customize it without any limitation (for the ABP Commercial, you can download the source code of a [module](https://commercial.abp.io/modules)/[theme](https://commercial.abp.io/themes) if you have a [license](https://commercial.abp.io/pricing) type that includes the source code).

## The Domain Layer

Most of your domain layer code will remain same, while you need to perform some minor changes in your domain objects.

### Aggregate Roots & Entities

The ABP Framework and the ASP.NET Boilerplate both have the `IEntity` and `IEntity<T>` interfaces and `Entity` and `Entity<T>` base classes to define entities but they have some differences.

If you have an entity in the ASP.NET Boilerplate application like that:

````csharp
public class Person : Entity //Default PK is int for the ASP.NET Boilerplate
{
    ...
}
````

Then your primary key (the `Id` property in the base class) is `int` which is the **default primary key** (PK) type for the ASP.NET Boilerplate. If you want to set another type of PK, you need to explicitly declare it:

````csharp
public class Person : Entity<Guid> //Set explicit PK in the ASP.NET Boilerplate
{
    ...
}
````

ABP Framework behaves differently and expects to **always explicitly set** the PK type:

````csharp
public class Person : Entity<Guid> //Set explicit PK in the ASP.NET Boilerplate
{
    ...
}
````

`Id` property (and the corresponding PK in the database) will be `Guid` in this case.

#### Composite Primary Keys

ABP Framework also has a non-generic `Entity` base class, but this time it has no `Id` property. Its purpose is to allow you to create entities with composite PKs. See [the documentation](https://docs.abp.io/en/abp/latest/Entities#entities-with-composite-keys) to learn more about the composite PKs.

#### Aggregate Root

It is best practice now to use the `AggregateRoot` base class instead of `Entity` for aggregate root entities. See [the documentation](https://docs.abp.io/en/abp/latest/Entities#aggregateroot-class) to learn more about the aggregate roots.

In opposite to the ASP.NET Boilerplate, the ABP Framework creates default repositories (`IRepository<T>`) **only for the aggregate roots**. It doesn't create for other types derived from the `Entity`.

If you still want to create default repositories for all entity types, find the *YourProjectName*EntityFrameworkCoreModule class in your solution and change `options.AddDefaultRepositories()` to `options.AddDefaultRepositories(includeAllEntities: true)` (it may be already like that for the application startup template).

#### Migrating the Existing Entities

We suggest & use the GUID as the PK type for all the ABP Framework modules. However, you can continue to use your existing PK types to migrate your database tables easier.

The challenging part will be the primary keys of the ASP.NET Boilerplate related entities, like Users, Roles, Tenants, Settings... etc. Our suggestion is to copy data from existing database to the new database tables using a tool or in a manual way (be careful about the foreign key values).

#### Documentation

See the documentation for details on the entities:

* [ASP.NET Boilerplate - Entity documentation](https://aspnetboilerplate.com/Pages/Documents/Entities)
* [ABP Framework - Entity documentation](https://docs.abp.io/en/abp/latest/Entities)

### Repositories

> ABP Framework creates default repositories (`IRepository<T>`) **only for the aggregate roots**. It doesn't create for other types derived from the `Entity`. See the "Aggregate Root" section above for more information.

The ABP Framework and the ASP.NET Boilerplate both have the default generic repository system, but has some differences.

#### Injecting the Repositories

In the ASP.NET Boilerplate, there are two default repository interfaces you can directly inject and use:

* `IRepository<TEntity>` (e.g. `IRepository<Person>`) is used for entities with `int` primary key (PK) which is the default PK type.
* `IRepository<TEntity, TKey>` (e.g. `IRepository<Person, Guid>`) is used for entities with other types of PKs.

ABP Framework doesn't have a default PK type, so you need to **explicitly declare the PK type** of your entity, like `IRepository<Person, int>` or `IRepository<Person, Guid>`.

ABP Framework also has the `IRepository<TEntity>` (without PK), but it is mostly used when your entity has a composite PK (because this repository has no methods work with the `Id` property). See [the documentation](https://docs.abp.io/en/abp/latest/Entities#entities-with-composite-keys) to learn more about the **composite PKs**.

#### Restricted Repositories

ABP Framework additionally provides a few repository interfaces:

* `IBasicRepository<TEntity, TKey>` has the same methods with the `IRepository` except it doesn't have `IQueryable` support. It can be useful if you don't want to expose complex querying code to the application layer. In this case, you typically want to create custom repositories to encapsulate the querying logic. It is also useful for database providers those don't support `IQueryable`.
* `IReadOnlyRepository<TEntity,TKey>` has the methods get data from the database, but doesn't contain any method change the database.
* `IReadOnlyBasicRepository<TEntity, TKey>` is similar to the read only repository but also doesn't support `IQueryable`.

All the interfaces also have versions without `TKey` (like ``IReadOnlyRepository<TEntity>`) those can be used for composite PKs just like explained above.

#### GetAll() vs IQueryable

ASP.NET Boilerplate's repository has a `GetAll()` method that is used to obtain an `IQueryable` object to execute LINQ on it. An example application service calls the `GetAll()` method:

````csharp
public class PersonAppService : ApplicationService, IPersonAppService
{
    private readonly IRepository<Person, Guid> _personRepository;

    public PersonAppService(IRepository<Person, Guid> personRepository)
    {
        _personRepository = personRepository;
    }

    public async Task DoIt()
    {
        var people = await _personRepository
            .GetAll() //GetAll() returns IQueryable
            .Where(p => p.BirthYear > 2000) //Use LINQ extension methods
            .ToListAsync();
    }
}
````

ABP Framework's repository doesn't have this method. Instead, it implements the `IQueryable` itself. So, you can directly use LINQ on the repository:

````csharp
public class PersonAppService : ApplicationService, IPersonAppService
{
    private readonly IRepository<Person, Guid> _personRepository;

    public PersonAppService(IRepository<Person, Guid> personRepository)
    {
        _personRepository = personRepository;
    }

    public async Task DoIt()
    {
        var people = await _personRepository
            .Where(p => p.BirthYear > 2000) //Use LINQ extension methods
            .ToListAsync();
    }
}
````

> Note that in order to use the async LINQ extension methods (like `ToListAsync` here), you may need to depend on the database provider (like EF Core) since these methods are defined in the database provider package, they are not standard LINQ methods.

#### FirstOrDefault(predicate), Single()... Methods

ABP Framework repository has not such methods get predicate (expression) since the repository itself is `IQueryable` and all these methods are already standard LINQ extension methods those can be directly used.

However, it provides the following methods those can be used to query a single entity by its Id:

* `FindAsync(id)` returns the entity or null if not found.
* `GetAsync(id)` method returns the entity or throws an `EntityNotFoundException` (which causes HTTP 404 status code) if not found.

#### Sync vs Async 

ABP Framework repository has no sync methods (like `Insert`). All the methods are async (like `InsertAsync`). So, if your application has sync repository method usages, convert them to async versions. 

In general, ABP Framework forces you to completely use async everywhere, because mixing async & sync methods is not a recommended approach.

#### Documentation

See the documentation for details on the repositories:

* [ASP.NET Boilerplate - Repository documentation](https://aspnetboilerplate.com/Pages/Documents/Repositories)
* [ABP Framework - Repository documentation](https://docs.abp.io/en/abp/latest/Repositories)

### Domain Services

Your domain service logic mostly remains same on the migration. ABP Framework also defines the base `DomainService` class and the `IDomainService` interface just works like the ASP.NET Boilerplate.

## The Application Layer

Your application service logic remains similar on the migration. ABP Framework also defines the base `ApplicationService` class and the `IApplicationService` interface just works like the ASP.NET Boilerplate, but there are some differences in details.

### Declarative Authorization

ASP.NET Boilerplate has `AbpAuthorize` and `AbpMvcAuthorize` attributes for declarative authorization. Example usage:

````csharp
[AbpAuthorize("MyUserDeletionPermissionName")]
public async Task DeleteUserAsync(...)
{
    ...
}
````

ABP Framework doesn't has such a custom attribute. It uses the standard `Authorize` attribute in all layers.

````csharp
[Authorize("MyUserDeletionPermissionName")]
public async Task DeleteUserAsync(...)
{
    ...
}
````

This is possible with the better integration to the Microsoft Authorization Extensions libraries. See the Authorization section below for more information about the authorization system.

### CrudAppService and AsyncCrudAppService Classes

ASP.NET Boilerplate has `CrudAppService` (with sync service methods) and `AsyncCrudAppService` (with async service methods) classes.

ABP Framework only has the `CrudAppService` which actually has only the async methods (instead of sync methods).

ABP Framework's `CrudAppService` method signatures are slightly different than the old one. For example, old update method signature was ` Task<TEntityDto> UpdateAsync(TUpdateInput input) ` while the new one is ` Task<TGetOutputDto> UpdateAsync(TKey id, TUpdateInput input) `. The main difference is that it gets the Id of the updating entity as a separate parameter instead of including in the input DTO.

### Data Transfer Objects (DTOs)

There are similar base DTO classes (like `EntityDto`) in the ABP Framework too. So, you can find the corresponding DTO base class if you need.

#### Validation

You can continue to use the data annotation attributes to validate your DTOs just like in the ASP.NET Boilerplate.

ABP Framework doesn't include the ` ICustomValidate ` that does exists in the ASP.NET Boilerplate. Instead, you should implement the standard `IValidatableObject` interface for your custom validation logic.

## The Infrastructure Layer

### IAbpSession vs ICurrentUser and ICurrentTenant

ASP.NET Boilerplate's `IAbpSession` service is used to obtain the current user and tenant information, like ` UserId ` and `TenantId`.

ABP Framework doesn't have the same service. Instead, use `ICurrentUser` and `ICurrentTenant` services. These services are defined as base properties in some common classes (like `ApplicationService` and `AbpController`), so you generally don't need to manually inject them. They also have much properties compared to the `IAbpSession`.

### Authorization

ABP Framework extends the [ASP.NET Core Authorization](https://docs.microsoft.com/en-us/aspnet/core/security/authorization/introduction) by adding **permissions** as auto [policies](https://docs.microsoft.com/en-us/aspnet/core/security/authorization/policies) and allowing the authorization system to be usable in the [application services](Application-Services.md) too.

#### AbpAutorize vs Autorize

Use the standard `[Autorize]` and `[AllowAnonymous]` attributes instead of ASP.NET Boilerplate's custom `[AbpAutorize]` and `[AbpAllowAnonymous]` attributes.

#### IPermissionChecker vs IAuthorizationService

Use the standard `IAuthorizationService` to check permissions instead of the ASP.NET Boilerplate's `IPermissionChecker` service. While `IPermissionChecker` also exists in the ABP Framework, it is used to explicitly use the permissions. Using `IAuthorizationService` is the recommended way since it covers other type of policy checks too.

#### AuthorizationProvider vs PermissionDefinitionProvider

You inherit from the `AuthorizationProvider` in the ASP.NET Boilerplate to define your permissions. ABP Framework replaces it by the `PermissionDefinitionProvider` base class. So, define your permissions by inheriting from the `PermissionDefinitionProvider` class.

### Unit of Work

TODO

### Multi-Tenancy

TODO

## Missing Features

The following features are not present for the ABP Framework. Here, a list of major missing features (and the related issue for that feature waiting on the ABP Framework GitHub repository):

* [Multi-Lingual Entities](https://aspnetboilerplate.com/Pages/Documents/Multi-Lingual-Entities) ([#1754](https://github.com/abpframework/abp/issues/1754))

Most of these features will eventually be implemented. However, you can implement them yourself if they are important for you. If you want, you can [contribute](Contribution/Index.md) to the framework by implementing these yourself.