# Git Module Architecture Diagrams

This document provides visual representations of the Git module's architecture, data flows, and component interactions.

## 1. Component Hierarchy

```
┌─────────────────────────────────────────────────────────────────┐
│                         Git Service                             │
│                      (Interface Implementation)                 │
│                                                                 │
│  • Repository Management (CRUD)                                │
│  • Branch Operations                                           │
│  • Commit Operations                                           │
│  • Diff & Merge                                               │
│  • File Operations                                            │
│  • Tags & References                                          │
│  • Hooks Integration                                          │
└────────────┬────────────────────────────────┬─────────────────┘
             │                                │
             │                                │
    ┌────────▼─────────┐            ┌────────▼──────────┐
    │   Git API Layer  │            │  Hook System      │
    │   (api.Git)      │            │  (hook.Client)    │
    │                  │            │                   │
    │  • Command Exec  │            │  • PreReceive     │
    │  • Caching       │            │  • PostReceive    │
    │  • Optimization  │            │  • Update         │
    └────────┬─────────┘            └───────────────────┘
             │
             │
    ┌────────▼─────────┐
    │  Command Builder │
    │  (git/command)   │
    │                  │
    │  • Safe Args     │
    │  • Validation    │
    │  • Execution     │
    └────────┬─────────┘
             │
             │
    ┌────────▼─────────┐
    │   Git Binary     │
    │   (exec.Command) │
    └──────────────────┘
```

## 2. Service Layer Structure

```
Service
├── reposRoot: string ──────────► /data/repos/
│                                 ├── {uid1}/
│                                 ├── {uid2}/
│                                 └── {uid3}/
│
├── sharedRepoRoot: string ─────► /data/shared_temp/
│                                 └── (temporary clones)
│
├── reposGraveyard: string ─────► /data/cleanup/
│                                 └── (deleted repos)
│
├── git: *api.Git ──────────────► Git API Adapter
│
├── hookClientFactory ──────────► Hook Client Factory
│
├── store: storage.Store ───────► Storage Backend
│
└── gitHookPath: string ────────► /path/to/hook/binary
```

## 3. Request Flow - Create Repository

```
Client Request
      │
      ▼
┌─────────────────┐
│ Service Layer   │  1. Validate parameters
│ CreateRepository│  2. Generate unique UID
└────────┬────────┘  3. Create directory structure
         │
         ▼
┌─────────────────┐
│   API Layer     │  4. Initialize bare repo (git init --bare)
│   InitRepository│  5. Set default branch
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Command Layer  │  6. Execute: git init --bare
│  git init       │  7. Execute: git symbolic-ref HEAD
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   Git Process   │  8. Create .git structures
└────────┬────────┘  9. Initialize refs, objects, config
         │
         ▼
┌─────────────────┐
│ Service Layer   │  10. Clone to temp directory
│ (post-init)     │  11. Add initial files (if any)
└────────┬────────┘  12. Commit and push
         │          13. Install hook symlinks
         ▼
┌─────────────────┐
│    Response     │  Return: {UID: "abc...xyz"}
└─────────────────┘
```

## 4. Request Flow - Commit Files

```
Client Request (CommitFilesParams)
      │
      │  Actions: [CREATE, UPDATE, DELETE, MOVE]
      │  Branch: "main"
      │  Message: "Update files"
      │
      ▼
┌─────────────────────────┐
│  Service.CommitFiles    │
│  1. Validate params     │
│  2. Setup identities    │
└────────┬────────────────┘
         │
         ▼
┌─────────────────────────┐
│  Shared Repo Manager    │
│  3. Get temp repo       │
│  4. Clone from origin   │
└────────┬────────────────┘
         │
         ▼
┌─────────────────────────┐
│  File Operations        │
│  For each action:       │
│    CREATE  → write file │
│    UPDATE  → write file │
│    DELETE  → rm file    │
│    MOVE    → mv file    │
│    PATCH   → apply diff │
└────────┬────────────────┘
         │
         ▼
┌─────────────────────────┐
│  Git Operations         │
│  5. git add <files>     │
│  6. git commit          │
│     --author            │
│     --date              │
└────────┬────────────────┘
         │
         ▼
┌─────────────────────────┐
│  Push & Hooks           │
│  7. git push origin     │
│  8. Trigger hooks       │
│     • pre-receive       │
│     • post-receive      │
└────────┬────────────────┘
         │
         ▼
┌─────────────────────────┐
│  Response               │
│  {                      │
│    CommitID: sha,       │
│    ChangedFiles: [...]  │
│  }                      │
└─────────────────────────┘
```

## 5. Hook Execution Flow

```
Git Push
    │
    ▼
┌─────────────────────┐
│  Git Server         │
│  (receives push)    │
└─────────┬───────────┘
          │
          │  Call hook via symlink
          ▼
┌─────────────────────┐
│  pre-receive hook   │◄─── symlink to gitHookPath
│  (binary)           │
└─────────┬───────────┘
          │
          │  Create hook.Client from env vars
          ▼
┌─────────────────────┐
│  Hook Client        │
│  PreReceive()       │
│                     │
│  • Validate refs    │
│  • Check policies   │
│  • Scan secrets     │
│  • Size checks      │
└─────────┬───────────┘
          │
          │  Return: Allow/Deny
          ▼
┌─────────────────────┐
│  Git Server         │
│  (accept/reject)    │
└─────────┬───────────┘
          │
          │  If accepted
          ▼
┌─────────────────────┐
│  Update refs        │
│  (fast-forward or   │
│   merge)            │
└─────────┬───────────┘
          │
          ▼
┌─────────────────────┐
│  post-receive hook  │◄─── symlink to gitHookPath
│  (binary)           │
└─────────┬───────────┘
          │
          │  Create hook.Client from env vars
          ▼
┌─────────────────────┐
│  Hook Client        │
│  PostReceive()      │
│                     │
│  • Trigger events   │
│  • Update cache     │
│  • Send webhooks    │
│  • Index changes    │
└─────────┬───────────┘
          │
          ▼
     Complete
```

## 6. Command Building Process

```
Service Layer Request
      │
      ▼
┌─────────────────────────────┐
│  command.New("commit")      │
│  Create base command        │
└──────────┬──────────────────┘
           │
           ▼
┌─────────────────────────────┐
│  Add Options (via Builder)  │
│  .Add(                      │
│    WithFlag("-m"),          │
│    WithArg("message"),      │
│    WithPostSepArg("file")   │
│  )                          │
└──────────┬──────────────────┘
           │
           ▼
┌─────────────────────────────┐
│  Validation Phase           │
│  • Command name allowed?    │
│  • Action regex valid?      │
│  • Flags safe?              │
│  • Args properly escaped?   │
└──────────┬──────────────────┘
           │
           ▼
┌─────────────────────────────┐
│  Build Argument List        │
│  [global...] [name]         │
│  [action] [flags...]        │
│  [args...] [--]             │
│  [postsepargs...]           │
└──────────┬──────────────────┘
           │
           ▼
┌─────────────────────────────┐
│  Execute with Context       │
│  exec.CommandContext(       │
│    ctx,                     │
│    "git",                   │
│    args...                  │
│  )                          │
└──────────┬──────────────────┘
           │
           ▼
┌─────────────────────────────┐
│  Handle Result              │
│  • Capture stdout/stderr    │
│  • Check exit code          │
│  • Parse output             │
│  • Wrap errors              │
└──────────┬──────────────────┘
           │
           ▼
      Return Result
```

## 7. Caching Strategy

```
Request: GetLastCommit(repo, path)
    │
    ▼
┌────────────────────────┐
│  Check Cache           │
│  key = (repo, path)    │
└────┬────────────┬──────┘
     │            │
   Hit│            │Miss
     │            │
     ▼            ▼
┌─────────┐  ┌─────────────────┐
│ Return  │  │ Execute Git Cmd │
│ Cached  │  │ git log -1      │
│ Commit  │  └────────┬────────┘
└─────────┘           │
                      ▼
                 ┌─────────────────┐
                 │ Parse Result    │
                 │ Create Commit   │
                 └────────┬────────┘
                          │
                          ▼
                 ┌─────────────────┐
                 │ Store in Cache  │
                 │ TTL = config    │
                 └────────┬────────┘
                          │
                          ▼
                     Return Commit

Cache Invalidation:
├── Time-based (TTL expired)
├── Event-based (new commit on path)
└── Explicit (cache.Delete)
```

## 8. Storage Abstraction

```
Service Layer
      │
      │  Large file / Artifact
      ▼
┌─────────────────────┐
│ storage.Store       │◄──── Interface
└─────────┬───────────┘
          │
          │  Implementations:
          │
    ┌─────┴─────┬──────────┬──────────┐
    │           │          │          │
    ▼           ▼          ▼          ▼
┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐
│ Local  │ │   S3   │ │  GCS   │ │ Azure  │
│  FS    │ │ Bucket │ │ Bucket │ │  Blob  │
└────────┘ └────────┘ └────────┘ └────────┘

Flow:
1. Service calls store.Save(path, reader)
2. Store implementation handles:
   • Destination selection
   • Upload/copy
   • Verification
   • Cleanup
3. Returns: stored path or URL
```

## 9. Diff Streaming Architecture

```
Request: Diff(baseRef, targetRef)
    │
    ▼
┌─────────────────────────┐
│  Service.Diff()         │
│  Returns: (chan, chan)  │
└────────┬────────────────┘
         │
         ├──► fileDiffChan <-chan *FileDiff
         └──► errChan <-chan error
         │
         │  Spawns goroutine
         ▼
┌─────────────────────────┐
│  Git Command            │
│  git diff --raw         │
│  Streaming output       │
└────────┬────────────────┘
         │
         │  Line by line
         ▼
┌─────────────────────────┐
│  Parser                 │
│  • Parse diff header    │
│  • Parse hunks          │
│  • Identify files       │
└────────┬────────────────┘
         │
         │  Send to channel
         ▼
┌─────────────────────────┐
│  fileDiffChan <- diff   │
└────────┬────────────────┘
         │
         │  Consumer processes
         ▼
┌─────────────────────────┐
│  Client                 │
│  for diff := range      │
│      fileDiffChan {     │
│    // Process diff      │
│  }                      │
└─────────────────────────┘

Benefits:
• Memory efficient (no buffering entire diff)
• Concurrent processing (consumer can process while producing)
• Cancellable (context cancellation stops production)
```

## 10. Wire Dependency Injection

```
Application Startup
        │
        ▼
┌──────────────────────┐
│  Wire Configuration  │
│  wire.Build(...)     │
└─────────┬────────────┘
          │
          │  Compile-time DI
          ▼
┌──────────────────────┐
│  ProvideGITAdapter   │
│                      │
│  Input:              │
│  • config            │
│  • lastCommitCache   │
│  • githookFactory    │
│                      │
│  Creates: *api.Git   │
└─────────┬────────────┘
          │
          ▼
┌──────────────────────┐
│  ProvideService      │
│                      │
│  Input:              │
│  • config            │
│  • adapter           │◄─── from ProvideGITAdapter
│  • hookClientFactory │
│  • storage           │
│                      │
│  Creates: Interface  │
└─────────┬────────────┘
          │
          ▼
┌──────────────────────┐
│  Application         │
│  Receives fully      │
│  configured Service  │
└──────────────────────┘

Dependency Graph:
    Config
      │
      ├─────────────┐
      │             │
      ▼             ▼
LastCommitCache  HookFactory
      │             │
      └──────┬──────┘
             │
             ▼
          api.Git
             │
             ├─────────┐
             │         │
             ▼         ▼
          Service   Storage
             │
             ▼
       Application
```

## 11. Error Flow

```
Git Command Execution
         │
         ▼
┌────────────────────┐
│  exec.Command      │
│  Exit Code != 0    │
└────────┬───────────┘
         │
         ▼
┌────────────────────┐
│  Command Layer     │
│  NewError(         │
│    err,            │
│    stderr          │
│  )                 │
└────────┬───────────┘
         │
         ▼
┌────────────────────┐
│  API Layer         │
│  Interpret error   │
│  • Not found       │
│  • Conflict        │
│  • Invalid         │
└────────┬───────────┘
         │
         ▼
┌────────────────────┐
│  Service Layer     │
│  Wrap with context │
│  errors.NotFound() │
│  errors.Conflict() │
│  errors.Internal() │
└────────┬───────────┘
         │
         ▼
┌────────────────────┐
│  Client            │
│  Handle error      │
│  • Log             │
│  • Retry           │
│  • User message    │
└────────────────────┘

Error Types:
├── NotFound: Resource doesn't exist
├── Conflict: Concurrent modification
├── InvalidArgument: Validation failure
└── Internal: Unexpected error
```

## 12. Repository Lifecycle State Machine

```
                   [CreateRepository]
                           │
                           ▼
┌─────────────────────────────────────┐
│           CREATING                   │
│  • Generate UID                      │
│  • Init bare repo                    │
│  • Set default branch                │
└──────────┬───────────────────────────┘
           │
           │  Success
           ▼
┌─────────────────────────────────────┐
│           ACTIVE                     │◄────┐
│  • Accept pushes                     │     │
│  • Serve clones                      │     │
│  • Execute hooks                     │     │
└──────────┬───────────┬───────────────┘     │
           │           │                      │
    [Delete]│           │[Sync]               │
           │           └──────────────────────┘
           ▼
┌─────────────────────────────────────┐
│         MOVING_TO_GRAVEYARD          │
│  • Rename to graveyard               │
│  • Atomic operation                  │
└──────────┬───────────────────────────┘
           │
           ▼
┌─────────────────────────────────────┐
│          IN_GRAVEYARD                │
│  • No longer accessible              │
│  • Pending cleanup                   │
└──────────┬───────────────────────────┘
           │
           │  [Background Cleanup]
           ▼
┌─────────────────────────────────────┐
│           DELETED                    │
│  • Filesystem entry removed          │
└─────────────────────────────────────┘

Error Recovery:
• CREATING → Error → Cleanup → DELETED
• MOVING_TO_GRAVEYARD → Error → IN_GRAVEYARD (best effort)
```

## 13. Branch Operation Flows

```
Create Branch
    │
    ▼
┌────────────────┐
│  Validate      │
│  • Name        │
│  • Source      │
└────┬───────────┘
     │
     ▼
┌────────────────┐
│  Get Source    │
│  Commit SHA    │
└────┬───────────┘
     │
     ▼
┌────────────────┐
│  Update Ref    │
│  refs/heads/   │
│  {branch}      │
└────┬───────────┘
     │
     ▼
   Success


Delete Branch
    │
    ▼
┌────────────────┐
│  Check         │
│  Not default   │
└────┬───────────┘
     │
     ▼
┌────────────────┐
│  Delete Ref    │
│  refs/heads/   │
│  {branch}      │
└────┬───────────┘
     │
     ▼
   Success


Update Default
    │
    ▼
┌────────────────┐
│  Validate      │
│  Branch exists │
└────┬───────────┘
     │
     ▼
┌────────────────┐
│  Update HEAD   │
│  symbolic-ref  │
└────┬───────────┘
     │
     ▼
   Success
```

## Summary

These diagrams illustrate:

1. **Layered Architecture**: Clear separation of concerns across Service, API, Command, and Git binary layers
2. **Data Flow**: How requests flow through the system and are transformed at each layer
3. **Hook Integration**: How Git hooks integrate with the service for policy enforcement
4. **Caching Strategy**: Performance optimization through intelligent caching
5. **Streaming**: Memory-efficient handling of large data sets
6. **Error Handling**: Comprehensive error propagation and wrapping
7. **Dependency Injection**: Compile-time DI for clean dependencies
8. **State Management**: Repository lifecycle and state transitions
9. **Security**: Multiple layers of validation and safe command construction

The architecture demonstrates a well-designed system that balances performance, security, and maintainability.
