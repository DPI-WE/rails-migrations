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

'rails generate migration AddEmailToUsers email:string'

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

<schema>
You shouldn't manually edit the `schema.rb` file. It is an auto-generated reflection of the database created when a migration is run.
</schema>

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

8. **Execution order is crucial**: Dependencies should be carefully managed to avoid errors, such as attempting to reference a model before its creation. A well-orchestrated order ensures a smooth and error-free transition.

## Exercise: Adding a New Column
Objective: Add a username column to your users table.

1. Generate a migration named `AddUsernameToUsers`.
2. In the migration file, use the `add_column` method to add a `username` column of type string to the users table.
3. Run the migration.

## Quiz

- What is the primary purpose of Rails migrations?
- To manage database schema changes over time.
  - Correct! Migrations help manage and track changes to the database schema in a controlled manner.
- To create new Rails applications.
  - Not quite. Creating new applications is handled by Rails generators, not migrations.
- To handle user authentication.
  - Not quite. User authentication is managed by gems like Devise or custom code, not migrations.
{: .choose_best #migration_purpose title="Purpose of Rails Migrations" points="1" answer="1" }

- What command is used to generate a new migration in Rails?
- rails new MigrationName
  - Not quite. The `rails new` command is used to create a new Rails application.
- rails db:migrate
  - Not quite. The `rails db:migrate` command is used to run pending migrations.
- rails generate migration MigrationName
  - Correct! This command generates a new migration file with a specified name.
{: .choose_best #generate_migration title="Generating a Migration" points="1" answer="3" }

- Which methods are typically included in a Rails migration file?
- `start` and `stop`
  - Not quite. These are not standard methods in Rails migrations.
- `up` and `down`
  - Correct! These methods describe how to apply and revert the migration. We also have the revertable `change` method.
- `create` and `delete`
  - Not quite. These are not the methods used to define migration changes.
{: .choose_best #migration_methods title="Methods in a Migration File" points="1" answer="2" }

- What command do you use to apply migrations to the database?
- rails db:migrate
  - Correct! This command applies all pending migrations to the database.
- rails db:rollback
  - Not quite. This command is used to undo the last migration.
- rails generate migration
  - Not quite. This command is used to generate new migrations.
{: .choose_best #apply_migrations title="Applying Migrations" points="1" answer="1" }

- Why is it important that many migration operations are reversible?
- To improve application performance.
  - Not quite. While important, reversibility is not directly related to performance.
- To easily undo a migration if needed.
  - Correct! Reversibility allows developers to revert changes safely.
- To enhance user authentication.
  - Not quite. Reversibility pertains to database schema changes, not authentication.
{: .choose_best #reversible_operations title="Reversible Operations" points="1" answer="2" }

- Which of the following methods is used to add a column to a table in Rails?
- create_table
  - Not quite. This method is used to create a new table.
- remove_column
  - Not quite. This method is used to remove an existing column.
- add_column
  - Correct! This method adds a new column to an existing table.
{: .choose_best #add_column title="Adding a Column" points="1" answer="3" }

- What is the purpose of the `schema_migrations` table in Rails?
- To keep track of which migrations have been applied.
  - Correct! This table records the versions of applied migrations.
- To store user data.
  - Not quite. User data is stored in user-defined tables, not `schema_migrations`.
- To manage Rails routes.
  - Not quite. Routes are managed in the `routes.rb` file, not the `schema_migrations` table.
{: .choose_best #schema_migrations_table title="Purpose of schema_migrations Table" points="1" answer="1" }

- Which of the following is a best practice when working with Rails migrations?
- Edit migrations that have been merged into the main branch.
  - Not quite. It's best to create a new migration for changes rather than editing existing ones.
- Avoid using version control for `schema.rb`.
  - Not quite. It's important to track `schema.rb` in version control for consistency.
- Make a single structural change per migration.
  - Correct! Keeping migrations simple and focused helps in troubleshooting and rollback.
{: .choose_best #best_practices title="Best Practices for Migrations" points="1" answer="3" }

## Conclusion
Migrations are a fundamental part of Rails' approach to database management, allowing for easy and safe modifications to the database schema. By understanding and utilizing migrations, you can ensure that your application's data model evolves smoothly as your application grows. Understanding the mechanics of Rails migrations and following best practices ensures a smooth development process and helps maintain a clean and manageable database schema.

## Next Steps
Practice creating migrations for adding, removing, and modifying columns in a table. Experiment with rolling back migrations to understand how to safely undo changes.
