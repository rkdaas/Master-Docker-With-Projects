# Stage 1 - 

FROM python:3.10-slim AS build

WORKDIR /app

COPY requirements.txt /app/

COPY devops /app/

RUN python -m venv /app/venv 

RUN /app/venv/bin/pip install --upgrade pip && \
    /app/venv/bin/pip install --no-cache-dir -r requirements.txt

# Stage 2 - 

FROM python:3.10-slim

WORKDIR /app

COPY --from=build /app /app

ENV PATH="/app/venv/bin:$PATH"

CMD ["python","manage.py","runserver","0.0.0.0:8000"]


