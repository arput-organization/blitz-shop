# Migration `20200824205522`

This migration has been generated by Alan at 8/24/2020, 10:55:22 PM.
You can check out the [state of the schema](./schema.prisma) after the migration.

## Database Steps

```sql
PRAGMA foreign_keys=OFF;

CREATE TABLE "new_User" (
"id" INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
"createdAt" DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
"updatedAt" DATETIME NOT NULL,
"name" TEXT ,
"email" TEXT NOT NULL,
"hashedPassword" TEXT ,
"role" TEXT NOT NULL DEFAULT 'user',
"bio" TEXT NOT NULL DEFAULT '')

INSERT INTO "new_User" ("id", "createdAt", "updatedAt", "role", "name", "email", "hashedPassword") SELECT "id", "createdAt", "updatedAt", "role", "name", "email", "hashedPassword" FROM "User"

PRAGMA foreign_keys=off;
DROP TABLE "User";;
PRAGMA foreign_keys=on

ALTER TABLE "new_User" RENAME TO "User";

CREATE UNIQUE INDEX "User.email_unique" ON "User"("email")

CREATE TABLE "new_Post" (
"id" INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
"createdAt" DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
"updatedAt" DATETIME NOT NULL,
"title" TEXT NOT NULL,
"intro" TEXT NOT NULL,
"content" TEXT NOT NULL,
"userId" INTEGER ,
"status" TEXT NOT NULL DEFAULT 'draft',
FOREIGN KEY ("userId") REFERENCES "User"("id") ON DELETE SET NULL ON UPDATE CASCADE
)

INSERT INTO "new_Post" ("id", "createdAt", "updatedAt", "title", "intro", "content", "userId") SELECT "id", "createdAt", "updatedAt", "title", "intro", "content", "userId" FROM "Post"

PRAGMA foreign_keys=off;
DROP TABLE "Post";;
PRAGMA foreign_keys=on

ALTER TABLE "new_Post" RENAME TO "Post";

PRAGMA foreign_key_check;

PRAGMA foreign_keys=ON;
```

## Changes

```diff
diff --git schema.prisma schema.prisma
migration 20200818190435..20200824205522
--- datamodel.dml
+++ datamodel.dml
@@ -2,21 +2,21 @@
 // learn more about it in the docs: https://pris.ly/d/prisma-schema
 datasource sqlite {
   provider = "sqlite"
-  url = "***"
+  url = "***"
 }
 // SQLite is easy to start with, but if you use Postgres in production
 // you should also use it in development with the following:
 //datasource postgresql {
 //  provider = "postgresql"
-//  url = "***"
+//  url = "***"
 //}
 generator client {
   provider        = "prisma-client-js"
-  previewFeatures = ["transactionApi"]
+  previewFeatures = ["transactionApi", "connectOrCreate"]
 }
 // --------------------------------------
@@ -33,8 +33,9 @@
   followedBy     User[]    @relation("UserFollows", references: [id])
   following      User[]    @relation("UserFollows", references: [id])
   favorites      Post[]    @relation("Favorites", references: [id])
   comments       Comment[]
+  bio            String    @default("")
 }
 model Session {
   id                 Int       @default(autoincrement()) @id
@@ -61,8 +62,9 @@
   User        User?     @relation(fields: [userId], references: [id])
   userId      Int?
   favoritedBy User[]    @relation("Favorites", references: [id])
   comments    Comment[]
+  status      String    @default("draft")
 }
 model Comment {
   id        Int      @default(autoincrement()) @id
```

