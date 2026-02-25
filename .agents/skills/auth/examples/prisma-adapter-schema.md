# Prisma Adapter Schema

When using Auth.js with the `@auth/prisma-adapter`, you must append these core models to your `prisma/schema.prisma` file.

These models handle users, accounts (OAuth identities like Google/GitHub), sessions (if using DB strategy), and verification tokens (for magic links).

## `prisma/schema.prisma`

```prisma
// Append these to your existing schema:

enum UserRole {
  USER
  ADMIN
}

model User {
  id            String    @id @default(cuid())
  name          String?
  email         String?   @unique
  emailVerified DateTime?
  image         String?
  password      String?   // Included if using Credentials provider
  role          UserRole  @default(USER)
  accounts      Account[]
  sessions      Session[]
  
  createdAt     DateTime  @default(now())
  updatedAt     DateTime  @updatedAt
}

model Account {
  id                String  @id @default(cuid())
  userId            String
  type              String
  provider          String
  providerAccountId String
  refresh_token     String? @db.Text
  access_token      String? @db.Text
  expires_at        Int?
  token_type        String?
  scope             String?
  id_token          String? @db.Text
  session_state     String?

  user User @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@unique([provider, providerAccountId])
  @@index([userId])
}

model Session {
  id           String   @id @default(cuid())
  sessionToken String   @unique
  userId       String
  expires      DateTime
  user         User     @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@index([userId])
}

model VerificationToken {
  identifier String
  token      String   @unique
  expires    DateTime

  @@unique([identifier, token])
}
```

After modifying the schema, always run:
```bash
npx prisma db push
npx prisma generate
```
