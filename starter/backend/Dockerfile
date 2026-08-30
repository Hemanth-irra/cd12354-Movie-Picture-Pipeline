FROM python:3.10-slim-bookworm

# Create non-root user
RUN useradd -m -u 1000 app

# Install system dependencies required by uWSGI
RUN apt-get update && \
    apt-get install -y \
    build-essential \
    gcc \
    libpcre3 \
    libpcre3-dev \
    libffi-dev \
    && rm -rf /var/lib/apt/lists/*

# Create application directory
RUN mkdir -p /app/backend

# Copy dependency files
COPY Pipfile* /app/backend/

# Install Pipenv
RUN pip install --no-cache-dir pipenv

# Install Python dependencies
WORKDIR /app/backend
RUN pipenv install --system --deploy

# Copy application source
COPY . /app/backend/

# Make /app available for importing backend as a package
WORKDIR /app

# Set ownership
RUN chown -R app:app /app

# Run as non-root user
USER app

# Expose Flask/uWSGI port
EXPOSE 5000

# Start Flask application through uWSGI
CMD ["uwsgi", "--http", "0.0.0.0:5000", "--module", "backend:app"]