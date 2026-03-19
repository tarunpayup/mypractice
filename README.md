# mypractice

This repo currently contains a single Python entrypoint (`app.py`). The extra files (`requirements.txt`, `package.json`) exist to make installing dependencies and deploying/running on a **DigitalOcean Droplet** predictable and repeatable.

## Files

### app.py
- **What it is:** The Python program entrypoint.
- **Why it exists:** This is what you run on the Droplet (or locally) via `python3 app.py`.

### requirements.txt
- **What it is:** The standard Python dependency lock list used by `pip`.
- **Why we put it here:**
  - Droplets are just Linux VMs; you typically provision them by installing Python packages with `pip install -r requirements.txt`.
  - Keeping dependencies in one file makes server setup repeatable (same packages/versions every time).

**Note:** Right now `app.py` only prints `Hello World!` and has no third-party imports, so `requirements.txt` is intentionally empty with examples.

### package.json
- **What it is:** A small, convenient place to store *deployment helper scripts* under `npm run …`.
- **Why we put it here (even for a Python app):**
  - `npm run` gives you a consistent, memorable command interface for common operational tasks (SSH, rsync deploy, remote run).
  - It avoids having to remember long shell commands and keeps them versioned with the project.

The scripts are designed for a Droplet and rely on environment variables so you don’t hard-code server details.

## How to use on a DigitalOcean Droplet

### 1) Set environment variables
These scripts expect:
- `DROPLET_HOST` (required): your droplet IP or DNS name
- `DROPLET_USER` (optional, default: `root`)
- `APP_DIR` (optional, default: `/opt/mypractice`)

Example:

```sh
export DROPLET_HOST="203.0.113.10"
export DROPLET_USER="root"
export APP_DIR="/opt/mypractice"
```

### 2) SSH into the droplet
```sh
npm run droplet:ssh
```

### 3) Copy code to the droplet (rsync)
```sh
npm run droplet:sync
```

This sync excludes `.venv` and `__pycache__` because those are local artifacts and shouldn’t be deployed.

### 4) Run the app on the droplet
```sh
npm run droplet:run
```

## Installing Python dependencies on the droplet

On the droplet, once Python/pip is installed:

```sh
python3 -m pip install -r requirements.txt
```

As you add libraries (Flask/FastAPI/requests/etc.), pin them in `requirements.txt` so the droplet install remains deterministic.
