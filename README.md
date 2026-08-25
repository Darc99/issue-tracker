# Issue Tracker API

A small REST API for creating and managing software issues. The service is built with FastAPI and stores its data in a local JSON file, making it easy to run without a database.

## Features

- Create, list, retrieve, update, and delete issues
- Validation with Pydantic models
- Issue priorities: `low`, `medium`, and `high`
- Issue statuses: `open`, `in_progress`, and `closed`
- Interactive API documentation through FastAPI
- Request timing in the `X-Process-TIme` response header

## Requirements

- Python 3.10 or newer

## Setup

Create and activate a virtual environment, then install the dependencies:

```bash
python -m venv .venv
source .venv/bin/activate
python -m pip install "fastapi[standard]"
```

On Windows PowerShell, activate the environment with:

```powershell
.venv\Scripts\Activate.ps1
```

## Run the API

Start the development server from the project root:

```bash
fastapi dev main.py
```

The API is available at <http://127.0.0.1:8000>.

Interactive documentation is available at:

- Swagger UI: <http://127.0.0.1:8000/docs>
- ReDoc: <http://127.0.0.1:8000/redoc>
- OpenAPI schema: <http://127.0.0.1:8000/openapi.json>

## API

All issue endpoints use the `/api/v1/issues` prefix.

| Method | Endpoint | Description |
| --- | --- | --- |
| `GET` | `/api/v1/issues/` | List all issues |
| `POST` | `/api/v1/issues/` | Create an issue |
| `GET` | `/api/v1/issues/{issue_id}` | Get one issue |
| `PUT` | `/api/v1/issues/{issue_id}` | Update an issue |
| `DELETE` | `/api/v1/issues/{issue_id}` | Delete an issue |

### Create an issue

```bash
curl -X POST http://127.0.0.1:8000/api/v1/issues/ \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Add pagination",
    "description": "Support pagination when listing issues.",
    "priority": "high"
  }'
```

The `title` must contain 3-100 characters and the `description` must contain 5-1000 characters. `priority` defaults to `medium`. New issues always start with an `open` status and receive a generated UUID.

### List issues

```bash
curl http://127.0.0.1:8000/api/v1/issues/
```

### Update an issue

```bash
curl -X PUT http://127.0.0.1:8000/api/v1/issues/<issue-id> \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Add paginated issue list",
    "priority": "medium"
  }'
```

Update fields are optional. The current route applies changes to `title`, `description`, and `priority`; the `status` field is accepted by the update schema but is not currently persisted by the route.

### Delete an issue

```bash
curl -i -X DELETE http://127.0.0.1:8000/api/v1/issues/<issue-id>
```

Successful deletion returns `204 No Content`. Requests for an unknown issue ID return `404 Not Found`.

## Data storage

Issues are loaded from and saved to [`data/issues.json`](data/issues.json). The file is created automatically if it does not exist. There is no database migration or external service required.

## Project structure

```text
.
├── main.py                 # FastAPI application and middleware setup
├── app/
│   ├── schemas.py          # Request and response models
│   ├── storage.py          # JSON file persistence
│   ├── middleware/timer.py # Request timing middleware
│   └── routes/issues.py    # Issue endpoints
└── data/issues.json        # Local issue data
```

## Notes

- CORS is currently enabled for all origins. Restrict `allow_origins` before deploying to production.
- The JSON file is intended for local development and small demos. Concurrent writes and multi-process deployments should use a database instead.