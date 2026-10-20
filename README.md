> **Note:** This README was AI-generated and reviewed for this project.

# FastAPI Social Media Tutorial

A small social-media application built with FastAPI, FastAPI Users, SQLite, SQLAlchemy, ImageKit, and Streamlit.

Users can create an account, log in with JWT authentication, upload images or videos, add captions, view the feed, and delete their own posts.

## Features

- User registration and JWT login
- Protected feed, upload, and delete-post endpoints
- Image and video uploads through ImageKit
- Captions displayed below media in the Streamlit feed
- Feed ordered by newest post first
- Owner-only post deletion
- Interactive FastAPI documentation with Swagger UI
- SQLite database for local development

## Project structure

```text
.
├── app/
│   ├── app.py       # FastAPI application and post endpoints
│   ├── db.py        # SQLAlchemy models and database dependencies
│   ├── images.py    # ImageKit configuration
│   ├── schemas.py   # Request and response schemas
│   └── users.py     # FastAPI Users authentication configuration
├── forntend.py      # Streamlit frontend (filename kept for compatibility)
├── main.py          # Uvicorn entry point
├── pyproject.toml   # Python dependencies
├── uv.lock          # Locked dependency versions
└── test.db          # Local SQLite database
```

## Requirements

- Python 3.8 or newer
- `uv` recommended for dependency management
- An ImageKit account and credentials

Python 3.8 is currently used by this project. Some newer dependency versions may eventually require a newer Python version.

## Configuration

Create a `.env` file in the project root with the ImageKit credentials used by [app/images.py](app/images.py):

```env
IMAGEKIT_PRIVATE_KEY=your_private_key
IMAGEKIT_PUBLIC_KEY=your_public_key
IMAGEKIT_URL_ENDPOINT=https://ik.imagekit.io/your_imagekit_id
```

Do not commit real credentials to source control.

## Installation

Using `uv`:

```bash
uv sync
```

Or using the existing virtual environment:

```bash
source .venv/bin/activate
```

## Run the application

Start the FastAPI backend in one terminal:

```bash
uv run python main.py
```

The backend is available at:

- API: <http://localhost:8000>
- Swagger UI: <http://localhost:8000/docs>
- OpenAPI JSON: <http://localhost:8000/openapi.json>

Start the Streamlit frontend in a second terminal:

```bash
uv run streamlit run forntend.py
```

The frontend is available at:

<http://localhost:8501>

If `uv` is not being used, run the commands with the virtual-environment executables:

```bash
./.venv/bin/python main.py
./.venv/bin/streamlit run forntend.py
```

## Using the frontend

1. Open <http://localhost:8501>.
2. Enter an email and password.
3. Click **Sign Up**.
4. Click **Login** with the newly registered account.
5. Use **Upload** to select an image or video and add an optional caption.
6. Use **Feed** to view posts and delete posts created by the current user.

Captions are shown as normal text below images and videos. This avoids clipping long captions inside the media itself.

## API endpoints

### Authentication

| Method | Endpoint | Description |
| --- | --- | --- |
| `POST` | `/auth/register` | Register a user |
| `POST` | `/auth/jwt/login` | Log in and receive a JWT |
| `GET` | `/users/me` | Get the current user |

### Posts

| Method | Endpoint | Authentication | Description |
| --- | --- | --- | --- |
| `POST` | `/upload` | Required | Upload an image or video |
| `GET` | `/feed` | Required | Get the newest posts |
| `DELETE` | `/posts/{post_id}` | Required | Delete a post owned by the current user |

For protected endpoints, send the token returned by `/auth/jwt/login`:

```http
Authorization: Bearer <access_token>
```

## Database

The application uses SQLite at `test.db`. Tables are created when the FastAPI application starts.

`create_all()` creates missing tables but does not migrate existing tables when model columns change. If an older local database is missing a newly added column, use a migration or recreate the local development database after backing up any data you need to keep.

## Troubleshooting

### `Failed to load feed` or `Upload failed`

Check that:

1. The FastAPI backend is running on port `8000`.
2. You are logged in through the Streamlit frontend.
3. The database schema contains the current `posts` columns.
4. ImageKit credentials are valid for uploads.

### `IndexError` while displaying a post

Old records with placeholder or invalid media URLs cannot be displayed. The frontend now skips invalid media URLs and shows a warning instead of stopping the entire feed.

### Port already in use

Run the services on different ports:

```bash
uv run python -m uvicorn app.app:app --host 0.0.0.0 --port 8001
uv run streamlit run forntend.py --server.port 8502
```

## Development checks

Compile the Python modules:

```bash
./.venv/bin/python -m py_compile app/app.py app/users.py app/db.py app/schemas.py forntend.py main.py
```

Check the Git diff for whitespace errors:

```bash
git diff --check
```
