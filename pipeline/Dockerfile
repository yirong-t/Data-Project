# Sets the official, minimal Debian-based Python 3.13 image as the starting foundation.
FROM python:3.13.11-slim


# This is a multi-stage trick. It looks into the official uv Docker image (ghcr.io/astral-sh/uv:latest), grabs the pre-compiled uv binary from its /uv path, and copies it directly into your current image's /bin/ directory.
# Why: You get uv instantly without needing to install it via pip or curl, saving build time and avoiding extra dependencies.
COPY --from=ghcr.io/astral-sh/uv:latest /uv /bin/

# : Creates a directory named /app inside the container (if it doesn't exist) and sets it as the active working directory for any subsequent commands (COPY, RUN, ENTRYPOINT). 
WORKDIR /app

# Modifies the container's environment variables to place the virtual environment's executable folder (/app/.venv/bin) at the very front of the system PATH.
# This ensures that when you run commands like python or scripts installed by your packages later on, the system automatically uses the ones inside the virtual environment instead of the global system Python.
ENV PATH="/app/.venv/bin:$PATH"

# Copies only your project's configuration, lock file, and Python version file from your local machine into the container's /app directory.
COPY "pyproject.toml" "uv.lock" ".python-version" ./

# Runs uv sync to create the virtual environment and install all project dependencies. The --locked flag forces uv to strictly use the exact versions listed in your uv.lock file.
RUN uv sync --locked

# Copies your actual Python code (pipeline.py) into the container. 
# Since your code changes frequently, this step is placed near the bottom so it doesn't invalidate the cached layer created by the heavy uv sync step above.
COPY pipeline.py pipeline.py

# Set entry point
# Defines the default command that executes immediately when the container starts up.

# Why: Running uv run python pipeline.py ensures that the script executes cleanly within the context of the managed environment.
ENTRYPOINT [ "uv", "run", "python", "pipeline.py" ]