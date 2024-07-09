# Role-based Access Control

> Role-based access control (RBAC) restricts access to all or some features of an application based on a user's role within the application. For example, only users with an `Admin` role should be able to approve or delete other users; only users with a `Dev` role should have access to debug logs.
>
> We call this part of auth: "Authorization". Authentication is verifying the credentials of a user, Authorization is verifying that the user can access certain features.

## Learning Objectives

- Implement bearer authentication and authorization
- Use role-based authorization to limit API access to specific user groups
- Use enums in Prisma to define two user roles: User and Admin

## Setting up

1. <u>**Copy**</u> the `.env.example` file and rename your copy to `.env`
2. Edit the `DATABASE_URL` variable in `.env`, swapping `YOUR_DATABASE_URL` for the URL of your database
3. If you have not previously done so (e.g. for a past exercise), create another separate **TEST** database instance
4. Edit the `TEST_DATABASE_URL` variable in `.env`, swapping `YOUR_TEST_DB_URL` for the URL of the separate **TEST** database instance you just created
5. Run `npm ci` to install the project dependencies.
6. Run `npx prisma migrate reset` to execute the existing migrations & data seed. Press `y` when it asks if you're sure.

## Instructions

Run the app with `npm start`

1. Use an enum to define two roles in the prisma schema: `USER` and `ADMIN`. Add a role property to the User model, defaulting to the USER role.
    - https://www.prisma.io/docs/orm/prisma-schema/data-model/models#defining-enums
2. Create a migration for your schema changes & regenerate the prisma client. Revisit previous exercises if you need a refresher on the commands needed for this.
3. Explore the [test/api/routes/user.spec.js](./test/api/routes/user.spec.js) and [test/api/routes/post.spec.js](./test/api/routes/post.spec.js) files to figure out the next steps. The exercise is considered complete when all of the tests pass. You must not change any of the tests.
    1. Run the tests and try to understand fully what they're asking for
    2. Try to come up with a plan for which tests you want to pass first and how you might do it. This could be noting down what new routes might be needed, changes that need to be made to existing routes etc.
    3. This process is equivalent to building an API to an API Spec - this time you're building to a pre-defined set of tests instead

## Testing your work

- First, make sure you have created / setup the test database instance and env var, as described in the "Setting Up" section.
- Next, run the command `npm run test:migration` - this will run the schema migrations against the test database. **You need to do this after making any schema changes.**
- Run the test suite with `npm test` for core requirements.

## Extensions

**Option 1**
- Implement a new model named `Permission` which connects roles to specific permissions. Example dataset:

| id | role | permission  |
|---|---|---|
| 1 | ADMIN | DELETE_ANY_USER |
| 2 | ADMIN | DELETE_ANY_POST |
| 3 | USER | CREATE_POSTS |
| 4 | USER | DELETE_MY_POST |
| 5 | USER | DELETE_MY_USER |

- You'll then need to check that the user performing an action has access to the relevant permission instead of just checking their role. For example, on `DELETE /posts/3` you'll check that the authenticated user has access to the `DELETE_MY_POST` permission if that post was created by the authenticated user, or the `DELETE_ANY_POST` permission if not.

This is one approach used for fine-tuning user permissions in an app. You could take this one step further by replacing the role enum with a model and adding a join table to remove the possibility of adding duplicate permission names.

**You might need to change the existing tests to satisfy this extension - do so within reason. You should try to create your own new tests as well.**

**Option 2**
- Switch Bearer auth for a different style of auth (e.g. OAuth2, passport.js)

**You might need to change the existing tests to satisfy this extension - do so within reason.**

**Option 3**
- Create a front-end application to consume this API. You are free to use any technology for this - the only requirements are:
    - registration & login forms
    - post creation & post list views
    - a page that only admins can access to view a list of users
    - (optional extra) admins can delete posts and users
    - (optional extra) users can delete their own posts
    - (optional extra) user-friendly error messages on the frontend
