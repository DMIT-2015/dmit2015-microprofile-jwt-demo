# DMIT2015 Secure RBAC REST API Demo

## Purpose

This instructor demonstration project shows how to secure Jakarta REST endpoints using **MicroProfile JWT** and **role-based access control (RBAC)**.

The project uses `Student` REST endpoints to demonstrate how different operations can be restricted to different application roles. Student records are **not** owned by individual users in this example.

## Main Learning Objectives

After reviewing this project, you should be able to explain how to:

- Require JWT authentication for REST API requests.
- Read application roles from the JWT `groups` claim.
- Use `@RolesAllowed` to restrict individual endpoints.
- Distinguish authentication from authorization.
- Test permitted and denied requests with valid JWTs.
- Interpret common security responses such as `401 Unauthorized` and `403 Forbidden`.

## Demo Roles

- `Sales`
- `Shipping`
- `Administration`

These are example business roles. Role names and permission rules must be chosen for each application's requirements.

## Role Permissions

Access is determined by the authenticated user's role, not by ownership of a `Student` record.

### Sales

Can retrieve all Students, retrieve a Student by ID, and create a Student. Cannot update or delete Students.

### Shipping

Can retrieve all Students, retrieve a Student by ID, and update a Student. Cannot create or delete Students.

### Administration

Can retrieve all Students, retrieve a Student by ID, create, update, and delete Students.

### Endpoint Permission Summary

| Operation | Sales | Shipping | Administration |
|---|:---:|:---:|:---:|
| Retrieve all Students | Allowed | Allowed | Allowed |
| Retrieve Student by ID | Allowed | Allowed | Allowed |
| Create Student | Allowed | Denied | Allowed |
| Update Student | Denied | Allowed | Allowed |
| Delete Student | Denied | Denied | Allowed |

A denied operation with a **valid JWT** but insufficient role should return `403 Forbidden`.

## Conceptual Model

```text
Request to secured endpoint
  |
  v
Is the JWT present and valid?
  |
  +-- No --> Access denied (status depends on failure and server configuration)
  |
  +-- Yes
       |
       v
    Does the user have a permitted role?
       |
       +-- No --> 403 Forbidden
       |
       +-- Yes --> Run the REST endpoint
```

With the WildFly configuration used in this course, an anonymous request to an `@RolesAllowed` endpoint may return `403 Forbidden`. An invalid or expired token normally results in `401 Unauthorized`.

## Running the Project

Start the configured OpenID Connect identity provider, then from this project folder run:

```bash
mvn wildfly:dev
```

Default API port:

```text
http://localhost:8090
```

The full REST endpoint URL also depends on the application's deployment context path. Consult the source code and runtime configuration. Press `Ctrl+C` to stop WildFly.

## What to Observe During the Demo

- Where `@RolesAllowed` is applied to `Student` operations.
- How a JWT's `groups` claim maps to application roles.
- How permitted requests succeed and wrong-role requests fail.
- Why denied-role tests should use a **valid JWT** with the wrong role rather than an anonymous request.
- Why authorization belongs in the REST API even when a client hides unavailable actions.

## Practice

1. Obtain valid access tokens for test users with different roles.
2. Call the same `Student` endpoint using each token.
3. Compare permitted and denied `POST`, `PUT`, and `DELETE` results against the table.
4. Try an expired or otherwise unusable token and compare its response with a wrong-role request.

Apply the *pattern* to other APIs by defining their own roles and endpoint permissions, rather than copying this permission table unchanged.
