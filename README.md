# projeto-nlw-java

REST API for event registration with a referral ranking, built with Java
and Spring Boot and connected to a MySQL database.

> **Note:** This is a guided learning project, built while following
> Rocketseat's Next Level Week (NLW). I use it to practice building a
> layered Java backend, connecting it to a relational database, running
> MySQL with Docker and testing an API with Postman.

## What it does

- Create and list events
- Register a user for an event, optionally through the referral of
  another user
- Generate a ranking of the users who referred the most people to an event

## Tech

- Java 21
- Spring Boot 3.4.3 (Spring Web, Spring Data JPA)
- MySQL 8.4 (Docker Compose)
- Maven (wrapper included)
- Postman, to test the endpoints

## Project structure

- `events/` - Spring Boot application
  - `controller/` - REST endpoints
  - `service/` - business rules
  - `repo/` - data access (Spring Data JPA)
  - `model/` - entities: `Event`, `User`, `Subscription`
  - `dto/` - response objects
  - `exception/` - custom exceptions
- `db/db_events.mwb` - MySQL Workbench model
- `db_events.sql` - SQL script that creates the schema
- `docker-compose.yml` - MySQL container

## Database

Schema `db_events`, created from `db_events.sql`:

| Table | Purpose |
|-------|---------|
| `tbl_event` | Events: title, pretty name (unique), location, price, dates and times |
| `tbl_user` | Users: name and email (unique) |
| `tbl_subscription` | Registrations: subscribed user, referring user (optional) and event |

## How to run

1. Start MySQL from the repository root:
```
   docker compose up -d
```
   The database is available at `localhost:3336` (user `root`). The
   credentials are in `docker-compose.yml` and
   `events/src/main/resources/application.properties`. They are
   development-only credentials for local use.
2. Run `db_events.sql` against that database [confirm how you ran it,
   for example with MySQL Workbench].
3. Start the application (requires JDK 21):
```
   cd events
   ./mvnw spring-boot:run
```
   On Windows: `mvnw.cmd spring-boot:run`. The API runs on
   `http://localhost:8080`.

## Endpoints

| Method | Route | Description |
|--------|-------|-------------|
| POST | `/events` | Create an event. The pretty name is generated from the title |
| GET | `/events` | List all events |
| GET | `/events/{prettyName}` | Get an event by its pretty name |
| POST | `/subscription/{prettyName}` | Register a user for an event |
| POST | `/subscription/{prettyName}/{userId}` | Register a user through the referral of `userId` |
| GET | `/subscription/{prettyName}/ranking` | Top referrers of an event |
| GET | `/subscription/{prettyName}/ranking/{userId}` | Position of a user in the ranking |

Example body for the registration endpoints:

```json
{ "name": "Jane Doe", "email": "jane@example.com" }
```

The registration response contains the subscription number and a
referral link (`designation`). The domain in the link is a placeholder
from the course.

## Known issues

Found while reviewing the code. Not yet verified with tests or fixed:

- **Registering a new user:** in `SubscriptionService.createNewSubscription`,
  the user saved to the database is stored in a different variable from
  the one later set as the subscriber. The registration of a user whose
  email is not yet in the database may fail.
- **Ranking with few referrers:** `GET /subscription/{prettyName}/ranking`
  returns `subList(0, 3)`, which throws an error if the event has fewer
  than 3 referrers.
- **No automated tests:** the only test is the default `contextLoads`.

## Next steps

- Reproduce the issues above with Postman
- Fix them and add tests for the registration and ranking logic

## What I practiced

- Layered architecture: controller, service and repository
- Mapping entities and relationships with JPA (`@ManyToOne`)
- A native SQL query to build the referral ranking
- DTOs as Java records and custom exceptions mapped to HTTP 404 and 409
- Running MySQL in a Docker container
- Testing HTTP requests with Postman

## Author

Gabriel Henrique Carvalho Gama ([GitHub](https://github.com/gabriel-hcg))
