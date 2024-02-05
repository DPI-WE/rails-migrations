# Rails Migrations 🕰️

Today, we'll explore a crucial aspect of web development with Ruby on Rails: migrations. As developers, managing a database schema is a common task. Rails migrations provide a powerful and version-controlled way to modify your database schema over time. This lesson will help you understand migrations, their importance, and how to use them effectively.

## Introduction to Migrations
Migrations are Ruby classes that are designed to make it simple to create and modify database tables. Rails uses a Ruby Domain Specific Language (RDSL) to describe changes to your database in a way that is database-agnostic. This means you can write one set of migrations and apply them to different types of databases (MySQL, PostgreSQL, SQLite, etc.) without changing your migration files.

## Why Migrations?
1. **Version Control for Your Database**: Migrations keep track of changes to the database schema, allowing multiple developers to work on the same application and apply changes in a consistent and controlled manner.
2. **Reversible Operations**: Many migration operations are reversible, which means you can easily undo a migration if you need to.
3. **Clarity and Documentation**: Migrations provide a clear history of your database schema changes, which serves as documentation for what changes were made and why.

## Creating a Migration
To create a new migration, you'll use the rails generate migration command followed by the name of your migration. The name should describe the purpose of the migration. For example:

`rails generate migration AddEmailToUsers`

This command will generate a migration file in the `db/migrate` directory. The file will be prefixed with a timestamp to ensure migrations are run in the order they were created.

## Anatomy of a Migration
A migration file contains two main methods: `up` and `down`. The `up` method is used to describe the changes to apply to the database when the migration is run. The `down` method describes how to revert those changes. Alternatively, the `change` method can be used for simple migrations that Rails can automatically reverse.

```ruby
class AddEmailToUsers < ActiveRecord::Migration[6.0]
  def change
    add_column :users, :email, :string
  end
end
```

## Running Migrations
To apply your migration to the database, you'll use the `rails db:migrate` command. Rails will execute the `up` method or `change` method if no `down` method is defined, modifying the database schema accordingly.

```bash
rails db:migrate
```

To roll back a migration, you can use:

```bash
rails db:rollback
```

## Migration Methods
Rails provides a variety of methods to create or modify database tables:

- `create_table`
- `drop_table`
- `add_column`
- `remove_column`
- `rename_column`
- `add_index`

## What Happens When a Migration is Run?
When you run a migration using the `rails db:migrate` command, several things happen behind the scenes:

1. **Rails Checks the `schema_migrations` Table**: Rails maintains a `schema_migrations` table in your database that keeps track of which migrations have already been run. Each migration file has a unique timestamp identifier, and when a migration is successfully run, Rails inserts this timestamp into the `schema_migrations` table.

2. **Migration is Applied to the Database**: Rails reads the migration file and applies the changes described in it to the database. This could involve creating or dropping tables, adding or removing columns, or changing data types, among other schema changes.

3. **`schema.rb` is Updated**: After running migrations, Rails updates the db/schema.rb file. This file acts as the authoritative source for your database schema, allowing new instances of the application to replicate the schema without running all the migrations sequentially.

Example of `schema_migrations` Table
Imagine you have two migrations:

- `20230101120000_create_users.rb`
- `20230102120000_add_email_to_users.rb`

After running these migrations, the `schema_migrations` table will look something like this:

```
| version               |
|-----------------------|
| 20230101120000        |
| 20230102120000        |
```

This table tells Rails that these migrations have already been applied, preventing them from being run again in the future.

## Helpful Heuristics for Working with Migrations
1. **One Change Per Migration**: It's a good practice to make a single structural change per migration. This approach keeps migrations simple, focused, and easy to troubleshoot or reverse if needed.

2. **Use Descriptive Migration Names**: Naming your migrations descriptively helps to document what changes are being made. For example, `AddEmailToUsers` is more informative than `UpdateUsersTable`.

3. **Test Migrations Both Ways**: Ensure your migrations work both when applied and when rolled back. This testing ensures that you can safely undo a migration without leaving the database in an inconsistent state.

4. **Track Your `schema.rb` with Version Control**: This file should be included in your version control system. It's crucial for setting up new instances of the app and keeping track of the current database schema.

5. **Regularly Check for Pending Migrations**: Before deploying your application or after pulling changes from others, run `rails db:migrate:status` to check for any pending migrations. This practice helps to avoid issues related to schema mismatches.

6. **Use `rails db:schema:load` for New Instances**: When setting up a new instance of your application, use `rails db:schema:load` to load the schema directly from `schema.rb`, rather than running all migrations from scratch. This method is faster and ensures the new instance matches the current schema state.

7. **Avoid Editing Migrations That Have Been Merged**: Once a migration has been merged into your main branch and possibly run in production, avoid editing it. If you need to make changes, create a new migration.


## Exercise: Adding a New Column
Objective: Add a username column to your users table.

1. Generate a migration named `AddUsernameToUsers`.
2. In the migration file, use the `add_column` method to add a `username` column of type string to the users table.
3. Run the migration.

## Conclusion
Migrations are a fundamental part of Rails' approach to database management, allowing for easy and safe modifications to the database schema. By understanding and utilizing migrations, you can ensure that your application's data model evolves smoothly as your application grows. Understanding the mechanics of Rails migrations and following best practices ensures a smooth development process and helps maintain a clean and manageable database schema.

## Next Steps
Practice creating migrations for adding, removing, and modifying columns in a table. Experiment with rolling back migrations to understand how to safely undo changes.
