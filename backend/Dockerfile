FROM python:3.6-slim
WORKDIR /app
RUN apt-get update && apt-get install -y sqlite3 && apt-get clean
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 5000
CMD ["gunicorn", "-w", "4", "-b", "0.0.0.0:5000", "conduit.wsgi:application"]
