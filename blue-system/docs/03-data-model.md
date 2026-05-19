# 03 — Data Model (first draft)

A sketch in Prisma-ish syntax. Goal: enough to start; not the final word.

```prisma
model User {
  id              String   @id @default(cuid())
  handle          String   @unique
  email           String   @unique
  emailVerifiedAt DateTime?
  joinedAt        DateTime @default(now())
  reputation      Int      @default(0)
  isAuditor       Boolean  @default(false)

  verifierRoles   VerifierRole[]
  guidesAuthored  Guide[]      @relation("Author")
  votes           Vote[]
  disputesOpened  Dispute[]    @relation("Opener")
}

model Subject {
  id        String   @id @default(cuid())
  slug      String   @unique
  name      String
  parentId  String?
  parent    Subject? @relation("SubjectTree", fields: [parentId], references: [id])
  children  Subject[] @relation("SubjectTree")
  guides    Guide[]
  createdAt DateTime @default(now())
}

model VerifierRole {
  id        String  @id @default(cuid())
  userId    String
  subjectId String
  maxLevel  Int
  grantedAt DateTime @default(now())
  user      User    @relation(fields: [userId], references: [id])
  subject   Subject @relation(fields: [subjectId], references: [id])

  @@unique([userId, subjectId])
}

model Guide {
  id            String   @id @default(cuid())
  slug          String
  subjectId     String
  level         Int
  title         String
  body          String   // Markdown
  status        GuideStatus
  authorId      String
  prereqIds     String[] // ids of Level<level guides
  createdAt     DateTime @default(now())
  publishedAt   DateTime?
  upvotes       Int      @default(0)
  downvotes     Int      @default(0)

  subject Subject @relation(fields: [subjectId], references: [id])
  author  User    @relation("Author", fields: [authorId], references: [id])
  methods Method[]
  reviews Review[]

  @@unique([subjectId, slug])
}

enum GuideStatus { DRAFT SUBMITTED IN_REVIEW PUBLISHED RETURNED }

model Method {
  id          String  @id @default(cuid())
  guideId     String
  title       String
  body        String  // Markdown
  isMain      Boolean @default(false)
  usageScore  Int     @default(0)
  guide       Guide   @relation(fields: [guideId], references: [id])
}

model Review {
  id        String   @id @default(cuid())
  guideId   String
  startedAt DateTime @default(now())
  deadline  DateTime
  status    ReviewStatus
  guide     Guide    @relation(fields: [guideId], references: [id])
  votes     Vote[]
}

enum ReviewStatus { OPEN CLOSED_APPROVED CLOSED_REJECTED EXPIRED }

model Vote {
  id            String   @id @default(cuid())
  reviewId      String
  verifierId    String
  decision      VoteDecision
  justification String   // REQUIRED, min 50 chars
  castAt        DateTime @default(now())

  review   Review @relation(fields: [reviewId], references: [id])
  verifier User   @relation(fields: [verifierId], references: [id])

  @@unique([reviewId, verifierId])
}

enum VoteDecision { APPROVE REJECT ABSTAIN }

model Dispute {
  id          String   @id @default(cuid())
  openerId    String
  guideId     String?
  type        DisputeType
  body        String
  status      DisputeStatus @default(OPEN)
  openedAt    DateTime @default(now())
  resolvedAt  DateTime?
  resolution  String?

  opener User @relation("Opener", fields: [openerId], references: [id])
}

enum DisputeType {
  HIERARCHY_PLACEMENT
  CONTENT_ACCURACY
  VERIFIER_CONDUCT
  CROSS_NICHE_CONFLICT
  SUBJECT_TAXONOMY
}

enum DisputeStatus { OPEN UNDER_REVIEW RESOLVED DISMISSED }
```

## Things explicitly not yet modeled
- Vendors / product links / affiliate tracking.
- Exam authoring and grading for verifier applications.
- Edit history / diff storage (probably use an event-sourced approach later).
- Notifications & moderation queue.
