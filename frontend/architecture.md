# Hawkeye.ML Solution Architecture

## Solution Overview

`Hawkeye.ML.WebApi.sln` contains 6 projects:

| Project | Type | Role |
|---|---|---|
| `Hawkeye.ML.Angular.client` | Angular Frontend | UI — all pages the user sees |
| `Hawkeye.ML.Server.WebAPI` | ASP.NET Core Backend | REST API, controllers, authentication |
| `Hawkeye.ML.Infrastructure` | .NET Standard Library | Shared ML model types, enums, utilities |
| `Hawkeye.ML.Infrastructure.UnitTests` | Test Project | Tests for Infrastructure |
| `Hawkeye.ML.Server.WebAPI.UnitTests` | Test Project | Tests for WebAPI |
| `CSVToCocoJSON` | Utility Tool | Converts CSV label data to COCO JSON format |

---

## The Big Picture

```
BROWSER
   │
   ▼
Hawkeye.ML.Angular  (Frontend - what users see)
   │  src/app/
   │  ├── pages: home, datasets, images, label-classes, ml-models, admin, account
   │  ├── services/api.service.ts  ← all HTTP calls live here
   │  ├── identity/               ← login, register, auth guard
   │  └── proxy.conf.js           ← forwards /api/* to port 7289 (dev only)
   │
   │  HTTP calls (GET/POST/PUT/DELETE)
   ▼
Hawkeye.ML.Server.WebAPI  (Backend API - port 7289 dev / hawkeyemanager.com prod)
   │  Controllers/
   │  ├── LabelsController        → /api/labels
   │  ├── ImageryController       → /api/imagery
   │  ├── DatasetController       → /api/dataset
   │  ├── MLModelController       → /api/mlmodel
   │  ├── ImageLabelController    → /api/imagelabel
   │  ├── LabelGroupsController   → /api/labelgroups
   │  └── AccountController       → /api/account
   │  DTO/Dtos.cs                 ← shapes of data sent to/from Angular
   │  Models/ApplicationDbContext ← EF Core, defines all tables
   │
   │  EF Core (generates SQL automatically)
   ▼
SQL Database
   ├── Images           ← photos/imagery
   ├── Labels           ← label classes
   ├── LabelGroups      ← groups for labels
   ├── ImageLabels      ← bounding boxes / annotations on images
   ├── DataSets         ← collections of images for training
   ├── DataSetImages    ← which images belong to which dataset
   ├── DataSetLabels    ← which labels belong to which dataset
   ├── DataSetUsers     ← which users are assigned to which dataset
   ├── MLModels         ← trained ML model records
   ├── Tags / ImageTags ← image tagging
   ├── SystemSettings   ← app configuration
   └── AspNetUsers/Roles ← login accounts (ASP.NET Identity)
```

---

## Frontend: Hawkeye.ML.Angular.client

### Pages & Routes

| Route | Component | Access |
|---|---|---|
| `/home` | HomeComponent | User |
| `/labelclasses` | LabelClassesComponent | User |
| `/datasets` | DatasetsComponent | User |
| `/images` | ImagesComponent | User |
| `/mlmodels` | MlModelsComponent | User |
| `/admin` | AdminComponent | Admin |
| `/account` | AccountComponent | User |
| `/help` | HelpPageComponent | User |
| `/signin` | SignInComponent | Public |
| `/forbidden` | ForbiddenComponent | Public |

### Key Folders

```
src/app/
├── services/api.service.ts     ← ALL backend calls go through here
├── identity/                   ← signin, register, auth guard, interceptor
├── datasets/                   ← dataset list, create, edit, upload images
├── images/                     ← image gallery & annotation viewer
├── label-classes/              ← label class management table
├── new-label-class/            ← create/edit label dialog
├── groups/                     ← label group management dialogs
├── ml-models/                  ← ML model list & upload
├── admin/                      ← user management
├── account/                    ← user profile & password
├── modules/                    ← shared utilities (drag-drop, confirm dialog)
└── pipes/                      ← date-format pipe
```

### API Service Key Methods (`services/api.service.ts`)

| Method | HTTP | Endpoint |
|---|---|---|
| `getLabelData()` | GET | `/api/labels` |
| `postLabelData()` | POST | `/api/labels` |
| `putLabelData()` | PUT | `/api/labels/{id}` |
| `getLabelCount()` | GET | `/api/labels/count` |
| `getGroups()` | GET | `/api/labelgroups` |
| `getImageryData()` | GET | `/api/imagery` |
| `getAnnotations()` | GET | `/api/imagelabel/{imageId}` |
| `getDataSet()` | GET | `/api/dataset` |
| `postDataSet()` | POST | `/api/dataset` |
| `putDataSet()` | PUT | `/api/dataset/{id}` |
| `getMLModels()` | GET | `/api/mlmodel` |
| `getUserInfo()` | GET | `/api/account/userinfo` |
| `getUsers()` | GET | `/api/account/users` |

### How to Find the Backend from a Frontend Call

1. Find the URL in `api.service.ts` e.g. `http.put("/api/labels/...")`
2. Take the segment after `/api/` → `labels`
3. Capitalise → look for `LabelsController.cs` in the backend
4. Find `[HttpPut]` attribute for the matching action

---

## Backend: Hawkeye.ML.Server.WebAPI

### Controllers

#### LabelsController `/api/labels`
- `GET /api/labels?startRecord=&recordCount=` — paginated label list
- `GET /api/labels/count` — total label count
- `GET /api/labels/labelTitles` — all label titles
- `POST /api/labels` — create new label
- `PUT /api/labels/{id}` — update label (title, code, group)
- `POST /api/labels/uploadimage/{id}` — upload label icon image

#### LabelGroupsController `/api/labelgroups`
- CRUD for label groups

#### ImageryController `/api/imagery`
- `GET /api/imagery?startRecord=&recordCount=` — paginated images
- `GET /api/imagery/count` — total image count

#### ImageLabelController `/api/imagelabel`
- `GET /api/imagelabel/{imageId}` — get annotations for an image
- `PUT /api/imagelabel/{imageId}` — save annotations (bounding boxes/polygons)
- `GET /api/imagelabel/countPerLabel/{label}` — how many images use a label
- `GET /api/imagelabel/annotatedCount` — total annotated image count

#### DatasetController `/api/dataset`
- Full CRUD for datasets
- `POST /api/dataset/{id}/assignUsers` — assign users to a dataset

#### MLModelController `/api/mlmodel`
- Full CRUD for ML models

#### AccountController `/api/account`
- `GET /api/account/userinfo` — current user info
- `GET /api/account/users` — all users (admin)
- `POST /api/account/updateUser` — update user roles/email
- `PUT /api/account/updatePassword/{id}` — change password

### Authentication & Middleware (Program.cs)
- **Auth schemes:** Bearer token + Cookie (SameSite=None, 1-hour expiry)
- **Identity endpoints:** `/register`, `/login`, `/refresh`, `/logout`
- **CORS:** allowed for `https://ml.hawkeyemanager.com`
- **File size limits:** 200MB max for image uploads (AWS S3 storage)
- **Swagger:** available at `/swagger` (requires login)

### DTOs (`DTO/Dtos.cs`)
Data shapes sent between Angular and the backend:

| DTO | Used for |
|---|---|
| `LabelDto` | Label data |
| `LabelGroupDto` | Label group data |
| `ImageDto` | Image metadata |
| `ImageLabelDto` | Annotation data (polygon, bounding box) |
| `DataSetDto` | Dataset with assigned users & image count |
| `MLModelDto` | ML model metadata |
| `TagDto` / `ImageTagDto` | Image tagging |

---

## Database: ApplicationDbContext

**Location:** `Hawkeye.ML.Server/Models/ApplicationDbContext.cs`

### Tables (DbSets)

| Table | Model | Purpose |
|---|---|---|
| `Images` | Image.cs | Photo metadata, file path |
| `Labels` | Label.cs | Label classes (e.g. "Stop Sign") |
| `LabelGroups` | LabelGroup.cs | Groups of labels |
| `ImageLabels` | ImageLabel.cs | Bounding boxes / polygons on images |
| `DataSets` | DataSet.cs | Named collections of images for training |
| `DataSetImages` | DataSetImage.cs | Image ↔ Dataset membership |
| `DataSetLabels` | DataSetLabel.cs | Label ↔ Dataset membership |
| `DataSetUsers` | DataSetUser.cs | User ↔ Dataset assignment |
| `MLModels` | MLModel.cs | Trained model records |
| `Tags` | Tag.cs | Tags for images |
| `ImageTags` | ImageTag.cs | Image ↔ Tag relationships |
| `SystemSettings` | SystemSettings.cs | App config key-value pairs |
| `AspNetUsers` | ApplicationUser.cs | User accounts (ASP.NET Identity) |

### Soft Deletes
Most tables have a `DeletedAt` column. Records are never hard-deleted — they are marked with a timestamp instead. This preserves history.

### Migrations
Located at `Hawkeye.ML.Server/Migrations/` — tracks every database schema change from initial creation (2023-11-30) onward.

---

## Shared Library: Hawkeye.ML.Infrastructure

**Target:** .NET Standard 2.0 (works with both .NET Framework and .NET Core)

### Key Contents
- `MlModelType` enum — `ImageClassification`, `ObjectDetection`, `SemanticSegmentation`, `ObjectDetectionWithTracking`, etc.
- `LabelType` enum — `Rectangle`, `Polygon`
- `PredictedVideoResult.cs` — video prediction result models
- `Utils.cs` — shared utility functions

---

## Project Dependencies

```
Hawkeye.ML.Angular.client
    └── (no .NET references, Angular only)

Hawkeye.ML.Server.WebAPI
    └── → Hawkeye.ML.Infrastructure  (ProjectReference)
    └── → EntityFramework Core 8.0   (NuGet)
    └── → ASP.NET Identity           (NuGet)
    └── → AWS S3 / SSM               (NuGet)
    └── → SkiaSharp                  (NuGet)
    └── → Swashbuckle/Swagger        (NuGet)

Hawkeye.ML.Infrastructure
    └── → Newtonsoft.Json            (NuGet)
    └── → YamlDotNet                 (NuGet)
    └── → CsvHelper                  (NuGet)
```

---

## Dev vs Production

| | Development | Production |
|---|---|---|
| Angular runs on | `http://localhost:4200` | Built & served by the backend |
| API runs on | `https://localhost:7289` | `https://ml.hawkeyemanager.com` |
| `apiUrl` in environment.ts | `''` (empty, uses proxy) | `'https://ml.hawkeyemanager.com'` |
| API proxy | `proxy.conf.js` forwards `/api/*` to `:7289` | Not needed |
| Debug/Release | F5 in VS = Debug | Published build = Release |

---

## Common File Navigation Guide

| What you want to change | Where to look |
|---|---|
| Page layout / HTML | `src/app/{page}/{page}.component.html` |
| Page logic / save / load | `src/app/{page}/{page}.component.ts` |
| API call definition | `src/app/services/api.service.ts` |
| Backend endpoint logic | `Hawkeye.ML.Server/Controllers/{Name}Controller.cs` |
| Data shape (what's sent over HTTP) | `Hawkeye.ML.Server/DTO/Dtos.cs` |
| Database table structure | `Hawkeye.ML.Server/Models/{ModelName}.cs` |
| Database schema changes | `Hawkeye.ML.Server/Migrations/` |
| App startup / middleware | `Hawkeye.ML.Server/Program.cs` |

---

## Example: Tracing a Feature End-to-End

**Example: Edit a label's group**

```
1. User clicks Edit button on label row
      label-classes.component.html  → (click)="openEditDialog(label)"
      label-classes.component.ts    → dialog.open(NewLabelClassComponent)

2. Dialog opens, loads groups
      new-label-class.component.ts  → apiService.getGroups()
      api.service.ts                → GET /api/labelgroups
      LabelGroupsController         → queries DB → returns groups

3. User selects None, clicks Save
      new-label-class.component.ts  → saveChanges() → apiService.putLabelData()
      api.service.ts                → PUT /api/labels/{id}
      LabelsController.PutLabel()   → foundLabel.Group = null
      ApplicationDbContext          → SaveChangesAsync()
      SQL                           → UPDATE Labels SET GroupId = NULL WHERE Id = {id}
```
